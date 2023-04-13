# Document

## 1、浏览器环境，规格

​	JS语言最初是为Web浏览器创建的，后来已经演变了一种具有多种用途和平台（主机环境）的语言。

​	![image-20230411215831299](https://raw.githubusercontent.com/Rainchen0504/picture/master/202304112158531.png)

window根对象：

1. 它是JS代码的全局对象；
2. 它代表浏览器窗口，并提供控制它的方法；



### （1）文档对象模型DOM

​	将所有页面内容表示为可修改的对象；



### （2）浏览器对象模型BOM

​	表示由浏览器（主机环境）提供的用于处理文档之外的所有内容的其他对象；

- `navigator`对象提供了有关浏览器和操作系统的背景信息；
- `location`对象允许读取当前URL，并且可以将浏览器重定向到新的URL；



## 2、DOM树

HTML/XML 文档在浏览器内均被表示为 DOM 树

- 标签成为元素节点，并形成文档结构；
- 文本成为文本节点；
- HTML 中的所有东西在 DOM 中都有它的位置，甚至对注释也是如此；



## 3、遍历DOM

对 DOM 的所有操作都是以 `document` 对象开始。它是 DOM 的主“入口点”。从它我们可以访问任何节点。

![image-20230412091352174](https://raw.githubusercontent.com/Rainchen0504/picture/master/202304120913496.png)

给定一个 DOM 节点，可以使用导航属性访问其直接的邻居。

- 对于所有节点：

  `parentNode,c`

- 仅对于元素节点：
