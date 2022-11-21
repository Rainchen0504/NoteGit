# 一、全局概览

![image-20221117162715532](https://raw.githubusercontent.com/Rainchen0504/picture/master/202211171637291.png)



## 1、初始化和挂载

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202211171734884.png" alt="image-20221117173436438" style="zoom:50%;" />

​	执行new Vue()后，Vue会调用_init函数进行初始化，包括初始化生命周期、事件、props、methods、data、computed和watch等。其中最重要的过程是通过`Object.defineProperty`设置setter和getter函数，用来实现**响应式**和**依赖收集**。

​	初始化后会调用$mount挂载组件，如果使用的是运行时编译版本，即不存在render function但是存在template的情况，就需要执行**编译**步骤。



## 2、编译

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202211171736580.png" alt="image-20221117173552940" style="zoom:50%;" />

compile编译可以分成parse、optimize和generate三个阶段，最中需要得到render function。

### （1）parse解析模板

parse会用正则等方式解析template模板中的指令、class、style等数据，形成抽象语法树（AST）



### （2）optimize优化语法树

optimize主要作用是标记static静态节点，这是Vue在编译过程的优化，当update更新界面时，会有一个patch（对比新旧node）的过程，diff算法会直接跳过静态节点，从而减少了比较的过程，优化了patch的性能。



### （3）generate生成代码

generate是将AST转化成render function字符串的过程，得到结果是render的字符串以及staticRenderFns字符串。



经过上面三个步骤后，组件就会存在渲染VNode所需的render function了。



## 3、响应式

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202211171741168.png" alt="image-20221117174113932" style="zoom:50%;" />

响应式是Vue的核心部分，初始化时由于使用了`Object.defineProperty`对实例对象进行了绑定，使得对象被读取时会执行`getter`函数，被修改赋值时执行`setter`函数。

执行render function进行渲染时会读取实例对象的值，触发getter进行**依赖收集**，依赖收集目的是将观察者Watcher对象放到当前订阅者Dep的管理下形成一种关联关系：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202211211720820.png" alt="image-20221121172021622" style="zoom:50%;" />

修改值的时候，就会触发setter，setter通知依赖收集得到的观察者Dep中的每一个Watcher依赖的值发生改变了，需要重新渲染视图，Watcher调用update函数更新视图，update函数中包含一个patch的过程也就是常说的diff算法过程，还包括使用队列异步更新。



## 4、Virtual DOM虚拟DOM

​	render function会将编译后的结果转化成VNode节点。虚拟DOM就是以VNode节点对象作为基础的树，用对象属性描述节点，实质上是对真实DOM的抽象，最后可以通过操作映射到真实DOM上。

举个简单例子：

```js
{
  tag:"div",	//说明是一个div标签
  children:[	//存放该标签的子节点
    {
      tag:"a",	//说明这是一个a标签
      text:"click"	//标签的内容
    }
  ]
}
```

渲染后可以得到真实结构

```html
<div>
  <a>click</a>
</div>
```



## 5、更新视图

修改对象值的时候，会通过`setter -> Watcher -> update`的流程来修改对应的视图，具体的情况应该是这样：

当数据变数后，执行render function就可以得到一个新的VNode节点，如果在此时想更新视图，最直接的方法是直接解析新的虚拟节点，然后用innerHTML方法渲染到真实DOM中，但是这么做对与只进行一小部分修改的情况下造成了极大的性能浪费。

为了只修改「改变了的地方」，这里引入了patch函数，将新旧VNode传入比较，通过diff算法得到差异通知对应的DOM修改。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202211211750909.png" alt="image-20221121175026354" style="zoom:80%;" />



# 二、响应式原理

Vue是一款MVVM框架，数据模型是JS对象，操作对象可以直接影响视图，核心实现就是「响应式系统」。

## 1、`Object.defineProperty`

vue基于`Object.defineProperty`方法实现的响应式系统。

```js
/*
    obj: 目标对象
    prop: 需要操作的目标对象的属性名
    descriptor: 描述符
    return value 传入对象
*/
Object.defineProperty(obj, prop, descriptor)
```



## 2、实现observer

以对象举例处理，不考虑数组的情况。

定义一个cb函数，该函数模拟视图更新，调用就表示视图更新，内部会包含一些更新视图的方法。

```js
function cb (val){
  //渲染视图的方法
 	console.log("视图更新啦");
}
```

之后定义一个`defineReactive`函数，这个方法通过`Object.defineProperty`实现对对象的**响应式化**。参数包括obj（需要绑定的对象）、key（绑定对象的某个属性），val（具体的值）。

经过`defineReactive`处理后，obj对象的key属性在被读取时就会触发reactiveGetter方法，被修改的时候会触发reactiveSetter方法。

```js
function defineReactive(obj, key, val){
  Object.defineProperty(obj, key, {
  	enumerable:true,	//属性可枚举
    configurable:true,	//属性可以被修改
    get: function reactiveGetter(){
      return val	//这里进行依赖收集
    },
    set: function reactiveSetter(newValue){
      if(newValue === val)return
      cb(newValue)
    }
  })
}
```

这里只能对单个属性进行处理，因此需要在外层封装一层`observer`。外层函数传入一个value即需要响应式处理的对象，通过遍历对所有的属性都执行`defineReactive`方法。（真实的情况observer会对传入的对象进行递归调用defineReactive方法）

```js
function observer(value){
  if(!value || typeof value !== 'object'){
    return
  }
  Object.keys(value).forEach((key) => {
    defineReactive(value, key, value[key])
  })
}
```

在Vue的构造函数中，对options的data进行处理其实就相当于下面的写法（此处把data函数直接作为对象处理）

```js
class Vue {
  //vue的构造类
  constructor(options){
    this._data = options.data;
    observer(this._data)
  }
}
```

之后通过new关键字创建Vue实例对象时，就会将data中的数据进行「响应式」化处理，之后修改data中的数据就会触发setter中的cb方法触发视图更新方法。

```js
let app = new Vue({
  data:{
    test:"我是晨哥"
  }
})
app._data.test = "我不是晨哥";	//会触发cb，打印“视图更新啦”
```



# 三、依赖收集