# 一、let和const命令

### （1）暂时性死区

​	ES6 明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。<font color=red>凡是在声明之前就使用这些变量，就会报错</font>。

本质上即：只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

### （2）const

​	const实际上并不是变量的值不得改动，而是变量指向的那个**内存地址所保存的数据**不得改动。

### （3）ES6声明变量

​	ES6在ES5的var和function基础上添加了let和const命令，还提供了import和class命令，共6种变量声明的方法。



# 二、变量的解构赋值

​	ES6允许从数组和对象中按照一定模式提取值，对变量进行赋值。

​	数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。



# 三、字符串扩展及新增方法

## 1、字符串可遍历

字符串可以被`for...of`循环遍历；



## 2、字符串包含判断

判断一个字符串是否包含在另一个字符串中：

- **includes()**：返回布尔值，表示是否找到了参数字符串；
- **startsWith()**：返回布尔值，表示参数字符串是否在原字符串的头部；
- **endsWith()**：返回布尔值，表示参数字符串是否在原字符串的尾部；

支持第二个参数，表示截止或者开始位置索引。



## 3、repeat方法

repeat方法返回新字符串，将目标字符重复n次。

```javascript
//次数为小数会向下取整；为负数会报错，为0或NaN会得到""，为字符串会转数字。
"字符串".repeat(重复次数)
```



## 4、replace方法

replace()方法只能替换第一个匹配，replaceAll()方法可以替换所有匹配项。



# 四、数值的扩展

## 1、进制转换

二进制前缀0B；八进制前缀0o；都可以通过Number("进制格式的数")方法转成十进制。



## 2、isFinite方法

检查一个数值是否为有限的，不是无限的Infinity；如果参数不是数值，一律返回false。



## 3、isNaN方法

检查一个值是否为NaN，如果参数不是NaN一律返回false。



## 4、isInterger方法

判断一个数是否为整数，不是一律返回false。



## 5、Math方法扩展

### （1）Math.trunc()

- `Math.trunc`方法用于去除一个数的小数部分，返回整数部分。
  - 非数值会先转换为数值再输出；
  - 空值和无法截取取整的值返回NaN；

### （2）Math.sign()

- `Math.sign`方法用来判断一个数是正数、负数、还是零。对于非数值，会先转换成数值。
  - 参数为正数，返回+1；
  - 参数为负数，返回-1；
  - 参数为0，返回0；
  - 参数为-0，返回-0；
  - 其他值，返回NaN

### （3）Math.cbrt()

​	用于计算一个数的立方根

### （4）BigInt数据类型

​	JS所有数字都保存成64位浮点数，因此限制了数值的精度只能到53个二进制位（16个十进制位），大于该范围整数是无法精确表示的，同时大于或等于2的1024次方的数值JS无法表示，返回Infinity。

​	因此JS引入了新的数据类型BigInt(大整数)，只用来表示整数，没有位数限制，任何位数的整数都可以精确表示。

```javascript
//为了和Number类型区别，BigInt类型必须加后缀n
1234n//bigInt
1234//普通整数
```



# 五、函数的扩展

## 1、函数参数默认值

```javascript
function point(x=0,y=0){
  this.x = x;
  this.y = y;
}
cosnt p = new Point();
p	//{x=0,y=0}
```

- 参数变量是默认声明的，不能使用let和const再次声明会报错；

- 使用参数默认值时，函数不能有同名参数，会报语法错误；
- 函数的length属性返回默认值的参数个数，指定默认值后length属性将失真；
- 设置参数默认值，函数初始化时参数会形成一个单独的作用域，初始化结束后作用域就会消失。

```javascript
let x = 1;
function f(y=x){
  let x = 2;
  console.log(y)
}
f()
//调用f函数，y=x形成单独的作用域，x没定义会指向外层全局x，函数内部的局部变量影响不到默认变量x
```



## 2、rest参数(...变量名)

取代arguments对象获取多余参数。rest参数搭配的变量是一个数组，将多余的参数放到数组中。

```javascript
function add(...val){
  console.log(val)//[2,5,6]
}
add(2,5,6)
```

- arguemnts对象<font color=red>**不是数组，是一个类似数组的对象**</font>。这个伪数组使用数组的方法前必须使用**`Array.from`**将其转为数组。
- rest参数是真正的数组，不存在转换的要求。

```javascript
//arguments变量的写法
function sortNumbers(){
  return Array.from(arguments).sort();
}
//rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();
```

rest参数之后不能再有其他参数，必须是最后一个参数，否则会报错。

同时函数的length属性不包括rest参数。



## 3、严格模式

函数内部可以设置严格模式，但是函数参数使用默认值、解构赋值、扩展运算符时使用严格模式会报错。

```javascript
// 报错写法
function doSomething(a, b = a) {
  'use strict';
}
const doSomething = function ({a, b}) {
  'use strict';
};
const doSomething = (...a) => {
  'use strict';
};
const obj = {
  doSomething({a, b}) {
    'use strict';
  }
};
```



## 4、箭头函数

注意点：

1. 没有自己的this，指向调用的外层this
2. 不可以当构造函数，不能使用new命令
3. 不可以使用arguments对象，可以使用rest参数代替
4. 不可以使用yield命令，不能用做Generator函数



# 六、数组的扩展

## 1、扩展运算符

三个点（...），类似于rest参数的逆运算，把一个数组转为逗号分隔的参数序列。

如果对一个空数组使用扩展运算符，不会产生任何影响。

```javascript
//扩展运算符应用
//（1）复制数组（深拷贝）
const a = [1,2];
const b = [...a];

//（2）合并数组(浅拷贝)
const a = [...a1,...a2];

//（3）解构赋值结合
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

//（4）字符串(将字符串转成数组)
[...'hello']
[ "h", "e", "l", "l", "o" ]

//（5）实现Iterator接口对象
任何定义遍历器(Iterator)接口的对象都可以使用扩展运算符转成真正的数组

//（6）Map和Set结构，Generator函数
let map = new Map([[1,'one'],[2,'two'],[3,'three']]);
let arr = [...map.keys()];
//[1, 2, 3]
let set = new Set([1,2,3,4,5,1,2])
let arr = [...set]
//[1, 2, 3, 4, 5]
```



## 2、Array.from()

将两类对象转成真正的数组：<font color=red>**类数组的对象和可遍历的对象**</font>（包括Set和Map）

实际应用中常见的类数组对象是DOM操作返回的NodeList集合以及函数内部的argument对象。

```javascript
let ps = document.querySelectorAll("p");
Array.from(ps);

function foo(){
  var args = Array.from(arguments);
}
```

Array.from()还可以接受一个函数作为第二个参数，可以对每个元素进行处理并放入返回的数组。



## 3、Array.of()

将一组值转换为数组，用来弥补构造函数`Array()`的不足。

```javascript
Array.of(3,11,5);	//[3,11,5]
```

基本上可以用来替代`Array()`或`new Array()`，不存在由于参数不同而导致的重载。

该方法总是返回参数值组成的数组，没参数就返回空数组。



## 4、find()和findIndex()

find方法找到第一个符合条件的数组成员，返回值为找到的元素对象，没找到就返回undefined。

findIndex方法第一个符合条件的数组成员，返回值为找到元素的次序，没找到就返回-1。



## 5、fill()

使用给定值填充数组，还可接受另外两个参数指定填充起始位置和结束位置。

如果填充的内容是对象，被赋值的是同一个内存地址，不是深拷贝。



## 6、遍历数组方法

- entries()方法：对键值对的遍历
- keys()方法：对键名的遍历
- values()方法：对键值的遍历

```javascript
let arr = ["zhang","yu","chen"];

//keys
for(let i of arr.keys()){
  console.log(i)
}
0	1	2

//values
for(let v of arr.values()){
  console.log(v)
}
zhang	yu chen

//entries
for(let [i,e] of arr.entries()){
  console.log(i,e)
}
0'zhang'  1'yu'  2'chen'
```



## 7、includes()

返回一个布尔值，表示数组中是否包含指定值，与字符串的includes方法类似。

接收第二个参数表示从哪一位开始查找。

```javascript
[1, 2, 3].includes(2)	// true
[NaN].includes(NaN)	//true
```

注意⚠️：

Map和Set数据结构有一个has方法

- Map结构的has方法，是用来<font color=red>查找键名的</font>

`Map.prototype.has(key);WeakMap.prototype.has(key)`

- Set结构的has方法，是用来<font color=red>查找值的</font>

`Set.prototype.has(value);WeakSet.prototype.has(value)`



## 8、flat()扁平化方法

将多维数组转成一维数组，即数组扁平化方法。

```javascript
[1, 2, [3, 4]].flat()	// [1, 2, 3, 4]
```

**默认情况下flat()方法只会扁平化一层**，多层嵌套情况下需将层数作为参数执行。

```javascript
[[1, 2],[2, 3, [5, [7]]]].flat(3);	
//[ 1, 2, 2, 3, 5, 7 ]
```

如果不知道多少层，<font color=deepred>使用`Infinity`关键字作为参数</font>。



## 9、at()

at方法接受一个整数作为参数，返回对应位置的元素，<font color=deepred>**支持负索引，作用于数组、字符串和类型数组**</font>。

```javascript
const arr = [1,2,3,4,5];
arr.at(2)	//3
arr.at(-2)	//4
```

如果参数位置超出范围返回`undefined`。



## 10、group()

分组函数接受三个参数，当前成员、当前成员索引、原数组

```javascript
const array = [1, 2, 3, 4, 5];
array.group((item, index, array) => {
  return item % 2 === 0 ? 'even': 'odd';
});
//返回值是一个对象：{ odd: [1, 3, 5], even: [2, 4] }
```

另外还可以使用对象作为分组的键名

```javascript
const arr = [1,2,3,4,5];
const odd = {odd:true};
const evem = {even:true};
arr.groupMap((item,index,array) => {
  return item % 2 === 0 ? even : odd;
})
//返回值是一个Mep结构：Map {{odd: true}:[1, 3, 5],{even: true}:[2,4]}
```



# 七、对象的扩展

## 1、可枚举性

对象的**每个属性都有一个描述对象**，用来控制属性的行为，可以使用`Object.getOwnPropertyDescriptor`方法获取。

```javascript
let obj = {foo:123};
Object.getOwnPropertyDescriptor(obj,"foo");
//{ value: 123, writable: true, enumerable: true, configurable: true }
```



## 2、属性遍历

ES6共有5中方法可以遍历对象的属性：

### （1）for...in

循环遍历对象自身的和继承的可枚举属性（除了Synbol属性），一般不用于遍历数组

### （2）Object.keys(obj)

返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名

### （3）Object.getOwnPropertyNames(obj)

返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名

### （4）Object.getOwnPropertySymbols(obj)

返回一个数组，包含对象自身的所有 Symbol 属性的键名

### （5）Reflect.ownKeys(obj)

返回一个数组，包含对象自身的（不含继承的）所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

- 上面的遍历方法遵守相同的属性排列规则：
  - 先遍历所有数值键，按照数值升序排列；
  - 遍历所有字符串键，按照加入时间升序排列；
  - 遍历所有 Symbol 键，按照加入时间升序排列；

```javascript
Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
//[ '2', '10', 'b', 'a', Symbol() ]
```



## 3、扩展运算符

### （1）解构赋值

​	从一个对象取值，相当于将目标对象自身的所有可遍历的、但尚未被读取的属性，分配到指定的对象上面。所有的键和值，都会拷贝到新对象上面。

​	**解构赋值的拷贝是浅拷贝**，即如果一个键的值是复合类型的值（数组、对象、函数）、那么解构赋值**拷贝的是这个值的引用**，而不是这个值的副本。

```javascript
let obj = { a: { b: 1 } };
let { ...x } = obj;
obj.a.b = 2;
x.a.b // 2
```

​	扩展运算符的解构赋值，不能复制继承自原型对象的属性。

```javascript
let o1 = { a: 1 };
let o2 = { b: 2 };
o2.__proto__ = o1;
let { ...o3 } = o2;
o3 // { b: 2 }
o3.a // undefined
```



## 4、对象新增方法

### （1）Object.is()

​	用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。

1. == 会进行强制类型转换；
2. === 比较NaN不等于自身，+0等于-0；

Object.is()解决以上问题，+0不等于-0，NaN等于NaN。



### （2）Object.assign()

​	用于对象的合并，将源对象的所有可枚举属性，复制到目标对象。第一个参数是目标对象，后面参数都是源对象。

​	源对象不是对象时会先转换成对象再进行合并；不能使用null和undefined作为目标对象，会报错。

```javascript
const target = { a: 1, b: 1 };
const source1 = { b: 2, c: 2 };
Object.assign(target, source1);
target // {a:1, b:2, c:2}
```

- assign方法执行的是浅拷贝，拷贝的是对象的引用；
- 同名属性会替换；
- 会把数组视为对象；
- 只能进行值的复制



### （3）Object.getOwnPropertyDescriptors() 

返回指定对象的所有自身属性（非继承属性）的描述对象。



### （4）`__proto__`属性，Object.setPrototypeOf()，Object.getPrototypeOf() 

<font color=deepred>Javascript语言的对象继承是通过原型链实现的</font>，ES6提供了更多操作原型链的方法。

- `__proto__`属性用来读取或设置当前对象的原型对象(prototype)。该属性使用的前提是浏览器支持，本质上是一个内部属性。`__proto__`调用的是`Object.prototype.__proto__`。

- `Object.setPrototypeOf`方法作用和`__proto__`相同，设置一个对象的原型对象。

```javascript
Object.setPrototypeOf(object, prototype)
```

- `Object.getPrototypeOf`方法用于读取一个对象的原型对象。

```javascript
Object.getPrototypeOf(obj);
```



### （5）Object.keys()，Object.values()，Object.entries()

#### 	1.Object.keys()

​	**返回一个数组**，成员是参数对象自身的（不含继承的）<font color=deepred>所有可遍历属性的**键名**</font>。

#### 	2.Object.values()

​	**返回一个数组**，成员是参数对象自身的（不含继承的）<font color=deepred>所有可遍历属性的**键值**</font>。

#### 	3.Object.entries()

​	**返回一个数组**，成员是参数对象自身的（不含继承的）<font color=deepred>所有可遍历属性的**键值对数组**</font>。



### （6）Object.fromEntries()

将一个键值对数组转为对象。

```javascript
Object.fromEntries([
  ['foo', 'bar'],
  ['baz', 42]
])
// { foo: "bar", baz: 42 }
适合将Map解构转成对象
```



# 八、运算符的扩展

## 1、指数运算符`**`

```javascript
2 ** 2	//4
2 ** 3  //8
```



## 2、链判断运算符`?.`

如果存在就继续执行，不存在返回undefined。

1. 短路机制。不满足条件就不再继续执行；

2. 括号影响。链判断运算符对圆括号外部没有影响，只对圆括号内部有影响；

3. 报错场合。

   ```javascript
   //构造函数中使用会报错
   new a?.()
   new a?.b()
   //运算符右侧有模板字符串报错
   a?.`{b}`
   a?.b`{c}`
   //运算符左侧是super报错
   super?.()
   super?.foo
   //链运算符用于赋值运算符左侧
   a?.b = c
   ```

4. 右侧不能为十进制数值。



## 3、null判断运算符`??`

​	行为类似`||`，但是只有运算符左侧的值为`null`或`undefined`时，才会返回右侧的值。

​	跟链判断运算符`?.`配合使用，为`null`或`undefined`的值设置默认值。

```javascript
const result = res.config?.setting ?? 100;
//如果res.config是null或者undefined就会返回默认值100；
```

**<font color=deepred>如果多个逻辑运算符(&&、||、??)一起使用，必须用括号表明优先级，否则会报错</font>**。



## 4、逻辑赋值运算符

先进行逻辑运算，然后根据结果再赋值运算。

```javascript
// 或赋值运算符
x ||= y
// 等同于（如果x不存在就设为y）
x || (x = y)

// 与赋值运算符
x &&= y
// 等同于
x && (x = y)

// Null 赋值运算符
x ??= y
// 等同于
x ?? (x = y)
```



# 九、Set和Map数据结构

## 1、Set

### （1）基本用法

类似于数组，成员都是唯一的，没有重复值。

**Set本身就是一个构造函数**，用来生成Set数据结构。

```javascript
//数组去重的方法
[...new Set(array)]
//也可以用来字符串去重
[...new Set('ababbc')].join('')	//"abc"
```



### （2）Set实例属性和方法

Set实例的方法有两大类：**<font color=deepred>操作方法和遍历方法</font>**。

##### 操作方法

- `Set.prototype.add(value)`：添加某个值，返回 Set 结构本身。
- `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `Set.prototype.clear()`：清除所有成员，没有返回值。

**<font color=red>Array.from方法可以将Set结构转成数组</font>**

```javascript
//数组去重方法
function delRepeat(arr){
  return Array.from(new Set(arr))
}
```



##### 遍历方法

- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员

`Set`的遍历顺序就是插入顺序,默认可遍历，默认遍历器生成的函数就是values方法，即可以直接使用for...of遍历Set。



## 2、Map

### （1）基本用法

​	JavaScript 的对象，本质上是**键值对的集合**（Hash 结构），但是传统上只能用字符串当作键。

​	**<font color=deepred>Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应</font>，是一种更完善的 Hash 结构实现**。

```javascript
//Map构造函数接受数组作为参数
const items = [
  ['name', '张三'],
  ['title', 'Author']
];
const map = new Map();
items.forEach(([key,value]) => map.set(key,value))
```

任何具有Iterator接口、且每个成员都是一个双元素的数组的数据结构都以当作Map构造函数的参数。`Set`和`Map`都可以用来生成新的 Map。

```javascript
//对同一个键多次赋值，后面的值将覆盖前面的值。
const map = new Map();
map.set(1,'aaa');
map.set(1,'bbb');
map.get(1)	//'bbb';

//读取一个未知的键，则返回undefined
new Map().get('asfddfsasadf')	//undefined

//只有对同一个对象的引用，Map 结构才将其视为同一个键。
const map = new Map();
map.set(['a'], 555);
map.get(['a']) // undefined
//出现上面问题的原因是 Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。
```



### （2）实例的属性和操作方法

##### 属性

- size 属性：返回 Map 结构的成员总数；



##### 操作方法

- `Map.prototype.set(key, value)`：设置键名`key`对应的键值为`value`，然后返回整个 Map 结构。如果`key`已经有值，则键值会被更新，否则就新生成该键；
- `Map.prototype.get(key)`：读取`key`对应的键值，如果找不到`key`，返回`undefined`；
- `Map.prototype.has(key)`：返回一个布尔值，表示某个键是否在当前 Map 对象之中；
- `Map.prototype.delete(key)`：删除某个键，返回`true`。如果删除失败，返回`false`；
- `Map.prototype.clear(key)`：清除所有成员，没有返回值；



##### 遍历方法

- `Map.prototype.keys()`：返回键名的遍历器。
- `Map.prototype.values()`：返回键值的遍历器。
- `Map.prototype.entries()`：返回所有成员的遍历器。
- `Map.prototype.forEach()`：遍历 Map 的所有成员。

**Map 的遍历顺序就是插入顺序**



### （3）与其他数据结构的互相转换 

#### 	1.Map转数组

**<font color=red>Map 结构转为数组结构，比较快速的方法是使用扩展运算符（`...`）</font>**

```javascript
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);
[...map.keys()]	// [1, 2, 3]
[...map.values()]	// ['one', 'two', 'three']
[...map.entries()]	// [[1,'one'], [2, 'two'], [3, 'three']]
[...map]	// [[1,'one'], [2, 'two'], [3, 'three']]
```



#### 	2.数组转Map

将数组传入 Map 构造函数，就可以转为 Map

```javascript
new Map([[true, 7],[{foo: 3}, ['abc']]])
// Map {
//   true => 7,
//   Object {foo: 3} => ['abc']
// }
```



#### 	3.Map转对象

如果所有 Map 的键都是字符串，它可以无损地转为对象；

如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名；

```javascript
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}
const myMap = new Map()
  .set('yes', true)
  .set('no', false);
strMapToObj(myMap)
// { yes: true, no: false }
```



#### 	4.对象转Map

对象转为 Map 可以通过`Object.entries()`

```javascript
let obj = {"a":1, "b":2};
let map = new Map(Object.entries(obj));

//手写转换函数
function objToStrMap(obj){
  let strMap = new Map();
  for(let k of Object.keys(obj)){
    strMap.set(k,obj[k]);
  }
  return strMap;
}
objToStrMap({yes: true, no: false})
// Map {"yes" => true, "no" => false}
```



# 十、Proxy

## 1、基本用法

​	用于修改某些操作的默认行为，属于一种"元编程"，对编程语言进行编程。

​	**<font color=deepred>在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，可以对外界的访问进行过滤和改写。</font>**

```javascript
var proxy = new Proxy(target, handler)
```

1. new Proxy()表示生成一个Proxy实例；
2. target表示要拦截的目标对象；
3. <font color=deepred>handler**也是对象**，指定拦截的行为</font>；

**注意⚠️：要使Proxy起作用，必须针对proxy实例进行操作，而不是针对目标对象进行操作。**

```javascript
var proxy = new Proxy({}, {
  get: function(target, propKey) {
    return 35;
  }
});
//必须针对proxy实例，拦截器总是返回35，所以任何属性都返回该值
proxy.time // 35
```



```javascript
//如果handler没有设置任何拦截，即空对象，那就等同于直接访问target。
var target = {};
var hadnler = {};
var proxy = new Proxy(target, handler);
proxy.a = "b";
target.a	//"b"
```



## 2、实例方法

Proxy支持的拦截操作共13种。

#### （1）get()

**功能**：<font color=red>拦截某个属性的读取操作</font>；

**参数**：可以接受三个参数，<font color=blue>**目标对象、属性名和proxy实例本身**（操作行为所针对的对象）</font>，且最后一个参数为可选参数。

- 实现数组读取负数索引

```javascript
function createArray(...elements){
    let handler = {
        get(target,propKey,receiver){
            let index = Number(propKey);
            if(index < 0){
                propKey = String(target.length + index)
            }
            return Reflect.get(target,propKey,receiver)
        }
    };
    let target = [];
    target.push(...element);
    return new Proxy(target,handler)
}
let arr = ['a','b','c'];
arr[-2]	//'b'
```

- 实现生成DOM节点的通用函数dom

```javascript
const dom = new Proxy({}, {
    get(target,property){
        return function(attrs = {},...children){
            const el = document.createElement(property);
            for(let prop of Object.keys(attrs)){
                el.setAttribute(prop,attrs[prop])
            }
            for(let child  of children){
                if (typeof child === 'string') {
                    child = document.createTextNode(child);
                }
                el.appendChild(child);
            }
            return el;
        }
    }
});
const el = dom.div({},
  'Hello, my name is ',
  dom.a({href: '//example.com'}, 'Mark'),
  '. I like:',
  dom.ul({},
    dom.li({}, 'The web'),
    dom.li({}, 'Food'),
    dom.li({}, '…actually that\'s it')
  )
);
document.body.appendChild(el);
```

```html
<!--结果如下-->
<div>
    "Hello, my name is"
    <a href="//example.com">Mark</a>
    ". I like:"
   <ul>
       <li>The web</li>
       <li>Food</li>
       <li>…actually that's it</li>
    </ul>
</div>
```

- 如果一个属性不可配置且不可写，则 Proxy 不能修改该属性，否则通过 Proxy 对象访问该属性会报错。


```javascript
const target = Object.defineProperties({}, {
  foo: {
    value: 123,
    writable: false,
    configurable: false
  },
});
const handler = {
  get(target, propKey) {
    return 'abc';
  }
};
const proxy = new Proxy(target, handler);
proxy.foo	// TypeError: Invariant check failed
```



#### （2）set()

**功能**：<font color=red>拦截某个属性的赋值操作</font>；

**参数**：可以接受四个参数，**<font color=blue>目标对象、属性名、属性值和Proxy实例本身</font>**，且最后一个参数为可选参数。

- 实现对象私有化属性（只要属性名用下划线开头），不可被外部使用

```javascript
const handler = {
  get(target, key) {
    invariant(key, "get");
    return target[key];
  },
  set(target, key, value) {
    invariant(key, "set");
    target[key] = value;
    return true;
  },
};
function invariant (key, action) {
  if (key[0] === '_') {
    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
  }
}
const target = {};
const proxy = new Proxy(target, handler);
//只要读写的属性名的第一个字符是下划线，一律抛出错，从而实现禁止读写内部属性的目的
proxy._prop
// Error: Invalid attempt to get private "_prop" property
proxy._prop = 'c'
// Error: Invalid attempt to set private "_prop" property
```

- 如果目标对象自身的某个属性不可写，那么`set`方法将不起作用；

- **<font color=deepred>set代理应当返回一个布尔值</font>**，**严格模式下**，set代理如果没有返回true，就会报错。

```javascript
'use strict';
const handler = {
  set:function(obj, prop, value){
    obj[prop] = value;
    return false;
  }
};
const proxy = new Proxy({}, handler);
proxy.foo = "bar";
//严格模式下，set代理返回false或者undefined，都会报错。
```



#### （3）apply()

**功能**：<font color=red>拦截函数的调用、call和apply操作</font>；

**参数**：可以接受三个参数，**<font color=blue>目标对象、目标对象（this）的上下文和目标对象的参数数组</font>**。

```javascript
var target = function () {
  return "I am the target";
};
var handler = {
  apply: function () {
    return "I am the proxy";
  },
};
var p = new Proxy(target, handler);	
//"I am then proxy"
```

变量p是Proxy的实例，当它作为函数调用时p()，就会被apply方法拦截，返回一个字符串。



#### （4）has()

**功能**：<font color=red>判断对象是否具有某个属性</font>（典型操作是`in`运算符）；

**参数**：可以接受两个参数，<font color=blue>**目标对象、需查询的属性名**</font>。

```javascript
var target = { _prop: "foo", prop: "foo" };
let handler = {
  has(target,key){
    if(key[0] === "_"){
      return false;
    }
    return key in target;
  }
};
var proxy = new Proxy(target, handler);
console.log("prop" in proxy)  //true
console.log("_prop" in proxy)	 //false
//当访问的属性名是_开头的就会返回false，不被in运算符发现
```

has()方法拦截的是`HasProperty`操作，而不是`HasOwnProperty`，has方法**不判断一个属性是对象自身属性还是继承的属性**。





#### （5）construct()

**功能**：<font color=red>拦截new命令</font>；

**参数**：可以接受三个参数，<font color=blue>**目标对象、构造函数的参数数组和new命令的构造函数**</font>。

```javascript
const handler = {
  construct(target, args ,newTarget){
    return new target(...args);
  }
};
```

使用案例：

```javascript
const p = new Proxy(function(){}, {
  construct(target, args){
    return { value: args[0] * 10 };
  }
})
console.log(new p(1))	//{value: 10}
```

注意⚠️：

1. `construct()`方法**<font color=deepred>返回的必须是对象</font>**，否则会报错；
2. `construct()`拦截的构造函数，所以**<font color=deepred>目标对象必须是函数</font>**，否则会报错；



#### （6）deleteProperty()

**功能**：<font color=red>拦截delete操作，该方法抛出错误或者false不能删除</font>；

**参数**：可以接受两个参数，<font color=blue>**目标对象、属性名**</font>。

```javascript
var handler = {
  deleteProperty (target, key) {
    invariant(key, 'delete');
    delete target[key];
    return true;
  }
};
function invariant (key, action) {
  if (key[0] === '_') {
    throw new Error(`禁止删除开头为_的属性`);
  }
}
var target = { _prop: 'foo' };
var proxy = new Proxy(target, handler);
delete proxy._prop
// Error: 禁止删除开头为_的属性
```



#### （7）defineProperty()

**功能**：<font color=red>拦截`Object.defineProperty()`操作</font>；

**参数**：可以接受三个参数，<font color=blue>**目标对象、属性名和proxy实例本身**</font>。

```javascript
var handler = {
  defineProperty（target, key, descriptor）{
  	return false;
	}
};
var target = {};
var proxy = new Proxy(target, handler);
proxy.foo = "bar"
```

例子中defineProperty方法内部只返回false，从而导致添加新属性是无效的，这里的false表示操作失败。



#### （8）getPrototypeOf()

**功能**：<font color=red>拦截获取对象原型的操作</font>；

**范围**：<font color=blue>拦截的方法包括以下几种：</font>

- `Object.prototype.__proto__`
- `Object.prototype.isPrototypeOf()`
- `Object.getPrototypeOf()`
- `Reflect.getPrototypeOf()`
- `instanceof`

`getPrototypeOf()`方法的返回值必须是对象或者`null`，否则报错。另外，如果目标对象不可扩展， `getPrototypeOf()`方法必须返回目标对象的原型对象。



## 3、Proxy.revocable()

`Proxy.revocable()`方法**<font color=blue>返回一个可取消的 Proxy 实例</font>**；

```javascript
let target = {};
let handler = {};
let {proxy,revoke} = Proxy.revocable(target,handler);
proxy.foo = 123;
proxy.foo //123
revoke();
proxy.foo // TypeError: Revoked
```

Proxy.revocable()方法返回一个对象，该对象的`proxy`属性是`Proxy`实例；

revoke属性是一个函数，调用该属性可以取消Proxy实例，当再访问Proxy时就会抛出错误。



## 4、this问题

​	Proxy不是目标对象的透明代理，**不做任何拦截的情况下无法保证与目标对象的行为一致**。因为<font color=deepred>Proxy代理时，目标对象内部的this关键字会指向Proxy代理</font>。

```javascript
//举个栗子：一旦proxy代理target，target.m()内部的this就是指向proxy，而不是target。
const target = {
  m:function(){
    console.log(this === proxy);
  }
};
const handler = {};
const proxy = new Proxy(target, handler);
target.m();  //false
proxy.m();	//true
```



### Proxy的缺陷：

**<font color=blue>有些原生对象的内部属性，只有通过正确的`this`才能拿到，所以Proxy无法代理这些原生对象的属性</font>**。

```javascript
//getDate()方法只能在Date对象实例上面拿到
const target = new Date();
const handler = {};
const proxy = new Proxy(target, handler);
proxy.getDate();
// TypeError: this is not a Date object.
```

**<font color=blue>Proxy 拦截函数内部的`this`，指向的是`handler`对象</font>**。
