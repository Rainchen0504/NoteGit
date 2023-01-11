# 官方描述

**Node*.*js** 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。*Node*.*js* 使用了一个事件驱动、非阻塞式 I/O 的模型,使其轻量又高效。

# 一、Node基础

## 1、浏览器内核

Gecko:早期被Netscape和Mozilla Firefox浏览器使用;

Trident:微软开发，被IE4~IE11浏览器使用，但是Edge浏览器已经转向Blink;

Webkit:苹果基于KHTML开发、开源的，用于Safari，Google Chrome之前也在使用;

Blink:是Webkit的一个分支，Google开发，目前应用于Google Chrome、Edge、Opera等;

事实上，经常说的<font color=orange>浏览器内核指的是浏览器的**排版引擎**</font>。排版引擎也被称为**浏览器引擎**，**页面渲染引擎**，或**样板引擎**。



## 2、JS引擎

​	浏览器内核一般由两部分组成，即渲染引擎和JS引擎。渲染引擎负责HTML解析、布局、渲染等相关工作；JS引擎负责解析、执行JS代码。

编写的JavaScript无论你交给浏览器或者Node执行，最后都是需要被CPU执行的。但是CPU只认识自己的指令集，实际上是机器语言，才能被CPU所执行。所以需要JavaScript引擎帮助我们**将JavaScript代码翻译成CPU指令来执行**。

常见的JS引擎有以下几种：

- **SpiderMonkey**:第一款JavaScript引擎，由Brendan Eich开发(也就是JavaScript作者)；
- **Chakra**:微软开发，用于IE浏览器；
- **JavaScriptCore**:WebKit中的JavaScript引擎，Apple公司开发；
- **V8**:Google开发的强大JavaScript引擎，也帮助Chrome从众多浏览器中脱颖而出；



## 3、V8引擎

​	V8是用C ++编写的Google开源高性能JavaScript和WebAssembly引擎，它用于Chrome和Node.js等。

​	跨平台性：它实现ECMAScript和WebAssembly，并在Windows 7或更高版本，macOS 10.12+和使用x64，IA-32， ARM或MIPS处理器的Linux系统上运行。

​	V8可以独立运行，也可以嵌入到任何C ++应用程序中。



#### v8引擎中JS代码执行过程

- **Parse**模块<font color=orange>将JS代码转换成AST（抽象语法树）</font>，因为解释器不能直接识别JS代码；
  - 如果函数没有被调用，那么是不会转换成AST的。
- **Ignition**是一个<font color=orange>解释器，会将AST转换成ByteCode（字节码）</font>；
  - 同时会收集TurboFan优化所需要的信息（比如函数参数的类型信息，有了类型才能真正真实的运算）
  - 如果函数值调用一次，Ignition会进行解释并执行字节码
- **TurboFan**是一个<font color=orange>编译器，可以将字节码直接编译为CPU可以直接执行的机器码</font>；
  - 如果一个函数被多次调用，那么就会被标记为热点函数，那么就会经过TurboFan转换成优化的机器码，提高代码的执行性能
  - 但是，机器码实际上也会被还原为ByteCode，这是因为如果后续执行函数的过程中，类型发生了变化(比如sum函数原来执行的是number类型，后来执行变成了string类型)，之前优化的机器码并不能正确的处理运算，就会逆向的转换成字节码;



## 4、浏览器和Node架构的区别

![image-20220322161837523](https://raw.githubusercontent.com/Rainchen0504/picture/master/202203221618648.png)



## 5、Node.js架构

![image-20220322163648233](https://raw.githubusercontent.com/Rainchen0504/picture/master/202203221636333.png)

编写的JavaScript代码会经过V8引擎，再通过Node.js的Bindings，将任务放到Libuv的事件循环中。

**libuv**(Unicorn Velociraptor)是使用C语言编写的库。libuv提供了事件循环、文件系统读写、网络IO、线程池等等内容;



## 6、Node的输出

- 最常用的输入内容的方式:console.log
- 清空控制台:console.clear
- 打印函数的调用栈:console.trace



## 7、常见的全局对象

![image-20221004174042938](https://raw.githubusercontent.com/Rainchen0504/picture/master/202210041740437.png)

- **__dirname:**<font color=deepred>获取**当前文件所在的路径**，不包括后面的文件名</font>；
- **__filename:**<font color=deepred>获取**当前文件所在的路径和文件名称**，包括后面的文件名</font>；
- **process对象:**process提供了Node进程中相关的信息；
- **console对象:**提供了简单的调试控制台；
- **定时器函数:**在Node中使用定时器有好几种方式：
  - setTimeout(callback, delay[, ...args]):callback在delay毫秒后执行一次
  - setInterval(callback, delay[, ...args]):callback每delay毫秒重复执行一次
  - setImmediate(callback[, ...args]):callbackI / O事件后的回调的“立即”执行
  - process.nextTick(callback[, ...args]):添加到下一次tick队列中



## 8、global和window的区别

​	在浏览器中执行的JavaScript代码，如果在顶级范围内通过var定义的一个属性，默认会被添加到window 对象上。但是在node中，通过var定义一个变量，它只是在当前模块中有一个变量，不会放到全局中。



# 二、JS模块化

​	模块化开发最终的目的：

1. 将程序划分成<font color=red>一个个小的结构</font>。
2. 这个结构中编写属于自己的逻辑代码，<font color=red>有自己的作用域</font>，不会影响到其他的结构。
3. 这个结构可以将自己希望暴露的变量、函数、对象等<font color=red>导出给其结构使用</font>。
4. 也可以通过某种方式，<font color=red>导入另外结构中的变量、函数、对象等</font>。

按照这种**结构划分**开发程序的过程，就是<font color=orange>**模块化开发**</font>的过程



## 1、CommonJS和Node

​	CommonJS是一个规范，最初提出来是在浏览器以外的地方使用，并且当时被命名为**ServerJS**，后来为了 体现它的广泛性，修改为**CommonJS**，平时我们也会简称为CJS

​	Node中对CommonJS进行了支持和实现，Node中每一个js文件都是一个单独的模块。这个模块中包括CommonJS规范的核心变量:**exports、module.exports、require**;

- exports和module.exports可以负责对模块中的内容进行导出;
- require函数可以帮助导入其他模块(自定义模块、系统模块、第三方库模块)中的内容。



## 2、CommonJS导入和导出

### （1）exports导出

<font color=red>exports**是一个对象**，可以在这个对象中添加很多个属性，添加的属性会导出</font>

```js
//bar导出文件
const name = "zhang";
const sayHello = function(name){
  console.log("hello")
}
exports.name = name;
exports.sayHello = sayHello;

//main导入文件,bar变量等于exports对象,相当于浅拷贝
const bar = require('./bar')
console.log(bar.name)
```



### （2）module.exports

​	CommonJS中是没有module.exports的概念的。但是为了实现模块的导出，Node中使用的是Module的类，每一个模块都是Module的一个实例，也就是 module;所以在Node中真正用于导出的其实根本不是exports，而是module.exports。

为什么exports也可以导出：module对象的exports属性是exports对象的一个引用，也就是说module.exports = exports = main中的bar。



### （3）require

<font color=red>require**是一个函数**，可以帮助我们引入一个文件(模块)中导入的对象</font>

常见的查找规则：require(X)

- 情况一：X是一个核心模块，比如path、http
  - 直接返回核心模块，并且停止查找
- 情况二：以 ./ 或 ../ 或 /(根目录)开头的
  - 第一步：将X当做一个文件在对应的目录下查找
    - 1、如果有后缀名，按照后缀名的格式查找对应的文件
    - 2、如果没有后缀名，会按照如下顺序
      - 直接查找文件X
      - 查找X.js文件
      - 查找X.json文件
      - 查找X.node文件
  - 第二步：没有找到对应的文件，将X作为一个目录
    - 查找目录下面的index文件
      - 查找X/index.js文件
      - 查找X/index.json文件
      - 查找X/index.node文件
  - 如果没有找到，那么报错:not found
- 情况三：直接是一个X(没有路径)，并且X不是一个核心模块
  - 如果没有找到，那么报错:not found



## 3、CommonJS规范缺点

### （1）加载模块是同步的

同步的意味着只有等到对应的模块加载完毕，当前模块中的内容才能被运行；

这个在服务器不会有什么问题，因为服务器加载的js文件都是本地文件，加载速度非常快；



### （2）浏览器加载存在阻塞

浏览器加载js文件需要先从服务器将文件下载下来，之后在加载运行;

采用同步的就意味着后续的js代码都无法正常运行，即使是一些简单的DOM操作;



### （3）浏览器通常不采用

webpack中使用CommonJS是另外一回事，它会将我们的代码转成浏览器可以直接执行的代码。



### （4）早期浏览器采用AMD或CMD

目前一方面现代的浏览器已经支持ES Modules，另一方面借助于webpack等工具可以实现对CommonJS或者ES Module代码的转换，所以AMD和CMD已经使用非常少了。

#### 	（4.1）AMD规范

AMD主要是应用于浏览器的一种模块化规范：

​	1、AMD是Asynchronous Module Definition(异步模块定义)的缩写

​	2、它采用的是异步加载模块；

​	3、事实上AMD的规范还要早于CommonJS，但是CommonJS目前依然在被使用，而AMD使用的较少了；

​	4、AMD实现的比较常用的库是require.js和curl.js。

#### 	

#### 	（4.2）CMD规范

CMD规范也是应用于浏览器的一种模块化规范：

​	1、CMD 是Common Module Definition(通用模块定义)的缩写;

​	2、采用了异步加载模块，但是它将CommonJS的优点吸收了过来；

​	3、CMD使用也非常少了；

​	4、CMD实现的比较常用的库是SeaJS。





## 4、ES Module导入和导出

### （1）export导出

- 导出方式一

  在语句声明的前面直接加上export关键字

  ```js
  export const age = 18
  ```

- 导出方式二

  将所有需要导出的标识符添加到export后面的{}中

  ```js
  export {name,age,height}
  ```

- 导出方式三

  导出时通过as关键字给标识符起别名

  ```js
  export {name as realname}
  ```

  

### （2）import导入

- 导入方式一

  import {标识符列表} from '模块'，模块必须要有文件类型后缀

  ```js
  import { name, age } from "./foo.js"
  ```

- 导入方式二

  导入时通过as关键字给标识符起别名

  ```js
  import { name as fname } from "./foo.js"
  ```

- 导入方式三

  通过 * 将模块功能放到一个模块功能对象上

  ```js
  import * as info from "./foo.js"
  ```



## 5、ES Module特点

CommonJS、AMD、CMD等都是**<font color=blue>社区规范</font>**，ESModule是**<font color=blue>ES自己推出的模块化系统</font>**。

1. 使用了<font color=deepred>**import和export**</font>关键字；
   1. export负责将模块内的内容导出
   2. import负责从其他模块导入内容
2. 采用<font color=deepred>**编译期的静态分析**</font>，并且也<font color=deepred>**加入了动态引用的方式**</font>；
3. 采用ES Module将<font color=deepred>**自动采用严格模式**</font>:**use strict**



## 6、ES Module解析流程

### （1）阶段一:构建

​	根据地址查找js文件，并且下载，将其解析成模块记录；



### （2）阶段二:实例化

​	对模块记录进行实例化，并且分配内存空间，解析模块的导入和导出语句，把模块指向对应的内存地址。



### （3）阶段三:运行

​	运行代码，计算值，并且将值填充到内存地址中



## 7、CommonJS和ESModule加载过程

### 1.CommonJS

#### （1）加载js文件的过程是<font color=red>运行时加载，是同步的</font>

运行时加载意味着是js引擎在执行js代码的过程中加载模块；

同步的就意味着一个文件没有结束之前，后面的代码不会执行；

#### （2）通过module.exports<font color=red>导出的是一个对象</font>

导出的是一个对象意味着可以将这个对象的引用在其他模块中赋值给其他变量；

但是最终指向的都是同一个对象，那么一个变量修改了对象的属性，所有的地方都会被修改；



### 2.ES Module

#### （1）加载js文件的过程是<font color=red>编译(解析)时加载的，是异步的</font>

import不能和运行时相关的内容放在一起使用，不能把import放到`if`语句中。所以称ES Module是静态解析的。

#### （2）JS引擎在import时获取过程是异步的，<font color=red>不会阻塞主线程继续执行</font>

设置了 type=module 的代码，相当于在script标签上也加上了 async 属性。后面代码不会阻塞执行；

#### （3）通过export<font color=red>导出的是变量本身的引用</font>

export在导出一个变量时，js引擎会解析这个语法，并且创建**模块环境记录**，它会和变量进行绑定，在导入的地方，是可以实时的获取到绑定的最新值的；



### 3.commonJS和ES Module交互

- Node环境下，CommonJS不能加载ES Module
- ES Module可以加载CommonJS



# 三、Node常用内置模块

## 1、内置模块path

path模块用于对路径和文件的处理，比如在服务器上解决不同系统开发时的文件路径问题(符号不同)

### （1）path常见API

- 从路径中获取信息
  - dirname：获取文件的父文件夹；
  - basename：获取文件名；
  - extname：获取文件扩展名；
- 路径的拼接
  - path.join
- 文件和文件夹的拼接
  - 绝对路径拼接path.resolve
  - resolve函数会判断拼接的路径前面是否有 /或../或./。
    - 如果有，表示是一个绝对路径，会返回对应的拼接路径；
    - 如果没有，会和当前执行文件所在的文件夹进行路径拼接；



## 2、内置模块fs

fs即File System，标识文件系统。

### （1）fs的API操作方式

#### 	1.方式一：

同步操作文件，代码会被阻塞，不能继续执行；

#### 	2.方式二：

异步回调函数操作文件，代码不会被阻塞，需要传入回调函数，获取结果时触发；

#### 	3.方式三：

异步Promise操作文件，代码不会阻塞，通过fs.promises方法操作，返回promise通过then和catch处理；

```js
//举个例子
const fs = require('fs')
const filepath = "./abc.txt"

//方式一：同步操作
const info = fs.statSync(filepath);
console.log("后续需要执行的代码");
console.log(info);

//方式二：异步操作
fs.stat(filepath, (err, info) => {
  if (err) { return; }
  //是否是文件
  console.log(info.isFile());
  //是否是文件目录
  console.log(info.isDirectory());
});
console.log("后续需要执行的代码");

//方式三：promise
fs.promises.stat(filepath)
  .then((info) => {
    console.log(info);
  })
  .catch((err) => {
    console.log(err);
  });
console.log("后续需要执行的代码");
```



### （2）文件描述符

文件描述符：每个打开的文件都分配一个数字标识符，系统层使用这些标识跟踪文件，称为文件描述符。

fs.open()方法用于分配新的文件描述符：一旦被分配，可用于文件读写。

```js
const fs = require('fs');
fs.open("./abc.txt", (err, fd) => {
  if (err) { return; }
  // 通过描述符去获取文件的信息
  fs.fstat(fd, (err, info) => {
    console.log(info);
  })
})
```



### （3）文件的读写

```js
fs.readFile(path[, options], callback) //读取文件的内容;
fs.writeFile(file, data[, options], callback) //在文件中写入内容;

// 1.文件写入
const content = "你好啊,李银河";
fs.writeFile("./abc.txt", content, { flag: "a" }, (err) => {
  console.log(err);
});

// 2.文件读取
fs.readFile("./abc.txt", {encoding: 'utf-8'}, (err, data) => {
	console.log(data);
});
```

其中option可配置以下两种参数：

- flag：写入的方式
  - w：打开文件写入，默认值
  - w+：打开文件读写，不存在则创建文件
  - r+：打开文件读写，不存在抛异常
  - r：打开文件读取的默认值
  - a：打开要写入的文件，将流放在文件末尾，如果不存在则创建文件
  - a+：打开文件以进行读写，将流放在文件末尾，如果不存在则创建文件
- encoding：字符的编码
  - 目前都基本都使用的是UTF-8编码
  - 如果不填，返回的是Buffer



### （4）文件夹操作

#### 4.1、新建文件夹

```js
//使用fs.mkdir()或fs.mkdirSync()创建一个新文件夹
//举个例子
const dirname = './why';
if (!fs.existsSync(dirname)) {
  fs.mkdir(dirname, err => {
    console.log(err);
  });
}
```



#### 4.2、获取文件夹内容

##### 同步读取文件夹readdirSync

```js
const result = fs.readdirSync(folder)
console.log(result)
```



##### 异步回调读取文件夹readdir

```js
fs.readdir(folder, (err,files)=>{
  files.forEach(file => {
    if(file.isDirextory()){
      const newFolder = path.resolve(dirname,file.name);
      readFloders(newFolder);
    }else{
      console.log(file.name)
    }
  })
})
```



#### 4.3、文件重命名

```js
fs.rename("./why", "./kobe", err => {
  console.log(err);
})
```



#### 4.4、删除文件夹

```js
//同步删除文件夹
fs.rmdirSync()
//异步删除文件夹
fs.mrdir()
```



## 3、events模块

​	Node核心API都是基于异步事件驱动的：

​	事件发射器发出某一个事件，可以监听这个事件，并在事件发生时传入一个回调函数。

**发出事件和监听事件都是通过EventEmitter类来完成的，它们都属于events对象**。

- emitter.on(eventName, listener)：监听事件，也可以使用 addListener；
- emitter.off(eventName, listener)：移除事件监听，也可以使用removeListener；
- emitter.emit(eventName[, ...args])：发出事件，可以携带一些参数；
- emitter.once(eventName, listener)：事件监听一次；
- emitter.prependListener()：将监听事件添加到最前面；
- emitter.prependOnceListener()：将监听事件添加到最前面，但是只监听一次；
- emitter.removeAllListeners([eventName])：移除所有的监听器；

```js
//应用举例：
const EventEmmiter = require('events');
//监听事件
const bus = new EventEmmiter();

function clickHandle(args){
  console.log("监听到click事件",args)
}

bus.on("click",clickHandle);

setTimeout(()=>{
  bus.emit("click","zhangyuchen");
  bus.off("click",clickHandle);
})
```



此外，EventEmmiter实例还有一些属性可以记录一些信息

- emitter.eventNames():返回当前 EventEmitter对象注册的事件字符串数组;
- emitter.getMaxListeners():返回当前 EventEmitter对象的最大监听器数量，可以通过setMaxListeners() 来修改，默认是10;
- emitter.listenerCount(事件名称):返回当前 EventEmitter对象某一个事件名称，监听器的个数;
- emitter.listeners(事件名称):返回当前 EventEmitter对象某个事件监听器上所有的监听器数组;



# 四、包管理工具

## 1、包管理工具npm

npm管理的包其实发布到registry上的，下载时也是在registry上下载的。

npm init创建package.json配置文件。

npm包遵循semver版本规范X.Y.Z：

- X主版本号(major):当你做了不兼容的 API 修改(可能不兼容之前的版本);
- Y次版本号(minor):当你做了向下兼容的功能性新增(新功能增加，但是兼容之前的版本);
- Z修订号(patch):当你做了向下兼容的问题修正(没有新功能，修复了之前版本的bug);

##### 注意⚠️：^和～的区别

- ^x.y.z:表示x是保持不变的，y和z永远安装最新的版本;
- ~x.y.z:表示x和y保持不变的，z永远安装最新的版本;



## 2、npm执行流程

![image-20220327214802234](https://raw.githubusercontent.com/Rainchen0504/picture/master/202203272148320.png)

- npm install会检测是有package-lock.json文件
  - 没有lock文件
    - 分析依赖关系
    - 从registry仓库中下载压缩包
    - 获取到压缩包后会对压缩包进行缓存
    - 将压缩包解压到项目的node_modules文件夹中
  - 有lock文件
    - 检测和pack.json版本是否一致
      - 不一致会重新构建依赖关系，直接走顶层的流程
    - 优先查找缓存
      - 没找到会从registry仓库下载，直接走顶层流程
    - 查找到获取压缩文件，解压到node_module文件夹



## 3、npm命令

```js
//强制重新build
npm rebuild
//清除缓存
npm cache clean
```



## 4、Yarn

另一node包管理工具，由Facebook、Google、Exponent 和 Tilde 联合推出。



## 5、npx

npx是npm5.2以后自带的命令，常见的用途是<font color=deepred>调用项目中某个模块的指令</font>。

npx的原理非常简单，它会到当前目录的node_modules/.bin目录下查找对应的命令;



## 6、npm发包

- 注册npm账号
- 命令行登录`npm login`
- 修改package.json
- 发布到npm仓库中`npm publish`
- 更新仓库
  - 修改版本号
  - 重新发布
- 删除发布的包`npm unpublish`
- 让发布的包过期`npm deprecate`



## 6、pnpm

### （1）特点

- 当使用npm或Yarn时，如果有100个项目，并且所有项目都有一个相同的依赖包，那么在硬盘上就需要<font color=red>保存100份该相同依赖包的副本</font>；

- 当使用pnpm时，依赖包被<font color=red>存放在一个统一位置</font>
  - 如果对<font color=blue>同一依赖包使用相同的版本</font>，那么磁盘上<font color=red>只有这个依赖包的一份文件</font>；
  - 如果你对<font color=blue>同一依赖包需要使用不同的版本</font>，则仅有<font color=red>版本之间不同的文件会被存储起来</font>；



### （2）存储位置

- 在 Linux 上，默认是 ~/.local/share/pnpm/store
- 在 Windows 上: %LOCALAPPDATA%/pnpm/store
- 在 macOS 上: ~/Library/pnpm/store

可以通过pnpm store path命令获取当前活跃的store目录



### （3）安装和使用

安装`npm install -g pnpm`

| npm命令             | pnpm等价命令      |
| ------------------- | ----------------- |
| npm install         | pnpm install      |
| npm install <pkg>   | pnpm add <pkg>    |
| npm uninstall <pkg> | pnpm remove <pkg> |
| npm run <cmd>       | pnpm <cmd>        |



### （4）从npm迁移到pnpm

1. 安装`pnpm`为全局包

   ```shell
   npm i -g pnpm@latest
   ```

2. 从项目中移除`npm`依赖项安装库和`lock`文件

   ```shell
   rm -rf node_modules package-lock.json
   ```

3. 使用`pnpm`安装项目依赖项

   ```shell
   pnpm i
   ```



# 五、创建个人脚手架





# 六、事件循环和异步IO

## 1、事件循环

- 浏览器事件循环是编写的JS和浏览器API调用的一个沟通桥梁；
- Node事件循环是编写的JS和系统调用之间的沟通桥梁；

JS是**单线程**的，但是**JS线程有自己的容器进程：浏览器或者Node**

​	JS代码是在一个单独的线程中执行的，只能同时做一件事，打开一个tab页会生成一个进程（防止卡死导致整个浏览器都崩溃推出），每个进程中有很多线程），其中包含JS线程。

​	JS代码执行任务过程中，会判断任务是同步还是异步，同步进入主线程，异步加入到调用栈中，不影响后续代码的执行，执行完主线程后，从调用栈中读取对应的函数进入主线程执行。

​	不同的任务会进入到不同的队列，事件循环中维护着两个队列，分别为宏任务和任务两列，执行每个宏任务先查看微任务队列是否有任务要执行，宏任务执行之前，必须保证微任务队列是空的。

​	**"<font color=red>有微则微，无微则宏</font>"**

### （1）宏任务

setTimeout、setInterval、ajax、DOM监听等

### （2）微任务

Promise中的then回调、Mutation Observe API、queueMicrotask()等

### （3）async和await

async和await是Promise的一个语法糖：

- await关键字后面执行的代码，看作是包裹在`(resolve,reject) => {函数执行}`中的代码 ，属于直接执行的代码；

- await的下一条看作是then(res => {函数执行})中的代码l；



## 2、Node架构

​	浏览器中的EventLoop是根据HTML5定义的规范来实现的，不同的浏览器可能会有不同的实现，而Node中是由 libuv实现的。

​	libuv是一个多平台专注异步IO的库。

![image-20220330174016822](https://raw.githubusercontent.com/Rainchen0504/picture/master/202203301740279.png)

​	libuv主要维护EventLoop和worker threads（线程池）

​	EventLoop负责调用系统的一些操作：文件IO、Network、子线程等。



## 3、阻塞和非阻塞IO

- 任何程序中的文件操作都需要系统调用（操作系统的文件系统），**<font color=orange>文件的操作就是操作系统的IO操作（输入、输出）</font>**。
- 操作系统提供了阻塞式调用和非阻塞式调用
  - **阻塞式调用**：调用结果返回前，线程处于阻塞状态；
  - **非阻塞式调用**：调用执行后，线程不会停止；
- 开发中的很多耗时操作都使用的是非阻塞式调用。

### （1）非阻塞IO的问题

​	非阻塞IO意味着没有获取到需要读取的结果，因此需要频繁的确定读取的数据是否完整。这个过程称之为**<font color=orange>轮询</font>**。

​	轮询一般由libuv提供的线程池来完成。线程池会通过轮训等方式等待结果,获取到结果时，就将对应的回调放到事件循环中。事件循环就负责接管后续的回调工作，告知JS程序执行对应的回调。

### （2）阻塞和非阻塞、同步和异步的区别

- 阻塞和非阻塞是对于被调用者来说的：
  - 也就是系统调用，操作系统提供了阻塞调用和非阻塞调用;
- 同步和异步是对于调用者来说的：
  - 也就是自己的程序；
  - 如果发起调用后不进行其它任何操作，只等待结果，称为同步调用；
  - 如果发起调用之后，并不会等待结果，继续完成其他的工作，等到有回调时再去执行，这个过程就是异步调用;
- libuv采用的就是非阻塞异步IO的调用方式；



## 4、Node事件循环的阶段

一次完整的事件循环Tick分成多个阶段：

### （1）定时器

本阶段执行已经被 setTimeout() 和 setInterval() 的调度回调函数。

### （2）待定回调

对某些系统操作(如TCP错误类型)执行回调，比如TCP连接时接收到 ECONNREFUSED。

### （3）idle，prepare

仅系统内部使用。

### （4）轮询

检索新的 I/O 事件;执行与 I/O 相关的回调;

### （5）检测

setImmediate() 回调函数在这里执行。

### （6）关闭的回调函数

一些关闭的回调函数，如:socket.on('close', ...)



## 5、Node的微任务和宏任务

宏任务：setTimeout、setInterval、IO事件、setImmediate、close事件;

微任务：Promise的then回调、process.nextTick、queueMicrotask;



## 6、Node事件执行过程

Main script直接执行的任务

nextTicks

其它微任务

timers

immediate

注意：

​	**如果事件循环开启的时间小于 setTimeout函数的执行时间**，那么意味着先开启了事件循环，在这个循环中执行到timer阶段是没有定时器进入到timer queue中的，所以不会执行定时器的内容。

​	**如果事件循环开启的时间大于 setTimeout函数的执行时间**，意味着在第一次 tick中，已经准备好了timer queue，会按照上面的顺序执行。



# 七、Buffer使用

## 1、Buffer和二进制

​	服务端为了实现更多功能，需要直接操作二进制数据，因此Node提供了一个类Buffer，同时这个类是全局的。

​	Bugger可以看成一个二进制数组，数组中每一项可以保存8位二进制。

- 补充内容：
  - 通常会将8位合在一起作为一个单元，这个单元称之为一个字节(byte)；
  - 1byte = 8bit，1kb=1024byte，1M=1024kb；
  - TCP传输的是字节流，在写入和读取时都需要说明字节的个数；



## 2、创建Buffer

### （1）字符串、汉字创建Buffer

```js
const buffer01 = new Buffer('chen');
console.log(buffer01);	//<Buffer 63 68 65 6e>

const buffer01 = new Buffer('张雨晨');
console.log(buffer01);	//<Buffer e5 bc a0 e9 9b a8 e6 99 a8>
```



### （2）创建指定位数的Buffer

```js
const buffer = Buffer.alloc(8);
console.log(buffer); 
//<Buffer 00 00 00 00 00 00 00 00>

buffer01[0] = "w".charCodeAt();
buffer01[1] = 100;
buffer01[2] = 0x66;
console.log(buffer);
//<Buffer 77 64 66 00 00 00 00 00>
```



## 3、Buffer和文件读取

#### （1）文本读取

```js
fs.readFile('./test.txt',(err,data) => {
  console.log(data)
  //<Buffer e6 9d 8e e9 93 b6 e6 b2 b3>可以通过toString()编译
})
```



#### （2）图片读取

```js
//图片读取同上
fs.readFile("./bar.png", (err, data) => {
  console.log(data);
  //非常长的字节
});

//引入更为高效的sharp库
const fs = require("fs");
const sharp = require("sharp");

sharp("./007.jpg").resize(200, 200).toFile("./first.png");

sharp("./007.jpg")
  .resize(300, 300)
  .toBuffer()
  .then((data) => {
    fs.writeFile("./second.png", data, (err) => console.log(err));
  });
```



# 八、Stream

## 1、流

​	从一个文件读取数据时，文件的二进制（字节）数据源源不断被读取到程序中，这个一连串的字节就是程序中的流。

​	流可读可写，是连续字节的一种表现形式和抽象概念。



## 2、文件读写的Stream

- Node中很多对象是基于流实现的：
  - http模块的request和response对象；
  - process.stdout对象

- 另外所有的流都是EventEmitter实例；
- Node中有四种基本流类型
  - Writable:可以向其写入数据的流(例如 fs.createWriteStream())。
  - Readable:可以从中读取数据的流(例如 fs.createReadStream())。
  - Duplex:同时为Readable和的流Writable(例如 net.Socket)。
  - Transform:Duplex可以在写入和读取数据时修改或转换数据的流(例如zlib.createDeflate())。

举例介绍writable和readable

### 1、Readable：

- 之前读取文件信息的方式：

```js
fs.readFile("./foo.txt", (err, data) => {
  console.log(data)
})
```

这会一次性将一个文件的所有内容都读到程序中，会产生文件过大、无法控制一次读取大小的问题。

- 因此，可以使用createStream，属于fs模块读取值的一个方法。

```js
const read = fs.createStream("./foo.txt", {
  start:3,//文件读取的开始位置
  end:8,//问卷读取结束的位置
  highWaterMark:4//一次读取的字节长度，默认是64KB
})
```

- 读取完成后怎么获取流数据呢？

```js
//监听data事件，获取读取值
read.on("data", (data) => {
  console.log(data)
})
read.on("open", () => {
  console.log("文件被打开")
})
read.on("end", () => {
  console.log("文件读取结果")
})
read.on("close", () => {
  console.log("文件被关闭")
})
read.parse()//暂停读取
read.resume()//继续读取
```



### 2、Writeable

- 之前写入文件信息的方式：

```js
fs.writeFile("./foo.txt","内容", (err) => {
  console.log(err)
})
```

相当于一次性将所有的内容写入到文件中，无法精确控制写入位置和写入频率。

- 因此，可以使用createWriteStream，属于fs模块写入值的一个方法。

```js
const writer = fs.createWriteStream("./foo.txt", {
  flags:"r+",//默认是w，使用r或r+写入，如果希望追加不设置start使用a或a+
  start:2,//写入的位置
})
```

- 写入完成后的监听

```js
writer.write("你好啊", (err) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log("写入成功");
});
write.close();
//不会主动关闭，需要调用close方法关闭，但是真实情况下不调用close，一般用end方法
write.on("close", () => {
  console.log("文件被关闭")
})
```

1. 但是不能监听到close：写入流打开后是不会自动关闭的，必须手动关闭然后发出一个finish事件。
2. 实际中常用end方法：end**方法相当于执行了两步**，write写入数据和调用close方法。



###  3、pipe

可以将读取到的输入流，手动的放到输出流中进行写入:

```js
//传统写法，把一个读取的内容写入到另一个文件中
fs.readFile('./bar.txt', (err,data) => {
  fs.writeFile('./baz.txt', data, (err) => {
    console.log(err)
  })
})

//Stream写法
const reader = fs.createReadStream("./foo.txt");
const writer = fs.createWriteStream('./foz.txt');
reader.pipe(writer);
writer.close();
```



# 九、Http模块

​	HTTP：超文本传输协议，是一种用于分布式、协作式和超媒体信息系统的应用层协议

## 1、Web服务器

​	当应用程序需要某一个资源时，可以向一个台服务器，通过Http请求获取到这个资源;提供资源的服务器，就是一个Web服务器；

![image-20220402114447456](https://raw.githubusercontent.com/Rainchen0504/picture/master/202204021144680.png)

- 目前有的开源Web服务器：Nginx、Apache(静态)、Apache Tomcat(静态、动态)、Node.js。



### （1）创建简单的web服务器

```js
const http = require('http');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  res.end("Hello Server");
});

// 启动服务器,并且制定端口号和主机
server.listen(HTTP_PROT, "0.0.0.0", () => {
  console.log(`服务器在${HTTP_PROT}启动成功`);
});
```

注意⚠️：

可以使用npm安装**nodemon**来维持服务器更新，nodemon XX要执行的文件，就可以实现不关闭终端更新



### （2）第二种创建服务器

- http.createServer会返回服务器的对象；
- 底层是使用new Server创建对象；

```js
function createServer(opts, requestListener){
  return new Server(opts, requestListener);
}
```

- 可以手动创建服务器对象

```js
const server2 = new http.Server((req, res) => {
  res.end("hello server2")
});
server2.listen(9000, () => {
  console.log("服务器启动成功～")
})
```

创建Server时会传入一个回调函数，这个回调函数被调用时会传入两个参数：

1. req：request请求对象，包含请求相关的信息；
2. res：response响应对象，包含要发送给客户端的信息；



### （3）监听主机和端口

#### 	1.Server

**Server**通过listen方法来开启服务器，并且在某一个主机和端口上监听网络请求。

#### 	2.listen函数

- 参数一：端口port
  - 可以不传，系统会默认分配端；
- 参数二：主机host域名
  - localhost：本质上是一个域名，通常情况下会被解析成127.0.0.1;
  - 127.0.0.1：回环地址表达的意思其实是我们主机自己发出去的包，直接被自己接收；
    - 正常的数据库经过 应用层-->传输层-->网络层-->数据链路层-->物理层；
    - 回环地址是在网络层直接获取到，不经过数据链路层和物理层；
    - 比如监听 127.0.0.1时，在同一个网段下的主机中，通过ip地址是不能访问的；
  - 0.0.0.0：监听IPV4上所有的地址，再根据端口找到不同的应用程序，监听 0.0.0.0时，在同一个网段下的主机中，通过ip地址是可以访问的。不写端口如果是IPv4默认是0.0.0.0
- 参数三：回调函数
  - 服务器启动成功时的回调函数



## 2、request对象

向服务器发送请求时携带的信息Node会封装到一个request对象中。

```js
const server = http.creater((req,res) => {
  console.log(req.url);//请求URL
  console.log(req.method);//请求方式GET、POST
  console.log(req.headers);//携带信息(数据格式、编码信息、编码格式)
  res.end("hello world")
})
```



### （1）URL的处理

#### 	1.URL用法

​	客户端发送请求时会请求不同的数据，传入不同的请求地址，服务器根据不同的请求地址作出不同响应。

​	通过操作req.url判断返回对应值。

#### 	2.URL解析

比如`http://localhost:8000/login?name=why&password=123`的url就是`/login?name=why&password=123`;

如果请求地址携带参数，解析使用<font color=orange>**内置url模块**</font>:

```js
const http = require('http');
const url = require('url');
const qs = require('querystring');

// 创建一个web服务器
const server = http.createServer((req, res) => {
  // /login?username=why&password=123
  const { pathname, query } = url.parse(req.url);
  if (pathname === '/login') {
    const { username, password } = qs.parse(query);
    console.log(username, password);
    res.end("请求结果~");
  }
});

// 启动服务器,并且制定端口号和主机
server.listen(8888, '0.0.0.0', () => {
  console.log("服务器启动成功~");
});
```



### （2）method的处理

数据增删查改对应的请求方式：

- GET:查询数据;

- POST:新建数据;
- PATCH:更新数据;
- DELETE:删除数据;

比如创建一个请求方法实现：创建一个用户，用户接口为/user，请求方式采用post，携带数据username和password。

```js
//需要判断接口是/user且请求方式是POST
//判断后要提取携带的数据，监听req中的data
const server = http.createServer((req,res) = {
  const {pathname} = url.parse(req.url);
  if(pathname === '/user'){
  	if(req.method === 'POST'){
      req.setEncoding('utf-8');
      req.on('data',(data) => {
        const {username,password} = JSON.parse(data);
        console.log(username,password)
      })
      req.on('end',() => {
        console.log("传输结束")
      })
      res.end("create user success")
    }
	}
}

server.listen(8888, '0.0.0.0', ()=> {
  console.log("服务器启动成功~");
})
```



### （3）请求头headers处理

request对象的header中也包含很多客户端会默认传递过来的信息：

#### 	1.content-type：请求携带的数据的类型

```js
application/json	//表示是一个json类型;

text/plain	//表示是文本类型;

application/xml	//表示是xml类型;

multipart/form-data	//表示是上传文件;
```

#### 	2.content-length：文件的大小和长度

#### 	3.keep-alive

- http是基于TCP协议的，但是通常在进行一次请求和响应结束后会立刻中断；
- 在http1.0中，如果想要继续保持连接：
  - 浏览器需要在请求头中添加connection: keep-alive；
  - 服务器需要在响应头中添加 connection:keey-alive；
  - 当客户端再次请求时，就会使用同一个连接，直接一方中断连接；
- 在http1.1中，所有连接默认是 connection: keep-alive的：
  - 不同的Web服务器会有不同的保持 keep-alive的时间；
  - Node中默认是5s；

#### 	4.accept-encoding：客户端支持的文件压缩格式

#### 	5.accept：客户端可接受格式类型

#### 	6.user-agent：客户端相关信息



## 3、response对象

### （1）返回响应结果

给客户端返回响应的结果数据，可以通过两种方式：

- write方法：直接写出数据，但是并没有关闭流；
- end方法：写出最后数据，并且写出后会关闭流；

**如果没有调用end和close，客户端将会一直等待结果**，所以客户端发送网络请求时，都会设置超时事件。



### （2）返回状态码

Http状态码时用来表示Http响应状态的数字代码

| 状态码 | 状态描述              | 信息说明                                                     |
| ------ | --------------------- | ------------------------------------------------------------ |
| 200    | OK                    | 客户端请求成功                                               |
| 201    | Created               | 请求成功，通常是POST请求创建了一个新的资源                   |
| 301    | Moved Permanently     | 请求资源的URL已永久更改，响应中会给出了新的 URL              |
| 302    | Found                 | 请求资源的URI临时更改。客户机应该在将来的请求中继续使用相同的 URI |
| 304    | Not Modified          | 表示使用缓存，和上次响应比还没有被修改，可以继续使用相同的缓存版本的响应 |
| 400    | Bad Request           | 客户端有语法错误，服务器无法或者不进行处理                   |
| 401    | Unauthorized          | 未授权错误，客户端必须携带身份信息，提供身份验证才能获得请求的响应 |
| 403    | Forbidden             | 服务器知道客户端身份，但是客户端没有权限访问，拒绝提供服务   |
| 404    | Not Found             | 服务器找不到请求的资源，比如输入错误的URL                    |
| 500    | Internal Server Error | 服务器遇到了不可预期的情况，比如传错参数                     |
| 503    | Service Unavailable   | 服务器不可用，可能处理维护或者重载状态，暂时无法访问         |

**<font color=red>设置状态码两种方式：</font>**

```js
res.statusCode = 400;
res.writeHead(200);
```



### （3）响应头文件

返回头部信息的两种方式：

```js
//方式一：
res.setHeader	//一次写入一个头部信息;
res.setHeader(
	"Content-Type","application/json;charset=utf8"
)

//方式二：
res.writeHead	//同时写入header和status;
res.writeHead(200,{
  "Content-Type":"application/json;charset=utf8"
})
```



## 4、http请求

### （1）网络请求

axios使用的是封装xhr；

node中使用的是http内置模块；

```js
//发送get请求
http.get("http://localhost:8000", (res) => {
  res.on("data", data => {
    console.log(JSON.parse(data.toString())
  })
})

//发送post请求
const req = http.request({
  method: 'POST',
  hostname: 'localhost',
  port: 8888
}, (res) => {
  res.on('data', (data) => {
    console.log(data.toString());
  });

  res.on('end', () => {
    console.log("获取到了所有的结果");
  })
});
req.end();
```



### （2）文件上传（原生）

```js
const http = require("http");
const fs = require("fs");
const qs = require("querystring");

const server = http.createServer((req, res) => {
  if (req.url === "/upload") {
    if (req.method === "POST") {
      req.setEncoding("binary");

      let body = "";
      const totalBoundary = req.headers["content-type"].split(";")[1];
      const boundary = totalBoundary.split("=")[1];

      req.on("data", (data) => {
        body += data;
      });

      req.on("end", () => {
        console.log(body);
        // 处理body
        // 1.获取image/png的位置
        const payload = qs.parse(body, "\r\n", ": ");
        const type = payload["Content-Type"];

        // 2.开始在image/png的位置进行截取
        const typeIndex = body.indexOf(type);
        const typeLength = type.length;
        let imageData = body.substring(typeIndex + typeLength);

        // 3.将中间的两个空格去掉
        imageData = imageData.replace(/^\s\s*/, "");

        // 4.将最后的boundary去掉
        imageData = imageData.substring(
          0,
          imageData.indexOf(`--${boundary}--`)
        );

        fs.writeFile("./foo.png", imageData, "binary", (err) => {
          res.end("文件上传成功~");
        });
      });
    }
  }
});

server.listen(8000, () => {
  console.log("文件上传服务器开启成功~");
});
```



# 十、express框架

express出现早于koa，核心是中间件。

## 1、express安装使用

### （1）使用express脚手架

```js
//安装脚手架
npm install -g express-generator
//创建项目
express express-demo
//安装依赖
npm install
//启动项目
node bin/www
```



### （2）手动搭建

```js
npm init -y
npm install express
```



### （3）基本使用

```js
const express = require("express");
const app = express();//创建服务器
app.get("/home", (req,res,next) => {
  res.end("hello world")
})
app.listen(8000, () => {
  console.log("服务器启动成功")
})
```



## 2、中间件

​	**Express应用程序本质上是一系列中间件函数的调用，中间件就是一个个传递给express的回调函数**。

### （1）中间件作用

1. 执行任何代码;
2. 更改请求(request)和响应(response)对象;
3. 结束请求-响应周期(返回数据);
4. 调用栈中的下一个中间件;

​	如果当前中间件功能没有结束请求-响应周期，则必须调用next()将控制权传递给下一个中间件功能，否则请求将被挂起。

![image-20220411213150632](https://raw.githubusercontent.com/Rainchen0504/picture/master/202204112131669.png)



### （2）路径方法中间件

```js
app.use((req, res, next) => {
  console.log("common middleware01");
  next();
})
app.get('/home', (req, res, next) => {
  console.log("home path and method middleware01");
});
app.post('/login', (req, res, next) => {
  console.log("login path and method middleware01");
})
```



### （3）中间件数据解析

#### 	1.json解析

```js
//自己编写的json解析
app.use((req, res, next) => {
  if (req.headers["content-type"] === "application/json") {
    req.on("data", (data) => {
      const info = JSON.parse(data.toString());
      req.body = info;
    });
    req.on("end", () => {
      next();
    });
  } else {
    next();
  }
});

//使用express解析
app.use(express.json())
//可使用req.body获取参数
```

#### 	2.urlencoded解析

```js
app.use(express.urlencoded({ extended: true }));
// true: 那么对urlencoded进行解析时, 它使用的是第三方库: qs
// false: 那么对urlencoded进行解析时, 它使用的是Node内置模块: 
```

#### 	3.form-data解析

```js
//引入外部multer方法
const multer = require("multer");
const upload = multer();
app.use(upload.any());
app.post('/login', (req, res, next) => {
  console.log(req.body);
  res.end("用户登录成功~")
});
```

```js
//form-data上传文件
const path = require("path");

const express = require("express");
const multer = require("multer");

const app = express();

//设置文件后缀
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, "./uploads/");
  },
  filename: (req, file, cb) => {
    cb(null, Date.now() + path.extname(file.originalname));
  },
});

const upload = multer({
  // dest: './uploads/'
  storage,
});

app.post("/login", upload.any(), (req, res, next) => {
  console.log(req.body);
  res.end("用户登录成功~");
});
```

#### 	4.中间件保存日志

引入第三方库morgan

```js
const morgan = require('morgan');
const writerStream = fs.createWriteStream('./logs/access.log', {
  flags: "a+"
})
app.use(morgan("combined", {stream: writerStream}));
```

#### 	5.客户端发送请求的方式

- 方式一:通过get请求中的URL的params
- 方式二:通过get请求中的URL的query；
- 方式三:通过post请求中的body的json格式(中间件中已经使用过)；
- 方式四:通过post请求中的body的x-www-form-urlencoded格式(中间件使用过)；
- 方式五:通过post请求中的form-data格式(中间件中使用过)；



### （4）传递参数params和query

```js
app.use('/login/:id/:name', (req,res,next) => {
  	console.log(req.params);
  	res.json("请求成功")
})

app.use('/login', (req,res,next) => {
  	console.log(req.query);
  	res.json("请求成功")
})
```



### （5）响应数据

- end方法
  - 类似于http中的response.end方法，用法是一致的；
- json方法
  - json方法中可以传入很多的类型:object、array、string、boolean、number、null等，它们会被转换成json格式返回；
- status方法
  - 用于设置状态码；



## 3、express路由

可以使用 express.Router来创建一个路由处理程序：

- 一个Router实例拥有完整的中间件和路由系统；
- 因此，也被称为迷你应用程序(mini-app)；

```js
//主文件
const express = require("express");
const userRouter = require("./router/users");
const app = express();
app.use("/users",userRouter);
app.listen(8000, () => {
  console.log("路由服务启动成功")
})

//路由文件
//请求所有的用户信息: get /users
//请求所有的某个用户信息: get /users/:id
//请求所有的某个用户信息: post /users body {username: passwod:}
//请求所有的某个用户信息: delete /users/:id 

const express = require('express');
const router = express.Router();
router.get('/', (req, res, next) => {
  res.json(["why", "kobe", "lilei"]);
});
router.get('/:id', (req, res, next) => {
  res.json(`${req.params.id}用户的信息`);
});
module.exports = router;
```



## 4、静态资源服务器

**node可以作为静态资源服务器**，express提供了方便部署静态资源的方法：

```js
const express = require('express');
const app = express();
app.use(express.static("./build静态资源打包后文件目录"))
app.listen(8000, () => {
  console.log("路由服务器启动成功~");
});
```



## 5、错误处理

```js
//完整的处理方式，项目级
const express = require("express");
const app = express();

const USERNAME_DOES_NOT_EXISTS = "USERNAME_DOES_NOT_EXISTS";

app.post('/login', (req, res, next) => {
  // 加入在数据中查询用户名时, 发现不存在
  const isLogin = false;
  if (isLogin) {
    res.json("user login success~");
  } else {
    // res.type(400);
    // res.json("username does not exists~")
    next(new Error(USERNAME_DOES_NOT_EXISTS));
  }
})

app.use((err,req,res,next) => {
  let status = 404;
  let message = "";
  switch(err.message) {
    case USERNAME_DOES_NOT_EXISTS:
      message = "username does not exists~";
      break;
    default: 
      message = "NOT FOUND~"
  }
  
  res.status(status);
  res.json({
    errCode: status,
    errMessage: message
  })
})

app.listen(8000, () => {
  console.log("路由服务器启动成功~");
});
```



# 十一、Koa框架

node.js的下一代web框架，是express同一个团队开发的。旨在为Web应用程序和API提供更小、更丰富和更强大的能力。

## 1、koa安装使用

```js
//安装：npm install koa

//使用案例：
const Koa = require("koa");
const app = new Koa();
app.use((ctx,next) => {
  	ctx.response.body = "hello koa"
})
app.listen(8000, () => {
  console.log("服务器启动成功")
})
```

- ctx上下文对象：
  - koa并没有像express一样，将req和res分开，而是将它们作为ctx的属性；
  - ctx代表依次请求的上下文对象；
  - ctx.request:获取请求对象；
  - ctx.response:获取响应对象；
- next本质上是一个dispatch



## 2、koa中间件

​	koa通过创建的app对象，注册中间件，且**<font color=orange>只能通过use方法</font>**注册。koa不提供methods的方式注册和path中间件来匹配路径。

​	所以在正式开发中，会采用**request手动判断**或者是**第三方路由中间件**来分离路径和method。

```js
// 手动注册中间件分离路径和方法
app.use((ctx, next) => {
  if (ctx.request.url === '/login') {
    if (ctx.request.method === 'GET') {
      console.log("来到了这里~");
      ctx.response.body = "Login Success~";
    }
  } else {
    ctx.response.body = "other request~";
  }
});
```



## 3、koa路由

可以选择第三方库:koa-router

```js
npm install koa-router
```

- 使用时一般封装一个xx.router.js文件；
- 在Koa实例上注册为中间件；

```js
//rotuer文件
const Router = require("koa-router");
const userRouter = new Router({prefix:"/user"});
useRouter.get("/",(ctx, next) => {
  ctx.response.body = "展示结果"
})
//或者
useRouter.post("/",(ctx, next) => {
  ctx.response.body = "展示结果"
})
module.exports = userRouter;

//app上注册中间件
//启动路由，按照给定的中间件的顺序执行use，也就是注册全部路由
app.use(userRouter.routes());
//中间件响应允许方法的请求，当路由跳转失败时拦截抛出错误
app.use(userRouter.allowedMethods());
```

**注意⚠️：<font color=orange>allowedMethods用于判断某一个methods是否支持</font>：**

如果请求的是没有定义的方法，会自动报错：method not allowed，状态码405；

如果请求的是没有定义的接口，会自动报错：not implemented，状态码501；



## 4、参数解析

### （1）params-query

```js
//请求地址http://localhost:8000/user/123
const userRouter = new Router({prefix:"/users"})
userRouter.get("/:id",(ctx,next) => {
  //获取params
  console.log(ctx.params.id)
})

//请求地址http://localhost:8000/login?username=why&password=123
app.use((ctx,next) => {
  //获取query
  console.log(ctx.request.body)
})
```



### （2）json

**安装依赖：npm install koa-bodyparser；**

使用koa-bodyparser的中间件；

```js
app.use(bodyParser());
app.use((ctx,next) => {
  console.log(ctx.reqauest.body)
})
```



### （3）urlencoded

和json一致



### （4）form-data

**安装依赖：npm install koa-multer；**

使用multer中间件

```js
const upload = multer({});
app.use(upload.any());
app.use((ctx,next) => {
  console.log(ctx.req.body)
})
```

上传文件

```js
const Koa = require('koa');
const Router = require('koa-router');
const multer = require('koa-multer');

const app = new Koa();
const uploadRouter = new Router({prefix: '/upload'});

const upload = multer({
  dest: './uploads/'
});

uploadRouter.post('/avatar', upload.single('avatar'), (ctx, next) => {
  console.log(ctx.req.file);
  ctx.response.body = "上传头像成功~";
});

app.use(uploadRouter.routes());
```

- 其中multer(options)中的参数：
  - dest——文件上传保存位置，如果省略，文件将保存在内存中，不会写入磁盘；
  - limits——指定数据大小和类型
- **upload.single('file')**：单文件上传的中间件，参数file是前端上传的文件字段名。
- **upload.array('file',maxCount)**：多文件上传的中间件，参数一是前端上传的文件字段名，参数而是文件上传的最大数量。
- **upload.fields([{name: 'xxx', maxCount: Number},{},······])** ：多文件上传的中间件，参数是一个数组，里边可配置多个对象。





## 5、数据的响应

- 数据结果：body将响应主体设置为以下一种
  - string：字符串数据
  - Buffer：Buffer数据
  - Stream：流数据
  - Object||Array：对象或者数组
  - null：不输出任何内容
  - 如果response.status尚未设置，Koa将自动将状态设置为200或者204
- 请求状态
  - ctx.status = 201；
  - ctx.response.status = 204；



## 6、静态服务器

koa没有内置部署相关的功能，需要使用第三方库：<font color=orange>npm install koa-static</font>

部署过程类似于express

```js
const Koa = require("koa");
const staticAssets = require("koa-static");

const app = new Koa();

app.use(staticAssets("./build"));

app.listen(8000, () => {
  console.log("koa服务器启动成功~");
});
```



## 7、错误处理

```js
//直接上代码
const Koa = require('koa');

const app = new Koa();

app.use((ctx, next) => {
  ctx.app.emit('error', new Error("错误信息"), ctx);
});

app.on('error', (err, ctx) => {
  ctx.status = 400;
  ctx.response.body = err.message;
})

app.listen(8000, () => {
  console.log("koa服务器启动成功~");
});
```



## 8、koa和express框架对比

- express是完整强大的，其中内置了非常多的功能；

- koa是简洁自由的，只包含最核心的功能，并不会对使用其他中间件进行任何的限制
  - 没提供基本的get和post；
  - 需要手动或者路由来判断请求方式或其他功能；
- express和koa框架核心都是中间件
  - 中间件的执行机制是不同的，特别是针对某个中间件中包含异步操作时；



# 十二、MySQL

## 1、认识MySQL

### （1）常见数据库

- **关系型数据库**：MySQL、Oracle、DB2、SQL Server、Postgre SQL等
  - 数据表之间相互关联，形成一对一、一对多、多对多关系；
  - 可利用SQL语句在多张数据表中关联查询数据；
  - 支持事务，对数据的访问更加的安全；

- **非关系型数据库**：MongoDB、Redis、Memcached、HBse等
  - 基于key-value的对应关系，查询过程不需要SQL解析，性能高；
  - 通常不支持事物，需要在程序中保证原子性操作；



### （2）MySQL

- MySQL原本是一个开源的数据库，原开发者为瑞典的MySQL AB公司，在2009年被Oracle收购；

- MySQL是一个关系型数据库，其实本质上就是一款软件、一个程序：
  - 这个程序中管理着多个数据库；
  - 每个数据库中可以有多张表；
  - 每个表中可以有多条数据；



## 2、操作数据库方式

### （1）终端有两种连接数据库的方式：

**方式一：**

<font color=red>mysql -uroot -pCoderwhy888.</font> 

**方式二：**

<font color=red>mysql -uroot -p</font>

<font color=red>Enter password: your password</font>



### （2）终端显示数据库

- 指令：show databases;

- MySQL默认的数据库:
  - infomation_schema:信息数据库，其中包括MySQL在维护的其他数据库、表、列、访问权限等信息;
  - performance_schema:性能数据库，记录着MySQL Server数据库引擎在运行 过程中的一些资源消耗相关的信息;
  - mysql:用于存储数据库管理者的用户信息、权限信息以及一些日志信息等;
  - sys:相当于是一个简易版的performance_schema，将性能数据库中的数据汇 总成更容易理解的形式;

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202205252128363.png" alt="image-20220525212743174" style="zoom: 50%;" />



### （3）终端创建数据库&表

```mysql
#创建数据库
create database chengehub;

#使用数据库
use chengehub

#创建表
 create table user(
    name varchar(20),
    age int,
    height double
);
#在表中插入数据
insert into user (name,age,height) valuse ("zhang",18,1.88)
```



### （4）GUI工具

实际开发中可以使用GUI工具连接数据库，常见的MySQL的GUI工具有Navicat、SQLYog、TablePlus；



## 3、SQL语句

### 1、分类

- DDL数据定义语言：
  - 可以通过DDL语句对数据库或者表进行:创建、删除、修改等操作
- DML数据操作语言：
  - 可以通过DML语句对表进行:添加、删除、修改等操作
- DQL数据查询语言：
  - 可以通过DQL从数据库中查询记录
- DCL数据控制语言：
  -  对数据库、表格的权限进行相关访问控制操作



### 2、数据库操作

- #### 查看当前数据库

```mysql
#查看所有库
SHOW DATABASES;
#使用某个数据库
USE chengehub;
# 查看当前正在使用的数据库
SELECT DATABASE();
```

- #### 创建新的数据库

```mysql
#创建新的数据库
CREATE DATABASE IF NOT EXISTS chenge;
#可以设置数据库类型
CREATE DATABASE IF NOT EXISTS chenge DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;
```

- #### 删除数据库

```mysql
#删除数据库
DROP DATABASE users
DROP DATABASE IF EXISTS bilibili;
```

- #### 修改数据库

```mysql
#修改数据库的字符集和排序规则
ALTER DATABASE bilibili CHARACTER SET = utf8 COLLATE = utf8_unicode_ci;
```



### 3、数据表的操作

- #### 查看数据表

```mysql
#查看所有的数据表
SHOW TABLES;
#查看选中表结构
DESC user;
```

- #### 创建数据表

```mysql
CREATE TABLE IF NOT EXISTS `user` (
	id INT PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(20) NOT NULL,
	age INT DEFAULT 0,
	telPhone VARCHAR(20) DEFAULT '' UNIQUE NOT NULL
)
```

- #### 删除数据表

```mysql
#DELETE方法
#只删除数据不删除表的结构，会走事务，一行一行删除。
#只是给删除的数据打了个标记为已删除，磁盘上所占空间不会变小，存储空间不会被释放
DELETE FROM `tablename` WHERE xxx

#DROP方法
#立即释放磁盘空间，删除表约束、索引、触发器等
DROP TABLE `tablename`

#TURNCATE方法
#执行后立即生效，无法找回，立刻释放磁盘空间，删除表数据不删除表结构
TRUNCATE TABLE `moment`;
```


- #### 修改数据表
```mysql
#修改表名
ALTER TABLE `moments` RENAME TO `moment`;

#添加一列
ALTER TABLE `moment` ADD `publishTime` DATETIME;

#删除一列
ALTER TABLE `moment` DROP `updateTime`;

#修改列名称
ALTER TABLE `moment` CHANGE `publishTime` `publishDate` DATE;

#修改列数据类型
ALTER TABLE `moment` MODIFY `id` INT;

#修改表中所有数据
UPDATE `products` SET `title` = 'iPhone12';

#修改符合条件数据
UPDATE `products` SET `title` = 'iPhone12' WHERE `title` = 'iPhone';

#修改数据并记录更新时间
ALTER TABLE `products` ADD `updateTime` TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;
```



### 4、SQL数据类型

MySQL支持的数据类型有:数字类型，日期和时间类型，字符串(字符和字节)类型，空间类型和 JSON数据类型。

- 数字类型包括：
  - 整数类型：INT、INTEGER等；
  - 浮点数类型：FLOAT、DOUBLE(FLOAT是4个字节，DOUBLE是8个字节)；
  - 精确数字类型：DECIMAL，NUMERIC(DECIMAL是NUMERIC的实现形式)；
- 日期类型包括：
  - YEAR以YYYY格式显示值
  - DATE类型用于具有日期部分但没有时间部分的值（YYYY-MM-DD）
  -  DATETIME类型用于包含日期和时间部分的值（YYYY-MM-DD hh:mm:ss）
  - TIMESTAMP数据类型被用于同时包含日期和时间部分的值（范围是UTC的时间范围）
  - DATETIME或TIMESTAMP 值可以包括在高达微秒(6位)精度的后小数秒一部分
- 字符串类型包括：
	- CHAR类型创建表示为固定长度的字符串（0到255之间）
	- VARCHAR类型的值是可变长度的字符串，长度指定为（0到65536之间）
	- BINARY和VARBINARY类型用于存储二进制字符串，存储的是字节字符串
	- BLOB用于存储大的二进制类型
	- TEXT用于存储大的字符串类型



### 5、表约束

- #### 主键：PRIMARY KEY

  一张表中区分每条记录唯一性的字段，这个字段是不会重复的并且不会为空。
  - 主键是表中唯一的索引；
  - 必须是NOT NULL的，MySQL会隐式的设置为NOT NULL；
  - 主键可以是多列索引，PRIMARY KEY(key_part, ...)，称之为联合主键；
  - 开发中尽量不要使用业务字段来作为主键；

  

- #### 唯一：UNIQUE
	
	- 唯一的，不会重复的字段使用UNIQUE约束；
	- 使用UNIQUE约束的字段在表中必须是不同的；
	- UNIQUE 索引允许NULL包含的列具有多个值NULL；
	
	
	
- #### 不能为空：NOT NULL
	
	- 必须插入值，不可以为空，使用 NOT NULL 来约束；
	
	
	
- #### 默认值：DEFAULT
	
	- 在没有设置值时给予一个默认值；
	
	
	
- #### 自动递增：AUTO_INCREMENT
	
	- 不设置值时可以进行递增，比如用户的id，使用AUTO_INCREMENT来完成；



### 6、数据表查询语句
#### （1）基本查询语句
```mysql
#查询所有的数据并且显示所有的字段
SELECT * FROM `products`;

#查询字段并起别名
SELECT title as t, brand as b, price as p FROM `products`;

#where比较运算符
SELECT * FROM `products` WHERE price <= 1000;

#where逻辑运算符
SELECT * FROM `products` WHERE `brand` = '华为' and `price` < 2000;
SELECT * FROM `products` WHERE brand = '华为' or price < 1000;

#模糊查询（%表示匹配任意个的任意字符；_表示匹配一个的任意字符）
SELECT * FROM `products` WHERE title LIKE 'v%';#查询所有以v开头的title
SELECT * FROM `products` WHERE title LIKE '%M%';#查询带M的title
SELECT * FROM `products` WHERE title LIKE '__M%';查询带M的title必须是第三个字符

#查询结果排序（ASC升序；DESC降序）
SELECT * FROM `products` WHERE brand = '华为' or price < 1000 ORDER BY price ASC;

#分页查询
SELECT * FROM `products` LIMIT 30 OFFSET 0;
SELECT * FROM `products` LIMIT 30 OFFSET 30;
```



#### （2）聚合函数

```mysql
#常用的聚合函数举例
#平均值
SELECT AVG(price) FROM `products` WHERE brand = '华为';

#最高和最低值
SELECT MAX(score) FROM `products`;
SELECT MIN(score) FROM `products`;

#总数求和
SELECT SUM(voteCnt) FROM `products`;

#总条目数量计算
SELECT COUNT(*) FROM `products`;
SELECT COUNT(*) FROM `products` WHERE brand = '华为';
```

![image-20220602150952413](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021509889.png)



#### （3）Group By

通常和聚合函数一起使用，先对数据分组，再对每组数据进行计算。
```mysql
#应用举例
SELECT brand,
	COUNT(*) as count,
	ROUND(AVG(price),2) as avgPrice,
	MAX(price) as maxPrice,
	MIN(price) as minPrice,
	AVG(score) as avgScore
FROM `products` GROUP BY brand;

#如果要给Group By添加约束，使用HAVING
SELECT brand,
	COUNT(*) as count,
	ROUND(AVG(price),2) as avgPrice,
	MAX(price) as maxPrice,
	MIN(price) as minPrice,
	AVG(score) as avgScore
FROM `products` GROUP BY brand HAVING avgPrice < 4000 and avgScore > 7;
```



#### （4）多表查询

- ##### 5外键约束

  使用外键约束可以将两张表联系起来

```mysql
#把products中的brand_id和brand的id联系起来

#情景一：新建表添加外键约束
FOREIGN KEY (brand_id) REFERENCES brand(id);
#情景二：已经创建好的表添加外键约束
ALTER TABLE `products` ADD FOREIGN KEY (brand_id) REFERENCES brnad(id);
```

- ##### 更新外键约束

**更新或删除时添加语句**：

1. RESTRICT：更新或删除某个记录时，会检查是否有关联的外键约束，有则会报错并拒绝更新或删除；
2. NO ACTION：和RESTRICT一致，在SQL标准中定义的；
3. CASCADE：更新或删除某个有外键关联的记录时，更新会更新对应记录，删除会把关联记录一起删掉；
4. SET NULL：更新或删除某个有外键关联的记录时，将对应的值设置为NULL；

**执行操作**：

```mysql
#第一步：查看表结构
SHOW CREATE TABLE `products`;

#第二步：删除之前的外键
ALTER TABLE `products` DROP FOREIGN KEY products_ibfk_1;

#第三步：添加新外键
ALTER TABLE `products` ADD FOREIGN KEY (brand_id) REFERENCES brand(id) ON UPDATE CASCADE ON DELETE CASCADE;
```

- ##### 默认多表查询

```mysql
#笛卡尔乘积X*Y
SELECT * FROM `user`,`moment`
```



#### （5）多表连接

SQL JOIN操作：左连接、右连接、内连接、全连接。

![image-20220602161526688](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021615694.png)

##### 左连接：

![image-20220602161943158](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021619593.png)

```mysql
SELECT * FROM `Table_A` A LEFT JOIN `Table_B` B ON A.key = B.key;
```

![image-20220602162223951](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021622274.png)

```mysql
SELECT * FROM `Table_A` A  LEFT JOIN `Table_B` B ON A.key = B.key WHERE B.key IS NULL;
```



##### 右连接：

![image-20220602162435729](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021624065.png)

```mysql
SELECT * FROM `Table_A` A RIGHT JOIN `Table_B` B ON A.key = B.key;
```

![image-20220602162459383](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021625400.png)

```mysql
SELECT * FROM `Table_A` A RIGHT JOIN `Table_B` B ON A.key = B.key WHERE A.key IS NULL;
```



##### 内连接：

![image-20220602163036828](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021630105.png)

```mysql
SELECT * FROM Table_A A INNER JOIN Table_B B ON A.key = B.key;
```



##### 全连接：MySql中需要使用UNION实现

![image-20220602163109222](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021631494.png)

```mysql
(SELECT * FROM `Table_A` A LEFT JOIN `Table_B` B ON A.key = B.key) UNION
(SELECT * FROM `Table_A` A RIGHT JOIN `Table_B` B ON A.key = B.key);
```

![image-20220602163131848](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021631016.png)

```sql
(SELECT * FROM `Table_A` A LEFT JOIN `Table_B` B ON A.key = B.key WHERE B.id IS NULL)
UNION
(SELECT * FROM `Table_A` A LEFT JOIN `Table_B` B ON A.key = B.key WHERE A.id IS NULL);
```



#### （6）查询结果转对象(JSON_OBJECT)

```mysql
SELECT 
	products.id as id, 
	products.title as title,
  products.price as price, 
  products.score as score, 
	JSON_OBJECT('id', brand.id, 'name', brand.name, 'rank', brand.phoneRank, 'website', brand.website) 
as brand FROM products 
LEFT JOIN brand ON products.brand_id = brand.id;
```

![image-20220602165227823](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021652652.png)



#### （7）查询结果转数组(JSON_ARRAYAGG)

```mysql
SELECT 
	stu.id, 
	stu.name, 
	stu.age,
	JSON_ARRAYAGG(JSON_OBJECT('id', cs.id, 'name', cs.name)) 
as courses FROM students stu
LEFT JOIN students_select_courses ssc ON stu.id = ssc.student_id 
LEFT JOIN courses cs ON ssc.course_id = cs.id
GROUP BY stu.id;
```

![image-20220602165526386](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206021655455.png)



## 4、Node连接MySql

### （1）认识mysql2

在node中执行SQL语句，一般借用两个库：

- mysql：最早的Node连接MySQL的数据库驱动；
- mysql2：在mysql的基础之上，进行了很多的优化、改进；

mysql2兼容mysql的API；性能更快/好；提供预编译语句，防止SQL注入；支持Promise，可以使用async和await语法等。



### （2）使用mysql2

安装：`npm install mysql2`

创建连接：

```js
const mysql = require("mysql2");
const connection = mysql.createConncetion({
  host:'localhost',
  database:'数据库名字',
  user:'root',
  password:'密码'
})
```

执行SQL语句：

```js
connection.query(
	'SELECT * FROM table_name',
  (err,result,fields) => {
    console.log(err);
    console.log(result);
    //使用完关闭连接
    connection.destory()
  }
)
```



### （3）预编译语句

提高性能：将创建的语句模块发送给MySql后被编译并存储，使用时提供真实参数才执行，多次执行也只编译一次。

防止SQL注入：只对预留的模块进行编译



### （4）连接池

mysql2提供了连接池，可以在需要时自动创建连接，并且连接不会被销毁。limit属性可以限制最大连接创建个数。

```js
const connections = mysql.createPool({
  host: "localhost",
  port: "8000",
  database: "chengehub",
  user: "root",
  password: "zhangYU0504",
  connectionLimit: 5
});
```



### （5）ORM

- 对象关系映射：
  - 在可编程语言中，使用虚拟对象数据库的效果；
  - 在Java中使用的ORM包括：Hibernate，MyBatis；
- Node中ORM通常使用sequelize；
- Sequelize和MySQL一起使用，需要先安装：
  - mysql2：sequelize在操作mysql时使用的是mysql2；
  - sequelize:使用它来让对象映射到表中；
  - `npm install sequelize mysql2`



### （6）Sequelize使用

首先创建Sequelize对象，指定数据库、用户名、密码、数据库类型等，其次测试连接是否成功即可。

![image-20220607151701284](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206071517492.png)

#### 创建单表

![image-20220607151759457](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206071518494.png)

操作单表

![image-20220607151828514](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206071518669.png)

#### 创建多表

![image-20220607152606912](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206071526000.png)

操作多表

![image-20220607152632365](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206071526389.png)



# 十三、项目点

## 1、登录凭证

登录成功后以某个身份去访问其他资源和数据，需要有凭证证明已经登录过。

常用的有cookie、session、token

### （1）Cookie

#### 	1.1cookie基础

- 类型为“**小型文本文件**，某些网站为了辨别用户身份而存储 在用户本地终端(Client Side)上的数据。
- cookie保存在客户端中，按照存储的位置，Cookie分为内存和硬盘Cookie。
  - 内存Cookie由浏览器维护，**保存在内存中**，浏览器关闭时Cookie就会消失，其存在时间是短暂的；
  - 硬盘Cookie**保存在硬盘中**，有一个过期时间，用户手动清理或者过期时间到时，才会被清理；
  - 不设置过期时间默认是内存Cookie，关闭浏览器时会自动删除；
  - 设置过期时间并且不为0或者负数的是硬盘Cookie。



#### 	1.2cookie属性

- 生命周期
  - 可以设置expires或者max-age配置过期时间：
    - expires：设置的是Date.toUTCString()，设置格式是;expires=date-in-GMTString-format;
    - max-age:设置过期的秒钟，max-age=max-age-in-seconds (例如一年为60*60*24*365);
- 作用域
  - Domain指定接受cookie的主机，不指定默认是origin，不包括子域；指定则包含子域名；
  - Path指定主机下接受cookie的路径；



#### 	1.3客户端设置cookie

```js
//通过js直接设置和获取（会话关闭时被删除）
document.cookie
//设置cookie
document.cookie = "xx=xx;max-age=10"
```



#### 	1.4服务端设置cookie

koa默认支持直接操作cookie

```js
demoRouter.get("/",(ctx,next) => {
  ctx.cookie.set("name","xx",{maxAge:1000*10})
  ctx.body = "设置cookie成功"
})
//取的时候通过ctx.cookie.get("属性名")获取
```



### （2）Seesion

Session是基于cookie实现机制，在koa中使用需要安装第三方包koa-session。

```javascript
//引入和设置session属性
const KoaSeeion = require("koa-session");
const session = KoaSeeion({
  key:"session",//cookie的key
  maxAge:5*1000,//过期时间
  httpOnly:true,//不允许通过js获取cookie
  rolling:true,//每次响应时刷新session的有效期
  signed:true//是否使用signed签名认证，防止数据被篡改
})

//路由中携带session和获取
demoRouter.get("/",(ctx,next) => {
  ctx.session.xx = {id:"xx",name:"xx"}
  ctx.body = "设置session成功"
})
//使用ctx.session.xx获取携带的值
```



### （3）token

#### 	1.1cookie和session缺陷

- Cookie会被附加在每个HTTP请求中，所以无形中增加了流量(事实上某些请求是不需要的)；
- Cookie是明文传递的，所以存在安全性的问题；
- Cookie的大小限制是4KB，对于复杂的需求来说是不够的；
- 对于浏览器外的其他客户端(比如iOS、Android)，必须手动的设置cookie和session；
- 对于分布式系统和服务器集群中不确定可以保证其他系统也可以正确的解析session；

所以前后端分离项目中使用token较多。



#### 	1.2token使用步骤

生成token：登录时候颁发；

验证token：访问某些资源或接口时验证；



#### 	1.3对称加密JWT

​	**JWT实现token**

- JWT生成的token由三部分组成：
  - header
    - alg加密算法，默认是**对称加密算法HS256**，采用同一个密钥进行加密和解密；
    - typ固定值JWT；
    - 会通过base64Url算法进行编码；
  - payload
    - 携带的数据，默认会携带iat和令牌签发时间；
    - 可以设置过期时间exp；
    - 会通过base64Url算法进行编码；
  - signature
    - 设置一个secretKey，将前两个结果合并后进行HS256算法；
    - HMACSHA256(base64Url(header)+.+base64Url(payload), secretKey)；
    - 如果secretKey暴露是一件非常危险的事情，因为之后就可以模拟颁发token，也可以解密token

![image-20220616152521182](https://raw.githubusercontent.com/Rainchen0504/picture/master/202206161525406.png)

真实开发中使用jsonwebtoken这个库完成

```javascript
const jwt = require('jsonwebtoken');
const SERCET_KEY = "abccba123";
//颁发token
testRouter.post('/test', (ctx, next) => {
  const user = {id: 110, name: 'why'};
  const token = jwt.sign(user, SERCET_KEY, { expiresIn: 10 });
  ctx.body = "颁发成功";
});

//验证token
testRouter.get('/demo', (ctx, next) => {
  const authorization = ctx.headers.authorization;
  const token = authorization.replace("Bearer ", "");
  try {
    const result = jwt.verify(token, SERCET_KEY);
    ctx.body = result;
  } catch (error) {
    console.log(error.message);
    ctx.body = "token是无效的~";
  }
});
```



#### 	1.4非对称加密

- HS256加密算法单一密钥暴露是非常危险的，因此可以使用**非对称加密RS256**

私钥：用于发布令牌；

公钥：用于验证令牌；

- 使用openssl生成一对私钥和公钥

```shell
openssl
> genrsa -out private.key 1024
> rsa -in private.key -pubout -out public.key
```

使用公钥和私钥签发和验证签名

```javascript
const jwt = require("jsonwebtoken");
const fs = require("fs");
const PRIVATE_KEY = fs.readFileSync('./keys/private.key');
const PUBLIC_KEY = fs.readFileSync('./keys/public.key');
//颁发令牌
demoRouter.post("/",(ctx,next) => {
  const user = {id: 110, name: 'police'};
  const token = jwt.sign(user, PRIVATE_KEY, {
    expiresIn: 10,
    algorithm: "RS256"
  });
  ctx.body = token;
})

//验证令牌
demoRouter.get("/demo",(ctx,next) => {
  const authorization = ctx.headers.authorization;
  const token = authorization.replace("Bearer ", "");
  try {
    const result = jwt.verify(token, PUBLIC_KEY, {
      algorithms: ["RS256"]
    });
    ctx.body = result;
  } catch (error) {
    console.log(error.message);
    ctx.body = "token是无效的~";
  }
})
```

