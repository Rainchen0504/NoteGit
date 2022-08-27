# 一、数组

### 1、添加删除元素

数组是一个可以修改的对象。如果添加删除元素会动态变化。

- 末尾添加用push方法；
- 开头添加使用unshift方法；
- 末尾删除用pop方法；
- 开头删除用shift方法；



### 2、任意位置添加删除元素

- splice方法添加删除指定索引上指定数量的元素；



### 3、数组方法

| 方法        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| concat      | 连接 2 个或更多数组，并返回结果                              |
| every       | 对数组中的每个元素运行给定函数，如果该函数对每个元素都返回 true，则返回 true |
| filter      | 对数组中的每个元素运行给定函数，返回该函数会返回 true 的元素组成的数组 |
| forEach     | 对数组中的每个元素运行给定函数。这个方法没有返回值           |
| join        | 将所有的数组元素连接成一个字符串                             |
| indexOf     | 返回第一个与给定参数相等的数组元素的索引，没有找到则返回-1   |
| lastIndexOf | 返回在数组中搜索到的与给定参数相等的元素的索引里最大的值     |
| map         | 对数组中的每个元素运行给定函数，返回每次函数调用的结果组成的数组 |
| reverse     | 颠倒数组中元素的顺序                                         |
| slice       | 传入索引值，将数组里对应索引范围内的元素作为新数组返回       |
| some        | 对数组中的每个元素运行给定函数，如果任一元素返回 true，则返回 true |
| sort        | 按照字母顺序对数组排序，支持传入指定排序方法的函数作为参数   |
| toString    | 将数组作为字符串返回                                         |
| valueOf     | 和toString类似，将数组作为字符串返回                         |



求斐波那契数列的前 20 个数

```javascript
const fibonacci = [];
fibonacci[1] = 1;
fibonacci[2] = 1;
for (let i = 3; i < 20; i++) {
  fibonacci[i] = fibonacci[i - 1] + fibonacci[i - 2];
}
console.log(...fibonacci);
```



### 4、ES6和数组新功能

#### (1)新增数组方法

##### includes方法：

​	数组中存在某个元素返回true，否则返回false；

##### find方法：

​	根据回调函数给定的条件从数组中查找元素，如果找到则返回该元素；

##### findIndex方法：

​	根据回调函数给定的条件从数组中查找元素，如果找到则返回该元素在数组中的索引；

##### @@iterator：

​	返回一个包含数组键值对的迭代器对象，通过同步调用得到数组元素的键值对；



#### (2)迭代方法

##### 1.for...of循环迭代：

​	迭代数组值的循环；

##### 2.@@iterator对象：

​	为 Array 类增加了一个@@iterator 属性，需要通过 Symbol.iterator 来访问；

```javascript
let numbers = [1, 2, 3, 4];
let iterator = numbers[Symbol.iterator]();
console.log(iterator.next().value);	//1
console.log(iterator.next().value);	//2
```

不断通过迭代器的next方法可以依次得到数组中的值，所有值迭代完后iterator.next().value返回undefined。

##### 3.数组的entries、keys和values方法

entries方法返回包含键值对的@@iterator

```javascript
//方法一：
let number = [1, 2, 3, 4, 5];
let newEntries = number.entries();
for (const n of newEntries) {
  console.log(n);
}
//[ 0, 1 ]
//[ 1, 2 ]
//[ 2, 3 ]
//[ 3, 4 ]
//[ 4, 5 ]

//方法二：
let aEntries = number.entries();
console.log(aEntries.next().value); // [0, 1] - 位置0的值为1 console.log(aEntries.next().value); // [1, 2] - 位置1的值为2 console.log(aEntries.next().value); // [2, 3] - 位置2的值为3
```

##### 4.from方法：

Array.from()方法根据已有数组创建一个新数组；

```javascript
let number = [1, 2, 3, 4, 5];
let numbers = Array.from(number);
number[0] = 9;
console.log(number);
console.log(numbers);
//[ 9, 2, 3, 4, 5 ]
//[ 1, 2, 3, 4, 5 ]
```



# 二、栈

### 1、数据结构

遵从<font color=deepred>**先进后出**</font>原则的有序集合。

应用场景：编程语言的编译器和内存中保存变量、方法调用等，也被用于浏览器历史记录(<u>浏览器返回按钮</u>)



### 2、构建基于数组的栈结构

可以使用数组来保存栈里的元素，但要对元素的插入和删除功能进行限制。

```javascript
//创建一个表示栈结构的类
class Stack {
  constructor(){
    this.items = []
  }
}
```

- push：添加一个或几个新元素到栈顶；

  ```javascript
  push(element) {
    this.items.push(element);
  }
  ```

- pop：移除栈顶元素，同时返回被移除的元素；

  ```javascript
  pop(){
    return this.items.pop();
  }
  ```

- peek：返回栈顶元素，不操作原数组；

  ```javascript
  peek() {
    return this.items[this.items.length - 1];
  }
  ```

- isEmpty：如果栈为空返回true，不为空返回false；

  ```javascript
  isEmpty(){
    return this.items.length === 0;
  }
  ```

- clear：移除栈里的所有元素；

  ```javascript
  clear(){
    this.items = [];
  }
  ```

- size：返回栈里的元素个数，和length属性类似；

  ```javascript
  size() {
    return this.items.length;
  }
  ```



### 3、使用数组构建的Stack类

```javascript
class Stack {
  constructor() {
    this.items = [];
  }
  push(element) {
    this.items.push(element);
  }
  pop() {
    return this.items.pop();
  }
  peek() {
    return this.items[this.items.length - 1];
  }
  isEmpty() {
    return this.items.length === 0;
  }
  clear() {
    this.items = [];
  }
  size() {
    return this.items.length;
  }
}
//初始化Stack类
const stack = new Stack();
console.log(stack.isEmpty()); // 输出为true
stack.push(5);
stack.push(8);
console.log(stack.peek()); // 输出8
stack.push(11); 
console.log(stack.size()); // 输出3 
console.log(stack.isEmpty()); // 输出false
stack.push(15);
stack.pop();
stack.pop(); 
console.log(stack.size()); // 输出2，剩5和8
```



### 4、构建基于对象的Stack类

​	使用数组大部分方法的时间复杂度是O(n)，要遍历整个数组的所有位置；同时数组是元素的有序集合，占用内存会更多。

```javascript
class Stack {
  constructor() {
    this.count = 0;
    this.items = {};
  }
}
```

- 向栈中插入元素

  ```javascript
  push(element){
    this.items[this.count] = element;
    this.count++;
  }
  ```

- 栈的大小

  ```javascript
  size(){
    return this.count;
  }
  ```

- 验证栈是否为空

  ```javascript
  isEmpty() {
    return this.count === 0;
  }
  ```

- 从栈中删除弹出元素

  ```javascript
  pop(){
    if(this.isEmpty()){
      return undefined
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result
  }
  ```

- 查看栈顶值

  ```javascript
  peek(){
    if(this.isEmpty()){
      return undefined
    }
    return this.items[this.count - 1];
  }
  ```

- 清空栈

  ```javascript
  clear(){
    this.items = {};
    this.count = 0;
  }
  ```

- 创建toString方法

  ```javascript
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[0]}`;
    for (let i = 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
  ```

  

以上方法时间复杂度都是O(1)，表示可以直接找到目标元素并对其操作。



### 5、用栈解决问题

#### （1）十进制到二进制

前提是用数组构建的Stack类（数组伪造的栈结构）

![image-20220706151307002](https://raw.githubusercontent.com/Rainchen0504/picture/master/202207061513978.png)

```javascript
//十进制转二进制，使用栈思想
function transformNum(aimNumber) {
  const arrStack = new Stack(); //数组栈
  let number = aimNumber; //要转换的源数据
  let rem; //压入栈中的值
  let resultString = ""; //转换得到的二进制结果

  //当除2的结果不为0，即没转换完,继续循环
  while (number > 0) {
    rem = Math.floor(number % 2);
    arrStack.push(rem);
    number = Math.floor(number / 2);
  }

  //栈长度不为0，从栈顶取值拼接转换结果
  while (!arrStack.isEmpty()) {
    resultString += arrStack.pop().toString();
  }
	//返回结果
  return resultString;
}
console.log(transformNum(5));	//101
```



#### （2）十进制到其他进制

根据上面的结果衍生数字转其他进制的方法

```javascript
//转其他进制2～36
function transformAny(aimNumber, base) {
  const arrStack = new Stack();
  let number = aimNumber;
  let rem;
  let resultString = "";

  //如果不在2~36之间返回空
  if (base < 2 || base > 36) {
    return "";
  }

  while (number > 0) {
    rem = Math.floor(number % base);
    arrStack.push(rem);
    number = Math.floor(number / base);
  }

  while (!arrStack.isEmpty()) {
    resultString += arrStack.pop().toString();
  }

  return resultString;
}
```



# 三、队列

### 1、队列数据结构

​	队列是遵循**<font color=deepred>先进先出</font>**原则的一组有序的项，尾部添加，顶部移除。



### 2、创建队列

```javascript
class Queue {
  constructor() {
    this.count = 0;	//控制队列大小
    this.lowestCount = 0;	//追踪第一个元素
    this.items = {};
  }
}
```

- enqueue：向队列添加新元素，**只能添加到队尾**；

  ```javascript
  enqueue(element){
    this.items[this.count] = element;
    this.count++
  }
  ```

- dequeue：从队列移除项，先进的先移除；

  ```javascript
  dequeue(){
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;
    return result;
  }
  ```

- peek：查看队列头元素；

  ```javascript
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.lowestCount];
  }
  ```

- isEmpty：如果队列为空返回true，不为空返回false；

  ```javascript
  isEmpty(){
    return this.count - this.lowestCount === 0;
  }
  ```

- clear：移除队列里的所有元素；

  ```javascript
  clear(){
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }
  ```

- size：返回队列中的元素个数；

  ```javascript
  size() {
    return this.count - this.lowestCount;
  }
  ```

- toString：转字符串

  ```javascript
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[this.lowestCount]}`;
    for (let i = this.lowestCount + 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
  ```

  

### 3、使用构建的Queue队列类

```javascript
class Queue {
  constructor() {
    this.count = 0;
    this.lowestCount = 0;	//追踪第一个与元素
    this.items = {};
  }
  enqueue(element){
    this.items[this.count] = element;
    this.count++
	}
  dequeue(){
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;
    return result;
  }
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.lowestCount];
  }
  isEmpty(){
  	return this.count - this.lowestCount === 0;
  }
  clear(){
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }
  size() {
    return this.count - this.lowestCount;
  }
}
//初始化Queue类
const queue = new Queue();
console.log(queue.isEmpty()); // 输出为true
queue.enqueue("zhang");
queue.enqueue("yu");
console.log(queue.size()); // 输出2
console.log(queue.isEmpty()); // 输出为false
console.log(queue.dequeue()); // 输出zhang
console.log(queue.peek()); //输出yu
```



### 4、双端队列结构

​	双端队列是**允许同时从头和尾部添加、移除元素的特殊队列**。

#### （1）创建双端队列类

```javascript
class Dequeue {
  constructor() {
    this.count = 0; //总数
    this.lowestCount = 0; //追踪第一个元素
    this.items = {}; //队列
  }
  //在头部添加元素
  addFront(element) {
    if (this.isEmpty()) {
      this.addBack(element);
    } else if (this.lowestCount > 0) {
      this.lowestCount--;
      this.items[this.lowestCount] = element;
    } else {
      for (let i = this.count; i > 0; i--) {
        this.items[i] = this.items[i - 1];
      }
      this.count++;
      this.items[0] = element;
    }
  }
  //在尾部添加元素
  addBack(element) {
    this.items[this.count] = element;
    this.count++;
  }
  //从头部移除一个元素
  removeFont() {
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;
    return result;
  }
  //从尾部移除一个元素
  removeBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;
  }
  //返回头部第一个元素
  peekFront() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.lowestCount];
  }
  //返回尾部第一个元素
  peekBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.count - 1];
  }
  //空队列返回true，否则返回false
  isEmpty() {
    return this.size() === 0;
  }
  //清除队列所有元素
  clear() {
    this.count = 0;
    this.items = {};
    this.lowestCount = 0;
  }
  //返回队列元素个数
  size() {
    return this.count - this.lowestCount;
  }
  //转字符串
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[this.lowestCount]}`;
    for (let i = this.lowestCount + 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}
```

#### （2）使用双端队列类

```javascript
const dequeue = new Dequeue();
dequeue.addFront("zhang");
dequeue.addFront("yu");
dequeue.addFront("chen");
dequeue.removeFont();
console.log(dequeue.size()); //2
console.log(dequeue.isEmpty()); //false
console.log(dequeue.peekFront()); //yu
```



### 5、使用队列和双端队列解决问题

#### （1）循环队列-击鼓传花

```javascript
//使用普通队列结构
function passFlower(elementList,num){
	const queue = new Queue();
  const outOne = [];
  //首先把名字都加入队列；
  for(let i = 0; i < elementList.length; i++){
    queue.enqueue(elementList[i])
  }
  
  while(queue.size() > 1){
    //从头部提取后添加到尾部，执行要求次数
    for(let i = 0; i < num; i++){
    	queue.enqueue(queue.dequeue());
    }
    //从执行完的队列获取头部
    outOne.push(queue.dequeue());
  }
  return {
    eliminated: outOne,
    winner: queue.dequeue(),
  }
}

const names = ["zhang", "yu", "chen", "zhen", "shuai"];
const result = passFlower(names, 7);
result.eliminated.forEach((name) => {
  console.log(`${name}在击鼓传花中被淘汰`);
});
//chen在击鼓传花中被淘汰
//yu在击鼓传花中被淘汰
//shuai在击鼓传花中被淘汰
//zhen在击鼓传花中被淘汰
//胜利者是zhang
```



#### （2）回文检查

​	**<font color=deepred>回文是正反都能读通</font>的单词、词组、数或一系列字符的序列**。例如 madam 或 racecar。

最简单做法：字符串反向排列，然后和原值对比，相同就是一个回文。

数据结构最简单方法：双端队列，从头部取一个再从尾部取一个对比，如果相同就删去重新再执行，如果不同就不是回文。

```javascript
//使用双端队列
function palindromeChecker(aString) {
  //检查字符是否合法
  if (
    aString === undefined ||
    aString === null ||
    (aString !== null && aString.length === 0)
  ) {
    return false;
  }
  //创建双端队列
  const deque = new Dequeue();
  //移除空格，字母小写
  const lowerString = aString.toLocaleLowerCase().split(" ").join("");
  let firstChar, lastChar;
  //向队列添加元素
  for (let i = 0; i < lowerString.length; i++) {
    deque.addBack(lowerString.charAt(i));
  }
  //从前和后各取一个元素比较，不同就false，相同循环继续
  while (deque.size() > 1 && isEqual) {
    firstChar = deque.removeFront();
    lastChar = deque.removeBack();
    if (firstChar !== lastChar) {
      isEqual = false;
    }
  }
  return isEqual;
}

```



#### （3）JS任务队列
