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



## 3、绘制0.5px的线

### （1）viewport方式

```html
<meta 
      name="viewport" 
      content="width=device-width,initial-scale=0.5,minimum-scale=0.5,maximum-scale=0.5" />
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

css的引入方式有两种，link标签和@import引入

### （1）作用

- link是HTML提供的标签，不仅可以加载CSS文件，还可以定义rel连接属性；

- @import是CSS提供的语法规则，只有导入样式表的作用；



### （2）加载顺序

- link标签引入的CSS在加载页面时被同时加载；
- @import引入的CSS将在页面加载完毕后被加载；



### （3）兼容性

- link标签作为HTML元素，不存在兼容性问题；
- @import是CSS2.1才有的语法，只能在IE5+被识别；



### （4）权重

link引入样式权重大于@import引入的样式；



## 5、transition和animation

### （1）transition

该属性是一个简写属性，表示过渡、转变的意思，用于设置四个过渡属性：

- transition-property：设置过渡效果的属性名称（默认值是all）；
- transition-duration：设置过渡完成所需要的时间（默认值是0）；
- transition-timing-function：设置过渡速度效果曲线（默认值是ease）；
- transition-delay：设置过渡的开始时间（默认值是0）；



### （2）animation

该属性是一个简写属性，通过关键帧实现动画效果，用于设置六个动画属性：

- animation-name：设置绑定到选择器的@keyframes名称（默认值是none）；
- animation-duration：设置完成动画所花费的时间（默认值是0）；
- animation-timing-function：设置动画的速度曲线（默认值是ease）；
- animation-delay：设置动画延迟几秒开始（默认值是0）；
- animation-iteration-count：设置动画播放的次数（默认值是1）；
- animation-direction：设置时候轮流反向播放动画（默认值是normal）；



### （3）区别

1. transition是一个过渡效果，没有中间状态，需要设置触发事件才能执行；
2. animation是一个动画效果，有多个关键帧，可以在任意一个中间帧设置状态，不需要设置触发事就能执行；
