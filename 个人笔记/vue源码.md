# Vue2源码解读

## 版本

2.6.14



## 地址

github仓库地址：<mark style="background-color: #40E0D0">https://github.com/vuejs/vue.git</mark>，当前默认是2.7.6版本，需切换版本分支。

commit标签：<font color=deepred>**612fb89547711cacb030a3893a0065b785802860 (HEAD -> main, tag: v2.6.14)**</font>

切换指令：`git reset --hard` 版本号，通过`git log`可以查看提交记录。



## 装包

使用npm i安装依赖，建议node版本14.15.5，不建议使用16以上版本。



## 调试

在packjson文件中dev命令尾部添加`--sourcemap`，方便调试查看源码位置。

执行`npm run dev`，查看项目目录下的dist文件夹，内部vue文件夹下生成vue.js.map和vuemin.js则打包成功。

⚠️不要终止终端，开着可以保持热模块更新，即修改源码会自动进行打包，方便调试。



## 目录结构

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



## 初始化过程

入口地址：<mark style="background-color: #40E0D0">/src/core/index.js</mark>

初始化流程图





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



### （1）创建Vue实例(new Vue流程)

<mark style="background-color: #40E0D0">位置：/src/core/instance/index.js</mark>

```javascript
//创建Vue实例的构造函数
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    //调用Vue实例时当前环境不是生产环境且没有没有用new操作符时进入这里
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  // 调用 Vue.prototype._init方法，参数params就是模板中new Vue里面包含的对象
  this._init(options)
}
```

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



#### 方法：Vue.prototype._init

<mark style="background-color: #40E0D0">位置：/src/core/instance/init.js</mark>

```javascript
export function initMixin (Vue: Class<Component>) {

  // 负责 Vue 的初始化过程
  Vue.prototype._init = function (options?: Object) {

    //缓存当前的上下文（vue实例）到vm变量中
    const vm: Component = this

    //设置_uid属性，每个实例都有唯一的_uid。当新建Vue实例时或者渲染组件时就会触发init方法，uid就会递增
    vm._uid = uid++

    //打标记点测试性能
    let startTag, endTag
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      startTag = `vue-perf-start:${vm._uid}`
      endTag = `vue-perf-end:${vm._uid}`
      mark(startTag)
    }

    //在observed函数中会做判断，如果_isVue为true就不会新建observer实例
    vm._isVue = true

    //处理组件配置项
    if (options && options._isComponent) {
      //当前这个Vue实例是子组件时走这里，为vm.$options添加一些属性，以提高代码的执行效率
      initInternalComponent(vm, options)
    } else {
      /**
       * 当前Vue实例不是组件，是实例化的Vue对象时走这里
       * 合并Vue构造函数的全局配置到根组件的局部配置，比如Vue.component注册的全局组件会合并到根的components配置选项中
       * 每个子组件的配置选项合并则发生在两个地方：
       *  1、Vue.component 方法注册的全局组件在注册时做了选项合并
       *  2、{components:{xx}} 方式注册的局部组件在执行编译器生成的render函数时做了选项合并，包括根组件中的components配置
       */
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    
    //设置代理，将vm实例上的属性代理到vm._renderProxy属性上
    //开发环境设置代理主要是起提示作用，开发环境中会提供较多警告帮助解决常见错误，而生产环境不具备此功能
    if (process.env.NODE_ENV !== 'production') {
      //开发环境调initProxy方法
      initProxy(vm)
    } else {
      //生产环境下Vue实例的_renderProxy属性指向vue实例本身
      vm._renderProxy = vm
    }
    vm._self = vm
    //初始化生命周期相关的属性
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm)
    initState(vm)
    initProvide(vm)
    callHook(vm, 'created')

    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      vm._name = formatComponentName(vm, false)
      mark(endTag)
      measure(`vue ${vm._name} init`, startTag, endTag)
    }

    if (vm.$options.el) {
      vm.$mount(vm.$options.el)
    }
  }
}
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



#### 方法：resolveConstructorOptions

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

  

#### 方法：resolveModifiedOptions

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



#### 方法：mergeOptions

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



#### 方法：initProxy

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