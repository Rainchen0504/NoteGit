## 属性

### 1、Array.length

​	length 是Array的实例属性。返回或设置一个数组中的元素个数。该值是一个无符号 32-bit 整数，并且总是大于数组最高项的下标。



## 方法

### 1、concat()

​	concat() 方法用于连接两个或多个数组。此方法不会更改现有数组，而是返回一个新数组，其中，包含连接数组的值。

```javascript
let hege = ['a','b'];
let stale = ['c','d','e'];
let children = hege.concat(stale)
console.log(children);	//['a','b','c','d','e']
```



### 2、copyWithin()

​	将数组元素复制到数组中的另一个位置，覆盖现有值。此方法永远不会向数组添加更多项。注意：此方法会覆盖原始数组。

`copyWithin(target,start,end)`,target目标位置的索引；start截取数组开始位置的索引，end截取数组结束位置的索引。

```javascript
let array1 = ['a', 'b', 'c', 'd', 'e'];
console.log(array1.copyWithin(0, 3, 4));	//["d", "b", "c", "d", "e"]
console.log(array1.copyWithin(1, 3));	//["d", "d", "e", "d", "e"]
```



### 3、entries()

​	entries() 方法返回一个新的**Array Iterator**迭代器对象，对象包含数组中每个索引的键/值对。此对象的原型上有一个next方法，可用用于遍历迭代器取得原数组的[key,value]。

```javascript
let fruit = ['a','b','c','d'];
let f= fruit.entries();
console.log(f.next().value);    //[0, 'a']
console.log(f.next().value);    //[1, 'b']
console.log(f.next().value);    //[2, 'c']
console.log(f);		//Array Iterator{}迭代器
```



### 4、every()

​	every()方法测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。若收到一个空数组，此方法在一切情况下都会返回 true。

```javascript
let ages = [32,33,16,40];
let isBlow = (current) => current<35;
console.log(ages.every(isBlow));	//false
```



### 5、fill()

​	fill()方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。

`fill(value,[start,end])`,value用来填充数组的值，start可选起始索引，end终止索引。

```javascript
const array1 = [1, 2, 3, 4];
console.log(array1.fill(0, 2, 4));	//[1, 2, 0, 0]
console.log(array1.fill(5, 1));	//[1, 5, 5, 5]
console.log(array1.fill(6));	//[6, 6, 6, 6]
```



### 6、filter()

​	filter()方法创建一个新数组，其中包含通过所提供函数实现的测试的所有元素。

```javascript
const words = ['limit', 'elite', 'exuberant', 'destruction', 'present'];
const result = words.filter(word => word.length > 6);
console.log(result);	//["exuberant", "destruction", "present"]
```



### 7、find()

​	find()方法返回数组满足提供的测试函数的第一个元素的值，否则返回undefined。

```javascript
const array1 = [5, 12, 8, 130, 44];
const found = array1.find(element => element > 10);
console.log(found);	//12
```



### 8、findIndex()

​	findIndex()方法返回数组中满足提供的测试函数的第一个元素的**索引**。若没有找到对应元素则返回-1。

```javascript
const array1 = [5, 12, 8, 130, 44];
const isLargeNumber = (element) => element > 13;
console.log(array1.findIndex(isLargeNumber));	//3
```



### 9、forEach()

​	forEach()方法用于调用数组的每个元素，并将元素传递给回调函数。forEach()对于空数组是不会执行回调函数的。

```javascript
array.forEach(function(currentValue,index,arr),thisvalue)
//currentValue，必须，当前元素
//index，可选，当前元素索引值
//arr，可选，当前元素所属的数组对象

//ES6中的写法
array.forEach(item => {执行内容})
```

​	forEach()方法对数组的每个元素执行一次给定的函数。

```javascript
const array1 = ['a', 'b', 'c'];
array1.forEach(element => console.log(element));	//挨个输出'a','b','c'

//将for循环转换为forEach
const items = ['item1', 'item2', 'item3'];
const copy = [];
// before
for (let i=0; i<items.length; i++) {
  copy.push(items[i]);
}
// after
items.forEach(function(item){
  copy.push(item);
});
```



### 10、indexOf()

​	indexOf()方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。

```javascript
const beasts = ['ant', 'bison', 'camel', 'duck', 'bison'];
console.log(beasts.indexOf('bison'));	//1
console.log(beasts.indexOf('bison', 2));	//4
console.log(beasts.indexOf('giraffe'));	//-1
```



### 11、isArray()

​	isArray() 方法确定对象是否是数组。如果对象是数组，Thinction 返回 true，否则返回 false。

```javascript
Array.isArray([1, 2, 3]);
// true
Array.isArray({foo: 123});
// false
Array.isArray("foobar");
// false
Array.isArray(undefined);
// false
```



### 12、join()

​	join() 方法将数组的元素转换为字符串。该方法将一个数组的所有元素连接成一个字符串并返回这个字符串，如果数组只有一个项目，那么将返回该项目而不使用分隔符。

```javascript
const elements = ['Fire', 'Air', 'Water'];
console.log(elements.join());	//"Fire,Air,Water"
console.log(elements.join(''));	//"FireAirWater"
console.log(elements.join('-'));	//"Fire-Air-Water"
```



### 13、lastIndexOf()

​	lastIndexOf()方法返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex处开始。

```javascript
const animals = ['Dodo', 'Tiger', 'Penguin', 'Dodo'];
console.log(animals.lastIndexOf('Dodo'));	//3
console.log(animals.lastIndexOf('Tiger'));	//1
```



### 14、map()

​	map() 方法返回一个新数组，新数组中的元素为原始数组中的每个元素调用函数处理后得到的值。

```javascript
let numbers = [4,9,16,25];
let result = numbers.map(Math.sqrt);
console.log(result);	//[2,3,4,5]
```



### 15、pop()

​	pop()方法从数组中删除最后一个元素，并返回该元素的值。此方法更改数组的长度。当数组为空时返回undefined。

```javascript
const plants = ['broccoli', 'cauliflower', 'cabbage', 'kale', 'tomato'];
console.log(plants.pop());	//"tomato"
console.log(plants);	//["broccoli", "cauliflower", "cabbage", "kale"]
```



### 16、push()

​	push()方法将一个或多个元素添加到数组的末尾，并返回该数组的新长度。

```javascript
const animals = ['pigs', 'goats', 'sheep'];
const count = animals.push('cows');
console.log(count);	//4
console.log(animals);	//["pigs", "goats", "sheep", "cows"]
animals.push('chickens', 'cats');
console.log(animals);	//["pigs", "goats", "sheep", "cows", "chickens", "cats"]
```



### 17、reduce()

reduce()方法对数组中的每个元素执行一个reducer函数，将其结果汇总为单个返回值。

```javascript
const array1 = [1, 2, 3, 4];
const reducer = (previousValue, currentValue) => previousValue + currentValue;
console.log(array1.reduce(reducer));	//1+2+3+4=10;
console.log(array1.reduce(reducer, 5));	//1+2+3+4+5=15;
```

#### （1）⚠️：**reducer函数**接收4个参数：

​		Accumulator (acc) (累计器)，它是上一次调用回调时返回的累积值或是初始值；

​		Current Value (cur) (当前值)，数组中正在处理的元素；

​		Current Index (idx) (当前索引)，可选值，数组中正在处理的当前元素的索引；

​		Source Array (src) (源数组)，调用reduce()的数组；

​	**reducer** 函数的返回值分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

#### （2）运行过程

```javascript
[0, 1, 2, 3, 4].reduce((accumulator, currentValue, currentIndex, array) => {return accumulator + currentValue}, 10)
```

callback 被调用五次，每次调用的参数和返回值如下表：

| callback    | accumulator | currentValue | currentIndex |      array      | return value |
| :---------- | :---------: | :----------: | :----------: | :-------------: | :----------: |
| first call  |     10      |      0       |      0       | [0, 1, 2, 3, ]  |      10      |
| second call |     10      |      1       |      1       | [0, 1, 2, 3, 4] |      11      |
| third call  |     11      |      2       |      2       | [0, 1, 2, 3, 4] |      13      |
| fourth call |     13      |      3       |      3       | [0, 1, 2, 3, 4] |      16      |
| fifth call  |     16      |      4       |      4       | [0, 1, 2, 3, 4] |      20      |

#### （3）高级用法

- 计算数组中每个元素出现的次数

```javascript
let names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];
let nameNum = names.reduce((pre,cur)=>{
  if(cur in pre){
    pre[cur]++
  }else{
    pre[cur] = 1 
  }
  return pre
},{})
console.log(nameNum); //{Alice: 2, Bob: 1, Tiff: 1, Bruce: 1}
```

- 数组去重

```javascript
let arr = [1,2,3,4,4,1]
let newArr = arr.reduce((pre,cur)=>{
    if(!pre.includes(cur)){
      return pre.concat(cur)
    }else{
      return pre
    }
},[])
console.log(newArr);// [1, 2, 3, 4]
```

- 将二维数组转化为一维

```javascript
let arr = [[0, 1], [2, 3], [4, 5]]
let newArr = arr.reduce((pre,cur)=>{
    return pre.concat(cur)
},[])
console.log(newArr); // [0, 1, 2, 3, 4, 5]
```

- 将多维数组转化为一维

```javascript
let arr = [[0, 1], [2, 3], [4,[5,6,7]]]
const newArr = function(arr){
   return arr.reduce((pre,cur)=>pre.concat(Array.isArray(cur)?newArr(cur):cur),[])
}
console.log(newArr(arr)); //[0, 1, 2, 3, 4, 5, 6, 7]
```

- 对象里的属性求和

```javascript
var result = [
    {
        subject: 'math',
        score: 10
    },
    {
        subject: 'chinese',
        score: 20
    },
    {
        subject: 'english',
        score: 30
    }
];
let sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, 0);
console.log(sum) //60
```



### 18、reduceRight()

​	reduceRight()方法接受一个函数作为累加器（accumulator）和数组的每个值（从右到左）将其减少为单个值。

```javascript
const array1 = [[0, 1], [2, 3], [4, 5]].reduceRight(
  (accumulator, currentValue) => accumulator.concat(currentValue)
);
console.log(array1);	//[4, 5, 2, 3, 0, 1]
```



### 19、reverse()

​	reverse() 方法将数组中元素的位置颠倒，并返回该数组。数组的第一个元素会变成最后一个，数组的最后一个元素变成第一个。该方法会改变原数组。

```javascript
const array1 = ['one', 'two', 'three'];
console.log('array1:', array1);	//["one", "two", "three"]
const reversed = array1.reverse();
console.log('reversed:', reversed);	//["three", "two", "one"]
console.log('array1:', array1);	//["three", "two", "one"]
```



### 20、some()

​	some() 方法测试数组中是不是至少有1个元素通过了被提供的函数测试,。如果有一个元素满足条件，则表达式返回true, 如果没有满足条件的元素，则返回false.剩余的元素不会再执行检测.它返回的是一个Boolean类型的值。如果用一个空数组进行测试，在任何情况下它返回的都是`false`。

```javascript
const array = [1, 2, 3, 4, 5];
const even = (element) => element % 2 === 0;
console.log(array.some(even));	//true
```



### 21、slice()

​	slice()方法返回一个新的数组对象，这一对象是一个由begin和end决定的原属组的**浅拷贝**（包括begin，不包括end），原始数组不会被改变。

```javascript
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];
console.log(animals.slice(2));	//["camel", "duck", "elephant"]
console.log(animals.slice(2, 4));	//["camel", "duck"]
console.log(animals.slice(-2));	//["duck", "elephant"]
console.log(animals.slice(2, -1));	//["camel", "duck"]
```



### 22、shift()

​	shift()方法从数组中删除**第一个**元素，并返回该元素的值。此方法更改数组的长度。如果数组为空则返回undefined。

```javascript
const array1 = [1, 2, 3];
const firstElement = array1.shift();
console.log(array1);	//[2,3]
console.log(firstElement);	//1
```



### 23、sort()

​	sort()方法用**原地算法**对数组的元素进行排序，并返回数组。默认排序顺序是在将元素转换为字符串，然后比较它们的UTF-16代码单元值序列时构建的。由于它取决于具体实现，因此无法保证排序的时间和空间复杂性。

```javascript
const months = ['March', 'Jan', 'Feb', 'Dec'];
months.sort();
console.log(months);	//["Dec", "Feb", "Jan", "March"]
const array1 = [1, 30, 4, 21, 100000];
array1.sort();
console.log(array1);	//[1, 100000, 21, 30, 4]
```

要实现真正的排序写法如下：

```javascript
let numbers = [32,5,3,11,496,9]
numbers.sort((a, b) => a - b);
console.log(numbers);	//[3, 5, 9, 11, 32, 496]
```



### 24、splice()

​	splice()方法通过删除或替换现有元素或者原地添加新的元素来修改数组,并以数组形式返回被修改的内容。此方法会改变原数组。

```javascript
const months = ['Jan', 'March', 'April', 'June'];
months.splice(1, 0, 'Feb');
console.log(months);	//["Jan", "Feb", "March", "April", "June"]
months.splice(1, 2, 'May');
console.log(months);	//['Jan', 'May', 'April', 'June']
```



### 25、toString()

​	tostring()方法返回一个字符串，表示指定的数组及其元素。

```javascript
const array1 = [1, 2, 'a', '1a'];
console.log(array1.toString());	//"1,2,a,1a"
```



### 26、unshift()

​	unshift()方法将一个或多个元素添加到数组的**开头**，并返回该数组的**新长度**(该方法修改原有数组)。

```javascript
const array1 = [1, 2, 3];
console.log(array1.unshift(4, 5));	//返回值为长度5
console.log(array1);	//[4, 5, 1, 2, 3]
```



### 27、includes()

​	includes()方法用来判断一个数组是否包含一个指定的值，如果包含则返回true，否则返回false。

​	`arr.includes(valueToFind,fromIndex)`，参数valueToFind是需要查找的元素值，参数fromIndex可选，默认为0，从fromIndex索引处查找valueToFind。

```javascript
const array1 = [1, 2, 3];
console.log(array1.includes(2));	//true
const pets = ['cat', 'dog', 'bat'];
console.log(pets.includes('cat'));	//true
console.log(pets.includes('at'));//false
```



### 28、flat()	

​	flat()方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组元素合并为一个新的数组返回。

```js
const arr1 = [0,1,[3,4]];
console.log(arr1.flat());		//[0,1,2,3,4];
const arr2 = [0,1,2,[3,[4,5]]];
console.log(arr2.flat(2));	//[0, 1, 2, 3, 4, 5]
```

语法：

`var newArray = arr.flat([dept])`

参数：depth为可选填的值，可以指定要提取嵌套数组的结构深度，默认为1。

返回值：一个包含将数组与子数组中所有元素的新数组。



<font color=red>注意⚠️:该方法**不支持IE浏览器**</font>

使用 **Infinity**，可展开任意深度的嵌套数组
全局属性 Infinity 是一个数值，表示<font color=blue>**无穷大**</font>。

```js
let arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
arr4.flat(Infinity);	// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```



### 29、from()

`Array.from()`方法对一个类似数组或者可迭代对象<font color=deepred>创建一个新的、浅拷贝的数组实例</font>。

```js
const list1 = Array.from([1,2,3], x => x + x)	//[2, 4, 6]
const list2 = Array.from('foo')	//["f", "o", "o"]
const data = [
  { name: "questions", value: 17 },
  { name: "schools", value: 25 }
];
const names = Array.from(data, (el) => el.name);	//[ 'questions', 'schools' ]
```

语法:

```js
// 箭头函数
Array.from(arrayLike, (element) => { /* … */ } )
// 映射函数
Array.from(arrayLike, mapFn)
```

参数：

1. arrayLike表示想要转换成数组的伪数组或可迭代对象；
2. mapFn可选的，如果指定了该参数，新数组中的每个元素会执行该回调函数；

返回值：一个新的数组实例



## 总结：

### 1、修改原数组的方法：

copyWithin()、fill()、pop()、push()、reverse()、shift()、sort()、splice()、unshift

### 2、不会修改原数组的方法：

concat()、filter()、map()、slice()

### 3、返回布尔值的方法：

every()、isArray()、some()



## 数组的遍历

### 1、for循环

```javascript
for (let i = 0; i < arr.length; i++)
```

运行的最快，可兼容旧版浏览器；

### 2、for..of循环

```javascript
for (let item of arr)
```

​	该循环不能获取当前元素的索引，只是获取元素的值，因为数组也是对象，所以可以使用。

### 3、for..in循环

(1)`for..in`循环会遍历**所有属性**，不仅仅是这些数字属性。

​	在浏览器和其它环境中有一种“类数组”的对象，具有length和索引属性，但是也可能有其他非数字的属性和方法，for..in循环会把它们都遍历出来，增加额外的对象。

(2)`for..in`循环适用于普通对象，并且做了对应的优化。

​	但是不适用于数组，因此速度要慢 10-100 倍。当然即使是这样也依然非常快。只有在遇到瓶颈时可能会有问题。但是我们仍然应该了解这其中的不同。

​	**通常来说，尽量不建议使用for..in来处理数组**

