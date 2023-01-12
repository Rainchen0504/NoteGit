## 1、判断图片是否进入可视区域

1. `window.innerHeight`是浏览器可视区域的高度
2. `document.body.scrollTop || document.documentElement.scrollTop`是浏览器滚动过的距离
3. `img.offsetTop`是元素顶部距离文档顶部的高度，包括滚动条的距离
4. 内容到达显示区域的条件是`img.offsetTop < window.innerHeight + document.body.scrollTop`

![image-20230109173631164](https://raw.githubusercontent.com/Rainchen0504/picture/master/202301091736437.png)



