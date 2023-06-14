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
<meta name="viewport" content="width=device-width,initial-scale=0.5,minimum-scale=0.5,maximum-scale=0.5" />
```



### （2）transform缩放

```css
tarnsform: scale(0.5, 0.5)
```



### （3）box-shadow阴影

```css
height: 1px;
background: none;
box-shadow: 0 0.5px 0 #000
```



## 4、link和@import
