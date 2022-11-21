## 属性

### 1、String.length

​	length属性表示一个字符串的长度；该属性返回字符串中字符编码单元的数量。JavaScript 使用UTF-16编码，该编码使用一个 16 比特的编码单元来表示大部分常见的字符，使用两个代码单元表示不常用的字符。因此 length 返回值可能与字符串中实际的字符数量不相同。

​	空字符串的length为0，静态属性String.length返回1。



## 方法

### 1、chartAt()方法

​	charAt()方法从一个字符串中返回指定的字符。`str.chartAt(idnex)`，index是一个介于0和字符串长度减1的。

​	字符串中的字符从左向右索引，第一个字符的索引值为 0，最后一个字符（的索引值为 `stringName.length - 1`。 如果指定的 index 值超出了该范围，则返回一个空字符串。



### 2、concat()方法

​	concat()方法将一个或多个字符串与原字符串连接合并，形成一个新的字符串并返回。

​	不过强烈建议使用赋值操作符`+`代替concat方法。



### 3、endsWith()方法

​	endsWith()方法用来判断当前字符串是否是以另外一个给定的子字符串“结尾”的，根据判断结果返回true或false。

​	`str.endsWith(searchString,length)`，searchString要搜索的字符串；length可选，作为str的长度。

```javascript
const str1 = 'Cats are the best!';
console.log(str1.endsWith('best', 17));	//true
const str2 = 'Is this a question';
console.log(str2.endsWith('?'));	//false
```



### 4、startsWith()方法

​	startsWith()方法用来判断当前字符串是否以另外一个给定的子字符串开头，并根据判断结果返回 true 或 false。

​	`str.startsWith(searchString[, position])`，searchString要搜索的字符串，position可选，在 str中搜索 searchString的开始位置，默认值为 0。

```javascript
const str1 = 'Saturday night plans';
console.log(str1.startsWith('Sat'));	//true
console.log(str1.startsWith('Sat', 3));	//false
```



### 5、includes()方法

​	includes方法用于判断一个字符串是否包含在另一个字符串中，根据情况返回true或false。

​	`str.includes(searchString[,position])`，searchString要在此字符串中搜索的字符串；position可选，从当前字符串的哪个索引位置开始搜索子字符串，默认值为0。

⚠️：这个方法是区分大小写的。

```javascript
var str = 'To be, or not to be, that is the question.';
console.log(str.includes('To be'));       // true
console.log(str.includes('nonexistent')); // false
console.log(str.includes('To be', 1));    // false
console.log(str.includes('TO BE'));       // false
```



### 6、indexOf()方法

​	indexOf()方法返回调用它的String对象中第一次出现的指定值的索引，从fromIndex处进行搜索，如果未找到该值，则返回-1。

​	`str.indexOf(searchValue[,fromIndex])`,searchValue要被查找的字符串值，fromIndex可选，开始查找的位置，可以是任意整数，默认为0。如果 fromIndex 的值小于 0，或者大于 str.length，那么查找分别从 0和str.length开始。

```javascript
var anyString = "Brave new world";
console.log(anyString.indexOf("w"));	// logs 8
console.log(anyString.lastIndexOf("w"));	// logs 10
console.log(anyString.indexOf("new"));	// logs 6
console.log(anyString.lastIndexOf("new"));	// logs 6
```



### 7、lastIndexOf()方法

​	lastIndexOf()方法返回调用String对象的指定值最后一次出现的索引，在一个字符串中的指定位置fromIndex处从后向前搜索。如果没找到这个特定值则返回-1 。

​	例子见indexOf()方法。



### 8、match()方法

​	match()方法检索返回一个字符串匹配正则表达式的结果。

```javascript
const paragraph = 'The quick brown fox jumps over the lazy dog;'
const regex = /[A-Z]/g;
const found = paragraph.match(regex);
console.log(found);	//["T"]
```



### 9、padEnd()方法

​	padEnd()方法会用一个字符串填充当前字符串（如果需要的话则重复填充），返回填充后达到指定长度的字符串。从当前字符串的末尾（右侧）开始填充。

```javascript
const str1 = 'Breaded Mushrooms';
console.log(str1.padEnd(25, '.'));	//"Breaded Mushrooms........"
const str2 = '200';
console.log(str2.padEnd(5));	//"200  "
```



### 10、padStart()方法

​	padStart()方法用另一个字符串填充当前字符串(如果需要的话，会重复多次)，以便产生的字符串达到给定的长度。从当前字符串的左侧开始填充。

```javascript
const str1 = '5';
console.log(str1.padStart(2, '0'));	//"05"
const fullNumber = '2034399002125581';
const last4Digits = fullNumber.slice(-4);
const maskedNumber = last4Digits.padStart(fullNumber.length, '*');
console.log(maskedNumber);	//"************5581"
```



### 11、repeat()方法

​	repeat()构造并返回一个新字符串，该字符串包含被连接在一起的指定数量的字符串的副本。

```javascript
"abc".repeat(-1)	// RangeError: repeat count must be positive and less than inifinity
"abc".repeat(0)	// ""
"abc".repeat(2)	// "abcabc"
"abc".repeat(3.5)	// "abcabcabc" 参数count将会被自动转换成整数.
({toString : () => "abc", repeat : String.prototype.repeat}).repeat(2)
//"abcabc",repeat是一个通用方法,也就是它的调用者可以不是一个字符串对象.
```



### 12、replace()方法

​	replace()方法返回一个由替换值替换部分或所有的模式匹配项后的新字符串。模式可以是一个字符串或者是一个正则表达式，替换值可以是一个字符串或者是一个每次匹配都要调用的回调函数。**如果`pattern`是字符串，则仅替换第一个匹配项。**

​	原字符串不会改变。

```javascript
const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';
console.log(p.replace('dog', 'monkey'));
// expected output: "The quick brown fox jumps over the lazy monkey. If the dog reacted, was it really lazy?"
const regex = /Dog/i;
console.log(p.replace(regex, 'ferret'));
// expected output: "The quick brown fox jumps over the lazy ferret. If the dog reacted, was it really lazy?"
```



### 13、search()方法

​	search()方法执行正则表达式和String对象之间的一个搜索匹配。如果匹配成功返回正则表达式在字符串中首次匹配项的索引，否则返回-1。

```javascript
var str = "hey JudE";
var re = /[A-Z]/g;
var re2 = /[.]/g;
console.log(str.search(re)); // returns 4
console.log(str.search(re2)); // returns -1
```



### 14、slice()方法

​	slice()方法提取某个字符串的一部分，指定开始位置和结束位置索引，并返回一个新的字符串，且不会改动原字符串。

```javascript
const str = 'The quick brown fox jumps over the lazy dog.';
console.log(str.slice(31));	//"the lazy dog."
console.log(str.slice(4, 19));	//"quick brown fox"
console.log(str.slice(-4));	//"dog."
console.log(str.slice(-9, -5));	//"lazy"
```



### 15、split()方法

​	split()方法使用指定的分隔符字符串将一个String对象分割成子字符串**数组**，以一个指定的分割字符串来决定每个拆分的位置。 

```javascript
const str = 'The quick brown fox jumps';
const words = str.split(' ');
console.log(words[3]);	//"fox"
const chars = str.split('');
console.log(chars[8]);	//"k"
const strCopy = str.split();
console.log(strCopy);	//["The quick brown fox jumps"]
const = para = "A,B,C";
console.log(para.split(","))	//['A','B','C'];
```



### 16、substring()方法

​	substring()方法返回一个字符串在开始索引到结束索引之间的一个子集, 或从开始索引直到字符串的末尾的一个子集，返回是个新字符串。

​	`str.substring(indexStart,indexEnd)`，indexStart开始的索引，indexEnd结束的索引，前闭后开区间，如果start和end相同，返回一个空字符串；省略end，返回整个字符串；任意参数小于0或NaN，被当作0；任意参数大于字符串长度，被当作字符串长度；如果start大于end，就把两个参数调换。

```javascript
var anyString = "Mozilla";
// 输出 "Moz"
console.log(anyString.substring(0,3));
console.log(anyString.substring(3,0));
console.log(anyString.substring(3,-3));
console.log(anyString.substring(3,NaN));
console.log(anyString.substring(4,4));	// 输出 ""
```



### 17、toLowerCase()方法

​	toLowerCase()会将调用该方法的字符串值转为小写形式，并返回。



### 18、toUpperCase()方法

​	toUpperCase()方法将调用该方法的字符串转为大写形式并返回（如果调用该方©法的值不是字符串类型会被强制转换）。



### 19、trim()方法

​	trim()方法会从一个字符串的两端删除空白字符。在这个上下文中的空白字符是所有的空白字符 (space, tab, no-break space 等) 以及所有行终止符字符（如 LF，CR等）。返回一个代表调用字符串两端去掉空白的新字符串。