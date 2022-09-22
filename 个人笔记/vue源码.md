# 一、Vue2源码解读

## 1、版本

2.6.14



## 2、地址

github仓库地址：<mark style="background-color: #40E0D0">https://github.com/vuejs/vue.git</mark>，当前默认是2.7.6版本，需切换版本分支。

commit标签：<font color=deepred>**612fb89547711cacb030a3893a0065b785802860 (HEAD -> main, tag: v2.6.14)**</font>

切换指令：`git reset --hard` 版本号，通过`git log`可以查看提交记录。



## 3、装包

使用npm i安装依赖，建议node版本14.15.5，不建议使用16以上版本。



## 4、目录结构

```shell
├── benchmarks    处理复杂情况时（大表格、大批量svg）Vue内置的的一些跑分demo
├── dist    构建打包的输出目录，包含不同Vue版本
├── examples    案例目录
├── flow    Vue源码类型检查用flow，这里存放类型声明文件
├── packages   一些额外的npm包，可以单独引用
│ ├── vue-server-renderer   负责服务端渲染的包
│ ├── vue-template-compiler   配合vue-loader使用的模板解析包
│ ├── weex-template-compiler  
│ └── weex-vue-framework
├── scripts   存放npm脚本配置文件，结合webpack、rollup等工具执行编译
├── src vue   源码目录
│ ├── compiler    解析模板相关目录（编译器）
│ │ ├── codegen   把AST转换成Render函数
│ │ ├── directives
│ │ ├── parser    把模板解析成AST
│ ├── core    运行时核心包
│ │ ├── components    组件属性，主要包含keep-live
│ │ ├── config.js   一些默认配置项
│ │ ├── global-api    全局API，比如Vue.use()、Vue.component()、Vue.extend()等
│ │ ├── instance    实例化相关内容(生命周期、事件)，Vue构造函数就在这个目录下
│ │ ├── observer    响应式原理核心
│ │ ├── util    工具方法
│ │ └── vdom    虚拟DOM相关，比如patch算法就在这
│ ├── platforms   平台相关的编译器代码
│ │ ├── web
│ │ │ ├── compiler    web端编译器，用来编译模板成render函数
│ │ │ ├── runtime   web端运行时，用来创建Vue实例
│ │ │ ├── server    服务端渲染
│ │ │ └── util    相关工具类
│ │ └── weex    和web端类似
│ ├── server    服务端渲染(ssr)相关
│ ├── sfc   转换单文件组件(.vue)
│ └── shared    全局共享的一些方法和常量
├── test    测试用例目录
└── types   支持TS的类型声明文件
```

Vue.js 的源码都在 src 目录下：

### （1）compiler

​	compiler目录包括把模板解析成 ast 语法树，ast 语法优化，代码生成等功能。

​	编译的工作可以在构建时做（借助 webpack、vue-loader 等辅助插件）；也可以在运行时做，使用包含构建功能的 Vue.js。显然，编译是一项耗性能的工作，所以更推荐前者——离线编译。

### （2）core

​	核心代码，包括内置组件、全局API、Vue实例化、观察者、虚拟DOM、工具函数。

### （3）platform

​	web平台和native客户端的入口，分别打包成运行在 web 上和 weex 上的 Vue.js。

### （4）server

​	服务端渲染，跑在服务端Node.js上。

​	服务端渲染主要把组件渲染为服务器端的 HTML 字符串，直接发送到浏览器，最后将静态标记"混合"为客户端上完全交互的应用程序。

### （5）sfc

​	把 .vue 模板文件内容解析成一个 JavaScript 的对象。

### （6）shared

​	定义被浏览器端的 Vue.js 和服务端的 Vue.js 所共享的工具和方法。



## 5、源码构建

​	vue源码基于Rollup构建，`npm run build`执行`node scripts/build.js`命令， 进入scripts目录下的build.js文件，build.js文件会从config.js文件读取配置，通过终端命令行参数对构建配置做过滤，构建不同需求版本的vueJS，生成到dist目录中。

### （1）不同版本Vue

|                               | **UMD**            | **CommonJS**          | **ES Module**      |
| ----------------------------- | ------------------ | --------------------- | ------------------ |
| **Full**                      | vue.js             | vue.common.js         | vue.esm.js         |
| **Runtime-only**              | vue.runtime.js     | vue.runtime.common.js | vue.runtime.esm.js |
| **Full (production)**         | vue.min.js         |                       |                    |
| **Runtime-only (production)** | vue.runtime.min.js |                       |                    |

- Full：完整版，包含编译器和运行时；
- Compiler：编译器，将模板字符串转为JS渲染函数版本，体积大，效率低；
- Runtime：运行时，用来创建Vue实例，渲染并处理虚拟DOM等的代码，体积小，效率高。（去除编译器）使用脚手架创建Vue项目默认使用的是运行时版本，配合打包工具中的compiler，在编译阶段就把.vue文件转换为render函数。

### （3）终端参数补充

​	process.argv属性返回一个数组，包含启动node进程时的命令行参数；[0]参数返回启动Node.js进程的可执行文件所在的绝对路径；[1]为当前执行的JavaScript文件路径，剩余的元素为其他命令行参数。

​	比如输入node scripts/build.js “web-runtime-cjs,web-server-renderer”

```javascript
console.log(process.argv[0]) // 打印D:\nodeJs\node.exe
console.log(process.argv[1]) // 打印E:\Study_document\vue-resource\vue-dev\scripts\build.js
console.log(process.argv[2]) // 打印 web-runtime-cjs,web-server-renderer
```



### （4）调试

在packjson文件中dev命令尾部添加`--sourcemap`，方便调试查看源码位置。

执行`npm run dev`，查看项目目录下的dist文件夹，内部vue文件夹下生成vue.js.map和vuemin.js则打包成功。

⚠️不要终止终端，开着可以保持热模块更新，即修改源码会自动进行打包，方便调试。



按照官方文档构建一个案例，引入打包后的vue.js文件，在创建实例之前打上断点

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue2源码解读</title>
  </head>
  <body>
    <div id="app">{{ message }}</div>
    <script src="../dist/vue.js"></script>
    <script>
      debugger;
      new Vue({
        el: "#app",
        data: {
          message: "hello Vue",
        },
      });
    </script>
  </body>
</html>
```

打开控制台，在浏览器上运行，即可找到Vue构造函数对应源码位置。



## 6、初始化入口

在web环境下使用运行时和编译器构建出来的Vue文件入口是`src/platforms/web/entry-runtime-with-compiler.js`，其中有声明Vue的来源

```js
import Vue from './runtime/index'
```

进入该文件`src/platforms/web/runtime/index.js`，核心内容是Vue真正初始化的位置：

```javascript
import Vue from 'core/index'
```

<mark style="background-color: #40E0D0">位置：/src/core/index.js</mark>

这里有两处关键代码:

- `import Vue from './instance/index'`---->此处初始化Vue实例
- `initGlobalAPI(Vue)`---->这里初始化全局Vue API



### （1）Vue 的定义

<mark style="background-color: #40E0D0">位置：/src/core/instance/index.js</mark>

```js
import { initMixin } from './init'
import { stateMixin } from './state'
import { renderMixin } from './render'
import { eventsMixin } from './events'
import { lifecycleMixin } from './lifecycle'
import { warn } from '../util/index'

//创建Vue实例的构造函数（用函数实现类）
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    //调用Vue实例时当前环境不是生产环境而且没有用new操作符时进入这里
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  // 调用 Vue.prototype._init方法，参数params就是模板中new Vue里面包含的对象
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)

export default Vue
```

​	用 Function 实现的类，只能通过 `new Vue` 去实例化。后面有很多 `xxxMixin` 的函数调用，并把 `Vue` 当参数传入，功能都是给 Vue 的 prototype 上扩展一些方法，Vue 按功能把向原型上挂载方法的过程分解到不同模块文件中实现，而不是在一个模块里实现所有。这种方式是用 Class 难以实现的。这么做的好处是非常方便代码的维护和管理。



⚠️**<font color=blue>拓展点</font>**：

- instanceof运算符：

  ```javascript
  //obj instanceof con --> con.prototype是否存在参数obj的原型链上
  function Vue(options) {
    console.log("2",this instanceof Vue)
    console.log("3",this)
    if(!(this instanceof Vue)){
      console.log("5请使用new关键字")
    }
    this._init(options)
  }
  Vue.prototype._init = function(options){
  	console.log("11",options)
  }
  const demo1 = new Vue('Honda')
  //2,true
  //3,Vue{}	当前实例对象
  //11,Honda
  const demo2 = Vue('benz')
  //2,false
  //Windowu对象
  //5请使用new关键字
  //Error: this._init is not a function，全局window对象上没有_init方法
  ```



### （2）InitGlobalAPI

<mark style="background-color: #40E0D0">位置：src/core/global-api/index.js</mark>

Vue.js 在整个初始化过程中，除了给它的原型 prototype 上扩展方法，还会给 `Vue` 这个对象本身扩展全局的静态属性方法。

```javascript
export function initGlobalAPI (Vue: GlobalAPI) {
  // config
  const configDef = {}
  configDef.get = () => config
  if (process.env.NODE_ENV !== 'production') {
    configDef.set = () => {
      warn(
        'Do not replace the Vue.config object, set individual fields instead.'
      )
    }
  }
  Object.defineProperty(Vue, 'config', configDef)

  // exposed util methods.
  // NOTE: these are not considered part of the public API - avoid relying on
  // them unless you are aware of the risk.
  Vue.util = {
    warn,
    extend,
    mergeOptions,
    defineReactive
  }

  Vue.set = set
  Vue.delete = del
  Vue.nextTick = nextTick

  // 2.6 explicit observable API
  Vue.observable = <T>(obj: T): T => {
    observe(obj)
    return obj
  }

  Vue.options = Object.create(null)
  ASSET_TYPES.forEach(type => {
    Vue.options[type + 's'] = Object.create(null)
  })

  // this is used to identify the "base" constructor to extend all plain-object
  // components with in Weex's multi-instance scenarios.
  Vue.options._base = Vue

  extend(Vue.options.components, builtInComponents)

  initUse(Vue)
  initMixin(Vue)
  initExtend(Vue)
  initAssetRegisters(Vue)
}
```

这里是在 Vue 上扩展的一些全局方法的定义，Vue 官网中关于全局 API （比如set、delete、component、extend、nextTick等）都可以在这里找到。`Vue.util` 暴露的方法最好不要依赖，因为它可能经常会发生变化，是不稳定的。



## 7、数据驱动

vue的核心思想之一就是数据驱动视图。对视图的修改不会直接操作DOM，DOM变成了数据的映射，Vue会将模板渲染成DOM。

### 7.1 new Vue

<mark style="background-color: #40E0D0">位置：/src/core/instance/index.js</mark>

Vue只能通过new关键字实例化，接着调用`this._init`方法，进入到该方法文件；

#### （1）Vue初始化

<mark style="background-color: #40E0D0">位置：/src/core/instance/init.js</mark>

​	这部分代码主要完成了<font color=deepred>**合并配置、初始化生命周期、初始化事件中心、初始化渲染、初始化data、初始化props、初始化computed、初始化watcher等工作**</font>。

​	在init初始化的最后，如果有 `el` 属性，则调用 `vm.$mount` 方法挂载 `vm`，挂载的目标就是把模板渲染成最终的 DOM。

```javascript
Vue.prototype._init = function (options?: Object) {
  //缓存当前的上下文（vue实例）到vm变量中
  const vm: Component = this;

  //设置_uid属性，每个实例都有唯一的_uid。当新建Vue实例时或者渲染组件时就会触发init方法，uid就会递增
  vm._uid = uid++;

  //打标记点测试性能
  let startTag, endTag;
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== "production" && config.performance && mark) {
    startTag = `vue-perf-start:${vm._uid}`;
    endTag = `vue-perf-end:${vm._uid}`;
    mark(startTag);
  }

  //在observed函数中会做判断，如果_isVue为true就不会新建observer实例
  // a flag to avoid this being observed
  vm._isVue = true;

  //处理组件配置项
  // merge options
  if (options && options._isComponent) {
    // 每个子组件初始化时走这里，为vm.$options添加一些属性，以提高代码的执行效率
    // optimize internal component instantiation
    // since dynamic options merging is pretty slow, and none of the
    // internal component options needs special treatment.
    initInternalComponent(vm, options);
  } else {
    /**
      * 当前Vue实例不是子组件（根组件->实例化的Vue对象）时走这里
      * 合并Vue的全局配置到根组件的局部配置，比如 Vue.component 注册的全局组件会合并到根实例的 components 选项中
      * 至于每个子组件的选项合并则发生在两个地方：
      * 1、Vue.component 方法注册的全局组件在注册时做了选项合并
      * 2、{ components: { xx } } 方式注册的局部组件在执行编译器生成的 render 函数时做了选项合并，包括根组件中的 components 配置
     */
    vm.$options = mergeOptions(
      //把当前Vue实例传过去
      resolveConstructorOptions(vm.constructor),
      options || {},
      vm
    );
  }

  //设置代理，将vm实例上的属性代理到vm._renderProxy
  //开发环境设置代理主要是起提示作用，开发环境中会提供较多警告帮助解决常见错误，而生产环境不具备此功能
  /* istanbul ignore else */
  if (process.env.NODE_ENV !== "production") {
    //开发环境调initProxy方法
    initProxy(vm);
  } else {
    //生产环境下Vue实例的_renderProxy属性指向vue实例本身
    vm._renderProxy = vm;
  }
  // expose real self
  vm._self = vm;

  //初始化生命周期相关的属性，比如mounted、destoryed等；
  //初始化组件实例关系属性，比如 $parent、$children、$root、$refs等
  initLifecycle(vm);

  initEvents(vm);
  initRender(vm);
  callHook(vm, "beforeCreate");
  initInjections(vm); // resolve injections before data/props
  initState(vm);
  initProvide(vm); // resolve provide after data/props
  callHook(vm, "created");

  /* istanbul ignore if */
  if (process.env.NODE_ENV !== "production" && config.performance && mark) {
    vm._name = formatComponentName(vm, false);
    mark(endTag);
    measure(`vue ${vm._name} init`, startTag, endTag);
  }

  //如果检测到实例化Vue时有写el属性，就调用vm.$mount挂载vm，挂载的目标就是把模板渲染成DOM
  if (vm.$options.el) {
    vm.$mount(vm.$options.el);
  }
};
```

⚠️**<font color=blue>拓展点</font>**：

- constructor始终指向创建当前对象的构造函数

  ```javascript
  function Vue(option){
    console.log("2",this.constructor)
    const vm = this
  }
  new Vue({name:"zhang"})
  //2 ƒ Vue(option){
  //  	console.log("2",this.constructor)
  //  	const vm = this
  //  }
  ```



##### （1.1）解析配置属性resolveConstructorOptions

<mark style="background-color: #40E0D0">位置：/src/core/instance/init.js</mark>

```javascript
//解析当前实例构造函数上的options，合并基类上的options配置选项
export function resolveConstructorOptions (Ctor: Class<Component>) {
  let options = Ctor.options
  //如果Ctor(构造函数)是通过new关键字新建的Vue构造函数实例时，options就是Vue构造函数上的options，不执行if内代  -->拓展1
  //如果Ctor是Vue.extend构建的子类，有super属性，该属性指向父类构造器（基类构造函数）,执行if内代码  -->拓展2
  if (Ctor.super) {
    //递归解析"基类"即构造函数Vue的选项options赋值给superOptions变量
    const superOptions = resolveConstructorOptions(Ctor.super)
    //把"子类"自身（也就是Vue.extend）构造函数上的选项options赋值给cachedSuperOptions变量
    const cachedSuperOptions = Ctor.superOptions
    //如果不相同说明基类构造函数选项options发生了改变，如执行了Vue.mixin方法等
    if (superOptions !== cachedSuperOptions) {
      //把"子类"的superOptions属性重新替换成最新的
      Ctor.superOptions = superOptions
      //检查子类options是否有后期修改或者附加的属性
      const modifiedOptions = resolveModifiedOptions(Ctor)
      //如果子类的options存在修改，则把修改后的options属性合并到Ctor.extendOptions属性上
      if (modifiedOptions) {
        extend(Ctor.extendOptions, modifiedOptions)
      }
      //调用mergeOptions方法把子类上的extendOptions和父类构造函数上的options合并起来
      options = Ctor.options = mergeOptions(superOptions, Ctor.extendOptions)
      if (options.name) {
        options.components[options.name] = Ctor
      }
    }
  }
  return options
}
```

![image-20220720112634035](https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201126302.png)

⚠️**<font color=blue>拓展点</font>**：

- **拓展1**：options定义来源

  在拓展1位置获取Ctor（构造函数）的options属性，得到如下内容

  ![image-20220719204430883](https://raw.githubusercontent.com/Rainchen0504/picture/master/202207192044302.png)

  options如何从空对象到上图属性过程如下解释

  属性定义的位置：

  1. 首先，package.json文件的命令dev中，通过rollup打包时入口文件地址为`scripts/config.js`；
  2. 进入config.js文件后查找target地址，进入`src/platforms/web/entry-runtime-with-compiler.js`；
  3. 文件中`import Vue from './runtime/index'`，进入该目录；
  4. Vue.options的部分中给`Vue.options.directives`添加了model和show属性，给`Vue.options.components`添加了Transition和TransitionGroup属性；
  5. 再进入文件中`import Vue from 'core/index'`目录下，找到`initGlobalAPI`方法，进入方法`global-api/index`；
  6. 该方法实现了components中的KeepAlive、filters和_base属性的添加。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202207192229598.png" alt="image-20220719222939861" style="zoom:80%;" />

- **拓展2**：Vue.extend(options)

  ​	extend继承基础构造器Vue类（基类），传入一个extendOption生成一个新的构造函数（子类），在extend的时候会进行mergeOption，融合原型上的baseOption，所以extend出来的子类也能使用keep-alive等全局组件。

  ​	作用：生成组件类在挂载全局组件和设置components属性时会用到，在生成DOM的时候会new实例化挂载。

  ```javascript
  let Footer = Vue.extend({
    data(){
      return {
        footerName:"I CAN DO IT"
      }
    },
    template:'<div>{{footerName}}</div>'
  })
  //实例化方法：
  //使用new extendName().$mount(''+el)方式进行实例化（模拟组件）
  new Footer().$mount('#footer-view')
  //使用vue.component进行实例化
  Vue.component("footer-view",Footer)
  ```

  ​	在通过Vue.extend构造子类的时候，Vue.extend方法会为子类添加一个super属性，指向其父类构造器。

  ```javascript
  Vue.extend = function (extendOptions: Object): Function {
    ...
    Sub['super'] = Super
    ...
  }
  ```

  

##### （1.2）解析附加属性resolveModifiedOptions

<mark style="background-color: #40E0D0">位置：/src/core/instance/init.js</mark>

```javascript
//解析传入的构造函数选项中是否有后期修改或者附加的属性
function resolveModifiedOptions (Ctor: Class<Component>): ?Object {
  let modified
  //传入的子类构造函数的选项options
  const latest = Ctor.options
  //执行Vue.extend时封装的options选项，方便检查该子类的options有没有变化
  const sealed = Ctor.sealedOptions
  //如果当前构造器上的options属性不在Vue.extend时封装的options选项里，那么该属性就是新添加的，记录新增的属性并返回
  for (const key in latest) {
    if (latest[key] !== sealed[key]) {
      if (!modified) modified = {}
      modified[key] = latest[key]
    }
  }
  return modified
}
```



##### （1.3）合并配置mergeOptions

<mark style="background-color: #40E0D0">位置：/src/core/util/options.js</mark>

```javascript
//合并函数，将子类和基类的options合并起来，生成一个新的配置对象
export function mergeOptions (
  parent: Object, //父类构造函数上的options（基类实例本身构造函数上的options）--人话：Vue实例自带的options
  child: Object,//子类构造函数上的extendOptions（基于基类创建子类实例在实例化时传入的options）--人话：实例化时传入的配置项
  vm?: Component//组件实例本身	-->拓展1
): Object {
  //判断是否是生产环境，不是则调用方法检查组件名称是否可用
  if (process.env.NODE_ENV !== 'production') {
    //如果options(实例化时传入的配置项)有components属性，调用validateComponentName方法
    //如果包含数字，字母，下划线，连接符，并且以字母开头且不是内置或关键字元素作为组件名，通过校验
    checkComponents(child)
  }
  //如果child是function类型取options属性作为child
  if (typeof child === 'function') {
    child = child.options
  }
	//分别把child中的props,inject,directives属性转成对象形式，标准化处理	-->拓展2
  normalizeProps(child, vm)
  normalizeInject(child, vm)
  normalizeDirectives(child)
  // 如果实例化时传入的options有mixins和extends属性时，再次调用merge方法
  // 合并mixins和extends里的内容到父类parent构造函数的options上
  if (!child._base) {
    if (child.extends) {
      parent = mergeOptions(parent, child.extends, vm)
    }
    if (child.mixins) {
      for (let i = 0, l = child.mixins.length; i < l; i++) {
        parent = mergeOptions(parent, child.mixins[i], vm)
      }
    }
  }
  
  const options = {}
  let key
  //遍历父类构造函数的options属性
  for (key in parent) {
    mergeField(key)
  }
  //遍历子类构造函数(实例化时)的options属性
  for (key in child) {
     //如果父类中不存在则执行合并方法，否则跳过，因为父类和子类构造函数具有相同属性的情况在上面处理过了
    if (!hasOwn(parent, key)) {
      mergeField(key)
    }
  }
  //属性合并策略，childVal优先级高于parentVal
  function mergeField (key) {
    //strats = Object.create(null)	-->拓展3
    const strat = strats[key] || defaultStrat
    //defaultStrat方法返回值时执行了一个三目运算符，如果child上存在该属性时就取child上的值，不存在取parent上的值
    options[key] = strat(parent[key], child[key], vm, key)
  }
  return options
}
```

![image-20220720185649410](https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201856891.png)

⚠️**<font color=blue>拓展点</font>**：

- **拓展1**：打印参数实例：

  ```html
  <!--模板中写法-->
  <div id="app">{{ message }}
    <button-counter></button-counter>
  </div>
  <script>
    var app = new Vue({
      el: "#app",
      data: {
        message: "Hello Vue!",
      },
      components:{
        buttonCounter:{
          template:"<button v-on:click='count++'>点击次数{{ count }}</button>",
          data(){
            return {
              count : 0
            }
          }
        }
      }
    });
  </script>
  ```

  打印`parent、child和vm`对象，结果如下

  ```shell
  parent-->{components: {…}, directives: {…}, filters: {…}, _base: ƒ}
  child\{el: '#app', data: {…}, components: {…}}
  Vue {_uid: 0, _isVue: true}
  
  {components: {…}, directives: {…}, filters: {…}, _base: ƒ}
  {template: "<button v-on:click='count++'>点击次数{{ count }}</button>", _Ctor: {…}, data: ƒ}
  undefined
  ```

- **拓展2**：props,inject,directives属性转对象标准化处理

  1. normalizeProps处理props属性

     ```javascript
     function normalizeProps(options: Object, vm: ?Component) {
       const props = options.props;
       if (!props) return;
       const res = {};
       let i, val, name;
       //props是数组,比如场景：
       //Vue.component('son', {
       //  props: ['sendInfo'],
       //  template: '<h1>{{ sendInfo }}</h1>'
       //})
       if (Array.isArray(props)) {
         i = props.length;
         while (i--) {
           val = props[i];
           if (typeof val === "string") {
             name = camelize(val);
             //每项的值作为res对象的key，value值为{type:null}
             res[name] = { type: null };
             //上面场景最终结果为{ sendInfo: { type: null } }
           } else if (process.env.NODE_ENV !== "production") {
             warn("props must be strings when using array syntax.");
           }
         }
     	//props是对象，比如场景：
       //Vue.component('son', {
       //	props: {
       //    propC:{
       //      type:String,
       //      required:true
       //    }
       //  },
       //  template: '<h1>{{ sendInfo }}</h1>'
       //})
       } else if (isPlainObject(props)) {
         //isPlainObject方法内部使用object.prototype.toString方法判断结果是否是[object Object]
         for (const key in props) {
           val = props[key];
           //把对象的key使用camelize方法先转为驼峰形式
           name = camelize(key);
           //如果对象的值是纯对象(object.prototype.toString结果是[object Object])，则赋值给res，否则赋值{type: val}
           res[name] = isPlainObject(val) ? val : { type: val };
           //上面场景最终结果为
           // propC:{
           //   type:String,
           //   required:true
           // }
         }
       //如果传入的props不是纯对象也不是数组，且当前环境也不是生产环境报警告
       } else if (process.env.NODE_ENV !== "production") {
         warn(
           `Invalid value for option "props": expected an Array or an Object, ` +
             `but got ${toRawType(props)}.`,
           vm
         );
       }
       //把处理过的props重新赋值给options.props
       options.props = res;
     }
     ```

  2. normalizeInject处理inject属性

     inject属性配合provide使用，允许一个祖先组件向所有子孙后代注入依赖，内部和props相同。

  3. normalizeDirectives处理directive属性

     directive主要是实现自定义指令用。

- **拓展3**：starts中的合并策略总结(实例化时每个实例的配置属性合并策略)

![image-20220720180313213](https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201803601.png)

1. 钩子函数策略（mergeHook方法）

   （1）child的options上不存在而parent的options上存在，则返回parent上的属性。

   <img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201820497.png" alt="image-20220720181958075" style="zoom:80%;" />

   （2）child和parent都存在该属性，则返回concat之后的属性。

   <img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201821856.png" alt="image-20220720182110042" style="zoom:80%;" />

   （3）child上存在该属性，parent不存在，且child上的该属性是Array，则直接返回child上的该属性

   <img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201822383.png" alt="image-20220720182216063" style="zoom:80%;" />

   

   （4）child上存在该属性，parent不存在，且child上的该属性不是Array，则把该属性先转换成Array，再返回。

   <img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202207201822925.png" alt="image-20220720182234673" style="zoom:80%;" />

2. props/methods/inject/computed属性的策略

   如果child的options上这些属性存在，则先判断是不是对象。

   （1）如果parent options上没有该属性，则直接返回child的options上的属性；

   （2）如果parent options和child options都有，则合并parent options和child options并生成一个新的对象。

   ​	(如果parent和child上有同名属性，合并后的以child options上的为准)

3. components/directives/filters属性的策略

   道理同上

4. data和provide的策略

   （1）情况一，当前调用mergeOptions操作的是vm实例（调用new新建vue实例触发mergeOptions方法）

   ​		  如果新建实例传入的options有data属性，则合并实例和构造该实例的构造函数上的data属性。

   （2）情况二，当前调用mergeOptions操作的不是vm实例（通过Vue.extend/Vue.component调用了mergeOptions方法）

   ​		  有一个没有则取有的，两个都有则合并处理。



##### （1.4）初始化代理initProxy

<mark style="background-color: #40E0D0">位置：/src/core/instance/proxy.js</mark>

```javascript
initProxy = function initProxy (vm) {
  //判断当前环境中Proxy属性是否可用
  if (hasProxy) {
    //如果Proxy属性存在，把处理过后的vm属性赋值给_renderProxy属性；
    const options = vm.$options
    const handlers = options.render && options.render._withStripped
    ? getHandler
    : hasHandler
    vm._renderProxy = new Proxy(vm, handlers)
  } else {
    //否则把vm实例本身赋值给_renderProxy属性；
    vm._renderProxy = vm
  }
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202207211742695.png" alt="image-20220721174246246" style="zoom:80%;" />



##### （1.5）初始化生命周期和关系属性initLifecycle

<mark style="background-color: #40E0D0">位置：/src/core/instance/lifecycle.js</mark>



##### （1.6）初始化事件中心initEvents

<mark style="background-color: #40E0D0">位置：/src/core/instance/events.js</mark>



##### （1.7）初始化渲染initRender

<mark style="background-color: #40E0D0">位置：/src/core/instance/render.js</mark>



##### （1.8）初始化data/methods/props/computed/watch属性initState

<mark style="background-color: #40E0D0">位置：/src/core/instance/state.js</mark>

```js
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```

new Vue时设置了data属性会调用initData方法：

```js
function initData (vm: Component) {
  let data = vm.$options.data
  //判断data是函数还是对象
  data = vm._data = typeof data === 'function'
    ? getData(data, vm)
    : data || {}
  if (!isPlainObject(data)) {
    //如果data不是一个对象会报警告
    data = {}
    process.env.NODE_ENV !== 'production' && warn(
      'data functions should return an object:\n' +
      'https://vuejs.org/v2/guide/components.html#data-Must-Be-a-Function',
      vm
    )
  }
  // proxy data on instance
  const keys = Object.keys(data)
  const props = vm.$options.props
  const methods = vm.$options.methods
  let i = keys.length
  while (i--) {
    //保证data值的属性名和props还有methods方法名不能重名，因为最终这些属性都会挂载在vm实例对象上
    const key = keys[i]
    if (process.env.NODE_ENV !== 'production') {
      if (methods && hasOwn(methods, key)) {
        warn(
          `Method "${key}" has already been defined as a data property.`,
          vm
        )
      }
    }
    if (props && hasOwn(props, key)) {
      process.env.NODE_ENV !== 'production' && warn(
        `The data property "${key}" is already declared as a prop. ` +
        `Use prop default value instead.`,
        vm
      )
    } else if (!isReserved(key)) {
      // 使用proxy将data中数据属性都代理到vm实例的_data属性上
      proxy(vm, `_data`, key)
    }
  }
  // observe data
  // 对data做响应式处理
  observe(data, true /* asRootData */)
}
```

同文件下proxy函数

```js
// 相当于在方法中使用this.XX调用data中的属性实质上是使用this._data.XX
export function proxy (target: Object, sourceKey: string, key: string) {
  sharedPropertyDefinition.get = function proxyGetter () {
    return this[sourceKey][key]
  }
  sharedPropertyDefinition.set = function proxySetter (val) {
    this[sourceKey][key] = val
  }
  Object.defineProperty(target, key, sharedPropertyDefinition)
}
```

this.XX可以获取data中值的原因



### 7.2 Vue实例挂载

#### （1）常见挂载方法

vue2.x版本通过new Vue()进行实例化，常见使用方法有以下几种：

- 方法一：

  ```js
  import Vue from 'vue';
  import App from "./App.vue";
  new Vue({
    el: "#app",
    template:"<div>{{message}}</div>",
    data: {
      message: "hello Vue",
    },
  });
  ```

- 方法二：

  ```js
  import Vue from 'vue';
  import App from "./App.vue";
  import router from './router';
  import store from './store';
  new Vue({
    router,
    store
  }).$mount("#app");
  ```

- 方法三：

  ```js
  import Vue from 'vue';
  import App from "./App.vue";
  import router from './router';
  import store from './store';
  new Vue({
    router,
    store,
    render:h => h(App)
  }).$mount("#app");
  ```

**<font color=red>当Vue实例没有el属性时，就需要手动使用$mount进行挂载</font>**



#### （2）挂载实现

​	**Vue通过$mount方法完成实例化挂载的**，而$mount方法在web和weex平台中运行时、编译器文件中都有定义。该方法的实现受平台、构建方法影响。

<font color=blue>这里分析**带compiler编译器版本**的$mount实现（工作中会使用webpack中的vue-loader解析模板一般采用运行时版本）</font>

##### （2.1）挂载入口文件

<mark style="background-color: #40E0D0">位置：src/platform/web/entry-runtime-with-compiler.js</mark>

```js
//缓存原型上的$mount方法
const mount = Vue.prototype.$mount
//再重新定义$mount方法
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && query(el)

  /* istanbul ignore if */
  //对el进行限制，不能挂载在body、html这样的根元素节点上
  if (el === document.body || el === document.documentElement) {
    process.env.NODE_ENV !== 'production' && warn(
      `Do not mount Vue to <html> or <body> - mount to normal elements instead.`
    )
    return this
  }

  const options = this.$options
  // resolve template/el and convert to render function
  //判断挂载时是否定义了render方法
  if (!options.render) {
    let template = options.template
    if (template) {
      //没render情况下是如果设置了template属性
      if (typeof template === 'string') {
        if (template.charAt(0) === '#') {
          template = idToTemplate(template)
          /* istanbul ignore if */
          if (process.env.NODE_ENV !== 'production' && !template) {
            warn(
              `Template element not found or is empty: ${options.template}`,
              this
            )
          }
        }
      } else if (template.nodeType) {
        template = template.innerHTML
      } else {
        if (process.env.NODE_ENV !== 'production') {
          warn('invalid template option:' + template, this)
        }
        return this
      }
    } else if (el) {
      //没render情况下是如果设置了el属性
      template = getOuterHTML(el)
    }

    //将template或el字符串转换成render方法
    if (template) {
      /* istanbul ignore if */
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
        mark('compile')
      }
      //"在线编译"过程，使用compileToFunctions函数进行转换
      const { render, staticRenderFns } = compileToFunctions(template, {
        outputSourceRange: process.env.NODE_ENV !== 'production',
        shouldDecodeNewlines,
        shouldDecodeNewlinesForHref,
        delimiters: options.delimiters,
        comments: options.comments
      }, this)
      options.render = render
      options.staticRenderFns = staticRenderFns

      /* istanbul ignore if */
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
        mark('compile end')
        measure(`vue ${this._name} compile`, 'compile', 'compile end')
      }
    }
  }
  //调用原先原型上的 $mount 方法挂载
  return mount.call(this, el, hydrating)
}
```

1. 这里缓存了原型上的 `$mount` 方法，再重新定义该方法。

2. 首先对 `el` 做了限制，Vue 不能挂载在 `body`、`html` 这样的根节点上。

3. 其次如果没有定义 `render` 方法，**则会把 `el` 或者 `template` 字符串转换成 `render` 方法**。

   在 Vue 2.0 版本中，所有 Vue 的组件的渲染最终都需要 `render` 方法，无论是用单文件 .vue 方式开发组件，还是写了 `el` 或者 `template` 属性，最终都会转换成 `render` 方法，这个过程是<font color=red> **Vue 的一个“在线编译”的过程，它是调用 compileToFunctions 方法实现**</font>。

4. 最后调用原先原型上的 `$mount` 方法挂载。



##### （2.2）$mount方法

<mark style="background-color: #40E0D0">位置：src/platform/web/runtime/index.js</mark>

​	<font color=deepred>**这里再调原先原型上的$mount方法是因为：当前分析使用的是带编译器的版本，在挂载时首先需要进行编译再调用公共的挂载方法，因此先在当前版本$mount入口文件原型上添加编译方法，然后在调用原型上的公共方法**。</font>

​	原型上的$mount方法属于公共方法，可以被 `runtime only` 版本的 Vue 直接使用；

```js
// public mount method
Vue.prototype.$mount = function (
	//el表示挂载的元素
  el?: string | Element,
  //hydrating和服务端渲染相关参数
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}
```

`$mount` 方法支持传入 2 个参数：

1. 第一个是 `el`，表示**挂载的元素**，可以是字符串，也可以是 DOM 对象，如果是字符串在浏览器环境下会调用 `query` 方法转换成 DOM 对象的。
2. 第二个参数是和服务端渲染相关，在浏览器环境下不需要传第二个参数。



##### （2.3）mountComponent方法

<mark style="background-color: #40E0D0">位置：src/core/instance/lifecycle.js</mark>

$mount方法会调用mountComponent方法实现挂载

```js
export function mountComponent (
  vm: Component,
  el: ?Element,
  hydrating?: boolean
): Component {
  vm.$el = el
  if (!vm.$options.render) {
    vm.$options.render = createEmptyVNode
    if (process.env.NODE_ENV !== 'production') {
      /* istanbul ignore if */
      if ((vm.$options.template && vm.$options.template.charAt(0) !== '#') ||
        vm.$options.el || el) {
        //用runtime版本实例化挂载时必须使用render函数，使用template是无法编译的
        warn(
          'You are using the runtime-only build of Vue where the template ' +
          'compiler is not available. Either pre-compile the templates into ' +
          'render functions, or use the compiler-included build.',
          vm
        )
      } else {
        //template和render都没有定义报错
        warn(
          'Failed to mount component: template or render function not defined.',
          vm
        )
      }
    }
  }
  callHook(vm, 'beforeMount')

  let updateComponent
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
    updateComponent = () => {
      const name = vm._name
      const id = vm._uid
      const startTag = `vue-perf-start:${id}`
      const endTag = `vue-perf-end:${id}`

      mark(startTag)
      const vnode = vm._render()
      mark(endTag)
      measure(`vue ${name} render`, startTag, endTag)

      mark(startTag)
      vm._update(vnode, hydrating)
      mark(endTag)
      measure(`vue ${name} patch`, startTag, endTag)
    }
  } else {
    //vm._render方法生成虚拟Node，vm._update方法则更新DOM
    updateComponent = () => {
      vm._update(vm._render(), hydrating)
    }
  }

  // we set this to vm._watcher inside the watcher's constructor
  // since the watcher's initial patch may call $forceUpdate (e.g. inside child
  // component's mounted hook), which relies on vm._watcher being already defined
  //实例化渲染watcher，负责视图更新，一个vue实例对应一个渲染watcher，当vm发生变化时触发回调函数
  //Watcher是观察者类，收集依赖项并在变化时触发回调
  new Watcher(vm, updateComponent, noop, {
    before () {
      if (vm._isMounted && !vm._isDestroyed) {
        callHook(vm, 'beforeUpdate')
      }
    }
  }, true /* isRenderWatcher */)
  hydrating = false

  // manually mounted instance, call mounted on self
  // mounted is called for render-created child components in its inserted hook
  //此处vm.$node表示vm的父虚拟节点，值为null说明vm是根实例
  if (vm.$vnode == null) {
    //当实例挂载完成，执行mounted钩子函数
    vm._isMounted = true
    callHook(vm, 'mounted')
  }
  return vm
}
```

1. **`mountComponent` 核心就是实例化一个渲染`Watcher`**，在它的<font color=deepred>回调函数中会调用 `updateComponent` 方法，在此方法中调用 **`vm._render` 方法生成虚拟 Node**，最终调用 **`vm._update` 更新 DOM，将虚拟Node挂载在真实DOM上**</font>。
2. `Watcher` 起到两个作用：
   1. 初始化的时候会执行回调函数
   2. 当 vm 实例中的监测的数据发生变化的时候执行`mountComponent`回调函数；
3. 函数最后判断为根节点的时候设置 `vm._isMounted` 为 `true`， 表示这个实例已经挂载了，同时执行 `mounted` 钩子函数。



#### （3）render

