# 基础

### 1、构建工具

#### （1）Gulp

​	[Gulp](http://gulpjs.com/) 是一个**基于流**的自动化构建工具。 除了可以管理和执行任务，还支持监听文件、读写文件。Gulp通过5个方法就可以胜任几乎所有构建场景： `gulp.task` 注册一个任务；`gulp.run` 执行任务；`gulp.watch` 监听文件变化； `gulp.src` 读取文件； `gulp.dest` 写文件。

**缺点是集成度不高，需要写很多配置**。



#### （2）Webpack

​	打包模块化 JavaScript 的工具，在 Webpack 里**一切文件皆模块**，通过 Loader 转换文件，通过 Plugin 注入钩子，最后输出由多个模块组合成的文件。Webpack 专注于构建模块化项目。



#### （3）Rollup

​	和 Webpack 很类似但**专注于 ES6 的模块打包工具**，Rollup 在打包 JavaScript 库时比 Webpack打包出来的代码更小更快。 但功能不够完善，很多场景都找不到现成的解决方案。



### 2、安装

```shell
npm init -y
#wepack3以上版本都需要安装webpack-cli(用于在命令行中运行webpack)
npm install webpack webpack-cli --save-dev
```



# 配置



# 实战



# 优化



# 原理