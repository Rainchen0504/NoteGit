## 1、HTML5

1. 标签增删

   新增8个语义元素，header、section、footer、aside、nav、main、article、figure；

   新的表单控件，calander、date、time、email、url、search；

   新的 input 类型 color、date、datetime、datetime-local、email；

   移除过时标签 big、font、frame、frameset

2. 支持canvas 绘图，支持内联 SVG。支持 MathML；

3. 多媒体标签 audio、video、source、embed、track；

4. 本地离线存储，把需要离线存储在本地的文件列在一个 manifest 配置文件；

   1. `localStorage`长期存储数据，浏览器关闭后数据不丢失；

   2. `sessionStorage`的数据在浏览器关闭后自动删除；

5. 增加web 存储，localStorage、SessionStorage；

6. 增加地理定位API，多线程编程的 web worker 和 websocket 协议等



## 2、iframe

### （1）定义

​	HTML内联框架元素，表示嵌套的浏览上下文（浏览器展示文档的环境）。够将另一个 HTML 页面嵌入到当前页面中



### （2）优点

1. 独立性，可以在页面上独立显示一个页面或者内容，不会与页面其他元素产生冲突。
2. 可重用性，可以在多个页面中重用同一个页面或者内容，可以减少代码的冗余。
3. 异步加载，加载是异步的，页面可以在不等待 iframe 加载完成的情况下进行展示。
4. 方便实现跨域访问，可以方便地实现跨域访问，这在某些场景下非常有用。



### （3）缺点

1. 安全问题，可以嵌入来自其他网站的内容，这可能会导致安全问题，例如点击劫持等攻击。
2. SEO问题，搜索引擎可能无法正确解析 iframe 中的内容，这可能会影响页面的搜索排名。
3. 页面加载速度，内容需要额外的 HTTP 请求和页面加载时间，这可能会影响整个页面的加载速度。
4. 可访问性，有些屏幕阅读器可能无法正确读取 iframe 中的内容，这会影响可访问性。
5. 阻塞主页面onload事件；



## 3、Doctype 作用

Doctype 声明于文档最前面，告诉浏览器以何种方式来渲染页面，这里有两种模式，严格模式和混杂模式。

- 严格模式

  排版和 JS 运作模式是以该浏览器支持的最高标准运行

- 混杂模式

  向后兼容，模拟老式浏览器，防止浏览器无法兼容页面



## 4、盒子模型

1. IE盒模型：

   width的宽度包含 border + padding + content

2. W3C标准盒模型：

   width的宽度包含 content



## 5、BFC

### （1）定义

**块级格式上下文**，是一个<font color=deepred>**独立的渲染区域**</font>，有一定的布局规划，与区域外部毫不相干。



### （2）特征

1. BFC是一个独立的容器，容器内子元素不会影响容器外的元素，反之亦是如此；
2. 盒子从顶端开始垂直的一个个排列，盒子之间垂直的间距是由`margin`决定的；
3. 在同一个BFC中，两个相邻的块级盒子的垂直外边距会发生重叠；
4. BFC区域不会和`float box`发生重叠；
5. BFC能够识别并包含浮动元素，当计算其区域高度时，浮动元素也可以参与计算；



### （3）产生条件

1. `float`值不为`none`；
2. `position`的值不为`static`或者`relative`；
3. `display`的值为`inline-block`、`table-cell`、`flex`、`table-caption`或者`inline-flex`；
4. `overflow`的值不为`visible`；



### （4）作用

1. 解决高度坍塌问题；
2. 清除内部浮动；
3. 自适应多栏布局；



## 6、块元素和行内元素

### （1）块元素

独占一行，并且自动填满父级，可以设置`margin`和`padding`以及宽高属性，内部可以包含块元素和行内元素；

常见元素：`div p h1-h6 ul li ol tr td hr`



### （2）行内元素

不会独占一行，`width`和`height`会失效，并且在垂直方向的`padding`和`margin`会失效，只能包含行内元素；

常见元素：`span img a input i select`



### （3）空元素

`br hr link meta`



## 7、图片格式区别

### （1）png格式

图片背景透明，可以支持的颜色很多；

使用范围广，使用频率最高；



### （2）jpg格式

图片不支持透明，是静态图，可压缩，支持颜色较多；

使用范围广，可调整分辨率；



### （3）gif格式

动态图，支持颜色少；

使用频率较低；



