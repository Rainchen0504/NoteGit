## 1、JS数据类型和存储

Javascript一共有**8种数据类型**，包含7种基本数据类型和1种引用数据类型；

### （1）基本数据类型

```js
String、Number、Boolean、Null、undefined、Symbol、BigInt
```

也称为原始数据类型，直接存储在栈中，占据空间小，大小固定，被频繁使用；



### （2）引用数据类型

```js
Object（包含function、Array、Date）
```

​	本质上是一组无序的名值对组成的，同时存储在栈和堆中，占据空间大，大小不固定；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。



## 2、判断数据类型的方法

### （1）typeof

- **概要**：typeof操作符返回一个字符串，表示未经计算的操作数类型；

- **说明**：可以判断8种类型：`String、Boolean、Number、undefined、Symbol、BigInt、Object、Function`，其wpw中`typeof null === 'object'、typeof NaN === number`；
- **优点**：除了null，其他基本数据类型都能判断；
- **缺点**：function之外的其他引用数据类型，基础数据类型null，构造函数创建的字符串、数字、布尔值等都被创建为object；

```js
typeof(1);		// number
typeof(NaN);	// number
typeof(true);	// boolean
typeof('');	    // string
typeof(undefined);	// undefined
typeof(13n);	// bigint
typeof(Symbol(13));  	// symbol
typeof({name:"chenge"});	// object
typeof([1, 2, 3]);	// object
typeof(null);	// object
let func1 = function(){
  console.log('hello');
}
typeof(func1);	// function
```



### （2）instanceof

- **概要**：返回一个布尔值，表示一个对象是否为某个构造函数的实例；
- **说明**：判断构造函数的 prototype 属性是否出现在某个实例对象的原型链上，一般用来判断引用数据类型；
- **优点**：能够监测出引用数据类型；
- **缺点**：无法判断基础数据类型；

```js
var arr1 = new Array(2)
arr1 instanceof Array  // true
arr1 instanceof Object  // true
var str1 = new String("123")
str1 instanceof String  // true
str1 instanceof Object  // true
var str2 = '1234'
str2 instanceof String // false
let num1 = new Number('123');
num1 instanceof Number;	// true
num1 instanceof Object;	// true
let num2 = 13;
num2 instanceof Number;	// false
function A(name, age){
  this.name = name;
  this.age = age;
}
let p1 = new A('ostuthere', 18);
p1 instanceof A;		// true
p1 instanceof Object;	// true
// A.prototype有可能被改变，改变后的 prototype 不一定会出现在 p1 的原型链上
A.prototype = {
  sayHi: function(){
    console.log('hi');
  }
}
p1 instanceof A;	// false
```



### （3）Object.prototype.toString.call()

- **概要**：返回`[object type]`，type是调用者的数据类型；
- **优点**：能够检测出所有的数据类型；
- **缺点**：不兼容IE6以前版本的浏览器；

```js
Object.prototype.toString.call(1); // [object Number]
Object.prototype.toString.call(true); // [object Boolean]
Object.prototype.toString.call('13');	// [object String]
Object.prototype.toString.call(undefined);	// [object Undefined]
Object.prototype.toString.call(null);	// [object Null]
Object.prototype.toString.call(Symbol(13));	// [object Symbol]
Object.prototype.toString.call(13n);	// [object BigInt]
Object.prototype.toString.call([1,2,3]);	// [object Array]
Object.prototype.toString.call(new Function());	// [object Function]
Object.prototype.toString.call(new Date());	// [object Date]
Object.prototype.toString.call(new RegExp());	// [object RegExp]
Object.prototype.toString.call(new Error());	// [object Error]
Object.prototype.toString.call(window) ;	// [object global] window是全局对象global的引用
```



## 3、判断对象是否存在属性

### （1）Reflect.has()

检查一个对象是否有某个属性，包含原型上的属性；

```js
Reflect.has({name: "zhang"}, "name") // true
Reflect.has({name: "zhang"}, "age") // false
```



### （2）Object.hasOwn()

用于替代`Object.prototype.hasOwnProperty`的方法，如果指定的对象自身有指定的属性就返回true。如果属性是继承的或不存在就返回false；

```js
const object1 = {prop: 'exists'};
console.log(Object.hasOwn(object1, 'prop'));
// Expected output: true
console.log(Object.hasOwn(object1, 'toString'));
// Expected output: false
```



### （3）Object.prototype.hasOwnProperty()

返回一个布尔值，表示对象自有属性（非继承）中是否有指定属性；

```js
const object1 = {};
object1.property1 = 42;
console.log(object1.hasOwnProperty('property1'));
// Expected output: true
console.log(object1.hasOwnProperty('toString'));
// Expected output: false
```



### （4）in操作符

返回一个布尔值，表示指定的属性是否在指定对象的原型链中；

```js
const car = { make: 'Honda', model: 'Accord' };
console.log('make' in car);
// Expected output: true
delete car.make;
if ('make' in car === false) { //此处没有make属性结果为false
  car.make = 'Suzuki';
}
console.log(car.make);
// Expected output: "Suzuki"
```



## 4、作用域、作用域链和执行上下文

### （1）作用域

​	作用域是函数和变量的可访问范围，控制函数、变量的可见性和生命周期。分为全局作用域、函数作用域和块作用域。

- 全局作用域

  全局作用域中的对象在全局任何地方都能访问，常用的例如window对象；

- 函数作用域

  函数作用域中的对象只能在函数内部访问，函数执行结束后就会被销毁；

- 块作用域

  ES6新增块作用域，用一对大括号包裹的一段代码（循环语句、判断语句、函数）甚至单独的一个{}都可以被看作是一个块作用域；



### （2）作用域链

​	每个执行上下文的变量环境中，都包含一个外部引用，指向外部的执行上下文，当使用外部变量时，查找变量的链式关系就是作用域链。JS执行过程中，作用域链是由词法作用决定的，而词法作用域是**函数声明位置决定的**，是静态作用域，代码编译阶段就决定好的，和函数怎么调用没关系。



### （3）执行上下文

​	执行上下文是代码运行时产生的执行环境，分为全局执行上下文和函数执行上下文。执行上下文包含变量环境和词法环境。



## 5、this

**<font color=pink>this是和执行上下文绑定的</font>**，每个执行上下文都有一个this。

### （1）全局执行上下文

指向window对象



### （2）函数执行上下文

#### 1、普通函数调用

- 独立调用指向全局变量window；
- 对象调用内部方法指向对象本身；



#### 2、箭头函数调用

指向包裹箭头函数的第一个普通函数；



#### 3、call/apply/bind

指向绑定的对象；



#### 4、构造函数调用

指向构造函数创建出的实例；

构造函数实例化四步过程：

1. 创建一个空对象；
2. 将新对象的隐式原型`__proto__`指向构造函数的原型对象`prototype`；
3. 使用`CreateObj.call()`方法，把创建的空对象作为call的参数改变this指向，给对象添加属性和方法；
4. 返回新创建的对象；

```js
function A () {
  this.name = "A"
}

// 使用new关键字实例化对象
function newA () {
  const obj = {}
  obj.__proto__ = A.prototype
  A.call(obj)
  return obj
}
```

```js
// new命令的内部简化流程
function _new () {
  // 将arguments对象转为数组
  var args = [].slice.call(arguments);
  // 取出构造函数
  var constructor = args.shift();
  // 创建一个空对象，继承构造函数的 prototype 属性
  var context = Object.create(constructor.prototype);
  // 执行构造函数
  var result = constructor.apply(context, args);
  // 如果返回结果是对象，就直接返回，否则返回 context 对象
  return (typeof result === 'object' && result != null) ? result : context;
}
```



## 6、原型和原型链

### （1）原型

​	JS中使用构造函数新建对象，每个构造函数内部都有一个`prototype`属性，这个属性是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。

当使用构造函数新建一个对象后，这个对象内部包含一个指针，这个指针指向构造函数的`prototype`属性对应的值。在ES5中这个指针被称为对象的原型，使用`Object.getPrototypeOf()`方法可以获取对象的原型（浏览器实现了`_proto_`属性用来访问对象的原型，但是该方法不规范，不建议使用）。



### （2）原型链

​	当访问一个对象的属性时，如果该属性不在这个对象内部，会去原型对象中查找这个属性，原型对象也有自己的原型，这样一直查找下去就是原型链的概念。

​	所有的对象原型都可以追溯到`Object.prototype`，也就是所有对象都继承了Object.prototype的属性，因此所有对象都有`valueOf`和`toString`方法。

`Object.prototype`的原型是`null`,null没有任何属性喝方法，也没有自己的原型，原型链的尽头是`null`。



### （3）实现继承

使用class类，用`extends`关键字进行继承，或直接改变对象

```js
class Car {
  constructor(brand) {
    this.brand = brand;
  }
  showBrand() {
    console.log("the brand of car :>> ", this.brand);
  }
}

class ElectricCar extends Car {
  constructor(brand, duration) {
    super(brand);
    this.duration = duration;
  }
  showDuration() {
    console.log(
      `duration of this ${this.brand} ElectricCar :>> `,
      this.duration
    );
  }
}

ElectricCar.prototype.showOriginator = function (originator) {
  console.log(`originator of this ElectricCar :>> `, originator);
};

const tesla = new ElectricCar("tesla", "600km");
tesla.showBrand(); // the brand of car :>>  tesla
tesla.showDuration(); // duration of this tesla ElectricCar :>>  600km
console.log("tesla instanceof Car :>> ", tesla instanceof Car); // tesla instanceof Car :>>  true
console.log("tesla instanceof ElectricCar :>> ", tesla instanceof ElectricCar); // tesla instanceof ElectricCar :>>  true
console.log("tesla.__proto__ :>> ", tesla.__proto__); // tesla.__proto__ :>>  Car {}
console.log(
  "ElectricCar.prototype === tesla.__proto__  :>> ",
  ElectricCar.prototype === tesla.__proto__
); // ElectricCar.prototype === tesla.__proto__  :>>  true
tesla.showOriginator("Mask"); // originator of this  ElectricCar :>>  Mask

const bydCar = {
  brand: "比亚迪",
  duration: "666km",
};
bydCar.__proto__ = ElectricCar.prototype;

bydCar.showBrand(); //the brand of car :>>  比亚迪
bydCar.showDuration(); // duration of this 比亚迪 ElectricCar :>>  666km

```



## 7、闭包

​	在JS中，根据词法作用域的规则，内部函数总是能访问外部函数中声明的变量，当通过调用一个外部函数返回一个内部函数后，即使该外部函数已经执行结束，但是内部函数引用外部函数的变量依然保存在内存中，这些变量的集合称为闭包。

### （1）优点

内部变量是私有的，可以做到隔离作用域，保持数据不被污染；



### （2）缺点

垃圾回收机制无法清理到内部变量，会导致内存泄漏；



## 8、JS数组方法

```js
push // 向尾部添加，修改原数组
pop // 从尾部删除，修改原数组
shift // 从头部删除，修改原数组
unshift // 向头部添加，修改原数组
splice // 删除、添加元素，修改原数组
slice // 返回指定元素，不修改原数组
concat // 合并数组
join // 将数组所有元素合并放入字符串中
toString // 将数组转字符串
reverse // 反转数组
sort // 数组排序，从小到达
indexOf // 返回第一次出现的索引
lastIndexOf // 返回最后一次索引
forEach // 循环遍历数组
map // 遍历数组返回新数组
filter // 筛选符合条件的项
```



## 9、JS字符串方法

```js
chartAt	// 返回指定位置的字符串
chartCodeAt	//返回指定位置字符的unicode编码
concat	//拼接字符串
indexOf	//检索字符串
match	//找到正则匹配的字符串
repalce	//替换正则匹配的字符串
search	//检索正则匹配的值
slice	//提取字符串片段，返回新字符串，不修改原字符串
split	//拆分字符串为数组
toLocaleLowerCase	//把字符串转换为小写
toLocalUpperCase	//把字符串转换为大写
toLowerCase	//把字符串转换为小写
toUpperCase	//把字符串转换为大写
substr	//从起始索引提取指定数量字符
substring	//提取两个索引之间的字符
```



## 10、JS延迟加载

​	因为JS是单线程语言，所以加载JS时会阻塞页面的渲染和其他行为，所以为了提高页面加载的速度和性能，通常等页面加载完成后再加载JS。

延迟加载方法：

1. 将JS脚本放在文档底部，使得JS脚本尽可能在最后执行；
2. 动态创建DOM标签，对文档的加载事件进行监听，当文档加载完成后再动态创建JS标签引入JS脚本；
3. 给JS脚本添加defer修饰符，defer属性会让脚本的加载和文档的解析同步执行，然后当文档解析完成后再执行脚本文件，实现页面渲染不被阻塞；
4. 给JS脚本添加async修饰符，async属性会让脚本异步加载，当JS脚本加载完成后就执行，如果文档没解析完成还会造成阻塞现象；



## 11、JS模块化

服务端文件基本都存储在本地磁盘，读取速度非常快，采用<font color=pink>同步的加载方式</font>没有问题；

浏览器端文件的加载是使用网络请求，使用<font color=pink>异步的加载的方式</font>更合适；

### （1）CommonJS

通过`require`引入模块，通过`module.exports`定义模块的输出接口，是以同步的方式引入模块；



### （2）ESModule

使用`import`和`export`形式导入和导出模块，是以异步的方式加载模块；



### （3）AMD

采用异步加载的方式加载模块，模块的加载不影响后面语句的执行，所有依赖该模块的语句都定义在一个回调函数中，等加载完成后再执行回调函数，require.js实现了AMD规范；



### （4）CMD

和AMD一样都是为了解决异步模块加载问题；



## 12、CommonJS和ESModule模块化差异

### （1）语法上

`CommonJS`使用`module.exports={}`导出一个模块对象，`require('path')`引入模块对象；

`ESModule`使用的是`export`导出指定数据，`import`引入具体数据；



### （2）值类型上

`CommonJS`输出的是值的拷贝，输出后模块内部变化不会影响这个值；

`ESModule`输出的是值的引用，且不会缓存值；



### （3）加载时机上

`CommonJS`模块是运行时加载，就是对象，输入时先加载整个模块生成一个对象，然后从对象上读取；

`ESModule`是编译时加载，不是对象，可以通过`export`命令显式指定输出代码，可以指定加载某个输出值；



## 13、AMD和CMD模块化差异

### （1）模块定义时对依赖的处理不同

1. AMD推崇依赖前置，在定义模块时候就要声明其依赖的模块；
2. CMD推崇就近依赖，只有在用到某个模块时再去`require`；



### （2）对依赖模块执行时机不同

AMD和CMD对于模块的加载方式都是**异步加载**

1. AMD是预加载，在依赖模块加载完成后就直接执行依赖模块，依赖模块执行顺序和书写顺序不一定一致；
2. CMD是懒加载，在依赖模块加载完成后并不执行只进行下载，等到所有依赖模块都加载好后进入回调函数逻辑，遇到`require`语句才执行对应的模块，执行顺序和书写顺序一致；

```js
// CMD
define(function (require, exports, module) {
	var a = require("./a");
  a.doSomething();
  //省略逻辑
  var b = require("./b");
  b.doSomething();
})
```

```js
// AMD
define(["./a", "./b"], function(a,b) {
  // 依赖必须一开始就写好
  a.doSomething()
  //省略逻辑
  b.doSomething()
  // ...
})
```



## 14、事件循环

### （1）任务队列

​	JS是单线程的（JS引擎在执行代码时只有一个主线程，每次只能干一件事），同时还是非阻塞运行的（执行异步任务时，会先将异步任务挂起，等待异步返回结果再执行回调）。

​	在JS代码执行时，会将对象存在堆中，在栈中存放一些基础类型变量和对象的指针，对于普通函数就是正常的入栈出栈即可，涉及到异步任务时，JS执行会把对应的任务放到事件队列中（微任务、宏任务队列）。



### （2）事件分类

<font color=pink>**JS分为同步任务和异步任务，异步任务又分宏任务和微任务**</font>。

- 宏任务：整体代码script、setTimeout、setInterval、setImmediate、i/o操作（输入输出，比如读取文件操作、网络请求）、ui render（dom渲染，即更改代码重新渲染dom的过程）、异步ajax等
- 微任务：Promise（then、catch、finally）、async/await、process.nextTick、Object.observe(⽤来实时监测js中对象的变化)、 MutationObserver(监听DOM树的变化)

![image-20230725102720487](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307251027261.png)



### （3）执行顺序

1. JS代码在执行过程中，先执行同步代码；
2. 遇到异步任务区分宏任务和微任务；
3. 任务为宏任务时加入到宏任务队列；
4. 任务为微任务时加入到微任务队列；
5. 当所有同步代码执行完毕后将微任务队列调入主线程执行；
6. 微任务执行完毕后将宏任务队列调入主线程执行；
7. 执行完所有任务后第一次事件循环结束；
8. 主线程会不断获取任务队列中的任务，也就是事件循环；



## 15、内存泄漏

### （1）造成泄漏原因

1. 没解除绑定事件；

   注册了事件监听器但没有正确解绑，会导致内存泄漏。比如当一个DOM元素被删除时，它仍然会保留对事件监听器的引用，如果没有解绑，事件监听器将无法被垃圾回收。

2. 意外的全局变量；

   JS如果定义了全局变量，会一直存在在内存中，直到页面关闭。

   ```js
   // 绑定在window对象上
   function fn(){ a = "message" }
   fn()
   function fm(){ this.a = "message" }
   fm()
   ```

3. 闭包；

   闭包可以让函数访问外部作用域中的值，然后将访问到的变量保存到内存中。

   ```js
   function fn(){
     var a = "hello"
     return function(){
       console.log(a)
     }
   }
   ```

4. 遗忘的定时器；

   在JS中使用setInterval()或setTimeout()函数时，必须保证不用时清除。

5. 循环引用；

   当两个或多个对象之间存在相互引用，并且没有被其他对象引用，就会发生循环引用。

6. 未清理DOM引用；

   虽然在某个地方删除了元素，但是对象中还存在对DOM的引用。

   ```js
   var elements = {
     btn: document.getElementById('btn'),
   }
   function doSomeThing() {
     elements.btn.click()
   }
   function removeBtn() {
     // 将body中的btn移除, 也就是移除 DOM树中的btn
     document.body.removeChild(document.getElementById('btn'))
     // 但是此时全局变量elements还是保留了对btn的引用, btn还是存在于内存中,不能被回收
   }
   ```

   

### （2）避免泄漏方法

1. 避免创建全局变量；
2. 手动删除失效的定时器和DOM；
3. 解绑不必要的事件监听；
4. 手动删除失效的DOM引用；
5. 解除闭包，将闭包变量置为null；
6. 使用严格模式，在JS文件头部或函数顶部加`use strict`；



## 16、ES6新增特性

- 块级作用域let、const
- 类class
- 箭头函数
- 模板字符串
- 对象解构赋值
- 对象字面量
- 拓展运算符
- 函数默认参数
- promise
- 模块化import/export
- Set和Map
- Proxy
- 生成器和迭代器
- 基本数据类型Symbol



## 17、箭头函数

不会创建自身的this，只会从上一级继承this，箭头函数this在定义时候就已经确认了，由词法作用域决定，不会被调用对象改变。





























































































































































## 8、web worker

### （1）说明

​	JS最初设计是运行在浏览器中的，为了防止多个线程同时操作DOM，带来渲染冲突问题，所以JS执行器被设计成单线程。当遇到需要大量计算的场景时（图像处理、视频解码等），JS线程会被长时间阻塞，甚至造成页面卡顿，影响用户体验。

​	`Web Worker` 是 HTML5 标准的一部分，允许在 js 主线程之外开辟新的 Worker 线程，并将一段 js 脚本运行其中，赋予开发者利用 js 操作多线程的能力。因为是独立的线程，Worker 线程与 js 主线程能够同时运行，互不阻塞。



### （2）使用限制

1. 通信限制：worker线程与主线程不在同一个上下文，不能直接通信，需要通过`postMessage`方法来通信；
2. 脚本限制：worker线程不能执行`alert`、`confirm`，但可以使用 `XMLHttpRequest` 对象发出ajax请求；
3. DOM操作限制：worker线程在与主线程的window不同的另一个全局上下文中运行，其中无法读取主线程所在网页的DOM对象，也不能获取 `document`、`window`等对象，但是可以获取`navigator`、`location(只读)`、`XMLHttpRequest`、`setTimeout`等浏览器API；
4. 文件限制：worker线程无法读取本地文件，它所加载的脚本必须来自网络，且需要与主线程的脚本同源；



### （3）使用方法

#### 3.1、创建worker

创建 worker 只需要通过 new 调用 Worker() 构造函数即可

```js
const worker = new Worker(path, options);
```

- path：有效的JS脚本地址，必须遵守同源策略；
- options：配置worker类型、凭证；



#### 3.2、数据传递

主线程与 worker 线程都是通过 postMessage 方法来发送消息，以及监听 message 事件来接收消息

```js
// 主线程
const myWorker = new Worker('/worker.js');
myWorker.addEventListener('message', e => {
  console.log(e.data)	// worker线程发送过来的消息
})
// 也可以使用这种写法
// myWorker.onmessage = e => {
//   console.log(e.data) // 接收消息
// }
myWorker.postMessage('发送消息')	// 向worker线程发送消息，对应worker线程中的e.data
```

```js
// worker线程
self.addEventListener('message', e => {
  console.log(e.data); // 主线程发送的消息
  self.postMessage('发送消息'); // 向主线程发送的消息
});
```



#### 3.3、关闭worker

worker 线程的关闭在主线程和 worker 线程都能进行操作

```js
// 主线程
const myWorker = new Worker("./worker.js")
myWorker.terminate() //关闭worker
```

```js
// worker线程
self.close()
```



## 14、addEventListener

语法：`addEventListener(event, function, useCapture)`

参数：

- event 指定事件名；
- function 指定要事件触发时执行的函数；
- useCapture 指定事件是否在捕获或冒泡阶段执行，true在捕获阶段处理，false在冒泡阶段处理；



## 15、constructor和instanceof

- constructor是一个对象属性，指向创建该对象的构造函数；

```js
function Person(name) {
  this.name = name;
}
const zhang = new Person();
console.log(zhang.constructor === Person); // true
```

- instanceof是JS运算符，用于检查对象是否是某个特定类型的实例；

```js
function Person(name) {
  this.name = name;
}
const zhang = new Person();
console.log(zhang instanceof Person); // true
```

