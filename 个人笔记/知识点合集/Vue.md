## 1、Vue基本原理

### （1）简介

​	每个组件实例都有相应的**`watcher`**实例，会在组件渲染过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而影响关联的组件得以更新。

1. 创建Vue实例后，Vue会调用init方法初始化生命周期、事件、props、methods、data、computed和watch等属性方法。其中最重要的是对data进行遍历，给属性设置getter/setter方法，实现响应式和依赖收集；
2. 初始化后调用$mount方法挂载当前组件实例；
3. 接着进行模板编译，包含三个过程：
   1. parse阶段使用正则表达式解析template模板，形成抽象语法树；
   2. optimize阶段标记静态节点，执行编译优化，方便diff算法跳过静态节点，提高patch函数的性能；
   3. generate阶段将AST转化成render function字符串，得到结果是render的字符串以及staticRenderFns字符串；

4. render函数将编译后的结果转换为VNode节点，虚拟DOM就是以VNode节点对象作为基础所构建的树，是对真实DOM的抽象，最后会映射到真实DOM上；
5. 当对象被读取时执行getter函数进行**依赖收集**，依赖收集器Dep管理了被劫持对象Observe和订阅者Watcher之间的关联关系。当对象被修改的时候执行setter函数通知依赖收集器中收集的所有Watcher依赖的值发生了改变，需要重新渲染视图，Watcher调用update函数更新视图；
6. update函数负责更新视图，update函数中包含一个patch的过程也就是diff算法过程，以及使用队列异步更新视图的过程；

![image-20230731173937869](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307311739949.png)



### （2）优点

1. 轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十`kb`；
2. 入手简单：国人开发，中文文档，易于学习和理解；
3. 双向数据绑定：数据操作方面简单；
4. 组件化：实现html的封装和复用，减少代码编写量；
5. 虚拟DOM：不使用原生DOM节点，极大解放DOM操作，减少性能消耗；
6. 数据视图分离：数据更改简单，且只需要操作数据就能实现页面修改；



## 2、Vue响应式

### （1）数据劫持

​	通常利用`Object.defineProperty`劫持对象的访问器，在属性值发生变化时可以获取变化，从而进行下一步操作。



### （2）发布/订阅者模式

​	<font color=pink>发布订阅</font>是一种消息范式。

​	消息的发送者（也就是发布者）不会将消息直接发送给特定的接收者（也就是订阅者）。而是将发布的消息分为不同的类别，无需了解特定订阅者的存在。

​	同样的订阅者可以表达对一个或多个类别的兴趣，只接收感兴趣的消息，无需了解发布者的存在。

​	不同于观察者和被观察者，**发布者和订阅者是互相不知道对方的存在的**，**发布者只需要把消息发送到订阅器里面，订阅者只管接受自己需要订阅的内容**。



### （3）响应式原理

​	Vue响应式原理是采用<font color=pink>**数据劫持**</font>结合<font color=pink>**发布者-订阅者**</font>的方式，通过`Object.defineProperty()`劫持对象属性的setter和getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

```js
Observe // 被劫持对象
Compile // vue的编译器
Watcher // 订阅者
Dep // 依赖收集器，收集watcher订阅者们
```

1. 给`Observe`的数据对象进行递归遍历，包括子属性对象的属性，都加上`getter`和`setter`属性，获取值时触发getter，修改属性时触发setter。

2. `Compile`解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦有数据变化，收到通知，更新视图。

3. `Watcher`订阅者是`Observer`和`Compile`之间通信的桥梁，`Watcher`主要任务是：

   ① 在自身实例化时向依赖收集器Dep中添加自身；

   ② 自身必须具有`update()`方法；

   ③ 当依赖属性变动发起`Dep.notify`通知时，能调用`update`方法，触发`Compile`中绑定的回调更新视图；

   ④ MVVM作为数据绑定的入口，整合`Observer、Compile、Watcher`三者，通过`Observer`监听自己的model数据变化，通过Compile解析编译模板指令，最终利用Watcher搭建Observer和Compile之间的通信桥梁，达到数据变化到视图更新、视图交互变化到数据变更的双向绑定效果；

   ![image-20230731165606320](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307311656909.png)



响应式原理：先对数据进行劫持监听，设置一个监听器Observer，用来监听所有的属性，如果属性发生了变化，就需要告诉订阅者Watcher是否需要更新。因为订阅者数据有很多个，所以需要有一个依赖收集器Dep专门管理订阅者，然后在监听器Observer和订阅者Watcher之间进行统一管理。接着，需要有模板解析器Compile，对每个节点元素进行扫描和解析，将相关指令对应初始化成一个订阅者Watcher，并替换模板数据或者绑定相应的函数，此时订阅者Watcher接收到相应属性的变化，回执行相应的更新函数，从而更新视图。











深度监听需要递归，计算量大

无法监听新增属性，删除属性

无法监听原生数组，需要特殊处理



Vue的响应式：

首先对数据进行劫持挤，设置一个Observer监听器，用来





















3、Object.defineProperty缺点

### （1）语法

```js
Object.defineProperty(obj , prop, descriptor)
```



### （2）参数

1. `obj`要定义的对象；
2. `prop`要定义或修改的属性名称；
3. `descriptor`要定义或修改的属性描述符（配置对象），可配置属性包括：
   1. value：给`target[key]`设置初始值；
   2. get：调用`target[key]`时触发；
   3. set：修改设置target[key]`时触发；
   4. enumerable：控制key是否可出现在target的枚举属性（for...in以来可枚举）默认为false；
   5. writable：控制`target[key]`是否可被重写，默认为false；
   6. configurable：控制能否改变`options`，已经删除key属性，默认为false；



### （3）缺点

1. 深度监听需要递归，一次性计算量大；
2. 无法监听新增属性、删除属性（需要使用`Vue.set`和`Vue.delet e`）;
3. 无法监听原生数组，需要重写方法；





## 4、MVVM、MVC和MVP的区别

### （1）MVC

M：model数据模型，V：view视图模型，C：controller控制器

​	MVC 通过分离 Model、View 和 Controller 的方式来组织代码结构。其中 View 负责页面的显示逻辑，Model 负责存储页面的业务数据，以及对相应数据的操作。







## 10、Vue.$set原理

​	当执行$set方法时，Vue会检测该对象是否已经被劫持为响应式对象，如果没有就转换为响应式对象，并为其添加新属性；如果已经是响应式对象，就直接为其添加新属性。然后再向该对象的监听器发送变化通知，更新视图。







响应式原理

当`render`渲染该数据时，触发`getter`进行依赖收集，收集依赖的目的是将观察者watcher放到当前订阅者Dep的管理下形成一种关联关系。

当修改值的时候





# Vue2

## 1、实现push方法重写

1. 创建原始的数组原型对象，Array.prototype；
2. 创建一个新的对象，继承自原始的数组对象；
3. 在新的对象上添加一个名为push的方法，该方法可以在数组末尾添加一个或多个元素，并触发通知更新；
4. 将新的对象设置为Vue.prototype.$push属性的值，以便在Vue实例中使用该方法；

```js
// 获取原始的数组原型对象
const arrayProto = Array.prototype;

// 创建一个新的对象，继承自原始的数组原型对象
const arrayMethods = Object.create(arrayProto);

// 给新对象添加一个push方法
arrayMethods.push = function(...args) {
  // 调用原始的push方法，在数组末尾添加元素
  const result = arrayProto.push.apply(this, args);
  
  // 触发通知更新，这里需要获取当前的Observer对象
  const ob = this.__ob__;
  ob.notify()
  
  //返回添加元素后的新长度 
  return result
}

// 将新对象设置为Vue.prototype.$push属性的值
Vue.prototype.$push = function (item) {
  // 调用数组实例的push方法，该方法已被重写
  return arrayMethods.push.call(this, item)
}
```





# Vue3