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

3. `Watcher`订阅者是`Observe`和`Compile`之间通信的桥梁，`Watcher`主要任务是：

   ① 在自身实例化时向依赖收集器Dep中添加自身；

   ② 自身必须具有`update()`方法；

   ③ 当依赖属性变动发起`Dep.notify`通知时，能调用`update`方法，触发`Compile`中绑定的回调更新视图；

   ④ MVVM作为数据绑定的入口，整合`Observe、Compile、Watcher`三者，通过`Observe`监听自己的model数据变化，通过Compile解析编译模板指令，最终利用Watcher搭建Observer和Compile之间的通信桥梁，达到数据变化到视图更新、视图交互变化到数据变更的双向绑定效果；

   ![image-20230731165606320](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307311656909.png)



Vue响应式原理：

1. 数据劫持

   Vue使用`Object.defineProperty`方法来劫持数据的对象属性。在Vue实例化过程中，会遍历数据对象的每个属性，并为其添加getter和setter方法。当访问或修改属性时，Vue就能捕捉到，并执行相应的操作。

2. 依赖追踪

   在属性劫持的过程中，每个属性都会关联一个依赖收集器。当属性被访问时，Vue会将订阅者Watcher对象添加到对应属性的依赖收集器中；当属性发生修改时，对应的依赖收集器会通知所有的订阅者依赖的属性发生了变化。

3. 模板解析

   模板解析器对每个节点元素进行扫描和解析，将相关的指令对应初始化成一个订阅者Watcher，并替换模板数据或者绑定相应的函数，这个时候当订阅者Watcher收到相应属性的变化，会执行相应的update更新函数，从而更新视图。

4. 响应式组件更新

   当数据发生变化时，Vue会进行异步的批量更新操作，以提高性能。Vue使用了异队列和nextTick机制来实现这一点。当数据变化时，会将Watcher对象添加到异步队列中，然后通过nextTick在下一个事件循环中执行更新操作，从而避免频繁更新DOM。



## 3、Object.defineProperty

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
2. 无法监听新增属性、删除属性（需要使用`Vue.set`和`Vue.delete`）;
3. 无法监听原生数组，需要重写方法；



## 4、MVVM、MVC和MVP的区别

### （1）MVC

M：model数据模型，V：view视图模型，C：controller控制器

​	MVC 通过分离 Model、View 和 Controller 的方式来组织代码结构。其中 View 负责页面的显示逻辑，Model 负责存储页面的业务数据，以及对相应数据的操作。

​	View和Model应用了观察者模式，当Model层发生改变时候会通知有关View层更新页面。Controller层是View和Model层的纽带，主要负责用户与应用的响应操作。当用户与View层页面产生交互的时候，Controller中的事件触发器就开始工作，通过调用Model层，完成对Model的修改，然后 Model 层再去通知View视图更新。

![image-20230806204841330](https://raw.githubusercontent.com/Rainchen0504/picture/master/202308062048247.png)



### （2）MVVM 

M：model数据模型，V：view视图模型，ViewModel：负责监听数据模型model中数据的变化并且控制view视图的更新

​	Model和View没有直接关联，而是通过ViewModel进行联系，Model和ViewModel之间有着双向绑定的关系。因此当Model中的数据改变时会触发View层的更新，View中由于用户交互操作而改变的数据会同步在Model中同步。

​	这种模式实现了Model和View的数据自动同步，因此开发者只需要专注于数据的维护操作即可，不需要手动操作DOM。

![image-20230807084744037](https://raw.githubusercontent.com/Rainchen0504/picture/master/202308070847420.png)



## 5、MVVM优缺点

### （1）优点

1. 分离视图和模型，将滴代码的耦合程度，提高代码复用性；
2. 提高代码可测试性，让开发者可以更好的测试代码；
3. 自动更新DOM，利用双向绑定，数据更新后视图自动更新；



### （2）缺点

1. BUG很难被调试，当出现问题时，无法确定是view页面还是model的问题，并且由于数据绑定的关系，bug会快速传递到其他位置，不方便快速定位错误；
2. 大模型中数据量较大，当长期持有时，不释放内存就造成内存占用过高问题；
3. 对于大型的图形应用程序，视图状态较多，ViewModel构建和维护成本较高；



## 6、Vue常用指令和作用

- v-on：给标签绑定函数，语法糖为@；
- v-bind：动态绑定，语法糖为：
- v-slot：组件插槽，语法糖为#
- v-for：循环数组
- v-if：显示隐藏
- v-show：显示隐藏
- v-text：解析文本
- v-html：解析html



## 7、Vue常用修饰符

- v-on

  - `.stop` - 调用 `event.stopPropagation()`。 阻止默认事件；
  - `.prevent` - 调用 `event.preventDefault()`。阻止默认行为；
  - `.native` - 监听组件根元素的原生事件；

- v-bind

  - `.prop` - 作为一个 DOM property 绑定而不是作为 attribute 绑定。

    `.camel` - (2.1.0+) 将 kebab-case attribute 名转换为 camelCase。(从 2.1.0 开始支持)

    `.sync` (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 `v-on` 侦听器。

- v-model

  - `.lazy`- 取代 `input` 监听 `change` 事件
  - `.number` - 输入字符串转为有效的数字
  - `.trim` - 输入首尾空格过滤



## 8、Vue内置组件

### （1）component

渲染一个元组件，根据is的值，决定哪个组件被渲染。

### （2）transition

在Vue插入、更新或者移除DOM时，提供多种不同方式的过渡、动画效果。

### （3）transition-group

给列表统一设置过渡动画。

### （4）keep-alive

主要用于保留组件状态或避免组件重新渲染。可配置属性：

`include`：属性用于指定哪些组件会被缓存；

`exclude`：属性用于指定哪些组件不会被缓存；

`max`：属性用于设置最大缓存个数；

### （5）slot

`name`：字符串，用于命名插槽；



## 9、v-if、v-show原理和区别

### （1）v-if

Vue编译器会解析模板，并生成抽象语法树，遇到v-if指令时，会生成相应的条件节点。

- 如果条件为真，则条件节点会被渲染为真实的DOM元素，并插入到父节点中。
- 如果条件为假，则不会被渲染为真实DOM，相应的节点会被销毁。
- 如果条件表达式值发生变化，响应式观察者会检测到变化，重新计算虚拟DOM树。



### （2）v-show

v-show指令的元素会生成虚拟节点，且渲染的时候也会渲染成真实节点，只是通过条件语句控制节点属性中的display是否为none。



### （3）区别

- 相同点
  - 都是用来控制显示隐藏的，true时候显示，false时候隐藏；
- 不同点
  - 原理不同：
    - `v-show`:一定会渲染，只是修改display属性
    - `v-if`:根据条件渲染
  - 应用场景不同：
    - 频繁切换用`v-show`
    - 不频繁切换用`v-if`
  - 原理不同
    - `v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建，操作的实际上是dom元素的创建或销毁
    - `v-show` 元素总是会被渲染，并且只是简单地基于 CSS 进行切换 它操作的是display:none/block属性
  - 性能消耗不同
    - `v-if`：具有更高的切换开销，如果在运行时条件很少改变，则使用 `v-if` 较好
    - `v-show`：有更高的初始渲染开销，如果需要非常频繁地切换，则使用 `v-show` 较好



## 10、v-for和v-if优先级

在Vue2中，<font color=deepred>**`v-for`优先级高于`v-if`**</font>

在Vue3中，<font color=pink>**`v-if`优先级高于`v-for`**</font>



## 11、v-for必须绑定key

### （1）必须绑定key的原因

​	key属性是DOM元素的唯一标识。当数组发生增删时，默认需要把发生改动的项目全部进行重绘，会造成性能的极大消耗。当添加唯一标识之后， 一旦发生增删操作之后，重绘之前会检测新绘制的元素和已有的元素是否存在相同的 key ， 相同则复用，减少资源性能消耗。

1. key的作用主要是为了高效的更新虛拟DOM，其原理是vue在patch过程中通过key可以精准判断两个节点是否是同一个，从而避免频繁更新不同元素，使得整个patch过程更加高效，减少DOM操作量，提高性能。
2. vue中在使用相同标签名元素的过渡切换时，也会使用到key属性，其目的也是为了让vue可以区分它们，否则vue只会替换其内部属性而不会触发过渡效果。
3. 若不设置key会以数据的index来匹配，Vue 会最大限度减少动态元素并且尽可能的尝试**就地修改/复用**相同类型元素（就地更新）。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序，而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。



### （2）不建议使用index索引的原因

1. 节点顺序发生变化时，会导致不必要的重新渲染。当列表的数据源发生变化时，新旧数据的顺序可能会发生变化。如果两次渲染的节点顺序不同，即使实际上这些节点的内容是一样的，也会导致整个列表的重新渲染。这是因为 Vue 无法识别出新旧节点的对应关系，只能全部重新渲染。
2. 删除和插入节点时，会导致不必要的重新渲染。当从列表中删除或插入一个节点时，所有后续节点的索引都会发生变化。如果我们以循环的索引作为 key，这些节点的 key 值也会随之发生变化，导致它们被识别为新创建的节点，而不是仅更新相应的内容。这将导致整个列表的重新渲染，即使实际上只有一个节点发生了变化。
3. 如果你用 index 作为 key，那么在删除第2项的时候，index 就会从 1 2 3 变成 1 2（而不是 1 3），那么 Vue 依然会认为删除的是第三项，产生bug。



## 12、v-model实现原理

### （1）作用在表单元素

```vue
<input v-model="message" />
```

上面的元素等价于：

```vue
<input :value="message" @input="message=$event.target.value" />
```



### （2）单个作用在组件上

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
<script>
  export default{
    props:["modelValue"],
    emits:["update:modelValue"],
    methods:{
      inputChange(event){
        this.$emit("update:modelValue",event.target.value)
      }
    }
  }
</script>
```



### （3）多个绑定在组件上

```vue
<!--父组件-->
<my-input v-model="message" v-model:title="title"></my-input>
```

```vue
<!--子组件-->
<template>
	<div>
        <input :value="modelValue" @imput="inputChange"/>
        <input :value="title" @imput="inputChange2"/>
    </div>
</template>
<script>
	export default {
        props:["modelValue", "title"],
        emits:["update:modelValue", "update:title"],
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



## 13、v-model和.sync

### （1）作用

.sync和v-model的作用都是实现父子组件之间的双向绑定



### （2）原理

v-model的原理

```html
<com1 v-model="val"></com1>
<!--等价于-->
<com1 :value="val" @input="val=$event.target.value"></com1>
```

.sync的原理

```html
<com1 :a.sync="num" .b.sync="num2"></com1> 
<!--等价于-->
<com1 
  :a="num" @update:a="val=>num=val"
  :b="num2" @update:b="val=>num2=val"></com1> 
```



### （3）区别

1. 写法格式不同

   ```js
   // v-model
   :value="num" + @input="updateNum"
   // .sync
   :num="num" + @update:num
   ```

2. v-model一个组件只能绑定一个，而.sync可以绑定多个



## 14、Vue2和Vue3对比

### （1）响应式系统

​	Vue2响应式实现的方式是通过ES5的`Object.defineProperty`，然后递归遍历所有属性，给每个属性添加`getter`和`setter`方法，劫持对象的属性，从而维护视图和数据的依赖关系，实现响应式。

```js
Object.defineProperty(obj, attr, {
  get:function(){
    // 值的读取操作，进行依赖收集
    return obj[attr]
  },
  set:function(newValue){
    // 值的更新操作，触发依赖更新
    obj[attr] = newValue
  }
})
```

​	Vue3响应式实现的方式是通过ES6新增的`Proxy`和`Reflect`实现的，劫持整个对象，使用代理对象和反射对象相互配合实现响应式。该方式可以监听动态新增和删除的属性，监听数组索引的变化和`length`属性的变化。

```js
new Proxy(data, {
  // 拦截读取属性
  get (target, prop) {
    return Reflect.get( target, prop )
  }
  // 拦截设置属性值或添加新属性
  set (target, prop, value) {
  	return Reflect.set( target, prop, value )
	}
  // 拦截删除属性
	deleteProperty (target, prop) {
    return Reflect.deleteProperty( target, prop )
  }
})
```



### （2）书写方式

1. 提供setup的方式配合组合式API，可以建立组合逻辑、创建响应式数据、创建通用函数、注册生命周期钩子函数等；
2. 支持多个根节点；



### （3）编译方式

1. Vue2.x：通过标记静态节点，优化 diff 算法的过程；
2. Vue3.x：
   1. 静态标记，vue3 中标记和提升所有的静态节点，diff 的时候只要对比动态节点内容；
   2. 跟节点限制，template 中不需要唯一的根节点，可以直接放文本或者同级标签；
   3. 静态提升，所有静态的节点都被提升到`render`方法之外，只会在应用启动的时候被创建一次，之后使用只需要应用提取的静态节点，随着每次的渲染被不停的复用；
   4. `patch flag`，在动态标签末尾加上相应的标记，只能带 patchFlag 的节点才被认为是动态的元素，会被追踪属性的修改,能快速的找到动态节点,而不用逐个逐层遍历，提高了虚拟 dom diff 的性能；
   5. 缓存事件处理函数`cacheHandler`，避免每次触发都要重新生成全新的`function`去更新之前的函数；



### （4）源码体积

- 相比 Vue2，Vue3 整体体积变小了，移除了一些不常用的 API
- tree-shanking
  - 任何一个函数，如 ref、reactive、computed 等，仅在用到的时候打包；
  - 通过编译阶段的静态分析，找到没有引入的模块并打上标记，将这些模块都摇掉；



### （5）diff算法

- vue2中使用的是双端diff算法，同时比较新旧两组节点的两个端点的算法（比头、比尾、头比尾、尾比头）
- vue3中使用的是快速diff算法，借鉴了文本diff算法的预处理思路，先处理新旧两组节点中相同的前置节点和后置节点。当前置节点和后置节点全部处理完毕后，如果无法通过简单的挂载新节点或者卸载已经不存在的节点来更新，则根据节点间的索引关系，构造出一个最长递增子序列，最长递增子序列所指向的节点即为不需要移动的节点；



### （6）生命周期

beforeCreate   -> 使用 setup()
created            -> 使用 setup()
beforeMount    -> onBeforeMount
mounted          -> onMounted
beforeUpdate  -> onBeforeUpdate
updated           -> onUpdated
beforeDestroy -> onBeforeUnmount
destroyed        -> onUnmounted
errorCaptured -> onErrorCaptured



## 15、computed和watch的区别

### （1）计算属性computed

- 支持缓存，只有依赖的数据发生变化时，才会重新计算；
- 不支持异步；
- 如果computed属性的属性值是函数，那么**默认使用get方法**，函数的返回值就是属性的属性值；在computed中，属性有一个get方法和一个set方法，当数据发生变化时，会调用set方法；
- **如果一个属性是由其他属性计算而来的，这个属性依赖其他的属性，一般会使用computed**



### （2）watch监听器

- **不支持缓存**，当数据发生变化时，就会触犯相应的操作；
- 支持异步监听；
- 监听的函数接收两个参数，第一个参数是最新的值，第二个参数是变化之前的值；
- 监听数据必须是data中声明的或者父组件传递过来的props中的数据；



## 16、computed和methods的区别

- 计算属性是基于依赖进行缓存的，只有在它的相关依赖发生改变时才会重新求值；
- 方法是函数集合，是主动调用才会执行的函数；



## 17、组件通信

### （1）prop/emit

用于父子组件之间的通信。

- prop父组件向子组件传值；
- $emit子组件向父组件发送事件和传递值；



### （2）provide/inject

用于父子和祖孙组件之间的通信，非响应式的。

```js
provide(){
  return {
    app: this
  }
}
data(){
  return {
    num: 1
  }
}
inject:['app']
// 使用方法
console.log(this.app.num)
```



### （3）ref/$refs

通过$refs方法获取组件实例，可以获取到目标组件的数据和方法。



### （4）$parent/$children

- 使用`$parent`可以让组件访问父组件的实例（访问的是上一级父组件的属性和方法）；
- 使用`$children`可以让组件访问子组件的实例，但是，`$children`并不能保证顺序，并且访问的数据也不是响应式的。



### （5）$attr/$listeners

- $attrs继承所有父组件属性（除了prop传递的属性、class和style），一般用在组件的子元素上；
- 





### （6）eventBus事件总线



## 18、生命周期执行

### （1）生命周期说明



### （2）执行顺序和机制



## 19、组件缓存keep-alive









## 15、Vue.$set原理

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