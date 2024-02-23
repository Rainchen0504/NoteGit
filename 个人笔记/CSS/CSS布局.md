### 一、Flex布局

### 1、简介

​	Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。

​	任意一个容器都可以指定为flex布局，行内元素也可以使用flex布局，Webkit 内核的浏览器，必须加上`-webkit`前缀。

注意⚠️：设为flex后，子元素的<font color=red>float、clear、vertical-align</font>属性将失效。

### 2、基本概念

​	采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"<font color=red>容器</font>"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"<font color=red>项目</font>"。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231842680.png" alt="image-20211123184235707" style="zoom: 33%;" />

​	容器默认存在两根轴：**水平的主轴**（main axis）和**垂直的交叉轴**（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。

项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

### 3、容器的属性

#### （1）flex-direction属性

​	该属性决定主轴的方向（项目的排列方向）

```css
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231848019.png" alt="image-20211123184753316" style="zoom:33%;" />

可选项共有四个值

- row：默认值，主轴为水平方向，起点在左端；
- row-reverse：主轴为水平方向，起点在右端；
- column：主轴为垂直方向，起点在上沿；
- column-reverse：主轴为垂直方向，起点在下沿；

#### （2）flex-wrap属性

该属性定义轴线的排列方向，控制如何换行

```css
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

可选项共三个值：

- nowrap：默认值，不换行；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231859460.png" alt="image-20211123185850742" style="zoom:33%;" />

- wrap：换行，第一行在上；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231859178.png" alt="image-20211123185918284" style="zoom:33%;" />

- wrap-reverse：换行，第一行在下方；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231859401.png" alt="image-20211123185942146" style="zoom:33%;" />

#### （3）flex-flow属性

该属性是`flex-direction`和`flex-wrap`属性的简写，默认值为`row nowrap`。

```css
.box {
  flex-flow: <flex-direction> || <flex-wrap>;
}
```

#### （4）justify-content属性

该属性定义了项目在主轴上的对齐方式

```css
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

可选项共五个值：

- flex-start：默认值，左对齐；
- flex-end：右对齐；
- center：居中；
- space-between：两端对齐，项目之间的间隔都相等；
- space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231911057.png" alt="image-20211123191138229" style="zoom: 25%;" />

#### （5）align-items属性

该属性定义项目在交叉轴上如何看齐

```css
.box {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```

可选项共五个值：

- flex-start：交叉轴的起点对齐；
- flex-end：交叉轴的终点对齐；
- center：交叉轴的中点对齐；
- baseline：项目的第一行文字的基线对齐；
- stretch：默认值，如果项目未设置高度或设为auto，将占满整个容器的高度；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231923245.png" alt="image-20211123192246971" style="zoom:25%;" />

#### （6）align-content属性

​	该属性定义了多根轴线的对齐方式，如果项目只有一根轴线，该属性不起作用。

```css
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

可选项共6个值：

- flex-start：与交叉轴的起点对齐；
- flex-end：与交叉轴的终点对齐；
- center：与交叉轴的中点对齐；
- space-between：与交叉轴两端对齐，轴线之间的间隔平均分布；
- space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍；
- stretch：默认值，轴线占满整个交叉轴；

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231945342.png" alt="image-20211123194516069" style="zoom:25%;" />

### 4、项目的属性

#### （1）order

​	该属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。

```css
.item {
  order: <integer>;
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231948884.png" alt="image-20211123194834944" style="zoom:33%;" />

#### （2）flex-grow

​	该属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。

```css
.item {
  flex-grow: <number>; /* default 0 */
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231949769.png" alt="image-20211123194947963" style="zoom:33%;" />

​	如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

#### （3）flex-shrink

​	该属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

```css
.item {
  flex-shrink: <number>; /* default 1 */
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231952384.png" alt="image-20211123195213348" style="zoom:50%;" />

​	如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。**负值对该属性无效**。

#### （4）flex-basis

​	该属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。

```css
.item {
  flex-basis: <length> | auto; /* default auto */
}
```

它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。

#### （5）flex

​	该属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为0 1 auto。后两个属性可选。

```css
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

该属性有两个快捷值：`auto` (1 1 auto) 和 none (0 0 auto)。

建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。



<font color=red>`flex: 1`的填充方向为设置`flex`属性的父元素的排列方向</font>；

#### （6）align-self

​	该属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于stretch。

```css
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231956822.png" alt="image-20211123195543792" style="zoom: 33%;" />

该属性可能取6个值，除了auto，其他都与align-items属性完全一致。



/*************************************************************************************************************************************************************************************************************************************/



## 二、Grid网格布局

### 1、简介

​	将网页划分成一个个网格，可以任意组合不同的网格，做出各种布局。只能通过复杂的 CSS 框架达到的效果，现在浏览器内置了。

​	Flex 布局是轴线布局，只能指定"项目"针对轴线的位置，可以看作是一维布局。Grid 布局则是将容器划分成"行"和"列"，产生单元格，然后指定"项目所在"的单元格，可以看作是二维布局。Grid 布局远比 Flex 布局强大。



### 2、基础用法

#### （1）容器和布局

​	<font color=red>采用网格布局的区域</font>，称为"容器"。<font color=red>容器内部采用网格定位的子元素</font>，称为"项目"。

```html
<div>
  <div><p>1</p></div>
  <div><p>2</p></div>
  <div><p>3</p></div>
</div>
```

​	上面代码中，最外层的<div>元素就是容器，内层的三个<div>元素就是项目。

⚠️注意：项目只能是容器的顶层子元素，不包含项目的子元素，比如上面代码的元素就不是项目。Grid 布局只对项目生效。      

#### （2）行和列

容器里面的水平区域称为"行"，垂直区域称为"列"。

#### （3）单元格

行和列的交叉区域，称为"单元格"。

正常情况下，n行和m列会产生n x m个单元格。比如，3行3列会产生9个单元格。

#### （4）网格线

划分网格的线，称为"网格线"。水平网格线划分出行，垂直网格线划分出列。

正常情况下，n行有n + 1根水平网格线，m列有m + 1根垂直网格线，比如三行就有四根水平网格线。



### 3、进阶用法

​	Grid布局的属性分成两类。一类定义在容器上面，称为<font color=red>容器属性</font>；另一类定义在项目上面，称为<font color=red>项目属性</font>。

#### （1）display属性

`display:grid`指定一个容器采用网格布局。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231201292.png" alt="image-20211123120044145" style="zoom:50%;" />

上图是`display:grid`的效果，默认情况下，容器元素都是块级元素，但是也可以设成行内元素。

```css
//块级元素
div {
  display: grid;
}
//行内元素
div {
  display: inline-grid;
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231346857.png" alt="image-20211123134647734" style="zoom:50%;" />

上图是`display: inline-grid`的效果，设为网格布局以后，容器子元素（项目）的<font color=orange>float、display: inline-block、display: table-cell、vertical-align和column-*等设置都将失效。</font>

#### （2）行列属性

​	容器指定了网格布局以后，接着就要划分行和列。`grid-template-columns`属性定义每一列的列宽，`grid-template-rows`属性定义每一行的行高。

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
}
```

​	上面代码指定了一个三行三列的网格，列宽和行高都是100px，除了使用绝对单位，也可以使用百分比。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231351493.png" alt="image-20211123135113812" style="zoom: 33%;" />

①<font color=red>repeat()</font>

​	使用repeat()函数，简化重复的值。repeat()接受两个参数，第一个参数是重复的次数，第二个参数是所要重复的值。repeat()重复某种模式也是可以的。

```css
/*上面的例子可以简写为*/
.container {
  display: grid;
  grid-template-columns: repeat(3, 33.33%);
  grid-template-rows: repeat(3, 33.33%);
}
```

例子：

定义了6列，第一列和第四列的宽度为100px，第二列和第五列为20px，第三列和第六列为80px。

```css
grid-template-columns: repeat(2, 100px 20px 80px);
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231403817.png" alt="image-20211123140336959" style="zoom: 33%;" />

②<font color=red>auto-fill关键字</font>

​	有时单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用auto-fill关键字表示自动填充。

```css
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, 100px);
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231417931.png" alt="image-20211123141653846" style="zoom:33%;" />

③<font color=red>fr关键字</font>

​	为了方便表示比例关系，网格布局提供了fr关键字（fraction 的缩写，意为"片段"）。如果两列的宽度分别为1fr和2fr，就表示后者是前者的两倍。

```css
.container {
  display: grid;
  grid-template-columns: 1fr 2fr;
}
```

fr可以和绝对长度的单位结合使用，绝对长度为固定尺寸，其他尺寸按照比例动态适配。

④<font color=red>minmax()</font>

​	minmax()函数产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。

```css
grid-template-columns: 1fr 1fr minmax(100px, 1fr);
```

上面代码中，minmax(100px, 1fr)表示列宽不小于100px，不大于1fr。

⑤<font color=red>auto关键字</font>

​	auto关键字表示由浏览器自己决定长度。

```css
grid-template-columns: 100px auto 100px;
```

上面代码中，第二列的宽度，基本上等于该列单元格的最大宽度，除非单元格内容设置了min-width，且这个值大于最大宽度。

⑥<font color=red>网格线的名称</font>

​	`grid-template-columns`属性和`grid-template-rows`属性里面，还可以使用方括号，指定每一根网格线的名字，方便以后的引用。

```css
.container {
  display: grid;
  grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
  grid-template-rows: [r1] 100px [r2] 100px [r3] auto [r4];
}
```

上面代码指定网格布局为3行 x 3列，因此有4根垂直网格线和4根水平网格线。方括号里面依次是这八根线的名字。网格布局允许同一根线有多个名字，比如[fifth-line row-5]。

⑦<font color=red>布局实例</font>

​	`grid-template-columns`属性对于网页布局非常有用。两栏式布局只需要一行代码。下面代码将左边栏设为70%，右边栏设为30%。

```css
.wrapper {
  display: grid;
  grid-template-columns: 70% 30%;
}                             
```

​	传统的十二网格布局，写起来也很容易。

```css
grid-template-columns: repeat(12, 1fr);
```

#### （3）间隔属性

​	`grid-row-gap`属性设置行与行的间隔（行间距），`grid-column-gap`属性设置列与列的间隔（列间距）。

```css
.container {
  grid-row-gap: 10px;
  grid-column-gap: 40px;
}
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231442239.png" alt="image-20211123144222334" style="zoom:33%;" />

`grid-gap`属性是`grid-row-gap`和`grid-column-gap`的合并简写形式

```css
/*上面的可以简写为*/
.container {
  grid-gap: 20px 20px;
}
```

如果grid-gap省略了第二个值，浏览器认为第二个值等于第一个值。

根据最新标准，上面三个属性名的grid-前缀已经删除，<font color=red>grid-column-gap和grid-row-gap写成column-gap和row-gap，grid-gap写成gap</font>。

#### （4）区域属性

​	网格布局允许指定"区域"，一个区域由单个或多个单元格组成。grid-template-areas属性用于定义区域。

上面代码先划分出9个单元格，然后将其定名为a到i的九个区域，分别对应这九个单元格。

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                       'd e f'
                       'g h i';
}
```

​	多个单元格合并成一个区域的写法如下，下面代码将9个单元格分成a、b、c三个区域。

```css
grid-template-areas: 'a a a'
                     'b b b'
                     'c c c';
```

如果某些区域不需要利用，则使用"点"（.）表示。

```css
grid-template-areas: 'a . c'
                     'd . f'
                     'g . i';
```

上面代码中，中间一列为点，表示没有用到该单元格，或者该单元格不属于任何区域。

注意，区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为区域名-start，终止网格线自动命名为区域名-end。

比如，区域名为header，则起始位置的水平网格线和垂直网格线叫做header-start，终止位置的水平网格线和垂直网格线叫做header-end。

#### （5）容器默认顺序属性

​	划分网格以后，容器的子元素会按照顺序，自动放置在每一个网格。默认的放置顺序是“先行后列”，即先填满第一行，再放入第二行。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231500755.png" alt="image-20211123150029728" style="zoom:33%;" />

这个顺序由`grid-auto-flow`属性决定，默认值我是row，即“先行后列”。也可以设置成column，变成“先列后行”。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231502040.png" alt="image-20211123150158992" style="zoom:33%;" />

`grid-auto-flow`属性除了设置成row和column，还可以设成row dense和column dense。这两个值主要用于，某些项目指定位置以后，剩下的项目怎么自动放置。

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231503287.png" alt="image-20211123150345001" style="zoom:33%;" />

​	上图让1号项目和2号项目占据两个单元格，然后在默认的grid-auto-flow:row情况下得到该结果。观察发现，1号项目后面是空的，这是因为3号项目默认跟着2号项目，所以会排在2号项目后面。如果改为row dense，表示“先行后列”，并且尽可能紧密填满，尽量不出现空格。

```css
grid-auto-flow: row dense;
```

修改后效果如下图：

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231504835.png" alt="image-20211123150436949" style="zoom:33%;" />

上图会先填满第一行，再填满第二行，所以3号项目就会紧跟在1号项目的后面。8号项目和9号项目就会排到第四行。

如果将设置改为column dense，表示"先列后行"，并且尽量填满空格。

```css
grid-auto-flow: column dense;
```

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202111231505608.png" alt="image-20211123150534924" style="zoom:33%;" />

上图会先填满第一列，再填满第2列，所以3号项目在第一列，4号项目在第二列。8号项目和9号项目被挤到了第四列。

#### （6）单元格内容的排列方式

​	justify-items属性设置单元格内容的水平位置（左中右），align-items属性设置单元格内容的垂直位置（上中下）。

```css
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
}                          
```

这两个属性的写法完全相同，都可以取下面这些值。

- start：对齐单元格的起始边缘。
- end：对齐单元格的结束边缘。
- center：单元格内部居中。
- stretch：拉伸，占满单元格的整个宽度（默认值）。
- place-items属性是align-items属性和justify-items属性的合并简写形式。
- place-items:  ;如果省略第二个值，则浏览器认为与第一个值相等。



## 三、CSS实现元素上下左右居中

**以下内容皆以实现wrapper里的content居中为目的**

### 1、<font color=red>已知元素宽高</font>前提下

#### （1）left：50%，top：50%

父元素设置相对定位，position：relative；

子元素（要居中元素）设置绝对定位，position：absolute；left：50%；top：50%；

![image-20211224110511132](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241105388.png)

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241105384.png" alt="image-20211224110540634" style="zoom:33%;" />



#### （2）设置margin：auto

父元素设置相对定位,position: relative;

子元素(要居中的元素)设置绝对定位，position: absolute; margin: auto

<img src="/Users/rain_chen/Library/Application Support/typora-user-images/image-20211224110651672.png" alt="image-20211224110651672" style="zoom: 67%;" />

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241107599.png" alt="image-20211224110739785" style="zoom:33%;" />



#### （3）flex布局

父元素设置 display: flex; justify-content: center;  align-items: center;

![image-20211224110839409](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241108210.png)

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241109093.png" alt="image-20211224110902495" style="zoom:33%;" />



### 2、<font color=red>未知元素宽高</font>前提下

#### （1）四个方向设置值，把元素撑开

父元素设置相对定位，position: relative; 

子元素设置绝对定位，position: absolute; top与bottom设置一样的值，left与right设置一样的值，把容器撑开；

![image-20211224111008088](https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241110455.png)

<img src="https://raw.githubusercontent.com/Rainchen0504/picture/master/202112241111052.png" alt="image-20211224111057165" style="zoom:33%;" />

