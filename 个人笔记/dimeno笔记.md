## 8月20日

### 1、ES6中Class和Module

#### （1）Class

​	ES6引入了Class（类）这个概念，作为对象的模板，通过class关键字，可以定义类。

```javascript
//定义类
class Point(){
    constructor(x,y){
        this.x = x;
        this.y = y;
    }
    toString(){
        return '(' + this.x + ',' + this.y +')';
    }
}
let point = new Point(2,3);
point.toString(); //(2,3)
```

在上面的代码块里，先是定义一个point，里面有一个construction函数，这就是构造函数。而this关键字则代表实例对象。

#### （2）Module

##### 1、export和import

​	ES6实现了模块功能，试图解决JS代码的的依赖和部署上的问题，取代现有的commonJS和AMD规范，成为浏览器和服务器的通用的模块解决方案。

- export用于用于自定义模块，规定对外接口；
- import用于输出其他模块的功能，同时创建命名空间，防止函数名冲突。

ES6允许将独立的JS文件作为模块，也就是说，允许一个JavaScript脚本文件调用另一个脚本文件。最简单的模块就是一个JS文件，里面使用export关键字输出变量。

```javascript
//独立的js文件profile.js
export var firstName = "Zhang";
export var lastName = "YuChen";

//export还有下面这种写法，两者是等价的
var firstName = "Zhang";
var lastName = "YuChen";
export({firstName,lastName});
```

使用export定义模块后，其他JS文件就可以通过import关键字加载这个模块了。

```javascript
import {firstName,lastName} from './profile';
function setHeader(element){
    element.textContent = firstName + "" + lastName;
}
```

##### 2、模块的整体加载

​	export关键字除了整体输入变量，还可以输出方法或类（class）

```javascript
//circle.js
//方法1返回圆的面积
export function area(radius){
    return Math.PI * radius * radius;
}
// 方法2返回圆的周长
export function circumference(radius) {
 return 2 * Mathi.PI * radius;
}
```

定义一个main.js文件引用上面的模块。

```javascript
//main.js
import {area,circumference} from "circle";
console.log("圆面积： " + area(4));
console.log("圆周长： " + circumference(14));
```

上面的写法是逐一制定要导入的方法，但是还有另外一种写法，就是module关键字，整体导入。

```javascript
// main.js
module circle from 'circle.js';
console.log("圆面积： " + circle.area(4));
console.log("圆周长： " + circle.circumference(14));
```

module关键字后面跟着一个变量，表示导入的模块定义在该变量上。

##### 3、export default语句

​	如果不想为某个属性或方法制定输入的名称，可以使用export default语句。

```javascript
// export-default.js
export default function foo() { // foo()就是这个模块的默认方法
	console.log('foo');
}
```

当在其它模块中导入该模块时，import语句可以为默认方法指定任意名字。

```javascript
// import-default.js
import customName from './export-default';
customName(); //'foo'
```

显然，一个模块只能由一个默认方法。

### 2、splice直接修改数组，slice创建新数组	

```javascript
arr.slice(start,end)  //从哪一位开始截到哪一位
arr.splice(start,num,newobj)  //从哪一位开始，截/加几个元素，替换/新增元素是啥
```



## 8月23日

### 1、npm

​	npm全称Node package manage，时随NodeJS一起安装的包管理和分发工具，便于让JS开发者下载、安装、上传以及管理已经安装的包。

#### （1）`npm help`

​	查看某条命令的详细帮助；



#### （2）`npm init`

​	在项目中引导创建一个package.json文件；



#### （3）`npm init XXX`

​	想要通过npm init XXX调用包就必须提供一个create-XXX脚本；



#### （4）`npm config`

​	管理配置文件的，利用npm config list可以列出所有的配置项，利用get、set、delete可以执行、设置、删除配置项的操作；



#### （5）`npm install`安装模块

​	不指定包时会将package.json列出的依赖安装到node_modules中，如果指定包名，则安装指定的包。-g是全局安装。

- ​		安装时加上--save-dev等价于-D，安装的依赖包信息会保存到devDependencies中，这些依赖一般是开发环境的，比如eslint、webpack、babel等，这些依赖一般不会被打包工具处理到构建结果中。
- ​		安装时加上--save等价于-S，安装的依赖包保存到dependencies中，这些依赖一般是属于生产环境的，程序正常运行时需要加载的依赖，会被打包到项目的构建结果中。



#### （6）`npm uninstall`卸载模块。

- npm uninstall "依赖名称"：删除依赖，但不会删除package.json的配置。
- npm uninstall "依赖名称"  --save-dev：删除依赖，同时删除package.json中devdependencies 的配置。
- npm uninstall "依赖名称" --save：删除依赖，同时删除package.json中dependencies 的配置。



#### （7）`npm start`启动模块

​	是一个语义化的命令，通常会在scripts中自定义start脚本，如果没有自定义start脚本，npm start默认执行node serve.js。



#### （8）`npm run`

​	用来运行我们定义的`scripts`，命令后直接跟脚本名称就行。在`npm run`时，我们可以调用一些特殊路径下的可执行文件或脚本，这些路径包括环境变量PATH定义的路径，也包括当前项目`node_modules`中的`./bin`。



#### （9）`npm version`

​	从语义上看，`npm version`会修改`package.json`中的`version`字段，用来管理包的版本号。



#### （10）`npx`

##### 1、**使用npx可以在命令行直接执行本地已安装的依赖包命令，不用在scripts脚本写入命令**，也不用麻烦的去找本地脚本。

使用场景：

我们本地安装了一个依赖包

```javascript
npm i -D mocha
```

想要在本地（当前目录）执行它时，什么都不做时是不能运行这个命令的：

![img](https://upload-images.jianshu.io/upload_images/15009210-3b360d8c00b6d9db.png?imageMogr2/auto-orient/strip|imageView2/2/w/356/format/webp)

我们一般会使用几种方式来运行我们想要运行的命令：

###### 1、使用package.json的scripts脚本。

```javascript
//package.json
"scripts": {
  "findmocha": "mocha --version",
}
```

然后在命令行执行：

```javascript
npm run findmocha
```

![img](https://upload-images.jianshu.io/upload_images/15009210-352a9b70d2c491f7.png?imageMogr2/auto-orient/strip|imageView2/2/w/449/format/webp)

###### 2、在命令行中直接找到模块的二进制文件运行

###### 3、全局安装模块

使用npx可以直接在命令行执行我们要运行的命令：

```javascript
npm i -D mocha
npx mocha --version
```

<img src="https://upload-images.jianshu.io/upload_images/15009210-0c46415adf29b0d2.png?imageMogr2/auto-orient/strip|imageView2/2/w/520/format/webp" alt="img" style="zoom:110%;" />



##### 2、不用全局安装，直接在命令行执行一次性命令

​	有很多命令，我们只需要执行一次的，但是却要全局安装一次，实在不科学，使用npx，可以在不全局安装依赖包的情况下，运行命令，而且运行后不会污染全局环境。

比如：

```
npx create-react-app my-react-app
```

npx 将create-react-app下载到一个临时目录，使用以后再删除。每次运行这个命令，都会重新下载依赖包，运行后删除。



##### 3、npx原理

npx的原理就是在运行时执行下列流程：

- 去`node_modules/.bin`路径检查npx后的命令是否存在，找到之后执行；
- 找不到，就去环境变量`$PATH`里面，检查npx后的命令是否存在，找到之后执行；
- 还是找不到，自动下载一个临时的依赖包最新版本在一个临时目录，然后再运行命令，运行完后删除，不污染全局环境。



### 2、前端异常捕获

#### (1)现有的异常监控方案

- window.onerror全局异常捕获

  目前前端捕获页面异常的方式主要有两种：try...catch和window.onerror。

​	window.onerror的方法可以在任何执行上下文中执行，如果给window对象增加一个错误处理函数，便既能处理捕获错误又能保持代码的优雅性了。window.onerror一般用于捕捉脚本语法错误和运行时错误，可以获得出错的文件信息，如出错信息、出错文件、行号等，当前页面执行的所有JavaScript脚本出错都会被捕捉到。

```javascript
window.onerror = function (msg, url, line){
         // 可以捕获异步函数中的错误信息并进行处理，提示Script error.
    console.log(msg);   // 获取错误信息
    console.log(url);   // 获取出错的文件路径
    console.log(line);  // 获取错误出错的行数
};

setTimeout(function() {
    console.log(obj);   // 可以被捕获到，并在onerror中处理
}, 200);
```

​	然而，使用onerror要注意，在不同浏览器中实现函数处理返回的异常对象是不相同的，而且如果报错的JavaScript和HTML不在同一个域名下，错误时window.onerror中的errorMsg全部为script error而不是具体的错误描述信息，此时需要添加JavaScript脚本的跨域设置。

```javascript
<script src="//www.domain.com/main.js" crossorigin></script>
```

​	如果服务器因为一些原因不能设置跨域或设置起来比较麻烦，那就只能在每个引用的文件里添加try...catch进行处理。

- try-catch运行时解决方案

​	一般来说，使用try...catch可以捕捉前端JavaScript的运行时错误，同时拿到出错的信息，例如错误信息描述、堆栈、行号、列号、具体的出错文件信息等。我们也可以在这个阶段将用户浏览器信息等静态内容一起记录下来，快速地定位问题发生的原因。需要注意的是，try...catch无法捕捉到语法错误，只能在单一的作用域内有效捕获错误信息，如果是异步函数里面的内容，就需要把function函数块内容全部加入到try...catch中执行。

```javascript
try{
    // 单一作用域try...catch可以捕获错误信息并进行处理
    console.log(obj);
}catch(e){
    console.log(e); //处理异常，ReferenceError: obj is not defined
}

try{
    // 不同作用域不能捕获到错误信息
    setTimeout(function() {
        console.log(obj); // 直接报错，不经过catch处理
    }, 200);
}catch(e){
    console.log(e);
}

// 同一个作用域下能捕获到错误信息
setTimeout(function() {
    try{
        // 当前作用域try...catch可以捕获错误信息并进行处理
        console.log(obj); 
    }catch(e){
        console.log(e); //处理异常，ReferenceError: obj is not defined
    }
}, 200);
```

在上面的这个例子中，try...catch无法获取异步函数setTimeout或其他作用域中的错误信息，这样就只能在每个函数里面添加try...catch了。



#### (2)改进的一站式解决方案

- ES6 Class的异常捕获方案

- Promise内的错误捕获



### 3、JS执行机制

#### 1、关于javascript

​	javascript是一门单线程语言，一切js多线程的说法都是纸老虎，因为js要对浏览器DOM进行操作，比如某个指令是增加某个dom元素，另一条指令是删除该dom元素，多线程会出现这种矛盾冲突的情况，而单线程则避免了这种问题。



#### 2、javascript事件循环

单线程带来的问题就是如果一个任务耗时过长，后面的任务就得等着，容易出现等待时间较长的问题，因此将任务分成两类：

- **同步任务**
- **异步任务**

<img src="C:\Users\Administrator\Desktop\微信图片_20210823224024.jpg" style="zoom: 45%;" />

- 同步和异步任务分别进入不同的执行场所，同步的进入主线程，异步的进入Event Table并注册函数。
- 当指定的事情完成时，Event Table会将这个函数移入Event Queue。
- 主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
- 上述过程会不断重复，也就是常说的Event Loop(事件循环)。

<u>如何知道主线程为空？</u>

​	js引擎存在monitoring process进程，会持续不断的检查主线程执行栈是否为空，一旦为空，就会去Event Queue那里检查是否有等待被调用的函数。

```javascript
let data = [];
$.ajax({
    url:www.javascript.com,
    data:data,
    success:() => {
        console.log('发送成功!');
    }
})
console.log('代码执行结束');
```

上面是一段简易的`ajax`请求代码，执行流程如下：

1. ajax进入Event Table，注册回调函数`success`。
2. 执行`console.log('代码执行结束')`。
3. ajax事件完成，回调函数`success`进入Event Queue。
4. 主线程从Event Queue读取回调函数`success`并执行。



#### 3、setTimeout

​	有时候会存在定时器设置时间为3s，实际却5、6秒才执行函数，具体例子如下：

```javascript
setTimeout(() => {
    task();
},3000)
console.log('执行console');
```

​	上面代码的执行顺序是先执行console，后执行task函数。修改代码如下：

```javascript
setTimeout(() => {
    task()
},3000)

sleep(10000000)
```

​	会发现执行`task()`时需要的时间远超过3s。

分析下上面代码的执行顺序：

1. `task()`进入Event Table并注册,计时开始。
2. 执行`sleep`函数，很慢，非常慢，计时仍在继续。
3. 3秒到了，计时事件`timeout`完成，`task()`进入Event Queue，但是`sleep`也太慢了，还没执行完，只好等着。
4. `sleep`终于执行完了，`task()`终于从Event Queue进入了主线程执行。

​    上述的流程走完，我们知道`setTimeout`这个函数，是经过指定时间后，把要执行的任务(本例中为`task()`)加入到Event Queue中，又因为是单线程任务要一个一个执行，如果前面的任务需要的时间太久，那么只能等着，导致真正的延迟时间远远大于3秒。



##### 拓展setTimeout(fn,0)

​	这种写法并不代表立即执行。`setTimeout(fn,0)`的含义是，指定某个任务在主线程最早可得的空闲时间执行，意思就是不用再等多少秒了，只要主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。但是注意一点，**即便主线程为空，0毫秒实际上也是达不到的。根据HTML的标准，最低是4毫秒。**



#### 4、setInterval

对于执行顺序来说，`setInterval`会每隔指定的时间将注册的函数置入Event Queue，如果前面的任务耗时太久，那么同样需要等待。唯一需要注意的一点是，对于`setInterval(fn,ms)`来说，我们已经知道不是每过`ms`秒会执行一次`fn`，而是每过`ms`秒，会有`fn`进入Event Queue。一旦**`setInterval`的回调函数`fn`执行时间超过了延迟时间`ms`，那么就完全看不出来有时间间隔了**。



#### 5、Promise与process.nextTick(callback)

`Promise`参考ES6。

`process.nextTick(callback)`类似node.js版的"setTimeout"，在事件循环的下一次循环中调用 callback 回调函数。



#### 6、微任务与宏任务

##### （1）macro-task(宏任务)：**<u>包括整体代码script，setTimeout，setInterval</u>**

##### （2）micro-task(微任务)：**<u>Promise，process.nextTick</u>**

不同类型的任务会进入对应的Event Queue，比如`setTimeout`和`setInterval`会进入相同的Event Queue。

​	**<u>事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。</u>**

##### （3）事件循环、宏任务、微任务的关系如下图所示：

<img src="https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/11/21/15fdcea13361a1ec~tplv-t2oaga2asx-watermark.awebp" alt="img" style="zoom:50%;" />

用开头那段代码来说明：

```javascript
setTimeout(function() {
    console.log('setTimeout');
})

new Promise(function(resolve) {
    console.log('promise');
}).then(function() {
    console.log('then');
})

console.log('console');
```

1. 这段代码作为宏任务，进入主线程。
2. 先遇到`setTimeout`，那么将其回调函数注册后分发到宏任务Event Queue。
3. 接下来遇到了`Promise`，`new Promise`立即执行，`then`函数分发到微任务Event Queue。
4. 遇到`console.log()`，立即执行。
5. 整体代码script作为第一个宏任务执行结束，看看有哪些微任务？我们发现了`then`在微任务Event Queue里面，执行。
6. ok，第一轮事件循环结束了，我们开始第二轮循环，当然要从宏任务Event Queue开始。我们发现了宏任务Event Queue中`setTimeout`对应的回调函数，立即执行。
7. 结束。



## 8月25日

### 1、position：sticky粘性定位

​	元素根据正常文档流进行定位，然后相对最近的滚动祖先和最近块级祖先，基于top、right、left、bottom的值进行偏移，偏移值不会影响任何其他元素。

​	粘性定位元素依赖于用户的滚动，表现形式像在position：relative和position：fixed定位之间切换。即为在跨越特定阈值前为相对定位，之后为固定定位。这个阈值为left、right、top、bottom之一。

**注意**：IE浏览器、Edge15及更早IE版本不支持sticky定位，Safari需要使用-webkit-prefix。

```html
<style>
    div.sticky {
        position: -webkit-sticky; /* Safari */
        position: sticky;
        top: 0;
        background-color: green;
        border: 2px solid #4CAF50;
    }
</style>
<body>
    <p>尝试滚动页面。</p>
	<p>注意: IE/Edge 15 及更早 IE 版本不支持 sticky 属性。</p>
	<div class="sticky">我是粘性定位!</div>
	<div style="padding-bottom:2000px">
    	<p>滚动我</p>
      	<p>来回滚动我</p>
      	<p>滚动我</p>
      	<p>来回滚动我</p>
      	<p>滚动我</p>
      	<p>来回滚动我</p>
	</div>
</body>
```



## 8月26日

### 1、js合并两个对象

#### （1）$.extend()

```javascript
var obj1 = {'a':1};
var obj2 = {'b':2};
var c = $.extend(obj1,obj2);
console.log(obj1); //{a:1,b:1} obj1已被修改
```

或者

```javascript
var obj3 = $.extend({},obj1,obj2);
console.log(obj3); //{a:1,b:1} 不会改变obj1,obj2
```

其中，$.extend()方法**第一个参数设为true时表示深拷贝**。

#### （2）遍历赋值

```javascript
var obj1={'a':1};
var obj2={'b':2,'c':3};
for(var key in obj2){
　　if(obj2.hasOwnProperty(key)===true){
　　//此处hasOwnProperty是判断自有属性，使用 for in 循环遍历对象的属性时，原型链上的所有属性都将被访问会避免原型对象扩展带来的干扰
　　obj1[key]=obj2[key];
　　}
}
console.log(obj1);//{'a':1,'b':2,'c':3};
```

#### （3）Obj.assign()

```javascript
//可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。
Object.assign(target, ...sources)
//复制一个对象var obj = { a: 1 ,b:2};
var copyObj = Object.assign({}, obj);
console.log(copyObj); // { a: 1,b:2 }//合并多个对象
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };
var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1); // { a: 1, b: 2, c: 3 }, 且目标对象自身也会改变。
```



## 8月30日

### 1、select默认值

设置element-ui中el-select的默认值选择项的方法，就是把option中的value值绑定给select的v-model。

### 2、批量下载

谷歌浏览器不支持同时循环配批量下载。



## 8月31日

### 1、contents方法

​	contents() 方法获得匹配元素集合中每个元素的子节点，包括文本和注释节点。

​	如果给定表示 DOM 元素集合的 jQuery 对象，.contents() 方法允许我们检索 DOM 树中的这些元素的直接子节点，并用匹配元素构造新的 jQuery 对象。.contents() 和 .children()方法类似，不同的是前者在结果 jQuery 对象中包含了文本节点以及 HTML 元素。

.contents() 方法也可以用于获得 iframe 的内容文档，前提是该 iframe 与主页面在同一个域。

### 2、js对象

任何不是数值、字符串、布尔值、符号、null、undefined的值都是对象。



## 9月3日

### 1、css选择器符号

#### （1）空格——后代选择器

```css
div p {}
```

表示div元素里面所有的p元素；

#### （2）>子选择器

```css
div>p{}
```

表示div元素里的所有的子代（不包含孙代以后）p元素

#### （3）～兄弟元素选择器

```css
.cls~p{}
```

表示.cls元素往后同级的p元素



### 2、toFixed()方法

`toFixed(x)`使用定点表示法来格式化一个数值。

参数x必须，规定小数的位数，介于 0 到 20 （包括）之间，实现环境可能支持更大范围。如果忽略该参数，则默认为 0。

```javascript
var numObj = 12345.6789;
numObj.toFixed();	// 返回 "12346"：进行四舍六入五看情况，不包括小数部分
numObj.toFixed(1);	// 返回 "12345.7"：进行四舍六入五看情况
numObj.toFixed(6);	// 返回 "12345.678900"：用0填充
```

抛出异常：

`rangeError`:如果参数太小或太大；

`TypeError`:如果该方法用在一个非Number类型的对象上调用。



### 3、获取小数点后位数和值的方法

```javascript
let num = 123.2234546;	//从小数点位置把数字分成两部分
let str = num.toString().split('.'); //['123','2234546']
str[1] !== undefined ? str[1] : '';
str[0]	//整数部分
str[1]	//小数部分
```



### 4、VUE标签内写法规范

属性写在前面，方法写在后面



### 5、部分js事件

#### （1）`keydown`事件

当用户按下键盘的任意键时触发，而且如果按住不放，会重复触发此事件；

#### （2）`keypress`事件

当用户按下键盘的字符键时触发，而且如果按住不放的话，会重复触发此事件；

#### （3）`keyup`事件

当用户释放键盘上的键时触发；

#### （4）`oninput`事件

在<input>或<textarea>元素的值发生改变时触发；



## 9月6日

### 1、http和https

#### （1）HTTP

​	HTTP协议是**超文本传输协议**的缩写，它是从WEB服务器传输超文本标记语言(HTML)到本地浏览器的传送协议。

#### （2）HTTP特点

1. http协议支持客户端/服务端模式，也是一种请求/响应模式的协议。
2. 简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。
3. 灵活：HTTP允许传输任意类型的数据对象。传输的类型由Content-Type加以标记。
4. 无连接：限制每次连接只处理一个请求。服务器处理完请求，并收到客户的应答后，即断开连接，但是却不利于客户端与服务器保持会话连接，为了弥补这种不足，产生了两项记录http状态的技术，一个叫做Cookie,一个叫做Session。
5. 无状态：无状态是指协议对于事务处理没有记忆，后续处理需要前面的信息，则必须重传。

#### （3）URI和URL的区别

​	HTTP使用统一资源标识符（Uniform Resource Identifiers, URI）来传输数据和建立连接。

- URI：Uniform Resource Identifier 统一资源**标识**符

- URL：Uniform Resource Location 统一资源**定位**符

  URI 是用来标示 一个具体的资源的，我们可以通过 URI 知道一个资源是什么。

  URL 则是用来定位具体的资源的，标示了一个具体的资源位置。互联网上的每个文件都有一个唯一的URL。

#### （4）响应状态码

​	访问一个网页时，浏览器会向web服务器发出请求。此网页所在的服务器会返回一个包含HTTP状态码的信息头用以响应浏览器的请求。

**状态码分类**：

- 1XX- 信息型，服务器收到请求，需要请求者继续操作。
- 2XX- 成功型，请求成功收到，理解并处理。
- 3XX - 重定向，需要进一步的操作以完成请求。
- 4XX - 客户端错误，请求包含语法错误或无法完成请求。
- 5XX - 服务器错误，服务器在处理请求的过程中发生了错误。

**常见状态码**：

- 200 OK - 客户端请求成功
- 301 - 资源（网页等）被永久转移到其它URL
- 302 - 临时跳转
- 400 Bad Request - 客户端请求有语法错误，不能被服务器所理解
- 401 Unauthorized - 请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用
- 404 - 请求资源不存在，可能是输入了错误的URL
- 500 - 服务器内部发生了不可预期的错误
- 503 Server Unavailable - 服务器当前不能处理客户端的请求，一段时间后可能恢复正常。

#### （5）HTTPS

​	HTTPS适合传输一些敏感信息，绝大说的网站现在都采用的是https协议，这也是未来互联网发展的趋势。

​	HTTP+SSL/TLS，通过 SSL证书来验证服务器的身份，并为浏览器和服务器之间的通信进行加密。

#### （6）HTTPS的缺点

- HTTPS协议多次握手，导致页面的加载时间延长近50%；
- HTTPS连接缓存不如HTTP高效，会增加数据开销和功耗；
- 申请SSL证书需要钱，功能越强大的证书费用越高。
- SSL涉及到的安全算法会消耗 CPU 资源，对服务器资源消耗较大。

#### （7）总结HTTPS和HTTP的区别

​	https是http协议的安全版本，http协议的数据传输是明文的，是不安全的，https使用了SSL/TLS协议进行了加密处理。

​	http和https使用连接方式不同，默认端口也不一样，http是80，https是443。	



### 2、fetch请求

![img](https://img-blog.csdnimg.cn/20200203091744468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hiaWFvNjg=,size_16,color_FFFFFF,t_70)

```javascript
fetch('http://xxx/xxx.json？a=123').then(res => {
		return res.json();//这是一个promise
}).then(res => {
    console.log(res);//向后台请求的数据
})
```



### 3、css换行

​	`White-space、word-break、word-wrap(overflow-wrap)`

- white-space，**控制空白字符的显示**，同时还能控制是否自动换行。它有五个值：`normal | nowrap | pre | pre-wrap | pre-line`
- word-break，**控制单词如何被拆分换行**。它有三个值：`normal | break-all | keep-all`
- word-wrap（overflow-wrap）**控制长度超过一行的单词是否被拆分换行**，是`word-break`的补充，它有两个值：`normal | break-word`



### 4、js判断一个对象为空的方法

#### (1)将json对象转化为json字符串，再判断该字符串是否为"{}"。

```javascript
let data = {};
let b = (JSON.stringify(data) == "{}");
alert(b);  //true
```

#### (2)for in循环判断

```javascript
let obj = {};
let b = function() {
    for(let key in obj) {
        return false;
    }
    return true;
}
alert(b());  //true

```

#### (3)jq的isEmptyObject方法

此方法是jq将`for in`进行封装，使用时需要依赖jq

```javascript
let data = {};
let b = $.isEmptyObject(data);
alert(b);  //true
```

注意⚠️：第2和第3种方法只适用于对象字面量形式的对象。

#### (4)Object.getOwnPropertyNames()方法

​	此方法是使用Object对象的getOwnPropertyNames方法，获取到对象中的属性名，存到一个数组中，返回数组对象，我们可以通过判断数组的length来判断此对象是否为空。

```javascript
let data = {};
let arr = Object.getOwnPropertyNames(data);
alert(arr.length == 0);  //true
```

#### (5)使用ES6的Object.keys()方法

​	ES6的新方法，返回值也是对象中属性名组成的数组

```javascript
let data = {};
let arr = Object.keys(data);
alert(arr.length == 0);  //true
```



## 9月7日

### 1、中文正则规则

验证中文

```javascript
/^[\u4E00-\u9FA5]/g  
```

限制只能输入中文(非中文替换成空字符)

```javascript
value=value.replace(/[^\u4E00-\u9FA5]/g,'')
```

​	其中特殊字符 **^** 在方括号表达式外代表匹配输入字符的开始位置；在方括号表达式内使用代表非，即不接受该方括号表达式中的字符集合。要匹配 ^ 字符本身，请使用 \^。



### 2、js实现禁用浏览器后退

#### (1)会退后，产生一个前进事件

这种方式体验不好，用户能体验到界面的后退、前进。

```html
<script language="JavaScript"> 
		javascript:window.history.forward(1); 
</script> 
```

#### (2)禁用键盘的backspace键

这种做法，没有办法消除鼠标的误操作

```html
<script type="text/javascript">
    //处理键盘事件 禁止后退键（Backspace）密码或单行、多行文本框除外 
    function banBackSpace(e) {
        let ev = e || window.event;//获取event对象 
        let obj = ev.target || ev.srcElement;//获取事件源 
        let t = obj.type || obj.getAttribute('type');//获取事件源类型 

        //获取作为判断条件的事件类型 
        let vReadOnly = obj.getAttribute('readonly');
        let vEnabled = obj.getAttribute('enabled');
        //处理null值情况 
        vReadOnly = (vReadOnly == null) ? false : vReadOnly;
        vEnabled = (vEnabled == null) ? true : vEnabled;

        //当敲Backspace键时，事件源类型为密码或单行、多行文本的， 
        //并且readonly属性为true或enabled属性为false的，则退格键失效 
        let flag1 = (ev.keyCode == 8 && (t == "password" || t == "text" || t == "textarea")
            && (vReadOnly == true || vEnabled != true)) ? true : false;

        //当敲Backspace键时，事件源类型非密码或单行、多行文本的，则退格键失效 
        let flag2 = (ev.keyCode == 8 && t != "password" && t != "text" && t != "textarea")
            ? true : false;

        //判断 
        if (flag2) {
            return false;
        }
        if (flag1) {
            return false;
        }
    }
    //禁止后退键 作用于Firefox、Opera 
    document.onkeypress = banBackSpace;
    //禁止后退键 作用于IE、Chrome 
    document.onkeydown = banBackSpace;
</script>
```

#### (3)最优方案

消除后退的所有动作，包括键盘、鼠标手势等产生的后退动作。

```html
<script>
		//防止页面后退
  	history.pushState(null,null,document.URl)；
    window.addEventListener('popstate',function(){
      	history.pushState(null,null,document.URL);
    });
</script>
```



### 3、switch case

```javascript
switch(表达式){
  	case n1:
    		代码块
    		break;
  	case n2:
    		代码块
    		break;
  	default:
    		默认代码块;
}
```

​	计算一次 switch 表达式，把表达式的值与每个 case 的值进行对比，如果存在匹配，则执行关联代码。其中**default规定不存在case匹配时所运行的代码**。



## 9月8日

### 1、css中:root选择器

​	`:root`这个CSS伪类匹配文档树的根元素，对于HTML来说，`:root`表示<html>元素，除了优先级更高之外，与html选择器相同。

在声明全局CSS变量时，`:root`会很有用

```css
:root {
  --main-color : hotpink;
  --pane-padding : 5px 42px;
}
```



### 2、var()

​	**`var()`**函数可以代替元素中任何属性中的值的任何部分，**`var()`**函数不能作为属性名、选择器或者其他除了属性值之外的值。（这样做通常会产生无效的语法或者一个没有关联到变量的值。）

#### （1）语法

```css
var( <custom-property-name> , <declaration-value> )
```

​	该方法第一个参数是要替换的自定义属性名称。函数可选第二个参数作为回退值。如果第一个参数引用的自定义属性无效，则该函数将使用第二个值。

#### （2）值

**<custom-property-name> 自定义属性名**

​	在实际应用中它被定义为以两个破折号开始的任何有效标识符。 自定义属性仅供作者和用户使用; CSS 将永远不会给他们超出这里表达的意义。

**<declaration-value> 声明值（后备值）**

​	回退值被用来在自定义属性值无效的情况下保证函数有值。回退值可以包含任何字符，但是部分有特殊含义的字符除外，例如换行符、不匹配的右括号（如)、]或}）、感叹号以及顶层分号（不被任何非`var**()**`的括号包裹的分号，例如`var(--bg-color, --bs;color)`是不合法的，而`var(--bg-color, --value(bs;color))`是合法的）。

#### （3）实例

```css
:root {
  --main-bg-color: pink;
}
body {
  background-color: var(--main-bg-color);
}
```

当第一个值未定义时，回退值生效：

```css
/* 在父元素样式中定义一个值 */
.component {
  --text-color: #080; /* header-color 并没有被设定 */
}

/* 在 component 的样式中使用它： */
.component .text {
  color: var(--text-color, black); /* 此处 color 正常取值 --text-color */
}
.component .header {
  color: var(--header-color, blue); /* 此处 color 被回退到 blue */
}
```



### 3、js中url转义escape()、encodeURI()和decodeURI()

​	escape()除了 ASCII 字母、数字和特定的符号外，对传进来的字符串全部进行转义编码，因此如果想对URL编码，最好不要使用此方法。

​	encodeURI() 用于编码整个URI,因为URI中的合法字符都不会被编码转换。

​	encodeURIComponent方法在编码单个URIComponent（指请求参数）应当是最常用的，它可以将参数中的中文、特殊字符进行转义，而不会影响整个URL。

#### （1）encodeURI()	//转义一个URI中的字符

​	语法：encodeURI(uri)　　//这个在编码不同的AJAX请求时，解决中文乱码问题经常用到。

```javascript
let str1 = "你好javascript";
let str2 = encodeURI(str1);
document.write(str2);   //输出%E4%BD%A0%E5%A5%BDjavascript 
```

#### （2）decodeURI()	//解码一个URI中的字符

```javascript
let str1 = "你好javascript";
let str2 = encodeURI(str1);
document.write(str2);   //输出%E4%BD%A0%E5%A5%BDjavascript
let str3 = decodeURI(str2);
document.write("<br/>" + str3)  //输出    你好javascript
```



### 4、Array.isArray()

Array.isArray()用于确定传递的值是否是一个数组，`Array.isArray(obj)`，

如果对象是数组，返回true，否则返回false。



### 5、oncopy复制事件,onpaste粘贴事件。



### 6、linear-gradient线性渐变

从上到下，从绿色渐变到红色：

```css
linear-gradient(green,red)
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109081728738.png" alt="image-20210908172833325" style="zoom:15%;" />

渐变轴为45度，从绿色渐变到红色：

```css
linear-gradient(45deg,green,red)	
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109081731707.png" alt="image-20210908173054730" style="zoom: 15%;" />

从右下到左上，从绿色渐变到红色：

```css
linear-gradient(to left top, green, red)
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109081734290.png" alt="image-20210908173423079" style="zoom:15%;" />

从下到上，从蓝色开始渐变、到高度40%位置是绿色渐变开始、最后以红色结束

```css
linear-gradient(0deg, blue, green 40%, red)
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109081736830.png" alt="image-20210908173621582" style="zoom:15%;" />



## 9月10日

### 1、js操作类名

- **classList.add(newClassName)**

添加新的类名，如果已经存在，取消添加；

- **classList.contains(oldClassName)**

确定元素中是否包含指定的类名，返回值为true，flase；

- **classList.remove(oldClassName)**

移除已经存在的类名；

- **classList.toggle(className)**

如果classList中存在给定的值，删除该值，否则添加该值；

- **classList.replace(oldClassName,newClassName)**

类名替换



### 2、文件上传

```javascript
<input type="file" accept="application/pdf,image/gif,image/png" multiple="multiple">
```

其中，multiple属性可以设置多文件上传；accept属性设置上传文件格式，不同格式文件限制方法见链接https://www.jianshu.com/p/6f1ceb5bae7d。



## 9月13日

### 1、jQuery数据-data()方法

​	这是个底层方法，data()方法向被选元素附加数据，或者从被选元素获取数据。

#### （1）从元素返回数据

```javascript
$(selector).data(name)
```

参数name可选，规定要取回的数据的名称。如果没有规定名称，则该方法将以对象的形式从元素中返回所存储的数据。

#### （2）向元素附加数据

```javascript
$(selector).data(name,value)
```

参数name必须，规定要设置的数据的名称；参数value必须，规定要设置的数据的值。

#### （3）使用对象向元素附加数据

```javascript
$(selector).data(object)
```

参数object必须，规定包含名称/值对的对象。



### 2、call和apply方法

```javascript
var obj1 = {
  showName() {
    console.log(this.name)
  },
  name: 'obj1'
};
var obj2 = {
  name: 'obj2'
}
obj1.showName();		//obj1
obj1.showName.call(obj2);		//obj2
```

这样就可以让本来没有showName方法的obj2调用了showName方法。
即call和apply方法并不只是概念上的改变this指向，最重要的作用就是可以让一个元素调用另外一个元素的方法。



### 3、Element.getBoundingClientRect()

​	`Element.getBoundingClientRect()` 方法返回元素的大小及其相对于视口的位置。果是标准盒子模型，元素的尺寸等于`width/height` + `padding` + `border-width`的总和。如果`box-sizing: border-box`，元素的的尺寸等于 `width/height`。



### 4、函数防抖

```javascript
//事件触发间隔小于wait ms时，只执行后一次的事件fn
function debounce(fn,wait){
  let timer = null;
  return function(){
    if(timer !== null){
      clearTimeout(timer);
    }
    timer = setTimeout(fn,wait);
  }
}

function demo(){
  console.log(Math.random());
}
//绑定事件触发方式resize，改变窗口大小，观察浏览器控制台的变化，当触发事件resize的时间间隔小于1000ms时，只执行最后一次的demo方法。
window.addEventListnener("resize",debounce(demo,1000))
```



## 9月14日

### 1、user-select属性

​	CSS属性，控制用户能否选中文本，除了文本框内，对被载入为chrome的内容没有影响。

**语法**：none元素及其子元素的文本不可选；

​			auto默认，浏览器允许的情况下可以选择文本；

​			text用户可选择文本；

​			all单击选取文本；

​			contain允许在元素内选择，但是选区将被限制在该元素的边界条件内。



## 9月15日

### 1、nav标签

<nav>标签是HTML5中的新标签，定义导航链接的部分。



## 9月22日

### 1、touch-action

​	CSS属性`touch-action`用于设置**触摸屏**用户如何操纵元素的区域（例如，浏览器内置的缩放功能）。

touch-action属性的值：

- auto:当触控事件发生在元素上时，由浏览器来决定进行哪些操作，比如对viewpoint进行平滑、缩放等。
- none:当触控事件发生在元素上时，不进行任何操作。
- pan-x:启用单指水平平移手势。
- pan-y:启用单指垂直平移手势。
- manipulation:浏览器只允许进行滚动和持续缩放操作。任何其它被auto值支持的行为不被支持。启用平移和缩小缩放手势，但禁用其他非标准手势，例如双击以进行缩放。 禁用双击可缩放功能可减少浏览器在用户点击屏幕时延迟生成点击事件的需要。 这是“**pan-x pan-y pinch-zoom**”（为了兼容性本身仍然有效）的别名。
- pan-left, pan-right,pan-up,pan-down:启用以指定方向滚动开始的单指手势。 一旦滚动开始，方向可能仍然相反。 请注意，滚动“向上”（**pan-up**）意味着用户正在将其手指向下拖动到屏幕表面上，同样 **pan-left** 表示用户将其手指向右拖动。 多个方向可以组合，除非有更简单的表示（例如，“**pan-left pan-right**”无效，因为“**pan-x**”更简单，而“**pan-left pan-down**”有效）。
- Pinch-zoom:启用多手指平移和缩放页面。 这可以与任何平移值组合。



### 2、input标签上传同名文件二次无效问题

input通过onchange事件触发js，两次文件重复所以事件没有触发，因此需要在每次上传成功之后把input的值清空即可。



### 3、activated和deactivated

##### (1)activated

类型：function

详细：被keep-alive缓存的组件激活时调用。该钩子在服务器端渲染期间不被调用。

##### (2)deactivated

类型：function

详细：被keep-alive缓存的组件失活时调用。该钩子在服务器端渲染期间不被调用。

⚠️：当组件在<keep-alive>内被切换，activated和deactivated这两个生命周期钩子函数将会被对应执行。

**页面第一次进入，钩子的触发顺序created-> mounted-> activated，退出时触发deactivated。当再次进入（前进或者后退）时，只触发activated。**



### 4、函数表达式和函数声明

#### （1）语法

- 函数声明：在主代码流中声明为单独的语句的函数

```javascript
function sum (a,b){
	return a + b; 
}
```

- 函数表达式：在一个表达式中或另一个语法结构中创建的函数

```javascript
let sum = function (a,b){
	return a + b;
}
```

#### （2）调用

- 函数表达式是在代码执行到达时被创建，并且仅从那一刻起可用。一旦代码执行到赋值表达式 `let sum = function…` 的右侧，此时就会开始创建该函数，并且可以从现在开始使用（分配，调用等）。
- 在函数声明被定义之前，就可以被调用。当 JavaScript **准备** 运行脚本时，首先会在脚本中寻找全局函数声明，并创建这些函数。我们可以将其视为“初始化阶段”。在处理完所有函数声明后，代码才被执行。所以运行时能够使用这些函数。

#### （3）选用原则

​	当我们需要声明一个函数时，首先考虑函数声明语法。它能够为组织代码提供更多的灵活性。因为我们可以在声明这些函数之前调用这些函数。



## 9月27日

### 1、父子组件传值$emit

- 子组件

```vue
<template>
    <div class="app">
       <input @click="sendMsg">
    </div>
</template>
<script>
export default {
    data () {
        return {
            //将msg传递给父组件
            msg1: "我是子组件的msg1",
          	msg2: "我是子组件的msg2"
        }
    },
     methods:{
         sendMsg(){
             //send: 是父组件指定的传数据绑定的函数，this.msg:子组件给父组件传递的数据
             this.$emit('send',this.msg1,this.msg2)
         }
     }
}
</script>
```

- 父组件

```vue
<template>
    <div class="app">
        <child @send="getMsgFormSon"></child>
    </div>
</template>
<script>
import child from './child.vue'
export default {
    data () {
        return {
            msg1 : "",
          	msg2 : "",
        }
    },
    components:{
        child,
    },
    methods:{
      //接收子组件传递的值
      getMsgFormSon(data1,data2){
        	this.msg1 = data1;
        	this.msg2 = data2;
      }
   }
}
</script>
```



### 2、移动端vw布局

​	在你的CSS中，只要使用到了`viewport`的单位（`vw`、`vh`、`vmin`或`vmax` ）地方，需要在样式中添加`content`：

```css
.my-viewport-units-using-thingie {
    width: 50vmin;
    height: 50vmax;
    top: calc(50vh - 100px);
    left: calc(50vw - 100px);
 
    /* hack to engage viewport-units-buggyfill */
    content: 'viewport-units-buggyfill; width: 50vmin; height: 50vmax; top: calc(50vh - 100px); left: calc(50vw - 100px);';
}
```

​	这可能会令你感到恶心，而且我们不可能每次写`vw`都去人肉的计算。特别是在我们的这个场景中，咱们使用了postcss-px-to-viewport这个插件来转换`vw`，更无法让我们人肉的去添加`content`内容。

​	这个时候就需要前面提到的postcss-viewport-units插件。这个插件将让你无需关注`content`的内容，插件会自动帮你处理。比如插件处理后的代码：

![img](https://raw.githubusercontent.com/Rainchen0504/picture/master/202110110945258.png)

Viewport Units Buggyfill还提供了其他的功能。详细的这里不阐述了。但是`content`也会引起一定的副作用。比如`img`和伪元素`::before`(`:before`)或`::after`（`:after`）。在`img`中`content`会引起部分浏览器下，图片不会显示。这个时候需要全局添加：

```css
img {
    content: normal !important;
}
```

而对于`::after`之类的，就算是里面使用了`vw`单位，Viewport Units Buggyfill对其并不会起作用。比如：

```css
// 编译前
.after {
    content: 'after content';
    display: block;
    width: 100px;
    height: 20px;
    background: green;
}
 
// 编译后
.after[data-v-469af010] {
    content: "after content";
    display: block;
    width: 13.333vw;
    height: 2.667vw;
    background: green;
}
```

这个时候我们需要通过添加额外的标签来替代伪元素。



## 10月8日

### 1、h5防止页面回退的方法

```javascript
history.pushState(null,null,document.URL);
window.addEventListener("popstate",function(){
  history.pushState(null,null,document.URL)
})
```



### 2、历史记录前进与后退

#### (1)后退

`window.history.back()`--相当于用户在浏览器的工具栏上点击返回按钮；

#### (2)前进

`window.history.forward()`--相当于用户在浏览器的工具栏上点击前进按钮；

#### (3)移动到指定历史记录点

​	通过go()方法，指定一个相对于当前页面位置的数值，从当前会话的历史记录中加载页面（当前位置页面索引：0，上一页：-1，下一页：1）；

​	window.history.go(-2)--后退2页，相当于调用两次back();

​	window.history.go(1)--前进1页，相当于调用forward();

可以通过**window.history.length**，得到历史记录栈中一共有多少页。



### 3、添加/修改历史记录条目

​	**HTML5的新API扩展了window.history，使历史记录点更加开放了。可以<u>存储当前历史记录点</u>pushState、<u>替换当前历史记录点</u>replaceState、<u>监听历史记录点</u>popstate。**

#### (1)pushState(stateObject,titile,URL)

​	逐条添加历史记录条目：

​	a.状态对象（stateObject）--一个JavaScript对象，与用pushState()方法创建的新历史记录条目关联。无论何时用户导航到新创建的状态，popstate事件都会被触发，并且事件对象的state属性都包含历史记录条目的状态对象的拷贝；

​	b.标题（title）--传入一个简短的标题，标明将要进入的状态；（FireFox浏览器目前会忽略该参数，但是传一个空字符串会比较安全）

​	c.地址（URL）--新的历史记录条目的地址（可选，不指定的话则为文档当前URL）；浏览器不会在调用pushState()方法后加载该地址；传入的URL与当前URL应该是同源的，否则，pushState()会抛出异常。

​	**某种意义上，调用pushState()有点类似于设置window.location='#foo'，它们都会在当前文档内创建和激活新的历史记录条目。**



#### (2)replaceState()

​	history.replaceState()操作类似于history.pushState()，不同之处在于replaceState()方法会**修改**当前历史记录条目而并非创建新的条目。



#### (3)popstate()

​	简而言之就是HTML5新增的用来控制浏览器历史记录的api。

​	每当激活的历史记录发生变化时，都会触发popstate事件。如果被激活的历史记录条目是由pushState所创建，或是被replaceState方法影响到的，popstate事件的状态属性将包含历史记录的状态对象的一个拷贝。



## 10月9日

### 1、Array.from()方法

​	将一个类数组对象或者可遍历对象转换成一个真正的数组(浅拷贝)，其中类数组对象最基本的要求就是有length属性的对象。

#### （1）将字符串转换成数组

```javascript
let str = "hello";
console.log(Array.form(str));	//['h','e','l','l','o'];
```

#### （2）Array.from参数是一个真正的数组

​	返回一个一模一样的新数组

```javascript
console.log(Array.from([12,45,47,56]))	//[12,45,47,56]
```

#### （3）将类数组对象转成真正的数组

```javascript
let arrLike = {
  	0: 'tom', 
  	1: '65',
  	2: '男',
  	3: ['jane','john','Mary'],
  	'length':4
}
let arr = Array.form(arrLike);
console.log(arr);	//['tom','65','男',['jane','john','Mary']]
```

如果去掉上面的length属性，会变成一个长度为0的空数组。

```javascript
let arrayLike = {
    'name': 'tom', 
    'age': '65',
    'sex': '男',
    'friends': ['jane','john','Mary'],
    length: 4
}
let arr = Array.from(arrayLike)
console.log(arr)	//[undefined,undefined,undefined,undefined]
```

如果对象的属性名不再是数字类型的，而是字符串类型的，结果是长度为4元素均为undefined的数组。

​	因此，**<u>要将一个类数组对象转换为一个真正的数组，必须具备以下条件</u>：**

- 该类数组对象必须具有length属性，用于指定数组的长度。如果没有length属性，那么转换后的数组是一个空数组。
- 该类数组对象的属性名必须为数值型或字符串型的数字。



#### （4）将set结构数据转成真数组

```javascript
let arr = [12,45,45,97,79,97];
let set = new Set(arr);
console.log(Array.from(set))	//[12, 45, 97, 79]
```

Array.from还可以接受第二个参数，作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

```javascript
let arr = [12,45,45,97,79,97]
let set = new Set(arr)
console.log(Array.from(set, item => item + 1)) //[13,46,98,80]
```



## 10月11日

### 1、Math.abs()方法

`Math.abs()`函数返回指定数字“x”的绝对值。

语法：`Math.abs(x)`

```javascript
Math.abs('-1');     // 1
Math.abs(-2);       // 2
Math.abs(null);     // 0
Math.abs("string"); // NaN
Math.abs();         // NaN
```



### 2、Math.pow()方法

`Math.pow()`函数返回基数的指数次幂。

语法：`Math.pow(base, exponent) `

```javascript
console.log(Math.pow(7, 3));	//343
console.log(Math.pow(4, 0.5));	//2
console.log(Math.pow(-7, 0.5));	//NaN
```



## 10月13日

### 1、JS阻止冒泡和取消默认事件(默认行为)

​	js冒泡和捕获是事件的两种行为，使用event.stopPropagation()起到阻止捕获和冒泡阶段中当前事件的进一步传播；使用event.preventDefault()可以取消默认事件。

#### （1）阻止冒泡和捕获

​	**w3c**的方法是**e.stopPropagation()**，**IE**则是使用**e.cancelBubble=true**。

​	stopPropagation是事件对象的一个方法，作用是阻止目标元素的冒泡事件，但是会不阻止默认行为。即在一个按钮绑定一个“click”事件，那么这个点击事件回依次在它的父级元素中触发，stopPropagation就是阻止目标元素的事件冒泡到父级元素。

#### （2）取消默认事件

​	**w3c**的方法是**e.preventDefault()**，**IE**则是使用**e.returnValue=false**。

​	preventDefault是事件对象的一个方法，作用是取消一个目标元素的默认行为，当事件对象的cancelable为false时表示没有默认行为。目标元素必须有默认行为才能被取消，例如链接	`<a>`默认动作就是跳转到指定页面，默认事件能阻止跳转。

#### （3）总结

当需要**停止冒泡行为**时，可以使用

```javascript
function stopBubble(e) { 
//如果提供了事件对象，则这是一个非IE浏览器 
	if ( e && e.stopPropagation ) {
    //因此它支持W3C的stopPropagation()方法 
		e.stopPropagation(); 
  }else 
    //否则，我们需要使用IE的方式来取消事件冒泡 
    window.event.cancelBubble = true; 
}
```

当需要**阻止默认行为**时，可以使用

```javascript
//阻止浏览器的默认行为
function stopDefault(e) {
  //阻止默认浏览器动作(W3C)
  if( e && e.preventDefault ){
    e.preventDefault();
  //IE中阻止函数器默认动作的方式
  }else{
    window.event.returnValue = false; 
  }
  return false;
}
```

⚠️注意：

```javascript
//阻止input的默认事件，禁止输入，可以禁止英文输入但是无法禁止中文输入
	.keydown(function (e){
    e.preventDefault();
    e.returnValue = false;
  });
```



## 10月15日

### 1、vue中watch监听总结

​	watch:{}是一个对象，一定要当成对象来用，可监听数据，是vue中数据发生变化进行处理的函数。它有三个选项：

- handler：其值是一个回调函数。即监听到变化时应该执行的函数。
- deep：其值是true或false，确认是否深入监听。(一般监听时是不能监听到对象属性值的变化的)。
- immediate：其值是true或false，确认是否以当前的初始值执行handle的函数。

#### （1）普通监听

```vue
<div>
  <p>FullName: {{fullName}}</p>
	<p>FirstName: <input type="text" v-model="firstName"></p>
</div>
<script>
	new Vue({
  	el: '#root',
  	data: {
    	firstName: 'Dawei',
    	lastName: 'Lou',
    	fullName: ''
  	},
  	watch: {
　　 	//普通的watch监听
    	firstName(newName, oldName) {
      	this.fullName = newName + ' ' + this.lastName;
    	}
  	} 
	})
</script>
```

​	当我们输入firstName后，watch监听每次修改变化的新值，然后计算输出fullName。

#### （2）handler方法和immediate属性

​	watch 的一个特点是，最初绑定的时候是不会执行的，要等到 `firstName` 改变时才执行监听计算。那如果我们想要最初绑定的时候就执行，代码如下

```vue
<script>
	watch: {
  firstName: {
    handler(newName, oldName) {
      this.fullName = newName + ' ' + this.lastName;
    },
    // 代表在wacth里声明了firstName这个方法之后立即先去执行handler方法
    immediate: true
  }
}
</script>
```

​	给 firstName 绑定了一个`handler`方法，之前我们写的 watch 方法其实默认写的就是这个`handler`，Vue.js会去处理这个逻辑，最终编译出来其实就是这个`handler`。

​	而`immediate:true`代表如果在 wacth 里声明了 firstName 之后，就会立即先去执行里面的handler方法，如果为 `false`就跟我们以前的效果一样，不会在绑定的时候就执行。

#### （3）深度监听

```vue
<div>
  <p>obj.a: {{obj.a}}</p>
  <p>obj.a: <input type="text" v-model="obj.a"></p>
</div>
<script>
	new Vue({
  	el: '#root',
  	data: {
    	obj: {
      	a: 123
    	}
  	},
  	watch: {
    	obj: {
      	handler(newName, oldName) {
         	console.log('obj.a changed');
      	},
      	immediate: true
    	}
  	} 
	})
</script>
```

​	在输入框中输入数据视图改变obj.a的值时，发现时无效的。受现代 JavaScript 的限制 (以及废弃 `Object.observe`)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 `getter/setter` 转化过程，所以属性必须在 `data` 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

​	默认情况下 handler 只监听`obj`这个属性它的引用的变化，我们只有给`obj`赋值的时候它才会监听到，比如我们在 mounted事件钩子函数中对`obj`进行重新赋值：

```vue
<script>
	mounted: {
  	this.obj = {
    	a: '456'
  	}
	}
</script>
```

​	这样 handler 才会执行，打印`obj.a changed`。相反，如果我们需要监听`obj`里的属性`a`的值呢？这时候`deep`属性就派上用场了！

```vue
<script>
	watch: {
  obj: {
    handler(newName, oldName) {
      console.log('obj.a changed');
    },
    immediate: true,
    deep: true
  }
}
</script>
```

​	`deep`的意思就是深入观察，监听器会一层层的往下遍历，给对象的所有属性都加上这个监听器，但是这样性能开销就会非常大了，任何修改`obj`里面任何一个属性都会触发这个监听器里的 handler。



## 10月18日

### 1、V8执行过程总结

- 源代码经过Parser解析器，经过语法分析和语句分析生成AST；

- AST经过Ignition解析器生成字节码并执行；

- 在执行过程中，如果发现热点代码，将热点代码交给TurboFan编译器生成机器码并执行；

- 如果热点代码不再满足要求，进行去优化处理；

  ​	这种字节码与解释器和编译器结合的技术，就是我们通常所说的即时编译（JIT）。



## 10月21日

### 1、本地存储

​	localstorage是一种如果你不主动去清除，会一直将数据存储在客户端的储存方式，<font color=#ff0000>即使关闭了浏览器，下次打开的时候仍然可以看到之前存储的未主动清除的数据（即便是杀毒软件或者浏览器自带的清除功能，也不能将localStorage存储的数据清除掉）。</font>

​	sessionStorage是一种临时的会话存储，只要当前的会话窗口未关闭，存储的信息就不会丢失，<font color=#ff0000>即便刷新了页面或者在编辑器中更改了代码，存储的会话信息也不会丢失。</font>用法和localStorage一致。

#### （1）存储localStorage

```javascript
var arr = [1,2,3];
localStorage.setItem("sort",arr);
console.log(localStorage.getitem("sort"));
```

#### （2）清空localStorage

```javascript
localStorage.clear();
```

#### （3）删除键值对

```javascript
localStorage.removeItem("sort");
```

⚠️：**<u>storage存储的数据只能是字符串，其他类型的数据需做类型转换；storage直接属于顶层对象window。</u>**

#### （4）不同域名下设置相同的本地存储会不会冲突？

答案是不会，不同域名下访问的页面不同，所以不会受影响。

#### （5）同一个域名下有a和b两个页面存放的不同本地存储效果是怎样的？

当访问a的时候，本地存储是a页面赋的值，当访问b的时候本地存储就是b的值。



## 11月9日

### 1、JS判断设备类型和浏览器类型

<font color="red">判断的核心就是通过navigator.userAgent方法获取浏览器信息做判断，这段信息在每个请求的请求头都能看到。</font>

#### (1)判断设备类型

```javascript
browserDevice(){
    let sUserAgent = navigator.userAgent.toLowerCase();
    let bIsIpad = sUserAgent.match(/ipad/i) == "ipad";
    let bIsIphoneOs = sUserAgent.match(/iphone os/i) == "iphone os";
    let bIsMidp = sUserAgent.match(/midp/i) == "midp";
    let bIsUc7 = sUserAgent.match(/rv:1.2.3.4/i) == "rv:1.2.3.4";
    let bIsUc = sUserAgent.match(/ucweb/i) == "ucweb";
    let bIsAndroid = sUserAgent.match(/android/i) == "android";
    let bIsCE = sUserAgent.match(/windows ce/i) == "windows ce";
    let bIsWM = sUserAgent.match(/windows mobile/i) == "windows mobile";
    if (bIsIpad || bIsIphoneOs || bIsMidp || bIsUc7 || bIsUc || bIsAndroid || bIsCE || bIsWM) {
        //手机设备
        console.log("iPhone");
    } else {
        //pc设备
        console.log("PC");
    }
}
```

#### (2)判断浏览器类型

```javascript
browserType(){
    let userAgent = navigator.userAgent;
    //判断是否是Opera浏览器
    let isOpera = userAgent.indexOf("Opera") > 1;
    //判断是否是IE浏览器
    let isIE = userAgent.indexOf("compatible") > -1 && userAgent.indexOf("MSIE") > -1 && !isOpera;
    //判断是否是Edge浏览器
    let isEdge = userAgent.indexOf("Edge") > -1;
    //判断是否是Firefox浏览器
    let isFirefox = userAgent.indexOf("Firefox") > -1;
    //判断是否是Safari浏览器
    let isSafari = userAgent.indexOf("Safari") > -1 && userAgent.indexOf("Chrome") == -1;
    //判断是否是Chrome浏览器
    let isChrome = userAgent.indexOf("Chrome") > -1 && userAgent.indexOf("Safari") > -1;
    if (isIE) {
        console.log("当前是IE浏览器");
    } else if (isOpera) {
        console.log("当前是Opera浏览器");
    } else if (isEdge) {
        console.log("当前是Opera浏览器");
    } else if (isFirefox) {
        console.log("当前是火狐浏览器");
    } else if (isSafari) {
        console.log("当前是Safari浏览器");
    } else if (isChrome) {
        console.log("当前是谷歌浏览器");
    } else {
        console.log("建议使用谷歌或火狐浏览器打开");
    }
};
```



## 11月12日

### 1、js实现一个链表反转算法

```javascript
reverseList(head){
  //定义一个中间变量存储cur的前一项
  let curBefore = head;
  //当前项
  let cur = curBefore;
  //当前的前一项
  let pre = null;
  //先判空
  if (cur == null){return null}
  //定一个cur，遍历把cur的后一项提到最前面去，然后把链表链接起来
  while (cur.next !== null){
    pre = cur.next;
    cur.next = pre.next;
    pre.next = curBefore;
    curBefore = pre;
  }
  return curBefore
},
```



## 11月17日

### 1、对象的扩展运算符

​	对象中的扩展运算符(...)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中。

```javascript
let bar = {a:1,b:2};
let baz = {...bar} //{a:1,b:2}
//上面内容相当于
let bar = { a: 1, b: 2 };
let baz = Object.assign({}, bar); // { a: 1, b: 2 }
```

- 扩展运算符对对象实例的拷贝属于一种<font color=red>浅拷贝</font>



## 11月22日

### 1、css避坑

​	vue组件中的style标签写法规范：

```vue
<style lang="scss/less" scoped>
  /*rel属性可能会导致样式失效*/
</style>
```



### 2、Vue动态样式的多种方法

#### （1）三元运算符判断

```vue
<div style="{flexDirection:show ? 'row':'row-reverse'}"></div>
<script>
export default{
  data(){
    return{
      show:true
    }
  }
}
</script>
```

#### （2）动态设置class

- 常用于循环列表中点击时，相应元素高亮；

```vue
<template>
	<div class="wrapper" v-for="(item,index) in houseList" :key="item" @click="rightTap(index)">
		<div class="houseTitle" :class="{'active' : index === rightIndex}">
			{{item.name}}
		</div>
	</div>
</template>
<script>
export default {
	data() {
		return {
			rightIndex:0,
			houseList:[]
		};
	},
	methods:{
		rightTap(index){ 
			this.rightIndex = index
		}
	}
}
</script>
<style lang="scss" scoped>
.wrapper{
	width: 118px;
	height: 60px;
	margin: 6px auto 0 auto;
	.houseTitle{
		font-size: 22px;
		text-align: center;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
	.active{
		color:#2a7ffa;
		 background-color: pink;
	}
}
</style>
```

- 为特定数值设置相应的样式

```vue
  <div 
    :class="activeId === item.id ?'activeStyle':'machineBar'" v-for="(item,index) in List" :key="item" 					     @click="clickEvent">    
      <p>{{item.name}}</p>    
  </div>

```

#### （3）方法判断

- 用于为不同的数值设置相应的样式

```vue
<template>
  <div v-for="(item,index) in houseList" :key="index">             
     <div :style="getStyle(item.status)">{{item.name}}</div>    
  </div> 
</template>
<script>
export default { 
  data(){
    return{
	  houseList:[
        { 
          id:1,
          name:1,
          status:'a'
        },{
          id:2,
          name:2,
          status:'b'
        },{
          id:3,
          name:3,
          status:'c'
        }
      ]
    }
  },
  methods:{
    getStyle(e){        
      console.log('值',e)        
      if(e === 'a'){            
        return {                
          width:'60px',
          height:'60px',                
          background:'yellow',                 
          margin: '10px auto'             
        }        
      }else if(e === 'b'){            
        return {                
          width:'60px',
          height:'60px',                  
          background:'red',                 
          margin: '10px auto'            
        }        
      }else if(e === 'c'){            
        return {                
          width:'60px',
          height:'60px',                 
          background:'pink',                 
          margin: '10px auto'             
        }        
      }
    }
  }
}
</script>
```

- 在元素多重事件下，显示相应的样式

```vue
<template>
  <div 
      class="homeWrap" :class="{'select': selected === 1,'click': clicked === 1}" 
      @click="handleClick(1)" @mousedown="menuOnSelect(1)">
     主页
  </div>   
</template>
<script>
export default {
  return {
      selected: 0,
      clicked: 0
  }
  methods:{
    menuOnSelect(value){
      this.selected = value;
    },
    handleClick(value){
      this.selected = 0
      this.clicked = value
    }
  }
 }
</script>
<style lang="stylus" scoped>
  .homeWrap.select 
    background red
  .homeWrap.click 
    background yellow
</style>
```

#### （4）数组绑定

```vue
<div :class="[isActive,isSort]"></div>
// 数组与三元运算符结合判断选择需要的class
<div class="item" :class="[item.name? 'lg':'sm']"></div>
<div class="item" :class="[item.age<18? 'gray':'']"></div>
// 数组对象结合
<div :class="[{ active: isActive }, 'sort']"></div>
<script>
  data() {
    return{
      isActive:'active',
      isSort:'sort'
   }
  }
  // css
  .active{
      /*这里写需要设置的第一种样式*/
    } 
  .sort{
      /*这里写需要设置的第二种样式*/
    }
</script>
```

#### （5）computed结合es6对象的计算属性名方法

```vue
 <div :class="classObject"></div>
 <script>
  export default {
    data(){
      return{
        isActive:true
      }
    },
    computed:{
      classObject() {
        return{
          class_a:this.isActive,
          class_b:!this.isActive
        //  这里要结合自身项目情况修改填写
        }
      }
    }
  }
</script>
<style>
// css
.class_a{
    /*这里写需要设置的第一种样式*/
}
.class_b{
   /*这里写需要设置的第二种样式*/
 }
</style>
```



## 11月23日

### 1、new Number()

​	`new Number()`是一个内建的函数构造器。虽然看着像是一个number，单实际上并不是一个真实的number：它有一堆额外的功能并且是一个对象。



## 11月29日

### 1、替换node-sass为dart-sass

**cli4项目改用dart-sass**

#### （1）如果安装了node-sass首先需要卸载

```js
npm uninstall node-sass
```

#### （2）安装dart-sass

```js
npm install sass sass-loader -D
```

#### （3）如果项目之前<font color=red>用到/deep/需要替换成::v-deep，否则会报错</font>，全局搜索 /deep/ , 将项目里的 /deep/ 替换为 ::v-deep

#### （4）node-sass与dart-sass区别

 1. node-sass 是用 node(调用 cpp 编写的 libsass)来编译 sass；
 2. dart-sass 是用 drat VM 来编译 sass；
 3. node-sass是自动编译实时的，dart-sass需要保存后才会生效
 4. 推荐 dart-sass 性能更好（也是 sass 官方使用的），而且 node-sass 因为国情问题经常装不上

#### （5）不选择node-sass的理由

node-sass在npm安装的时候大概率的会安装出错，或下载时间过长，因此考虑用dart-sass来替换；node-sass已经停止更新



## 12月1日

### 1、正则

```js
/^-?\d+(\.\d+)?$/
```

满足整数、小数、负数、小数点1个、负号开头验证。



### 2、js原生提供两个Base64相关方法

#### （1）btoa()：字符串或二进制值转为Base64编码；

#### （2）atob()：Base64编码转为原来的编码；

封装函数如下：

```js
// 字符串转base64
function encode(str){
    // 对字符串进行编码
    var encode = encodeURI(str);
    // 对编码的字符串转化base64
    var base64 = btoa(encode);
    return base64;
}
// base64转字符串
function decode(base64){
    // 对base64转编码
    var decode = atob(base64);
    // 编码转字符串
    var str = decodeURI(decode);
    return str;
}
```



## 12月6日

### 1、jQuery.each()方法

用于遍历指定的对象和数组：

```js
$.each(object,function(index,element){})
```

object：遍历的对象；

index：选择器的index位置

element：当前的元素（也可使用 "this" 选择器）



### 2、修改title标签名

```js
$(document).attr("title","修改内容");//修改title值
```



## 12月23日

### 1、数结构扁平化

```javascript
 //定义一个空数组
let kongArr = [];
//定义扁平化函数,参数是数据源
const bFn = function (source){
  //遍历树结构数组中的每一项
  source.forEach((item) => {
    //首先把每一项填充到空数组中
    kongArr.push(item);
    //其次如果item有子节点且该节点不为空时,让子节点再次执行该函数进行解析
    item.children && item.children.length>0 ? bFn(item.children) : ""
  })
};
//执行函数，传入数据源
bFn("数据源")
```



## 12月24日

### 1、JS刷新页面的方法

```javascript
history.go(0);
location.reload();
location.assign(location);
document.execCommand('Refresh');
location.replace(location);
document.URL=location.href;
```

#### （1）reload方法

该方法强迫浏览器刷新当前页面。

```javascript
//语法：
lcoation.reload((bForceGet))
```

参数：bForceGet，可选参数，默认为false，从客户端缓存里取当前页面。true则以GET方法，从服务端获取最新的页面，相当于客户端点击F5（“刷新”）。



#### （2）replace方法

方法通过指定URL替换当前缓存在历史里（客户端）的项目，因此当使用replace方法之后，你不能通过“前进”和“后退”来访问已经被替换的URL。

```javascript
//语法
location.replace(URL)
```



### 2、自动刷新的方法

#### （1）页面自动刷新

把下面代码加入<head>区域中

```html
<meta http-equiv="refresh" content="20;url=http://www.baidu.com"> 
<!--其中20指隔20秒后跳转到http://www.baidu.com页面-->
```



#### （2）页面自动跳转

```html
<meta http-equiv="refresh" content="20;url=http://www.baidu.com"> 
（其中20指隔20秒后跳转到http://www.baidu.comt页面）
```



#### （3）页面自动刷新js版

```html
<script type="text/javascript">
  function myrefresh()
  {
     window.location.reload();
  }
	setTimeout('myrefresh()',1000); //指定1秒刷新一次
</script>
```



#### （4）JS刷新框架的脚本语句

```html
//刷新包含该框架的页面用 
<script type="text/javascript">
 	parent.location.reload();
</script>

//子窗口刷新父窗口
<script type="text/javascript">
 	self.opener.location.reload();
</script>
(　或　<a href="javascript:opener.location.reload()" rel="external nofollow" >刷新</a> )

//刷新另一个框架的页面用 
<script type="text/javascript">
 	parent.另一FrameID.location.reload();
</script>
```



# 2022年1月5日

## 1、ajax异步请求中途取消

### （1）应用场景

​	当前端需要即时搜索时，会不断的向后端请求ajax，但是前端仅仅需要最后一次的搜索结果，之前的请求全部丢弃。



### （2）原生ajax

对于原生来说，取消ajax的关键是调用xmlhttp对象的`.abort()方法`

```js
let xmlhttp = new XMLHttpRequest();
xmlhttp.open(method,url,true);
xmlhttp.onreadystatechange = ()=>{} // 回调函数
xmlhttp.send(); // 发送请求
xmlhttp.abort(); // 请求终端
```



### （3）axios

使用Vue框架的话，就会使用axios发送ajax请求。在axios中是通过`axios.CancelToken.source()`方法取消请求。

```js
let CancelToken = axios.CancelToken;
let source = CancelToken.source();

axios({
    method:"GET",
    url:"https://api.github.com/",
    cancelToken:source.token
    //cancelToken的值起标识作用，标识由source控制的、将要被取消的ajax操作
}).then((res) => {
  //逻辑部分
    console.log(res.data);
}).catch((err) => {
  //如果调用了cancel方法，这里的res就是cancel传入的信息
  //编写逻辑
    console.log(err);
});

source.cancel('可以设置输出的信息，在catch中拿到');
```

如果有多个axios发送的请求，则需要定义多个cancelToken

```js
var CancelToken = axios.CancelToken;

var source = CancelToken.source();
axios({
    method:"GET",
    url:"https://api.github.com/",
    cancelToken:source.token
}).then((res) => {
    console.log(res.data);
}).catch((err) => {
    console.log(err);
});

var custom = CancelToken.source();
axios({
    method:"GET",
    url:"https://api.github.com/",
    cancelToken:custom.token
}).then((res)=>{
    console.log(res.data);
}).catch((err)=>{
    console.log(err);
});

source.cancel('Operation canceled by the user.');
custom.cancel('精确取消');
```



## 2、NProgress

vue-spa中使用NProgress来显示在页面顶部的进度条

### （1）**安装+基本使用**

```js
//基本安装
npm i nprogress -S
//基本用法
NProgress.start();//开启进度条
NProgress.done();//关闭进度条
```

### （2）**在页面切换中使用（路由配置）**

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
//引入nprogress 进度条插件
import NProgress from 'nprogress'
​
Vue.use(VueRouter);
​
// 简单配置  进度条,可以不配置：在axios中我们不再做配置，以用来区分。
NProgress.inc(0.2)//这将获取当前状态值并添加0.2直到状态为0.994
NProgress.configure({ easing: 'ease', speed: 500, showSpinner: false })//使用缓动（CSS缓动字符串）和速度（以毫秒为单位）调整动画设置,通过将加载微调器设置为false来关闭它。
​
​
export const constRouter = [
    {
        path: '/login',
        component: () => import('@/views/login/Login'),
    },
    ...
]
​
const router = new VueRouter({
    mode: 'history',
    base: process.env.BASE_URL,
    routes: constRouter
})
​
// 页面路由刚开始切换的时候
router.beforeEach(async (to,from,next) => {
    // 开启进度条
    NProgress.start();
})
​
// 页面路由切换完毕的时候
router.afterEach(() => {
    // 关闭进度条
    NProgress.done()
})
​
​
export default router
```



### （3）**在接口请求中使用（axios配置）**

```js
import axios from 'axios'
//引入nprogress 进度条插件
import NProgress from 'nprogress'

// 创建axios实例
const service = axios.create({
    baseURL: process.env.VUE_APP_BASE_API, //URL地址   环境变量文件
    timeout: 5000 ,//超时
})

// 请求拦截器
service.interceptors.request.use(
    config => {
    	// 开启进度条
		NProgress.start();
        return config
    },
    error => {
        return Promise.reject(error)
    }
)

// 响应拦截器
service.interceptors.response.use(
    response =>{
        // 关闭进度条
        NProgress.done()
    	return Promise.reject(response)
    },
    error => {
        // 关闭进度条
        NProgress.done()
        return Promise.reject(error)
    }
)

export default service;
```



# 2022年1月21日

## 1、window.requestAnimationFrame

**`	window.requestAnimationFrame()`** 告诉浏览器——希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

**语法**

```js
window.requestAnimationFrame(callback);
```

- 参数：callback

下一次重绘之前更新动画帧所调用的函数（也就是回调函数）。该回调函数会被传入`DOMHighResTimeStamp`参数，该参数与`performance.now()`的返回值相同，它表示`requestAnimationFrame()` 开始去执行回调函数的时刻。

- 返回值

一个 `long` 整数，请求 ID ，是回调列表中唯一的标识。是个非零值，没别的意义。你可以传这个值给 `window.cancelAnimationFrame()`以取消回调函数。





# 2022年2月15日

## 1、this.$set

有些时候在vue中为data中的某个对象添加一个属性，打印发现数据已经改变了，但是视图没有变化。

由于受JavaScript的限制，vue.js不能监听对象属性的添加和删除，因为在vue组件初始化的过程中，会调用getter和setter方法，所以该属性必须是存在在data中，视图层才会响应该数据的变化。

为了解决这种问题，可以使用this.$set()方法。

```js
//该方法有三个参数
this.$set( target, key, value )
//target：要更改的数据源(可以是对象或者数组)
//key：要更改的具体数据的索引
//value ：重新赋的值
```



# 2022年4月19日

## 1、el-table的selection列设置单选

```vue
<template>
  <el-table :data=listData ref="multipleTable"
            @select="selectRow" 
            @row-click="rowClick"
            @selection-change="handleSelectionChange"
            >
    <el-table-column type="selection"></el-table-column>
    <template v-for="(item,index) in listSettingData">
      <el-table-column v-if="item.show" :width="item.width"
                       :key="index" :label="item.label" :prop="item.prop"
                       :sortable="item.sortable"
                       >
      </el-table-column>
    </template>
  </el-table>
</template>
<script>
export default{
  data(){
    return{
      checkItem: [],
    }
  },
  methods:{
    //手动勾选时
    async selectRow(selection,row){
      await this.$refs.multipleTable.clearSelection();
      if (selection.length === 0){
        return
      }
      this.$refs.multipleTable.toggleRowSelection(row,true)
    },
    //选项发生变化时
    handleSelectionChange(val) {
      this.checkItem = val; //表格选中数据接收
    },
    //某一行被点击时
    rowClick(row, column) {
      const selectData = this.checkItem;
      this.$refs.multipleTable.clearSelection();
      if (selectData.length == 1) {
        selectData.forEach((item) => {
          // 判断 如果当前的一行被勾选, 再次点击的时候就会取消选中
          if (item == row) {
            this.$refs.multipleTable.toggleRowSelection(row, false);
          }
          // 不然就让当前的一行勾选
          else {
            this.$refs.multipleTable.toggleRowSelection(row, true);
          }
        });
      } else {
        this.$refs.multipleTable.toggleRowSelection(row, true);
      }
    },
  }
}
</script>
```





# 2022年8月26日

vue暴露的静态资源要放到public文件中才能通过axios请求获得，请求时写法：

```javascript
axios({
  method: "get",
  //这里/表示进入public文件中
  url: "/assets/..."
}).then((res) => {
  //成功的回调
})
```



# 2022年10月8日

## 1、移动端、H5开发手机网页调试工具

```http
https://github.com/liriliri/eruda
```

​	只有当 url 上带有 eruda=true 或本地存储 active-eruda 为 true 的时候，工具才会被加载并执行。其中设置面板有选项可以设置 active-eruda 使工具常驻，不用每次都在 url 上添加 eruda=true。



### 2、浏览器定位方法getCurrentPosition

`navigator.geolocation.getCurrentPosition`

```js
navigator.geolocation.getCurrentPosition((position) => {
  let gpsPoints = [
    {
      lng: position.coords.longitude,
      lat: position.coords.latitude,
    },
  ];
  //gpsPoints获取到的就是经纬度
});
```

该方法需要开启定位权限，因为需要获取用户隐私。

在项目中需要在https域名下才能获取用户定位信息。



# 2022年10月9日

## 1、Web Animations API

​	**<font color=deepred>Web Animations API</font>** 允许同步和定时更改网页的呈现，将`CSS3`实现的动画由`JS`代码实现，即 DOM 元素的动画。它通过组合两个模型来实现：时序模型和动画模型。

```vue
<div class="trangle"></div>

<script>
  let trangle = document.querySelector(".trangle");
  trangle.animate(
    [
      {
        transform: "rotate(0) translate3D(-10%, -10%, 0)",
        color: "#000",
      },
      { color: "#431236", offset: 0.1 },
      {
        transform: "rotate(360deg) translate3D(-10%, -10%, 0)",
        color: "#000",
      },
    ],
    {
      duration: 3000,
      iterations: Infinity,
    }
  );
</script>

<style>
  .trangle {
    width: 200px;
    height: 200px;
    background-color: pink;
  }
</style>
```





数组对象去重：

```javascript
let map = new Map()
for(let item of list){
  map.set(el.text,el)
}
return [...map.values()]
```



# 项目当前问题：

项目发布时需要把对应项目的数据导出出来，但是由于项目体积较大（组件多，一个项目600+组件，包含地图资源、组件信息、编组信息、项目信息等内容），面临的问题是从服务器导出时将项目资源打包成一个压缩文件，但是打包前要在缓存中定义一个变量存储数据，数据量超过了缓存大小，导致无法导出崩溃。

临时解决方案是调整服务器缓存大小，从256mb调整能容纳下700mb，下一步解决方法是采取读取一条写入一条的方法解决内存过大问题，但是存在以下两点问题：第一是这种方法无法知道整体资源的大小导致无法计算当前导出进度，从而无法显示进度交互。第二是这种方式需要对庞大的组件、编组、事件信息分解确定写入和读取的顺序和关系。





# 可视化项目组件状态更新机制

1、组件url变更-->请求数据-->刷新数据源-->检查过滤器-->检查数据映射-->自定义参数变更

2、过滤器状态变更-->检查过滤器-->检查数据映射-->输出结果

3、自动刷新重复操作1



# 11月18日

## 1、Function和eval函数

Function函数和eval函数,可以将一段代码转成对象或执行。

不同点是：
1、Function()返回的是为表达式创建的匿名函数，不会直接调用，只有当手动去调用创建出来的函数的时候才能调用；eval()返回的是表达式的结果，把字符串转换为代码后就直接执行。

2、eval()可以干扰作用域链，不管你在哪里执行 Function()，它只看到全局作用域。

具体分析

1、eval是一个特殊的函数，它可以将传入的字符串当做JavaScript代码来运行，并会将最后一句执行语句的结果，作为返回值。

语法：`eval(string)`

string参数必须，要计算的字符串，其中含有要计算的javascript表达式或要执行的语句。

```js
eval("x=10;y=20;document.write(x*y)")
document.write(eval("2+2"))
var x=10
document.write(eval(x+17));
//输出：200,4,27
```

2、Function对象（类）
语法：`let functionName = new Function(arg1,arg2,arg3...,function_body)`

每个arg都是一个函数，最后一个参数是参数主体(要执行的代码),这些参数都必须是字符串。

```js
function callAnotherFunc(fnFunction, vArgument) {
  fnFunction(vArgument);
}
var doAdd = new Function("iNum", "alert(iNum + 10)");
callAnotherFunc(doAdd, 10);  
//输出 "20"
```

可视化中的案例：

```js
const el =
      "/*\ntitle: Basic Line Chart\ncategory: line\ntitleCN: 基础折线图\ndifficulty: 0\n*/\noption = {\n  xAxis: {\n    type: 'category',\n    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']\n  },\n  yAxis: {\n    type: 'value'\n  },\n  series: [\n    {\n      data: [150, 230, 224, 218, 135, 147, 260],\n      type: 'line'\n    }\n  ]\n};\n";
const detail = `let option = null; \n
                        ${el} \n
                        return option`;
const fun = new Function(detail);
console.log(fun());
```

结果相当于

```js
let option = null;
option = {
  xAxis: {
    type: "category",
    data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
  },
  yAxis: { type: "value" },
  series: [
    {
      data: [150, 230, 224, 218, 135, 147, 260],
      type: "line",
    },
  ],
};
return option;
//使用时使用return fun(参数)执行
```

尽管可以使用 Function 构造函数创建函数，但最好不要使用它，因为用它定义函数比用传统方式要慢得多。不过，所有函数都应看作 Function 类的实例。



## 2、vue3中toRaw和markRaw

### 1、toRaw

​	`toRaw`，将响应式对象（由 `reactive`定义的响应式）转换为普通对象，返回由 reactive 或 readonly 方法转换成响应式代理的普通对象。



### 2、markRaw

#### （1）作用

标记一个对象,使其永远不会再成为响应式对象。



#### （2）应用场景

1.有些值不应被设置成响应式时,例如复杂的第三方类库等

2.当渲染具有不可变数据源的大列表时,跳过响应式转换可以提高性能

3.在动态渲染组件的时候我们就可以使用 markRaw 包裹。



### 3、使用案例

```vue
<template>
  <h2>{{state}}</h2>
  <button @click="testToRaw">测试toRaw</button>
  <button @click="testMarkRaw">测试markRaw</button>
</template>

<script lang="ts" setup>
	import { reactive,markRaw,toRaw } from 'vue'
  const state = reactive<any>({
    name: 'tom',
    age: 25,
  })
  const testToRaw = () => {
    const user = toRaw(state)
    user.age++  // 界面不会更新
  }
  const testMarkRaw = () => {
    const likes = ['a', 'b']
    // 往响应式对象中新增一个likes属性，该属性是响应式
    state.likes = markRaw(likes) // likes数组就不再是响应式的了
    setTimeout(() => {
      state.likes[0] += '--'
    }, 1000) //页面不会更新
  }
</script>
```





# 2022年11月22日

## jquery实现树状收缩表格

```html
<!DOCTYPE>
<html>
<head>
    <title></title>
    <meta http-equiv="X-UA-Compatible" charset="utf-8"/>
	<!--<script src="../js/jquery-1.3.2.min.js" type="text/javascript"></script>-->
	<script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
<style type="text/css">

table {
	font-family: verdana,arial,sans-serif;
	font-size:11px;
	border-width: 1px;
	border-color: #666666;
	border-collapse: collapse;
	width: 600px;
}
table th {
	border-width: 1px;
	padding: 8px;
	border-style: solid;
	border-color: #666666;
	background-color: #dedede;
}
table td {
	border-width: 1px;
	padding: 8px;
	border-style: solid;
	border-color: #666666;
	background-color: #ffffff;
	width:33%;
}
a {
	color: #99D;
}
</style>
  
</head>
<body>
  <table id="drillTable">
    <thead>
      <tr>
        <th style="text-align: center">区域</th>
        <th>推送企业总数</th>
        <th>已提交企业数</th>
        <th>上报率</th>
        <th>未填报数</th>
        <th>填报中数量</th>
        <th>审核通过数量</th>
        <th>审核率</th>
        <th>审核驳回数</th>
        <th>待审核数量</th>
      </tr>
    </thead>
    <tbody id="tableContainer">
      <tr
          d_dept_id="root"
          d_orgrank="d_orgrank_root"
          d_level="d_level_1"
          isShow="yes"
          style="display: none"
          >
        <td>
          <a class="changeItem"><span id="img">+</span>areaName</a>
        </td>
        <td>companyPush</td>
        <td>companySubmit</td>
        <td>submitRate</td>
        <td>companyNotSubmit</td>
        <td>companyReporting</td>
        <td>companyAuditPass</td>
        <td>auditRate</td>
        <td>companyBack</td>
        <td>waitAuditList</td>
      </tr>
    </tbody>
  </table>
  
  <script>
    var resultList = []
    function getTableData(param){
      $.ajax({
        type: "POST",
        url: "/directcore/report/getprogress4data",
        data: param,
        success: function (result) {
          if (!result.data){
            $('#tbody').html('')
            result.message ? zuidangermsg(result.message) : '';
          }else {
            //展示表格并填充数据
            resultList = [result.data]


            $(".changeItem").click(function () {
              drillDept(this);
            });

            $(function () {
              autoFirstClick();
            });

            function autoFirstClick() {
              $($("#drillTable").find("a")[0]).click();
            }

            function getTreeDate(id, list) {
              if (id === "root") {
                return list;
              } else {
                for (var i = 0; i < list.length; i++) {
                  if (list[i].areaCode === id) {
                    return list[i].child;
                  }
                  if (list[i].child && list[i].child.length) {
                    var result = getTreeDate(id, list[i].child);
                    if (result) {
                      return result;
                    }
                  }
                }
              }
            }

            //当前层的开闭
            function showClose(deptId) {
              $("tr[parent_id='" + deptId + "']").each(function (i) {
                var obj = $(this);
                var temp_rank = obj.attr("d_orgrank");
                var temp_flag = obj.attr("isShow");

                if ("yes" == temp_flag) {
                  obj.attr("isShow", temp_flag == "yes" ? "no" : "yes");
                  $("tr[d_orgrank^='" + temp_rank + "/" + "']").hide();
                  obj.hide();
                } else if ("no" == temp_flag) {
                  obj.attr("isShow", temp_flag == "yes" ? "no" : "yes");
                  obj.show();
                  showCloseChild($(this));
                  return;
                }
              });
            }
            //子集递归
            function showCloseChild(obj) {
              var temp_flag = obj.attr("isShow");
              var temp_rank = obj.attr("d_orgrank");
              var temp_dept_id = obj.attr("d_dept_id");
              if ("yes" == temp_flag) {
                obj.show();
                $("tr[parent_id='" + temp_dept_id + "']").each(function (i) {
                  showCloseChild(jQuery(this));
                });
              } else {
                return;
              }
            }
            //下钻
            function drillDept(td) {
              $(td)
                .find("#img")
                .html($(td).find("#img").html() == "+" ? "-" : "+");
              var objTr = $(td).parent().parent();
              var d_orgrank_all = objTr.attr("d_orgrank");
              var d_level = objTr.attr("d_level");
              var level = parseInt(d_level.substring("d_level_".length));
              var deptId = objTr.attr("d_dept_id");

              var isExists = $("tr[parent_id='" + deptId + "']");
              if (isExists.html()) {
                showClose(deptId);
                return;
              }

              //找到点击节点对应的父级书库数据
              var result = getTreeDate(deptId, resultList); //获取测试数

              var tJson = result;
              var tHtml = "";
              var space = "";
              for (var i = 0; i < level; i++) {
                space += "&nbsp&nbsp";
              }
              for (var i = 0; i < tJson.length; i++) {
                var tempDeptId = tJson[i].areaCode; //需要改的地方
                tHtml += "<tr d_dept_id='" + tempDeptId + "'";
                tHtml += " isShow='yes' ";
                tHtml += " parent_id='" + deptId + "' ";
                tHtml += " d_orgrank='" + d_orgrank_all + "/" + tempDeptId;
                tHtml += "' d_level='d_level_" + (level + 1) + "' >";

                if (tJson[i].child && tJson[i].child.length > 0) {
                  tHtml +=
                    "<td style='text-align:left;'>" +
                    space +
                    "<a class='xiazaunceng'" +
                    "><span id='img'>+</span>" +
                    tJson[i].areaName +
                    "</a></td>"; //需要改的地方
                } else {
                  tHtml +=
                    "<td style='text-align:left;'>" +
                    space +
                    tJson[i].areaName +
                    "</td>"; //需要改的地方
                }

                tHtml += "<td>" + tJson[i].quotaData.companyPush + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.companySubmit + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.submitRate + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.companyNotSubmit + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.companyReporting + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.companyAuditPass + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.auditRate + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.companyBack + "</td>"; //需要改的地方
                tHtml += "<td>" + tJson[i].quotaData.waitAuditList + "</td>"; //需要改的地方

                tHtml += "</tr>";
              }
              objTr.after(tHtml);
							
              //防止重复绑定，使用unbind()方法先解绑
              $(".xiazaunceng").unbind().click("click",function (){
                drillDept(this)
              })
            }
          }
        },
        error:function (e){
          zuidangermsg("操作失败",e.responseJSON.message)
        }
      });
    }
  </script>
</body>
</html>
```

### 拓展：unbind

unbind() 方法移除被选元素的事件处理程序。

该方法<font color=deepred>能够移除所有的或被选的事件处理程序</font>，或者当事件发生时终止指定函数的运行。

ubind() 适用于任何通过 jQuery 附加的事件处理程序。



# 2022年11月24日

## 1、大量数据渲染的动态列表

```vue
<template>
<!-- 虚拟列表 -->
<div>虚拟列表</div>
<div class="list" @scroll.passive="getScroll($event)">
  <div :style="sumHeight">
    <div
         class="item"
         v-for="(item, index) in options.slice(min - 2, min + 10)"
         :key="index"
         :style="itemHeight(item)"
         >
      <!-- 默认创建12个dom -->
      {{ item.value }}:{{ item.label }}
  	</div>
  </div>
</div>
</template>

<script setup>
import { ref } from 'vue';

//定义滚动事件，监听滚动距离,每一行高度为40，计算滚动长度对应的条数
let min = ref(2)
const getScroll = (event) => {
  let scrollTop = event.target.scrollTop
  //当滚动的距离大于2条数据高度时动态计算
  if (scrollTop > 2 * 40) {
    min.value = Math.ceil(scrollTop / 40)
  } else {
    min.value = 2
  }
}

//模拟10万条数据
const options = ref(
  Array.from({ length: 10000 }).map((_, idx) => ({
    value: `Option ${idx + 1}`,
    label: `${idx}`,
  })),
)

const sumHeight = {
  height: 40 * options.value.length + 'px',
  width: '100%',
}

const itemHeight = (param) => {
  return { top: `${40 * param.label}px` }
}
</script>

<style scoped lang="less">
.list {
  margin: 10px auto;
  width: 98%;
  height: 400px;
  overflow: auto;
  position: relative;
  border: 1px solid #000;
  .item {
    height: 40px;
    line-height: 40px;
    position: absolute;
    margin-left: 40px;
  }
}
</style>
```



# 2023年1月5日

## 1、使用html2canvas心得

使用该插件将dom绘制成canvas转成图片可用于保存，在可视化中应用于样式库组件渲染小效果图用。

```js
/**
* param是当前组件的属性对象，包括宽高等
* wrapScale全局缩放比例
* dom要转换的组件dom
* options是html2canvas插件的配置项
*/
const convertToImage = (param,wrapScale,dom,options = {}) => {
  const cacheScale = Number(wrapScale) / 100
  const scale = scaleFun(param,cacheScale)	//计算比例
  const opts = {
    scale, // 比例，默认是设备像素比
    width:param.width * cacheScale, //canvas宽度
    height:param.height * cacheScale, //canvas高度
    useCORS:true,	//允许跨域
    allowTaint:false,	//同上
    backgroundColor:"#1C1F25"	//canvas背景
  }
  return html2canvas(dom, opts).then((canvas) => {
    const canvasUrl = canvas.toDataURL("image/png")	//canvas生成base64
    const picFile = base64ToBlob(canvasUrl,"image/png")	//将base64编码的图片转换为blob文件
    return uploadImg(picFile,param)	//图片上传方法
  })
}

/**
* base64不能直接上传至oss服务器，需要转换成file文件上传,这里先转换为blob格式
* dataURI是base64图片
* type是文件类型
*/
const base64ToBlob = (dataURI,type) => {
  const binary = atob(dataURI.split(',')[1]);
  const array = [];
  for(let i = 0; i < binary.length; i++) {
    array.push(binary.charCodeAt(i));
  }
  return new Blob([new Uint8Array(array)], {type })
}

/**
* 请求接口，将blob格式抓换为file格式，再把转换后的图片上传获取链接
* dataURI是base64图片
* type是文件类型
*/
const uploadImg = (fileData,param) => {
 	const form = new FormData();
  form.append("bizType","9");
  const fileOfBlob = new File([fileData], `${new Date()}.jpg`);
  //请求参数填充
  form.append("file", fileOfBlob);
  form.append('proId',param.projectId);
  form.append('dirPath',DIR_PATH)
  //调用请求接口返回oss服务器链接
  const result = await api.upLoadFileToOss(form)
  if (result.success){
    return result.data.url
  }
  throw new Error(`网络异常`);
}
```



# 2023年1月12日

## 1、前端导出xlsx

### （1）`table_to_book`方法

方法：使用`xlsx`库中的<font color=deepred>`table_to_book`方法</font>。

实现过程：

1. 安装依赖

   ```shell
   npm install xlsx file-saver -D
   ```

2. 组件使用

   ```vue
   <template>
   	<table id="tableID"></table>
   </template>
   
   <script>
   import * as XLSX from "xlsx/xlsx.mjs";
   import FileSaver from "file-saver";
   export default {
     methods:{
       exportExecl(){
         const xlsxParam = { raw: true }; // 导出的内容只做解析，不进行格式转换
         //将表格生成工作对象
         const exportTable = XLSX.utils.table_to_book(
           document.querySelector("#table1"),
           xlsxParam
         );
         //获取二进制字符串作为输出
         const exportTableOut = XLSX.write(exportTable, {
           bookType: "xlsx",
           bookSST: true,
           type: "array"
         });
         //使用file-saver插件实现导出存储
         try {
           FileSaver.saveAs(
             new Blob([exportTableOut], {
               type: "application/octet-stream"
             }),
             `${Date.now()}报表.xlsx`
           );
         } catch (e) {
           if (typeof console !== "undefined") {
             console.log(e, exportTableOut);
           }
         }
         return exportTableOut;
       }
     }
   }
   </script>
   ```



### （2）原生js实现

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title></title>
  </head>
  <body>
    <p style="font-size: 20px; color: red">
      使用table标签方式将json导出xls文件
    </p>
    <button onclick="tableToExcel()">导出</button>
    <script>
      function tableToExcel() {
        //要导出的json数据
        const jsonData = [
          {
            name: "数据一",
            phone: "123456",
            email: "123@123456.com",
          },
          {
            name: "数据二"
            phone: "123456",
            email: "123@123456.com",
          },
          {
            name: "数据三",
            phone: "123456",
            email: "123@123456.com",
          },
          {
            name: "数据四",
            phone: "123456",
            email: "123@123456.com",
          },
        ];
        //列标题
        let head = `<tr><td>姓名</td><td>电话</td><td>邮箱</td></tr>`;
        let tbody = ""; //内容
        for (let item in jsonData) {
          tbody += `<tr>
                        <td>${jsonData[item].name + "\t"}</td>
                        <td>${jsonData[item].phone + "\t"}</td>
                        <td>${jsonData[item].email + "\t"}</td>
      </tr>`;
        }
        let str = head + tbody; //头部跟身体内容连接

        //Worksheet名
        let worksheet = "Sheet1";
        let uri = "data:application/vnd.ms-excel;base64,";

        //下载的表格模板数据
        let template = `<html xmlns:o="urn:schemas-microsoft-com:office:office" 
                  xmlns:x="urn:schemas-microsoft-com:office:excel" 
                  xmlns="http://www.w3.org/TR/REC-html40">
                  <head><!--[if gte mso 9]><xml><x:ExcelWorkbook><x:ExcelWorksheets><x:ExcelWorksheet>
                    <x:Name>${worksheet}</x:Name>
                    <x:WorksheetOptions><x:DisplayGridlines/></x:WorksheetOptions></x:ExcelWorksheet>
      </x:ExcelWorksheets></x:ExcelWorkbook></xml><![endif]-->
      </head><body><table>${str}</table></body></html>`;
        //下载模板
        window.location.href = uri + base64(template);
      }
      //输出base64编码
      function base64(s) {
        //字符串或二进制转为base64编码的方法：btoa
        return window.btoa(unescape(encodeURIComponent(s)));
      }
    </script>
  </body>
</html>

```



# 2023年1月31日

## 1、M1安装使用低版本node

安装nvm后，下载node12.18版本由于不兼容问题无法安装成功，因此需要开启兼容模式：

```shell
arch -x86_64 zsh
```



## 2、v-for和v-if优先级

在vue2中一个标签同时有`v-for`和`v-if`属性时，`v-for`优先级更高；

在vue3中一个标签同时有`v-for`和`v-if`属性时，`v-if`优先级更高；





# 2023年3月17日

## 1、接收下载后端文件流

后端返回的响应体类型是 `response.setContentType("application/vnd.ms-execl")`

响应头类型是`response.setHeader("Content-disposition", "attachment;filename=" + fileName + ".xlsx");`

```js
// 请求时
export const request = (option) =>
  new Promise((resolve, reject) => {
    axios({ ...option })
      .then((res) => {
      resolve(res.data);
    })
      .catch((err) => {
      reject();
    });
});

const exportDetail = (prams) => request({
  url: "路径",
  method: "",
  params,
  dataType: "application/json",
  headers: {
    "Content-Type": "application/json"
  },
  // 表示浏览器将要响应的数据类型，包括：'arraybuffer', 'document', 'json', 'text', 'stream'
  // 浏览器专属 'blob'
  responseType: "blob"
})

// 处理响应结果
exportDetail(params)
	.then((res) => {
    const blob = res;
    const binaryData = [];
    binaryData.push(blob);
    const url = window.URL.createObjectURL(new Blob(binaryData));
    const aLink = document.createElement("a");
    aLink.href = url;
    // 2.直接使用自定义文件名,设置下载文件名称
    aLink.setAttribute("download", "文件名字.xlsx");
    document.body.appendChild(aLink);
    // 模拟点击下载
    aLink.click();
    // 移除改下载标签
    document.body.removeChild(aLink);
	})
```



# 2023年7月4日

## 1、execl转json

### （1）安装依赖

```shell
npm install xlsx
```



### （2）项目引入

```js
import * as XLSX from "xlsx"
```



### （3）使用案例

```vue
<template>
  <div>
    <el-upload
      action=""
      :show-file-list="false"
      accept=".xls,.XLS,.xlsx,.XLSX"
      :http-request="httpRequest"
    >
      <el-button type="primary">导入</el-button>
    </el-upload>
  </div>
</template>

<script>
import * as xlsx from "xlsx";
export default {
  data() {
    return {
      jsonData: [],
    };
  },
  methods: {
    httpRequest(e) {
      // 文件信息
      const { file } = e;
      // 类型判断
      if (!file) {
        return false;
      } else if (!/\.(xls|xlsx)$/.test(file.name.toLowerCase())) {
        this.$message.error("上传格式不正确，请上传xls或者xlsx格式");
        return false;
      }

      // 读取文件
      const fileReader = new FileReader();
      fileReader.onload = (ev) => {
        try {
          const data = ev.target.result;
          // 获取所有表信息
          const workbook = xlsx.read(data, {
            type: "binary", // 以字符编码的格式解析
          });
          // 获取第一张表的表名
          const exlname = workbook.SheetNames[0];
          // 转成json数据
          const exl = xlsx.utils.sheet_to_json(workbook.Sheets[exlname]);
          this.jsonData = exl;
        } catch (e) {
          console.log("error", e);
          return falses;
        }
      };

      fileReader.readAsBinaryString(file);
    },
  },
};
</script>
```



# 2023年7月5日

## 1、iframe操作dom

当iframe加载完成后，

​	可以通过**`contentDocument`**获取iframe生成的<font color=deepred>文档对象</font>，

​	也可以通过**`contentWindow`**获取iframe生成的<font color=deepred>window对象</font>；

```vue
<template>
	<iframe src="" width="100%" height="200" class="iframe"></iframe>
</template>

<script>
	export default {
    mounted(){
      const iframe = document.querySelector('iframe')
      iframe.onload = () => {
        const iframeDocument = iframe.contentDocument
        // 可以获取内部dom元素
        iframeDocument.getElementById("")
        iframeDocument.querySelector("")
      }
    }
  }
</script>
```



### 注意⚠️：

由于安全问题，iframe文档的内容只能通过<font color=pink>**同一个域名下的另一个文档访问**</font>。
