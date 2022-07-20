# TypeScript

# 1、JS 的痛点

任何可以使用 JS 来实现的应用最终都会使用 JS 来实现。

但是 JS 也是有缺点的：

- ES5 之前使用 var 关键字关于作用域的问题
- 最初 JS 设计的数组类型并不是连续的内存空间
- 知道今天 JS 也没有加入类型检测机制



# 2、认识 TS

## （1）什么是 TS

### 1.类型系统按照<font color=red>类型检查的时机</font>分为动态类型和静态类型。

 动态类型是指在运行时才会进行类型检查，这种语言的类型错误往往会导致运行时错误。JavaScript 是一门解释型语言，没有编译阶段，所以它是动态类型

 静态类型是指编译阶段就能确定每个变量的类型，这种语言的类型错误往往会导致语法错误。TypeScript 在运行前需要先编译为 JavaScript，而在编译阶段就会进行类型检查，所以 **TypeScript 是静态类型**。



### 2.类型系统按照<font color=red>是否允许隐式类型转换</font>分为强类型和弱类型。

TS 和 JS 都是弱类型，Python 是强类型



### 3.总结

1. TypeScript 是添加了类型系统的 JavaScript，适用于任何规模的项目。
2. TypeScript 是一门静态类型、弱类型的语言。
3. TypeScript 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性。
4. TypeScript 可以编译为 JavaScript，然后运行在浏览器、Node.js 等任何能运行 JavaScript 的环境中。
5. TypeScript 拥有很多编译选项，类型检查的严格程度由你决定。
6. TypeScript 可以和 JavaScript 共存，这意味着 JavaScript 项目能够渐进式的迁移到 TypeScript。
7. TypeScript 增强了编辑器（IDE）的功能，提供了代码补全、接口提示、跳转到定义、代码重构等能力。
8. TypeScript 拥有活跃的社区，大多数常用的第三方库都提供了类型声明。
9. TypeScript 与标准同步发展，符合最新的 ECMAScript 标准（stage 3）。

 **TypeScript 是拥有类型的<font color=red>Javascript 超集</font>，可以编译成<font color=red>普通、干净、完整的 Javascript 代码</font>。**

**TS 的特点**

**采用 TS 的项目**

- Angular 源码在很早就使用 TypeScript 来进行了重写，并且开发 Angular 也需要掌握 TypeScript;

- Vue3 源码也采用了 TypeScript 进行重写，在前面阅读源码时我们看到大量 TypeScript 的语法;
- 包括目前已经变成最流行的编辑器 VSCode 也是使用 TypeScript 来完成的;
- 包括在 React 中已经使用的 ant-design 的 UI 库，也大量使用 TypeScript 来编写;
- 目前公司非常流行 Vue3+TypeScript、React+TypeScript 的开发模式;
- 包括小程序开发，也是支持 TypeScript 的;



# 3、大前端

大前端是一群最能或者最需要折腾的开发者：

 客户端开发者:从 Android 到 iOS，或者从 iOS 到 Android，到 RN，甚至现在越来越多的客户端开发者接触前端相关知识(Vue、React、Angular、小程序)

 前端开发者:从 jQuery 到 AngularJS，到三大框架并行:Vue、React、Angular，还有小程序，甚至现在也要 接触客户端开发(比如 RN、Flutter)

 目前又面临着不仅仅学习 ES 的特性，还要学习 TypeScript

 新框架的出现，又需要学习新框架的特性，比如 vue3.x、react18 等等



# 4、TS 的编译环境

TS 最终会被编译成 JS 来执行，所以需要搭建对应的环境：

```js
//安装命令，全局安装
npm install typescript -g
//查看安装
tsc --version
```

我们约定使用 TypeScript 编写的文件以 `.ts` 为后缀，用 TypeScript 编写 React 时，以 `.tsx` 为后缀。



# 5、TS 的运行环境

运行 TS 代码的三种方法

#### 方法一：使用默认的 tsc

通过 tsc 编译 TS 到 JS 代码，然后在浏览器或者 Node 环境下运行 JS 代码；

```js
tsc '文件名'
```

#### 方法二：使用 webpack

配置本地的 TS 编译环境和开启一个本地服务，可以直接运行在浏览器上；

#### 方法三：使用 ts-node

为 TS 的运行环境提供编译环境

```js
//全局安装
npm install -g typescript
npm install -g ts-node
//使用ts-node可以直接运行ts代码
ts-node '文件名'
```



# 6、类型

#### 基础类型：Boolean、Number、String、null、undefined 以及 ES6 的 Symbol 和 ES10 的 BigInt。

#### 引用类型：Array、Tuple 元组、object 包含{}、function。

#### 特殊类型：any、unknow、void、never、Enum 枚举。

#### 其他类型：类型推理、字面量类型、交叉类型。



## （1）字符串类型

使用 string 定义的

```tsx
let a: string = "123";
//也可以使用es6的字符串模板
let str: string = `dddd${a}`;
```



## （2）数字类型

不区分整数和浮点数，统一为 number 类型。支持十六进制、十进制、八进制和二进制。

```tsx
let notANumber: number = NaN; //Nan
let num: number = 123; //普通数字
let infinityNumber: number = Infinity; //无穷大
let decimal: number = 6; //十进制
let hex: number = 0xf00d; //十六进制
let binary: number = 0b1010; //二进制
let octal: number = 0o744; //八进制
```



## （3）布尔类型

```tsx
let booleand: boolean = true; //可以直接使用布尔值
let booleand2: boolean = Boolean(1); //也可以通过函数返回布尔值
```



## （4）空值类型

用 `void` 表示没有任何返回值的函数，主要用在不希望关心函数返回值的情况下。

```tsx
function voidFn(): void {
  console.log("test void");
}
```

void 也可以定义 undefined 和 null 类型

```tsx
let u: void = undefined;
let n: void = null;
```



## （5）Null 和 undefined 类型

```tsx
let u: undefined = undefined; //定义undefined
let n: null = null; //定义null
```

**<font color=deepred>null和undefined是所有类型的字类型</font>**



## （6）Any 和 unknown 类型

1. 没有强制限定类型，随时切换类型都可以

   ```tsx
   let anys: any = 123;
   anys = "123";
   anys = true;
   ```

2. 声明变量的时候没有指定任意类型默认为 any

   ```tsx
   let anys;
   anys = "123";
   anys = true;
   ```

3. 使用 any 意味着失去 TS 类型检测作用；

4. TS3.0 中引入 unknow 类型更安全，所有类型都可以分配给 unknown

   ```typescript
   let value: unknown; //unknown 可以定义任何类型的值
   value = true; // OK
   value = 42; // OK
   value = "Hello World"; // OK
   value = []; // OK
   value = {}; // OK
   value = null; // OK
   value = undefined; // OK
   value = Symbol("type"); // OK
   
   //unknown类型不能赋值给其他类型
   let names: unknown = "123";
   let names2: string = names;//报错
   
   //不能直接参与运算
   let a:unknow = 5;
   //console.log(a * 2)会报错
   //除非进行类型检查
   if (typeof a == "number") {
     console.log(a * 2);
   } else if (typeof a == "string") {
     console.log(a.length);
   }
   ```

5. unknow 类型在对象上不能调用属性和方法

   ```tsx
   let obj: unknow = { b: 1, ccc: (): number => 213 };
   obj.b;
   obj.ccc(); //报错
   
   let obj: any = { b: 1 };
   obj.a; //不报错
   ```

6. 如果没有对类型进行检查之前，不允许有任何操作，unknow是一个类型安全的any



## （7）对象和接口类型

### 对象类型

ts 中定义对象的方式用**interface**（接口），类似定义一种约束，**<font color=deepred>对象模版</font>**，让数据的结构满足约束

```tsx
interface Person {
  b: string;
  a: string;
}
const person: Person = {
  a: "213",
}; //报错，使用接口必须和接口约束保持一致
```

重名的接口可以合并使用，接口也支持继承：

```tsx
interface A {
  name: string;
}
interface A {
  age: number;
}
var x: A = { name: "xx", age: 20 };

interface A {
  name: string;
}
interface B extends A {
  age: number;
}
let obj: B = {
  age: 18,
  name: "string",
};
```



### 可选属性?操作符

```tsx
interface Person {
  b?: string;
  a: string;
}
const person: Person = { a: "123" };
```



### 任意属性 [propName: string]

**一旦定义了任意属性，那么<font color=deepred>确定属性和可选属性的类型都必须是它的类型的子集</font>**

```tsx
//定义[propName: string]: any，允许添加新的任意属性
interface Person {
  b?: string;
  a: string;
  [propName: string]: any;
}

const person: Person = {
  a: "213",
  c: "123",
};
```



### 只读属性 readonly

只读属性不允许重新赋值，会报错



## （8）数组类型

### 类型[]

```tsx
let arr: number[] = [1, 2, 3];
```



### 数组泛型

```tsx
let arr: Array<number> = [1, 2, 3, 4];
```



### 用接口表示数组

```tsx
interface NumberArray {
  [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```



### 多维数组

```tsx
let data: number[][] = [
  [1, 2],
  [3, 4],
];
```



### arguments 类数组

```tsx
function Arr(...args: any): void {
  console.log(arguments); //{ '0': 111, '1': 222, '2': 333 }
  //ts内置对象IArguments 定义
  let arr: IArguments = arguments;
}
Arr(111, 222, 333);

//其中 IArguments 是 TypeScript 中定义好了的类型，它实际上就是：
interface IArguments {
  [index: number]: any;
  length: number;
  callee: Function;
}
```



## （9）函数类型

### 1、函数的类型

<font color=red>**在 JS 中，有两种常见的定义函数的方式——函数声明和函数表达式**</font>

```typescript
//函数声明(Function Declaration)
function sum(x, y) {
  return x + y;
}
//函数表达式(Function Expressopn)
let sum = function (x, y) {
  return x + y;
};
```



#### （1）函数声明

一个函数有输入和输出，要在 TS 中进行约束，需要要把输入和输出都考虑到，函数声明类型定义较简单：

```typescript
function sum(x: number, y: number): number {
  return x + y;
}
```

其中，<font color=red>**输入多余的（或者少于要求的）参数，是不被允许的。**</font>

```typescript
function sum(x: number, y: number): number {
  return x + y;
}
sum(1, 2, 3); //Supplied parameters do not match any signature of call target.
sum(1); //Supplied parameters do not match any signature of call target.
```



#### （2）函数表达式

```typescript
//对函数表达式的定义：
let mySum = function(x: number, y: number):number {
  return x + y;
}
//这种写法可以通过编译，这种写法相当于只是对等号右侧的匿名函数进行了类型定义，左侧的mySum是通过赋值操作进行类型推断出来的。其完整写法如下：
let mySum: (x: number, y: number) => number = function (x: number,y: number): number {
  return x + y;
};

//注意，参数不能多传，也不能少传 必须按照约定的类型来
const fn = (name: string, age:number): string => {
    return name + age
}
fn('张三',18)
```

注意 ⚠️：在 TS 类型定义中`=>`用来<font color=red>表示函数的定义</font>，左边是输入类型，需要用括号括起来，右边是输出类型；在 ES6 中`=>`<font color=red>表示箭头函数。</font>



#### （3）用接口定义函数的形状

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function (source: string, subString: string) {
  return source.search(Substring) !== -1;
};

//定义参数 num 和 num2  ：后面定义返回值的类型
interface Add {
  (num: number, num2: number): number;
}
const fn: Add = (num: number, num2: number): number => {
  return num + num2;
};
fn(5, 5);
```

 采用函数表达式接口定义函数的方式时，对等号左侧进行类型限制，可以保证以后对函数名赋值时保证参数个数、参数类型、返回值类型不变。



#### （4）可选参数

与接口中的可选属性类似，用`？`表示可选的参数

```typescript
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + " " + lastName;
  } else {
    return firstName;
  }
}
let tomcat = buildName("tom", "cat");
let tom = buildName("tom");
```

要注意的是可选参数必须在必须参数后面，<font color=red>**可选参数后面不允许再出现必须参数了**</font>

```typescript
function buildName(firstName?: string, lastName: string) {
  if (firstName) {
    return firstName + " " + lastName;
  } else {
    return lastName;
  }
}
let tomcat = buildName("Tom", "Cat");
let tom = buildName(undefined, "Tom");
//报错：A required parameter cannot follow an optional parameter.
```



#### （5）参数默认值

TS 会将添加了默认值的参数识别为可选参数：

```typescript
function buildName(firstName: string, lastName: string = "Cat") {
  return firstName + " " + lastName;
}
let tomcat = buildName("Tom", "Cat"); //Tom Cat
let tom = buildName("Tom"); //Tom Cat
```

此时不受可选参数必须在必需参数后面的限制

```typescript
function buildName(firstName: string = "Tom", lastName: string) {
  return firstName + " " + lastName;
}
let tomcat = buildName("Tom", "Cat"); //Tom Cat
let cat = buildName(undefined, "Cat"); //Tom Cat
```



#### （6）剩余参数

ES6 中，可以使用`...rest`的方式获取函数中的剩余参数:

```typescript
//定义剩余参数
const fn = (array: number[], ...items: any[]): any[] => {
  return items;
};
let a: number[] = [1, 2, 3];
fn(a, "4", "5", "6"); //返回['4','5','6']
```

⚠️ 注意：剩余参数只能是最后一个参数。



#### （7）重载

重载是方法名字相同，而参数不同，返回类型可以相同也可以不同。给一个函数提供多个函数类型定义。

下面一个例子，实现一个 reverse 函数，实现输入内容的反转，不管是数字还是字符串。

利用联合类型，可以这么实现

```typescript
function reverse(x: number | string): number | string | void {
  if (typeof x === "number") {
    return Number(x.toString().split("").reverse().join(""));
  } else if (typeof x === "string") {
    return x.split("").reverse().join("");
  }
}
```

但是这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也为数字，输入为字符串的时候，输出也应该为字符串。

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string | void {
  if (typeof x === "number") {
    return Number(x.toString().split("").reverse().join(""));
  } else if (typeof x === "string") {
    return x.split("").reverse().join("");
  }
}
```

 复定义多次函数 reverse，前几次都是函数定义，最后一次是函数实现。TS 会优先从最前面的的函数定义开始匹配，所以多个函数定义如果包含关系，需要优先把精确的定义写在前面。



## （10）类型断言｜联合类型｜交叉类型

### 联合类型

取值可以为多种类型中的一种

```tsx
let myFavoriteNumber: string | number;
myFavoriteNumber = "seven";
myFavoriteNumber = 7;
```

函数使用联合类型

```tsx
const fn = (something: number | boolean): boolean => {
  return !!something;
};
```

当 ts 不确定一个联合类型的变量类型时，只能访问联合类型的所有类型里共有的属性或方法。



### 交叉类型

多种类型的集合，联合对象将具有所有联合类型的所有成员。

```tsx
interface People {
  age: number;
  height: number;
}
interface Man {
  sex: string;
}
const jiaocha = (man: People & Man) => {
  console.log(man.age); //18
  console.log(man.height); //180
  console.log(man.sex); //male
};
jiaocha({ age: 18, height: 180, sex: "male" });
```



### 类型断言

**语法为 值 `as` 类型**

```tsx
interface A {run:string}
interface B {build:string}
const fn = (type: A | B)string => {
  return type.run	//类型“A | B”上不存在属性“run”
}

//应该写为
interface A {run:string}
interface B {build:string}
const fn = (type: A | B): string => {
       return (type as A).run
}
```

类型断言只能够「欺骗」TypeScript 编译器，无法避免运行时的错误，反而滥用类型断言可能会导致运行时错误。

- 使用 any 临时断言

```tsx
window.abc = 123; //报错
(window as any).abc = 123; //不报错
//可以使用any临时断言在 any 类型的变量上，访问任何属性都是允许的。
```

- as const

字面值断言，与 cosnt 直接定义常量是有区别的

```tsx
const names = "zhang";
names = "hh"; //无法修改
let names2 = "zhang" as const;
names2 = "aa"; //无法修改
```

```tsx
let a1 = [10, 20] as const;
const a2 = [10, 20];
a1.unshift(30); // 错误，此时已经断言字面量为[10, 20],数据无法做任何修改
a2.unshift(30); // 通过，没有修改指针
```



### 类型断言不具影响力

将 something 断言为 boolean 虽然可以通过编译，但是并没有什么用 并不会影响结果, 因为编译过程中会删除类型断言

``` tsx
function toBoolean(something:any):boolean{
  return something as boolean
}
toBoolean(1);	//返回值为1
```



## （11）内置对象

JS 中有很多内置对象，可以在 TS 中当作定义好的类型。



### ECMAScript 内置对象

**`Boolean`、`Number`、`string`、`RegExp`、`Date`、`Error`**

```tsx
let b: Boolean = new Boolean(1);
console.log(b); //Boolean{true}
let n: Number = new Number(true);
console.log(n); //Number{1}
let s: String = new String("内置对象");
console.log(s); //String{'内置对象'}
let d: Date = new Date();
console.log(d); //当前时间GMT
let r: RegExp = /^1/;
console.log(r); // /^1/
let e: Error = new Error("error!");
console.log(e); //报错提示
```



### DOM 和 BOM 的内置对象

**`Document`、`HTMLElement`、`Event`、`NodeList` 等。**

```tsx
let body: HTMLElement = document.body;

let allDiv: NodeList = document.querySelectorAll("div");

//读取div 这种需要类型断言 或者加个判断应为读不到返回null
let div: HTMLElement = document.querySelector("div") as HTMLDivElement;

document.addEventListener("click", function (e: MouseEvent) {
  // Do something
});

//dom元素的类型声明，可以直接使用
interface HTMLElementTagNameMap {
  a: HTMLAnchorElement;
  abbr: HTMLElement;
  address: HTMLElement;
  applet: HTMLAppletElement;
  area: HTMLAreaElement;
  article: HTMLElement;
  aside: HTMLElement;
  audio: HTMLAudioElement;
  b: HTMLElement;
  base: HTMLBaseElement;
  bdi: HTMLElement;
  bdo: HTMLElement;
  blockquote: HTMLQuoteElement;
  body: HTMLBodyElement;
  br: HTMLBRElement;
  button: HTMLButtonElement;
  canvas: HTMLCanvasElement;
  caption: HTMLTableCaptionElement;
  cite: HTMLElement;
  code: HTMLElement;
  col: HTMLTableColElement;
  colgroup: HTMLTableColElement;
  data: HTMLDataElement;
  datalist: HTMLDataListElement;
  dd: HTMLElement;
  del: HTMLModElement;
  details: HTMLDetailsElement;
  dfn: HTMLElement;
  dialog: HTMLDialogElement;
  dir: HTMLDirectoryElement;
  div: HTMLDivElement;
  dl: HTMLDListElement;
  dt: HTMLElement;
  em: HTMLElement;
  embed: HTMLEmbedElement;
  fieldset: HTMLFieldSetElement;
  figcaption: HTMLElement;
  figure: HTMLElement;
  font: HTMLFontElement;
  footer: HTMLElement;
  form: HTMLFormElement;
  frame: HTMLFrameElement;
  frameset: HTMLFrameSetElement;
  h1: HTMLHeadingElement;
  h2: HTMLHeadingElement;
  h3: HTMLHeadingElement;
  h4: HTMLHeadingElement;
  h5: HTMLHeadingElement;
  h6: HTMLHeadingElement;
  head: HTMLHeadElement;
  header: HTMLElement;
  hgroup: HTMLElement;
  hr: HTMLHRElement;
  html: HTMLHtmlElement;
  i: HTMLElement;
  iframe: HTMLIFrameElement;
  img: HTMLImageElement;
  input: HTMLInputElement;
  ins: HTMLModElement;
  kbd: HTMLElement;
  label: HTMLLabelElement;
  legend: HTMLLegendElement;
  li: HTMLLIElement;
  link: HTMLLinkElement;
  main: HTMLElement;
  map: HTMLMapElement;
  mark: HTMLElement;
  marquee: HTMLMarqueeElement;
  menu: HTMLMenuElement;
  meta: HTMLMetaElement;
  meter: HTMLMeterElement;
  nav: HTMLElement;
  noscript: HTMLElement;
  object: HTMLObjectElement;
  ol: HTMLOListElement;
  optgroup: HTMLOptGroupElement;
  option: HTMLOptionElement;
  output: HTMLOutputElement;
  p: HTMLParagraphElement;
  param: HTMLParamElement;
  picture: HTMLPictureElement;
  pre: HTMLPreElement;
  progress: HTMLProgressElement;
  q: HTMLQuoteElement;
  rp: HTMLElement;
  rt: HTMLElement;
  ruby: HTMLElement;
  s: HTMLElement;
  samp: HTMLElement;
  script: HTMLScriptElement;
  section: HTMLElement;
  select: HTMLSelectElement;
  slot: HTMLSlotElement;
  small: HTMLElement;
  source: HTMLSourceElement;
  span: HTMLSpanElement;
  strong: HTMLElement;
  style: HTMLStyleElement;
  sub: HTMLElement;
  summary: HTMLElement;
  sup: HTMLElement;
  table: HTMLTableElement;
  tbody: HTMLTableSectionElement;
  td: HTMLTableDataCellElement;
  template: HTMLTemplateElement;
  textarea: HTMLTextAreaElement;
  tfoot: HTMLTableSectionElement;
  th: HTMLTableHeaderCellElement;
  thead: HTMLTableSectionElement;
  time: HTMLTimeElement;
  title: HTMLTitleElement;
  tr: HTMLTableRowElement;
  track: HTMLTrackElement;
  u: HTMLElement;
  ul: HTMLUListElement;
  var: HTMLElement;
  video: HTMLVideoElement;
  wbr: HTMLElement;
}
```



### 定义 promise

如果不指定返回的类型 TS 无法推断出返回类型，函数定义返回 promise 语法规则：Promise<T>类型

```tsx
function promise(): Promise<number> {
  return new Promise<number>((resolve, reject) => {
    resolve(1);
  });
}
promise().then((res) => {
  console.log(res);
});
```



### TS 核心库

[TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中定义了所有浏览器环境需要用到的类型，并且是预置在 TypeScript 中的。常见体现在报错和提示中。TS 核心库的定义中不包含 Node.js 部分。



### 用 TS 写 Node.js

node.js 不是内置对象的一部分，如果用 ts 写 node，需要引入第三方声明文件：

```tsx
npm install @type/node --save-dev
```



## （12）Class 类

### 类的概念

- 类：定义了一些事物的抽象特点，包含属性和方法；
- 对象：类的实例，通过 `new` 生成；
- 面向对象的三大特性：封装、继承、多态；
- 封装：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据；
- 继承：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性；
- 多态：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `Cat` 和 `Dog` 都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`；
- 存取器：用以改变属性的读取和赋值行为；
- 修饰符：修饰符是一些关键字，用于限定成员或类型的性质。比如 `public` 表示公有属性或方法；
- 抽象类：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现；
- 接口：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现。一个类只能继承自另一个类，但是可以实现多个接口；



### 类的属性和方法

```tsx
//使用class定义类、constructor定义构造函数。通过 new 生成新实例的时候，会自动调用构造函数。
class Animal {
  public name;
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    return `我的名字叫${this.name}`;
  }
}
let a = new Animal("老八");
console.log(a.sayHi()); //我的名字叫老八
```



### TS 中定义类

在 TS 中是不允许直接在 constructor 定义变量的，需要在 constructor 上先声明

![image-20220506185746736](https://raw.githubusercontent.com/Rainchen0504/picture/master/202205061857200.png)

如果定义了变量不使用也会报错

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202205071424046.png" alt="image-20220506185955859" style="zoom:67%;" />

通常给个默认值或赋值

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202205071424075.png" alt="image-20220506190134347" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202205071424881.png" alt="image-20220506190226713" style="zoom:67%;" />



### 类的修饰符

修饰符有三个：private、public、protected

- 使用**public 修饰符**，可以让你定义的变量内部访问也可以外部访问如果不写默认就是 public。
- 使用 **private 修饰符**，代表定义的变量私有的**只能在内部访**问不能在外部访问。
- 使用 **protected 修饰符**，代表定义的变量私有的**只能在内部和继承的子类中访问**不能在外部访问。

```tsx
class Person {
  public name: string;
  private age: number;
  protected some: any;
  constructor(name: string, age: number, some: any) {
    this.name = name;
    this.age = age;
    this.some = some;
  }
  run() {}
}

class Man extends Person {
  constructor() {
    super("张", 25, 1);
    console.log(this.some); //1
  }
  create() {}
}
let yuchen = new Person("张雨晨", 24, 2);
console.log(yuchen.name); //张雨晨
console.log(yuchen.age); //报错
console.log(yuchen); //{name:"张雨晨",age:24,some:2}

let man = new Man();
console.log(man); //{name:'张',age:25,some:1}
console.log(man.some); //报错
```



### static 静态属性和静态方法

用 static 定义的属性不可以通过 this 去访问**只能通过类名去调用**；

static 静态函数同样也是不能通过 this 去调用也是**通过类名去调用**；

但是，如果两个函数都是 static 静态方法是可以通过 this 互相调用的。

![image-20220506192319368](https://raw.githubusercontent.com/Rainchen0504/picture/master/202205071423834.png)



### interface 定义类

一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口（interfaces），用 `implements` 关键字来实现。

```typescript
interface PersonClass {
  get(type: boolean): boolean;
}

interface PersonClass2 {
  set(): void;
  asd: string;
}

class A {
  name: string;
  constructor() {
    this.name = "123";
  }
}

class Person extends A implements PersonClass, PersonClass2 {
  asd: string;
  constructor() {
    super();
    this.asd = "123";
  }
  get(type: boolean) {
    return type;
  }
  set() {}
}
```



### 抽象类

`abstract` 用于定义抽象类和其中的抽象方法。如果写的类实例化之后毫无用处此时可以把他定义为抽象类，或者把它作为一个基类， 通过继承一个派生类去实现基类的一些方法。

**抽象类无法被实例化**，使用 new 抽象实例会报错。

**抽象类中的抽象方法必须被子类实现**，子类继承抽象类后不使用抽象类的方法也会报错。

```typescript
abstract class A {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  //定义getName抽象方法但不能实现
  abstract getName(): string;
}
let a = new A()//报错，抽象类不能实例化

class B extends A {
  constructor() {
    super("小满");
  }
  //抽象方法getName必须在派生类实现
  getName(): string {
    return this.name;
  }
}
let b = new B();//不报错，派生类可以实例化
console.log(b.getName());
```



## （13）元祖类型

### 元组就是数组的变种

数组合并了相同类型的对象，而**元组合并了不同类型的对象**。
元组与集合的不同之处在于，元组中的元素类型可以是不同的，而且数量固定。元组的好处在于可以把多个元素作为一个单元传递。如果一个方法需要返回多个值，可以把这多个值作为元组返回，而不需要创建额外的类来表示。

```typescript
let arr: [number, string] = [1, "string"];
```

当赋值或者访问一个已知索引的元素时，会得到正确的类型:

```typescript
let arr: [number, string] = [1, "string"];
arr[0].length; //error,数字没有length
arr[1].length; //success
```



### 越界元素

```typescript
let arr: [number, string] = [1, "string"];
arr.push(true); //报错，只能是定义过的联合类型number|string
```

越界的元素的类型被限制为联合类型，即元组中已经定义的类型。



### 应用场景

常见应用在 execl 返回的数据

```typescript
let excel: [string, string, number, string][] = [
  ["title", "name", 1, "123"],
  ["title", "name", 1, "123"],
  ["title", "name", 1, "123"],
];
```



## （14）枚举类型

枚举类型用于取值被限定在一定范围内的场景，通过**enum 关键字**定义枚举。

### 数字枚举

```typescript
//基础枚举
enum Types {
  Red = 0,
  Green = 1,
  BLue = 2,
}
//增长枚举，其余值会从1开始增长
enum Types {
  Red = 1,
  Green,
  BLue,
}
```



### 字符串枚举

在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化。

```typescript
enum Types {
  Red = "red",
  Green = "green",
  BLue = "blue",
}
```

由于字符串枚举没有自增长的行为，字符串枚举可以很好的序列化。



### 异构枚举

枚举可以混合字符串和数字成员

```typescript
enum Types {
  No = "No",
  Yes = 1,
}
```



### 接口枚举

声明对象的时候要遵循接口规则

```typescript
enum Types {
  yyds,
  dddd,
}
interface A {
  red: Types.yyds;
}

let obj: A = {
  red: Types.yyds,
};
```



### 常数枚举

常数枚举是使用`const enum`定义的枚举类型，let 和 var 都不允许的声明。常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员，如果包含计算成员也就是使用计算符的成员时会报错。
const 声明的枚举会被编译成常量，普通声明的枚举编译完后是个对象。这样避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问。

```typescript
const enum Directions {
  Up,
  Down,
  Left,
  Right,
}
let directions = [
  Directions.Up,
  Directions.Down,
  Directions.Left,
  Directions.Right,
];
//const声明编译后
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
//普通声明编译后会是一个复杂对象
```



### 外部枚举

外部枚举是使用`declare enum`定义的枚举类型，

```typescript
const enum Directions {
  Up,
  Down,
  Left,
  Right,
}
let directions = [
  Directions.Up,
  Directions.Down,
  Directions.Left,
  Directions.Right,
];
//编译结果
var directions = [
  Directions.Up,
  Directions.Down,
  Directions.Left,
  Directions.Right,
];
```

外部枚举与声明语句一样，常出现在声明文件中，同时使用 declare 和 const 也是可以的。

```typescript
declare const enum Directions {
  Up,
  Down,
  Left,
  Right,
}
let directions = [
  Directions.Up,
  Directions.Down,
  Directions.Left,
  Directions.Right,
];
//编译结果
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```



## （15）类型推论｜类型别名

### 类型推论

TS 会在没有明确指定类型的时候推测出一个类型，这就是类型推论。

```typescript
let myFav = "six";
myFav = 6; //报错，不能赋值给别的类型
```

上面的代码等价于

```typescript
let myFav: string = "six";
myFav = 6; //6是number不是string
```

**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成`any`类型**而完全不被类型检查。

```typescript
let zhang;
zhang = 123;
zhang = "chenge";
zhang = true;
```



### 类型别名

**类型别名就是给一个类型起一个新的名字**（使用 type 关键字定义）。

- 定义类型别名

```typescript
type str = string;
let s: str = "我是晨哥";
```

- 定义函数别名

```typescript
type str = () => string;
let s: str = () => "我是雨晨";

type a = (a:number,b:number) => number;
let add:a = (x:number,y:number):number => {return x + y};
```

- 定义联合类型别名

```typescript
type str = string | number;
let s: str = 123;
let s2: str = "123";
```

- 定义值别名

```typescript
type value = boolean | 0 | "123";
let s: value = true; //变量s的值，只能是value定义的值
```



## （16）never 类型

TS 使用 never 类型表示不应该存在的状态。

```typescript
// 必定抛出异常，error将不会有返回值
function error(message: string): never {
  throw new Error(message);
}

//存在死循环，loop将不会有返回值
function loop(): never {
  while (true) {}
}
```



### never 和 void 的区别

```typescript
//void类型用于没有返回值，本身不会出错
function Void(): void {
  console.log();
}

//never用于只抛出异常不会有返回值
function Never(message: string): never {
  throw new Error(message);
}
```



### never 使用场景

举个例子：

```typescript
interface A {
  type:"foo"
}
interface B {
  type:"bar"
}
type All = A | B;
function handleValue(val:All){1
  switch(val.type){
      case:"foo":
      	break;
      case:"bar":
      	break;
    	default:	//兜底逻辑，进来基本是程序异常
      	const exhaustiveCheck:never = val;
      	break
  }
}
```

由于任何类型都不能赋值给 `never` 类型的变量，所以当存在进入 `default` 分支的可能性时，TS 的类型检查会及时发现这个问题。



## （17）symbol 类型

symbol 类型的值是通过**Symbol 构造函数**创建的。它可以传递参数作为唯一的标识，只支持 string 和 number 类型的参数。

```typescript
let sym1 = Symbol();
let sym2 = Symbol("key"); //可选的字符串key
```



### Symbol 值是唯一的

```typescript
const s1 = Symbol();
const s2 = Symbol();
// s1 === s2 =>false
```



### 用作对象属性的键

```typescript
let sym = Symbol();
let obj = {
  [sym]: "value",
};
console.log(obj[sym]); // "value"
```

**使用 Symbol 定义的属性，无法通过普通的遍历方法拿到**

1. for in 遍历
2. Obejct.keys 遍历
3. Object.getOwnPropertyNames()遍历

要想获取需要通过以下两种方法：

```typescript
//拿到具体的symbol 属性,对象中有几个就会拿到几个
Object.getOwnPropertySymbols(对象);

//es6 的 Reflect 拿到对象的所有属性
Reflect.ownKeys(对象);
```



## （18）泛型

泛型是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。



### 函数泛型

举个例子：两个函数一个是数字类型函数，另一个是字符串类型函数

```typescript
function num(a: number, b: number): Array<number> {
  return [a, b];
}
num(1, 2);

function str(a: string, b: string): Array<string> {
  return [a, b];
}
str("泛型", "优化");
```

上面的代码存在缺陷没有准确定义返回的类型，实际的结果中数组每一项都应该和输入的值的类型保持一致。

**使用泛型优化**

```typescript
function Demo<T>(a: T, b: T): Array<T> {
  return [a, b];
}
//T表任意输入的类型
Demo<number>(1, 2);
```

当然，可以使用不同的泛型参数名，只要数量和使用方式对应即可：

```typescript
function Sub<T, U>(a: T, b: U): Array<T | U> {
  const params: Array<T | U> = [a, b];
  return params;
}
Sub<Boolean, number>(true, 1);
```



### 定义泛型接口

声明接口的时候在名字后面加一个`<参数>`，使用时传递类型，使用接口的方式来定义一个函数需要符合的形状。

```typescript
interface Demo<T> {
  (arg: T): T;
}

function fn<T>(arg: T): T {
  return arg;
}

let result: Demo<number> = fn;
result(123);
```



### 对象字面量泛型

```typescript
let foo: { <T>(arg: T): T };
foo = function <T>(arg: T): T {
  return arg;
};
foo(123);
```



### 泛型约束

**在函数内部使用泛型变量的时候，由于事先不知道该变量的类型，所以不能随便操作其属性和方法**

```typescript
function getLength<T>(arg: T) {
  return arg.length;
} //这里会报错，因为变量类型不确定，泛型T不一定包含属性length
```

因此需要对使用的泛型进行约束，使其为具有 length 属性的类型。

```typescript
interface len {
  length: number;
}
//设置T时，必须包含length属性
function getLength<T extends Len>(arg: T) {
  return arg.length;
}
getLength<string>("123");
```



### 使用 keyof 约束对象

先定义 T 类型；使用 keyof 操作符获取 T 类型的所有键，返回联合类型；利用 extends 约束 K 类型必须为 T 键的联合类型的子类型。

```typescript
function prop<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

let o = { a: 1, b: 2, c: 3 };
console.log("6", prop(o, "a")); //1
```



### 泛型类

与泛型接口类似，泛型也可以用于类的类型定义中

```typescript
class Sub<T> {
  attr: T[] = [];
  add(a: T): T[] {
    return [a];
  }
}

let s = new Sub<number>();
s.attr = [1, 2, 3];
s.add(123);

let str = new Sub<string>();
str.attr = ["1", "2", "3"];
str.add("123");
```



# 7、tsconfig.json 配置文件

该文件通过 tsc --init 命令生成

```json
"compilerOptions": {
  "incremental": true, // TS编译器在第一次编译之后会生成一个存储编译信息的文件，第二次编译会在第一次的基础上进行增量编译，可以提高编译的速度
  "tsBuildInfoFile": "./buildFile", // 增量编译文件的存储位置
  "diagnostics": true, // 打印诊断信息
  "target": "ES5", // 目标语言的版本
  "module": "CommonJS", // 生成代码的模板标准
  "outFile": "./app.js", // 将多个相互依赖的文件生成一个文件，可以用在AMD模块中，即开启时应设置"module": "AMD",
  "lib": ["DOM", "ES2015", "ScriptHost", "ES2019.Array"], // TS需要引用的库，即声明文件，es5 默认引用dom、es5、scripthost,如需要使用es的高级版本特性，通常都需要配置，如es8的数组新特性需要引入"ES2019.Array",
  "allowJS": true, // 允许编译器编译JS，JSX文件
  "checkJs": true, // 允许在JS文件中报错，通常与allowJS一起使用
  "outDir": "./dist", // 指定输出目录
  "rootDir": "./", // 指定输出文件目录(用于输出)，用于控制输出目录结构
  "declaration": true, // 生成声明文件，开启后会自动生成声明文件
  "declarationDir": "./file", // 指定生成声明文件存放目录
  "emitDeclarationOnly": true, // 只生成声明文件，而不会生成js文件
  "sourceMap": true, // 生成目标文件的sourceMap文件
  "inlineSourceMap": true, // 生成目标文件的inline SourceMap，inline SourceMap会包含在生成的js文件中
  "declarationMap": true, // 为声明文件生成sourceMap
  "typeRoots": [], // 声明文件目录，默认时node_modules/@types
  "types": [], // 加载的声明文件包
  "removeComments":true, // 删除注释
  "noEmit": true, // 不输出文件,即编译后不会生成任何js文件
  "noEmitOnError": true, // 发送错误时不输出任何文件
  "noEmitHelpers": true, // 不生成helper函数，减小体积，需要额外安装，常配合importHelpers一起使用
  "importHelpers": true, // 通过tslib引入helper函数，文件必须是模块
  "downlevelIteration": true, // 降级遍历器实现，如果目标源是es3/5，那么遍历器会有降级的实现
  "strict": true, // 开启所有严格的类型检查
  "alwaysStrict": true, // 在代码中注入'use strict'
  "noImplicitAny": true, // 不允许隐式的any类型
  "strictNullChecks": true, // 不允许把null、undefined赋值给其他类型的变量
  "strictFunctionTypes": true, // 不允许函数参数双向协变
  "strictPropertyInitialization": true, // 类的实例属性必须初始化
  "strictBindCallApply": true, // 严格的bind/call/apply检查
  "noImplicitThis": true, // 不允许this有隐式的any类型
  "noUnusedLocals": true, // 检查只声明、未使用的局部变量(只提示不报错)
  "noUnusedParameters": true, // 检查未使用的函数参数(只提示不报错)
  "noFallthroughCasesInSwitch": true, // 防止switch语句贯穿(即如果没有break语句后面不会执行)
  "noImplicitReturns": true, //每个分支都会有返回值
  "esModuleInterop": true, // 允许export=导出，由import from 导入
  "allowUmdGlobalAccess": true, // 允许在模块中全局变量的方式访问umd模块
  "moduleResolution": "node", // 模块解析策略，ts默认用node的解析策略，即相对的方式导入
  "baseUrl": "./", // 解析非相对模块的基地址，默认是当前目录
  "paths": { // 路径映射，相对于baseUrl
    // 如使用jq时不想使用默认版本，而需要手动指定版本，可进行如下配置
    "jquery": ["node_modules/jquery/dist/jquery.min.js"]
  },
  "rootDirs": ["src","out"], // 将多个目录放在一个虚拟目录下，用于运行时，即编译后引入文件的位置可能发生变化，这也设置可以虚拟src和out在同一个目录下，不用再去改变路径也不会报错
  "listEmittedFiles": true, // 打印输出文件
  "listFiles": true// 打印编译的文件(包括引用的声明文件)
}

// 指定一个匹配列表（属于自动指定该路径下的所有ts相关文件）
"include": [
   "src/**/*"
],
// 指定一个排除列表（include的反向操作）
 "exclude": [
   "demo.ts"
],
// 指定哪些文件使用该配置（属于手动一个个指定文件）
 "files": [
   "demo.ts"
]
```

### 常用属性

#### include

指定编译文件默认是编译当前目录下所有的 ts 文件

#### exclude

指定排除的文件

#### target

指定编译 js 的版本例如 es5 es6

#### allowJS

是否允许编译 js 文件

#### removeComments

是否在编译过程中删除文件中的注释

#### rootDir

编译文件的目录

#### outDir

输出的目录

#### sourceMap

代码源文件

#### strict

严格模式

#### module

默认 common.js 可选 es6 模式 amd umd 等

