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

## 1、Object.defineProperty

<font color=deepred>vue基于`Object.defineProperty`方法实现的响应式系统</font>。

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

### （1）前提条件

​	以对象举例处理，不考虑数组的情况。



### （2）定义视图更新函数

​	定义一个cb函数，该函数模拟视图更新，调用就表示视图更新，内部会包含一些更新视图的方法。

```js
function cb (val){
  //渲染视图的方法
 	console.log("视图更新啦");
}
```



### （3）「响应式化」对象属性

​	定义一个`defineReactive`函数，这个方法通过`Object.defineProperty`实现对对象的**响应式化**。参数包括obj（需要绑定的对象）、key（绑定对象的某个属性），val（具体的值）。

经过`defineReactive`处理后，<font color=green>**obj对象的key属性在被读取时就会触发reactiveGetter方法，被修改的时候会触发reactiveSetter方法**</font>。

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



### （4）遍历响应整个对象

​	这里只能对单个属性进行处理，因此需要在外层封装一层`observer`。外层函数传入一个value即需要响应式处理的对象，通过遍历对所有的属性都执行`defineReactive`方法。（真实的情况observer会对传入的对象进行递归调用defineReactive方法）

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



### （5）串联流程举例

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

## 1、依赖收集必要性

- 举例一：

​	创建Vue实例后，如果对data中的参数进行操作修改text3属性的值，但视图中并不展示text3，并不需要触发cb视图更改方法，更新任何值都调用更新视图的方法就显得多此一举。

```js
new Vue({
    template: 
        `<div>
            <span>{{text1}}</span> 
            <span>{{text2}}</span> 
        <div>`,
    data: {
        text1: 'text1',
        text2: 'text2',
        text3: 'text3'
    }
});
```

- 举例二：

如果一个全局对象被多个Vue对象引用并展示，如果修改了这个对象那么o1和o2实例都需要进行视图的更新。

```js
let globalObj = {
  text1: 'text1'
};

let o1 = new Vue({
  template:
  `<div>
  	<span>{{text1}}</span> 
   <div>`,
  data: globalObj
});

let o2 = new Vue({
  template:
  `<div>
  	<span>{{text1}}</span> 
	 <div>`,
  data: globalObj
});
```

这时候就需要「依赖收集」将视图和数据形成一种对应关系，数据更新时根据视图是否展示进行视图更新方法的调用。

![image-20221122175740100](https://raw.githubusercontent.com/Rainchen0504/picture/master/202211221757800.png)



## 2、依赖收集的实现

### （1）订阅者Dep

​	Dep是一个订阅者，对象用于依赖收集，存放Watcher观察者对象。

```js
class Dep {
  constructor() {
    //存放Watcher对象的数组
    this.subs = []
  }
  //在subs中添加一个Watcher对象
  addSub(sub){
    this.subs.push(sub)
  }
  //通知所有的Watcher对象执行更新
  notify(){
    this.subs.forEach((item) => {
      item.update();
    })
  }
}
```



### （2）观察者Watcher

watcher实现了渲染方法`_render`和Dep的关联

```js
class Watcher {
  constructor() {
    Dep.target = this;
  }
  update() {
    console.log("更新视图的方法");
  }
}
Dep.target = null;

```

​	源码中Watcher 其实是在 Vue 初始化的阶段创建的，属于生命周期中 beforeMount 的位置创建的，创建 Watcher 时会执行 render 方法，最终将 Vue 代码渲染成真实的 DOM。



### （3）依赖收集

改造前面的`defineReactive`和Vue的构造函数，完成依赖收集。

```js
class Dep {
  constructor() {
    //存放Watcher对象的数组
    this.subs = [];
  }
  //在subs中添加一个Watcher对象
  addSub(sub) {
    this.subs.push(sub);
  }
  //通知所有Watcher对象执行更新
  notify() {
    this.subs.forEach((item) => {
      item.update();
    });
  }
}

function defineReactive(obj, key, val) {
  const dep = new Dep();
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      dep.addSub(Dep.target);
      return val;
    },
    set: function reactiveSetter(newValue) {
      if (newValue === val) return;
      dep.notify();
    },
  });
}

function observer(value) {
  if (!value || typeof value !== "object") return;
  Object.keys(value).forEach((key) => {
    defineReactive(value, key, value[key]);
  });
}

class Watcher {
  constructor() {
    //new一个Watcher对象的时候，将这个对象赋值给Dep.target
    Dep.target = this;
  }
  update() {
    console.log("更新视图的方法");
  }
}
Dep.target = null;

class Vue {
  constructor(options) {
    this._data = options.data;
    observer(this._data);
    /* 创建Watcher观察者对象 */
    new Watcher();
    /* 在这里模拟render的过程，为了触发name属性的get函数 */
    console.log("render~", this._data.name);
  }
}

let app = new Vue({
  data: {
    name: "zhang",
    age: 25,
  },
}); //打印render~ zhang
app._data.name = "chenge"; //更新视图的方法
```

​	Vue通过`defineProperty`完成了Data中所有数据的代理，为数据中每个属性创建了一个watcher收集对象，当属性被读取时触发`reactiveGetter`函数，会将当前的Watcher对象加入到依赖收集池Dep中；当属性被修改时触发`reactiveSetter`函数，会通知所有使用到这个data的Watcher对象去更新视图。



# 四、响应式原理

创建实例时将data返回的对象属性设置响应式，之后当修改数据时自动触发使用到该属性的函数或方法

```js
//构建订阅者，对每个属性创建dep依赖
class Depend {
  constructor() {
    this.reactiveFns = new Set();
  }
  addDepend(fn) {
    if (fn) {
      this.reactiveFns.add(fn);
    }
  }
  depend() {
    if (reactiveFn) {
      this.reactiveFns.add(reactiveFn);
    }
  }
  notify() {
    this.reactiveFns.forEach((el) => el());
  }
}

//关系函数，保存每个属性和对象和dep之间的关系
const objWeakMap = new WeakMap();
function getDepend(obj, key) {
  let map = objWeakMap.get(obj);
  if (!map) {
    map = new Map();
    objWeakMap.set(obj, map);
  }
  let dep = map.get(key);
  if (!dep) {
    dep = new Depend();
    map.set(key, dep);
  }
  return dep;
}

//需要监听对象的包裹函数
function reactive(obj) {
  //这里分别使用2和3的方法
  //1、2的原理
  Object.keys(obj).forEach((key) => {
    let value = obj[key];
    Object.defineProperty(obj, key, {
      get: function () {
        const dep = getDepend(obj, key);
        dep.depend();
        return value;
      },
      set: function (newValue) {
        value = newValue;
        const dep = getDepend(obj, key);
        dep.notify();
      },
    });
  });
  return obj;

  //2、3的原理
  // const proxyObj = new Proxy(obj, {
  //   get: function (target, key, receiver) {
  //     const dep = getDepend(target, key);
  //     dep.depend(); // 找响应式关系
  //     return Reflect.get(target, key, receiver);
  //   },
  //   set: function (target, key, newValue, receiver) {
  //     Reflect.set(target, key, newValue, receiver);
  //     const dep = getDepend(target, key);
  //     dep.notify();
  //   },
  // });
  // return proxyObj;
}

let reactiveFn = null;
function watchFn(fn) {
  reactiveFn = fn;
  fn();
  reactiveFn = null;
}

//使用响应式包裹的目标对象
const obj = reactive({
  work: "javascript",
  age: 35,
});

//观察函数，表示对这些函数要进行响应式
watchFn(function () {
  console.log(obj.work);
  console.log(obj.age);
});

obj.work = "送外卖";
```



# 五、虚拟DOM中的VNode节点

## 1、VNode

​	VNode 就是一个 JavaScript 对象，用 JavaScript 对象的属性来描述当前节点的一些状态，用 VNode 节点的形式来模拟一棵 虚拟 DOM 树。



## 2、模板到VNode结构

### （1）模板中的元素：

```vue
<template>
	<span class="demo" v-show="isSHow">this is span.</span>
</template>
```



### （2）渲染函数表示：

```js
function render(){
  // h函数就是createVnode函数
  return new VNode(
  	'span',
    {
      directives:[//指令集合数组
        {
          rawName:'v-show',
          expression:'isShow',
          name:'show',
          value:true
        }
      ],
      staticClass:'demo'//静态class
    },
    [ new VNode(undefined, undefined, undefined, 'this, is span.') ]
  )
}
```



### （3）转换成VNode：

```js
{
    tag: 'span',
    data: {
        directives: [//指令集合数组
            {
                rawName: 'v-show',
                expression: 'isShow',
                name: 'show',
                value: true
            }
        ],
        staticClass: 'demo'//class属性
    },
    text: undefined,
    children: [//子节点是一个文本VNode节点
        { 
            tag: undefined,
            data: undefined,
            text: 'This is a span.',
            children: undefined
        }
    ]
}
```



## 3、实现VNode

### （1）创建VNode类

创建一个简单的VNode类，只需描述当前节点信息即可

```js
class VNode {
  constructor(tag, data, children, text, el) {
    this.tag = tag; //标签名
    this.data = data; //节点数据，包括prop或者attrs等
    this.children = children; //子节点，数组形式
    this.text = text; //文本内容
    this.el = el; //对应的真实DOM节点
  }
}
```



结合VNode的结构，对创建VNode的类进行拓展，常用的生成节点方法：

### （2）创建一个空节点函数

```js
function createEmptyVNode () {
    const node = new VNode();
    node.text = '';
    return node;
}
```



### （3）创建一个文本节点函数

```js
function createTextVNode(val){
  return new VNode(undefined, undefined, undefined, String(val))
}
```



### （4）克隆一个VNode节点

```js
function cloneVNode(node){
  const cloneVnode = new VNode(
    node.tag,
    node.data,
    node.children,
    node.text,
    node.elm
  )
  return cloneVnode;
}
```



# 六、模板Compile编译

## 1、Compile

模板的编译可以分为三部分，解析parse阶段、optimize优化语法树阶段、generate将AST语法树转渲染函数阶段。

整个流程以下面的代码举例说明：

```html
<div :class="c" class="demo" v-if="isShow">
  <span v-for="item in sz">{{item}}</span>
</div>
```



## 2、parse

`parse`阶段会利用正则表达式将template分解成由指令、class、style等数据组成的对象，然后形成AST抽象语法树。

```js
{
    /* 标签属性的map，记录了标签上属性 */
    'attrsMap': {
        ':class': 'c',
        'class': 'demo',
        'v-if': 'isShow'
    },
    /* 解析得到的:class */
    'classBinding': 'c',
    /* 标签属性v-if */
    'if': 'isShow',
    /* v-if的条件 */
    'ifConditions': [
        {
            'exp': 'isShow'
        }
    ],
    /* 标签属性class */
    'staticClass': 'demo',
    /* 标签的tag */
    'tag': 'div',
    /* 子标签数组 */
    'children': [
        {
            'attrsMap': {
                'v-for': "item in sz"
            },
            /* for循环的参数 */
            'alias': "item",
            /* for循环的对象 */
            'for': 'sz',
            /* for循环是否已经被处理的标记位 */
            'forProcessed': true,
            'tag': 'span',
            'children': [
                {
                    /* 表达式，_s是一个转字符串的函数 */
                    'expression': '_s(item)',
                    'text': '{{item}}'
                }
            ]
        }
    ]
}
```



### （1）正则

```js
//展示部分会用到的正则
const ncname = '[a-zA-Z_][\\w\\-\\.]*';
const singleAttrIdentifier = /([^\s"'<>/=]+)/
const singleAttrAssign = /(?:=)/
const singleAttrValues = [
  /"([^"]*)"+/.source,
  /'([^']*)'+/.source,
  /([^\s"'=<>`]+)/.source
]
const attribute = new RegExp(
  '^\\s*' + singleAttrIdentifier.source +
  '(?:\\s*(' + singleAttrAssign.source + ')' +
  '\\s*(?:' + singleAttrValues.join('|') + '))?'
)
const qnameCapture = '((?:' + ncname + '\\:)?' + ncname + ')'
const startTagOpen = new RegExp('^<' + qnameCapture)
const startTagClose = /^\s*(\/?)>/
const endTag = new RegExp('^<\\/' + qnameCapture + '[^>]*>')
const defaultTagRE = /\{\{((?:.|\n)+?)\}\}/g
const forAliasRE = /(.*?)\s+(?:in|of)\s+(.*)/
```

### （2）advance

解析template采用循环进行字符串匹配，每匹配解析完一段就需要将已经匹配掉的去掉，头部的指针指向接下来需要匹配的部分。

### （3）parseHTML

循环解析模板template中的字符串，匹配到标签头、标签尾和文本的时候进行不同处理，直到tempalte解析完毕。

### （4）parseStartTag

用 `startTagOpen` 正则得到标签的头部，可以得到标签名，接着使用 `startTagClose` 与 `attribute`正则解析标签以及标签内的属性，直到解析标签的所有属性。

### （5）stack

维护一个stack栈保存已解析好的标签头，这样可以根据在解析尾部标签的时候得到所属的层级关系以及父标签。同时我们定义一个 `currentParent` 变量用来存放当前标签的父标签节点的引用， `root` 变量用来指向根标签节点。

### （6）流程图概览

![image-20221210101517123](https://raw.githubusercontent.com/Rainchen0504/picture/master/202212101309207.png)



## 3、optimize

​	优化抽象语法树，为静态节点添加【标记】（static属性，表示是否是静态节点），在patch时可以跳过标记的节点实现优化的目的。

```js
//经过标记过的VNode
{
    'attrsMap': {
        ':class': 'c',
        'class': 'demo',
        'v-if': 'isShow'
    },
    'classBinding': 'c',
    'if': 'isShow',
    'ifConditions': [
        'exp': 'isShow'
    ],
    'staticClass': 'demo',
    'tag': 'div',
    /* 静态标志 */
    'static': false,
    'children': [
        {
            'attrsMap': {
                'v-for': "item in sz"
            },
            'static': false,
            'alias': "item",
            'for': 'sz',
            'forProcessed': true,
            'tag': 'span',
            'children': [
                {
                    'expression': '_s(item)',
                    'text': '{{item}}',
                    'static': false
                }
            ]
        }
    ]
}
```

### （1）isStatic

传入一个node判断是否是静态节点。判断标准是当节点为表达式节点或者文本节点是就是静态节点，但是如果有if或者for条件时就是非静态节点。

### （2）markStatic

为所有的节点标记上 `static`属性，值就是`isStatic`的结果。

### （3）markStaticRoots

标记静态根，如果当前节点是静态节点，同时满足该节点并不是只有一个文本节点左右子节点时值为true否则为false。

### （4）optimize

调用`markStatic`和`markStaticRoots`函数就实现了标记优化



## 4、generate

将 AST 转化成 render funtion 字符串，最终得到 render 的字符串以及 staticRenderFns 字符串。

真实的Vue编译结果如下：

```js
with(this){
  //其中_c和_l都是函数简写
    return (isShow) ? 
    _c(
        'div',
        {
            staticClass: "demo",
            class: c
        },
        _l(
            (sz),
            function(item){
                return _c('span',[_v(_s(item))])
            }
        )
    )
    : _e()
}
```

# 七、数据更新diff和patch机制

## 1、数据更新视图

​	操作数据时会触发对应`Dep`中的`Watcher`对象。`Watcher`对象会调用对应的`update`修改视图，本质上是产生新的VNode和老的VNode进行一个`patch`的过程。



## 2、跨平台

​	使用虚拟DOM使得Vue具备跨平台的能力。

​	虚拟DOM就是一些JS对象，这些对象依赖一层适配层，将不同平台的API封装在内，以同样的接口对外提供。

```js
//假设有一个nodeOps的适配对象，根据平台的不同执行对应的API，对外则提供了统一的接口供虚拟DOM使用
const nodeOps = {
    setTextContent (text) {
        if (platform === 'weex') {
            node.parentNode.setAttr('value', text);
        } else if (platform === 'web') {
            node.textContent = text;
        }
    },
    parentNode () {
        //......
    },
    removeChild () {
        //......
    },
    nextSibling () {
        //......
    },
    insertBefore () {
        //......
    }
}
```



## 3、patch过程

patch的核心是`diff`算法，使用`diff`算法可以对比出两棵树的「差异」

![image-20230301213832905](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303012138382.png)

diff算法是通过同层的树节点进行比较，而非对树进行逐层搜索遍历，所以时间复杂度只有O(n)，是一种高效的算法。如上图，只有相同的颜色块会进行比较，并将对比得到的差异更新到视图上。

```js
// patch的过程，简化版
function path (oldVnode, vnode, parentElm) {
  if(!oldVnode) {
    // 如果旧节点不存在，相当于新的vnode替代原本没有的节点，直接在父节点上创建
    addVnodes(parentElm, null, vnode, 0, vnode.length - 1)
  } else if (!vnode) {
    // 如果新节点不存在，相当于要把老的节点删除，直接批量删除旧节点
    removeVnodes(parentElm, oldVnode, 0, oldVnode.length - 1)
  } else {
    // 当新旧节点都存在时候，判断是否是相同节点
    if (sameVnode(oldVNode, vnode)) {
      // 相同节点就patchVnode对比节点
      patchVnode(oldVNode, vnode)
    } else {
      // 不相同就删除老节点，增加新节点
      removeVnodes(parentElm, oldVnode, 0, oldVnode.length - 1)
      addVnodes(parentElm, null, vnode, 0, vnode.length - 1)
    }
  }
}
```

下面说一些API，这些API在patch的过程中会被用到，最中都会调用`nodeOps`中的相应函数来操作平台。

### （1）`insert`

`insert`用来在`parent`父节点下插入一个子节点，如果指定了`ref`则插入`ref`这个子节点前面。

```js
function insert (parent, elm, ref) {
  if (parent) {
    if (ref) {
      if (ref.parentNode === parent) {
        nodeOps.insertBefore(parent, elm, ref);
      }
    } else {
      nodeOps.appendChild(parent, elm)
    }
  }
}
```



### （2）`createElm`

`createElm`用来新建一个节点，tag存在创建一个标签节点，否则创建一个文本节点。

```js
function createElm (vnode, parentElm, refElm) {
  if (vnode.tag) {
    insert(parentElm, nodeOps.createElement(vnode.tag), refElm);
  } else {
    insert(parentElm, nodeOps.createTextNode(vnode.text), refElm);
  }
}
```



### （3）`addVnodes`

批量调用`createElm`新建节点

```js
function addVnodes (parentElm, refElm, vnodes, startIdx, endIdx) {
  for(startIdx <= endIdx; ++startIdx) {
    createElm(vnodes[startIdx], parentElm, refElm)
  }
}
```



### （4）`removeNode`

用来移除一个节点

```js
function removeNode (el) {
    const parent = nodeOps.parentNode(el);
    if (parent) {
        nodeOps.removeChild(parent, el);
    }
}
```



### （5）`removeVnodes`

批量调用 `removeNode` 移除节点

```js
function removeVnodes (parentElm, vnodes, startIdx, endIdx) {
  for (startIdx <= endIdx; ++startIdx) {
    const ch = vnodes[startIdx]
    if (ch) {
      removeNode(ch.elm);
    }
  }
}
```



### （6）sameVnode

判断两个节点是否相同，判断的根据是key值，tag(标签)，isCommit(注释),是否input的type一致等等

```js
function sameVnode () {
    return (
        a.key === b.key &&
        a.tag === b.tag &&
        a.isComment === b.isComment &&
        (!!a.data) === (!!b.data) &&
        sameInputType(a, b)
    )
}

function sameInputType (a, b) {
    if (a.tag !== 'input') return true
    let i
    const typeA = (i = a.data) && (i = i.attrs) && i.type
    const typeB = (i = b.data) && (i = i.attrs) && i.type
    return typeA === typeB
}
```



### （7）patchVnode

新旧节点都存在且是同一节点时进行深度比对

```js
function patchVnode (oldVnode, vnode) {
	// 新老节点完全相同时不用做任何改变
  if (oldVnode === vnode) {
    return;
  }
  
	// 当新老节点都是静态的(static)且key相同时，只要把旧节点写到新节点元素和组件实例上即可
  if (vnode.isStatic && oldVnode.isStatic && vnode.key === oldVnode.key) {
    vnode.elm = oldVnode.elm;
    vnode.componentInstance = oldVnode.componentInstance;
    return;
  }
  
	// 获取新老节点的elm ，以及孩子节点集合
  const elm = vnode.elm = oldVnode.elm;
  const oldCh = oldVnode.children;
  const ch = vnode.children;
  
	// 如果新节点是文本节点，直接设置文本
  if (vnode.text) {
    // 其中nodeOps提供了跨平台操作DOM的能力
    nodeOps.setTextContent(elm, vnode.text);
    
  } else {
  // 不是文本节点需要区分几种情况 
    if (oldCh && ch && (oldCh !== ch)) {
      // 如果孩子节点都存在且不相同，调用方法更新子节点
      updateChildren(elm, oldCh, ch);
      
    } else if (ch) {
      // 如果只有新节点存在孩子节点，且旧节点是文本节点则先清除文本然后将新节点的孩子节点批量插入到节点elm下
      if (oldVnode.text) nodeOps.setTextContent(elm, '');
      addVnodes(elm, null, ch, 0, ch.length - 1);
      
    } else if (oldCh) {
      // 如果只有旧节点存在孩子节点，则将旧节点的孩子节点全部清除
      removeVnodes(elm, oldCh, 0, oldCh.length - 1)
      
    } else if (oldVnode.text) {
      // 如果只有旧节点是文本节点，则清除节点文本内容
      nodeOps.setTextContent(elm, '')
    }
  }
}
```



### （8）updateChildren

对比新旧两个孩子节点的方法

```js
function updateChildren(parentElm, oldCh, newCh) {
    // 定义 oldStartIdx、newStartIdx、oldEndIdx、newEndIdx 分别是新老两个 VNode 的两边的索引
    let oldStartIdx = 0;
    let newStartIdx = 0;
 		let newEndIdx = newCh.length - 1;
    let oldEndIdx = oldCh.length - 1;
  
    // 定义 oldStartVnode、newStartVnode、oldEndVnode、newEndVnode 分别指向索引对应的 VNode 节点
    let oldStartVnode = oldCh[0];
    let oldEndVnode = oldCh[oldEndIdx];
    let newStartVnode = newCh[0];
    let newEndVnode = newCh[newEndIdx];
  
    // 定义当前面所有条件都不满足时，才使用的变量，具体后面分析
    let oldKeyToIdx, idxInOld, elmToMove, refElm;
 
  	// 开启循环，新旧节点左右索引逐渐向中间靠拢
    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
        if (!oldStartVnode) {
            // 当oldStartVnode不存在时，更新索引及对应的节点指向
            oldStartVnode = oldCh[++oldStartIdx];
        } else if (!oldEndVnode) {
     				// 当oldEndVnode不存在时，更新索引以及对应的节点指向
            oldEndVnode = oldCh[--oldEndIdx];
        } else if (sameVnode(oldStartVnode, newStartVnode)) {
						// 当新旧节点起始位vNode相同时，调用 patchVnode 并更新索引以及对应的节点指向
            patchVnode(oldStartVnode, newStartVnode);
            oldStartVnode = oldCh[++oldStartIdx];
            newStartVnode = newCh[++newStartIdx];
        } else if (sameVnode(oldEndVnode, newEndVnode)) {
       			// 当新旧节点终止位vNode相同时，调用 patchVnode 并更新索引以及对应的节点指向
            patchVnode(oldEndVnode, newEndVnode);
            oldEndVnode = oldCh[--oldEndIdx];
            newEndVnode = newCh[--newEndIdx];
        } else if (sameVnode(oldStartVnode, newEndVnode)) {
						// 当旧节点起始位和新节点终止位vNode相同时，将旧节点起始位节点插入父节点最后并更新索引以及对应的节点指向
            patchVnode(oldStartVnode, newEndVnode);
            nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm));
            oldStartVnode = oldCh[++oldStartIdx];
            newEndVnode = newCh[--newEndIdx];
        } else if (sameVnode(oldEndVnode, newStartVnode)) {
   					// 当旧节点终止位和新节点起始位vNode相同时，将旧节点起始位节点插入父节点最前并更新索引以及对应的节点指向
            patchVnode(oldEndVnode, newStartVnode);
            nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm);
            oldEndVnode = oldCh[--oldEndIdx];
            newStartVnode = newCh[++newStartIdx];
        } else {
        		// 当以上条件都不满足，调用 createKeyToOldIdx 获取一个 key-索引 的 map 集合
            let elmToMove = oldCh[idxInOld];
            if (!oldKeyToIdx) oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx);
						// 取出对应 key 的节点索引，不存在则为 null
            idxInOld = newStartVnode.key ? oldKeyToIdx[newStartVnode.key] : null;
            if (!idxInOld) {
    						// 索引不存在，直接创建一个新的节点
                createElm(newStartVnode, parentElm);
                newStartVnode = newCh[++newStartIdx];
            } else {
               	// 索引存在
                elmToMove = oldCh[idxInOld];
                if (sameVnode(elmToMove, newStartVnode)) {
     								// 两个节点相同，调用 patchVnode 将老节点集合中对应节点赋值为 undefined ，将节点插入 oldStartVnode 之前，更新对应索引
                    patchVnode(elmToMove, newStartVnode);
                    oldCh[idxInOld] = undefined;
                    nodeOps.insertBefore(parentElm, newStartVnode.elm, oldStartVnode.elm);
                    newStartVnode = newCh[++newStartIdx];
                } else {
             				// 两节点不相同，直接创建新节点插入，更新对应索引
                    createElm(newStartVnode, parentElm);
                    newStartVnode = newCh[++newStartIdx];
                }
            }
        }
    }
    if (oldStartIdx > oldEndIdx) {
     		// 终止条件， oldStartIdx > oldEndIdx 说明 newCh 中还有剩余节点，直接批量添加
        refElm = (newCh[newEndIdx + 1]) ? newCh[newEndIdx + 1].elm : null;
        addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx);
    } else if (newStartIdx > newEndIdx) {
   			// 终止条件， newStartIdx > newEndIdx 说明 oldCh 中还有剩余节点，直接批量删除
        removeVnodes(parentElm, oldCh, oldStartIdx, oldEndIdx);
    }
}
```



#### 8.1、第一步

定义 oldStartIdx、newStartIdx、oldEndIdx、newEndIdx 分别是新老两个 VNode 的两边的索引；

定义 oldStartVnode、newStartVnode、oldEndVnode、newEndVnode 分别指向索引对应的 VNode 节点；

![](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303032152303.png)



#### 8.2、第二步

定义一个循环♻️，该循环中新旧节点所有都向中间靠拢

```js
while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) 
```

![image-20230303215950139](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303032159218.png)

当旧节点开始和结束位置不存在的时候，旧节点开始和结束位索引继续向中间靠拢，更新对应的开始和结束位节点指向。

```js
if (!oldStartVnode) {
    oldStartVnode = oldCh[++oldStartIdx];
} else if (!oldEndVnode) {
    oldEndVnode = oldCh[--oldEndIdx];
}
```



#### 8.3、第三步

接下来是新旧节点开始和结束位两两比较的过程，共有4种情况

##### （1）新旧节点起始位符合sameVnode

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303032240243.png" alt="image-20230303224044068" style="zoom:50%;" />

说明老节点头部和新节点头部是相同节点，直接进行`patchVnode`，同时新旧节点起始位索引分别向后移动一位。



##### （2）新旧节点结束位符合sameVnode

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303032244007.png" alt="image-20230303224403420" style="zoom:50%;" />

说明老节点尾部和新节点尾部是相同节点，直接进行`patchVnode`，同时新旧节点结束位索引分别向前移动一位。



##### （3）旧节点起始位和新节点结束位符合sameVnode

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303032247039.png" alt="image-20230303224703777" style="zoom:50%;" />

说明旧节点头部和新节点尾部是同一节点，将旧节点头部直接移动到旧节点尾部，然后旧节点起始位索引向后移动一位，新节点结束位向前移动一位。



##### （4）旧节点结束位和新节点起始位符合sameVnode

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303032252250.png" alt="image-20230303225159789" style="zoom:50%;" />

说明旧节点尾部和新节点头部是同一节点，将旧节点尾部直接移动到旧节点头部，然后新节点起始位索引向后移动一位，旧节点结束位向前移动一位。



#### 8.4、第四步

以上条件都不符合时，首先会使用`createKeyToOldIdx`方法产生`key`和`index`索引对应的一个map表

```js
// createKeyToOldIdx函数的作用
let before = [
  {xx: xx, key:'key0'},
  {xx: xx, key:'key1'},
  {xx: xx, key:'key2'}
]
// 经过函数转换后
let after = {
  key0: 0,
  key1: 1,
  key2 :2
}
```

这样可以根据某一个 key 的值，快速地从 `oldKeyToIdx`（`createKeyToOldIdx` 的返回值）中获取相同 key 的节点的索引 `idxInOld`然后找到相同的节点。

- 如果没找到就创建新节点

```js
if (!idxInOld) {
    createElm(newStartVnode, parentElm);
    newStartVnode = newCh[++newStartIdx];
}
```

- 如果找到了就进行节点比对
  - 同时它符合 `sameVnode`，则将这两个节点进行 `patchVnode`，将该位置的老节点赋值 undefined（之后如果还有新节点与该节点key相同可以检测出来提示已有重复的 key ），同时将 `newStartVnode.elm` 插入到 `oldStartVnode.elm` 的前面。同理，`newStartIdx` 往后移动一位。<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303040119770.png" alt="image-20230304011915513" style="zoom:50%;" />
  - 不符合`sameVnode`，就创建一个新节点插入到`parentElm`的子节点中，`newStartIdx` 往后移动一位<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303040122346.png" alt="image-20230304012243088" style="zoom:50%;" />



#### 8.5、第五步

当`while`循环结束之后，新旧节点对比完成，如果新节点还有多的，就将新节点插入到真实DOM中

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303040124691.png" alt="image-20230304012436501" style="zoom:50%;" />

当旧节点还有多的，就将旧节点批量删除

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202303040126508.png" alt="image-20230304012556518" style="zoom:50%;" />



## 4、全流程图

![patch函数调用 patch(oldVnode,Vnode,parentElm)](https://raw.githubusercontent.com/Rainchen0504/picture/master/202303040158743.png)
