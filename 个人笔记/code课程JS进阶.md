**函数和方法的区别**：

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

 

闭包跟函数最大的区别在于，当捕捉闭包的时候，**自由变量**会在补充时被确定，这样即使脱离了捕捉时的上下文，也能照常运行。

闭包由两部分组成：闭包函数和该函数可以访问的自由变量；



使用new操作符调用一个函数，会执行如下操作：









# 一、JS运行原理

## 1、JS引擎

### （1）浏览器内核和JS引擎的关系

以WebKit举例，Webkit由两部分组成：

WebCore：负责HTMl解析、布局和渲染等工作；

JavaScriptCore：解析、执行Javascript代码；

V8就是另一个强大的JS引擎。



### （2）V8原理



