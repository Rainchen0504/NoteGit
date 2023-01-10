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
