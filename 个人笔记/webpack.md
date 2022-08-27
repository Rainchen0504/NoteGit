# 一、Webpack

## 1、Webpack打包工具

​	Webpack是一个静态的模块化打包工具，为现代的Javascript应用程序，

​	Webpack is a static module bundler for modern Javascript applications。



## 2、Vue项目加载文件

- **Javascript的打包**：将ES6转成ES5，TypeScript转成Javascript；
- **Css的处理**：Css文件模块的加载、提取，Sass和Less等预处理器处理；
- **资源文件img、font**：图片和字体文件的加载；
- **HTML资源的处理**：打包HTML资源文件；
- **处理vue项目的SFC文件.vue文件**；



## 3、使用前提

webpack安装依赖node环境。



## 3、配置别名

​	vue中配置别名后，js里可以使用“@”代替src目录，但是在scss中需要写成“~@”。



## 4、webpack对项目打包的过程

​	在处理应用程序时，会根据命令或者配置文件找到入口文件，从入口开始会生成一个依赖关系图，这个依赖关系图会包含应用程序中所需的所有模块（比如.js文件、css文件、图片、文字等），然后遍历图结构，打包一个个模块（根据文件的不同使用不同的loader来解析）。





## 5、webpack的安装

### （1）webpack

执行webpack命令，会执行node_modules下的webpack。



### （2）webpack-cli

webpack-cli中的代码执行时，才真正利用webpack进行编译和打包。webpack在执行时是依赖webpack-cli的，如果没安装就会报错。

​	注意：在第三方的脚手架事实上时没有使用webpack-cli的，而是类似于vue-serve-cli的东西。也就是<u>不使用脚手架，在命令行手动进行webpack打包时，需要同时安装webpack和webpack-cli的。</u>



## 6、Live Server

本地html文件中script标签引入ES6模块，直接在浏览器打开html文件，出现报错

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052052647.png" alt="image-20210905205254457" style="zoom:67%;" />

html结构部分代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <script src="./src/main.js"></script>
</body>
</html>
```

js部分

```javascript
// ES Moduled的导出,math.js文件
export function sum(num1, num2) {
  return num1 + num2;
};

//方法的引入,main.js文件
import { sum } from "./js/math";
console.log(sum(20, 30));
```

​	此处报错的原因是使用了ES6语法，浏览器默认将它作为js解析会报错，需要将其作为模板导入，script标签默认`type="text/javascript"`，需要改为`type="module"`，但是打开浏览器后又报错：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052054563.png" alt="image-20210905205415465" style="zoom: 50%;" />

​	从错误提示看，脚本是被跨域策略给拦截了，跨域请求只支持这些协议：http, data, chrome, chrome-extension, chrome-untrusted, https.，而我们的协议是file，这里我们需要本地起一个服务器来作为资源的提供方，简单的方式是安装VSCode的一个扩展Live Server。

​	<u>Live Server</u>是一个具有实时加载功能的小型服务器，可以使用它来破解html/css/javascript，但是不能用于部署最终栈点。也就是说我们可以在项目中实时用live-server作为一个实时服务器实时查看开发的网页或项目效果。



## 7、CommonJS和ESmodules的差异

### （1）总结：

- ommonJS模块输入的是一个值的拷贝，ES6模块输出的是值的引用（所以重新对其赋值）。
- commonJS模块是运行时加载（同步），ES6模块是编译时输出接口（异步）。



### （2）用法的区别：

```javascript
//commonJS导出一个对象
module.exports = {name:"zhang",age:24};
let x = require("./index")；

//ESmodules导出一个对象
//方式一
export {name:"zhang",age:24};
import {name,age} from "./index" //不能任意命名，只能解构赋值
//方式二
export default {name:"zhang",age:24};
import xx from "index"; //导入的对象可以任意命名
```



## 8、webpack五个核心概念

### （1）entry

入口，从该文件位置为入口起点打包，分析构建内部依赖图。

### （2）output

出口，webpack打包后的资源目的地，以及命名。

### （3）loader

让webpack可以处理非JS文件(webpack本身只认识Javascript)。

### （4）plugin

插件可以执行更广泛的任务，包括打包优化到压缩以及定义环境变量等。

### （5）mode

模式指示webpack使用相应模式配置，有development（本地调试运行环境）和production（优化上线运行环境）两种模式，默认启用的plugin不同。



## 8、初始化配置

```javascript
//初始化package.json文件
npm init
//下载webapck
npm install webpack
npm install webpack-cli
```



## 9、编译打包

在package.json文件中创建build指令，执行webpack命令：

```javascript
//1、开发环境
webpack src/index.js -o build/built.js --mode=development
//作用：打包编译js和json文件，将es6模块的语法转换成浏览器识别语法

//2、生产环境
webpack src/index.js -o build/built.js --mode=production
//作用：在开发配置上多一个功能，压缩代码
```

​	其中webpack命令后的地址为入口地址，-o表示出口，出口后的地址为打包后文件输出目录，最后定义mode模式选择开发环境。会默认在node_modules文件下面的bin文件里找webpack命令。

**注意⚠️**：<font color=deepred>webpack命令不能打包css、img等文件，不能将es6以上语法转换为es5以下的语法</font>。

 

## 10、配置webpack

配置webpack一般在根目录下的webpack.config.js文件中

```javascript
// resolve用来拼接绝对路径的方法
const { resolve } = require('path');
module.exports = {
  // webpack配置
  // 入口起点
  entry: './src/index.js',
  // 输出
  output: {
    // 输出文件名
    filename: 'built.js',
    // 输出路径
    // __dirname nodejs的变量，代表当前文件的目录绝对路径
    path: resolve(__dirname, 'build')
  },
  // loader的配置
  module: {
    rules: [
      // 详细loader配置
      // 不同文件必须配置不同loader处理
      {
        // 匹配哪些文件
        test: /\.css$/,
        // 使用哪些loader进行处理
        use: [
          // use数组中loader执行顺序：从右到左，从下到上 依次执行
          // 创建style标签，将js中的样式资源插入进行，添加到head中生效
          'style-loader',
          // 将css文件变成commonjs模块加载js中，里面内容是样式字符串
          'css-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          // 将less文件编译成css文件
          // 需要下载 less-loader和less
          'less-loader'
        ]
      }
    ]
  },
  // plugins的配置
  plugins: [
    // 详细plugins的配置
  ],
  // 模式
  mode: 'development', // 开发模式
  // mode: 'production'
}

```



## 11、webpack的依赖图

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052059114.png" alt="image-20210905205944882" style="zoom:50%;" />





## 12、loader的配置方式

在webapck.config.js文件中写明配置信息，module.rules中允许配置多个loader。

module.rules的配置如下：

（1）rules属性对应的是一个数组

（2）数组中存放的一个个的rule，rule是一个对象，对象可以设置多个属性：

- test属性：用于对资源进行匹配的，使用正则表达式。
- use属性：对应的值是一个数组[UseEntry]，使用多个loader；
  - loader属性：使用单个loader，对应的值是一个字符串
  - options：可选的属性，值是一个对象或字符串，值会传入loader中；
  - 如`use:['style-loader']`是loader属性的简写方式`use:[{loader:"style-loader"}]`。
  



## 14、常用loader

### （1）css-loader

​	只是负责将css文件进行解析，把css写到js文件中，并不会把解析后的css插入到页面中。



### （2）style-loader

​	用来实现将css-loader解析后的css文件插入到页面中。



### （3）less-loader

​	就是把less文件转换为css文件。



<u>其中在每个rules的配置项中use属性的loader执行顺序是从右向左（或者说从下到上），所以css-loader要写在style-loader前面，less-loader要写在css-loader前面。</u>

```javascript
module:{
  rules:[
    {
      test:"/\.css$/",
      use:[
        "style-loader",//创建style标签，将js中样式资源插入添加到head生效
        "css-loader"
      ]
    }
  ]
}
```





### （4）file-loader

​	用来处理jpg、png等格式的图片，还可以处理字体图标，作用就是帮我们处理import/require()方式引入的一个文件资源，并且会将该文件放到输出文件夹中。

```javascript
{
    exclude: /\.(css|js|html|less)$/,
    loader:"file-loader",
    options:{
      name:"[hash:6].[ext]",
    }
}
```



### （5）url-loader

<font color=red>**Webpack5中已弃用，如果还要使用，需在options同级增加type属性`type: "javascript/auto"`**</font>

​	和file-loader方式是相似的，但是可以将较小的文件转成base64的URI。

```javascript
{
    test: /\.(png|jpe?g|gif|svg)$/,
    use: {
        loader:"url-loader",
        options:{
            //给图片文件重命名
            name:"img/[name]_[hash:6].[ext]",
              
            // 图片大小小于100kb就会被base64处理
            // 优点：减少请求数量（降低服务器压力）
            // 缺点：图片体积更大（文件请求速度变慢）
            limit:100*1024，
            
            //url-loader默认使用es6模块划解析，如果使用commonJS中require的引入方式会解析不了
            //esModule是否关闭url-loader的es6模块化，使用commonJS解析
            esModule:false
        },
        type: "javascript/auto"
    }
}
```



拓展：**PlaceHolders**

​	有时候处理后（打包后会改变名称，比如图片名称会变成长串字符）的文件名称按照一定的规则进行显示，比如想保留原来文件的拓展名或者防止重复用的hash值等。这时候可以使用<u>PlaceHolders</u>来完成。

常用的placeholder：

- 【ext】处理文件扩展名；
- 【name】处理文件的名称；
- 【hash】文件的内容，使用MD4的散列函数处理，生成的一个128位hash值
- 【contentHash】在file-laoder中和[hash]结果是一致的
- 【hash:<length>】截图hash的长度，默认32个字符太长了
- 【path】文件相对webpack配置文件的路径

```javascript
使用前
{
    test:/\.(png|ipe?g|gif|svg)$/,
    use:{
        loader:"file-loader"
    }
}

使用后
{
    test:/\.(png|ipe?g|gif|svg)$/,
    use:{
        loader:"file-loader",
        options:{
            // 出口文件夹
            outputPath:"img",
            name:"[name]_[hash:6].[ext]"
        }
    }
}
```



### （6）html-loader

<font color=red>**Webpack5中使用html-whinimg-loader替代**</font>

```javascript
{
  test: /\.html$/,
    // 处理html文件的img图片（负责引入img，从而能被url-loader进行处理）
    loader: "html-withimg-loader",
},
```



## 15、资源模块类型(asset module type)

**<font color=deepred>在webpack5之后，可以直接使用资源模块类型来替代file-loader、url-loader等</font>**。<u>资源模块类型(asset module type)</u>，通过添加4种类型的模块，来替代这些loader。

- `asset/resource`发送一个单独文件并导出URL。之前通过file-loader实现；
- `asset/inline`导出一个资源的data URI。之前使用url-loader实现；
- `asset/source`导出资源的源代码。之前通过使用raw-loader实现；
- `asset`在导出一个data URI和发送单独的文件之间自动选择。之前通过使用url-loader，并且配置资源体积限制实现。

比如加载图片的方式：

```javascript
{
    test:"/\.(jpe?g|png|gif|svg)$/",
    type:"asset/resource"
}
```

自定义文件的输出路径和文件名的方法：

#### (1)方式一：修改output，添加assetModuleFilename属性；

```javascript
output:{
    filename:"js/bundle.js",
    path:path.resolve(__dirname,"./build"),
    assetModuleFilename:"img/[name]_[hash:6][ext]"
}
```



#### (2)方式二：在rule中添加一个generate属性，并且设置filename。

```javascript
{
    test:"/\.(jpe?g|png|gif|svg)$/",
    type:"asset",
    generator:{
        filename:"img/[name]_[hash:6][ext]"
    },
    parser:{
        dataUrlCondition:{
            maxSize:100*1024
        }
    }
}
```





## 16、loader和plugin

loader是用于<u>特定的模块类型进行转换</u>，plugin可以用于执行<u>更加广泛的任务</u>，比如打包优化、资源管理、环境变量注入等。





## 17、常用的webpack插件

### （1）CleanWebpackPlugin

​	用于自动删除一遍出口文件夹，然后重新创建，省去每打包一次手动删一次的麻烦。使用时须先安装后引入。

```javascript
const {CleanWebpackPlugin} = require("clean-webpack-plugin");
plugins:[
    // 一个个的插件对象
    new CleanWebpackPlugin()
  ]
}
```



### （2）HtmlWebpackPlugin

​	用于在打包出口文件生成html文件，自动引入打包后的js文件。这个默认生成的html文件是根据ejs的一个模板生成的。使用时须先安装后引入。

```javascript
//自定义html模板
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  <body>
    <noscript>
      <strong>We're sorry but <% htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

上面代码存在的语法<% 变量 %>这是EJS模块填充数据的方式。在配置HtmlWebpackPlugin时，可以添加如下配置:

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    plugins:[
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
        //在进行htmlWebpackPlugin.options.title读取时，会读到该信息;
        title:"webpack项目",
        //指定要使用的模块所在的路径;
        template:"./plubic/index.html"
        })
    ]
}
```

但是这时编译还会报错，<link rel="icon" href="<%= BASE_URL %>favicon.ico">。因为未设置解析BASE_URL这个常量。



### （3）DefinePlugin

​	允许在编译时创建配置的全局常量，是一个webpack内置的插件,这样就可以的读取到`BASE_URL`的值。

```javascript
const {DefinePlugin} = require('webpack');
module.exports = {
    plugins:[
        new DefinePlugin({
        BASE_URL:'"./"'
        })
    ]
}
```



### （4）CopyWebapckPlugin

​	可以将public目录下的文件放到打包后的build文件中。使用时须先安装后引入。

```javascript
new CopyWebpackPlugin({
    //复制的规则在patterns中设置
    patterns:[
        {
            //from设置从哪一个源中开始复制
            from:"public",
            //to复制到的位置，可以省略，会默认复制到打包的目录下
            globOptions:{
            //globOptions设置一些额外的选项，其中可以编写需要忽略的文件
                ignore:[
                    //mac目录下自动生成的一个文件
                    '**/.DS_Store',
                    //也不需要复制，因为已经通过HtmlWebpackPlugin完成了index.html的生成
                    '**/index.html'
                ]
            }
        }
    ]
})
```



## 18、Mode配置

​	可以告知webpack使用响应模式的内置优化：默认值时production；可选的值有none、development、production。

这几个选项的区别如下表：

| 选项        | 描述                                                        |
| ----------- | ----------------------------------------------------------- |
| development | 会将DefinePlugin中process.env.NODE_ENV的值设为development。 |
| production  | 会将DefinePlugin中process.env.NODE_ENV的值设为production。  |
| none        | 不使用任何默认优化选项                                      |





## 18、webpack插件顺序

​	webpack里插件是没有先后顺序的，插件的执行机制和内部的hook回调是有关系的。





## 19、实现一个loader和plugin

### （1）loader

- loader是一个函数；
- 不能使用箭头函数，要用到上下文的this；
- source接收到的是待处理的文件源码；
- return处理后的文件源码，也是下一个loader接收到的文件源码；

```javascript
//replaceLoader.js
module.exports = function(source){
  console.log(source,this);
  return source.replace("hello webpack")
}
```

使用这个loader

```javascript
{
  test: /\.js$/,
  use: path.resolve(__dirname, './loader/replaceLoader.js')
}
```



### （2）plugin

```javascript
//格式
class PluginName{
  constructor(option){
  }
  apply(compiler){
    ...
  }
}
```

- 一定要写apply方法，webpack会通过apply方法启动插件；
- PluginName可以写成普通function，apply必须挂载到原型对象（PluginName.prototype）
- class类中的apply不能写成箭头函数；
- webpack的compiler钩子，查看钩子决定插件在哪一步做什么

```javascript
//编写一个假的html-webpack-plugin，输出一个fake.html文件
class HtmlPlugin {
 constructor (options) {
 }
 apply(compiler) {
  compiler.hooks.emit.tap('HtmlPlugin', (compolation) => {
      const content = '<html><body>fake html</body></html>'
      compolation.assets['fake.html'] = {
        source: function () {
          return content
        },
        size: function () {
          return content.length
        }
      } 
    })
 }
}
module.exports = HtmlPlugin
```

使用这个plugin

```javascript
plugin:[
  new HtmlPlugin()
]
```





## 20、babel

Babel是一个<u>工具链</u>，主要用于旧浏览器或者环境中将ECMA2015+转成向后兼容版本的JS。（JSX转成JS用的babel）。

### 1、babel原理

​	babel可以看做是一个编译器，就是将源代码转换成浏览器可以识别的另一段源代码。babel也拥有编译器的工作流程：解析阶段(parsing)，转换阶段(transformation)，生成阶段(code generation)。

**简化流程：**

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052242158.png" alt="image-20210905224200099" style="zoom: 67%;" />

**细节流程：**

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052242992.png" alt="image-20210905224238521" style="zoom: 33%;" />



### 2、设置babel

#### （1）安装

`npm install babel-loader @babel/core @babel/preset-env @babel/polyfill core-js`

#### （2）配置

- Babel/core负责转换用的一些插件

![image-20220827232143859](https://raw.githubusercontent.com/Rainchen0504/picture/master/202208272321183.png)

- babel/preset-env最新转换规则，只能转译基本语法(promise就不能转换)
- babel/polyfill负责转译所有JS新语法，但是只是为解决部分兼容性问题将所有兼容性代码引入，体积太大
  - npm i @babel/polyfill
  - import "@babel/polyfill"在入口文件中引入
- core-js负责按需转译JS新语法
  - npm i core-js
  - 按需加载useBuiltIns:"usage"
  - 指定core-js的版本，当前最新为3版本
  - 还可以设置兼容浏览器的版本targets

```javascript
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /node_modules/,//排除node_module文件
      loader: 'babel-loader',
      options: {
        // 预设：指示babel做怎么样的兼容性处理
        presets: [
          [
            '@babel/preset-env',
            {
              // 按需加载
              useBuiltIns: 'usage',
              // 指定core-js版本
              corejs: {
                version: 3
              },
              // 指定兼容性做到哪个版本浏览器
              targets: {
                chrome: '60',
                firefox: '60',
                ie: '9',
                safari: '10',
                edge: '17'
              }
            }
          ]
        ]
      }
    }
  ]
},
```



## 25、Vue打包后的不同版本

查看依赖文件中的vue文件夹中的dist文件里面的版本

### (1)vue(.runtine).global(.prod).js:

- 通用浏览器中的`<script src="...">`直接使用；
- 我们之前通过CDN引入和下载的Vue版本就是这个版本；
- 会暴露一个全局的Vue来使用；



### (2)vue(.runtime).esm-browser(.prod).js：

- 用于通过原生ES模块导入使用（在浏览器中通过`<script type="module">`来使用）



### (3)vue(.runtime).esm-bundler.js：

- 用于webpack、rollup和parcel等构建工具；
- 构建工具中默认是vue.runtime.esm-bundler.js；
- 如果我们需要解析模板template，那么需要手动指定vue.esm-bundler.js； 



### (4)vue.cjs(.prod).js：

- 服务器端渲染使用；
- 通过require()在Node.js中使用;





## 26、VSCode对SFC文件的支持

SFC单文件组件

插件一：Vetur，从Vue2开始就使用的VSCode支持Vue插件；

插件二：Volar，官方推荐的插件（后续会基于Volar开发官方的VSCode）；





## 27、Vue文件的打包过程

```javascript
npm install vue-laoder -D
```

在webpack的模板规则中进行配置

```javascript
{	test:/\.vue$/,	loader:"vue-loader"}
```

但是打包依然会报错，还需要添加@vue/compiler-sfc来对template进行解析

```javascript
npm install @vue/compiler-sfc -D
```

另外需要配置对应的Vue插件：

```javascript
const {VueLoaderPlugin} = require('vue-loader/dist/index');
new VueLoaderPlugin()
```

重新打包即可支持.vue文件的写法。





## 28、搭建本地服务器

为了完成自动编译，webpack提供了以下几种可选的方式：

- webpack watch mode；
- webpack-dev-server（常用）；
- webpack-dev-middleware（不建议使用）；



### （1）webpack watch

该模式下webpack依赖图中的所有文件，只要有一个发生更新，那么代码将被重新编译；

开启watch的方式有以下两种：

- 方式1、在导出的配置中添加watch:true；
- 方式2、在启动webpack的命令中添加--watch；



### （2）webapck-dev-server

​	watch模式下可以监听到文件的变化，但是事实上它本身是不具备自动刷新浏览器的功能的；

- 安装webpack-dev-server

```javascript
//安装dev-servernpm install webpack-dev-server -D
```

- 修改配置文件

![image-20211111193935671](https://raw.githubusercontent.com/Rainchen0504/picture/master/202111111948046.png)

webpack-dev-server在编译后<font color="red">不会写入到任何输入文件</font>，而是将bundle文件<font color="red">保留在内存中</font>。

- 配置项补充

webpack-dev-server配置项中的**contentBase**表示提供静态资源的目录：

​	<font color=deepred>在没有使用html-webpack-plugin插件时，contentBase是有效的</font>，会直接读取目录下的内容，并且如果这个目录下有index.html，就直接打开这个index.html。

​	<font color=deepred>当使用了html-webpack-plugin时，contentBase无效</font>，会直接打开打包代码输出的文件夹（output），并且打开这个文件夹下的index.html。

事实上webpack-dev-server使用了一个库叫memfs（memory-fs webapck自己写的）



### （3）热模块替换（HMR）

- HMR的全称是Hot Module Replacement，翻译为模块热替换；模块热替换是指在<font color=red>应用程序运行过程中，替换、添加、删除模块，而无需重新刷新整个页面</font>;

- HMR通过如下方式提高开发速度：

​	不重新加载整个页面，这样可以保留某些应用程序的状态不丢失；

​	只更新需要变化的内容，节省开发时间；

​	修改了css、js源码，会立即在浏览器更新，相当于直接在浏览器的devtools中直接修改样式；

- 默认情况下，webpack-dev-server已经支持HMR，我们只需要开启即可；

- 修改webpack的配置

```javascript
devServer: {
  	hot:true
}
```

![image-20211111194932104](https://raw.githubusercontent.com/Rainchen0504/picture/master/202111111949706.png)

- **<font color=red>HMR原理</font>**

express server负责直接提供静态资源的服务(打包后的资源直接被浏览器请求和解析);

**HMR Socket Server**是一个socket长连接：

​	长连接有一个最好的好处是建立连接后双方可以通信(服务器可以直接发送文件到客户端); 

​	当服务器监听到对应的模块发生变化时，会生成两个文件.json(manifest文件)和.js文件(update chunk); 

​	通过长连接，可以直接将这两个文件主动发送给客户端(浏览器);

 	浏览器拿到两个新的文件后，通过HMR runtime机制，加载这两个文件，并且针对修改的模块进行更新;



### （4）hotOnly、host配置

host设置主机地址，默认是localhost，如果希望同局域网下可以访问，可以改成0.0.0.0；

⚠️：localhost本质上是一个域名，通常解析成127.0.0.1（回环地址），表达的意思其实是我们主机自己发出去的包，直接被自己接收。

​	正常的数据库包经过：应用层 - 传输层 - 网络层 - 数据链路层 - 物理层。

⚠️：0.0.0.0监听IPV4上所有的地址，再根据端口找到不同的应用程序。



### （5）port、open、compress

 port设置监听的端口，默认情况下是8080；

open是否打开浏览器，默认是false；

compress是否为静态文件开启gzip compression，默认是fasle；



### （6）<font color=red>Proxy开发过程中解决跨域的方法</font>

​	将请求先发送到一个代理服务器，代理服务器和API服务器没有跨域的问题，就可以解决跨域问题;

配置选项：

- target：表示的是代理到的目标地址，比如 /api-hy/moment会被代理到`http://localhost:8888/api-hy/moment`;
- pathRewrite：默认情况下，我们的 /api-hy 也会被写入到URL中，如果希望删除，可以使用pathRewrite；
- secure：默认情况下不接收转发到https的服务器上，如果希望支持，可以设置为false;
- changeOrigin：表示是否更新代理后请求的headers中host地址；

![image-20211111201213601](https://raw.githubusercontent.com/Rainchen0504/picture/master/202111112012055.png)



### （7）resolve模块解析

​	resolve可以帮助webpack从每个 require/import语句中找到合适引入的模块代码。

#### ①<u>**webpack能解析三种文件的路径**</u>：

- 绝对路径

由于已经获得文件的绝对路径，因此不需要再做进一步解析；

- 相对路径

在这种情况下，使用 import 或 require 的资源文件所处的目录，被认为是上下文目录;

在 import/require 中给定的相对路径，会拼接此上下文路径，来生成模块的绝对路径;

- 模块路径

在 resolve.modules中指定的所有目录检索模块；默认值是 ['node_modules']，所以默认会从node_modules中查找文件;

可以通过设置别名的方式来替换初始模块路径，也就是alias配置属性；



#### ②<u>**extensions和alias配置**</u>

- extensions是解析到文件时自动添加扩展名

默认值是 ['.wasm', '.mjs', '.js', '.json']，想要添加加载 .vue 或者 jsx 或者 ts 等文件时，必须自己写上扩展名；

- 配置别名alias

当项目的目录结构比较深的时候，或者一个文件的路径可能需要 ../../../这种路径片段。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111112025683.png" alt="image-20211111202503725" style="zoom:50%;" />



### （8）historyApiFallback

- historyApiFallback是开发中非常常见的属性，主要作用是解决SPA页面在路由跳转之后，进行页面刷新时，返回404报错问题。

```js
module.exports = {
    devServer:{
      	historyApiFallback:true
    }
}
```

- boolean值，默认是false。
  - 如果设置为true，那么在刷新时，返回404错误时，会自动返回 index.html 的内容
- object类型的值，可以配置rewrites属性
  - 可以配置from来匹配路径，决定要跳转到哪一个页面

```js
module.exports = {
    devServer:{
        historyApiFallback:{
            rewrites:[{
                from:/.*/g,
              	//注意，to里面的参数是不能加点的
                to:"/page/index.html"
            }]
        }
    }
}
```

- 事实上devServer中实现historyApiFallback功能是通过connect-history-api-fallback库的
  - 可以查看`connect-history-api-fallback`文档





## 29、Vue CLI的运行原理

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111112030513.png" alt="image-20211111202953022" style="zoom:50%;" />





# 二、生产环境常见配置

## 1、提取css成单独文件

步骤一、安装`mini-css-extract-plugin`

步骤二、使用`MiniCssExtractPlugin.loader`替代style-loader，提取js中的css成单独文件

步骤三、调用插件，对输出的css文件进行重命名

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module: {
  rules: [
    {
      test: /\.css$/,
      use: [
        MiniCssExtractPlugin.loader,
        'css-loader'
      ] 
    }
  ] 
},
plugins: [
  new HtmlWebpackPlugin({
    template: './src/index.html'
  }),
  new MiniCssExtractPlugin({
    filename: 'css/built.css'
  }) 
]
```

js是按需加载，不提取css出来的话，就要多加载一些js文件。



## 2、css兼容性处理（设置前缀）

​	PostCSS是一个**通过JavaScript来转换样式的工具(插件)**，这个工具可以进行一些CSS的转换和适配，比如<font color=deepred>自动添加浏览器前缀，CSS样式重置等</font>。

​	post-preset-env可以帮助我们将一些现代的CSS特性，转成大多数浏览器认识的CSS，并且会根据目标浏览器或者运行时环境 添加所需的polyfill。（相当于内置autoprefixer）

步骤一：安装`npm install postcss-loader postcss-preset-env`；

步骤二：在module中注册loader，postcss-loader一般写在css-loader后面(运行之前)；

步骤三：创建postcss.config.js文件，配置插件

步骤四：注册插件

```javascript
module.exports = {
  plugins: [
    //找到package.json中browserslist里面的配置，通过配置加载指定的css兼容性样式
    require("postcss-preset-env")(),
    //添加浏览器前缀
    //require("autoprefixer")
  ],
};
```

步骤五：在package.json文件中配置不同环境下的浏览器兼容要求

```json
"browserslist": {
  // 开发环境 --> 设置node环境变量：process.env.NODE_ENV = development
  "development": [
    "last 1 chrome version",
    "last 1 firefox version",
    "last 1 safari version"
  ],
  // 生产环境：默认是遵循生产环境
  "production": [
    ">0.2%",
    "not dead",
    "not op_mini all"
  ]
}
```



## 3、压缩css

使用插件压缩css代码，减小体积，提升加载速度

步骤一：安装`npm install optimize-css-assets-webpack-plugin`

步骤二：引入插件`const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin' )`

步骤三：使用插件`new OptimizeCssAssetsWebpackPlugin()`



## 4、压缩js

将mode设置成production，生产环境下js会自动被压缩



## 5、压缩html

在HtmlWebpackPlugin配置项中添加minify配置项，一般设置两个属性

```javascript
new HtmlWebpackPlugin({
  template: './src/index.html', // 压缩html代码
  minify: {
  	// 移除空格 
  	collapseWhitespace: true, 
    // 移除注释 
    removeComments: true
  } 
})
```





# 三、Vite

## 认识vite

#### （1）Vite (法语意为"快速的") 是一种新型前端构建工具，能够显著提升前端开发体验。



#### （2）Vite构造

​	一个开发服务器，它基于原生ES模块提供了丰富的内建功能，HMR的速度非常快速;

​	一套构建指令，它使用rollup打开我们的代码，并且它是预配置的，可以输出生成环境的优化过的静态资源；



#### （3）Vite的安装和使用

Vite依赖Node的，前提安装好node环境，版本大于12.

- 安装vite：`npm install vite -g(-D)`全局或局部安装
- 通过vite启动项目`npx vite`



#### （4）Vite对css的支持

- vite直接支持css处理，可以直接导入css；
- vite直接支持css预处理，直接导入less，然后安装less编译器`npm install less -D`

- vite直接支持postcss的转换，只需要安装postcss，并且配置 postcss.config.js 的配置文件即可`npm install postcss postcss-preset-env -D`

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111112039850.png" alt="image-20211111203908651" style="zoom:50%;" />



#### （5）Vite对TypeScript的支持

 vite对TypeScript是原生支持的，它会直接使用ESBuild来完成编译；



#### （6）Vite对vue的支持

vite对vue提供第一优先级支持



#### （7）ESBuild解析（构建工具）

ESBuild的特点:

- 超快的构建速度，并且不需要缓存;
- 支持ES6和CommonJS的模块化; 
- 支持ES6的Tree Shaking;
- 支持Go、JavaScript的API;
- 支持TypeScript、JSX等语法编译; 
- 支持SourceMap;
- 支持代码压缩;
- 支持扩展其他插件;

快的原因：

- 使用Go语言编写的，可以直接转换成机器代码，而无需经过字节码;
- ESBuild可以充分利用CPU的多内核，尽可能让它们饱和运行;
- ESBuild的所有内容都是从零开始编写的，而不是使用第三方，所以从一开始就可以考虑各种性能问题;



#### （8）<font color=red>Vite脚手架</font>

Vite实际上是有两个工具的

- vite:相当于是一个构件工具，类似于webpack、rollup; 
- @vitejs/create-app:类似vue-cli、create-react-app;

```js
npm install @vitejs/create-app -g //安装vite脚手架
create-app xxx //创建项目
```

