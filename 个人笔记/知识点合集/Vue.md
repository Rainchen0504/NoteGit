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