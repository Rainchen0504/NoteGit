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

