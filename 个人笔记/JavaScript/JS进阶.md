### 1、函数和方法的区别：

函数function：独立的function，称之为一个函数

```javascript
function foo(){}
```

方法method：当一个函数属于一个对象时，这个函数就是这个对象的方法

```javascript
var obj = {
  foo:function(){}
}
obj.foo()
```

 

### 2、DOM和BOM

DOM<font color=deepred>**文档对象模型**</font>：页面所有的内容表示为可修改的对象；

BOM<font color=deepred>**浏览器对象模型**</font>：由浏览器提供的用于处理文档之外所有内容的其他对象；

获取元素目前最常用的是querySelector和querySelectAll。



### 3、事件冒泡和事件捕获

在浏览器上对一个元素点击时，不仅仅是单一元素本身，html元素是存在父子元素叠加的。

#### （1）事件冒泡：

​	默认情况下事件是从最内层的元素向外层依次传递的，这个顺序成为事件冒泡。IE采用了事件冒泡的方式。



#### （2）事件捕获：

​	另一种是从外层到内层的方式，称为事件捕获。Netscape采用了事件捕获的方式。

开发中通常使用的是事件冒泡，监听事件方法一般使用`addEventListener`，第三个参数false表示事件冒泡。true表示事件捕获。



### 4、事件对象

#### （1）常见属性

1. type:事件的类型;
2. target:当前事件发生的元素;
3. currentTarget:当前处理事件的元素;
4. eventPhase:事件所处的阶段;
5. offsetX、offsetY:事件发生在元素内的位置;
6. clientX、clientY:事件发生在客户端内的位置;
7. pageX、pageY:事件发生在客户端相对于document的位置; 
8. screenX、screenY:事件发生相对于屏幕的位置



#### （2）常见方法

**preventDefault**：取消事件的默认行为；

**stopPropagation**：阻止事件的进一步传递（冒泡和捕获都可以阻止）；



### 5、事件委托

**事件冒泡在某种情况下可以帮助我们实现强大的事件处理模式 –** **事件委托模式**

<font color=red>事件委托模式</font>：

​	当子元素被点击时，父元素可以通过冒泡可以监听到子元素的点击; 

​	并且可以通过event.target获取到当前监听的元素;

减少元素上的事件绑定，提高性能。



### 3、元素大小和滚动

- clientWidth:contentWidth+padding(不包含滚动条)
- clientHeight:contentHeight+padding
- clientTop:border-top的宽度
- clientLeft:border-left的宽度
- offsetWidth:元素完整的宽度
- offsetHeight:元素完整的高度
- offsetLeft:距离父元素的x
- offsetHeight:距离父元素的y
- scrollHeight:整个可滚动的区域高度
- scrollTop:滚动部分的高度

![image-20220907105827244](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209071058428.png)



### 4、盒模型

#### IE盒模型

width包含border + padding + content

![image-20220905165251190](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209051652435.png)

#### W3C标准盒模型

width包含 content

![image-20220905165238842](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209051652011.png)



### 5、BOM

**BOM主要包含以下对象模型：window、location、history、navigator、screen**

#### （1）location对象

常见属性：

- href: 当前window对应的超链接URL, 整个URL；
- protocol: 当前的协议；
- host: 主机地址；
- hostname: 主机地址(不带端口)；
- port: 端口；
- pathname: 路径；
- search: 查询字符串；
- hash: 哈希值；

常见方法：
 assign:赋值一个新的URL，并且跳转到该URL中；

replace:打开一个新的URL，并且跳转到该URL中(不同的是不会在浏览记录中留下之前的记录)；

reload:重新加载页面，可以传入一个Boolean类型；



#### （2）history对象

常见属性：

- length:会话中的记录条数；
- state:当前保留的状态值；

常见方法：

 back():返回上一页，等价于history.go(-1)；

forward():前进下一页，等价于history.go(1)；

 go():加载历史中的某一页；

pushState():打开一个指定的地址；

replaceState():打开一个新的地址，并且使用replace；



### 6、Storage

**localStorage**：本地存储，提供永久性的存储方法，关闭网页重新打开存储内容依旧保留；

**sessionStorage**：会话存储，提供本次会话的存储，关闭网页就会被清除；



### 7、this绑定规则

#### （1）默认绑定

​	默认<font color=blue>绑定全局对象window</font>上。独立函数调用采用的就是默认绑定。

```js
function test1() {
  console.log(this);//window对象
  test2();
}
function test2() {
  console.log(this);//window对象
}
test1();
```



#### （2）隐式绑定

​	通过<font color=blue>对象调用内部方法</font>，该方法的this指向发起调用的对象

```js
function foo() {
  console.log(this);
}

var obj1 = {
  name: "obj1",
  foo: foo,
};

var obj2 = {
  name: "obj2",
  obj1: obj1,
};

obj2.obj1.foo();//{name: 'obj1', foo: ƒ}
```



#### （3）显示绑定

​	指向通过apply和call设置的对象，call和apply方法第一个参数要求传入一个对象，即this绑定的对象。第二个参数apply是个数组，call是参数列表。

```javascript
function foo(name, age, height) {
  console.log("foo函数被调用:", this)
  console.log("打印参数:", name, age, height)
}
//apply，第一个参数是对象，第二个参数是数组
foo.apply(object, ["kobe", 30, 1.98]);

//call，第一个参数是对象，第二个参数是参数列表
foo.call("call", "james", 25, 2.05);
```

如果希望方法始终绑定到obj对象上，但是又不希望对象本身身上有函数方法，使用bind创建一个新的绑定函数

```javascript
function foo(name, age, height, address) {
  console.log("foo:", this)
  console.log("参数:", name, age, height, address)
}
var obj = { name: "why" }
var bar = foo.bind(obj)
bar() // this -> obj
```



#### （4）new绑定

new一个对象的过程：

1、创建一个新对象；

2、将this指向绑定到新创建的对象上；

3、执行构造函数的方法，给新对象添加属性；

4、返回新对象；

```js
//new一个对象的四步
function newObj(fn,...args){
  const obj = {}
  obj._proto_ = fn.prototype
  fn.apply(obj,args)
  return obj;
}
```



### 8、this绑定规则优先级

<FONT COLOR=DEEPRED>默认绑定优先级最低，显式绑定高于隐式绑定，new绑定高于隐式绑定，new绑定优先级高于bind。</font>

注意⚠️：

如果显式绑定null或undefined，则会使用默认规则；

独立函数调用也是默认绑定；

间接函数调用this指向全局；



### 9、箭头函数

不会绑定this、arguments属性，不能作为构造函数来使用（不能使用new关键字）

#### （1）this指向

​	箭头函数不会创建自己的this指针，<font color=red>**只会从自己的作用域链的上一层继承this**</font>。通过 `call()` *或* `apply()` 方法调用一个函数时，**<font color=blue>只能传递参数</font>**，指定this的第一个参数会被忽略，这种现象对于 bind 方法同样成立。



#### （2）写法优化

1. 如果只有一个参数，可以省略参数的()

   ```js
   nums.forEach(item => {})
   ```

2. 如果函数执行体中只有一行代码，可以省略大括号

   ```js
   nums.forEach(item => log(item))
   ```

3. 如果函数执行体只有返回一个对象那么需要给对象加()

   ```js
   let bar = () => ({name:"abc"})//表示返回对象{name:"abc"}
   ```

   

### 10、浏览器内核

Trident :IE、360安全浏览器、搜狗高速浏览器、百度浏览器、UC浏览器；

Webkit -> Blink :Google Chrome，Edge；

Gecko :Mozilla Firefox；

浏览器内核就是**<FONT COLOR=DEEPRED>浏览器排版引擎</FONT>**



### 11、script和页面关系

​	**<font color=blue>解析HTML遇到script元素会停止构建DOM树</font>**，因为JS会操作DOM，等到DOM树构建完成并且渲染再执行JavaScript，会造成严重的回流和重绘，影响页面的性能；

为了解决JS脚本加载时间长白屏问题script提供两个属性defer和async：

#### （1）defer

​	**浏览器不等待脚本下载**，继续解析HTML，构建DOM Tree；如果脚本提前下载好了，它会等待DOM Tree构建完成，在DOMContentLoaded事件之前先执行defer中的代码；

特点：<font color=blue>可以保证执行顺序；可以提升页面性能，推荐放到head中；仅使用外部脚本</font>；

#### （2）async

​	**让脚本不阻塞页面**，与defer类似。

特点：<font color=blue>不能保证顺序，属于独立下载、独立运行，不会等待其他脚本；不能保证在DOMContentLoaded之前或者之后执行</font>；

#### （3）应用场景

1. defer常用于需要在文档解析后操作DOM的JS代码，并对script文件有要求；
2. async常用于独立脚本，对DOM没有依赖；



### 12、输入URL到页面展示过程

![](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209071403000.png)

#### （1）URL加载总结

1. 浏览器进程收到URL，转发给网络进程；
2. 网络进程发起真正URL请求；
3. 网络进程收到响应头数据，转发给浏览器进程；
4. 浏览器接受响应头数据后发送“提交导航”给渲染进程；
5. 渲染进程和网络进程建立数据管道，接收HTML、CSS数据；
6. 渲染进程向浏览器进程发送“确认提交”准备接受和解析数据；
7. 浏览器进程收到渲染进程“提交”信息后，移除旧数据更新页面状态；



#### （2）渲染流程总结

1. 渲染进程把HTML内容转换成DOM树结构；
2. 渲染进程把CSS转换成styleSheet，计算DOM节点样式；
3. 创建布局树，计算绑定布局样式信息；
4. 对布局树分层，创建分层树；
5. 对每个图层生成绘制表，交给合成线程；
6. 合成线程把图层分成图块，在光栅化线程池中转换成位图；
7. 合成线程发送绘制指令给浏览器进程；
8. 浏览器进程根据指令生成页面，显示在显示器上；



#### （3）HTTP请求总结

- 浏览器发起请求
  - 构建请求行信息；
  - 从浏览器缓存中查找请求文件，如果存在拦截请求，返回缓存资源，请求结束；
  - DNS解析，将域名映射为IP地址；
  - 等待TCP队列，一个域名最多建立6个TCP连接，超过需等待；
  - 建立TCP连接
  - 发送HTTP请求，请求数据格式包含请求行(请求方法+请求URL+协议版本)、请求头和请求体；
- 服务器处理请求
  - 返回响应数据，响应数据格式包含响应行(协议版本+状态码)、响应头和响应体；
  - 断开TCP连接；
  - 是否进行重定向，重定向重新执行请求流程；



### 13、JS引擎

浏览器内核由两部分组成：（以webkit为例）

- WebCore：负责HTML解析、布局、渲染等工作；
- JavaScriptCore：解析、执行JavaScript；



### 14、执行上下文

#### （1）JS执行流程

JavaScript 代码在执行之前需要先编译，编译会生成两部分：<font color=deepred>**执行上下文和可执行代码**</font>。变量提升就发生在编译阶段，JS会把变量和函数声明部分提升到开头。

![image-20220909094445849](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209090944954.png)

当一段代码定义了两个相同名字的函数，那么最后生效的是最后一个函数。



#### （2）执行上下文创建情况

- 情况一：<font color=blue>执行全局代码</font>，创建**全局上下文**，且整个页面周期只有一份；

- 情况二：<font color=blue>调用一个函数</font>时，创建**函数执行上下文**，函数执行结束执行上下文销毁；

- 情况三：<font color=blue>使用eval函数</font>会创建执行上下文；



### 15、调用栈

- JS引擎利用栈结构管理执行上下文，称调用栈,属于追踪函数执行的一个机制。

- 如果在一个函数 A 中调用了另外一个函数 B，那么 JavaScript 引擎会为 B 函数创建执行上下文，并将 B 函数的执行上下文压入栈顶。
- 当前函数执行完毕后，JavaScript 引擎会将该函数的执行上下文弹出栈。

- 调用栈是有大小的，空间被占满就会发生堆栈溢出。



### 16、作用域

​	作用域是指在程序中定义变量的区域，即**作用域控制着变量和函数的可见性和生命周期**。

- <font color=blue>全局作用域</font>中的对象在代码中的任何地方都能访问，其生命周期伴随着页面的生命周期。
- <font color=blue>函数作用域</font>就是在函数内部定义的变量或者函数，并且定义的变量或者函数只能在函数内部被访问。函数执行结束之后，函数内部定义的变量会被销毁。

```javascript
//举个例子
function foo(){
    var a = 1
    let b = 2
    {
      let b = 3
      var c = 4
      let d = 5
      console.log(a)
      console.log(b)
    }
    console.log(b) 
    console.log(c)
    console.log(d)
}   
foo()
```

对该函数创建执行上下文的分析图：

![image-20220909115600791](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209091156304.png)

​	作用域块中通过 **let 声明的变量，会被存放在词法环境的一个单独的区域中**，这个区域中的变量并不影响作用域块外面的变量。

​	词法环境内部，**维护了一个小型栈结构**，栈底是函数最外层的变量，<font color=deepred>进入一个作用域块后，就会把该作用域块内部的变量压到栈顶；当作用域执行完成之后，该作用域的信息就会从栈顶弹出，这就是词法环境的结构</font>。这里说的变量仅指**通过 let 或者 const 声明的变量**。

​	运行时沿着词法环境的栈顶向下查询，如果在词法环境中的某个块中查找到了，就直接返回给 JavaScript 引擎，如果没有查找到，那么继续在变量环境中查找。当作用域块执行结束之后，其内部定义的变量就会从词法环境的栈顶弹出。

​	块级作用域就是通过词法环境的栈结构来实现的，而变量提升是通过变量环境来实现，通过这两者的结合，JavaScript 引擎也就同时支持了变量提升和块级作用域了。



### 17、作用域链

​	每个执行上下文的变量环境中，都包含一个**外部引用**，用来指向外部的执行上下文。当函数中使用外部变量时，查找这个变量的“链条”称为**作用域链**。

​	**作用域链是由词法作用域决定的**，词法作用域是**由代码中函数声明位置决定的**，是静态作用域，在代码编译阶段就决定好的，和函数由谁调用没关系。

```javascript
//举个例子
function bar() {
    var myName = "极客世界"
    let test1 = 100
    if (1) {
        let myName = "Chrome浏览器"
        console.log(test)
    }
}
function foo() {
    var myName = "极客邦"
    let test = 2
    {
        let test = 3
        bar()
    }
}
var myName = "极客时间"
let myAge = 10
let test = 1
foo()
```

![image-20220911001321954](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209110013352.png)



### 18、闭包

​	根据词法作用域的规则，<font color=red>内部函数总是可以访问其外部函数中声明的变量</font>，**当通过调用一个外部函数返回一个内部函数后，即使该外部函数已经执行结束了，但是内部函数引用外部函数的变量依然保存在内存中，我们就把这些变量的集合称为闭包**。比如外部函数是 foo，那么这些变量的集合就称为 foo 函数的闭包。



### 19、函数参数对象和剩余参数

- rest剩余参数只包含那些没有对应形参的实参，而arguments对象包含了传给函数的所有实参；
- arguments对象不是一个真正的数组(<font color=red>类数组</font>)，而rest参数是一个<font color=red>真正的数组</font>，可以进行数组的所有操作；
- arguments是早期的ECMAScript中为了方便去获取所有的参数提供的一个数据结构，而rest参数是ES6中提供并且希望以此来替代arguments的；
- rest剩余参数必须放到最后一位，否则会报错；



### 20、函数柯里化

​	**只传递给函数一部分参数来调用它，让它返回一个函数去处理剩余的参数**，这个过程称为柯里化。

注意⚠️：柯里化<font color=red>**不会调用函数，只是对函数进行转换**</font>。

```javascript
//普通函数
function add1(x,y,z){
  return x + y + z;
}
add1(10,20,30)

//柯里化处理，方式一
function add2(x){
  return function(y){
    return function(z){
      return x + y + z
    }
  }
}
add2(10)(20)(30)

//柯里化处理，方式二
var add3 = x => y => z => {
  return x + y + z
}
add2(10)(20)(30)
```



#### （1）柯里化优势：

函数的职责单一：减少一个函数中处理的问题；

```javascript
//每一层只做单一的事
function add(x){
  x = x + 2;
  return function(y){
    y = y * 2;
    return function(z){
      z = z ** 2;
      return x + y + z;
    }
  }
}
```

函数的参数复用：外层函数传了返回里层函数调用时候就不用传了；

```javascript
function make(num){
	return function(count){
    return num + count;
  }
}
var add = make(5);
add(10);	//15
```



#### （2）自动柯里化函数

```javascript
function foo(x, y, z) {
  console.log(x + y + z);
}

// 自动柯里化函数
function autoCurry (fn) {
  // fn是传入的函数，长度是函数参数个数
  function curryied (...args) {
    // args是调用时的参数
    if (args.length >= fn.length) {
      return fn.apply(this, args)
    } else {
      function curryied2 (...args) {
        return curryied.apply(this, [...args, ...args2])
      }
      return curryied2
    }
  }
  return curryied
}

let curryFoo = autoCurry(foo);
console.log(curryFoo(10)(20)(30)) //60
console.log(curryadd(10, 20, 30)) //60
```



### 25、手写apply/call/bind

#### （1）call、apply和bind的区别

- call和apply会**改变this的指向并<font color=red>立即执行函数</font>**
  - apply接受数组参数；
  - call接受参数序列；

- bind不会立即执行函数而是返回this改变后的函数。



#### （2）apply

```javascript
//函数对象
function foo(age) {
  console.log(this, age);
}

//apply，接受参数是个数组
Function.prototype.ycapply = function (thisArg, otherArg) {
  //第一个参数是要绑定的this,当前this是调用的函数对象。第二个参数是数组
  thisArg =
    thisArg === undefined || thisArg === null ? window : Object(thisArg);
  //将this指向从函数对象绑定到thisArg上
  Object.defineProperty(thisArg, "fn", {
    enumerable: false,
    configurable: true,
    value: this,
  });
  thisArg.fn(...otherArg);
  delete thisArg.fn;
};
foo.ycapply({ name: "zhang" }, [25]); //{name: 'zhang'} 25
```



#### （3）call

```javascript
//函数对象
function foo(name,age){
  console.log(this,name,age)
}

//call,接受参数序列
Function.prototype.yccall = function (thisArg, ...otherArg) {
  thisArg =
    thisArg === null || thisArg === undefined ? window : Object(thisArg);
  Object.defineProperty(thisArg, "fn", {
    enumerable: false,
    configurable: true,
    value: this,
  });
  thisArg.fn(...otherArg);
  delete thisArg.fn;
};
foo.yccall({ name: "zhang" },"man",25);
```

还可以将重复部分封装在原型上

```javascript
Function.prototype.ycexec = function(thisArg, otherArgs) {
  thisArg = (thisArg === null || thisArg === undefined)? window: Object(thisArg)
  Object.defineProperty(thisArg, "fn", {
    enumerable: false,
    configurable: true,
    value: this
  })
  thisArg.fn(...otherArgs)
  delete thisArg.fn
}
Function.prototype.ycapply = function(){
  this.ycexec(thisArg,otherArgs)
}
Function.prototype.yccall = function(){
  this.ycexec(thisArg,otherArgs)
}
```



#### （4）bind

```javascript
//手写bind，返回this改变后的函数
function foo(name, age, height, address) {
  console.log(this, name, age, height, address);
}
Function.prototype.ycbind = function (thisArg, ...otherArgs) {
  thisArg =
    thisArg === null || thisArg === undefined ? window : Object(thisArg);
  Object.defineProperty(thisArg, "fn", {
    enumerable: false,
    configurable: true,
    writable: false,
    value: this,
  });
  return (...newArgs) => {
    var allArg = [...otherArgs, ...newArgs];
    thisArg.fn(...allArg);
  };
};

var newFoo = foo.ycbind("abc", "kobe", 30);
//使用时要调用才执行
```





### 21、组合函数

封装组合函数，同时调用传入的函数

#### （1）封装前

```javascript
//封装前
function double(num) {
  return num * 2;
}
function pow(num) {
  return num ** 2;
}
console.log(pow(double(2)));//16

//使用基础compose函数
function compose(num) {
  return pow(double(num));
}
console.log(compose(2));//16
```



#### （2）封装后

```js
//封装通用函数
function composeFn(...fns) {
  var length = fns.length;
  //边界判断
  if (length <= 0) return;
  for (var i = 0; i < length; i++) {
    var fn = fns[i];
    if (typeof fn !== "function") {
      throw new Error(`index position ${i} must be function`);
    }
  }
  //返回新函数
  return function (...args) {
    var result = fns[0].apply(this, args);
    for (var i = 1; i < length; i++) {
      var fn = fns[i];
      result = fn.apply(this, [result]);
    }
    return result;
  };
}
var newFn = composeFn(double, pow);
console.log(newFn(2));//16
```



### 22、对象属性操作

​	想要对一个属性进行比较精确的操作控制就可以使用属性描述符，通过属性描述符，可以<font color=deepred>精准的添加和修改对象的属性</font>。属性描述符**需要使用`Object.defineProperty`对对象进行添加或修改**。

#### （1）Object.defineProperty

接收三个参数：

- obj：要定义属性的对象；
- prop：要定义或修改的属性名称或symbol
- descriptor：要定义或修改的属性描述符

返回值是被传递给函数的对象



#### （2）属性描述符分类

##### 2.1、数据属性

configurable：是否可通过delete删除属性或修改特性；

enumerable：是否可通过for-in或Object.keys遍历；

writeable：是否可修改属性值；

value：属性值，读取属性时会返回该值，默认是undefined；



##### 2.2、存取属性

configurable：是否可通过delete删除属性或修改特性；

enumerable：是否可通过for-in或Object.keys遍历；

get：获取属性执行函数；

set：设置属性执行函数；



#### （3）对象方法补充

- Object.defineProperties()：在一个对象上定义多个属性；
- Object.getOwnPropertyDescriptor()：获取对象的描述符；
- Object.preventExtensions()：禁止对象扩展新属性；
- Object.seal()：密封对象，不允许配置删除属性；
- Object.freeze()：不允许修改现有属性；



### 23、对象原型

**<font color=red>每个对象都有一个内置属性[[prototype]]</font>**，该属性指向另外一个对象，当使用key获取对象的value时，对象中没有该属性，就会访问[[prototype]]内置属性指向的对象的属性。

**<font color=red>每个函数都有一个prototype属性</font>**，使用构造函数创建的对象的[[prototype]]属性始终指向该构造函数的prototype。

#### （1）获取原型方式

- 通过对象的`_proto_`属性获取(浏览器添加的，存在兼容性问题)；
- 通过Object.getPrototypeOf方法获得；



#### （2）constructor

原型对象上面有一个属性constructor，该属性指向当前的函数对象

```javascript
function Person(){}
var p1 = new Person();
Person.prototype.constructor;//[Function:Person]
p1.__proto__.constructor;//[Function:Person]
```



#### （3）函数对象

对象中的某些属性和方法是来自Function.prototype，在Function.prototype中添加的属性和方法, 可以被所有的函数获取

```javascript
var test = function () {};
Function.prototype.info = "hello zhang";
console.log(test.info);	//hello zhang
```



### 24、面向对象

#### （1）三大特性

<font color=red>**封装、继承、多态**</font>

封装：将属性和方法封装到一个类中；

继承：<font color=deepred>将重复代码和逻辑抽取到父类中，供子类继承使用</font>，是多态前提；

多态：不同对象在执行时表现出的不同形态；



#### （2）原型链

原型链的尽头是 `[Object: null prototype] {}`，从Object直接创建出来的对象原型都是null。

```javascript
var obj = {};//普通对象

obj.__proto__；//function Obejct(){}.prototype

obj.__proto__.__proto__;//[Object:null prototype]{}
```

**原型链最顶层的原型对象就是Object的原型对象**



#### （3）class类

- class定义的类, 不能作为一个普通的函数进行调用，只能通过new方法创建实例；
- 在子类的构造函数中使用this或者返回默认对象之前，必须先通过super调用父类的构造函数；
- 子类方法中扩充父类方法也需要通过super调用父类方法；
- JS只支持单继承（不支持多继承）



##### 3.1、定义类

```js
class myClass {
  //class方法
  constructor(){...};
  methods1(){};
  methods2(){};
  ...
}
```



##### 3.2、使用类

​	**在JS中，<font color=red>类的表现形式就是构造函数</font>。**使用 `new MyClass()` 来创建具有上述列出的所有方法的新对象。`new` 会自动调用 `constructor()` 方法，因此<font color=red>可以在 `constructor()` 中初始化对象</font>。

- 构造函数：
  - 构造函数是一个普通函数，和普通函数没有区别；
  - 当一个普通函数被new操作符调用后，这个函数就被称为是一个构造函数；

- 函数使用new操作符调用后执行的操作：
  1. 在内存中创建一个空对象；
  2. 在对象内部的隐式原型属性被赋值为构造函数的原型对象属性；
  3. 构造函数的this会指向创建出的新对象；
  4. 执行构造函数的内部代码即函数体；
  5. 如果构造函数没有返回非空对象，则返回创建出来的新对象。

**注意⚠️：类的方法之间没有逗号**



### 26、原型判断方法

#### （1）isPropertyOf

​	检测一个对象是否是另一个对象的原型，即一个对象是否被包含在另一个对象的原型链中。

```js
let p = {x:1};
let o = Object.create(p);
p.isPropertyOf(o);//true,表示o继承自p
Object.prototype.isPropertyOf(p);//true，表示p继承自Object.prototype
```



#### （2）instanceOf

判断一个对象是否是另一个类的实例

```js
let d = new Date();
d instanceOf Date;//true,d是Date的实例
d instanceOf Object;//true,所有对象都是Object的实例
```



#### （3）hasOwnProperty

判断某个属性是否存在于某个对象，不能判断原型链，不会在原型中查找

```js
let obj = {name:"zhang"};
Object.prototype.hasOwnProperty.call(obj,'name')//true
obj.hasOwnProperty('age')//false
```



### 26、ES6特性

- class类
- let、const
- 模版字符串
- 函数默认参数
- 解构赋值
- 箭头函数
- 扩展运算符
- 数字进制
  - 0b二进制，0o八进制，0x16进制
- Map和Set
- Proxy、Reflect
- Promise
- ES Module



### 27、ES7

- includes方法

数组的includes方法，找到一项就返回true，没找到返回false；

indexOf查找某项元素，找到返回第一次出现索引，没找到返回-1；

- 指数运算符

**运算符，可以对数字计算乘方；功能替代Math.pow



### 28、ES8

- Object.values方法

获取所有的value值

- Object entries

将对象转数组

- padStart和padEnd方法

对字符串进行首尾填充

- async、await



### 29、ES9

- Async iterator
- Promise finally



### 30、ES10

- flat数组扁平化
- flatMap
- trimStart和trimEnd方法消除首尾空格



### 31、ES11

- BigInt超出最大安全整数的数字

BigInt的表示方法是在数值的后面加上n

- ??逻辑赋值和?.可选链
- for...in...



### 32、ES12

- 对象垃圾回收回调

**FinalizationRegistry**对象提供对象被回收时的回调，通过register方法调用，注册要标记的对象。

```js
let obj = {name:"zhang"};
const registry = new FinalizationRegistry(value => {
  console.log("对象被销毁了",value)
})
registry.register(obj,"obj")
obj = null;//触发销毁
```

- replaceAll

字符串全替换



### 33、ES13

- Object.hasOwn方法

判断对象中是否存在某个属性

- class新增#表示私有属性，只能内部访问；

  static表示类的静态方法，可以用类直接调用；



### 34、Proxy

<font color=deepred>对比Object.defineProperty</font>：

1. Object.defineProperty设计之初不是为了监听整个对象中所有属性的，**需要使用遍历对每个属性实现**；
2. **新增、删除属性Object.defineProperty是无能为力的**；

#### （1）基本使用

​	先针对目标对象创建代理对象，然后<font color=red>对对象的所有操作**都通过代理对象完成**</font>，代理对象可以监听对代理对象的操作。

```js
const p = new Proxy(target, handler)
```

参数target表示要侦听的目标对象；

参数handler表示处理对象，其中包括get、set等捕获器；



#### （2）捕获器

常见的捕获器包括get、set、has、deleteProperty等

##### set捕获器

包含四个参数：**<font color=blue>target目标对象；propertyKey被侦听的属性；value新设置的属性值；receiver调用的代理对象</font>**；

```js
const objProxy = new Proxy(obj, {
  set: function(target, key, newValue) {
    console.log(`监听: 监听${key}的设置值: `, newValue)
    target[key] = newValue
  }
})
```

##### get捕获器

包含三个参数：**<font color=blue>target目标对象；propertyKey被侦听的属性；receiver调用的代理对象</font>**；

```js
const objProxy = new Proxy(obj, {
  get: function(target, key) {
    console.log(`监听: 监听${key}的获取`)
    return target[key]
  }
})
```



### 35、Reflect

也是ES6新增的一个API，它是**一个对象**，字面的意思是**反射**

#### （1）作用

- 提供了很多<font color=deepred>操作JavaScript对象的方法</font>，像Object中操作对象的方法；
- 获取原型的方法Reflect.getPrototypeOf(target)类似于 Object.getPrototypeOf()；
- 属性描述符比如Reflect.defineProperty(target, propertyKey, attributes)类似于Object.defineProperty() ;



#### （2）设计目的

​	早期的ECMA规范中没有考虑到对 **对象本身** 的操作如何设计会更加规范，所以将这些API放到了Object上面;  但是Object作为一个构造函数，这些操作实际上放到它身上并不合适，因此新增Reflect做到不操作原对象。



#### （3）和Object的区别

| Method Name                   | `Object`                                                     | `Reflect`                                                    |
| :---------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `defineProperty()`            | [`Object.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 返回传递给函数的对象。如果未在对象上成功定义属性，则返回`TypeError`。 | 如果在对象上定义了属性，则[`Reflect.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/defineProperty)返回布尔值。 |
| `defineProperties()`          | [`Object.defineProperties()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties) 返回传递给函数的对象。如果未在对象上成功定义属性，则返回`TypeError`。 | N/A                                                          |
| `set()`                       | N/A                                                          | 如果在对象上成功设置了属性，则[`Reflect.set()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/set)返回`true`，否则返回`false`。如果目标不是`Object`，则抛出`TypeError` |
| `get()`                       | N/A                                                          | [`Reflect.get()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/get)返回属性的值。如果目标不是`Object`，则抛出`TypeError`。 |
| `deleteProperty()`            | N/A                                                          | 如果属性从对象中删除，则[`Reflect.deleteProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/deleteProperty)返回`true`，否则返回`false`。 |
| `getOwnPropertyDescriptor()`  | 如果传入的对象参数上存在[`Object.getOwnPropertyDescriptor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor) ，则会返回给定属性的属性描述符，如果不存在，则返回`undefined`。 | 如果给定属性存在于对象上，则[`Reflect.getOwnPropertyDescriptor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/getOwnPropertyDescriptor) 返回给定属性的属性描述符。如果不存在则返回`undefined`，如果传入除对象（原始值）以外的任何东西作为第一个参数，则返回`TypeError` |
| `getOwnPropertyDescriptors()` | [`Object.getOwnPropertyDescriptors()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors) 返回一个对象，其中包含每个传入对象的属性描述符。如果传入的对象没有拥有的属性描述符，则返回一个空对象。 | N/A                                                          |
| `getPrototypeOf()`            | [`Object.getPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/GetPrototypeOf)返回给定对象的原型。如果没有继承的原型，则返回`null。`在 ES5 中为非对象抛出`TypeError`，但在 ES2015 中强制为非对象。 | [`Reflect.getPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/getPrototypeOf)返回给定对象的原型。如果没有继承的原型，则返回 null，并为非对象抛出`TypeError`。 |
| `setPrototypeOf()`            | 如果对象的原型设置成功，则[`Object.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf)返回对象本身。如果设置的原型不是`Object`或`null`，或者被修改的对象的原型不可扩展，则抛出`TypeError`。 | 如果在对象上成功设置了原型，则[`Reflect.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/setPrototypeOf) 返回 true，否则返回 false（包括原型是否不可扩展）。如果传入的目标不是`Object`，或者设置的原型不是`Object`或`null`，则抛出`TypeError`。 |
| `isExtensible()`              | 如果对象是可扩展的，则 Object.isExtensible（）返回 true，否则返回 false。如果第一个参数不是对象（原始值），则在 ES5 中抛出`TypeError`。在 ES2015 中，它将被强制为不可扩展的普通对象并返回`false`。 | 如果对象是可扩展的，则[`Reflect.isExtensible()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/isExtensible) 返回`true`，否则返回`false`。如果第一个参数不是对象（原始值），则抛出`TypeError`。 |
| `preventExtensions()`         | [`Object.preventExtensions()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions) 返回被设为不可扩展的对象。如果参数不是对象（原始值），则在 ES5 中抛出`TypeError`。在 ES2015 中，参数如为不可扩展的普通对象，然后返回对象本身。 | returns `true` if the object has been made non-extensible, and `false` if it has not. Throws a `TypeError` if the argument is not an object (a primitive).如果对象已变得不可扩展，则[`Reflect.preventExtensions()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/preventExtensions) 返回`true`，否则返回`false`。如果参数不是对象（原始值），则抛出`TypeError`。 |
| `keys()`                      | [`Object.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)返回一个字符串`数组`，该字符串映射到目标对象自己的（可枚举）属性键。如果目标不是对象，则在 ES5 中抛出`TypeError`，但将非对象目标强制为 ES2015 中的对象 | N/A                                                          |
| `ownKeys()`                   | N/A                                                          | [`Reflect.ownKeys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys)返回一个属性名称数组，该属性名称映射到目标对象自己的属性键。如果目标不是`Object`，则抛出`TypeError`。 |



#### （3）常见方法使用

##### 3.1、Reflect.has

##### 3.2、Reflect.get

##### 3.3、Reflect.set

##### 3.4、Reflect.deleteProperty

```js
const objProxy = new Proxy(obj, {
  has:function(target, key){
    //判断一个对象是否存在某个属性，和 in 运算符 的功能完全相同
    return Reflect.has(target, key)
  },
  get:function(target, key，receiver){
    //获取对象身上某个属性的值，类似于 target[name]
    return Reflect.get(target, key, receiver)
  },
  set:function(target, key, valu,receiver){
    //将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true
    return Reflect.set(target, key, value, receiver)
  },
  deleteProperty:function(target, key){
    //作为函数的delete操作符，相当于执行 delete target[name]
    return Reflect.deleteProperty(target, key)
  }
})
```



#### （4）优势

- 提供对象的代理，不直接操作原对象；
- Reflcet的set方法返回布尔值，可以用来判断操作是否成功；
- 参数receiver表示外层的Proxy对象，在set中可以决定对象访问器setter/getter的this指向；



### 36、Promise

三种状态：pending待定、fulfilled完成和rejected失败；

一旦状态被确定下来，Promise的状态会被锁死，Promise的状态是不可更改的；

#### （1）then方法

Promise对象上的方法，可以链式调用，接收resolve的参数回调；

#### （2）catch方法

Promise对象上的方法，接收reject的参数回调；

#### （3）finally方法

Promise对象上的方法，表示无论Promise对象无论变成fulfilled还是ejected最终都会被执行；

#### （4）all方法

将多个Promise包裹在一起形成一个新的Promise，所有的promise状态变为fulfilled时状态更改为fulfilled，一旦有一个promise状态为reject状态就变为reject；

#### （5）race方法

将多个Promise包裹在一起形成一个新的Promise，一旦有一个promise更改了状态就跟着改变状态；

#### （6）allSettled方法

当所有的Promise都有结果，无论是fulfilled，还是rejected时，才会更改最终的状态；

#### （7）any方法

等待第一个fulfilled状态更改状态为fulfilled，如果都是reject会报错`AggregateError: All promises were rejected`;



### 37、迭代器

<font color=deepred>	能使容器对象(链表或数组等)进行遍历访问的**对象**</font>。

​	迭代器协议是定义了产生一系列值的标准方式。Javascript中迭代器是**符合迭代器协议的对象**，体现在next()方法上，<font color=red>next方法</font>返回拥有两个属性的对象。

#### next方法

`{ done: Boolean, value: XX }`

- done属性：迭代到容器最后一个值时为true；没迭代到最后时为false；
- value属性：容器序列中的next值；

![image-20220927235607577](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209272356583.png)

```js
//数组通用迭代器
const names = ["abc", "cba", "nba"]
const nums = [100, 24, 55, 66, 86]
//迭代器构造函数
function createArrayIterator(arr){
  let index = 0;
  return {
    next:function(){
      if(index < arr.length){
        return {done:false, value:arr[index++]}
      }else{
        return {done:true, value:undefined}
      }
    }
  }
}
//names数组的迭代器
const namesIterator = createArrayIterator(names);
console.log(namesIterator.next());//{done:false,value:"abc"}
console.log(namesIterator.next());//{done:false,value:"cba"}
//nums数组的迭代器
const numsIterator = createArrayIterator(nums);
console.log(numsIterator.next());//{done:false,value:100}
console.log(numsIterator.next());//{done:false,value:24}
```



### 38、可迭代对象

<font color=deepred>	当一个对象实现了迭代器协议时，该对象就是一个**可迭代对象**</font>。

#### 可迭代对象实现

将对象变成一个可迭代对象的方法：

1. 必须实现一个特定的函数`Symbol.iterator`；
2. 函数必须要返回一个迭代器（作用于当前对象）

```js
//info就是个可迭代对象
const info = {
  friends: ["zhang", "yu", "chen"],
  //特定可迭代函数
  [Symbol.iterator]: function () {
    let index = 0;
    return {
      next: () => {
        if (index < this.friends.length) {
          return { done: false, value: this.friends[index++] };
        } else {
          return { done: true };
        }
      }
    };
  }
};
```

当使用 for...of 操作可迭代对象时，就会调用内部的迭代器方法；



#### 原生可迭代对象

​	<font color=deepred>许多原生对象已经实现了可迭代协议，会**自动生成一个可迭代对象**；</font>

包括：String、Array、Map、Set、arguemnts对象、NodeList集合；



### 39、自定义类的迭代及中断

​	通过class创建的实例对象，如果希望默认是可迭代的，那么在设计时就需要符合迭代器协议。

​	同时<font color=blue>**迭代器支持在不完全迭代的情况下中断**，需要在next方法的同层级添加return方法</font>。

```js
class Person {
  constructor(name, age, height, friend) {
    this.name = name;
    this.age = age;
    this.height = height;
    this.friend = friend;
  }
  running() {}
  //可迭代对象实现
  [Symbol.iterator]() {
    let index = 0;
    const iterator = {
      next: () => {
        if (index < this.friend.length) {
          return { done: false, value: this.friend[index++] };
        } else {
          return { done: true };
        }
      },
      return: () => {
        return { done: true };
      },
    };
    return iterator;
  }
}
//使用时可使目标对象变成可迭代对象
const p1 = new Person("zhang", 25, 1.8, ["zhangge", "yuge", "chenge"]);
for (const item of p1) {
  console.log(item);//zhangge,yuge
  if(item === "yuge"){
    break
  }
}
```



### 40、生成器

​	生成器是ES6中新增的一种**函数控制、使用方案**，可以更加灵活的控制函数什么时候继续和暂停执行。

#### （1）生成器函数特征

1. 生成器函数需要<font color=red>在function后面**加符号***</font>；
2. 生成器函数可以<font color=red>通过**yield关键字**控制函数的执行流程</font>；
3. 生成器函数返回值是一个Generator(生成器)，<font color=red>生成器是一种**特殊的迭代器**</font>；



#### （2）生成器函数执行

先声明生成器函数，然后调用next方法，可以通过yield返回结果。

```js
// 定义一个生成器函数
function* foo() {
  const value1 = 100;
  yield value1;
  
  const value2 = 200;
  yield value2;

  const value3 = 300;
  yield value3;
}
//调用生成器函数, 返回一个生成器对象
const generator = foo();
//调用next方法执行，执行到第一个yield暂停执行；
console.log(generator.next()); //{value: 100, done: false}
//执行到第二个yield暂停执行；
console.log(generator.next()); //{value: 200, done: false}
//执行到第三个yield暂停执行；
console.log(generator.next()); //{value: 300, done: false}
```



#### （3）next函数

​	生成器函数支持传递参数，可以在调用next函数时传递参数，这个参数会作为上一个yield语句的返回值；



#### （4）return函数

​	return函数也支持给生成器函数传递参数，但是return传值后这个生成器函数就会结束，之后调用next不会继续生成值了;

```js
function* foo(name) {
  const name2 = yield "aaaa";
  const name3 = yield "bbbb";
  yield "cccc";
  return undefined;
}
const generator = foo("next1");

//generator.return提前结束函数
console.log(generator.next()); //{value:'aaaa',done:false}
console.log(generator.return("next2")); //{value:'next2',done:true}
console.log(generator.next("next3")); //{value:'undefined',done:true}
console.log(generator.next("next4")); //{value:'undefined',done:true}
```



#### （5）throw函数

​	生成器函数内部支持抛出异常并在外部捕获，在catch语句中不能继续yield新值，只能在catch语句外部使用yield继续执行生成器函数；

```js
function* foo(name1) {
  const name2 = yield "aaaa";
  const name3 = yield "bbbb";
  // return "bbbb"
  yield "cccc";
  return undefined;
}
const generator = foo("next1");

//generator.throw向函数抛出一个异常
console.log(generator.next()); //{value:'aaaa',done:false}
console.log(generator.throw(new Error("throw error"))); //报错，thorw error
//不执行
console.log(generator.next("next3"));
console.log(generator.next("next4"));
```



#### （6）生成替代迭代器

生成器是特殊的迭代器，可以使用生成器来替代迭代器

```js
const names = ["zhang", "yu", "chen"];
//生成器函数
function* createGenerator(arr) {
  for (let i = 0; i < arr.length; i++) {
    yield arr[i];
  }
}
const range = createGenerator(names);
console.log(range.next());
console.log(range.next());
console.log(range.next());
console.log(range.next());

//使用语法糖版本
function* foo(arr) {
  yield* arr;
}
const func = foo(names);
console.log(func.next());
console.log(func.next());
console.log(func.next());
console.log(func.next());
```

**`yield*`提供语法糖写法，生成一个可迭代对象，它内部会依次迭代其中的每一个值；**



### 42、自定义类的生成器实现

```js
//使用yield语法糖
class PersonNew {
  constructor(name, age, height, friend) {
    this.name = name;
    this.age = age;
    this.height = height;
    this.friend = friend;
  }
  *[Symbol.iterator]() {
    yield* this.friend;
  }
}

const p = new PersonNew("zhang", 25, 18, ["zhangge", "yuge", "chenge"]);
for (let item of p) {
  console.log(item);
}
const personIterator = p[Symbol.iterator]();
console.log(personIterator.next());
console.log(personIterator.next());
console.log(personIterator.next());
console.log(personIterator.next());
```



### 43、异步请求：生成器和async/await方法

```js
//创建一个模拟异步的请求
function requestData(url){
  return new Promise((resolve,reject) => {
   	setTimeout(() => {
      resolve(url);
    }, 2000)
  })
}
```



#### （1）普通函数异步请求

```js
function getData() {
  requestData("zhang")
    .then((res1) => {
    console.log("结果一", res1);
    return requestData(res1 + "yu");
  })
    .then((res2) => {
    console.log("结果二", res2);
    return requestData(res2 + "chen");
  })
    .then((res3) => {
    console.log("结果三", res3);
  });
}
getData()
```



#### （2）生成器函数异步请求

```js
function* getData() {
  const res1 = yield requestData("zhang");
  console.log("结果一", res1);
  const res2 = yield requestData(res1 + "yu");
  console.log("结果二", res2);
  const res3 = yield requestData(res2 + "chen");
  console.log("结果三", res3);
}
//执行方法1:有几层写几层
const generator = getData();
generator.next().value.then((res1) => {
  generator.next(res1).value.then((res2) => {
    generator.next(res2).value.then((res3) => {
      generator.next(res3);
    });
  });
});
//执行方法2:递归执行
function execGenFn(genFn){
  const generator = genFn();
  function exex(params){
    const result = generator.next(params);
    if(result.done) return
    result.value.then((res) => {
      exec(res)
    })
  }
  exec();
}
execGenFn(getData);
```



#### （3）async/await异步请求

```js
async function getData() {
  const res1 = await requestData("zhang");
  console.log("结果一", res1);
  const res2 = await requestData(res1 + "yu");
  console.log("结果二", res2);
  const res3 = await requestData(res2 + "chen");
  console.log("结果三", res3);
}
getData();
```



### 43、async和await

#### （1）async

​	async**声明一个异步函数**，其内部代码默认情况下会被同步执行。

#### （2）await

​	await关键字后通常**跟一个Promise表达式**，await会等到Promise的状态变成fulfilled状态后继续执行异步函数。

- 如果await后面是<font color=deepred>普通值</font>，就<font color=deepred>直接返回</font>；
- 如果await后面是一个<font color=deepred>thenable的对象</font>，那么会根据对象的<font color=deepred>then方法调用</font>来决定后续的值；
- 如果await后面的表达式，返回的Promise表达式是<font color=deepred>reject的状态</font>，那么会将这个reject结果直接作为<font color=deepred>函数的Promise的reject值</font>；

#### （3）补充

​	**<font color=red>await关键字后面的表达式之后的代码是异步微任务</font>**



### 44、异常处理

#### （1）处理方法

使用throw关键字抛出异常，后面的语句就不会执行。

```js
//常用的方式就是封装一个异常
class CustomError {
  constructor(message,code){
    this.message = message;
    this.code = code;
  }
}
//使用自定义封装的异常
function foo(){
  throw new CustomError("错误信息",500)
}
//使用默认的异常处理
throw new Error("我是错误信息")//这里Error是JS提供的类，可直接创建使用
throw {errMessage:'我是错误信息',errCode:500}
```



#### （2）捕获方式

​	使用try catch进行捕获，如果有必须要执行的代码，使用finally执行（try和finally都有返回值，使用finally的）。



### 45、浏览器存储

#### （1）localStorage

​	本地存储，提供的是一种永久性的存储方法，在关闭掉网页重新打开时，存储的内容依然保留；



#### （2）sessionStorage

​	会话存储，提供的是本次会话的存储，在关闭掉会话时，存储的内容会被清除；



#### （3）cookie

​	浏览器存储，存储时间久，大小约4kb，手动设置有效期和失效时间，和请求一起发送，如果过大会带来性能问题；



#### （4）区别

| 特性           | cookie                                                       | localStorage             | seesionStorage                               |
| -------------- | ------------------------------------------------------------ | ------------------------ | -------------------------------------------- |
| 有效期         | 一般由服务器生成，可设置失效时间。如果在浏览器端生成Cookie，默认是关闭浏览器后失效 | 除非被清除，否则永久保存 | 仅在当前会话下有效，关闭页面或浏览器后被清除 |
| 存储大小       | 4kb                                                          | 5mb                      | 5mb                                          |
| 与请求一起发送 | 每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题 | 不参与和服务器的通信     | 不参与和服务器的通信                         |
| 作用域         | 同源窗口中是共享的                                           | 同源窗口中是共享的       | 同源窗口中是不共享的                         |
| 存储位置       | 浏览器和服务器                                               | 浏览器                   | 浏览器                                       |



#### （5）常用封装

```js
class Cache {
  constructor(isLocal = true) {
    this.storage = isLocal?localStorage:sessionStorage;
  }

  setCache(key, value) {
    if (!value) {
      throw new Error("value必须设置值");
    }
    if (value) {
      this.storage.setItem(key, JSON.stringify(value));
    }
  }

  getCache(key) {
    const result = this.storage.getItem(key);
    if (result) {
      return JSON.parse(result);
    }
  }

  removeCache(key) {
    this.storage.removeItem(key);
  }

  clear() {
    this.storage.clear();
  }
}

const localCache = new Cache();
const sessionCache = new Cache(false);
```



### 46、防抖和节流

#### （1）防抖

高频触发事件，单位时间内只响应最后一次，且每次触发时间都会重新计算。

```js
//手写基础版
function debounce(fn, delay) {
  let timer = null;
  const _debounce = function (...args) {
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      fn.apply(this, args);
      timer = null;
    }, delay);
  };
  return _debounce;
}
```



#### （2）节流

高频触发事件，单位时间内只响应一次。

```js
//手写基础版
function throttle(fn, delay) {
  let mark = true;
  const _throttle = function (...args) {
    if (!mark) { return }
    mark = false;
    setTimeout(() => {
      fn.apply(this, args);
      mark = true
    }, delay);
  };
  return _throttle;
}

//手写基础版时间戳形式
function throttle(fn,delay){
  let startTime = 0;
  const _throttle = function(...args){
    const nowTime = new Date().getTime();
    const waitTime = delay - (nowTime - startTime);
    if(waitTime <= 0){
      fn.apply(this,args)
      startTime = nowTime;
    }
  }
  return _throttle
}
```



### 47、深浅拷贝

#### （1）浅拷贝

```js
//结构赋值
const obj1 = {...obj}
//assugn
const obj2 = Object.assign({},obj)
```

#### （2）深拷贝

1. 简单版

   ```js
   //1.判断对象函数
   function isObjcet(obj) {
     const type = typeof obj;
     return type !== null && (type === "object" || type === "function");
   }
   //2.深拷贝核心函数
   function deepCopy(obj) {
     if (!isObjcet(obj)) {
       return obj;
     }
   
     const newObj = Array.isArray(obj) ? [] : {};
     for (const key in obj) {
       newObj[key] = deepCopy(obj[key]);
     }
   
     return newObj;
   }
   ```

   

2. 完整版

   ```js
   //1.判断对象函数
   function isObjcet(obj) {
     const type = typeof obj;
     return type !== null && (type === "object" || type === "function");
   }
   
   //2.深拷贝函数完整版（拷贝对象、数组、set、symbol、循环引用）
   function deepCopy(originValue, map = new WeakMap()) {
     // 0.如果值是Symbol的类型
     if (typeof originValue === "symbol") {
       return Symbol(originValue.description)
     }
   
     // 1.如果是原始类型, 直接返回
     if (!isObject(originValue)) {
       return originValue
     }
   
     // 2.如果是set类型
     if (originValue instanceof Set) {
       const newSet = new Set()
       for (const setItem of originValue) {
         newSet.add(deepCopy(setItem))
       }
       return newSet
     }
   
     // 3.如果是函数function类型, 不需要进行深拷贝
     if (typeof originValue === "function") {
       return originValue
     }
   
     // 4.如果是对象类型, 才需要创建对象
     if (map.get(originValue)) {
       return map.get(originValue)
     }
     const newObj = Array.isArray(originValue) ? []: {}
     map.set(originValue, newObj)
     // 遍历普通的key
     for (const key in originValue) {
       newObj[key] = deepCopy(originValue[key], map);
     }
     // 单独遍历symbol
     const symbolKeys = Object.getOwnPropertySymbols(originValue)
     for (const symbolKey of symbolKeys) {
       newObj[Symbol(symbolKey.description)] = deepCopy(originValue[symbolKey], map)
     }
   
     return newObj
   }
   ```

   

### 48、事件总线

自定义事件总线属于一种观察者模式，包括三个角色

1. 发布者：发出事件event
2. 订阅者：订阅事件，并会进行响应
3. 事件总线：事件中台

```js
//事件总线对象
class YCEventBus {
  constructor() {
    this.eventMap = {};
  }
	//监听事件
  on(eventName, eventFn) {
    let eventFns = this.eventMap[eventName];
    if (!eventFns) {
      eventFns = [];
      this.eventMap[eventName] = eventFns;
    }
    eventFns.push(eventFn);
  }
	//取消监听
  off(eventName, eventFn) {
    let eventFns = this.eventMap[eventName];
    if (!eventFns) return;
    for (let i = 0; i < eventFns.length; i++) {
      const fn = eventFns[i];
      if (fn === eventFn) {
        eventFns.splice(i, 1);
        break;
      }
    }
    if (eventFns.length === 0) {
      delete this.eventMap[eventName];
    }
  }
	//发射事件
  emit(eventName, ...args) {
    let eventFns = this.eventMap[eventName];
    if (!eventFns) return;
    eventFns.forEach((fn) => {
      fn(...args);
    });
  }
}
const eventBus = new YCEventBus()
//使用时通过on绑定事件名和事件执行函数
//一旦触发具体事件名那么绑定的所有执行函数都会执行
```



### 48、HTTP

​	超文本传输协议(缩写:HTTP)是一种用于分布式、协作式和超媒体信息系统的<font color=red>**应用层**协议</font>;

#### （1）HTTP版本

- HTTP/0.9
  - 发布于1991年; 
  - 只支持GET请求方法获取文本数据，当时主要是为了获取HTML页面内容;
- HTTP/1.0
  - 发布于1996年
  - 支持POST、HEAD等请求方法，支持请求头、响应头等，支持更多种数据类型(不再局限于文本数据)
  - 但是浏览器的每次请求都需要与服务器建立一个TCP连接，请求处理完成后立即断开TCP连接，每次建立连接增加了性能损耗
- HTTP/1.1（目前使用最广泛的版本）
  - 发布于1997年
  - 增加了PUT、DELETE等请求方法
  - 采用持久连接(Connection: keep-alive)，多个请求可以共用同一个TCP连接
- HTTP/2.0
  - 发布于2015年
- HTTP/3.0
  - 发布于2018年



#### （2）HTTP请求方式

常见请求方式：

- <font color=red>**GET**</font>：GET 方法请求一个指定资源的表示形式，使用 GET 的请求应该只被用于获取数据。
- HEAD：HEAD 方法请求一个与 GET 请求的响应相同的响应，但没有响应体。
- <font color=red>**POST**</font>：POST 方法用于将实体提交到指定的资源。
- PUT：PUT 方法用请求有效载荷(payload)替换目标资源的所有当前表示; 全网课程 超低价格
- <font color=red>**DELETE**</font>：DELETE 方法删除指定的资源;
- <font color=red>**PATCH**</font>：PATCH 方法用于对资源应部分修改;
- CONNECT：CONNECT 方法建立一个到目标资源标识的服务器的隧道，通常用在代理服务器，网页开发很少用到。 TRACE:TRACE 方法沿着到目标资源的路径执行一个消息环回测试。



#### （3）手写ajax请求

```js
//1、创建ajax核心
let xmlhttp = new XMLHttpRequest();
//2、创建请求（get/post 请求地址 请求异步）
xmlhttp.open("get","my.php?user=982395099&psd=123456",true);
//3、发送请求参数，key=value形式的字符串，多个参数用&连接。
//其中get请求请求参数不能写在send里面，要写在请求地址后面，以？连接，send里传null
xmlhttp.send(null);
//如果请求方式为post，需要设置请求头
//xmlhttp.setRequestHeader("Content-Type","application/x-www-form-urlencoded")
//4、接收响应
xmlhttp.onreadystatechange=function(){
  //readyState (请求的状态) 1(尚未初始化) 2(正在请求) 3(正在响应) 4(响应完毕)
  // status(服务端返回的状态码) 404 500 200(ok) 301 304
  if(xmlhttp.readyState==4&&xmlhttp.status==200){
    //responseText,可以获取服务器端返回的文本格式的数据
    var data=xmlhttp.responseText
    }
}

//实现原生ajax
const ajax = {
  get(url, fn) {
    const xmlhttp = new XMLHttpRequest();
    xmlhttp.open("GET", url, true);
    xmlhttp.onreadystatechange = function () {
      if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
        fn(xmlhttp.responseText);
      }
    };
    xmlhttp.send();
  },

  post(url, fn, data) {
    const xmlhttp = new XMLHttpRequest();
    xmlhttp.open("POST", url, true);
    xmlhttp.setRequestHeader(
      "Content-type",
      "application/x-www-form-urlencoded"
    );
    xmlhttp.onreadystatechange = function () {
      if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
        fn(xmlhttp.responseText);
      }
    };
    xmlhttp.send(data);
  },
};
```



#### （4）Fetch

​	Fetch可以看做是早期的XMLHttpRequest的替代方案，它提供了一种更加现代的处理方案，<font color=deepred>**返回值是一个Promise**，使用`.then`和`.catch`就可以拿结果</font>。

```js
//使用方式一:
fetch("请求地址").then(res => {
  // 1.获取到response
  const response = res
  // 2.获取具体的结果
  return response.json()
}).then(res => {
  console.log("res:", res)
}).catch(err => {
  console.log("err:", err)
})

//使用方式二:
async function getData() {
  const response = await fetch("请求地址")
  const res = await response.json()
  console.log("res:", res)
}
getData()
```





