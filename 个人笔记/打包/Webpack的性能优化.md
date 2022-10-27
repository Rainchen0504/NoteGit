# 1、webpack数据分析

## （1）文件体积分析

<font color=deepred>**webpack-bundle-analyzer**</font>

它能分析打包出的文件有哪些，大小占比如何，模块包含关系，依赖项，文件是否重复，压缩后大小

- webpack.config.js

```js
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer')
module.exports={
plugins: [
    new BundleAnalyzerPlugin({
    analyzerMode: 'disabled', // 不启动展示打包报告的http服务器
    generateStatsFile: true, // 是否生成stats.json文件 
    })
 ] 
}
```

- package.json

```json
"scripts": {
  "build": "webpack",
  "start": "webpack serve",
  "dev":"webpack  --progress",
   "analyzer": "webpack-bundle-analyzer --port 8888 ./dist/stats.json"
}
```



## （2）分析打包速度

<font color=deepred>**speed-measure-webpack-plugin**</font>

- webpack.config.js

```js
const SpeedMeasureWebpackPlugin = require('speed-measure-webpack5-plugin');
const smw = new SpeedMeasureWebpackPlugin();
module.exports = smw.wrap({
  mode: "development",
  devtool: 'source-map',
  ...
});
```



## （3）美化输出日志

<font color=deepred>**friendly-errors-webpack-plugin**</font>

安装方法

```bash
yarn friendly-errors-webpack-plugin  node-notifier -D
```

使用

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');
const notifier = require('node-notifier');

module.exports = {
  mode: "development",
  devtool: 'source-map',
  context: process.cwd(),
  entry: {
    main: "./src/index.js",
  },
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "main.js"
}, 
plugins:[
    new HtmlWebpackPlugin(),
    new FriendlyErrorsWebpackPlugin({
      onErrors: (severity, errors) => {
        const error = errors[0];
        notifier.notify({
          title: "Webpack编译失败",
          message: severity + ': ' + error.name, subtitle: error.file || '',
      	})
    	}
  	})
 	] 
};
```





# 2、编译时间优化

## （1）extensions

- 添加extensions后在用`require`、`import`的时候不用添加文件扩展名
- 编译的时候会依次添加扩展名进行匹配

```js
module.exports = {
    resolve: {
        extensions:[".js"、".jsx"、".json"]
    }
}
```



## （2）alias

配置文件别名可以加快webpack查找模块的速度

```js
const elementUi = path.resolve(__dirname, 'node_modules/element-ui/lib/theme-chalk/index.css')
module.exports = {
  resolve: {
    extensions:[".js"、".jsx"、".json"],
    alias: {'element-ui'}
  }
}
```

引入elementUi模块的时候，它会直接引入elementUi目标位置的文件，不需要从node_modules文件中按模块规则查找



## （3）modules

指定项目的所有第三方模块都是在项目根目录下的node_modules

```js
const elementUi = path.resolve(__dirname,'node_modules/element-ui/lib/theme-chalk/index.css')
module.exports = {
  resolve: {
    extensions:[".js"、".jsx"、".json"],
    modules: ['node_modules']
  }
}
```



## （4）oneOf

- 每个文件对于rules中的所有规则都会遍历一遍，如果使用oneOf，<font color=blue>只要能匹配一个就立即退出</font>
- 在oneOf中不能使用使2个配置处理同一类型文件

```js
module.exports = {
  module: {
    rules: [{
      oneOf:[
        {
          test: /\.js$/,
          include: path.resolve(__dirname, "src"),
          exclude: /node_modules/,
          use: [
            {
              loader: 'thread-loader',
              options: {
                workers: 3 
              }
            },
            {
              loader:'babel-loader',
              options: {
                cacheDirectory: true
              }
            }
          ]
        },
        {
          test: /\.css$/,
          use: ['cache-loader','logger-loader', 'style-loader', 'css-loader']
        } 
      ]
    }]
  }
}
```



## （5）external

如果某个库不想让它被webpack打包，想让它用cdn的方法引入，并且不影响在程序中以CMD、AMD方式进行使用。

下载插件

```bash
yarn add html-webpack-externals-plugin -D
```

在html文件中引入cdn的文件

```html
<script src="https://cdn.abc.com/vue/2.5.11/vue.min.js"></script>
```

webpack中的配置

```bash
externals: {
	vue: 'vue',
},
```



## （6）resolveLoader

​	就是指定loader的resolve，只作用于loader；resolve配置用来影响webpack模块解析规则。解析规则也可以称之为检索，索引规则。配置索引规则能够缩短webpack的解析时间，提升打包速度。

```js
module.exports = {
    resolve: {
        extensions:[".js"、".jsx"、".json"],
        modules: ['node_modules']
    },
    resolveLoader:{
     modules: [path.resolve(__dirname, "loaders"),'node_modules'],
  },   
}
```



## （7）noParse

- 用于配置哪些模块的文件内容不需要进行解析
- 不需要解析依赖就是没有依赖的第三方大型类库，可以配置这个字段，以提高整体的构建速度
- 使用noparse进行忽略的模块文件中不能使用import、require等语法

```js
module.exports = {
module: {
    noParse: /test.js/, // 正则表达式
 } 
}
```



## （8）thread-loader(多进程)

- 把thread-loader放置在其他 loader 之前
- include 表示哪些目录中的 .js 文件需要进行 babel-loader
- exclude 表示哪些目录中的 .js 文件不要进行 babel-loader
- exclude 的优先级高于 include ,尽量避免 exclude ，更倾向于使用 include

```js
module.exports = {
  module: {
    rules: [{
      oneOf:[
        {
          test: /\.js$/,
          include: path.resolve(__dirname, "src"),
          exclude: /node_modules/,
          use: [
            {
              loader: 'thread-loader',
              options: {
                workers: require('os').cpus().length - 1 // 自己电脑的核心数减1
              }
            },
            {
              loader:'babel-loader',
              options: {
                // babel在转移js非常耗时间，可以将结果缓存起来，下次直接读缓存；默认存放位置是 node_modules/.cache/babel-loader
                cacheDirectory: true 
              }
            }
          ]
        }
      ]
    }]
  }
}
```



## （8）cache-loader

- 在一些性能开销较大的loader之前添加cache-loader，可以将结果缓存到磁盘中
- 默认保存在 node_modules/.cache/cache-loader 目录下

```js
module.exports = {
  module: {
    rules: [{
     oneOf:[
        {
          test: /\.css$/,
          use: ['cache-loader','logger-loader', 'style-loader', 'css-loader']
       } 
     ]
    }]
  }
}
```



## （9）hard-source-webpack-plugin

- HardSourceWebpackPlugin 为模块提供了中间缓存,缓存默认的存放路径是 node_modules/.cache/hard-source
- 配置 hard-source-webpack-plugin 后，首次构建时间并不会有太大的变化，但是从第二次开始， 构建时间大约可以减少80% 左右
- webpack5中已经内置了模块缓存,不需要再使用此插件

```bash
yarn add hard-source-webpack-plugin -D
```

```js
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');
module.exports = {
plugins: [
   new HardSourceWebpackPlugin()
 ] 
}
```



# 3、编译体积优化

## （1）压缩js、css、HTML和图片

- optimize-css-assets-webpack-plugin是一个优化和压缩CSS资源的插件
- terser-webpack-plugin是一个优化和压缩JS资源的插件
- image-webpack-loader可以帮助我们对图片进行压缩和优化，例如降低图片分辨率，压缩图片体积等。

```bash
npm install terser-webpack-plugin optimize-css-assets-webpack-plugin image-webpack-loader -D
```

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {,
  optimization: {
     minimize: true
     minimizer: [
         new TerserPlugin()
     ]
  },
   module:{
     rules:[
         {
          test: /\.(png|svg|jpg|gif|jpeg|ico)$/,
          use: [
            'url-loader',
            {
                loader: 'image-webpack-loader',
                options: {
                    mozjpeg: {
                       progressive:true,
                       quality: 65 
                    },
                    optipng: {
                        enabled: false
                    },
                    pngquant: {
                        quality: '65-90',
                        speed: 4
                    },
                    gifsicle: {
                        interlaced: false
                    },
                    webp: {
                         quality: 75,
                    }
                }
            }
         }]
     ]
  },
  plugins:[
     new HtmlWebpackPlugin({
         template: './src/index.html',
          minify: {
              collapseWhitespace: true,
              removeComments: true
         }
     }) 
    new OptimizeCssAssetsWebpackPlugin(), 
  ]
 }
```



## （2）清除无用的css

purgecss-webpack-plugin单独提取CSS并清除用不到的CSS，一般与glob和glob-all配合使用。

⚠️注意：此插件必须和`CSS`代码抽离插件`mini-css-extract-plugin`配合使用。

```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const PurgecssPlugin = require("purgecss-webpack-plugin");
const glob = require("glob");
const PATHS = {
    src: path.join(__dirname, "src"),
};

module.exports = {
  optimization: {
     minimize: true
     minimizer: [
         new TerserPlugin()
     ]
  },
   module:{
     rules:[
         {
          test: /\.css$/,
          include: path.resolve(__dirname, "src"),
          exclude: /node_modules/,
          use: [
            {
              loader: MiniCssExtractPlugin.loader,  
            },
             "css-loader",
         }]
     ]
  },
  plugins:[
		new MiniCssExtractPlugin({
    	filename: "[name].css"
    }),
    new PurgecssPlugin({
      paths: glob.sync(`${path.join(__dirname, 'src')}/**/*`, 
      {nodir: true}), // 不匹配目录，只匹配文件
		}),
  ]
 }
```



## （3）Tree shaking

- webpack默认支持,可在 production mode下默认开启
- 在package.json 中配置:
  - "sideEffects": false 所有的代码都没有副作用(都可以进行 tree shaking)
  - 可能会把 css和@babel/polyfill 文件干掉可以设置 "sideEffects":["*.css"]
- 将没用的代码摇晃下来,从而实现删除代码中未被引用的代码。



## （4） Scope Hoisting

- Scope Hoisting 可以让 Webpack 打包出来的代码文件更小、运行的更快，它又译作 "作用域提升"，是在 Webpack3 中新推出的功能。
- scope hoisting的原理是将所有的模块按照引用顺序放在一个函数作用域里，然后适当地重命名一 些变量以防止命名冲突
- 这个功能在mode为 下默认开启,开发环境要用 webpack.optimizeModuleConcatenationPlugin 插件

```js
module.exports = {
  plugins: [
    new webpack.optimize.ModuleConcatenationPlugin(),
  ]
}
```



# 4、运行速度优化

​	对于大的Web应用来讲，将所有的代码都放在一个文件中显然是不够有效的，特别是当你的某些 代码块是在某些特殊的时候才会被用到。webpack有一个功能就是将你的代码库分割成chunks语块，当代码运行到需要它们的时候再进行加载。

## （1）入口点分割

```js
module.exports = {
  entry: {
    index: "./src/index.js",
    login: "./src/login.js"
  }
}
```

- 这种方法的问题
  - 如果入口chunks之间包含重复的模块(lodash)，那些重复模块都会被引入到各个bundle中
  - 不够灵活，并且不能将核心应用程序逻辑进行动态拆分代码



## （2）懒加载

可以用`import()`方式去引入模块，当需要的时候在加载某个功能对应代码

```js
const Login = () => import(/* webpackChunkName: "login" */'@/components/Login/Login')
```



## （3）prefetch

- 使用预先拉取，表示该模块可能以后会用到。浏览器会在空闲时间下载该模块
- prefetch的作用是告诉浏览器未来可能会使用到的某个资源，浏览器就会在闲时去加载对应的资源，若能预测到用户的行为，比如懒加载，点击到其它页面等则相当于提前预加载了需要的资源
- `<link rel="prefetch" as="script" href="test.js">`此方法添加头部，浏览器会在空闲时间预先拉取该文件

```js
import(/* webpackChunkName: 'login', webpackPrefetch: true*/'./login').then(result => {
  console.log(result.default);
});
```



## （4）提取公共代码

```js
module.exports = {
  output:{
    filename:'[name].js',
    chunkFilename:'[name].js'
  },
  entry: {
    index: "./src/index.js",
    login: "./src/login.js"
  },
  optimization: {
    splitChunks: {
      chunks: 'all',  // 分割同步异步的代码
      minSize: 0,    // 最小体积
      minRemainingSize: 0, // 代码分割后的最小保留体积，默认等于minSize
      maxSize: 0,  // 最大体积
      minChunks: 1,  // 最小代码快
      maxAsyncRequests: 30, // 最大异步请求数
      maxInitialRequests: 30, // 最小异步请求数
      automaticNameDelimiter: '~', // 名称分离符
      enforceSizeThreshold: 50000, //执行拆分的大小阈值，忽略其他限制
      // (minRemainingSize、maxAsyncRequests、maxInitialRequests) 
      cacheGroups: {
        defaultVendors: {
          test: /[\\/]node_modules[\\/]/,//控制此缓存组选择哪些模块
          priority: -10,//一个模块属于多个缓存组,默认缓存组的优先级是负数，自定义缓存组的优先级更高，默认值为0 //如果当前代码块包含已经主代码块中分离出来的模块，那么它将被重用，而不是生成新的模块。这可能会影响块的结果文件名。
        }, 
        default: {
          minChunks: 2,
          priority: -20
        }
      } 
    }
  }
  plugins: [
  //多个入口文件，把公共部分分割出来
    new HtmlWebpackPlugin({
      template:'./src/index.html',
      filename:'page1.html',
      chunks:['index']
  	}),
    new HtmlWebpackPlugin({
      template:'./src/index.html',
      filename:'page2.html',
      chunks:['login']
    }),
  ]
}
```



## （5）CDN

- 最影响用户体验的是网页首次打开时的加载等待。 导致这个问题的根本是网络传输过程耗时大， CDN的作用就是加速网络传输。
- CDN 又叫内容分发网络，通过把资源部署到世界各地，用户在访问时按照就近原则从离用户最近 的服务器获取资源，从而加速资源的获取速度
- 用户使用浏览器第一次访问我们的站点时，该页面引入了各式各样的静态资源，如果我们能做到持 久化缓存的话，可以在 http 响应头加上 Cache-control Expires字段来设置缓存，浏览器可以 将这些资源一一缓存到本地
- 用户在后续访问的时候，如果需要再次请求同样的静态资源，且静态资源没有过期，那么浏览器可以直接走本地缓存而不用再通过网络请求资源
- 缓存配置
  - HTML文件不缓存，放在自己的服务器上，关闭自己服务器的缓存，静态资源的URL变成指向 CDN服务器的地址
  - 静态的JavaScript、CSS、图片等文件开启CDN和缓存，并且文件名带上HASH值
  - 为了并行加载不阻塞，把不同的静态资源分配到不同的CDN服务器上
- 域名限制
  - 同一时刻针对同一个域名的资源并行请求是有限制 可以把这些静态资源分散到不同的 CDN 服务上去 多个域名后会增加域名解析时间
  - 可以通过在 HTML HEAD 标签中 加入去预解析域名，以降低域名解析带来的延迟