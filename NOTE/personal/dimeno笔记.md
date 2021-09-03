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
