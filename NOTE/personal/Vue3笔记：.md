## 一、基础知识

### 1、安装使用vue这个javascript库的方法：

- 在页面中通过CDN的方式引入
- 下载vue的javascript文件，并且手动引入
- 通过npm包管理工具安装使用
- 直接通过vue cli创建项目使用

方式一：CDN引入

​	CDN称之为内容分发网络，指通过相互连接的网络系统，利用最靠近每个用户的服务器，更快更可靠的将音乐、图片、视频、应用程序等文件发送给用户，来提高性能、可扩展性及低成本的网络内容传递给用户。

​	常用的CDN服务分为两种，自己的CDN服务器（需购买）；开源的CDN服务器，国际上使用较多的是unpkg、JSDeliver、cdnjs。

方式二：下载和引入

打开链接制所有代码，创建一个新文件，代码复制其中，然后引入使用。



### 2、模板语法

​	React使用的jsx语法，所以对应的代码都是比那些的类似于js的一种法；之后通过Babel将jsx编译成React.createElement函数调用。

​	Vue也支持jsx的开发模式，但是大多数情况下，使用基于HTML的模板语法，允许开发者以声明的方式将DOM和底层组件实例的数据绑定在一起，在底层的实现中，Vue将模板编译成虚拟DOM渲染函数。



### 3、根元素

​	在vue2中，template模板中只能有一个根元素；vue3是允许template中有多个根元素。



### 4、v-on的修饰符：

​		`.stop`--调用event.stopPropagation()

​		`.prevent`--调用event.preventDefault()

​		`.capture`--添加事件侦听器时使用capture模式

​		`.self`--只当事件时从侦听器绑定的元素本身触发时才触发回调

​		`.once`--只触发一次回调

​		`.left`--只当点击鼠标左键时触发

​		`.right`--只当点击鼠标右键时触发

​		`.middle`--只当点击鼠标中键时触发

​		`.passive`--{passive:true}模式添加侦听器



### 5、template元素

​	template元素可以当做不可见的包裹元素，并且在v-if上使用，但是最终template不会被渲染出来，有点类似与小程序中的block。



### 6、v-show

​	v-show是不支持template的，v-show不可以和v-else一起使用。



### 7、filter方法

​	filter方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的全部元素。filter()不会对空数组进行检测，且不会改变原数组。

```javascript
array.filter(function(currentValue,index,arr), thisValue)
```



### 8、v-for中key的作用

​	key属性主要用在Vue的虚拟DOM算法，在新旧ndoes对比时辨识VNodes；如果不适用key，Vue会使用一种最大限度减少动态元素并且尽可能尝试修改/复用相同类型的元素的算法；使用key时，会基于key的变化重新排列元素顺序，并且会移除/销毁key不存在的元素。



### 9、VNode

​	VNode全称是Virtual Node也就是虚拟节点，无论是组件还是元素，最终在Vue表现出来的都是一个个VNode，VNode的本质是一个JavaScript的对象。

例如：

```html
<div class="title" style="font-size: 30px;color: red;">哈哈</div>
```

该元素用VNode表现为：

```javascript
const vnode = {
    type:"div",
    props:{
        class:"title",
        style:{
            "font-size":"30px",
            "color":"red"
        },
    },
    children:"哈哈"
};
```



### 10、没有key的diff算法过程





### 11、有key 的diff算法过程

源码：

<img src="C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\8c80c6c2d89b4bc9929928e796dcd557\clipboard.png" alt="img" style="zoom:150%;" />

图解顺序：

#### （1）从头遍历

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\c6ae3da8357147cda9b50beeb4aea19e\clipboard.png)

#### （2）从尾部遍历

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\7313826f80964c06a21c2a39e8b04566\clipboard.png)

#### （3）旧节点遍历完成，有新节点就新增

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\f90b442824324dcabd877566cab0e5ae\clipboard.png)

#### （4）旧节点遍历完成，有旧节点就移除

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\1248825b6d924c51929397a382a95453\clipboard.png)

#### （5）特殊情况，未知或者乱序的节点

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\aba151221ebe45ffb0b5fa282932baf8\clipboard.png)



### 12、watch监听

​	watch只是在监听被监听对象的引用变化，对内部属性的变化是不会做出响应的；如果要监听对象内部属性的变化，则需要使用一个选项deep进行更深层次的侦听；如果希望一开始就立即执行一次，使用immediate选项。



### 13、特殊的事件侦听方式

​	使用$watch的API，可以再created的生命周期中使用this.$watch来侦听，第一个参数是要侦听的源，第二个参数是侦听的回调函数callback，第三个参数是额外的其它选项，比如deep、immediate。



### 14、原生实现v-model

```html
<input type="text" :value="message" @input="inputChange">
```

```javascript
data(){
    return{
        message:"";
    }
},
methods:{
    inputChange(e){
        this.message = e.target.value;
    }
}
```

v-bind绑定value属性的值；v-on绑定input事件监听到的函数，函数会取得最新的值赋到绑定的属性中。



### 15、v-model修饰符

- `.lazy`触发方式由input改为change失去焦点；
- `.number`自动将输入值转换为数字类型类型parseFloat，无法转换返回原内容；
- `.trim`自动过滤首尾的空格。



### 16、loadsh库

​	引入loadsh个库（提供了很多好用的方法的Javascript库），其中的方法浅拷贝用_.clone(要拷贝的元素)，深拷贝用_.cloneDeep(要拷贝的元素)。



## 二、Webpack

### 17、Webpack打包工具

​	Webpack是一个静态的模块化打包工具，为现代的Javascript应用程序，

​	Webpack is a static module bundler for modern Javascript applications。



### 18、Vue项目加载文件

- Javascript的打包：将ES6转成ES5，TypeScript转成Javascript；
- Css的处理：Css文件模块的加载、提取，Sass和Less等预处理器处理；
- 资源文件img、font：图片和字体文件的加载；
- HTML资源的处理：打包HTML资源文件；
- 处理vue项目的SFC文件.vue文件；



### 19、配置别名

​	vue中配置别名后，js里可以使用“@”代替src目录，但是在scss中需要写成“~@”。



### 20、webpack对项目打包的过程

​	在处理应用程序时，会根据命令或者配置文件找到入口文件，从入口开始会生成一个依赖关系图，这个依赖关系图会包含应用程序中所需的所有模块（比如.js文件、css文件、图片、文字等），然后遍历图结构，打包一个个模块（根据文件的不同使用不同的loader来解析）。



### 21、webpack的安装

#### （1）webpack：执行webpack命令，会执行node_modules下的webpack。

#### （2）webpack-cli：webpack-cli中的代码执行时，才真正利用webpack进行编译和打包。webpack在执行时是依赖webpack-cli的，如果没安装就会报错。

​	注意：在第三方的脚手架事实上时没有使用webpack-cli的，而是类似于vue-serve-cli的东西。也就是<u>不使用脚手架，在命令行手动进行webpack打包时，需要同时安装webpack和webpack-cli的。</u>



### 22、Live Server

​	本地html文件中script标签引入ES6模块，直接在浏览器打开html文件，出现报错

<img src="C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\d8d54e247299421b9d575c7fa952ab87\clipboard.png"  />

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

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\d7cbbf2431e74ab2a0845e25d1431a00\clipboard.png)

​	从错误提示看，脚本是被跨域策略给拦截了，跨域请求只支持这些协议：http, data, chrome, chrome-extension, chrome-untrusted, https.，而我们的协议是file，这里我们需要本地起一个服务器来作为资源的提供方，简单的方式是安装VSCode的一个扩展Live Server。

​	<u>Live Server</u>是一个具有实时加载功能的小型服务器，可以使用它来破解html/css/javascript，但是不能用于部署最终站点。也就是说我们可以在项目中实时用live-server作为一个实时服务器实时查看开发的网页或项目效果。



### 23、forEach()方法

​	forEach()方法用于调用数组的每个元素，并将元素传递给回调函数。forEach()对于空数组是不会执行回调函数的。

```javascript
array.forEach(function(currentValue,index,arr),thisvalue)
//currentValue，必须，当前元素
//index，可选，当前元素索引值
//arr，可选，当前元素所属的数组对象

//ES6中的写法
array.forEach(item => {执行内容})
```



### 24、webpack入口

​	当我们运行webpack时，webpack会查找当前目录下的 src/index.js作为入口；如果当前项目中没有存在src/index.js文件，那么会报错。



### 25、npm  run build

​	在脚本里面执行npm run build时(在package.json文件中配置build为webpack)不需要写npx，会默认在node_modules文件下面的bin文件里找webpack命令。



### 26、配置webpack

配置webpack一般在根目录下的webpack.config.js文件中

```javascript
cosnt path = require('path);
//导出配置信息
module.exports = {
    //入口文件
    entry:"./src/main.js";
    //出口文件
    output:{
        //出口文件名称
        filename:"bundle.js",
        //出口文件夹位置，这里是绝对路径
        path:path.resolve(__dirname,"./dist")
    }
}
```



### 27、webpack的依赖图

<img src="C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\3afba86587cf4611b885b0feeefca683\clipboard.png" alt="img" style="zoom:125%;" />



### 28、loader的配置方式

在webapck.config.js文件中写明配置信息，module.rules中允许配置多个loader。

module.rules的配置如下：

（1）rules属性对应的是一个数组

（2）数组中存放的一个个的rule，rule是一个对象，对象可以设置多个属性：

- ​		test属性：用于对资源进行匹配的，使用正则表达式。

- ​		use属性：对应的值是一个数组[UseEntry]

  ​		其中，UseEntry是一个对象，可以通过对象的属性设置一些其他属性：

  ​				loader：必须有个一个loader属性，对应的值是一个字符串；

  ​				options：可选的属性，值是一个对象或字符串，值会传入loader中；

  ​				query：目前使用options代替；

  ​		如use:['style-loader']是loader属性的简写方式use:[{loader:"style-loader"}]。



### 29、PostCSS

​	PostCSS是一个通过JavaScript来转换样式的工具(插件)，这个工具可以进行一些CSS的转换和适配，比如自动添加浏览器前缀，CSS样式重置等。

​	插件autoprefixer是用来解析css并为其添加浏览器厂商前缀的PostCSS插件。但是在配置postcss-loader时，配置插件并不需要使用autoprefixer，可以使用另一个插件<u>post-preset-env</u>。post-preset-env可以帮助我们将一些现代的CSS特性，转成大多数浏览器认识的CSS，并且会根据目标浏览器或者运行时环境 添加所需的polyfill。（相当于内置autoprefixer）

​	仅需在postcss.config.js文件中配置即可：

```javascript
module.exports = {
	plugins:[
		require("postcss-preset-env")
	]
}
```



### 30、常用loader

#### （1）css-loader只是负责将css文件进行解析，并不会把解析后的css插入到页面中。

#### （2）style-loader用来实现将css-loader解析后的css文件插入到页面中。

#### （3）less-loader就是把less文件转换为css文件。

<u>其中在配置文件中loader执行顺序是从右向左（或者说从下到上），所以css-loader要写在style-loader前面，less-loader要写在css-loader前面。</u>

#### （4）file-loader用来处理jpg、png等格式的图片，还可以处理字体图标，作用就是帮我们处理import/require()方式引入的一个文件资源，并且会将该文件放到输出文件夹中。

#### （5）url-loader和file-loader方式是相似的，但是可以将较小的文件转成base64的URI。

```javascript
{
    test: /\.(png|jpe?g|gif|svg)$/,
    use: {
        loader:"url-loader",
        options:{
            // 出口文件夹
            // outputPath:"img",
            name:"img/[name]_[hash:6].[ext]",
            // 小于100kb的图片才会做base64编码
            limit:100*1024
        }
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
//使用前
{
    test:/\.(png|ipe?g|gif|svg)$/,
    use:{
        loader:"file-loader"
    }
}

//使用后
{
    test:/\.(png|ipe?g|gif|svg)$/,
    use:{
        loader:"file-loader",
        options:{
            // 出口文件夹
            outputPath:"img",
            name:"[name]_[hash:6].[ext]"
        }
    }
}
```



### 31、资源模块类型(asset module type)

在webpack5之后，可以直接使用资源模块类型来替代file-loader、url-loader等。<u>资源模块类型(asset module type)</u>，通过添加4种类型的模块，来替代这些loader。

- `asset/resource`发送一个单独文件并导出URL。之前通过file-loader实现；
- asset/inline`导出一个资源的data URI。之前使用url-loader实现；
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



### 32、loader和plugin

​	loader是用于特定的模块类型进行转换，plugin可以用于执行更加广泛的任务，比如打包优化、资源管理、环境变量注入等。



### 33、常用的webpack插件

#### （1）CleanWebpackPlugin，用于自动删除一遍出口文件夹，然后重新创建，省去每打包一次手动删一次的麻烦。使用时须先安装后引入。

```javascript
const {CleanWebpackPlugin} = require("clean-webpack-plugin");

plugins:[
    // 一个个的插件对象
    new CleanWebpackPlugin()
  ]
}
```

#### （2）HtmlWebpackPlugin，用于在打包后的文件里生成html文件，还自动添加打包后的js文件。这个默认生成的html文件是根据ejs的一个模板生成的。使用时须先安装后引入。

```javascript
//自定义html模板
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <% htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
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

#### (3)DefinePlugin，允许在编译时创建配置的全局常量，是一个webpack内置的插件,这样就可以的读取到`BASE_URL`的值。

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

#### （4）CopyWebapckPlugin，可以将public目录下的文件放到打包后的build文件中。使用时须先安装后引入。

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

#### （5）Mode配置，可以告知webpack使用响应模式的内置优化：默认值时production；可选的值有none、development、production。

这几个选项的区别如下表：

| 选项        | 描述                                                        |
| ----------- | ----------------------------------------------------------- |
| development | 会将DefinePlugin中process.env.NODE_ENV的值设为development。 |
| production  | 会将DefinePlugin中process.env.NODE_ENV的值设为production。  |
| none        | 不使用任何默认优化选项                                      |



### 33、webpack插件顺序

​	webpack里插件是没有先后顺序的，插件的执行机制和内部的hook回调是有关系的。



### 34、babel

​	Babel是一个工具链，主要用于旧浏览器或者环境中将ECMA2015+转成向后兼容版本的JS。（JSX转成JS用的babel）。



### 35、babel的使用

#### （1）首先要安装库

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\b823559136da44cfba7bb1add4b333cf\clipboard.png)

#### （2）使用方法：

- src：源文件的目录
- --out-dir：指定要输入的文件夹dist

```javascript
npx babel src --out-dir dist
```

需要使用什么功能，就安装什么功能的插件并执行转换语句。

#### （3）但是如果要转换的内容过多，配置比较麻烦，可以使用<u>预设(preset)</u>

​	安装`@babel/preset-env`预设

```javascript
npm install @babel/preset-env -D
```

​	执行命令

```javascript
npx babel src --out-dir dist --presets=@babel/preset-env
```



### 36、babel原理

​	babel可以看做是一个编译器，就是将源代码转换成浏览器可以识别的另一段源代码。babel也拥有编译器的工作流程：解析阶段(parsing)，转换阶段(transformation)，生成阶段(code generation)。

**简化流程：**

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\8b10214b3a4e45ab8d96c0ed4b6c1392\clipboard.png)

**细节流程：**

![img](C:\Users\User\AppData\Local\YNote\data\baby_123789hh@163.com\5460e17cf98e4e4994ce92e816680714\clipboard.png)



### 37、babel-loader

```javascript
npm isntall babel-loader @babel/core
```

设置规则在加载js文件时，使用babel

```javascript
module:{
	rules:[
		{
			test:/\.m?js$/,
			use:{
				loader:"babel-laoder"
			}
		}
	]
}
```

配置完成后还需要使用指定的插件才会生效

```javascript
{
	test:/\.m?js$/,
	use:{
		loader:"babel-laoder"，
		options:{
			plugins:[
				"@babel/plugin-transform-block-scoping",
				"@babel/plugin-transform-arrow-functions"
			]
		}
	}
}
```



### 38、babel-preset

直接给webpack提供一个preset，根据预设来加载对应的插件列表，并传递给babel。

```javascript
npm install @babel/preset-env
```

此外，babel配置信息可以独立放到一个文件中，babel.config.js

```javascript
module.exports = {
	presets:[
		["@babel/preset-env"]
	]
}
```



### 39、Vue打包后的不同版本

查看依赖文件中的vue文件夹中的dist文件里面的版本

#### (1)vue(.runtine).global(.prod).js:

- 通用浏览器中的`<script src="...">`直接使用；
- 我们之前通过CDN引入和下载的Vue版本就是这个版本；
- 会暴露一个全局的Vue来使用；

#### (2)vue(.runtime).esm-browser(.prod).js：

- 用于通过原生ES模块导入使用（在浏览器中通过`<script type="module">`来使用）

#### (3)vue(.runtime).esm-bundler.js：

- 用于webpack、rollup和parcel等构建工具；
- 构建工具中默认是vue.runtime.esm-bundler.js；
- 如果我们需要解析模板template，那么需要手动指定vue.esm-bundler.js； 

#### (4)vue.cjs(.prod).js：

- 服务器端渲染使用；
- 通过require()在Node.js中使用;



### 40、VSCode对SFC文件的支持

SFC单文件组件

插件一：Vetur，从Vue2开始就使用的VSCode支持Vue插件；

插件二：Volar，官方推荐的插件（后续会基于Volar开发官方的VSCode）；



### 41、Vue文件的打包过程

```javascript
npm install vue-laoder -D
```

在webpack的模板规则中进行配置

```javascript
{
	test:/\.vue$/,
	loader:"vue-loader"
}
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


### 42、搭建本地服务器

为了完成自动编译，webpack提供了以下几种可选的方式：

- webpack watch mode；
- webpack-dev-server（常用）；
- webpack-dev-middleware（不建议使用）；

#### （1）webpack watch

​	该模式下webpack依赖图中的所有文件，只要有一个发生更新，那么代码将被重新编译；

开启watch的方式有以下两种：

- 方式1、在导出的配置中添加watch:true；
- 方式2、在启动webpack的命令中添加--watch；

#### （2）webapck-dev-server

watch模式下可以监听到文件的变化，但是事实上它本身是不具备自动刷新浏览器的功能的；

```javascript
//安装dev-server
npm install webpack-dev-server -D
```

webpack-dev-server在编译后不会写入到任何输入文件，而是将bundle文件保留在内存中。

事实上webpack-dev-server使用了一个库叫memfs（memory-fs webapck自己写的）

