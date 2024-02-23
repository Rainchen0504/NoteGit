# 2023年12月5日

## 1、JS对象属性顺序

ECMAScript2020开始，`Object.key`、`for...in`、`Object.getOwnPropertyNames`、`Reflect.ownKeys`都遵循以下规则：

### （1）key为自然数

即key为0或正整数，按照数字大小进行**<font color=deepred>升序排列</font>**

```js
const obj = {
  8: 8,
  1: 1,
  4: 4
}
console.log(Object.keys(obj)) 
//[ '1', '4', '8' ]
```

### （2）key为字符串

按照加入的时间顺序进行排列

```js
const obj = {
    "-1" : -1,
    c :'c',
    a : "a",
    "001": "001"
}
console.log(Object.keys(obj))
// [ '-1', 'c', 'a', '001' ]
```

### （3）key为混合类型

先按照自然数升序排列，然后按照字符串的加入时间排序

```js
const obj = {
  c: 8,
  1: 1,
  b: 4,
};
console.log(Object.keys(obj));
// [ '1', 'c', 'b' ]
```



# 2023年12月13日

## 1、不同系统打开键盘窗口高度







# 2023年12月18日

## 1、请求响应和预览不一致问题

1. 问题现象

   <center>控制台中preview预览和response响应结果不一致</center>

   <img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202312181110279.png" alt="image-20231218111040403" style="zoom:67%;" />

2. 问题原因

   ​	JS的number类型并不能完全表示Long型的数字，在Long长度大于17位时会出现精度丢失的问题，超过17位的部分浏览器会转换为0显示。

3. 解决方法

   1. 修改返回字段长度；
   2. 修改返回数据long类型为String类型，作为字符串处理；



# 2023年12月19日

## 1、移动端调试方法





# 2023年12月25日

## 1、h5 端页面在弹窗时禁止底部页面滚动

https://www.cnblogs.com/cwt981105/p/16898358.html





# 2023年12月26日

## 1、scrollIntoView方法

https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollIntoView

将调用它的元素滚动到浏览器窗口的可见区域

```js
  const targetDom = imageContent.value // 获取dom元素
  targetDom.scrollIntoView(true)	// 调用传参（布尔值，true到顶部，false到底部）
```
