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

## 1、创建项目

### （1）官方

```shell
npx create-react-app 项目名称
```

### （2）vite

```shell
npm create vite@latest
```



## 2、目录结构

```js
.
├── README.md	 //说明文档
├── package.json	//版本号、依赖、启动、打包命令
├── public
│   ├── favicon.ico 	//顶部icon
│   ├── index.html  //应用的index.html入口文件
│   ├── logo192.png  //被manifest.json使用
│   ├── logo512.png  //被manifest.json使用
│   ├── manifest.json  //和web app配置相关
│   └── robots.txt  //指定搜索引擎可以或无法爬取哪些文件
└── src
    ├── App.css  //App组件相关的样式
    ├── App.js  //App组件的代码文件
    ├── App.test.js  //App组件的测试代码文件
    ├── index.css  //全局样式文件
    ├── index.js  //整个应用程序入口文件
    ├── logo.svg  //启动项目看到的react图标
    ├── reportWebVitals.js  //默认注册PWA代码
    └── setupTests.js  //测试初始化文件
```



## 3、扩展：PWA

### （1）概述

PWA全称`Progressive Web App`，也称为<font color=deepred>渐进式WEB应用</font>；

- 一个PWA应用首先是一个网页，可以**通过Web技术编写出一个网页应用**；
- 加上`App Manifest`和`Service Worker`来实现PWA的<font color=yellow>安装和离线</font>等功能；

这种Web存在的形式，称之为是 Web App



### （2）解决的问题

1. 可以添加至主屏幕，点击主屏幕图标实现启动动画和隐藏地址栏；
2. 实现离线消息缓存，即使没有网络也可以使用离线功能；
3. 实现消息推送功能；
4. 类似于`Native APP`相关的功能；



## 4、脚手架中的Webpack

脚手架默认是<font color=gree>**基于Webpack开发**</font>的，但是默认配置文件是被隐藏的，要想显示处理需要执行脚本`npm run eject`。

![image-20230120095710900](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301200957716.png)



# 四、React组件化

## 1、分类特点

React的组件相对于Vue更加灵活和多样，按照不同的方式可以分成很多种类：

### （1）定义方式

函数组件和类组件

### （2）是否维护状态

无状态组件和有状态组件

### （3）职责不同

展示型组件和容器型组件

### （4）逻辑和展示分离

- <font color=yellow>函数组件、无状态组件、展示型组件</font>主要关注UI
- <font color=gren>类组件、有状态组件、容器型组件</font>主要关注数据逻辑



## 2、类组件

### （1）要求

- 组件名称必须是**大写字母开头**
- 类组件要**继承自`React.Component`**
- 类组件必须**实现`render`函数**，是<font color=red>**唯一必须实现的方法**</font>
- `constructor`是可选的，用来初始化一些数据，`this.state`中维护组件内部数据



### （2）render函数返回值

当render函数被调用时，**会检查`this.props`和`this.state`的变化**并返回以下类型：

1. **React元素**：
   1. 通常通过 JSX 创建；
   2. 原始标签会被渲染为DOM节点，组件会被渲染为自定义组件；
2. **数组或者`fragments`**：使render方法可以返回多个元素；
3. **`Protals`**：可以渲染子节点到不同的DOM子树中；
4. **字符串或数值类型**：在DOM中被渲染为文本节点；
5. **布尔类型或者null**：不渲染；



## 3、函数组件

函数组件是使用`function`来进行定义的函数，这个函数会返回和类组件中`render`函数一样的内容。

### 特点（和hooks不同）

- 会被更新并挂载，但是没有生命周期函数；
- this关键字不能指向组件实例（没有组件实例）;
- 没有内部状态（state）

```jsx
export default function App(){
  return (
  	<div>hello world</div>
  )
}
```



## 4、生命周期

`React`内部为了告诉外部处于哪个阶段会对组件内部实现的**某些函数进行回调**，这些函数就是<font color=deepred>生命周期函数</font>。

⚠️注意：这里主要讨论的是**类的生命周期，因为函数式组件是没有生命周期的**。

### （1）生命周期解析

![image-20230120114644049](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301201146854.png)

类组件的生命周期可分为三个状态：

- Mounting（挂载）：已插入真实DOM
- Updating（更新）：正在被重新渲染
- Unmounting（卸载）：已移除真实DOM



### （2）生命周期函数

#### 2.1、挂载

当组件实例被创建并插入DOM中时，其生命周期调用顺序如下：

- **Constructor**
  - 在`React`组件挂载之前，会调用它的构造函数
  - 如果不需要初始化`state`或不进行方法的绑定，则不需要为`React`组件实现构造函数
  - `constructor`中通常只做两件事：
    - 通过给`this.state`赋值对象来初始化内部的`state`
    - 为事件绑定实例（this）
- **getDerivedStateFromProps**
  - 在调用`render`方法之前调用，并且在初始挂载及后续更新时都会被调用
- **render**
  - `render`方法是class组件中唯一必须实现的方法
- **componentDidMount**
  - 在组件挂载后（插入DOM树中）立即调用
  - 通常进行的操作：
    - 依赖DOM的操作在这处理
    - 发送网络请求最好的地方
    - 添加一些订阅



#### 2.2、更新

每当组件的`state`或者`props`发生变化时，组件就会更新，生命周期调用顺序如下：

- **getDerivedStateFromProps**
  - 在调用`render`方法之前调用，在初始挂载及后续更新时都会被调用
- **shouldComponentUpdate**
  - 当`props`或者`state`发生变化时，会在渲染执行之前调用
- **render**
  - class组件中唯一必须实现的方法
- **getSnapshotBeforeUpdate**
  - 最后一次渲染输出前调用
- **componentDidUpdate**
  - 更新后立即调用，首次渲染不会执行此方法
  - 组件更新后在此处对DOM进行操作



#### 2.3、卸载

当组件从DOM中移除时会调用下面的方法：

- **componentWillUnmount**
  - 在组件卸载及销毁之前直接调用
  - 此方法中执行必要的清理操作，清除timer或者清除订阅等



## 5、组件嵌套



