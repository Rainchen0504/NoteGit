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



## 10、iframe

### （1）定义

​	HTML内联框架元素，表示嵌套的浏览上下文（浏览器展示文档的环境）。够将另一个 HTML 页面嵌入到当前页面中

### （2）缺点

1. 会阻塞主页面onload事件；
2. 搜索引擎无法读取，不利于SEO；
3. iframe 和主页面共享连接池，而浏览器对相同区域有限制所以会影响性能；



## 11、Doctype 作用

Doctype 声明于文档最前面，告诉浏览器以何种方式来渲染页面，这里有两种模式，严格模式和混杂模式。

- 严格模式

  排版和 JS 运作模式是以该浏览器支持的最高标准运行

- 混杂模式

  向后兼容，模拟老式浏览器，防止浏览器无法兼容页面

