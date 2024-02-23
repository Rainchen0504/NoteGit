# real time

## 一、需求地址

需求包含四个内容，大屏主屏、大屏副屏、移动端、控制页面



## 二、设计稿地址

https://codesign.woa.com/s/rMZe67qPBVRxnjk  R5MJ



## 三、上线时间

<font color=blue>**2024年1月5日24:00**</font>



## 四、技术栈

前端：Vue2

后端：go



## 五、活动流程

### 1、大屏主屏

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202401021715450.png" alt="image-20240102171539800" style="zoom:50%;" />

使用`transition-group`配合`transition: transform 0.5s`实现切换动画

使用`setInterval`定时器轮询请求列表接口（时间间隔待定），使用排序算法如下：

```js
bubbleSort() {
  let len = this.list.length;
  for (let i = 0; i < len; i++) {
    for (let j = 0; j < len - i - 1; j++) {
      if (this.list[j].value > this.list[j + 1].value) {
        // 相邻元素两两对比
        let tmp = this.list[j]; // 元素交换
        this.$set(this.list, j, this.list[j + 1]);
        this.$set(this.list, j + 1, tmp);
      }
    }
  }
},
```



### 2、大屏副屏

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202401021720064.png" alt="image-20240102172034870" style="zoom:50%;" />

同上



### 3、移动端

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202401021722023.png" alt="image-20240102172221875" style="zoom:50%;" />

样式改造



### 4、控制页面

使用三个按钮控制第一、第二、第三part，发送请求更新当前批次

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202401021719808.png" alt="image-20240102171921697" style="zoom:50%;" />



