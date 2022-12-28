# 一、小程序概览

## 1、小程序说明

​	小程序是一种<font color=deepred>不需要下载安装</font>即可使用的应用，它实现了“触手可及”的梦想，使用起来方便快捷，用完即走。



## 2、小程序优势

- 优点：
  - 体验比传统H5页面要好很多；
  -  相当于传统的App，使用起来更加方便，不需要在应用商店中下载安装，甚至注册登录等麻烦的操作；
  - 小程序可以间接的动态为添加新功能；



## 3、开发模式和技术选型

小程序是**<font color=blue>介于原生App和手机H5页面之间</font>**的一个产品定位。

### （1）原生小程序开发

#### 微信小程序

- 主要技术包括：WXML、WXSS、JavaScript

#### 支付宝小程序

- 主要技术包括：AXML、ACSS、JavaScript



### （2）框架开发小程序

#### mpvue

使用vue开发的框架，18年后不维护，已废弃

#### wepy

由腾讯开源的一款让小程序支持组件化开发的框架，前两年用的挺多，不推荐

#### uni-app

由DCloud团队开发和维护的使用 Vue 开发所有前端应用的框架，<font color=red>目前是很多公司的技术选型</font>

#### taro

由京东团队开发和维护的开放式跨端跨框架解决方案，支持多框架开发

#### 开发原生APP

产品使用体验整体相较于原生App差很多，其他的技术选项来开发原生App：ReactNative、Flutter；



## 4、技术核心

小程序的核心技术主要是三个：

- 页面布局：WXML，类似HTML
- 页面样式：WXSS，类似于CSS
- 页面脚本：JavaScript+WXS



## 5、项目结构

![image-20221204222531555](https://raw.githubusercontent.com/Rainchen0504/picture/master/202212042225319.png)

参考微信官方开发文档，下面只挑选说明

### pages文件

该文件夹中放置的是一个个页面，每个页面文件夹包含四个文件：

#### 1.1、wxml

wxml中可以使双大括号语法从js文件中获取变量，使用事件绑定方法从js中绑定事件；

#### 1.2、wxss

写样式的文件，给类写样式在wxml文件中使用class=XX绑定样式

#### 1.3、json

写一些静态变量和属性配置

#### 1.4、js

使用`Page({})`写法和vue中的选项式API类似

```js
Page({
	//页面的初始数据
  data: {},
	//生命周期函数--监听页面加载
  onLoad(options) {},
  //命周期函数--监听页面初次渲染完成
  onReady() {},
  //周期函数--监听页面显示
  onShow() {},
  //命周期函数--监听页面隐藏
  onHide() {},
  //周期函数--监听页面卸载
  onUnload() {},
  //相关事件处理函数--监听用户下拉动作
  onPullDownRefresh() {},
  //上拉触底事件的处理函数
  onReachBottom() {},
  //点击右上角分享
  onShareAppMessage() {}
})
```



## 6、小程序架构

 Vue的MVVM和小程序MVVM对比

![image-20221204224219735](https://raw.githubusercontent.com/Rainchen0504/picture/master/202212042242799.png)

DOM Listeners: ViewModel层可以将DOM的监听绑定到Model层

Data Bindings: ViewModel层可以将数据的变量, 响应式的反应到View层



## 7、架构模型

小程序采用了<font color=deepred>**双线程**模型架构</font>：

- WXML模块和WXSS样式运行于渲染层，渲染层使用WebView线程渲染（一个程序有多个页面会使用多个WebView线程）；
- JS脚本运行于逻辑层，逻辑层使用JsCore运行JS脚本；
- 这两个线程都会经由微信客户端（Native）进行中转交互；

![image-20221209175558414](https://raw.githubusercontent.com/Rainchen0504/picture/master/202212091755739.png)



## 8、配置文件

小程序很多开发需求都规定在配置文件中，常见的配置文件有

- `project.config.json`：项目配置文件, 比如项目名称、appid等
- `sitemap.json`：小程序搜索相关的
- `app.json`：全局配置
- `paeg.json`：页面配置



### 全局app配置文件app.json

| 属性   | 类型      | 必填 | 描述               |
| ------ | --------- | ---- | ------------------ |
| pages  | stringp[] | 是   | 页面路径列表       |
| window | Object    | 否   | 全局的默认窗口表现 |
| tabBar | Object    | 否   | 底部的tab栏的表现  |

pages：页面路径列表，用于指定小程序由哪些页面组成，所有的页面都必须在pages中注册；

window：全局默认窗口展示，包括很多配置属性；

tabBar：底部的tab是个数组；



### 页面配置文件page.json

​	每个页面也可以使用`.json`文件对当前窗口进行配置，页面中配置项在当前页面会覆盖`app.json`的` window`中相同的配置项。



## 9、注册小程序App函数

`App()`实例只有一个，是全局共享的（单例对象）

- 可以放置共享对象，但是该数据是非响应式的；

  ```js
  //全局app.js文件
  App({
    //共享数据，非响应式的静态数据
    globalData:{
      token:"",
      userInfo:{}
    },
  })
  
  //其他页面
  onLoad(){
    const app = getApp()
    console.log(app.globalData.token)
  }
  ```

- 全局共享的生命周期函数；

  ```js
  App({
    onLaunch(){},//监听小程序初始化
    onShow(){},//监听小程序启动或切前台
    onHide(){},//监听小程序切后台
    onError(){}，//错误监听函数
    onPageNotFound(){},//页面不存在监听函数
    onUnhandledRejection(){},//未处理的Promise拒绝事件监听函数
    onThemeChange(){}//监听系统主题变化
  })
  ```



## 10、注册页面Page函数

​	小程序中每个页面都有一个对应的js文件，调用`Page`函数注册页面实例。注册时可以绑定初始化数据、生命周期回调、事件处理函数等。

注册Page需要做的内容：

1. 在生命周期函数中发送网络请求，从服务器中获取数据；
2. 初始化一些数据，方便被wxml引用展示；
3. 监听wxml中的事件，绑定对应的事件函数；
4. 设置其他监听（包括页面滚动、上拉刷新、下拉加载等）；

```js
Page({
    //页面的初始数据
    data: {},
    //监听页面加载
    onLoad(options) {},
    //监听页面初次渲染完成
    onReady() {},
    //监听页面显示
    onShow() {},
    //监听页面隐藏
    onHide() {},
    //监听页面卸载
    onUnload() {},
    //监听用户下拉动作
    onPullDownRefresh() {},
    //页面上拉触底事件的处理函数
    onReachBottom() {},
    //用户点击右上角分享
    onShareAppMessage() {}
})
```



## 11、text组件

显示文本，<font color=deepred>类似于`span`标签，是行内元素</font>。

- `user-select`属性设置是否可以让用户选中；
- `decode`是否解码，可以解析的有` &nbsp; &lt; &gt; &amp; &apos; &ensp; &emsp;`



## 12、Button组件

创建按钮，默认块级元素

- size：按钮大小；
- type：样式类型；
- plain：是否镂空；
- disabled：是否禁用；
- loading：名称前面是否带loading图标；
- form-type：用于form组件，点击分别触发form的submit/reset事件；
- open-type：微信开放能力，包括打开客服会话、用户转发共享、打开APP、授权等功能；
- hover-class：指定按钮按下去；



## 13、view组件

视图组件，块级元素独占一行，通常作为容器组件

- hover-class：指定按下去的样式类；
- Hover-stop-propagation：指定是否阻止本节点的祖先节点出现点击态；
- hover-start-time：按住后多久出现点击态，ms；
- hover-stay-time：手指松开后点击态保留时间，ms；



## 14、Image组件

图片组件，用于显示图片

- src：图片资源地址，可以是本地图片也可以是网络图片；
- mode：图片剪裁、缩放的模式；
- webp：默认不解析webp格式，只支持网络资源；
- lazy-load：图片懒加载，进入一定范围才加载；
- show-menu-by-longpress：长按图片显示发送给朋友、收藏等等；
- binderror：当错误发生时触发；
- bindload：当图片载入完毕时触发；

⚠️注意：image组件<font color=deepred>默认宽度时320px、高度240px</font>



## 15、scroll-view组件

实现局部滚动必须添加scroll-x或者scroll-y属性，垂直方向滚动必须给scroll-view设置高度。

- scroll-x：允许横向滚动；
- scroll-y：允许纵向滚动；
- bindscrolltoupper：滚动到顶部/左侧时触发；
- bindscrolltolower：滚动到底部/右侧时触发；
- bindscroll：滚动时触发；



## 16、样式WXSS

### （1）写法

页面样式三种写法：包括行内样式、页面样式、全局样式

优先级依次是：行内样式 > 页面样式 > 全局样式



### （2）权重

WXSS优先级与CSS类似

![image-20221228193525208](https://raw.githubusercontent.com/Rainchen0504/picture/master/202212281935475.png)



### （3）尺寸单位

<font color=red>rpx</font>可以根据屏幕宽度进行自适应，规定屏幕宽度为750rpx。

设计稿一般用IPhone6作为<font color=deepred>视觉稿</font>的标准。IPhone6屏幕宽度为375px，共有750个物理像素，则750rpx=375px=750物理像素，此时1rpx=0.5px=1物理像素。



## 17、结构WXML

### （1）Mustache语法

WXML类似于HTML，可以使用单标签或双标签，必须有严格的闭合，并且大小写敏感。小程序提供和Vue一样的插值语法（双大括号语法）



### （2）显示隐藏

使用wx:if、wx:elif、wx:else实现逻辑判断，控制显示隐藏；

hidden属性是所有组件都默认拥有的属性，控制显示隐藏；

<font color=deepred>**hidden和wx:if的区别**</font>：

- hidden控制是否添加hidden实现显示隐藏，类似display:none的设置；
- wx:if通过控制组件是否渲染实现显示隐藏；



### （3）列表渲染

#### 3.1、wx:for

使用wx:for遍历字符串、数字或者数组实现列表渲染。

<font color=deepred>默认情况下</font>变量item表示某项，变量index表示数据下标。

```html
<view wx:for="{{10}}" wx:for="*this">
	<view>{{item}}---{{index}}</view>
</view>
```



#### 3.2、item/index名称

默认情况下，item和index的名字是固定的，但是也可以指定item和index的名称

```html
<view>
	<block wx:for="{{books}}"
         wx:key="id"
         wx:for-item="book"
         wx:for-index="num"
         >
  		<view>{{book.name}}---{{num}}</view>
  </block>
</view>
```



#### 3.3、key的作用

​	小程序内部也使用了<font color=deepred>虚拟DOM</font>（和Vue、React类似），当列表节点有很多时，为了更好的复用节点和高效的插入、删除新的节点，需要使用key这个属性。

wx:key的值以两种形式提供：

- <font color=pink>字符串</font>，表示在 for 循环的 array 中 item 的某个 property，该 property 的值需要是列表中唯一的字符串或数字，且不能

  动态改变；

- <font color=pink>保留关键字*this</font>，表示在 for 循环中的 item 本身，这种表示需要 item 本身是一个唯一的字符串或者数字；



### （4）block标签

`<block/> `并不是一个组件，它仅仅是一个包装元素，不会在页面中做任何渲染，只接受控制属性。

- block的常见用法：
  - 将需要进行遍历或者判断的内容进行包裹；
  - 将遍历和判断的属性放在block标签中，不影响普通属性的阅读，提高代码的可读性；

