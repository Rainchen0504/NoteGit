## 1、判断对象是否存在属性

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



## 2、判断数据类型方法

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
