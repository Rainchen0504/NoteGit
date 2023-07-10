## 1、CSS3

1. 增加媒体查询；
2. 增加弹性布局flex；
3. 增加边框如 border-radius，box-shadow 等；
4. 增加背景如 background-size，background-origin 等；
5. 增加动画 2D，3D 转换如 transform、animation 等；
6. 增加阴影 text-shadow；
7. 增加渐变 linear-gradient；



## 2、绘制三角形

```css
.trangle {
  width: 0px;
  height: 0px;
  border-top: 10px solid red;
  border-right: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-left: 10px solid transparent;
}
```



## 3、绘制1px物理像素

现象：CSS设置1px，但是移动端显示却有些粗；

- 物理像素：也叫设备像素，一个物理像素是显示器上最小的物理显示单元。在操作系统的调度下，每一个物理像素都有自己的颜色值和高亮，IPhone6上有750*1334个物理像素颗粒；

- 逻辑像素：设备独立像素或密度像素，由程序使用的虚拟像素，CSS像素，然后由系统转换为物理像素；

- 设备像素比：物理像素和逻辑像素的关系

  ```js
  设备像素比dpr = 物理像素 / 逻辑像素
  ```

​	由于手机屏幕分辨率越来越高，同样屏幕大小的一个手机，它的**实际物理像素数更多**了。因为<font color=pink>不同的移动设备有不同的像素密度</font>，所以写的1px在不同的移动设备上展示是不一样的。

### （1）使用box-shadow

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .box {
        width: 100px;
        height: 100px;
        margin: 50px auto;
        background-color: antiquewhite;
        box-shadow: 0 1px 0 0 rgba(0, 0, 0, 1);
      }
    </style>
  </head>
  <body>
    <div class="box"></div>
  </body>
</html>
```



### （2）设置border-image

利用1px宽度图片做border图片

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .box {
        width: 100px;
        height: 100px;
        margin: 50px auto;
        background-color: antiquewhite;
      }
      .border-image-1px {
        border-bottom: 1px solid transparent;
        border-image: url(./xnip.jpg) 30 stretch;
      }
    </style>
  </head>
  <body>
    <div class="box border-image-1px"></div>
  </body>
</html>
```



### （3）background-iamge实现

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>实现1px的解决办法</title>
    <style>
      .box {
        width: 100px;
        height: 100px;
        margin: 50px auto;
        background-color: antiquewhite;
      }
      .background-image-1px {
        background: url(border.png) repeat-x left bottom;
        -webkit-background-size: 100% 1px;
        background-size: 100% 1px;
        background-color: antiquewhite;
      }
    </style>
  </head>
  <body>
    <div class="box background-image-1px"></div>
  </body>
</html>
```



### （4）多背景渐变实现

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>实现1px的解决办法</title>
    <style>
      .background-gradient-1px {
        background: linear-gradient(#000, #000 100%, transparent 100%) left /
            1px 100% no-repeat,
          linear-gradient(#000, #000 100%, transparent 100%) right / 1px 100%
            no-repeat,
          linear-gradient(#000, #000 100%, transparent 100%) top / 100% 1px
            no-repeat,
          linear-gradient(#000, #000 100%, transparent 100%) bottom / 100% 1px
            no-repeat;
      }
      .box {
        width: 100px;
        height: 100px;
        margin: 50px auto;
        background-color: antiquewhite;
      }
    </style>
  </head>
  <body>
    <div class="box background-gradient-1px"></div>
  </body>
</html>
```



### （5）viewport + rem实现

首先在viewport设置缩放，通过JS去动态修改viewport的值

```html
<meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no" />
```

通过JS来修改viewport的值：

```js
var 
```



### （6）伪类+transfrom实现

### 





### （1）viewport方式

```html
<meta name="viewport" content="width=device-width,initial-scale=0.5,minimum-scale=0.5,maximum-scale=0.5" />
```



### （2）transform缩放

```css
tarnsform: scale(0.5, 0.5)
```



### （3）box-shadow阴影

设置box-shadow的第二个参数为0.5px，表示阴影垂直方向的偏移为0.5px

```css
height: 1px;
background: none;
box-shadow: 0 0.5px 0 #000
```



## 4、link和@import

### （1）作用

- link是 HTML 提供的标签，不仅可以加载CSS文件，还可以定义rel连接属性；
- @import是CSS提供的语法规则，只有导入样式表的作用；



### （2）加载时机

- link标签引入的CSS在加载页面时被同时加载；
- @import引入的CSS将在页面加载完毕后被加载；



### （3）兼容性

- link标签作为HTML元素，不存在兼容性问题；
- @import试CSS2.1才有的语法，只兼容IE5以上浏览器；



### （4）权重

`link`引入的样式权重大于`@import`引入的样式；



## 5、transition和animation

### （1）transition

该属性是一个简写属性，表示过渡、转变的意思。用于设置四个过渡属性。

- transition-property：设置过渡效果的属性名称（默认值是all）；
- transition-duration：设置过渡完成所需要的时间（默认值是0）；
- transition-timing-function：设置过渡速度效果曲线（默认值是ease）；
- transition-delay：设置过渡的开始时间（默认值是0）；



### （2）animation

该属性是一个简写属性，通过关键帧实现动画效果。用于设置六个动画属性。

- animation-name：设置绑定到选择器的@keyframem名称（默认值是none）；
- animation-duration：设置完成动画所花费的时间（默认值是0）；
- animation-timing-function：设置动画的速度曲线（默认值是ease）；
- animation-delay：设置动画延迟几秒开始（默认值是0）；
- animation-iteration-count：设置动画播放的次数（默认值是1）；
- animation-direction：设置时候轮流反向播放动画（默认值是normal）；



### （3）区别

- transition是一个**过渡效果**，<font color=pink>没有中间状态</font>，需要设置触发事件才能执行；
- animation是一个**动画效果**，<font color=pink>有多个关键帧</font>，可以在任意一个中间帧设置状态，不需要设置触发事件就能执行；



## 6、flex布局

弹性布局用来为盒模型提供最大的灵活性

### （1）容器属性

```css
/* 主轴方向 */
flex-direction: row | row-reverse | column | column-reverse;
/* 换行 */
flex-wrap: nowrap | wrap | wrap-reverse;
/* direction和wrap的简写 */
flex-flow: <flex-direction> || <flex-wrap>
/* 水平对齐 */
justify-content: flex-start | flex-end | center | space-between | space-around;
/* 垂直对齐 */
align-items: flex-start | flex-end | center | baseline | stretch;
/* 多轴线对齐方式 */
align-content: flex-start | flex-end | center | space-between | space-around | stretch;
```



### （2）项目属性

```css
/* 项目排列顺序，数值越小，排列越靠前，默认为0 */
order: <integer>
/* 放大比例 */
flex-grow: <number>
/* 缩小比例 */
flex-shrink: <number>
/* flex-grow、flex-shrink和flex-basis简写，默认值为0 1 auto，后两个属性可选填 */
flex: none | [flex-grow flex-shrink flex-basis]
```



## 7、垂直居中方法

### （1）margin:auto

```css
.parent {
    position: relative;
    width: 500px;
    height: 400px;
    background: blue;
}
.child {
    background: red;
    width: 50px;
    height: 50px;
    position: absolute;
    top: 0px;
    left: 0px;
    right: 0px;
    bottom: 0px;
    margin: auto;
}
```



### （2）margin负值+translate偏移

```css
.parent {
    width: 500px;
    height: 400px;
    position: relative;
    background: pink;
}
.child {
    width: 180px;
    height: 80px;
    background-color: #746;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```



### （3）利用flex

```css
.parent {
    width: 500px;
    height: 400px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: pink;
}
.child {
    width: 50px;
    height: 50px;
    background: red;
}
```



### （4）利用line-height

```css
.parent {
    width: 500px;
    height: 400px;
    border: 1px solid #546461;
    font-size: 0px;
    text-align: center;
    line-height: 400px;
}
.child {
    display: inline-block;
    width: 100px;
    height: 50px;
    font-size: 16px;
    background-color: red;
    vertical-align: middle;
}
```



## 8、JS动画和CSS3动画

### （1）JS动画

- 优点
  - Javascript动画控制力很强，可以在动画播放过程中对动画进行控制：开始、暂停、回放、终止、取消都是可以做到的；
  - 动画效果比CSS3动画丰富，有些动画效果比如曲线运动,冲击闪烁,视差滚动效果，只有JavaScript动画才能完成；
  - CSS3有兼容性问题，而JS大多时候没有兼容性问题；

- 缺点

  - JS是单线程的脚本语言，当JS在浏览器主线程运行时，主线程还有其它需要运行的JS脚本、样式、计算、布局、交互等一系列任务，对其他干扰线程可能出现阻塞，造成丢帧情况；
  - JS在做动画时，其复杂度是高于CCS3的，需要考虑一些计算、操作等问题；

  

### （2）CSS3动画

- 优点
  - 浏览器可以对动画进行优化；
  - 代码相对简单，性能调优方向固定；
  - 对于帧数表现不好的低版本浏览器，CSS3可以做到自然降级，而JS则需要撰写额外代码；

- 缺点
  - 代码冗余，实现复杂动画代码变得非常笨重；
  - 运行过程控制较弱，无法附加时间绑定回调函数；CSS动画只能暂停,不能在动画中寻找一个特定的时间点，不能在半路反转动画，不能变换时间尺度，不能在特定的位置添加回调函数或是绑定回放事件，无进度报告；



## 9、多行文本省略

```css
display: -webkit-box;
overflow: hidden;
width: 100px;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
```



## 10、元素隐藏

### （1）display:none

把元素隐藏起来，**会改变页面布局**，相当于删除元素；



### （2）opacity:0

把元素隐藏起来，**不会改变页面布局**，如果元素绑定了事件，那么点击该区域也能<font color=pink>触发</font>点击<font color=pink>事件</font>；



### （3）visibility:hidden

该元素隐藏起来，**不会改变页面布局**，<font color=pink>不会触发该元素已经绑定的事件</font>；



## 11、双边距重叠

多个相邻（兄弟或者父子关系）普通流的块元素垂直方向margin会重叠；

折叠结果为：

1. 两个相邻的外边距都是正数时，折叠结果是它们两者之间较大值；
2. 两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值；
3. 两个边距一正一负时，折叠结果是两者相加的和；



## 12、样式引入link和@import

### （1）link

link属于HTML标签，当页面加载时，link会被同时加载；

link不存在兼容问题，权重高于@import；



### （2）@import

@import是CSS提供的，@import引用的CSS会等到页面被加载完成再加载；

@import只能在IE5以上浏览器识别，@imoort权重低于link；



## 13、像素单位

| 单位 | 长度                     | 默认值                    | 使用场景       |
| ---- | ------------------------ | ------------------------- | -------------- |
| px   | 固定像素                 | 16px                      | 常用长度单位   |
| em   | 相对父元素字体大小       | 未经调整符合1em=16px      | 适配移动端长度 |
| rem  | 相对根元素字体大小       | 遵循html元素字体大小      | 适配移动端长度 |
| rpx  | 微信小程序独有自适应尺寸 | 1rpx=0.5px=1物理像素      | 移动端小程序   |
| vh   | 视口高度                 | 1vh 等于 1/100 的视口高度 | 自适应长度     |



## 14、CSS中的overflow属性

超出内容区内容处理方式：

- `scroll`——显示滚动条
- `auto`——子元素内容大于父元素时出现滚动条
- `visible`——溢出的内容在父元素之外
- `hidden`——溢出隐藏



## 15、隐藏元素方法

```css
/* 方法1,元素不可见,不占用文档空间  */
display: none;

/* 方法2,使一个元素完全透明,占用空间 */
opacity: 0;

/* 方法3,隐藏元素,但是元素占用空间 */
visibility: hidden;

/* 方法4,将元素定位为负值,显示在画布区域之外 */
position: absolute;

/* 方法5,缩小比例到不可见,位置被保留 */
transform: scale(0);

/* 方法6,高斯模糊到不可见,数值越大越糊 */
filter: blur(100px);

/* 方法7,高度为0，消除边框 */
height: 0;
```



## 16、CSS盒子模型

### （1）标准盒模型

```js
width = 内容宽度(content) + border + padding + margin
```

![](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307070015101.png)



### （2）IE盒模型

```js
width = 内容宽度(content + border + padding) + margin
```

![](https://raw.githubusercontent.com/Rainchen0504/picture/master/202307070016816.png)



## 
