---
title: "[Basic] Flex布局"
catalog: true
toc_nav_num: true
date: 2018-12-28 23:16:30
subtitle: "什么是Flex布局"
header-img: "https://raw.githubusercontent.com/zColdWater/Resources/master/Images/flex.jpg"
tags:
- Basic
catagories:
- Basic
---

> 当前环境: Xcode10.0 Swift4.2 iOS SDK 12.1

Flex 布局是什么？
=======
Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。
任何一个容器都可以指定为 Flex 布局。 - 引用阮一峰老师博客

外部容器
=======

> 1.flex-direction 2.flex-wrap 3.flex-flow 4.justify-content 5.align-items 6.align-content

1. **flex-direction**: 
    ```
    row（默认值）：主轴为水平方向，起点在左端。
    row-reverse：主轴为水平方向，起点在右端。
    column：主轴为垂直方向，起点在上沿。
    column-reverse：主轴为垂直方向，起点在下沿。
    ```

    ```CSS
    .container {
        flex-direction: row | row-reverse | column | column-reverse;
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/yGzggM)点击试试看!

2. **flex-wrap**:

    默认情况下，项目都排在一条线（又称"轴线"）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。

    ```
    nowrap:
    wrap:
    wrap-reverse:
    ```

    ```CSS
    .container{
        flex-wrap: nowrap | wrap | wrap-reverse;
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/bOogMe)点击试试看!

3. **justify-content**:

    justify-content属性定义了项目在主轴上的对齐方式。

    ```
    flex-start（默认值）：左对齐
    flex-end：右对齐
    center： 居中
    space-between：两端对齐，项目之间的间隔都相等。
    space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
    ```

    ```CSS
    .container {
    justify-content: flex-start | flex-end | center | space-between | space-around;
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/vvegbB)点击试试看!

4. **align-items**:

    align-items属性定义项目在交叉轴上如何对齐。也就是以纵轴为对齐

    ```
    flex-start：交叉轴的起点对齐。
    flex-end：交叉轴的终点对齐。
    center：交叉轴的中点对齐。
    baseline: 项目的第一行文字的基线对齐。
    stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
    ```

    ```CSS
    .container {
    align-items: flex-start | flex-end | center | baseline | stretch;
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/LMzxoo)点击试试看!

5. **align-content**:

    align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

    ```
    flex-start：与交叉轴的起点对齐。
    flex-end：与交叉轴的终点对齐。
    center：与交叉轴的中点对齐。
    space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
    space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
    stretch（默认值）：轴线占满整个交叉轴。
    ```

    ```CSS
    .container {
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/oJGBKr)点击试试看!

内部元素
=======
> 1.order 2.flex-grow 3.flex-shrink 4.flex-basis 5.flex align-self

1. **order**:

    order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。

    ```CSS
    .item {
        order: <integer>;
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/QzqpjO)点击试试看!

2. **flex-grow**:

    flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。

    如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。


    ```CSS
    .item {
        flex-grow: <number>; /* default 0 */
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/wRrJWe)点击试试看!

3. **flex-shrink**:

    flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

    ```CSS
    .item {
        flex-shrink: <number>; /* default 1 */
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/WLZpGX)点击试试看!

4. **flex-basis**:

    flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。

    ```CSS
     .item {
        flex-basis: <length> | auto; /* default auto */
    }
    ```
    在线[Demo](https://codepen.io/zcoldwater/pen/WLZppd)点击试试看!
    
    **注意:**当总items的`mainsize`大于屏幕总宽的时候按比例进行分配所占总屏幕宽。

5. **align-self**:

    `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

    该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

    ```CSS
    .item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
    }
    ```
    
    无Demo

