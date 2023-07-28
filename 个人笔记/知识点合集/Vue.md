## 1、Vue基本原理

### （1）简介

​	当一个Vue实例创建时，Vue会遍历data中的属性，使用`Object.defineProperty（Vue3.0使用Proxy）`将所有的属性转为`getter/setter`，并在内部追踪相关依赖，在属性被访问和修改时通知变化。

​	每个组件实例都有相应的**`watcher`**实例，会在组件渲染过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而影响关联的组件得以更新。

1. 创建Vue实例后，Vue会调用init方法初始化生命周期、事件、props、methods、data、computed和watch等属性方法。其中最重要的是对data进行遍历，给属性设置getter/setter方法，实现响应式和依赖收集；
2. 初始化后调用$mount方法挂载当前组件实例；
3. 接着进行模板编译，包含三个过程：
   1. parse阶段使用正则表达式解析template模板，形成抽象语法树；
   2. optimize阶段标记静态节点，执行编译优化，方便diff算法跳过静态节点，提高patch函数的性能；
   3. generate阶段将抽象语法树转换为render函数，形成渲染VNode所需的render函数；
4. render函数将编译后的结果转换为VNode节点，虚拟DOM就是以VNode节点对象作为基础所构建的树，是对真实DOM的抽象，最后会映射到真实DOM上；
5. 当对象被读取时执行getter函数进行**依赖收集**，依赖收集器Dep管理了被观察对象Observe和观察者Watcher之间的关联关系。当对象被修改的时候执行setter函数通知依赖收集器中收集的所有Watcher依赖的值发生了改变，需要重新渲染视图，Watcher调用update函数更新视图；
6. update函数负责更新视图，update函数中包含一个patch的过程也就是diff算法过程，以及使用队列异步更新视图的过程；

![image-20230728193232903](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307281932117.png)



### （2）优点

1. 轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十`kb`；
2. 入手简单：国人开发，中文文档，易于学习和理解；
3. 双向数据绑定：数据操作方面简单；
4. 组件化：实现html的封装和复用，减少代码编写量；
5. 虚拟DOM：不使用原生DOM节点，极大解放DOM操作，减少性能消耗；
6. 数据视图分离：数据更改简单，且只需要操作数据就能实现页面修改；







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