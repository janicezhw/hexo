---
title: HTML相关学习记录
date: 2019-06-21 14:39:23
tags:
---

# flexbox弹性布局
记录一些flexbox的基础概念，实际使用更多信息需要在相关文档中查看：
> 介绍：
> * https://www.runoob.com/w3cnote/flex-grammar.html
> * http://static.vgee.cn/static/index.html

> 练习：
> * https://flexboxfroggy.com/

> 文档：
> * https://www.runoob.com/
> * http://www.w3school.com.cn/
 
<!-- more -->

容器指定为Flex布局
```
.box{
  display: flex;
}
```
![](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15611011269156.jpg)
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。

项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。
## 容器的属性
主要6个属性：

```
flex-direction
flex-wrap
flex-flow
justify-content
align-items
align-content
```
### flex-direction
决定主轴方向
```
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```
row（默认值）：主轴为水平方向，起点在左端。
row-reverse：主轴为水平方向，起点在右端。
column：主轴为垂直方向，起点在上沿。
column-reverse：主轴为垂直方向，起点在下沿。
### flex-wrap
换行类型
```
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
nowrap（默认）：不换行
wrap：换行，第一行在上方
wrap-reverse：换行，第一行在下方；
### flex-flow
flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

```
.box {
  flex-flow: <flex-direction> <flex-wrap>;
}
```
### justify-content
justify-content属性定义了项目在主轴上的对齐方式。

```
.box { justify-content: flex-start | flex-end | center | space-between | space-around; }
```
flex-start（默认值）：左对齐
flex-end：右对齐
center：居中
space-between：两端对齐，项目之间的间隔都相等。
space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
![-w318](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15611016884844.jpg)

### align-items属性
align-items属性定义项目在交叉轴上如何对齐。

```
.box {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```
flex-start：交叉轴的起点对齐。
flex-end：交叉轴的终点对齐。
center：交叉轴的中点对齐。
baseline: 项目的第一行文字的基线对齐。
stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
![-w308](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15611018511756.jpg)
### align-content
align-content属性定义了多根轴线（多行）的对齐方式。如果项目只有一根轴线，该属性不起作用。

```
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```
flex-start：与交叉轴的起点对齐。
flex-end：与交叉轴的终点对齐。
center：与交叉轴的中点对齐。
space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
stretch（默认值）：轴线占满整个交叉轴。
![-w310](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15611020947516.jpg)

## flex容器中每个项目(item)的属性
以下6个属性设置在项目上
```
order
flex-grow
flex-shrink
flex-basis
flex
align-self
```
### order
order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
```
.item {
  order: <integer>;
}
```
### flex-grow属性
flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
* 如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。
* 如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

```
.item {
  flex-grow: <number>; /* default 0 */
}
```
![-w401](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15611038571452.jpg)

### flex-shrink
flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

* 如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。
* 如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。
* 负值对该属性无效。

```
.item {
  flex-shrink: <number>; /* default 1 */
}
```
![-w350](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15611039388435.jpg)

### flex-basis
flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。

```
.item { flex-basis: <length>; | auto; /* default auto */ }
```
### flex
flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。

该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

```
.item { flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ] }
```
### align-self属性
align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。

```
.item { align-self: auto | flex-start | flex-end | center | baseline | stretch; }
```

flex布局小游戏：https://flexboxfroggy.com/

-------
其他布局框架：Grid
http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html
https://www.w3schools.com/css/css_grid.asp
https://codepip.com/games/grid-garden/#zh-cn





js中函数的定义
参考：https://juejin.im/post/5aa1eb056fb9a028b77a66fd
```
function test(name) {  //声明式写法
    console.log(name)
}
test('Jerry')

let test2 = function(name) {  //赋值式写法
    console.log(name)
}
test2('Tom')
```

箭头函数写法

```
const test = (name) => {
    console.log(name)
}
test('Jerry')
```

对象中函数的调用：
关于this指向问题：
```
const obj = {
	a: function() { console.log(this) }    
}
obj.a()  //打出的是obj对象
```

```
const obj = {
    a: () => {
        console.log(this)
    }
}
obj.a()  //打出来的是window
```



