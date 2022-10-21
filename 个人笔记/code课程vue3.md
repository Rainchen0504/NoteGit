# 一、基础知识

## 1、安装使用vue这个javascript库

- 在页面中通过CDN的方式引入
- 下载vue的javascript文件，并且手动引入
- 通过npm包管理工具安装使用
- 直接通过vue cli创建项目使用



### （1）方式一：CDN引入

​	CDN称之为内容分发网络，指通过相互连接的网络系统，利用最靠近每个用户的服务器，更快更可靠的将音乐、图片、视频、应用程序等文件发送给用户，来提高性能、可扩展性及低成本的网络内容传递给用户。

​	常用的CDN服务分为两种，自己的CDN服务器（需购买）；开源的CDN服务器，国际上使用较多的是unpkg、JSDeliver、cdnjs。



### （2）方式二：下载和引入

​	打开链接制所有代码，创建一个新文件，代码复制其中，然后引入使用。



### （3）实际案例

vue这种代码编写过程称为**<font color=deepred>声明式编程</font>**

```vue
<div id="app">
	<button @click="change">{{title}}</button>
</div>

<script src="引入CDN路径或者本地资源"></script>

<script>
	const app = Vue.createApp({
    data(){
    	return {
        title: "这是vue项目"
      }
  	},
    methods:{
      change(){
        this.title = "这就是个vue项目"
      }
    }
  })
  app.mount("#app")
</script>
```



### （4）data和methods

#### 4.1data

data属性<font color=deepred>要求传入一个函数，并且要**返回一个对象**</font>，该对象会被响应式系统劫持。

- 在vue2.X版本中可以给data属性传入一个对象；
- 在vue3.X版本中<font color=blue>必须传入一个函数</font>，否则会在浏览器中报错；



#### 4.2methods

methods属性<font color=deepred>是**一个对象**，在这个对象中可以定义很多方法</font>，可以使用this关键字访问到data中返回的对象，

##### <font color=red>**methods对象中的函数不能是箭头函数**</font>

​	因为在函数中要通过this获取data对象中的数据，this应该是当前vue实例，而使用箭头函数的话会在上层作用域中查找this，<font color=blue>查找到的是**script作用域中的this**（即window对象），window对象中无法获取到data对象中的数据</font>。



## 2、模板语法

​	React使用的jsx语法，所以对应的代码都是比那些的类似于js的一种法；之后通过Babel将jsx编译成React.createElement函数调用。

​	Vue也支持jsx的开发模式，但是大多数情况下，使用基于HTML的模板语法，允许开发者以声明的方式将DOM和底层组件实例的数据绑定在一起，在底层的实现中，Vue将模板编译成虚拟DOM渲染函数。



## 3、v-指令

### （1）v-once

​	<font color=deepred>指定元素或指令**只执行一次**</font>，当数据再次发生变化时，相关元素和子元素将不更新，视为静态内容并跳过，此指令可用于性能优化。

```vue
<!--counter首次渲染后就不会被改变-->
<h1 v-once>当前计数{{counter}}</h1>
```



### （2）v-pre

​	用于<font color=deepred>**跳过**元素和子元素的**编译过程**</font>，显示原始的`Mustache`标签（跳过不需要编译的节点，提高编译速度）

```vue
<tempalte id="app">
  <!-- 不会编译message，显示的就是{{message}} -->
	<div v-pre>{{message}}</div>
</tempalte>
```



### （3）v-cloak

​	保持在元素上<font color=deepred>**直到关联组件实例结束编译**</font>。配合CSS规则一起使用，则可以隐藏未编译的 Mustache 标签直到组件实例准备完毕。

```vue
<!-- 下面例子的效果就是刷新页面三秒后创建实例完成，message才显示出来 -->
<style>
  [v-cloak] { display: none; }
</style>

<div id="app">
  <h2 v-cloak>{{message}}</h2>
</div>

<script src="引入CDN路径或者本地资源"></script>
<script>
	setTimeout(() => {
    // 1.创建app
    const app = Vue.createApp({
      data: function() {
        return {
          message: "Hello Vue"
        }
      },
    })
    // 2.挂载app
    app.mount("#app")}, 3000)
</script>
```



### （4）v-bind

用于**<font color=deepred>绑定一个或多个属性值</font>**，或者向另一个组件传递props值，语法糖是`:属性名=属性值`



### （5）v-on

**<font color=deepred>绑定事件监听</font>**

#### v-on修饰符

​	`.stop`--调用event.stopPropagation()；

​	`.prevent`--调用event.preventDefault()；

​	`.capture`--添加事件侦听器时使用capture模式；

​	`.self`--只当事件时从侦听器绑定的元素本身触发时才触发回调；

​	`.once`--只触发一次回调；

​	`.left`--只当点击鼠标左键时触发；

​	`.right`--只当点击鼠标右键时触发；

​	`.middle`--只当点击鼠标中键时触发；

​	`.passive`--{passive:true}模式添加侦听器



### （6）条件渲染

#### 6.1、v-if、v-else、v-else-if

只有在条件为true时，才会被渲染出来

- v-if是惰性的
- 当条件为false时，其判断的内容完全不会被渲染或者会被销毁掉
- 当条件为true时，才会真正渲染条件块中的内容



#### 6.2、v-show

根据一个条件决定是否显示元素或者组件



#### 6.3、v-show和v-if区别

- 用法上
  - v-show不支持tempalte
  - v-show不能和v-else一起使用
- 本质上
  - **<font color=red>v-show无论是否显示DOM都是实际存在的，只是通过CSS的display属性切换控制显隐</font>**；
  - **<font color=red>v-if是通过创建和销毁DOM来实现显隐的</font>**；
- 开发中
  - 如果需要频繁切换显示隐藏则使用v-show
  - 如果不频繁则使用v-if



### （7）v-model

#### 7.1、基本使用

v-model本质上<font color=blue>是语法糖</font>，负责监听用户的输入事件来更新数据，原理上有**两个操作**。

```html
<input type="text" v-model=“message>
```



#### 7.2、v-model原理

v-model的原理是背后有两个操作：

1. <font color=red>v-bind绑定value属性</font>的值；
2. <font color=red>v-on绑定input事件</font>监听到的函数，函数会取得最新的值赋到绑定的属性中；

```vue
<input v-model="message" />
```

等价于

```vue
<input :value="message" @input="message = $event.target.value" />
```



#### 7.3、v-model修饰符

- `.lazy`触发方式由input改为change失去焦点；
- `.number`自动将输入值转换为数字类型类型parseFloat，无法转换返回原内容；
- `.trim`自动过滤首尾的空格。



## 4、根元素

​	在vue2中，template模板中只能有一个根元素；vue3是允许template中有多个根元素。



## 5、template元素

​	template元素<font color=blue>**可以当做不可见的包裹元素**</font>，并且在v-if上使用，但是最终template不会被渲染出来，有点类似与小程序中的block。



## 6、v-for中key的作用

- key属性主要用在<font color=red>Vue的虚拟DOM算法</font>，在新旧Node节点对比时辨识<font color=red>VNodes</font>；
- 如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能尝试<font color=red>修改/复用相同类型的元素</font>的算法；
- 使用key时，会基于key的变化<font color=red>重新排列元素顺序</font>，并且会<font color=red>移除/销毁key不存在的元素</font>。



### （1）VNode

​	VNode全称是Virtual Node也就是虚拟节点，无论是组件还是元素，最终在Vue表现出来的都是一个个VNode，<font color=red>VNode的本质是一个JavaScript的对象</font>。

例如：

```html
<div class="title" style="font-size: 30px;color: red;">哈哈</div>
```

该元素用VNode表现为：

```javascript
const vnode = {
    type:"div",
    props:{
        class:"title",
        style:{
            "font-size":"30px",
            "color":"red"
        },
    },
    children:"哈哈"
};
```

![image-20211202153237229](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112021532481.png)





### （2）虚拟DOM

如果不只是一个简单的div，而是一大堆的元素，那么会形成一个VNode Tree：

![image-20211228141433063](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281414268.png)





### （3）Vue源码对于key的判断

看一个案例，点击按钮在中间插入一个f

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112021550951.png" alt="image-20211202155000242" style="zoom:50%;" />

这次更新对于ul和button是不需要进行更新的，需要更新的是li列表；

在Vue中，对于相同父元素的子元素节点并不会重新渲染整个列表;对于列表中 a、b、c、d它们都是没有变化的;在操作真实DOM的时候，只需要在中间插入一个f的li即可；

- **Vue对于列表的更新操作具体执行过程**：

Vue对于key和没有key会调用两个不同的方法；

​		**有key，就使用<font color=red>pathKeyedChildren方法</font>；**

​		**没有key，就使用<font color=red>pathUnkeyedChildren方法</font>；**

![image-20211202155951363](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112021559015.png)





### （4）没有key的diff算法过程（源码）

![image-20211202160110895](/Users/rain_chen/Library/Application Support/typora-user-images/image-20211202160110895.png)

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052028465.png" alt="image-20210905193809636" style="zoom: 50%;" />

上面的diff算法效率并不高，c和d事实上并不需要有任何改动，但是操作时c被f所使用了，所有后续的内容都要进行一次改动，并且最后进行新增。





### （5）有key 的diff算法过程

源码：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052043158.png" alt="image-20210905204233152" style="zoom: 50%;" />

图解顺序：

#### 5.1、从头遍历

a和b是一致的会继续进行比较;

c和f因为key不一致，所以就会break跳出循环;

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052043779.png" alt="image-20210905204344441" style="zoom:67%;" />



#### 5.2、从尾部遍历

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052046844.png" alt="image-20210905204414717" style="zoom:67%;" />



#### 5.3、旧节点遍历完成，有新节点就新增

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052047433.png" alt="image-20210905204656246" style="zoom:67%;" />



#### 5.4、旧节点遍历完成，有旧节点就移除

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052047428.png" alt="image-20210905204725240" style="zoom:67%;" />



#### 5.5、特殊情况，未知或者乱序的节点

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202109052048378.png" alt="image-20210905204755429" style="zoom:67%;" />

Vue在进行diff算法的时候，会尽量利用我们的key来进行优化操作

- 在没有key的时候我们的效率是非常低效的;
- 在进行插入或者重置顺序的时候，保持相同的key可以让diff算法更加的高效;



## 7、计算属性computed

对于任何包含响应式数据的复杂逻辑，应该使用**<font color=blue>计算属性</font>**。计算属性**是一个值**，<font color=red>**是有缓存的**</font>。

### （1）计算属性缓存

计算属性会**根据依赖关系进行缓存**，在计算数据不发生改变时是不需要重新计算的，一旦依赖数据发生变化计算属性会重新进行计算。



### （2）计算属性setter和getter

计算属性可以写成getter和setter形式：

```js
computed:{
	message:{
		get(){
			return this.surname + this.fullname;
		},
    set(value){
    	const names = value.split(" ")
			this.surname = names[0]
    },
	}
}

//一般使用时写法
computed:{
	message(){
    return this.surname + this.fullname;
  }
}
```



## 8、watch侦听器

​	watch只是在侦听被监听对象的引用变化，对内部属性的变化是不会做出响应的；

- 使用<font color=deepred>deep进行更深层次的侦听</font>，监听对象内部属性的变化；
- <font color=deepred>使用immediate选项</font>在一开始就立即执行一次；

### （1）常用写法

```js
watch: {
  // 默认watch监听不会进行深度监听，有两个参数: newValue/oldValue
  info(newValue, oldValue) {
     console.log("侦听到info改变:", newValue, oldValue)
  }
  
  // 进行深度监听写法
  info: {
    handler(newValue, oldValue) {
      console.log("侦听到info改变:", newValue, oldValue)
      console.log(newValue === oldValue)
    },
    // info进行深度监听
    deep: true,
    // 第一次渲染直接执行一次监听器
    immediate: true
  },
    
  //侦听对象的属性写法
  "info.name": function(newValue, oldValue) {
    console.log("name发生改变:", newValue, oldValue)
  }
}

```



### （2）$watch

支持<font color=blue>在生命周期使用this.$watch方法侦听数据</font>，该方法有三个参数：

- 参数一是要侦听的源
- 参数二是侦听的回调函数callback
- 参数三是额外的选项对象，包括deep和immediate等

```js
created() {
  this.$watch("message", (newValue, oldValue) => {
    console.log("message数据变化:", newValue, oldValue)
  }, { deep: true, immediate: true })
}
```





# 二、Vue组件化

## 1、组件化开发

*组件化思想：

-  将一个页面中所有的处理逻辑全部放在一起，处理起来就会变得非常复杂，不利于后续的管理以及扩展；
- 但将一个页面拆分成一个个小的功能块，每个功能块完成属于自己这部分独立的功能，那么之后整个页面的管理和维护就变得非常容易了；


​	基础部分通过createApp函数传入了一个对象App，这个<font color=blue>对象其实**本质上就是一个组件**</font>，也是我们应用程序的<font color=blue>**根组件**</font>。组件化提供了一种抽象，任何的应用都会被抽象成一颗组件树。

*组件注册方法：<font color=deepred>**全局注册和局部注册**</font>



### （1）全局注册

在任何其他的组件中都可以使用的组件

1. 全局组件需要使用全局创建的app来注册组件;
2. 通过component方法传入组件名称、组件对象即可注册一个全局组件了;
3. 之后可以在App组件的template中直接使用这个全局组件:



### （2）局部注册

只有在注册的组件中才能使用的组件

1. 局部注册是在需要使用到的组件中，通过components属性选项来进行注册；
2. 比如之前的App组件对象中除了data、computed、methods等属性，还可以有一个components选项；
3. 该components选项对应的是一个对象，对象中的键值对是`组件的名称: 组件对象`



## 2、工具链

创建Vue项目常用工具

### （1）Vue-cli

```shell
vue create XXX
#更新脚手架的指令
npm update -g @vue/cli
```



### （2）Vite

```shell
npm init vue@latest
#或者
npm create vite@latest
```



## 3、组件通信

父组件传递给子组件：<font color=red>通过props属性</font>

子组件传递给父组件：<font color=red>通过$emit触发事件</font>

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171657521.png" alt="image-20211117165726928" style="zoom:50%;" />



### （1）父组件到子组件

​	props是可以在组件上注册一些自定义的attribute；<font color=deepred>父组件给这些attribute赋值，子组件通过attribute的名称获取到对应的值</font>。

#### 1.1、props两种常见用法

- 方式一：<font color=blue>**字符串数组**</font>，数组中的字符串就是attribute的名称
- 方式二：<font color=blue>**对象类型**</font>，对象类型可以指定attribute名称的同时，指定它需要传递的类型、是否是必须的、设置默认值等；



##### ①props的数组用法

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171853810.png" alt="image-20211117185256529" style="zoom:50%;" />

数组用法只能说明传入的attribute的名称，并**不能对其进行任何形式的限制**。



##### ②props的对象用法

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171928678.png" alt="image-20211117192847008"  />

可以指定传入attribute的<font color=deepred>**类型、是否必传、是否设置默认值**</font>

- type类型的可设置项
  - String
  - Number
  - Boolean
  - Array
  - Object
  - Date
  - Function
  - Symbol



#### 1.2、prop的大小写命名

​	HTML 中的 attribute 名是大小写不敏感的，所以<font color=deepred>浏览器会把所有大写字符解释为小写字符</font>；

​	当使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需<font color=deepred>要使用其等价的 kebab-case (**短横线分隔命名**) 命名</font>；

```js
Vue.component('blog-post', {
  // 在 JavaScript 中是驼峰写法
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

```html
<!-- 在 HTML 中是短横线连接的 -->
<blog-post post-title="hello!"></blog-post>
```



#### 1.3、非props的attribute

- 当传递给一个组件某个属性，但是该属性并没有定义对应的props或者emits时，就称之为 **非Prop的 Attribute**；常见的包括class、style、id属性等；

- 当组件有单个根节点时，非Prop的Attribute将自动添加到根节点的Attribute中；

  <img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171940448.png" alt="image-20211117194037100" style="zoom:67%;" />

- **可以<font color=deepred>在组件中设置 inheritAttrs取消根元素继承attribute: false</font>，还<font color=red>可以通过$attrs来访问所有的非props的attribute</font>**；

![image-20211117194007881](https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171940604.png)

- 多个根节点的attribute如果没有显示的绑定，那么会报警告，我们必须手动的指定要绑定到哪一个属性上



### （2）子组件到父组件

#### 2.1、执行流程

子组件**定义emits属性**，使用**this.$emit发送事件和参数**给父组件，父组件**调用自定义事件接收**。

- 子组件：


<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171946905.png" alt="image-20211117194618034" style="zoom:50%;" />

- 父组件：


<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171947506.png" alt="image-20211117194656525" style="zoom:67%;" />



#### 2.2、自定义事件参数

自定义事件可以传递参数（载荷），父组件调用自定义事件时方法可以接收参数

![image-20211117194830862](https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171948231.png)

<font color=red>在vue3中可以对传递的参数进行验证</font>

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111171950834.png" alt="image-20211117195039914" style="zoom:67%;" />





### （3）非父子组件的通信

- <font color=red>Provide/Inject</font>
- <font color=red>Mitt全局事件总线</font>

#### 3.1、Provide/Inject

​	用于非父子组件之间共享数据，比如一些深度嵌套的组件，子组件想要获取父组件的部分内容，这种情况下使用props逐级传递会非常麻烦。因此可以使用Provide/Inject。

- 无论层级结构有多深，父组件都可以作为其所有子组件的依赖提供者。
- 父组件有一个provide选项来提供数据。
- 子组件有一个inject选项来使用这些数据。

基本使用方法：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261109457.png" alt="image-20211126110837688" style="zoom: 67%;" />

如果provide中提供的一些数据是来自data，直接通过this取值会报错；

想实现此功能需要使用<font color=red>Vue3中的新特性：computed函数</font>。

![image-20211126111354102](https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261113886.png)

**<font color=red>computed返回的是一个ref对象，需要取出其中的value来使用</font>**。



#### 3.2、全局事件总线mitt库

- Vue3从实例中移除了 $on、$off 和 $once 方法，所以如果希望**继续使用全局事件总线，要通过第三方的库**:

①首先安装这个库`npm install mitt`

②其次封装一个工具eventbus.js

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261125131.png" alt="image-20211126112519558" style="zoom:67%;" />

③在项目中使用

在下层组件中中监听事件，在外层中触发trigger事件

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261139868.png" alt="image-20211126113856625" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261137682.png" alt="image-20211126113705966" style="zoom:50%;" />

- mitt的事件取消

```js
emitter.all.clear() //取消emitter中所有的监听
//定义一个函数
function onFoo(){}
emitter.on('foo',onFoo)	//监听
emitter.off('foo',onFoo)	//取消监听
```



## 4、插槽slot

### （1）定义

**插槽的使用过程就是<font color=red>抽取共性、预留不同</font>；**

将<font color=red>共同的元素、内容依然在组件内</font>进行封装，同时将<font color=red>不同的元素使用slot作为占位</font>，让外部决定到底显示什么元素。



### （2）使用原则

Vue将<font color=#ff9966>`<slot>`元素作为承载分发内容</font>的出口；

在封装的组件中，使用特殊的元素`<slot>`接可以为封装组件开启一个插槽；

该插槽插入什么内容取决于父组件如何使用；



### （3）插槽的基本使用

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261355959.png" alt="image-20211126135501331" style="zoom:67%;" />

子组件中不写插槽slot父组件中的p标签就无法显示

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261355530.png" alt="image-20211126135547896" style="zoom:67%;" />

slot中可以插入普通的内容、html元素、组件元素，都是可以的。



### （4）插槽的默认内容

​	如果**没有插入对应的内容，那么需要显示一个**默认的内容，这个<font color=red>默认内容**只会在没有提供插入的内容时才会显示**</font>。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261453500.png" alt="image-20211126145302149" style="zoom:50%;" />



### （5）多个插槽的效果

​	如果一个组件中<font color=red>含有多个插槽</font>，插入多个内容时**每个插槽都会获取到我们插入的内容来显示**。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261458651.png" alt="image-20211126145811482" style="zoom:50%;" />



### （6）具名插槽的使用

为了实现插槽的内容是对应的显示，因此需使用具名插槽；

具名插槽就是<font color=red>给插槽取一个名字，`<slot>`元素有一个特殊的attribute：name；</font>一个不带name的slot，会带有隐含的名字default。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261612464.png" alt="image-20211126161237075" style="zoom:50%;" />



### （7）动态插槽名

​	上面的插槽名称都是固定的，可以<font color=red>通过`v-slot:[dynamicSlotName`方式动态绑定一个名称。</font>

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261616075.png" alt="image-20211126161633924" style="zoom:50%;" />



### （8）具名插槽缩写

​	跟v-on和v-bind一样，<font color=red>v-slot:可以简写替换为#</font>



### （9）作用域插槽

- 案例

1. 在父组件App.vue中的data定义好数据names数组；
2. 通过props传递给子组件ShowNames中接收；
3. ShowNames组件中遍历names数据；
4. 在子组件中定义插槽的传递参数；
5. 在父组件中通过v-slot:default的方式获取到slot的props；
6. 使用slotProps中的item和index渲染；

<img src="/Users/rain_chen/Library/Application Support/typora-user-images/image-20211126170819997.png" alt="image-20211126170819997" style="zoom:50%;" />

⚠️：此案例中template上不能绑定:key，会报错。

- 独占默认插槽的缩写

如果插槽是默认插槽default，那么在使用的时候<font color=red> v-slot:default="slotProps"可以简写为v-slot="slotProps"</font>：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261722784.png" alt="image-20211126172202862" style="zoom:50%;" />

如果我们的插槽只有默认插槽时，组件的标签可以被当做插槽的模板来使用，这样，我们就可以将 v-slot 直接用在组件上：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261722733.png" alt="image-20211126172217870" style="zoom:50%;" />

- 默认插槽和具名插槽混合

同时存在默认插槽和具名插槽，要按照完整的template来编写；

出现多个插槽，始终为所有的插槽使用完整的基于` <template>` 的语法；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261726542.png" alt="image-20211126172633516" style="zoom:50%;" />



## 5、组件的生命周期

​	<font color=red>生命周期函数是一些钩子函数，在某个时间会被Vue源码内部进行回调</font>；通过对生命周期函数的回调，可以知道目前组件正经历什么阶段，然后就可以编写属于自己的逻辑代码。

生命周期的流程：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111281644018.png" alt="image-20211128164005459" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111281644397.png" alt="image-20211128163702532" style="zoom: 50%;" />

## 6、$refs的使用

​	某些情况下在组件中想要直接获取到元素对象或者子组件实例，Vue中<font color=blue>不推荐进行DOM操作</font>的，这时候可以<font color=deepred>给元素或者组件绑定一个**ref的attribute属性**</font>。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111281356310.png" alt="image-20211128135631292" style="zoom: 67%;" />

​	this.$refs获取的到的是一个对象，持有<font color=blue>注册过ref attribute的所有DOM元素和组件实例</font>



## 7、$parent和$root

可以通过$parent来**访问父元素**；

可以通过$root来**访问根组件**；

注意⚠️:在Vue3中已经**<font color=red>移除了$children的属性</font>**，因此不能使用该属性。



## 8、动态组件

动态组件是<font color=red>使用component组件，**通过is来实现**</font>，is属性的值时注册的组件名。

动态组件也可以实现传值和监听事件，只需将<font color=red>属性和监听事件</font>放到component上来使用。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111261832278.png" alt="image-20211126183249709" style="zoom:50%;" />



## 9、keep-alive

​	默认情况下切换组件后，组件会被销毁掉，再次回来时会重新创建组件。但是为了实现<font color=red>切换时继续保持状态</font>而不是销毁时，使用一个内置的组件**keep-alive**。

### （1）keep-alive的属性

- include - string｜RegExp | Array。只有名称匹配的组件会被缓存。
- exclude - string | RegExp | Array。任何名称匹配的组件都不会被缓存。
- max - number ｜ string。最多可以缓存多少组件实例，一旦达到这个数字，那么缓存组件中最近没有被访问的实例会被销毁。

⚠️注意：匹配首先检查<font color=red>组件自身的name选项。</font>



### （2）缓存组件的生命周期

​	缓存组件再次进入时不会执行created或者mounted等生命周期函数，而是采用**<font color=red>activated和deactivated</font>**这两个生命周期钩子函数来监听。





## 10、代码分包和异步组件

### （1）代码分包

- 默认的打包过程：
  - 组件和组件之间是<font color=deepred>通过模块化直接依赖</font>的，webpack在打包时就会<font color=deepred>将组件模块打包到一起</font>(比如一个app.js文件中)
  - 当项目依赖关系过于复杂时，打包后的<font color=deepred>单个js文件过大，造成首屏渲染速度过慢问题</font>

​	对于一些**不需要立即使用**的组件，可以单独拆分成一些小的代码块在需要时从服务器加载下来运行，也就是懒加载优化。



### （2）异步组件

​	为了实现组件<font color=red>**异步加载方式**</font>(目的是可以对其进行分包处理)，Vue提供了一个函数:**<font color=red>defineAsyncComponent</font>**。

**该函数接受两种类型的参数**：

#### 2.1、工厂函数

类型一：工厂函数，该工厂函数<font color=red>需要返回一个Promis对象</font>；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111281237191.png" alt="image-20211128123741172" style="zoom: 67%;" />



#### 2.2、对象类型

类型二：<font color=red>接受一个对象类型</font>，对异步函数进行配置；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111281238222.png" alt="image-20211128123818252" style="zoom: 67%;" />



## 11、组件的v-model

​	前面在input中可以使用v-model来完成双向绑定，默认帮助我们完成了两件事<font color=red> v-bind:value的数据绑定和@input的事件监听</font>，**vue也支持在组件上使用v-model**。

在组件上使用时，等价于如下操作：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111282148622.png" alt="image-20211128214848854" style="zoom:50%;" />

input元素不同的只是**属性名称和事件触发的名称而已**；



### （1）组件v-model的实现

​	为了实现上面的myInput组件，在组件内部的`<input>`里必须：

- 将value属性绑定到一个名叫<font color=red>**modelValue的prop上**</font>；
- input事件触发时把新值<font color=red>**通过自定义的update:modelValue事件抛出去**</font>；

```vue
<!--父组件-->
<my-input v-model="message"></my-input>
```

```vue
<!--子组件-->
<template>
	<div>
    <input :value="modelValue" @input="inputChange"/>
  </div>
</template>
<script setup>
	const props = defineProps({
    modelValue
  })
  const emits = defineEmits(["update:modelValue"])
  const inputChange = (event) => {
    emits('update:modelValue',event.target.value)
  }
</script>
```



### （2）通过computed实现（优雅）

```vue
<!--在组件内部也使用双向绑定的方法，使用计算属性setter和getter-->
<template>
	<div>
    <input v-model="value"/>
  </div>
</template>
<script>
	export default{
    props:["modelValue"],
    emits:["update:modelValue"],
    computed:{
      value:{
        get(){
          return this.modelValue;
        },
        set(value){
          this.$emit("update:modelValue",value)
        },
      }
    }
  }
</script>
```



### （3）<font color=orange>绑定多个属性</font>(多input填空)

```vue
<!--父组件-->
<my-input v-model="message" v-model:title="title" />
```

```vue
<!--子组件-->
<template>
	<div>
    <input :value="modelValue" @input="inputChange"/>
    <input :value="title" @input="input2Change"/>
  </div>
</template>
<script>
	export default{
    props:["modelValue","title"],
    emits:["update:modelValue","update:title"],
    methods:{
      inputChange(event){
        this.$emit("update:modelValue",event.target.value)
      },
      input2Change(event){
        this.$emit("update:title",event.target.value)
      }
    }
  }
</script>
```

 **v-model:title相当于做了两件事**

- 绑定了title属性;
- 监听了 @update:title的事件;



## 12、Mixin

​	组件化开发过程中，**组件和组件之间会存在相同的代码逻辑**，可以使用Mixin对**<font color=blue>相同的代码逻辑进行抽取</font>**。

### （1）特点：

- Mixin提供了一种非常灵活的方式，来<font color=red>分发Vue组件中的可复用功能</font>;
- 一个Mixin对象可以包含<font color=red>任何组件选项</font>;
- 当组件使用Mixin对象时，<font color=red>所有Mixin对象的选项将被混合进入该组件本身的选项</font>中;



### （2）基本使用

![image-20211201203822524](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112012038482.png)



### （3）Mixin的合并规则

如果Mixin对象中的选项和组件对象中的选项**发生了冲突**，按如下规则操作：

- **如果是<font color=blue>data函数</font>的返回值对象**

  返回值对象默认情况下会<font color=red>进行合并</font>;

  如果data返回值对象的属性发生了冲突，那么会<font color=red>保留组件自身的数据</font>;

- **如果是<font color=blue>生命周期</font>钩子函数**

  生命周期的钩子函数会被合并到数组中，<font color=red>都会被调用</font>；

- **值为对象的选项，例如<font color=blue> methods、components 和 directives</font>，将被合并为同一个对象**

  比如都有methods选项，并且都定义了方法，那么它们<font color=red>都会生效</font>;

  但是如果对象的key相同，那么会取组件对象的键值对;



### （4）全局混入Mixin

**如果组件中的某些选项是所有组件都需要拥有的，那么可以使用全局的mixin**

​	全局的Mixin可以使用，<font color=blue>**应用app的方法** mixin 来完成注册</font>;

​	一旦注册，那么<font color=blue>全局混入的选项将会影响每一个组件</font>；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112012052869.png" alt="image-20211201205150359" style="zoom:67%;" />



# 三、Composition API

## 1、Options API

​	Vue2中编写组间的方式就是Options API:

- data定义数据、methods中定义方法、computed中定义计算属性、watch中监听属性改变，也包括生命 周期钩子;
- 缺陷是代码碎片化



## 2、Composition API

​	<font color=red>**将同一个逻辑关注点的代码收集在一起的写法，实际使用在Vue组件中通过setup函数实现。**</font>

​	setup其实就是组件的另外一个选项，只不过这个选项强大到可以用它来**替代之前所编写的大部分其他选项**，比如data、computed、watch、methods、生命周期等。



## 3、setup函数

### （1）setup函数参数

该函数有两个参数，<font color=red>**第一个参数是props，第二个参数是context。**</font>

#### 1.1、参数props

​	该参数表示<font color=blue>父组件传递过来的参数</font>

- 会被放到props对象中，可以直接在setup中使用；
- 因为setup中不能使用this.XX获取props的值，所以将props作为参数直接调用；



#### 1.2、参数context

​	该参数<font color=blue>是个对象，包含**三个属性**</font>

- attrs：所有非prop的attribute；
- slots：父组件传递过来的插槽；
- emit：当组件内部需要发出事件时会用到
  - 因为setup中不能使用this.$emit发出事件，所以将emit作为参数直接调用；



### （2）setup函数返回值

- 返回定义的<font color=red>变量</font>，替代data选项；
- 返回<font color=red>执行函数</font>，替代methods对象；

```js
setup(){
	const name = "chenge";
	let counter = 100;
	const increment = () => {
		counter++;
	}
	return{ name, counter, increment }
}
```



### （3）setup不可以使用this

官网关于this的描述：

1. 表达的含义是<font color=blue>this并没有指向当前组件实例</font>；
2. 并且<font color=red>**在setup被调用之前，data、computed、methods等都没有被解析**</font>；



## 4、Reactive

​	定义一个变量，默认情况下，<font color=red>Vue不会跟踪它的变化</font>，来**引起界面的响应式操作**。

- reactive函数可以给在setup中定义的数据<font color=red>提供响应式特性</font>，数据再次被使用时就会进行依赖收集；
- <font color=red>**data选项**，也是在内部交给了reactive函数将其编程响应式对象的</font>；
- reactive函数对**传入的参数是有限制的**，要求必须传入的是<font color=red>**一个对象或者数组类型**</font>，如果传入一个基本类型（string、number、boolean）会报一个警告⚠️；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112270946859.png" alt="image-20211227094644986" style="zoom:50%;" />



## 5、Ref

​	ref会返回一个<font color=red>**可变的响应式对象**</font>，该对象作为一个<font color=red>**响应式的引用**</font>维护者它内部的值；

⚠️注意：

1. 在<font color=blue>template模板中</font>引入ref的值**<font color=red>不需要</font>通过 ref.value 的方式来使用**，Vue会自动帮助我们进行解包操作;
2. 在<font color=blue> setup 函数内部</font>依然是一个 ref引用，对其进行操作时**<font color=red>依然需要</font>使用 ref.value的方式**;
3. 将ref放到一个reactive的属性当中，在模板中使用时**会自动解包**。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112271018330.png" alt="image-20211227101743353"  />

自动解包：

![image-20211227101846792](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112271018388.png)



## 6、readonly

​	如果希望被传递的响应式对象**不能被修改**，Vue提供了readonly的方法。readonly会返回<font color=blue>原生对象的只读代理</font>（<font color=red>使用proxy劫持set方法使对象不能被修改</font>）；

### （1）参数类型

使用readonly常见有三种类型参数：

- 普通对象
- reactive返回的对象
- ref的对象



### （2）使用规则

- readonly返回的对象都是**不允许修改的**；
- readonly处理原来的对象时**允许被修改**；
- 本质上就是<font color=red>**readonly返回的对象的setter方法**</font>被劫持了；
- 常用于组件传值时保证传递的值不被修改；

![image-20211227134817231](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112271348945.png)



## 7、reactive判断

### （1）isProxy

检查对象是否是由 reactive 或 readonly创建的 proxy。



### （2）isReactive

检查对象是否由reactive创建的响应式代理；

如果该代理是 readonly 建的，但包裹了由 reactive 创建的另一个代理，它也会返回 true。



### （3）isReadonly

检查对象是否是由 readonly 创建的只读代理。



### （4）toRaw

返回reactive或readonly代理的原始对象。



### （5）shallowReactive

创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换 (深层还是原生对象）。



### （6）shallowReadonly

创建一个 proxy，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换（深层还是可读、可写的）



## 8、toRefs和toRef

### （1）toRefs

对reactive返回的<font color=blue>响应式对象进行解构</font>获取值，那么<font color=red>数据**不再是响应式的**</font>

```js
const state = reactive({
  name:"chenge",
  age:24
});
const {name,age} = state;
```

为了实现解构后的属性是响应式的可以使用**toRefs函数**，可以<font color=red>将reactive返回的对象中的属性都转成ref</font>。

```js
const {name,age} = toRefs(state)
```

这时name和age本身都是ref，这种做法相当于在<font color=red>state.name和ref.value</font>之间建立了链接，<font color=red>任何一个修改都会引起另外一个的变化</font>。



### （2）toRef

​	如果原始对象是非响应式的，使用toRef包裹后不会更新视图，但数据是会变的；如果原始对象是响应式的是会更新视图并且改变数据的；

<font color=blue>转换一个reactive对象中的属性为ref</font>，可以使用toRef的方法：

```js
const before = {
  name:"zhang"
}
const state = toRef(before,'name');
const changeName = () => state.name = "chenge";
//页面视图不变，但是before和state中的name的值都变了
```



## 9、ref其他的API

### （1）unref

**<font color=red>获取一个ref引用中的value</font>**。

该方法的参数**是ref返回内部的值**，**否则返回参数本身**。是`val = isRef(val) ? val.value : val`的语法糖函数。

```js
let testRefObj = ref({ name: "alex", age: "5" });
const testVal = unref(testRefObj)//如果是ref则返回ref.value的值，不是则返回传入的对象
```



### （2）isRef

判断值是否是一个ref对象



### （3）shallowRef

创建一个浅层的ref对象



### （4）triggerRef

手动触发和 shallowRef 相关联的副作用

```js
const info = shallowRef({name:"zhang"});
const changeInfo = () => {
  info.value.name = "chenge";//此时不是响应式的
  triggerRef(info);//手动触发改变
}
```



### （5）customRef

创建一个**<font color=blue>自定义的ref</font>**，并**对其依赖项跟踪和更新触发**进行**显示控制**；

- 需要<font color=red>一个工厂函数</font>，该<font color=red>函数接受**track**和**trigger**函数</font>作为参数；
- 并且应该<font color=red>返回一个带有get和set的对象</font>；

下面是一个对双向绑定的属性进行debounce(节流)的操作

![image-20211227173338377](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112271733595.png)



## 10、ref获取DOM

​	只需要<font color=blue>定义一个ref对象</font>，**<font color=red>绑定到元素或者组件的ref属性上</font>**即可。ref的属性值必须和定义的ref对象属性名相同。

```vue
<template>
	<div ref="title"></div>
</template>
<script>
	import {ref} from "vue";
    export default {
        setup(){
            //这里title.value就是上面title的dom对象
            const title = ref(null);
            return { title }
        }
    }
</script>
```



## 11、计算属性computed

### （1）使用方式一

接收一个<font color=red>getter函数</font>，并为 getter 函数返回的值，返回一个不变的 ref 对象

```js
setup{
	const origin = ref("zhang");
	const change = computed(() => {
		retrun origin.value + "数据处理操作"
	})
}
```



### （2）使用方式二

接收一个<font color=red>具有get和set的对象</font>，返回一个可变的（可读写）ref对象

```js
setup{
	const origin = ref("zhang");
    const change = computed({
        get: () => {
            return origin.value + "数据处理操作"
        },
        set: (newValue) => {
            origin.value = newValue
        }
    });
}
```



## 12、数据侦听watch

在Composition API中，可以**<font color=red>使用watchEffect和watch</font>来完成响应式数据的侦听**。

### （1）watch

watch需要**<font color=blue>手动指定侦听的数据源</font>**，并执行回调函数。<font color=red>只有当被侦听的目标发生变化时才会执行回调</font>。默认是开启深度监听的。

#### 1.1、侦听单个数据

- 单个属性：<font color=red>**一个getter函数**</font>，但是该getter函数必须引用可响应式的对象（reactive或ref）

```js
setup{
	const my = reactive({name:"chenge", age:25})
    watch(my.name, (newValue,oldValue) => {
        console.log("值变化了")
    })
}
```



- 整个对象：<font color=red>**直接写入一个可响应式的对象**</font>，reactive或者ref（常用的是ref）

```js
setup(){
	const name = ref("chenge")
    watch(name, (newValue,oldValue) => {
        console.log("值变化了")
    })
}
```



#### 1.2、侦听多个数据

```vue
<script>
    import { ref, watch } from "vue";
    export default {
        setup(){
            const name = ref("chenge");
            const age = ref(25);
            watch([name,age], (newValue, oldValue) => {
                console.log("数据变化了,变化数组")
            })
        }
    }
</script>
```



#### 1.3、watch选项

若要进行**<font color=blue>深层侦听</font>**，要设置<font color=red>deep为true</font>；

若要进行**<font color=blue>立即执行</font>**，要设置<font color=red>immediate为true</font>；

```js
setup{
	const info = reactive({
        name:"chenge",
        hobby:{ type:"F1" }
    })
    watch(info, (newValue,oldValue) => {
        console.log("值变化了")
    },{
        immediate:true,
        deep:true
    })
}
```



#### 1.4、对比watchEffect

watch的特点：

- 懒执行副作用(第一次不会直接执行)；
- 更具体的说明当哪些状态发生变化时，触发侦听器的执行；
- 可以访问侦听状态变化前后的值；



### （2）watchEffect

watchEffect用于<font color=blue>**自动收集响应式数据的依赖**</font>；

#### 2.1、基本使用

​	watchEffect传入的函数<font color=blue>会被立即执行一次</font>，并且在**执行的过程中会收集依赖**；

​	<font color=red>只有收集的依赖发生变化时，watchEffect传入的函数才会再次执行</font>；

```js
const name = ref("zhang");
const age = ref(24);
watchEffect(() => {
  console.log("watchEffect执行",name.value, age.value);
})
```



#### 2.2、停止侦听

调用watchEffect的**<font color=red>返回值函数</font>**即可停止侦听

```js
const stopWatch = watchEffect(() => {
  console.log("watchEffect执行",name.value, age.value);
})
const change = () => {
    if(XX){ stopWatch() }
}
```



#### 2.3、清除副作用

副作用场景： 比如在开发时，侦听函数中执行网络请求，网络请求还没有达到时停止了侦听器，或者侦听器侦听函数被再次执行了，那么上次网络请求应该被取消，这时就清除了上一次的副作用。

- 给watchEffect传入的函数被回调时，其实可以获取到一个参数:<font color=blue>onInvalidate</font>
  - 当**副作用即将重新执行** 或者 **侦听器被停止** 时会执行该函数传入的回调函数；
  - 可以在传入的回调函数中，执行一些清除工作;

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112272256488.png" alt="image-20211227225625399" style="zoom:80%;" />



#### 2.4、执行时机

默认情况下，<font color=red>组件的更新会在副作用函数执行之前</font>，尝试在副作用函数中获取元素：

![image-20211227231805906](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112272318945.png)

执行结果会先后打印两次，分别为null和元素Dom对象。

这是因为<font color=blue>**setup函数在执行时就会立即执行传入的副作用函数**，这个时候DOM并没有挂载</font>，所以打印为null；而当DOM挂载时，会给title的ref对象赋值新的值，副作用函数会再次执行，打印出来对应的元素。



#### 2.5、修改执行时机

默认情况watchEffect在组件渲染之前执行

- 通过`flush:"post"`可以使侦听器延迟到组件渲染之后再执行；
- 设置`flush:"sync"`可以使响应式依赖发生改变时立即触发侦听器（强制同步触发），此操作会带来性能问题；

![image-20211227233746736](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112272337582.png)



## 13、生命周期钩子

可以使用<font color=red>**直接导入的 onX 函数**</font>注册生命周期钩子:

```js
onMounted(() => {
  console.log("onMounted")
});
onUpdated(() => {
  console.log("onUpdate")
})
```

![image-20211228111859058](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281119381.png)

注意⚠️：因为<font color=red>**`setup`是围绕`beforeCreate`和`created`生命周期钩子函数运行的**</font>，所以beforeCreate和created钩子中编写的任何代码都应该直接在`setup`函数中编写。



## 14、Provide/Inject函数

​	在composition API<font color=red>使用`Provide`和`Inject`函数进行非父子组件传值</font>，通过`provide`来提供数据，在后代组件中通过`inject`来注入需要的属性和对应的值。

### （1）provide

可以传入两个参数：<font color=deepred>name（提供的属性名称）、value（提供的属性值）</font>；

```js
let counter = 100;
let info = {name:"zhang",age:24};
provide("counter",counter);
provide("info",info);
```



### （2）inject

inject可以传入两个参数：<font color=deepred>要接收的property的name、默认值</font>；

```js
const counter = inject("counter"); //100
const info = inject("info"); //{name:"zhang",age:24}
```



### （3）数据响应式

provide提供值时<font color=deepred>**支持使用ref和reactive**</font>。

```js
let counter = ref(100);
let info = reactive({name:"zhang",age:24});
provide("counter",counter);
provide("info",info);
```



### （4）修改响应式数据

如果需要在接收处修改传递的响应式数据，那么<font color=deepred>在数据提供的位置**将修改方法进行共享**</font>，在后代组件中进行调用：

```js
const changeInfo = () => {
  info.name = "chenge"
}
provide("changeInfo",changeInfo)
```



## 15、componsition使用案例（抽取hooks函数）

​		在js文件中<font color=blue>export default function中编写</font>，return出数据和方法，在组件内import引入，在setup函数中声明js文件ruturn的内容，然后在setup中return出去即可在模板中使用。

注意⚠️：**一般hooks文件命名时使用use开头**。

![image-20211228134831510](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281348868.png)

![image-20211228134940801](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281349732.png)



## 16、script setup语法

在<font color=red>单文件组件（SFC）中使用组合式API的编译时语法糖</font>

### （1）特点

- 更少的样板内容，代码更简洁
- 能使用纯 Typescript 声明 prop 和抛出事件
- 更好的运行时性能
- 更好的 IDE 类型推断性能



### （2）执行时机

普通`<script>`只在组件被<font color=deepred>首次引入时执行一次</font>；

`<script setup>`中的代码会在<font color=deepred>每次实例被创建时执行</font>；



### （3）使用规则

​	在该标签内声明的顶层绑定（变量、函数声明、import引入）都可以直接在模板中使用。包括导入的组件也可以直接使用。



### （4）defineProps()

​	`script setup`中的props声明获取方法

```js
const props = defineProps({
  name:{
    type: String,
    default:""
  }
})
```



### （5）defineEmits()

​	`script setup`中的emits声明获取方法

```js
const emit = defineEmits(["change"])
//使用时直接调用
emit("change","传递的参数")
```



### （6）defineExpose()

​	<font color=deepred>`script setup`组件默认是关闭的</font>，通过ref获取组件实例的方法和变量不会直接暴露在`<script setup>`中，需要<font color=blue>在被获取实例的组件中使用defineExpose方法声明抛出</font>。

```js
//实例组件
const foo = () => {"执行的操作"}
defineExpose({foo})

//使用实例的组件
const comRef = ref(null)
comRef.value.foo()即可使用实例组件暴露出来的方法
```



## 17、自定义指令

​	除过v-show、v-for等指令，Vue支持<font color=red>使用自定义指令</font>对，需要<font color=red>对DOM元素进行底层操作</font>。

### （1）指令分类

**自定义指令分为两种：**

- <font color=blue>自定义局部指令</font>:**组件中通过 <font color=red>directives 选项</font>**，只能在当前组件中使用；
- <font color=blue>自定义全局指令</font>:**app的 <font color=red>directive 方法</font>**，可以在任意组件中被使用；



### （2）指令实例

下面以三种方式实现一个元素挂载完成后自动获取焦点的功能：

##### 实现方式1：聚焦的默认实现

![image-20211228162854628](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281628790.png)



##### 实现方式2:局部指令

自定义一个v-focus的局部指令，只需用在**组件选项中使用directives即可**。

它是一个对象，在对象中编写我们自定义指令的名称(注意:这里不需要加v-)。

自定义指令有一个生命周期，是在组件挂载后调用的 mounted，可以在其中完成操作。

![image-20211228163324043](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281633931.png)



##### 实现方式3:全局指令

自定义一个**全局的v-focus指令**可以让我们在任何地方直接使用

![image-20211228163510708](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281635859.png)



### （3）指令生命周期

一个指令定义的对象，Vue提供了下面的钩子函数：

<font color=red>**created**</font>：在绑定元素的 attribute 或事件监听器被应用之前调用；

<font color=red>**beforeMount**</font>：当指令第一次绑定到元素并且在挂载父组件之前调用；

<font color=red>**mounted**</font>：在绑定元素的父组件被挂载后调用；

<font color=red>**nbeforeUpdate**</font>：在更新包含组件的 VNode 之前调用；

<font color=red>**nupdated**</font>：在包含组件的 VNode及其子组件的 VNode更新后调用；

<font color=red>**beforeUnmount**</font>：在卸载绑定元素的父组件之前调用；

<font color=red>**unmounted**</font>：当指令与元素解除绑定且父组件已卸载时，只调用一次；



### （4）指令参数和修饰符

**参数即el、binding、vnode和prevNode**

指令是**可以动态的**，例如在`v-mydemo:[argument]="value"`中，`argument`参数可以根据组件实例数据进行更新，从而自定义指令可以在项目中被灵活应用。

- el：指令绑定到的元素，可用于直接操作DOM；

- binding：包含以下property的对象

  - instance：使用指令的组件实例
  - <font color=red>**value：传递给指令的值**</font>
  - oldvalue：先前的值，尽在beforeUpdate和Updated中可用
  - arg：参数传递给指令
  - <font color=red>**modifiers：包含修饰符的对象**</font>，在`v-mydemo.foo.bar`中，修饰符对象为{foo:true, bae:true}
  - dir：一个对象，在注册指令时作为参数传递

  其余两个参数不常用。

  

![image-20211228165314754](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281653106.png)

上面的例子info是参数的名称，aaa、bbb是修饰符的名称，后面是传入的具体的值，打印bindings获取到对应的内容。



### （5）指令案例

时间格式化指令v-format-time

```js
//format-time.js
import dayjs from 'dayjs'//轻量处理时间日期的js库
export default function(app){
  let format = "YYYY-MM-DD HH:mm:ss";
  app.directive("format-time",{
    created(el,bindings){
      if(bindings.value){
        format = bindings.value;
      }
    },
    mounted(el){
      const textContent = el.textContent;
      let timestamp = parseInt(el.textContent);
      if(textContent.length === 10){
        timestamp = timestamp*1000;
      }
      el.textContent = dayjs(timestamp).format(format)
    }
  })
}

//和format-time同级的index.js（如果有多个自定义指令都从这一个文件导出）
import registerFormatTime from './format-time';
export default function registerDirectives(app) {
  registerFormatTime(app);
}

//项目main.js文件
const app = createApp(App);
registerDirectives(app);
app.mount("#app");
```



## 18、Teleport

封装组件A在另一个组件B中使用时，A会被挂载在B的某个位置，形成一颗DOM树结构。

但是在某些情况下<font color=blue>希望B组件不挂载在A上</font>，可能是移动到Vue app之外的位置，比如body上等，这时可以使用teleport来完成。

### （1）语法属性

teleport是<font color=red>Vue提供的内置组件</font>，有两个属性：

1、<font color=red>**to**</font>指定将其中的内容<font color=deepred>移动到的目标元素</font>，可以使用选择器；

2、<font color=red>**disabled**</font>是否<font color=deepred>禁用teleport 的功能</font>；

![image-20211229115247947](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112291152009.png)



### （2）使用事项

#### 2.1、嵌套组件

可以在 teleport 中添加组件，并且也可以给他传入一些数据，会**整体都绑定到目标元素上**；



#### 2.2、多个teleport

多个teleport应用到**同一个目标上**(to的值相同)，那么这些目标<font color=deepred>**会进行合并**</font>；



## 19、suspense特性

**Suspense是一个内置的全局组件，该组件有两个插槽：**

<font color=red>default：</font>如果default可以显示，那么显示default的内容；

<font color=red>fallback：</font>如果default无法显示，那么会显示fallback的内容；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111281317873.png" alt="image-20211128131712253" style="zoom: 67%;" />



## 20、Vue插件

通常**向Vue全局添加一些功能**时，会采用**插件的模式，它有两种编写方式**：

### （1）编写方式

#### 1.1、对象类型

一个对象，但是必须包含一个<font color=red> install 的函数</font>，该<font color=red>函数**会在安装插件时执行**</font>；

```js
//对象类型的写法
export default {
  install(app){
    app.config.globalProperties.$name = "zhangyuchen"
  }
}
```

注册插件

```js
//main.js文件
import pluginObject from './plugins/plugins_object';
app.use(pluginObject);
```



#### 1.2、函数类型

一个function，这个函数会在<font color=red>**安装插件时自动执行**</font>；

```js
//函数类型的写法  
export default function(app) {
  console.log(app);
}
```

注册插件

```js
//main.js文件
import pluginFunction from './plugins/plugins_function';
app.use(pluginFunction);
```



### （2）插件功能

插件可以**完成的功能没有限制**，比如：

- <font color=deepred>添加全局方法或者 property</font>，通过把它们添加到config.globalProperties上实现；
- <font color=deepred>添加全局资源:指令/过滤器/过渡</font>等；
- 通过<font color=deepred>全局mixin</font>来添加一些组件选项；
- <font color=deepred>一个库，提供自己的API</font>，同时提供上面提到的一个或多个功能；



## 21、h函数

​	Vue推荐在**使用模板**来创建HTML，然后一些特殊的场景需要**JavaScript的完全编程的能力**，这个时候可以使用**<font color=pink>渲染函数</font>** ，它**比模板更接近编译器**；

### （1）认识h函数

- vue在生成真实的DOM之前，会<font color=skyblue>将节点转换成VNode</font>组成在<font color=blue>一颗树结构</font>，就是<font color=red>**虚拟DOM（VDOM）**</font>。
- 事实上template中的HTML最终也是<font color=skyblue>使用**渲染函数**</font>生成的<font color=red>对应的VNode</font>。

- 如果想充分的利用JavaScript的编程能力，可以自己<font color=skyblue>编写 createVNode 函数</font>，生成对应的VNode;



### （2）使用h函数

1. <font color=red>h()函数</font>是一个<font color=pink>用于**创建 vnode 的一个函数**</font>，
2. 更准确的**命名应该是<font color=deepred>createVNode()函数</font>，但是为了简化便称之为h()函数**。



### （3）h函数语法

h函数接收三个参数

```js
render(){
  return h("h2",{class:"title"},"内容")
}
```



#### 3.1、参数一

类型：String ｜ Object ｜ Function

一个<font color=skyblue>HTML标签或者组件名</font>，该参数是**必须的**；

#### 3.2、参数二

类型：Object 

<font color=skyblue>与attribute、prop和事件相对应的对象</font>，会在模板中使用。该参数是**可选的**；



#### 3.3、参数三

类型：String ｜ Array ｜ Object

子VNode，<font color=skyblue>使用`h()`创建，或使用字符串获取“文本Vnode”或者有插槽的对象</font>。该参数是**可选的**；



- 如果没有第二个参数，那么通常可以将第三个参数作为第二个参数传入;
- <font color=red>为了避免产生歧义，可以将null作为第二个参数传入，将children作为第三个参数传入</font>;



### （4）使用h函数

h函数可以在两个地方使用：

#### 4.1、render函数中

```js
import {h} from "vue";
export default {
  render(){
    return h('div',{class:"app"},"hello app")
  }
}
```



#### 4.2、setup函数

<font color=red>setup函数选项</font>中(setup本身需要是一个函数类型，函数再返回h函数创建的VNode)；

```js
import {h} from "vue";
export default {
  setup(){
    return () => h('div',{class:"app"},"hello app")
  }
}
```



### （5）h函数案例(计数器)

```js
import {h} from "vue";
export default {
  data(){
    return {
      counter:0
    }
  },
  render(){
    return h(
      "div",
      {class:"app"},
      [
        h("h2", null, `当前计数:${this.counter}`),
        h("button", {onClick:() => this.counter++}, "+1")
      ]
    )
  },
}
```



### （6）函数组件和插槽的使用

```js
//父组件
import { h } from "vue";
import HelloWorld from "./HelloWorld.vue";
export default {
  render() {
    return h("div", null, [
      h(HelloWorld, null, {
        default: (props) =>h("span",null,`app传入到HelloWorld中的内容: ${props.name}`)
      })
    ])
  }
};

//子组件
import {h} from "vue";
export default{
  render(){
    return h("div",null,[
      h("h2",null,"hello world"),
      this.$slots.default ? this.$slots.default({name: "zhangyuchen"}): h("span", null, "我是HelloWorld的插槽默认值")
    ])
  }
}
```

显示结果为

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281535968.png" alt="image-20211228153536179" style="zoom: 50%;" />





## 22、jsx

### （1）jsx的babel配置

如果希望项目中使用jsx，那么<font color=pinkred>需要添加对jsx的支持</font>：

-  jsx通常<font color=pink>需要Babel来进行转换</font>(React编写的jsx就是通过babel转换的);
- 对于Vue来说，只需要在Babel中配置对应的插件即可;

**安装babel支持Vue的jsx插件**：

```js
 npm install @vue/babel-plugin-jsx -D
```

在babel.config.js配置文件中配置插件：

![image-20211228153935491](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281539768.png)



### （2）jsx案例(计数器)

```jsx
export default {
  data() {
    return {
      counter: 0
    }
  },
  render() {
    const increment = () => this.counter++;
    const decrement = () => this.counter--;
    
    return (
      <div>
      	<h2>当前计数: {this.counter}</h2>
  			<button onClick={increment}>+1</button>
				<button onClick={decrement}>-1</button>
			</div>
		)
	}
}
```



### （3）jsx组件使用

```jsx
//子组件
export default {
  render(){
    return(
      <div>
        <h2>这是home页面</h2>
        <div className="content">{this.$slots.default ? this.$slots.default({name:"张雨晨"}) : <span>填充默认值</span>}
        </div>
      </div>
    )
  }
}
//父组件
import Home from "./Home.vue";
export default {
  render(){
    return(
      <div>
        <Home>
          {{default: props => <button>{props.name}</button>}}
        </Home>
      </div>
    )
  } 
}
```

显示结果为

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112281607724.png" alt="image-20211228160719595" style="zoom:50%;" />





# 四、VueRouter路由

## 1、路由发展

路由器<font color=pink>主要维护的是一个映射表</font>， 映射表会**决定数据的流向**。

路由的发展阶段：

### （1）后端路由阶段

#### 1.1、后端路由过程：

​	当页面中需要<font color=red>请求不同的**路径**内容</font>时, 交给服务器来进行处理, 服务器渲染好整个页面, 并且将页面返回给客户端。这种情况下渲染好的页面, <font color=red>不需要单独加载任何的js和css</font>, 可以直接交给浏览器展示, 这样也<font color=red>有利于SEO的优化</font>。



#### 1.2、后端路由缺点：

- 一种情况是<font color=red>整个页面的模块由后端人员来编写和维护的</font>；
- 另一种情况是前端开发人员如果要开发页面, <font color=red>需要通过PHP和Java等语言来编写页面代码</font>；
- 通常情况下<font color=red>HTML代码和数据以及对应的逻辑会混在一起</font>, 编写和维护都是非常糟糕的事情；



### （2）前后端分离阶段

#### 2.1、前端渲染理解

​	每次请求涉及到的静态资源都会从**静态资源服务器获取**，这些资源**包括HTML+CSS+JS**，然后在前端对这些请求回来的资源进行渲染；

​	客户端每请求一次，都会从静态资源服务器请求文件，后端只负责提供API；

#### 2.2、前后分离

前端通过Ajax获取数据，并且可以通过JavaScript将数据渲染到页面中；

最大的优点就是前后端责任的清晰，后端专注于数据上，前端专注于交互和可视化上；



### （3）单页面富应用阶段

其实SPA最主要的特点就是在前后端分离的基础上加了一层前端路由，也就是前端来维护一套路由规则。

其核心是<font color=red>**改变URL，但是页面不进行整体的刷新**</font>



## 2、URL的hash和history

### （1）hash模式

- hash模式也就是锚点，<font color=pink>本质上是改变window.location的href属性</font>。
- <font color=deepre>可以通过直接赋值location.hash来改变href，但是页面不发生刷新</font>；
- hash的优势就是兼容好，在老版IE中都可以运行，但是缺陷是<font color=red>**路径有一个#**</font>，显得不像一个真实的路径。



### （2）history模式

![image-20220104173500311](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201041735932.png)

history接口是HTML5新增的，有六种模式改变不刷新页面:

```js
replaceState	//替换原来的路径;

pushState	//使用新的路径;

popState	//路径的回退监听;

go	//向前或向后改变路径; 

forward	//向前改变路径; 

back	//向后改变路径;
```



## 3、vue-router(4.x版本)

### （1）认识vue-router

1. vue-router是**Vue.js的官方路由**
2. vue-router是**基于路由和组件的**
   1. 路由用于<font color=deepred>设定访问路径，将路径和组件映射起来</font>；
   2. 在vue-router的单页面应用中，页面的路径的改变就是组件的切换。
3. 安装Vue Router

```js
npm install vue-router
```



### （2）路由的使用步骤

1. 第一步：创建路由需要映射的组件；
2. 第二步：<font color=deepre>通过createRouter创建路由对象</font>，并且**传入routes和history模式**；
   1. 配置路由映射：组件和路径映射关系数组；
   2. 创建基于hash或者history的模式；
3. 使用app注册路由对象
4. 使用路由: 通过`<router-link>`和`<router-view>`

![image-20220104172242458](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201041722369.png)



### （3）路由的默认路径

​	实现`<router-view>`渲染**默认组件**，需在routes中配置一个映射。

1. <font color=deepre>path配置的根路径为"/"</font>；
2. <font color=deepre>redirect重定向</font>，将根路径重定向到"/XX"路径下。

```js
const routes = [
  {path: '/',redirect: '/home'},
  {path: '/home',component: Home},
  {path: '/about',component: About}
]
```



### （4）router-link

**router-link有很多属性可以配置:**

1. <font color=pink>to属性</font>：是一个字符串，或者是一个对象；
2. <font color=pink>replace属性</font>：设置replace属性的话，当点击时，会调用router.replace()，而不是router.push()；
3. <font color=pink>active-class属性</font>：设置激活a元素后应用的class，默认是router-link-active；
4. <font color=pink>exact-active-class属性</font>：链接精准激活时，应用于渲染的 <a> 的 class，默认是router-link-exact-active；



### （5）路由懒加载

#### 5.1、优点

​	当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。把不同路由对应的组件分割成不同的代码块，然后**当路由被访问时才加载对应的组件**，从而<font color=red>提高首屏的渲染效率</font>。



#### 5.2、动态导入组件

component<font color=pink>可以传入一个组件，也可以接收一个函数</font>，**该函数需要返回一个Promise，而import函数就是返回一个Promise**

```js
const routes = [
  {
    path: '/',
    redirect: '/home',
  },
  {
    path: '/home',
    component: () => import('../pages/Home.vue')
  }
]
```



#### 5.3、打包效果

分包是没有一个明确的名称的，webpack从3.x开始支持对分包进行命名(chunk name)

```js
component: () => import( /*webpackChunkName:"home-chunk"*/ '../pages/Home.vue')
```



### （6）动态路由

#### 6.1、基本匹配

有时候需要将给定匹配模式的路由映射到同一个组件：

比如有个user组件，应该对所有用户进行渲染，但是用户的ID是不同的，在Vue Router中，可以<font color=pink>在路径中使用一个动态字段来实现</font>，可以称之为<font color=deepred>路径参数</font>。

```js
{
  path:"/user/:id",
	component:() => import('../pages/User.vue')
}
```

在router-link中进行如下跳转：

```vue
<router-link to="/user/123">用户：123</router-link>
```



#### 6.2、获取动态路由值

针对上例如何在组件中获取路由的参数值：

- template中直接通过`$route.params`获取值；
  - 在created中，通过`this.$route.params`获取值；
  - 在setup中，要使用vue-router库提供的一个hook useRoute；
    - 在这个Hook中会返回一个Route对象，对象中保存着当前路由相关的值。

```vue
<template>
	<div>
    <h2>用户界面：{{ $route.params.id }}</h2>
  </div>
</template>
<script>
  import { useRouter } from 'vue-router'；
	export default {
    created(){
      console.log(this.$route.params.id)
    },
    setup(){
      //调用router函数
      const route = useRouter();
      console.log(route.params.id);
    },
  }
</script>
```



#### 6.3、匹配多个参数

| 匹配模式              | 匹配路径              | $route.params                |
| --------------------- | --------------------- | ---------------------------- |
| /users/:id            | /users/123            | { id: '123' }                |
| /users/:id/info/:name | /users/123/info/zhang | { id: '123', name: 'zhang' } |

```js
{
  path:'/user/:id/info/:name',
  component: () => import ('../pages/User.vue') 
}
```



### （7）Not Found

**对于没有匹配到的路由，我们通常会匹配到固定的某个页面**

写法一：

```js
//固定写法
{
  path:'/:pathMatch(.*)',
  component: () => import('../pages/notfound.vue')
}
```

可以通过` $route.params.pathMatch`获取到传入的参数:

![image-20220108214239904](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201082142537.png)



写法二：

在`/:pathMatch(.*)`后面又加一个 *

```js
//固定写法
{
  path:'/:pathMatch(.*)*',
  component: () => import('../pages/notfound.vue')
}
```

- 区别在于解析时是否解析`/`

![image-20220108214635193](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201082146338.png)



### （8）路由跳转

#### 8.1、push跳转

使用push的特点是<font color=red>**压入一个新的页面**</font>，用户在点返回时，<font color=red>上一个页面还可以回退</font>。

- 常见通过方法跳转

```js
turn(){this.$route.push("/Home");};
```

- 也可以传入一个对象

  - 不传参

    ```js
    turn(){
      this.$route.push({path:'/Home'})
    };
    ```

  - query传参

    ```js
    this.$router.push({
      path:"/Home",
      query:{ name:"zhang", age:24 }
    })
    ```

- 在setup中编写情况下，<font color=red>**通过useRouter来获取**</font>

```js
const router = useRouter();
const turn = () => {
  router.push('/Home')
}
```



#### 8.2、replace跳转

对当前页面进行替换

**声明式**

```js
<router-link :to="..." replace>
```

**编程式**

```js
route.replace(...)
```



#### 8.3、前进后退

##### 1. go方法

```js
router.go(1)；//向前移动一条记录，和router.forward()相同
router.go(-1)；//返回一条记录，和router.back()相同
router.go(3)；//前进3条记录
router.go(100)；//如果没有那么多记录，静默失败
```



##### 2. back方法

通过调用`history.back()`回溯历史。相当于`router.go(-1)`;



##### 3. forward方法

过调用`history.forward()`在历史中前进。相当于`router.go(1)`;



### （9）动态修改路由

#### 9.1、动态添加路由

使用场景：根据用户不同的权限，注册不同的路由。

使用方法：<font color=red>**addRoute方法**</font>

应用案例：

```js
const newRoute = [
  path:"/new",
  component: () => import("../page/New.vue")
]
//动态添加路由
router.addRoute(newRoute)

const homeNewRoute = [
  path:"newroute",
  component: () => import('../page/NewRoute.vue')
]
//为home添加一个children子路由，只需传入对应的name
router.addRoute('home',homeNewRoute)
```



#### 9.2、动态删除路由

删除路由有以下三种方式：

1. 方式一：添加一个name相同的路由

```js
router.addRoute({path:'/about',name:'about',component:About})
//这将删除之前已经添加的路由，因为名字必须是唯一的
router.addRoute({path:'/other',name:'about',component:Other})
```



2. 方式二：通过removeRoute方法，传入路由的名称

```js
router.addRoute({path:'/about',name:'about',component:About})
//删除路由
router.removeRoute('about')
```



3. 方式三：通过addRoute方法的返回值回调

```js
const removeRoute = router.addRoute(routeRecord);
removeRoute() //删除路由如果存在的话 
```



#### 9.3、路由其他方法

`router.hasRoute()`：**检查路由是否存在**。

`router.getRoutes()`：**获取一个包含所有路由记录的数组**。



### （10）路由导航守卫

vue-router 提供的导航守卫主要用来<font color=deepred>通过跳转或取消的方式守卫导航</font>。

#### 10.1、参数

**<font color=color>全局的前置守卫beforeEach</font>是在导航触发时会被回调的**，有两个参数：

- to：即将进入的路由Route对象；
- from：即将离开的路由Route对象；



#### 10.2、返回值

- false：取消当前导航；
- true：进行默认跳转；
- 不返回或者undefined：进行默认导航；
- 返回一个路由地址：
  - 可以是一个string类型的路径；
  - 可以是一个对象，对象中包含path、query、params等信息；

- 可选的第三个参数：next
  - 在Vue2中我们是通过next函数来决定如何进行跳转的；
  - 但是在Vue3中我们是通过返回值来控制的，不再推荐使用next函数，这是因为开发中很容易调用多次next；

```js
router.beforeEach((to,from) => {
  return false
})
```



#### 10.3、常用的登录守卫

![image-20220106184841848](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201061848218.png)



#### 10.4、完整的导航流程

- 导航被触发。
- 在失活的组件里调用 beforeRouteLeave 守卫。(也就是from组件里的守卫)
- 调用全局的 beforeEach 守卫。
- 在重用的组件里调用 beforeRouteUpdate 守卫(2.2+)。
- 在路由配置里调用 beforeEnter。（路由对象里的）
- 解析异步路由组件。
- 在被激活的组件里调用 beforeRouteEnter。（to组件里的守卫）
- 调用全局的 beforeResolve 守卫(2.5+)。（全局解析守卫）
- 导航被确认。（没有回退了）
- 调用全局的 afterEach 钩子。
- 触发 DOM 更新。
- 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。



### （11）router-link的v-slot

​	3.x版本的路由中，router-link有一个tag属性，可以决定router-link渲染成什么元素。

​	但是在4.x版本的路由开始，tag属性被移除了，但是提供了更加灵活的v-slot方式来定制渲染的内容。

`router-link`<font color=blue>通过一个**作用域插槽**暴露底层的定制能力，这是一个更高阶的API</font>。

```vue
<router-link to="/about" v-slot="{href,route,navigate,isActive,isExactActive}">
  <p @click="navigator">跳转about</p>
  <div>
    <p>href:{{href}}</p>
    <p>route:{{route}}</p>
    <p>navigate:{{navigate}}</p>
    <p>isActive:{{isActive}}</p>
    <p>isExactActive:{{isExactActive}}</p>
  </div>
</router-link>
```

**注意**⚠️：

1. 需要<font color=deepred>**使用custom表示整个元素要自定义**</font>，如果不写，自定义的内容<font color=red>会被包裹在一个a元素中</font>。
2. 使用<font color=deepred>v-slot作用域插槽来获取内部传递的值</font>。

参数解析：

- href：解析后的URL，将会作为`<a>`元素的`href`属性;
- route：解析后的规范化的route对象；
- navigate：触发导航的函数；
- isActive：是否匹配的状态；
- isExactActive：是否精准匹配的状态；



### （12）router-view的v-slot

router-view也暴露一个v-slot的API，主要用于` <transition> `和 `<keep-alive>`组件来包裹路由组件。

```vue
<router-view v-slot="{ Component, route }">
  <transition :name="route.meta.transioton">
    <keep-alive>
      <component :is="Component"></component>
  	</keep-alive>
	</transition>
</router-view>
```

除过传一个props对象，还可以写`v-slot={Component,route}`

- component表示要传递给`<component>`的VNodes；
- route表示解析出的标准路由地址；



### （13）路由元信息

​	路由对象<font color=deepred>**配置meta字段**附加路由信息</font>，比如过渡名称、谁可以访问路由等。可以通过接收属性对象的meta属性来实现，可以在路由地址和导航守卫上被访问到。

```js
const routes = [
  {
    path:"/home",
    component:() => import("./XX"),
    //路由元信息
    meta:{require:true}
  }
]
```

访问时该属性包含<font color=blue>在路由守卫的to对象中`to.meta.XX`</font>





# 五、Vuex状态管理

## 1、状态管理

​	在开发中处理各种各样的数据，这些数据需要保存在应用程序的某一个位置，这些数据的管理就称之为是**状态管理**。

- 前面管理状态的方法：
  - 在Vue中，使用组件化开发方式
  - 在组件中定义data或者在setup中返回使用的数据， 这些数据称之为state;
  - 在模块template中使用这些数据，模块最终会被渲染成DOM，称之为View；
  - 模块中行为事件修改state状态，这些事件称之为actions；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202201131134739.png" alt="image-20220113113411515" style="zoom:80%;" />

<center>单向数据流理念的简单示意</center>

- 状态，驱动应用的数据源；
- 视图，以声明方式将状态映射到视图；
- 操作，响应在视图上的用户输入导致的状态变化；

在应用遇到**多个组件共享状态**时，单向数据流的简洁性很容易被破坏，比如多个视图依赖同一个状态，或者来自不同视图的行为需要变更同一状态。





## 2、Vuex

### （1）Vuex基本思想

Vuex的开发借鉴了Flux、Redux、Elm等状态管理工具。

​	1、传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。

​	2、经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

​	因此，把组件的共享状态抽取出来，以一个全局单例模式管理。在这种模式下，我们的组件树构成了一个巨大的 “试图View”， 不管在树的哪个位置，任何组件都能获取状态或者触发行为。

**通过定义和隔离状态管理中的各个概念，并通过强制性的规则来维护视图和状态间的独立性，我们的代码边会变得更加结构化和易于维护、跟踪;**

![image-20220113110237237](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201131132928.png)

### （2）使用Vuex的情景

​	如果需要构建一个中大型单页应用，可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。





## 3、Vuex的安装使用

### （1）Vuex的安装

​	npm的安装方法

```js
npm install vue@next
```



### （2）创建Store实例

#### 每个Vuex应用的核心就是store（仓库）

- store本质上是一个容器，包含应用中大部分的状态（state）

#### Vuex和单纯的全局对象的区别

- <font color=red>Vuex的状态存储是响应式的</font>
  - 当Vue组件从store中读取状态的时候，若store中的状态发生变化，那么相应的组件也会被更新；
- <font color=red>不能直接改变store中的状态</font>
  - 改变store中的状态的唯一途径就显示<font color=red>**提交 (commit) mutation**</font>；
  - 可以方便的跟踪每一个状态的变化，从而让能够通过一些工具帮助我们更好的管理应用的状态；



#### 创建步骤

1、在src目录下的store文件夹创建index.js文件；

2、引入createStore，创建Store对象；

3、在main.js文件中引入store，在createApp(App)这个app实例中use使用；



### （3）组件中使用Store

组件中使用store的场景有以下三种情况：

- 在模板中使用

```vue
<template>{{$store.state.xx}}</template>
```

- 在options api中使用，比如computed

```js
//拿到$store使用commit提交执行mutation中对应的方法（不能执行异步操作）
this.$store.commit

//拿到$store使用dispatch提交执行action中对应的方法（可以执行异步操作）
this.$store.dispatch
```

- 在setup中使用

```js
//先引入useStore
import { useStore } from 'vuex';
import {computed} from "vue";
export default{
  setup(){
    const store = useStore();
    const sCounter = computed(() => store.state.counter)
  }
}
```



### （4）单一状态树

- Vuex使用单一状态树：
  -  用一个对象就包含了全部的应用层级状态；
  -  采用的是SSOT，Single Source of Truth，也可以翻译成单一数据源；
  -  每个应用将仅仅包含一个 store 实例；
  -  单状态树和模块化并不冲突，后面会说明模块的概念；
- **单一状态树的优势**
  - 如果状态信息是保存到多个Store对象中的，那么之后的管理和维护等等都会变得特别困难，所以使用单一状态树管理应用层级的全部状态；
  - 单一状态树能让使用者采用最直接的方式找到某个状态的片段，而且在之后的维护和调试过程中可以非常方便的管理和维护。





### （5）组件获取State状态

- 在组件模版中获取状态使用`$store.state.xx`，同时也可以使用计算属性

```vue
computed: {
 	counter(){
		return this.$store.state.counter
	} 
}
```

- 如果我们有很多个状态都需要获取话，可以使用mapState辅助函数

mapState的方式一：对象类型；

```js
import { mapState } from 'vuex'
export default {
  computed: {
    ...mapState({
      counter:state => state.counter,
      name:state => state.name,
    })
  }
}
```

mapState的方式二：数组类型；

```js
import { mapState } from 'vuex'
export default {
  computed: {
    ...mapState(['name','age','counter'])
  }
}
```

也可以使用展开运算符和来原有的computed混合在一起；



### （6）在setup中使用mapState

​	在setup中单个获取装是非常简单的，通过useStore拿到store后去获取某个状态即可；

```js
import { useStore } from "vuex";
import { computed } from "vue";
export default{
  setup(){
    const store = useStore();
    const name = computed(() => store.state.name)
    return {name}
  }
}
```

​	默认情况下，Vuex并没有在setup中提供非常方便的使用mapState的方式，因此这里写一个封装函数：

```js
//封装函数，写在单独的hooks文件里useState.js
import { mapState,useStore } from "vuex";
import {computed} from "vue";
export function useState(mapper){
    const store = useStore();
    //mapState返回的是一个对象，对象中每个属性的值都是函数
    const storeStateFn = mapState(mapper);
    
    //封装一个函数，把mapState中返回对象的每个属性值函数放到computed中；
    //因为computed要求里面是个函数
    const storeState = {};
    Object.keys(storeStateFn).froEach((fnKey) => {
      const fn = storeStateFn[fnKey].bind({$store:store});
      storeState[fnKey] = computed(fn);
    })
    return storeState 
}
```



### （7）getters的基本使用

当某些属性需要经过变化后来使用时，可以使用getters

```vue
<div>
  	<!-- 在模板中使用 -->
		<h2>{{$store.getters.totalPrice}}</h2>
</div>
<script>
		//在store中使用，可以接收两个参数
  	const store = createStore({
      	getters:{
            totalPrice(state,getters){
              	return state.books.price + getters.myName
            }
        }
    })
</script>
```

另外，getters中的函数本身可以返回一个函数，那么在使用时相当于重新调用这个函数

```js
const store = createStore({
    getters:{
        totalPrice(state,getters){
          	return(price) => {
              	totalPrice += state.books.price + getters.myName;
              	return totalPrice;
            }
        }
    }
})
```



### （8）mapGetters的辅助函数

- 在optionAPI中使用

```js
computed:{
  ...mapGetters(['ttalPrice','myName']),
  ...mapGetters([
    	finalPrice:"totalPrice",
    	finalName:"myName",
  ])
}
```

- <font color=red>在setup中使用</font>

  ```js
  import { computed } from 'vue'
  import { mapGetters, useStore } from 'vuex'
  export function useGetters(mapper) {
      // 拿到store独享
      const store = useStore()
      // 获取到对应的对象的functions: {name: function, age: function}
      const storeStateFns = mapGetters(mapper)
      // 对数据进行转换
      const storeState = {}
      Object.keys(storeStateFns).forEach(fnKey => {
          const fn = storeStateFns[fnKey].bind({$store: store})
          storeState[fnKey] = computed(fn)
      })
      return storeState
  }
  ```



### （9）Mutations的基本使用

更改Vuex里store状态state的唯一方法是提交mutation：

```js
mutations:{
  increment(state){
    state.counter++
  }
}
```



### （10）Mutation携带数据

提交mutation时会携带一些数据：

```js
mutations:{
  increment(state,payload){
    state.counter += payload
  }
}
```

其中，payload可以是**对象类型**

```js
addNumber(state,payload){
  state.counter += payload.count
}
//当payload是对象时，提交方式
$store.commit({
  type:"addNumber",
  count:100
})
```



### （11）Mutations辅助函数

- 在optionAPI中使用

```js
methods: {
  	...mapMutations(["increment", "decrement"]),
    ...mapMutations({
    		add: "increment"
  	})
},
```

- 在setup中使用

```js
setup() {
    const storeMutations = mapMutations(["increment", "decrement"])
    return {
      	...storeMutations
    }
}
```



### （12）mutation原则

1. <font color=red>**mutation必须是同步函数**</font>；
2. 因为devtool工具会记录mutation的日记
3. 每一条mutation被记录，devtools都需要捕捉到前一状态和后一状态的快照；
4. 但是在mutation中执行异步操作，就无法追踪到数据的变化；
5. 所以Vuex的重要原则中要求 mutation必须是同步函数



### （13）actions的基本使用

actions类似于mutation，不同之处在于：

- actions<font color=red>**提交的是mutation**</font>，**不是直接更改状态**；
- actions**可以包含任意<font color=red>异步操作</font>**；

```js
mutations:{
  increment(state){
    state.counter++
  }
},
  
actions:{
  increment(context){
    context.commit("increment")
  }
}
```

actions中有个**非常重要的参数context**

1、context是一个和store实例均有相同方法和属性的context对象；

2、所以可以从其中获取到commit方法来提交一个mutation，或者通过 context.state和context.getters来获取 state和getters;



### （14）actions的分发操作

- 分发使用的是<font color=red>store上的**dispatch函数**</font>

```js
add(){
  this.$store.dispatch("increment")
}
```

- 同时可以携带参数

```js
add(){
  this.$store.dispatch("increment",{count:100})
}
```

- 也可以以对象的形式分发

```js
add(){
  this.$store.dispatch({
    type:"increment",
    count:100
  })
}
```



### （15）actions的辅助函数

action也有对应的辅助函数，分为<font color=orange>对象类型写法</font>和<font color=red>数组类型写法</font>

- optionAPI写法

![image-20220119200906568](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192009761.png)

- setup写法

![image-20220119200927337](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192009624.png)



### （16）actions的异步操作

Action 通常是异步的，如何知道action什么时候结束的方法：

**可以通过让action返回一个Promise函数，在Promise的then中处理完成后的操作**

```js
//store的index文件
actions:{
  increment(context){
    //返回一个promise
    return new Promise((resolve,reject) => {
      //这里执行异步操作
      axios.get().then(res => {
        context.commit("increment")
        resolve("异步完成")
      }).catch(err => {
        reject(err)
      })
    })
  }
}

//在组件中使用
const store = useStore();
const increment = () => {
  store.dispatch("increment")
  	.then(res => {
    	//这里获取的是resolve里的值
			console.log(res)    
  	}).catch(res => {
    	//这里获取的是reject里的值
    	console.log(res)
  	})
}
```



### （17）module的基本使用

​	由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得非常复杂时，store 对象就有可

能变得相当臃肿。

​	为了解决臃肿问题，Vuex允许将store分割成<font color=red>**模块**</font>（module）。

​	<font color=blue>每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块</font>。

![image-20220119202653923](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192026956.png)



### （18）module的命名空间

- 对于模块内部的 mutation 和 getter，接收的第一个参数是<font color=red>**模块的局部状态对象**</font>
- 默认情况下，模块内部的action和mutation仍然是注册在**全局的命名空间**中的
  - 这样使得多个模块能够对同一个 action 或 mutation 作出响应
  - Getter 同样也默认注册在全局命名空间
- 如果<font color=red>希望模块具有更高的封装度和复用性</font>，可以<font color=blue>添加`namespaced: true`的方式使其成为**带命名空间的模块**</font>
  - 当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名

#### module的例子：

##### 组件分发home模块的状态和方法：

![image-20220119203640930](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192036716.png)

##### Vuex的store文件中module文件下home模块的写法：

![image-20220119203740401](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192037916.png)

##### Vuex的store文件根模块index的写法：

![image-20220119203757183](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192037330.png)



### （19）module修改或派发根组件

需要多传一个参数`{root:true}`

```js
changeNameAction({commit,dispatch,state,rootState,getters,rootGetters}){
  commit("changeName");
  commit("changeRootName",null,{root:true});
  dispatch("changePootNameAction",null,{root:true});
}
```



### （20）module的辅助函数的三种用法

#### 1、第一种:通过完整模块空间名称查找

![image-20220119205213422](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192052247.png)

#### 2、第二种：第一个参数传入模块空间名称，后面写上要使用的属性

![image-20220119205433133](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192054718.png)

#### 3、第三种：通过 createNamespacedHelpers 生成一个模块的辅助函数

![image-20220119205623569](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192056929.png)



### （21）在setup中使用module

![image-20220119210022580](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201192100512.png)

**上面的例子中引入了useState和useGetter**

useState的hooks文件

```js
import { mapState, createNamespacedHelpers } from 'vuex'
import { useMapper } from './useMapper'

//mapper映射
export function useState(moduleName, mapper) {
  console.log("6",moduleName);
  console.log("7",mapper);
  let mapperFn = mapState
  if (typeof moduleName === 'string' && moduleName.length > 0) {
    mapperFn = createNamespacedHelpers(moduleName).mapState
  } else {
    mapper = moduleName
  }

  return useMapper(mapper, mapperFn)
}
```

useGetter的hooks文件

```js
import { mapGetters, createNamespacedHelpers } from 'vuex'
import { useMapper } from './useMapper'

export function useGetters(moduleName, mapper) {
  let mapperFn = mapGetters
  if (typeof moduleName === 'string' && moduleName.length > 0) {
    mapperFn = createNamespacedHelpers(moduleName).mapGetters
  } else {
    mapper = moduleName
  }

  return useMapper(mapper, mapperFn)
}
```

其中，公共部分抽取useMapper

```js
import { computed } from 'vue'
import { useStore } from 'vuex'

export function useMapper(mapper, mapFn) {
  // 拿到store独享
  const store = useStore()

  // 获取到对应的对象的functions: {name: function, age: function}
  const storeStateFns = mapFn(mapper)

  // 对数据进行转换
  const storeState = {}
  Object.keys(storeStateFns).forEach(fnKey => {
    const fn = storeStateFns[fnKey].bind({$store: store})
    storeState[fnKey] = computed(fn)
  })

  return storeState
}
```



### （22）nextTick

将回调推迟到下一个 DOM 更新周期之后执行。在更改了一些数据以等待 DOM 更新后立即使用它。

<font color=red>nextTick内部实际上是将包裹的函数**放到微任务队列的最后执行**</font>（<font color=blue>watch函数、组件的更新、生命周期回调等等都是一个个微任务</font>）





# 五、Pinia状态管理

## 1、什么是Pinai

Pinia本质上依然是一个<font color=deepred>**状态管理的库**</font>，用于**跨组件、页面进行状态共享**(这点和Vuex、Redux一样)



## 2、对比Vuex

1. 提供了一个更简单的 API，具有更少的仪式，提供了 Composition-API 风格的 API；
2. 与 TypeScript 一起使用时具有可靠的类型推断支持；
3. mutations不再存在；
4. 不再有modules的嵌套结构；
5. 不再有命名空间的概念，不需要记住复杂关系；

![image-20221015155013144](https://raw.githubusercontent.com/Rainchen0504/picture/master/202210151550685.png)



## 3、使用方法

### （1）安装

```shell
npm install pinia;
yarn add pinia;
```



### （2）导入

创建一个pinia并传递给应用程序

```js
import { createPinia } from "pinia";
const pinia = createPinia();
export default pinia;
```



### （3）挂载

```js
import pinia from "./store";
createApp(App).use(pinia).mount("#app");
```



## 4、Store

一个Store是一个实体，会持有为绑定到你组件树的状态和业务逻辑，保存了全局的状态。

像始终存在，并且每个人都可以读取和写入的组件

可以定义任意数量的store管理状态









# 四、Vue3动画

Vue提供了一些内置组件和对应的API来完成动画。

## 1、Vue的transition动画<font color=red>（渐变）</font>

Vue 提供了 transition 的封装组件，可在条件渲染、动态组件、组件根节点中给任何元素添加进入/离开过渡。

```vue
<transition name = "fade">
	<h2 v-if="show">hello world</h2>
</transition>

<style scoped>
  .fade-enter-from,
  .fade-leave-to{
    opacity : 0;
  }
  
  .fade-enter-to,
  .fade-leave-from{
    opacity: 1;
  }
  
  .fade-enter-active,
  .fade-leave-active{
    transition: opacity 1s ease;
  }
</style>
```





## 2、transition组件的原理

当插入或删除包含在transition组件中的元素时，Vue会做如下操作：

- 自动嗅探<font color=red>目标元素是否应用了CSS过渡或者动画</font>，如果有<font color=red>在恰当的时机添加/删除CSS类名</font>；
- 如果transition组件提供了<font color=red>Javascript钩子函数</font>，这些钩子函数将在恰当的时机被调用；
- 如果<font color=red>没有找到Javascript钩子并且没有检测到CSS过渡/动画，DOM插入、删除操作会立即执行</font>；





## 3、过渡动画class

注意⚠️：**<font color=red>类名开头的v用transition上的name替代</font>**。

- v-enter-from：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除；
- v-enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义<font color=red>进入过渡的过程时间，延迟和曲线函数</font>。
- v-enter-to：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡/ 动画完成之后移除。
- v-leave-from：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
- v-leave-active：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义<font color=red>离开过渡的过程时间，延迟和曲线函数</font>。
- v-leave-to：离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被删除)，在过渡/ 动画完成之后移除。





## 4、class添加的时机和命名规则

![image-20211210193244205](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112101932306.png)

如果使用的是一个没有name的transition，那么所有的class是以` v- `作为默认前缀的；

如果添加了一个name属性，那么所有的class会以name-开头。





## 5、Vue的animation动画<font color=red>（动画）</font>

前面通过transition来实现动画效果的，另外也可以通过animation来实现。

```vue
<button @click="show = !show">切换</button>
<transition name = "bounce">
	<h2 v-if="show">hello world</h2>
</transition>

<style scoped>
  .bounce-enter-active{
    animation: animationName 0.5s;
  }
  .bounce-laeve-active{
    animation: animationName 0.5 reverse;
  }
  @keyframes animationName {
    0%{
      transform: scale(0)
    }
    50%{
      transform: scale(1.25)
    }
    100%{
      transform: scale(1)
    }
  }
</style>
```





## 6、同时设置过渡（渐变）和动画

Vue内部内部是**在监听 transitionend 或 animationend**，到底使用哪一个取决于元素应用的CSS规则。

- 如果只使用一个，那么<font color=red>Vue能自动识别类型并设置监听</font>；
- 如果同时使用了过渡和动画，可以<font color=red>设置 type 属性为 animation 或者 transition </font>来明确的告知Vue监听的类型。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121231565.png" alt="image-20211212123137057" style="zoom:50%;" />





## 7、显示的指定动画时间

可以**指定过渡的时间**，通过 **duration 属性**：

- <font color=red>number类型</font>：同时设置进入和离开的过渡时间；
- <font color=red>object类型</font>：分别设置进入和离开的过渡时间；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121235219.png" alt="image-20211212123540956" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121235705.png" alt="image-20211212123555057" style="zoom:50%;" />





## 8、过渡模式的mode

​	默认情况下<font color=red>进入和离开动画</font>是同时发生的，如果**不希望同时执行进入和离开动画**，那么需要设置transition的过渡模式：

- <font color=red>in-out</font>：新元素先进入过渡，完成之后当前元素过渡离开；
- <font color=red>out-in</font>：当前元素先进入过渡，完成之后新元素过渡进入；





## 9、appear初次渲染

 默认情况下，**首次渲染的时候是没有动画的**，如果我们**希望给他添加上去动画，那么就可以增加另外一个属性 appear**。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121244450.png" alt="image-20211212124435064" style="zoom:50%;" />





## 10、认识animate.css（**第三方库的动画库**）

### （1）**什么是animate.css**

**Animate.css**是一个已经准备好的、跨平台的动画库为我们的web项目，对于强调、主页、滑动、注意力引导 非常有用;



### （2）**如何使用Animate库**

第一步:需要安装animate.css库;

第二步:导入animate.css库的样式;

第三步:使用animation动画或者animate提供的类;



### （3）自定义过渡class

**我们可以通过以下 attribute 来自定义过渡类名:** 

​	enter-from-class

​	enter-active-class

​	enter-to-class

​	leave-from-class

​	leave-active-class

​	leave-to-class

他们的<font color=red>优先级高于普通的类名</font>，这对于 **Vue 的过渡系统和其他第三方 CSS 动画库**，如 Animate.css. 结合使用十分有用。





## 11、animate.css库的使用

- 安装animate.css

```
npm isntall animate.css
```

- 在main.js中导入animate.css

```js
import "animate.css"
```

- 用法有两种

1. 直接使用<font color=red>animate库中定义的 keyframes 动画</font>；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121304317.png" alt="image-20211212130437755" style="zoom:67%;" />

1. 直接使用<font color=red>animate库提供给我们的类</font>；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121305850.png" alt="image-20211212130457491" style="zoom:67%;" />





## 12、认识gsap库

​	某些情况下希望通过**JavaScript来实现一些动画的效果**，这个时候我们可以选择使用**gsap库**来完成。

### （1）什么是gsap

可以<font color=red>通过JavaScript为CSS属性、SVG、Canvas等设置动画</font>，并且是浏览器兼容的；



### （2）如何使用

1. 第一步:需要安装gsap库;
2. 第二步:导入gsap库;
3. 第三步:使用对应的api即可;



### （3）安装库

```
npm install gsap
```





## 13、transition提供的JS钩子

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121322643.png" alt="image-20211212132239445" style="zoom:50%;" />

这些钩子可以帮助我们监听动画执行到什么阶段了。

⚠️注意：

- 当我们使用JavaScript来执行过渡动画时，需要**进行 done 回调**，否则它们将会被同步调用，过渡会立即完成；
- 添加 **:css="false"**，也会让 Vue 会**跳过 CSS 的检测**，除了性能略高之外，这可以避免过渡过程中 CSS 规则的影响。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121324601.png" alt="image-20211212132358195" style="zoom:50%;" />





## 14、gsap库的使用

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121328095.png" alt="image-20211212132812127" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121328630.png" alt="image-20211212132826605" style="zoom:67%;" />





## 15、jsap实现数字变化

项目中常见的**数字快速变化的动画效果**，使用gsap来实现：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121332314.png" alt="image-20211212133255077" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112121333648.png" alt="image-20211212133313966" style="zoom:67%;" />







# 五、Vue source源码学习

![image-20211230110742607](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112301137210.png)





## <font color=red>**Vue.js模板转成视图的过程**</font>

​	**Vue.js通过编译(compile)将模板转换成渲染函数(render)，render函数调用返回虚拟节点构建成虚拟节点树，树结构就是一个虚拟DOM，同时根据虚拟DOM同步生成真实DOM。**

​	**状态改变时，在虚拟DOM中通过patch函数也叫做patching算法(主要是diff算法)对比新旧虚拟节点的差异，最后以最优最小改动应用到真实DOM上，更新视图。**

​	**其中，响应式系统贯穿编译和渲染过程，作用是建立了数据和视图的绑定关系，从而实现数据驱动视图。**





## 1、虚拟DOM（VNode）

### （1）虚拟DOM的优势

- 首先可以对真实的元素节点进行抽象，抽象成VNode(虚拟节点)，这样方便后续对其进行各种操作：
  - 直接操作DOM元素是有很多限制的，比如diff、clone等，但是使用javascript编程语言来操作就变得简单；
  - 可以使用JavaScript来表达非常多的逻辑，而对于DOM本身来说是非常不方便的；
  - 操作真实DOM会导致浏览器发生回流，造成浏览器重新渲染，影响性能。
- 其次方便实现跨平台，包括将VNode节点渲染成任意想要的节点：
  - 如渲染在canvas、WebGL、SSR、Native(iOS、Android)上；
  - Vue允许开发属于自己的渲染器（renderer），再其他平台上渲染；



### （2）虚拟DOM渲染过程

![image-20211229172041156](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112291720269.png)





## 2、三大核心系统

Vue源码包含三大核心：

- Compiler模块：编译模块系统；
- Runtime模块：也可以称之为Renderer模块，真正渲染的模块；
- Reactivity模块：响应式模块；

![image-20211230114226096](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112301142164.png)

源码中的文件夹分布

![image-20211230114553901](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112301145199.png)



#### **⚠️注意：compile解析**

vue中compile是一个非常复杂的内容，**compile的<font color=red>主要作用是解析模板</font>，生成渲染模板的render，<font color=red>render的作用又是生成VNode</font>**，compile主要分为三个板块：

1、**parse**接受template原始模板，按着模板的节点和数据生成对应的抽象语法树（ast）；

2、**optimize** 遍历ast的每一个节点，标记静态节点，这样就知道哪部分不会变化，于是在页面需要更新时，减少去对比这部分DOM，提升性能；

3、**generate** 把前两步生成完善的ast，组成render字符串，然后将render字符串通过`new Function`的方式转换成渲染函数





## 3、三大系统协同工作

![image-20211230115145982](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112301151672.png)





## 4、实现一个mini-vue

该Vue包括三个模块：渲染系统模块、可响应式系统模块、应用程序入口模块；



### （1）渲染系统实现

- 功能一：h函数，用于返回一个VNode对象；
- 功能二：mount函数，用于将VNode挂载到DOM上；
- 功能三：patch函数，用于对两个VNode进行对比，决定如何处理新的VNode；

#### 1、h函数-生成VNode

h函数的实现：直接返回一个VNode对象即可

（h函数返回一个VNode，而<font color=red>**VNode本质上就是一个javacript对象**</font>）

![image-20211230182144393](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112301821529.png)



#### 2、mount函数-挂载VNode

mount函数的实现：（将虚拟DOM转换成真实DOM的过程）

- 第一步：根据tag，创建HTML元素，并且存储 到vnode的el中；
- 第二步：处理props属性。如果on开头那么监听事件；普通属性直接通过setAttribute添加即可；
- 第三步：处理子节点。如果是字符串节点，那么直接设置textContent；如果是数组节点，那么遍历调用 mount 函数；

![image-20211230182310243](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112301823423.png)



#### 3、<font color=red>patch函数-对比两个VNode（diff算法）</font>

patch函数的实现，分为两种情况：

- n1和n2是不同类型的节点：·
  - 找到n1和n2的el的父节点，删除原来的n1节点的el；
  - 挂载n2节点到n1节点的el父节点上；
- n1和n2节点是相同的节点：
  - 处理props的情况
    - 先将新节点的props全部挂载到el上；
    - 判断旧节点的props是否不需要在新节点上，如果不需要，那么删除对应的属性；
  - 处理children的情况
    - 如果新节点是一个字符串类型，那么直接调用el.textContent = newChildren；
    - 如果新节点不是一个字符串类型：
      - 旧节点是一个字符串类型
        - 将el的textContent设置为空字符串；
        - 旧节点是一个字符串类型，那么直接遍历新节点，挂载到el上；
      - 旧节点也是一个数组类型
        - 取出数组的最小长度；
        - 遍历所有的节点，新节点和旧节点进行path操作；
        - 如果新节点的length更长，那么剩余的新节点进行挂载操作；
        - 如果旧节点的length更长，那么剩余的旧节点进行卸载操作；

![image-20220102155138967](https://raw.githubusercontent.com/Rainchen0504/picture/master/202201021551614.png)



### <font color=red>（2）响应式系统</font>

#### 1、响应式思想

​	可以理解为每个函数和该该函数所用的变量都是有对应的<font color=red>依赖关系</font>，当**变量被改变时**，对应函数就会<font color=red>自动重新执行</font>，更新改变后的值。

```js
const info = {counter:100};	//定义变量
function changeCounter(){
  console.log(info.counter * 2);
}
changeCounter();	//首次调用函数
info.counter++;	//当counter改变时，changeCounter函数中对info.counter有依赖，所以希望能自动调用函数；
```

如果有多个函数都对一个变量有依赖，那么多个函数都会同时执行，对于这种情况，封装一个函数，统一执行以来函数，就是响应式函数。



##### （1）手动实现

```js
//创建一个类函数，标准写法constructor(){} -> methods1(){} -> methods2(){}
class Dep {
  	constructor() {
        this.subscribers = new Set();//保证收集的依赖不是重复的
    }
    
    //收集副作用函数（就是当变量改变时，需要执行的函数）
    addEffect(effect) {
        this.subscribers.add(effect);
    }
    
    //变量改变，通知执行副作用函数
    notify() {
        this.subscribers.forEach(effect => {
            effect();
        })
    }
}
```

测试效果

```js
const info = { counter: 100 };
const dep = new Dep();	//实例化依赖函数
function doubleCounter() {
    console.log(info.counter * 2);
}

dep.addEffect(doubleCounter);	//收集依赖
info.counter++;	//变量改变
dep.notify();	//执行副作用函数
```



##### （2）手动收集改自动收集

```js
class Dep {
  	constructor() {
        this.subscribers = new Set();
    } 

  	//自动收集依赖函数
  	depend(){
      if(activeEffect){//判断是否存在副作用函数
        this.subscribers.add(activeEffect);
      }
    }
    
    //变量改变，通知执行副作用函数
    notify() {
        this.subscribers.forEach(effect => {
            effect();
        })
    }
}

//封装一个watchEffect函数
let activeEffect = null;//声明变量activeEffect
function watchEffect(effect) {
    activeEffect = effect;	//把副作用函数赋值给activeEffect
  	dep.depend(activeEffect);	//收集副作用函数
    effect();	//首次执行
    activeEffect = null;	//重新置空activeEffect
}
```

测试效果

```js
const info = { counter: 100 };
const dep = new Dep();	//实例化依赖函数

watchEffect(function () {
    console.log(info.counter * 2);
})

info.counter++;	//变量改变
dep.notify();
```



#### 2、Vue2实现

**Vue2中响应式的实现主要是<font color=red>基于数据劫持实现的（Object.defineProperty的get和set方法）</font>**

```js
const dep = new Dep()
const info = { count: 100, name: 'chenge'}
const bar = { age: 24 }

watchEffect(function() {   //watch1依赖info.count和info.name
    console.log(info.count, info.name)
})
watchEffect(function() {   //watch2依赖bar.age
    console.log(bar.age)
})
//watchEffect收集依赖时
```

在depend函数中，执行了收集不同的副作用添加在subscribers中，只要依赖一发生改变，所有副作用函数都会执行。但是这是浪费性能的，应该实现的是变量改变时，只执行与该变量有依赖关系的副作用函数。

因此，需要创建多个dep实例对象，使用subscribers保存不同变量的副作用。

##### （1）结构分析

​	针对于上面，存在两个对象 `info` （有两个属性），`bar`（有一个属性），那么就应该实例化三个dep对象，使用实例化中的subscribers来分别保存他们对应的副作用。

```js
//对于info对象，需要创建两个dep对象 dep1 和 dep2
info.count:  dep1.subscribers = [watch1]
info.name:   dep2.subsrcibers = [watch1, wathc2]

//对于bar对象，需要创建一个dep对象 dep3
bar.age:      dep3.subsrcibers = [watch3]
```

当 info和bar的属性改变，就执行各自的subscribers收集的副作用，然后执行`notify`函数即可



**注意⚠️：**

```markdown
**理解： `WeakMap` 和 `Map` 的区别**

**相同点：**
他们都是键值对的存在（key: value）

**不同点：**
Map 的 key 值是： `字符串`， value是任意类型
WeakMap的 key 值是： `对象` value是任意类型
```



**用info对象举例设计一个伪代码数据结构**

```js
const targetMap = new WeakMap()	//创建一个tragetMap，它是weakmap类型{}:value,key是一个对象

const infoMap = new Map(info)	//创建一个info的map对象
targetMap[info] = infoMap	//info对象的targetMap的键key为info对象，值是infoMap对象；

infoMap['count'] = dep1.subscribers
infoMap['name'] = dep2.subscribers
```

这样就可以做到数据改变时执行对应的dep的subscribers。



**真实代码实现**

```js
//创建一个WeakMap, 来保存info 和 bar 对象  （下面主要以info对象作为实例）
const targetWeakMap = new WeakMap()

//这个函数在对每个函数的属性遍历中触发，以info中的name属性为例，getDep(info, 'name')  拿到info的属性值为name的dep
function getDep(target, key) {
    //拿到WeakMap中key为info对象的value
    let depsMap = targetWeakMap.get(target)
    //初始化的时候是没有的，所以new Map()作为value，重新赋值
    if(!depsMap) {   
        depsMap = new Map()   
        targetWeakMap.set(target, depsMap)
    }
	
    //根据info对象，拿到value值， 为Map， 根据key，就可以拿到对应的dep
    let dep = depsMap.get(key)
    //处理初始化，如果没有，创建一个dep实例对象
    if(!dep) {
        dep = new Dep()
        depsMap.set(key, dep)
    }
	
    //返回拿到的dep
    return dep
}
```

##### （2）Vue2的响应式代码

```js
const info = reactive({count: 100, name: 'chenge'});
```

目标是调用**reactive**函数，传递一个对象，返回一个响应式对象

```js
//raw: 未加工的数据
function reactive(raw) {
    //遍历拿到所有的key值
    Object.keys(raw).forEach(item => {
        const dep = getDep(raw, item)
        let value = raw[item]
        Object.defineProperty(raw, item, {
            //当拿取info对象中的属性值的时候，就是调用get方法，那么就在这里添加依赖（所谓的数据劫持）
            get() {
                dep.depend()
                return value
            },
            set(newValue) { //当设置值的时候，就执行notify，执行所有的副作用
                if(value !== newValue) {
                    value = newValue
                    dep.notify()
                }
            }
        })
    })
    return raw
}
```

##### （3）Vue实现响应式函数的全流程

```js
//步骤一：
class Dep {
    constructor() {
        this.subscribers = new Set()
    }
    depend() {
        if(activeEffect) {
            this.subscribers.add(activeEffect)
        }   
    }
    notify() {
        this.subscribers.forEach(effect => {
            effect()
        })
    }
}

//步骤二：
let activeEffect = null
function watchEffect(effect) {
    activeEffect = effect
    effect()
    activeEffect =null
}

//步骤三：
const targetWeakMap = new WeakMap()
function getDep(target, key) {
    //根据target对象， 拿到dep的Map
    let depsMap = targetWeakMap.get(target)
    if(!depsMap) {
        depsMap = new Map()
        targetWeakMap.set(target, depsMap)
    }

    //根据获取到的depsMap，通过key值拿取各自的dep
    let dep = depsMap.get(key)
    if(!dep) {
        dep = new Dep()
        depsMap.set(key, dep)
    }

    return dep
}

//vue2对raw进行数据劫持
function reactive(raw) {
    Object.keys(raw).forEach(item => {
      	//拿到每一个对象的key
        const dep = getDep(raw, item)
        let value = raw[item]
        Object.defineProperty(raw, item, {
            get() {
                dep.depend()
                return value
            },
            set(newValue) {
                if(value !== newValue) {
                    value = newValue
                    dep.notify()
                }
            }
        })
    })
    return raw
}

//测试代码
const info = reactive({ count: 100, name: 'chenge'})
const bar = reactive({age: 24})

watchEffect(function() {   //watch1
    console.log('watch1: ', info.count, info.name)
})
watchEffect(function() {   //watch2
    console.log('watch2: ', info.name)
})
watchEffect(function() {   //watch3
    console.log('watch3: ', bar.age)
})
//均会先触发打印一次，然后修改后再打印一次
info.count++;
bar.age = 25;
```

- 第一步：定义`Dep`类，里面实现两个方法：`depend(收集副作用函数)`，`notify(数据改变，重新执行副作用函数)`
- 第二步：定义`watchEffect`函数，第一次执行，收集依赖
- 第三步：定义一个`reactive`函数，把数据变成响应式数据
- 第四步：测试代码验证



#### 3、Vue3实现

**Vue3中响应式的实现主要是<font color=red>基于Proxy实现的</font>**

```js
//Vue2
function reactive(raw){
  	reutrn raw
}
//Vue3
function reactive(raw){
  	return new Proxy(raw,{})
}
```

- Object.definedProperty 是劫持对象的属性时，如果新增元素
  - vue2需要再次执行defineProperty , 比如Vue.$set就是重新执行；
  - 而 Proxy 劫持的是整个对象，不需要做特殊处理；
- 修改对象的不同
  - 使用 defineProperty 时，我们修改原来的 obj 对象就可以触发拦截；
  - 使用 proxy，就必须修改代理对象，即 Proxy 的实例才可以触发拦截；
- Proxy 能观察的类型比 defineProperty 更丰富
  - has:in操作符的捕获器；
  - deleteProperty:delete 操作符的捕捉器；
  - 其他操作；
- Proxy 作为新标准将受到浏览器厂商重点持续的性能优化；
- 缺点:Proxy 不兼容IE，也没有 polyfill, defineProperty 能支持到IE9

```js
function reactive(raw) {
  	//会代理传进来的raw对象，执行后面对象的get和set
    return new Proxy(raw, {
        get(target, key) {
          	//target对象就是外面的raw对象
            const dep = getDep(target, key)
            dep.depend()
            return target[key]
        },
        set(target, key, newValue) {
            const dep = getDep(target, key)
            target[key] = newValue
            dep.notify(
        }
    })
}
```





### <font color=red>（3）框架外层API设计（入口文件）（搁置）</font>



## <font color=red>4、源码阅读（搁置）</font>













# 项目内容

一个组件导出的对象是该组件的描述，相当于一个类

而另一个组件使用导出的组件时，是根据导出的对象创建出来的一个真正的组件实例





vue3中子组件props在设置默认值default时，若值类型为Object或者Array时，不能直接defult而是要使用函数通过return将值返回出来才可以。





默认情况下，组件上的 `v-model` 使用 `modelValue` 作为prop 和 `update:modelValue` 作为emit



