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

<font color=deepred>函数组件</font>和<font color=deepred>类组件</font>

### （2）是否维护状态

<font color=deepred>无状态组件</font>和<font color=deepred>有状态组件</font>

### （3）职责不同

<font color=deepred>展示型组件</font>和<font color=deepred>容器型组件</font>

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



### （3）生命周期图谱

![image-20230125102604741](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301251026738.png)



## 5、组件通信

### （1）父到子

父组件通过<font color=pink>**属性=值**</font>的形式来传递给子组件数据；

子组件通过<font color=pink>**props**</font>参数获取父组件传递过来的数据；



#### 1.1、类组件

```jsx
//父组件
export default class App extends Component {
  render(){
    return {
      <div>
      	<Children name="晨哥" age="26"></Children>
      </div>
    }
  }
}
```

```jsx
//子组件
export default class Children extends Component {
  render(){
    const { name, age } = this.props;
    return {
      <div>
      	<h2>我是子组件</h2>
        <p>传递过来的数据{name + age}</p>
      </div>
    }
  }
}
```



#### 1.2、函数组件

```jsx
//父组件
export default class App extends Component {
  render(){
    return {
      <div>
      	<Children name="晨哥" age="26"></Children>
      </div>
    }
  }
}
```

```jsx
//子组件
function Children( props ){
  const { name, age } = props;
  return (
  	<div>
    	<h2>我是function子组件</h2>
      <p>传递过来的数据{name + age}</p>
    </div>
  )
}
```



#### 1.3、参数propTypes

​	传递给子组件的数据如果需要验证（未应用TypeScript的项目），可以通过`prop-types`库来进行参数验证。

```jsx
import PropTypes from "prop-types";
//设置类型
当前组件名.propTypes = {
  name: PropTypes.string,
  age: PropTypes.number
}

//设置默认值
当前组件名.defaultProps = {
  name: "默认值",
  age: 0
}
```



### （2）子到父

通过`props`传递消息，只是让父组件给子组件传递一个回调函数，在子组件中调用这个函数即可。

```jsx
//父组件
export default class App extends Component {
  constructor() {
    super();
    this.state = {
      counter: 100,
    };
  }
  changeCounter(count) {
    this.setState({
      counter: this.state.counter + count,
    });
  }
  render() {
    const { counter } = this.state;
    return (
      <div>
        <h2>当前计数:{counter}</h2>
        <AddCounter addClick={(count) => this.changeCounter(count)}
        ></AddCounter>
      </div>
    );
  }
}
```

```jsx
//子组件
export class AddCounter extends Component {
  addCount(count) {
    this.props.addClick(count);
  }
  render() {
    return (
      <div>
        <button onClick={(e) => this.addCount(1)}>+1</button>
        <button onClick={(e) => this.addCount(5)}>+5</button>
      </div>
    );
  }
}
```



### （3）context应用

`React`提供了一个API：**`Context`**。`Context`提供了一种<font color=pink>**在组件之间共享值的方式**，不必显式地通过组件树的逐层传递`props`</font>；

`Context`设计的目的是为了<font color=pink>共享对于一个组件而言是“全局”的数据</font>，比如当前认证的用户、主题等。

#### 3.1、`React.createContext`

- 创建一个需要共享的Context对象
- 如果一个组件订阅Context，那么该组件会从离自身最近的Provider中读取到当前的context值；
- defaultValue是组件在顶层查找过程中没有找到对应的Provider，那么就使用默认值

```jsx
const MyContext = React.createContext(defaultValue)
```



#### 3.2、`Context.Provider`

- 每个Context对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化；
- Provider 接收一个 value 属性，传递给消费组件；
- 一个 Provider 可以和多个消费组件有对应关系；
- 多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据；
- 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染；

```jsx
<MyContext.Provider value={/*某个值*/}></MyContext>
```



#### 3.3、`Class.contextType`

- 挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象；
- 能让使用 this.context 来消费最近 Context 上的那个值
- 可以在任何生命周期中访问到它，包括 render 函数中；

```jsx
MyClass.contextType = MyContext;
```



#### 3.4、`Context.Consumer`

- React组件可以订阅到context变更，能在<font color=deepred>**函数式组件**</font>中完成订阅context；
- 需要函数作为子元素；
- 该函数接收当前的context值，返回一个React节点；

```jsx
<MyContext.Consumer>
  {value => /*基于context值进行渲染*/}
</MyContext.Consumer>
```



## 6、组件插槽

react实现插槽的方式有两种：

### （1）children实现

每个组件都可以通过`props.children`获取到：<font color=deepred>包含组件的开始标签和结束标签之间的内容</font>

```jsx
//子组件
class NavBar extends Component {
  render(){
    const {children} = this.props;
    return (
    	<div className="nav-bar">
        <div className="left">{children[0]}</div>
        <div className="center">{children[1]}</div>
        <div className="right">{children[2]}</div>
      </div>
    )
  }
}
```

```jsx
//父组件
<NavBar>
	<button>按钮</button>
  <h2>我是标题</h2>
  <span>元素</span>
</NavBar>
```

#### 缺点：

​	使用`children`的方案存在一个问题：**通过索引获取传入的元素很容易出错**，不能精准获取传入的元素。



### （2）props实现

通过具体的属性名，让传入和获取元素时更加精准

```jsx
//子组件
export default Son extends Component {
  render(){
    const {leftSlot,centerSlot,rightSlot} = this.props
    return (
    	<div>
      	<div>{leftSlot}</div>
      </div>
    )
  }
}
```

```jsx
//父组件
class App extends Component {
  render(){
    const leftSlot = <strong>返回</strong>
    const centerSlot = <h2>标题</h2>
    const rightSlot = <i>更多</i>
    return (
    	<div>
      	<Son
          leftSlot={leftSlot}
          centerSlot={centerSlot}
          rightSlot={rightSlot}
          ></Son>
      </div>
    )
  }
}
```



## 7、setState解析

### （1）数据管理和界面渲染的流程解析

#### 1.1、Vue

```js
template模板 通过render(){}函数编译转换成h函数创建元素对象
div -> h("div", {}, children)
```

当数据被修改的时候由于做了数据劫持，会自动调用render函数



#### 1.2、React

react对数据管理和界面渲染的流程解析

```js
使用render(){}函数创建元素对象
div{message}使用babel编译 -> React.createElement("div",{},children)
```

当数据被修改的时候由于react没有做数据劫持，需要通过setState方法执行render函数



### （2）使用setState的原因

​	`React`没有实现类似`Vue2`中的`Object.defineProperty`或者`Vue3`中的`Proxy`方式监听数据的变化，必须通过`setState`方法告知`React`数据发生了变化。

​	`setState`方法是从`Component`中继承过来的：

```js
Component.prototype.setState = function(partialState, callback) {
  if (
    typeof partialState !== 'object' &&
    typeof partialState !== 'function' &&
    partialState != null
  ) {
    throw new Error(
      'setState(...): takes an object of state variables to update or a ' +
      'function which returns an object of state variables.',
    );
  }
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};
```



### （3）setState的用法

#### 3.1、基本用法

```jsx
this.setState({message:"嘻嘻"})
```



#### 3.2、传入一个回调函数

- 可以在回调函数中写新的state逻辑
- 当前的回调函数会将之前的state和props传递进来

```jsx
this.setState((state, props) => {
	console.log(this.state.message, this.props)
  return {
    message: "你好啊, 李银河"
  }
})
```



#### 3.3、异步更新

setState是异步的操作，并不能在执行完setState之后立马拿到最新的state的结果。

```jsx
changeText(){
  this.setState({
    message:"呵呵"
  })
  console.log(this.state.message) //打印结果还会是旧值
}
```



##### （1）、原因：

- 可以显著提升性能
  - 每次调用 setState都进行一次更新，会频繁调用render函数，界面重新渲染；
  - 最好的做法是获取到多个更新，之后进行批量更新；
- 如果同步更新state但是还没有执行render函数，那么state和props不能保持同步，容易产生很多开发问题



##### （2）、解决方法

###### 方法一：setState的回调

`setState`<font color=deepred>在`React`的事件处理中是一个**异步调用**</font>，如果需要在数据更新（合并）之后获取对应结果执行逻辑，可以传入第二个参数`callback`

```jsx
this.setState({message:"哈哈"}, () => {
  //执行更新后的逻辑
})
```

也可以在生命周期`componentDidUpdate`中执行逻辑



###### 方法二：在setTimeout中更新

```jsx
changeText(){
  setTimeout(() => {
    this.setState({message:"修改"})
    //这里写逻辑拿到的就是修改后的值
  },0)
}
```



###### 方法三：原生DOM事件

```jsx
componentDidMount(){
	const btnEl = document.getElementById("btn")
  btnEl.addEventListener('click', () => {
    this.setState({
      message:"修改值"
    })
    //这里取到的也是修改后的值
  })
}
```



##### （3）、版本区别

在`React18`之前：

1. 在组件生命周期或React合成事件中，<font color=pink>setState是异步的</font>；
2. 在setTimeout或者原生dom事件中，<font color=pink>setState是同步的</font>；



在`React18`之后：

所有的操作都被放到了批处理中，也就是进行异步处理。

如果想同步拿到结果，需要执行特殊的`flushSync`操作。



## 8、React更新机制

### （1）渲染流程

![image-20230204201210106](https://raw.githubusercontent.com/Rainchen0504/picture/master/202302042012854.png)



### （2）更新流程

![image-20230204202937039](https://raw.githubusercontent.com/Rainchen0504/picture/master/202302042029327.png)

- 当props或state发生改变时，会调用React的render方法，会创建一颗不同的树；
- react需要基于两颗不同的树之间的差别判断有效更新UI：
  - 如果一棵树参考另外一棵树进行完全比较更新，即使是最先进的算法复杂程度为 O(n3)，其中 n 是树中元素的数量；
  - 如果使用该算法那么开销会过于高昂，更新性能会非常低效；
- React对上面的算法进行了优化，优化后的复杂度为O(n)，优化步骤：
  - 同层节点之间进行相互比较，不会垮节点比较；
  - 不同类型的节点，产生不同的树结构；
  - 开发中，通过key来指定具体节点在不同渲染下是否保持稳定；



### （3）keys的优化

- 方式一：在最后位置插入数据，有没有key意义不大；
- 方式二：在前面插入数据，没key的情况下所有li都需要进行修改；
- 方式三：在中间插入数据，react会使用key匹配原有树上的子元素和最新树上的子元素；

key的注意事项⚠️：

1. key应该是唯一的；
2. key不要使用随机数（随机数在下一次render时，会重新生成一个数字）；
3. 使用index作为key，对性能是没有优化的；



## 9、性能优化

### （1）render函数调用

​	当组件存在嵌套关系时，修改组件的数据势必会引起组件重新`render`，进行`diff`算法，性能必然是很低的。事实上很多组件并不需要重新`render`，因为`render`调用有个前提，是依赖的数据（state、props）发生改变



### （2）
