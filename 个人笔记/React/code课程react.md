# 一、基础知识

<font color=deepred>**用于构建用户界面的 JavaScript 库**</font>

## 1、特点

### （1）声明式编程

React的特点是声明式编程。只需要维护自己的状态，当状态改变时，React可以根据最新的状态去渲染UI界面。

### （2）组件化开发

将复杂的界面拆分成一个个小的组件

### （3）多平台适配

- 13年发布最初为了开发Web页面
- 15年推出ReactNative，用于开发移动端平台；
- 17年推出ReactVR，用于开发虚拟现实Web应用程序；



## 2、开发依赖

### （1）依赖库

1. <font color=yellow>`react`</font>：包含`react`所必须的核心代码；
2. <font color=yellow>`react-dom`</font>:`react`渲染在不同平台所需要的核心代码；
3. <font color=yellow>`babel`</font>:将jsx转换成`React`代码的工具；



### （2）拆分原因

- `react`包中包含了`react web`和`react-native`所共同拥有的核心代码;
- `react-dom`针对`web`和`native`所完成的作用不同：
  - web端：`react-dom`会将jsx渲染成真实DOM，显示在浏览器中；
  - native端：`react-dom`会将jsx渲染成原生的控件（比如Android中的Button，IOS中的UIButton）



### （3）babel和React关系

`babel`是目前前端使用最广泛的编译器。常用的功能就是将ES6的语法转换成ES5的语法。

1. 默认情况下`React`<font color=deepred>可以不使用`babel`</font>；
2. 可以使用`React.createElement`编写代码，但是<font color=deepred>非常繁琐且可读性差</font>；
3. 编写jsx语法时可以让babel帮助<font color=deepred>转换成`React.createElement`</font>；



## 3、依赖引入

方式一：直接CDN引入

```html
<!-- 其中crossorigin属性是为了拿到跨域脚本的错误信息 -->
<script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

方式二：下载后添加本地依赖

方式三：通过npm管理（脚手架）



## 4、基础使用

在界面上显示一段文字

1. 第一步，引入依赖；
2. 第二步：编写`React`的`script`代码，必须在`script`标签上添加`type="text/babel"`，可以让`babel`解析`jsx`的语法；
3. 第三步：使用`ReactDOM.createRoot`函数，用于创建一个`React`根元素；
4. 第四步：使用`render`函数将结构渲染在根元素上，可以使用`{}`语法引入外部的变量或表达式；

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="root"></div>
    <div id="app"></div>
    <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

    <script type="text/babel">
      const message = "Hello World";
      const root = ReactDOM.createRoot(document.querySelector("#root"));
      root.render(<h2>{message}</h2>);
    </script>
  </body>
</html>
```



## 5、组件化开发

整个逻辑可以看作是一个整体，可以将其封装成一个组件。可以将逻辑封装到一个组件中再传递给`ReactDOM.render`函数作为参数。

### （1）封装类组件

1. <font color=deepred>定义一个类</font>（类名、组件名必须大写，小写会被认为是HTML元素），继承自`React.Component`;
2. <font color=deepred>实现当前组件的`render`函数</font>，render中返回jsx的渲染内容；

```html
<script type="text/babel">
	class App extends React.Component {
		  constructor() {
        super()
        this.state = {
          message: "Hello World"
        }
        // 对需要绑定的方法, 提前绑定好this
        this.btnClick = this.btnClick.bind(this)
      }
      btnClick(){
      	this.setState({
      		message:"hello react"
      	})
      }
      render(){
      	return (
      		<div>
  					<h2>{this.state.message}</h2>
  					<button onClick={this.btnClick}>修改文本</button>
  				</div>
      	)
      }
	}
	const root = ReactDOM.createRoot(document.querySelector("#root"))
  root.render(<App/>)
</script>
```



### （2）数据依赖

组件中的数据可以分为两类：

- **参与界面更新**的数据：当数据变量时，需要更新组件渲染的内容；
- **不参与界面更新**的数据：当数据变量时，不需要更新将组件渲染的内容；

参与**界面更新的数据**可以称之为<font color=deepred>参与数据流</font>，可以在构造函数中`this.state={数据}`来定义，当数据发生变化时可以调用`this.setState`更新数据，通知React进行`update`操作重新调用`render`函数，使用最新的数据来渲染页面。

```js
class App extends React.Component {
  constructor(){
    super()
    //定义数据
    this.state = {
      message:"我是数据"
    }
  }
  
  changeMessage(){
    //修改数据
    this.setState({
      message:"修改后的message"
    })
  }
}
```



### （3）事件绑定

在类中直接定义一个函数绑定到元素的点击事件上，这个函数的`this`指向结果为`undefined`。

原因分析：

​	DOM操作中监听点击时`this`是当前节点对象（比如说按钮等元素对象），但是React并不是直接渲染成真实的DOM，编写的button只是一个语法糖，它的本质是`React`的`Element`对象，所以react在执行函数时并没有绑定this，默认情况下this就是`undefined`；

​	绑定的目的是为了使用当前对象，就必须拿到当前对象的this，两种做法：方法一通过bind显式绑定this；方法二使用箭头函数即可。

```js
// 1.定义App根组件
class App extends React.Component {
  constructor() {
    super();
    this.state = {
      message: "Hello World",
    };
  }
  //默认绑定this结果为undefined
  btnClick() {
    console.log(this);
    this.setState({
      message: "hellow react",
    });
  }
  //显示绑定，this为当前实例对象，App实例
  btn1Click() {
    console.log(this);
    this.setState({
      message: "hellow react",
    });
  }
  //箭头函数指向调用者，App实例
  btn2Click() {
    console.log(this);
    this.setState({
      message: "hellow react",
    });
  }
  render() {
    const { message } = this.state;
    return (
      <div>
      	<h2>{message}</h2>
      	<button onClick={this.btnClick}>事件绑定</button>
				<button onClick={this.btn1Click.bind(this)}>方式一</button>
				<button onClick={() => this.btn2Click()}>方式二</button>
			</div>
		);
	}
}
// 2.创建root并且渲染App组件
const root = ReactDOM.createRoot(document.querySelector("#root"));
root.render(<App />);
```



# 二、JSX语法

## 1、JSX说明

`JSX`是一种`JavaScript`的语法扩展，也可以称为`JavaScript XML`。用于描述UI界面和`JavaScript`融合使用。



## 2、书写规范

- 顶层只能有一个根元素，所以每次都在外层包裹一个`div`元素（或者使用`Fragment`）；
- 通常在`JSX`外层包裹一个小括号`()`，`JSX`可以进行换行书写；
- `JSX`中标签可以是单标签，也可以是双标签；



## 3、JSX使用

### （1）注释

```jsx
{/*这是注释的写法*/}
```

### （2）变量

- 当变量为`Number、String、Array`类型时，可以直接显示；
- 当变量为`null、undefined、Boolean`类型时，内容为空，如果要显示需要<font color=deepred>转成字符串</font>；
- Object对象类型不能作为子元素；

### （3）绑定属性

任意元素的title属性、img标签的src属性、a元素的href属性、元素的class属性、内联样式style属性

### （4）事件绑定

事件命名采用小驼峰式，需要通过`{}`传入一个事件处理函数，该函数在事件发生时执行；

#### （5）this绑定

解决`this`为`undefined`的方法：

- 使用`bind`给`btnClick`显示绑定`this`
- 事件监听时传入箭头函数（推荐）

#### （6）参数传递

1. 获取event对象：默认`event`对象被直接传入，函数就可以直接获取到；
2. 获取更多参数：传入一个箭头函数，直接传入相关的其他参数；

```jsx
render(){
  return (
  	<div>
    	<button onClick={(e) => this.btnClick(e,"zhang",25)}>按钮</button>
    </div>
  )
}
btnCLick(event,name,age){
  console.log(this,event,name,age)
}
```

#### （7）条件渲染

`React`中条件判断和普通`Javascript`代码一致：

1. 条件判断语句：适合逻辑较多的情况
2. 三元运算符：适合逻辑简单的情况
3. 与运算符&&：适合如果条件成立，渲染某个组件，如果不成立都不渲染的情况
4. v-show的效果：控制`display`属性是否为`none`

#### （8）列表渲染

展示列表最多的方式就是使用数组的`map`方法。可以使用`flter`和`slice`函数过滤和截取数组中的内容。

同时列表展示时需要设置key，提高diff算法的效率。



## 4、本质

所有的`jsx`<font color=deepred>都会被转换成`React.createElement`函数的调用</font>。**`jsx`只是`React.createElement`函数的语法糖**。

### （1）语法

```jsx
React.createElement(component, props, ...children)
```

### （2）参数

该函数需要传递三个参数：

#### （1）type

当前`ReactElement`的类型:

- 如果是标签元素，那么就使用字符串表示"div"；
- 如果是组件元素，就直接使用组件的名称；

#### （2）config

- 所有jsx中的属性都在config中以对象的属性和值的形式存储；
- 比如传入className作为元素的class；

#### （3）children

- 存放在标签中的内容，以children数组的方式进行存储





# 三、React脚手架

