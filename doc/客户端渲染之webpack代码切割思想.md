本章讲的是客户端渲染的代码切割方式。

如果没有做代码分割，打包react项目之后，只有唯一的一个js文件可能有几百Kb甚至几M。这样大的
文件即使开启gzip，最终效果任然不是很好。

我在这里分享一种代码切割的方案，前提是你的项目大到需要用到它：

1、提取react和react-dom为公共组件，一开始我采用是将node_modules下的所有依赖都打包到公共组件，
但是当你使用了echarts或者其他很大的第三方插件时，就会变得没有必要，因为很少网站首屏就需要加载各种图表组件。

2、提取css，这个大家很熟悉了。css写的多了，也挺大的，100多kb也正常。

3、上面提取的公共组件的作用，对首屏初次加载性能优化没多大作用，反而是分离了多个http请求，导致消耗了http的性能。
但也有好处，就是可以将公共组件做缓存处理，缓存到浏览器，下次用户访问就能直接读取缓存啦。

4、接着是重头戏，我们需要将除了首页之外的页面全部使用webpack提供的import()方法提取出来，每个页面单独提取之后，
可以减少首页js的大小，这是优化首页加载性能的重要步骤。但是也有缺点，首次打开每个页面，都需要先加载一次js，好处就是
加载过一次js之后，页面就不需要重新加载，这样一来页面的切换又能很流畅了。

也许看完上面的步骤，你还不是很明白，你可以看看本项目的代码切割实现，利用的就是上面的原理。

**温馨提示，当你的项目打包后的bundle.js小于500Kb，我认为没有切割的必要**