### 1、函数和方法的区别：

函数function：独立的function，称之为一个函数

```javascript
function foo(){}
```

方法method：当一个函数属于一个对象时，这个函数就是这个对象的方法

```javascript
var obj = {
  foo:function(){}
}
obj.foo()
```

 

### 2、DOM和BOM

DOM文档对象模型：页面所有的内容表示为可修改的对象；

BOM浏览器对象模型：由浏览器提供的用于处理文档之外所有内容的其他对象；

获取元素目前最常用的是querySelector和querySelectAll。



### 3、元素大小和滚动

- clientWidth:contentWidth+padding(不包含滚动条)
- clientHeight:contentHeight+padding
- clientTop:border-top的宽度
- clientLeft:border-left的宽度
- offsetWidth:元素完整的宽度
- offsetHeight:元素完整的高度
- offsetLeft:距离父元素的x
- offsetHeight:距离父元素的y
- scrollHeight:整个可滚动的区域高度
- scrollTop:滚动部分的高度

![image-20220907105827244](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209071058428.png)



### 4、盒模型

#### IE盒模型

width包含border + padding + content

![image-20220905165251190](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209051652435.png)

#### W3C标准盒模型

width包含 content

![image-20220905165238842](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209051652011.png)



### 5、BOM

**BOM主要包含以下对象模型：window、location、history、navigator、screen**

#### （1）location对象

常见属性：

- href: 当前window对应的超链接URL, 整个URL；
- protocol: 当前的协议；
- host: 主机地址；
- hostname: 主机地址(不带端口)；
- port: 端口；
- pathname: 路径；
- search: 查询字符串；
- hash: 哈希值；

常见方法：
 assign:赋值一个新的URL，并且跳转到该URL中；

replace:打开一个新的URL，并且跳转到该URL中(不同的是不会在浏览记录中留下之前的记录)；

reload:重新加载页面，可以传入一个Boolean类型；



#### （2）history对象

常见属性：

- length:会话中的记录条数；
- state:当前保留的状态值；

常见方法：

 back():返回上一页，等价于history.go(-1)；

forward():前进下一页，等价于history.go(1)；

 go():加载历史中的某一页；

pushState():打开一个指定的地址；

replaceState():打开一个新的地址，并且使用replace；



### 6、Storage

**localStorage**：本地存储，提供永久性的存储方法，关闭网页重新打开存储内容依旧保留；

**sessionStorage**：会话存储，提供本次会话的存储，关闭网页就会被清除；



### 7、this绑定规则

#### （1）默认绑定

​	默认绑定全局对象window上



#### （2）隐式绑定

​	通过对象调用内部方法，该方法的this指向发起调用的对象



#### （3）显示绑定

​	指向通过apply和call设置的对象，call和apply方法第一个参数要求传入一个对象，即this绑定的对象。第二个参数apply是个数组，call是参数列表。

```javascript
function foo(name, age, height) {
  console.log("foo函数被调用:", this)
  console.log("打印参数:", name, age, height)
}
//apply，第一个参数是对象，第二个参数是数组
foo.apply(object, ["kobe", 30, 1.98]);

//call，第一个参数是对象，第二个参数是参数列表
foo.call("call", "james", 25, 2.05);
```

如果希望方法始终绑定到obj对象上，但是又不希望对象本身身上有函数方法，使用bind创建一个新的绑定函数

```javascript
function foo(name, age, height, address) {
  console.log("foo:", this)
  console.log("参数:", name, age, height, address)
}
var obj = { name: "why" }
var bar = foo.bind(obj)
bar() // this -> obj
```



#### （4）new绑定

new一个对象的过程：

1、创建一个新对象；

2、将this指向绑定到新创建的对象上；

3、执行构造函数的方法，给新对象添加属性；

4、返回新对象；



### 8、this绑定规则优先级

<FONT COLOR=DEEPRED>默认绑定优先级最低，显示绑定高于隐式绑定，new绑定高于隐式绑定，new绑定优先级高于bind。</font>

注意⚠️：

如果显示绑定null或undefined，则会使用默认规则；

独立函数调用也是默认绑定；



### 9、箭头函数

不会绑定this、arguments属性，不能作为构造函数来使用（不能使用new关键字）



### 10、浏览器内核

Trident :IE、360安全浏览器、搜狗高速浏览器、百度浏览器、UC浏览器；

Webkit -> Blink :Google Chrome，Edge；

Gecko :Mozilla Firefox；

浏览器内核就是**<FONT COLOR=DEEPRED>浏览器排版引擎</FONT>**



### 11、script和页面关系

​	**<font color=blue>解析HTML遇到script元素会停止构建DOM树</font>**，因为JS会操作DOM，等到DOM树构建完成并且渲染再执行JavaScript，会造成严重的回流和重绘，影响页面的性能；

为了解决JS脚本加载时间长白屏问题script提供两个属性defer和async：

#### （1）defer

​	**浏览器不等待脚本下载**，继续解析HTML，构建DOM Tree；如果脚本提前下载好了，它会等待DOM Tree构建完成，在DOMContentLoaded事件之前先执行defer中的代码；

特点：<font color=blue>可以保证执行顺序；可以提升页面性能，推荐放到head中；仅使用外部脚本</font>；

#### （2）async

​	**让脚本不阻塞页面**，与defer类似。

特点：<font color=blue>不能保证顺序，属于独立下载、独立运行，不会等待其他脚本；不能保证在DOMContentLoaded之前或者之后执行</font>；

#### （3）应用场景

1. defer常用于需要在文档解析后操作DOM的JS代码，并对script文件有要求；
2. async常用于独立脚本，对DOM没有依赖；



### 12、输入URL到页面展示过程

![](https://raw.githubusercontent.com/Rainchen0504/picture/master/202209071403000.png)

#### （1）URL加载总结

1. 浏览器进程收到URL，转发给网络进程；
2. 网络进程发起真正URL请求；
3. 网络进程收到响应头数据，转发给浏览器进程；
4. 浏览器接受响应头数据后发送“提交导航”给渲染进程；
5. 渲染进程和网络进程建立数据管道，接收HTML、CSS数据；
6. 渲染进程向浏览器进程发送“确认提交”准备接受和解析数据；
7. 浏览器进程收到渲染进程“提交”信息后，移除旧数据更新页面状态；



#### （2）渲染流程总结

1. 渲染进程把HTML内容转换成DOM树结构；
2. 渲染进程把CSS转换成styleSheet，计算DOM节点样式；
3. 创建布局树，计算绑定布局样式信息；
4. 对布局树分层，创建分层树；
5. 对每个图层生成绘制表，交给合成线程；
6. 合成线程把图层分成图块，在光栅化线程池中转换成位图；
7. 合成线程发送绘制指令给浏览器进程；
8. 浏览器进程根据指令生成页面，显示在显示器上；



#### （3）HTTP请求总结

- 浏览器发起请求
  - 构建请求行信息；
  - 从浏览器缓存中查找请求文件，如果存在拦截请求，返回缓存资源，请求结束；
  - DNS解析，将域名映射为IP地址；
  - 等待TCP队列，一个域名最多建立6个TCP连接，超过需等待；
  - 建立TCP连接
  - 发送HTTP请求，请求数据格式包含请求行(请求方法+请求URL+协议版本)、请求头和请求体；
- 服务器处理请求
  - 返回响应数据，响应数据格式包含响应行(协议版本+状态码)、响应头和响应体；
  - 断开TCP连接；
  - 是否进行重定向，重定向重新执行请求流程；



### 13、JS引擎

浏览器内核由两部分组成：（以webkit为例）

- WebCore：负责HTML解析、布局、渲染等工作；
- JavaScriptCore：解析、执行JavaScript；

