## 属性

### 1、Object.prototype.constructor

​	返回创建实例对象的object构造函数的引用。此属性的值是对函数本身的引用，而不是一个包含函数名称字符串。

​	所有对象都会从它的原型上继承一个constructor属性。

```javascript
var o = {};
o.constructor === Object;	//true
var o = new Object;
o.constructor === Object;	//true
var a = [];
a.constructor === Array;	//true
var a = new Array;
a.constructor === Array // true
var n = new Number(3);
n.constructor === Number; // true
```



默认情况下，普通对象具有toString和valueOf方法：

- toString方法返回一个字符串`[object object]`;
- valueof方法返回对象自身。



## 方法

### 1、assign()方法

​	object方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。返回目标对象。

​	`Object.assign(target,source)`,target为目标对象，source为源对象。如果目标对象中的属性具有相同的键，则属性将被源对象中的属性覆盖。

```javascript
const target = {a : 1,b : 2};
const source = {b : 4,c : 5};
const finall = Object.assign(target,source);
console.log(target);	//{ a: 1, b: 4, c: 5 }
console.log(source);	//{ a: 1, b: 4, c: 5 }
```



### 2、create()方法

​	assign()方法创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`。

​	`Object.create(proto,propertiesObject)`，proto新创建对象的原型对象，propertiesObjec可选。该方法返回一个新对象，带着指定的原型对象和属性。



### 3、defineProperties()方法

​	defineProperties方法直接在一个对象上定义新的属性或者修改现有属性，并返回该对象。

​	`Object.defineProperties(obj, props)`，参数obj表示定义或修改属性的对象；参数props表示要定义其可枚举属性或修改的属性描述符的对象。

```javascript
var obj = {};
Object.defineProperties(obj, {
  'property1': {
    value: true,
    writable: true
  },
  'property2': {
    value: 'Hello',
    writable: false
  }
});
console.log(obj);	//{property1:true,property2:"Hello"}
```



### 4、delete操作符

​	delete操作符用于删除对象的某个属性，如果没有指向这个属性的引用，那它最终会被释放。

```javascript
const Employee = {
  firstname: 'John',
  lastname: 'Doe'
};
console.log(Employee.firstname);	//"John"
delete Employee.firstname;
console.log(Employee.firstname);	//undefined
console.log(Employee)	//{lastname: 'Doe'}
```



### 5、Object.defineProperty()方法

​	Object.defineProperty()方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

​	默认情况下，使用Object.defineProperty()添加的属性值是**不可修改**（immutable）的。

```javascript
const object1 = {};
Object.defineProperty(object1, 'property1', {
  value: 42,
  writable: false
});
object1.property1 = 77;
// throws an error in strict mode
console.log(object1.property1);	//42
```



### 6、Object.entries()方法

​	Object.entries()方法返回一个给定对象自身可枚举属性的键值对数组，排列与使用for...in循环遍历该对象时返回的顺序一致（区别在于 for-in 循环还会枚举原型链中的属性）。

```javascript
const object1 = {
  a: 'somestring',
  b: 42
};
for (const [key, value] of Object.entries(object1)) {
  console.log(`${key}: ${value}`);
}
// expected output:
// "a: somestring"
// "b: 42"
```



### 7、Object.fromEntries()方法

​	Object.fromEntries()方法把键值对列表转换为一个对象。

```javascript
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
]);
const obj = Object.fromEntries(entries);
console.log(obj);	//{ foo: "bar", baz: 42 }
```



### 8、Object.getPrototypeOf()方法

​	Object.getPrototypeOf()方法返回指定对象的原型（内部[Prototype]属性的值]）。

```javascript
const prototype1 = {};
const object1 = Object.create(prototype1);
console.log(Object.getPrototypeOf(object1) === prototype1);	//true
```



### 9、Object.prototype.hasOwnProperty()方法

​	Object.prototype.hasOwnProperty()方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性(即是否具有指定的属性)。

```javascript
const object1 = {};
object1.property1 = 42;
console.log(object1.hasOwnProperty('property1'));	//true
console.log(object1.hasOwnProperty('toString'));	//false
```



### 10、Object.is()方法

​	Object.is()方法判断两个值是否为同一个值。`Object.is(value1, value2);`参数value为被比较的第一个值，value2为被比较的第二个值。

⚠️如果满足以下条件则两个值相等:

- 都是undefined；
- 都是 null；
- 都是 true或false；
- 都是相同长度的字符串且相同字符按相同顺序排列；
- 都是相同对象（意味着每个对象有同一个引用）；
- 都是数字且都是+0、-0、NaN、都是非零而且非NaN且为同一个值；

 与==不同，不会进行强制类型转换；与===不同，将数字 -0和 +0视为相等，而将Number.NaN与NaN视为不相等。

```javascript
Object.is('foo', 'foo');     // true
Object.is(window, window);   // true
Object.is('foo', 'bar');     // false
Object.is([], []);           // false
```



### 11、Object.keys()方法

​	Object.keys() 方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致 。

```javascript
var arr = ['a', 'b', 'c'];
console.log(Object.keys(arr)); //['0', '1', '2']
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.keys(obj)); //['0', '1', '2']
var anObj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.keys(anObj)); //['2', '7', '100']
```



### 12、Object.prototype.toString()方法

​	Object.prototype.toString()方法返回一个表示该对象的字符串。还可以通过toString()来获取每个对象的类型。

```javascript
var toString = Object.prototype.toString;
toString.call(new Date); // [object Date]
toString.call(new String); // [object String]
toString.call(Math); // [object Math]
toString.call(undefined); // [object Undefined]
toString.call(null); // [object Null]
```



### 13、Object.prototype.valueOf()方法

​	Object.prototype.valueOf()方法返回指定对象的原始值。

<center>不同类型对象的valueOf()方法的返回值<center>

| **对象** | **返回值**                                               |
| :------- | :------------------------------------------------------- |
| Array    | 返回数组对象本身。                                       |
| Boolean  | 布尔值。                                                 |
| Date     | 存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。 |
| Function | 函数本身。                                               |
| Number   | 数字值。                                                 |
| Object   | 对象本身。这是默认情况。                                 |
| String   | 字符串值。                                               |
|          | Math 和 Error 对象没有 valueOf 方法。                    |

```javascript
// Array：返回数组对象本身
var array = ["ABC", true, 12, -5];
console.log(array.valueOf() === array);   // true

// Date：当前时间距1970年1月1日午夜的毫秒数
var date = new Date(2013, 7, 18, 23, 11, 59, 230);
console.log(date.valueOf());   // 1376838719230

// Number：返回数字值
var num =  15.26540;
console.log(num.valueOf());   // 15.2654

// 布尔：返回布尔值true或false
var bool = true;
console.log(bool.valueOf() === bool);   // true
// new一个Boolean对象
var newBool = new Boolean(true);
// valueOf()返回的是true，两者的值相等
console.log(newBool.valueOf() == newBool);   // true
// 但是不全等，两者类型不相等，前者是boolean类型，后者是object类型
console.log(newBool.valueOf() === newBool);   // false

// Function：返回函数本身
function foo(){}
console.log( foo.valueOf() === foo );   // true
var foo2 =  new Function("x", "y", "return x + y;");
console.log( foo2.valueOf() );
/*
ƒ anonymous(x,y
) {
return x + y;
}
*/

// Object：返回对象本身
var obj = {name: "张三", age: 18};
console.log( obj.valueOf() === obj );   // true

// String：返回字符串值
var str = "http://www.xyz.com";
console.log( str.valueOf() === str );   // true
// new一个字符串对象
var str2 = new String("http://www.xyz.com");
// 两者的值相等，但不全等，因为类型不同，前者为string类型，后者为object类型
console.log( str2.valueOf() === str2 );   // false
```



### 14、Object.values()方法

​	Object.values()方法返回一个给定对象自身的所有可枚举属性值的数组，值的顺序与使用for...in循环的顺序相同 ( 区别在于 for-in 循环枚举原型链中的属性 )。

```javascript
var obj = { foo: 'bar', baz: 42 };
console.log(Object.values(obj)); // ['bar', 42]
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.values(obj)); // ['a', 'b', 'c']
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.values(an_obj)); // ['b', 'c', 'a']
```



## 对象的遍历

### 1、方法一

```javascript
let user = {name:"zhang",age:30};
for (let value of Object.values(user)){
  console.log(value);	//zhang,30
}
//Object.values遍历对象的值；
//Object.keys遍历对象的键；
```



### 2、方法二

```javascript
let user = {name:"zhang",age:30};
for (let i in user){
  console.log(user[i]);	//zhang,30
}
```

