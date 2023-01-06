下面是梳理过后的 Webpack 知识体系

![Webpack体系](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061000316.png)



# 一、Webpack 核心基础

Webpack 基础配置项如下所示，具体可查看文档：

## 1、项目配置

![image-20230106101445789](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061014215.png)

- [带你深度解锁 Webpack 系列(基础篇)](https://juejin.cn/post/6844904079219490830)



## 2、Loader

![image-20230106101425436](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061014617.png)



### （1）什么是 Loader

​	Loader 通常指**打包的方案**，作用即是**让 webpack 拥有了加载和解析⾮ JavaScript ⽂件的能⼒**

- [Loader 知识分享](https://juejin.cn/post/6950092728919130126)：关于 Loader 比较全的介绍



### （2）常用的 Loader

- babel-loader： 将 ES6+ 语法转换为 ES5 语法
- file-loader：把⽂件输出到⼀个指定目录中，在代码中通过相对 URL 去引⽤输出的⽂件。
- url-loader：可以在小于限制文件大小的情况下以 base64 的⽅式把⽂件内容注⼊到代码中去
- css-loader：加载 CSS，⽀持模块化、压缩、⽂件导⼊等特性
- style-loader：将处理好的 css 通过 style 标签的形式添加到页面上
- less-loader：解析 Less 文件
- sass-loader：将 SCSS/SASS 代码转换成 CSS
- postcss-loader：扩展 CSS 语法，如可以配合 autoprefixer 插件自动补齐 CSS3 前缀



### （3）如何编写 Loader

Loader 其实就是一个函数，通过获得处理前的源内容，对原内容执行处理后，返回处理后的内容。

- [如何编写一个 Webpack Loader](https://juejin.cn/post/6882895689773383694)
- [Webpack 原理系列七：如何开发 Loader](https://juejin.cn/post/6966785086473633806)



## 3、Plugin

![image-20230106101536327](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061015741.png)



### （1）什么是 Plugin

Plugin 是一个功能扩展器，webpack 的插件体系是一种基于 Tapable 实现的强耦合架构，可以在 webpack 运行到某个时刻的时候，帮我们做一些事情



### （2）常用的 Plugin

- html-webpack-plugin： 依据一个 HTML 模板，生成 HTML 文件，并把打包生成的 js 自动引入到这个 HTML 文件中
- terser-webpack-plugin: 支持压缩 ES6，同时可以开启  parallel  参数，使用多进程压缩，加快压缩
- mini-css-extract-plugin: CSS 提取到单独的⽂件中, ⽀持按需加载
- webpack-bundle-analyzer: 可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)
- speed-measure-webpack-plugin: 可以看到整个打包耗时、每个 Plugin 和 Loader 耗时



### （3）如何编写 Plugin

- [如何编写一个 Webpack Plugin](https://juejin.cn/post/6884866016565084173)
- [Webpack 原理系列二：插件架构原理与应用](https://juejin.cn/post/6955421936373465118)



## 4、server服务

![image-20230106101653232](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061016523.png)



## 5、SourceMap

![image-20230106101617351](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061016466.png)

Sourcemap 解决开发代码与实际运行代码不一致时造成无法 debug 的问题，它是**源代码和目标代码出错位置的映射**。

- [Webpack Sourcemap 回顾](https://juejin.cn/post/6941924227444375589)
- [深入浅出之 Source Map](https://juejin.cn/post/7023537118454480904)



# 二、Webpack 原理

## 1、Webpack 打包构建流程

![image-20230106102609479](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061026892.png)

- [[万字总结] 一文吃透 Webpack 核心原理](https://juejin.cn/post/6949040393165996040)：非常非常经典的原理文章，非常非常推荐
- [Webapck5 核心打包原理全流程解析](https://juejin.cn/post/7031546400034947108)



## 2、Webpack 懒加载

webpack 的懒加载实际借助了 ES6 的 import()语法和 jsonp。

- [一文搞懂 webpack 懒加载机制 —— webpack 系列](https://juejin.cn/post/6924484965073862664)
- [webpack 的异步加载原理及分包策略](https://segmentfault.com/a/1190000038180453)



## 3、热更新原理

![image-20230106102639306](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061026656.png)

热更新即是当对代码进行修改并保存后，webpack 将对代码重新打包，在不刷新浏览器的前提下就能够对页面进行更新。

- [Webpack HMR 原理解析](https://zhuanlan.zhihu.com/p/30669007)
- [搞懂 webpack 热更新原理](https://github.com/careteenL/webpack-hmr)
- [轻松理解 webpack 热更新原理](https://juejin.cn/post/6844904008432222215)



## 4、Tree Shaking

![image-20230106102654341](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061026414.png)

Tree Shaking 本质上为了消除无用的 JS 代码，减少加载文件体积。

实现前提：

- 代码遵循**ES6 Module**规范
- 将`mode` 选项为`production`
  - optimization.usedExports: true
  - optimization.minimize: true

- [Webpack | TreeShaking 工作原理](https://zhuanlan.zhihu.com/p/472733451)
- [Webpack 原理系列九：Tree-Shaking 实现原理](https://juejin.cn/post/7019104818568364069)



# 三、Webpack 优化

![image-20230106102801155](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061028445.png)

这里不多做赘述，详细的内容看我另一篇文章《webpack性能优化》。

# 四、Webpack5

![image-20230106102956658](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061029921.png)

Webpack5 的主要变更：

- 用持久性缓存来提高构建性能。
- 用更好的算法和默认值来改进长期缓存。
- 更好的 Tree Shaking 和代码生成来改善包大小。
- 改善与网络平台的兼容性。
- 在不引入任何破坏性变化的情况下，清理那些在实现 v4 功能时处于奇怪状态的内部结构。
- 试图通过现在引入突破性的变化来为未来的功能做准备，尽可能长时间地保持在 v5 版本上。

Webpack5 的重大的特性就是<font color=deepred>模块联邦</font>，它主要是为了更好的共享代码，让代码跨应用间利用 CDN 直接共享，不再需要本地安装 NPM 包、构建再发布。

-  [模块联邦浅析](https://juejin.cn/post/7101457212085633054)

- [webpack5 上手指南](https://juejin.cn/post/6955266854839386119)



# 五、构建工具历史

## 1、模块化规范

![image-20230106103141416](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061031700.png)



## 2、构建工具

![image-20230106103202647](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301061032619.png)