## 1、pinia基本特点

​	Vue状态管理工具，和Vuex相比pinia去除了vuex中对于同步函数Mutations和异步函数Actions的区分。直接在Actions中便能够使用同步和异步方法。其次相比于vuex，pinia对于typescript的支持性更好，友好的devTools支持，pinia只有1kb，简化了很多方法的写法。由于vuex比较完善，因此,pinia更加适合小型项目，vuex更加适合大型项目。



## 2、项目使用

### （1）安装

```shell
npm install pinia@next
```



### （2）导入pinia

```javascript
//在main.js文件中
import { createApp } from 'vue'
import App from './App.vue'
import { createPinia } from 'pinia'
//从pinia中结构出来的createPinia是一个函数,挂载需要先调用执行
const app = createApp(App)
app.use(createPinia())
app.mount('#app')
```



### （3）配置状态管理文件

在项目目录src文件夹下创建store文件夹，并创建一个index.js文件

```javascript
import {defineStore} from 'pinia';
//defineStore返回的是一个回调方法
export const useDemoStore = defineStore({
  id:'demo',//状态管理的唯一标志，也可以使用defineStore(id,{});的形式
  state(){
    return{
      name:'zhang',
      age:20
    }
  },
  getters:{
    testGetters(){
      //可以直接使用this获取state里面的值
      return this.name+'哥'
    }
  },
  actions:{
    addAge(){
      this.age++
    },
  }
})
```



### （4）传参和调用

```vue
<script>
//在状态管理工具中定义addAge函数s
actions:{
  addAge(gap){
    this.age+=gap;
  }
}

//组件中导入对应状态管理工具
import {userTestStore} from "./store";
const store = useTestStore();
//解构出store实例里面的方法
const {addAge} = store;
  
</script>

//组件调用
<div>output age:{{ store.age }}</div>
<button @click="addAge(10)">click for add age</button>
```

⚠️注意：解构store实例里面的state变量时，不能直接修改其值，否则解构出来的变量会失去响应式；如果一定要修改需使用storeToRefs()方法。

```vue
//错误案例
<div>error:{{ age }}</div>
<button @click="test">change error age</button>

<script>
let { age } = store;
function test() {
  console.log(" error add age");
  age++;
}
</script>
```

要想让解构后的值还具有响应式特性，方法如下：

```javascript
import { storeToRefs } from "pinia";
let { age } = storeToRefs(userTestStore());
function test() {
  age.value++;//注意这里要加上.value因为被转化成了ref类型
}
```



### （5）修改store值得五种方式

```javascript
//store.js文件
// defineStore 调用后返回一个函数，调用该函数获得 Store 实体
import { defineStore } from "pinia";
import { Names } from "./store-name";
//定义的defineStore()，并且它需要一个唯一的名称，Names.Test名称抽离出去作为第一个参数传递
export const useTestStore = defineStore(Names.Test, {
    state: () => ({ current: 1, name: 'zhang' }),
    //computed：修饰一些值
    getters: {

    },
    //methods:可以做同步异步，提交state
    actions: {
        setCurrent(type: boolean) {
            //通过this指向属性
            if (!type) return false
            this.current++
        }
    }
})
```

在vue3中使用：

```vue
<template>
  <div>
    pinia:{{ current }}---{{ Test.name }}
    <button @click="setCur">set</button>
  </div>
</template>
<script lang="ts" setup>
import { useTestStore } from './store';

const Test = useTestStore()
const current = computed(() => Test.current)
/**
 * state的五种方式修改值
    1.直接修改值
        Test.current=2
    2.通过$patch修改,支持单个或多个属性修改
        Test.$patch({current:33})
    3.$patch工厂函数方式
        Test.$patch((state) => {
          state.current = 99;
          state.name = '范迪塞尔'
        })
    4.$state 缺点是必须修改整个对象
        Test.$state = { current: 88, name: '123' }
    5.action
        Test.setCurrent()
 */
const setCur = () => {
  Test.current = 2
  Test.$patch({ current: 33 })
  Test.$patch((state) => {
    state.current = 99;
    state.name = '范迪塞尔'
  })
  Test.$state = { current: 88, name: '123' }
  Test.setCurrent(true)
}
</script>

<style lang = "scss" scoped>
</style>
```

