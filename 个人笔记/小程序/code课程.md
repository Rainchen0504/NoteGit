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



## 6、小程序架构

 Vue的MVVM和小程序MVVM对比

![image-20221204224219735](https://raw.githubusercontent.com/Rainchen0504/picture/master/202212042242799.png)

DOM Listeners: ViewModel层可以将DOM的监听绑定到Model层

Data Bindings: ViewModel层可以将数据的变量, 响应式的反应到View层



## 7、语法

参考微信官方开发文档，下面只挑选说明

### （1）pages文件

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

