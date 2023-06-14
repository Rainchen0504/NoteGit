## 1、HTML5

1. 标签增删

   新增8个语义元素，header、section、footer、aside、nav、main、article、figure；

   新的表单控件，calander、date、time、email、url、search；

   新的 input 类型 color、date、datetime、datetime-local、email；

   移除过时标签 big、font、frame、frameset

2. 支持canvas 绘图，支持内联 SVG。支持 MathML；

3. 多媒体标签 audio、video、source、embed、track；

4. 本地离线存储，把需要离线存储在本地的文件列在一个 manifest 配置文件；

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