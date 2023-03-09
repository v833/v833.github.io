---
title: 在Flexbox Froggy 游戏中搞定flex布局
date: 2021-06-13 17:32:52
tags: CSS
categories: 前端
---
{% note info, 最近发现了一款学习flex布局的游戏,分享给大家 => [Flexbox Froggy](http://flexboxfroggy.com/) %}
{% p primary, 一共 24 关,下面开始通关之旅~ %}
{% title h2, 附上参考答案 %}

{% note success, 1、justify-content: flex-end %}
{% note success, 2、justify-content: center %}
{% note success, 3、justify-content: space-around %}
{% note success, 4、justify-content: space-between %}
{% note success, 5、align-items: flex-end %}
{% note success, 6、justify-content: center;</br>
&nbsp; &nbsp;&nbsp; align-items: center %}
{% note success, 7、justify-content: space-around;</br>&nbsp; &nbsp;&nbsp;&nbsp;align-items: flex-end %}
{% note success, 8、flex-direction: row-reverse %}
{% note success, 9、flex-direction: column %}
{% note success, 10、flex-direction: row-reverse;</br>&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;
    justify-content: flex-end %}
{% note success, 11、flex-direction: column;</br>&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;
    justify-content: flex-end %}
{% note success, 12、flex-direction: column-reverse;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    justify-content: space-between %}
{% note success, 13、flex-direction: row-reverse;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    justify-content: center;</br>&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;
    align-items: flex-end %}
{% note success, 14、order: 1 %}
{% note success, 15、order: -1 %}
{% note success, 16、align-self: flex-end %}
{% note success, 17、order: 1;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    align-self: flex-end %}
{% note success, 18、flex-wrap: wrap; %}
{% note success, 19、flex-direction: column;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    flex-wrap: wrap %}
{% note success, 20、flex-flow: column wrap %}
{% note success, 21、align-content: flex-start %}
{% note success, 22、align-content: flex-end %}
{% note success, 23、flex-direction: column-reverse;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    align-content: center %}
{% note success, 24、flex-flow: column-reverse wrap-reverse;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    justify-content: center;</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    align-content: space-between %}

{% title h2, 总结 %}
>Flex 布局原理就是：给父盒子添加 flex 属性，来控制子盒子的位置排列方式从而实现 flex 布局。
>当我们为父盒子设为 flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效。
{% title h3, flex-direction , warning %}

Flex 布局中默认的主轴是 row，x轴。


如果想改变主轴方向可通过设置 flex-direction 来改变。


flex-direction:column;将主轴改为y轴，纵轴。


flex-direction:row; 将主轴改为x轴，横轴。


flex-direction:row- reverse;主轴为x轴，**并且翻转**。


flex-direction:column- reverse;主轴为y轴，**并且翻转**。
{% title h3, justify-content , warning %}
通过 justify-content 能够设置主轴子元素排列形式。

值为 flex-start 所有子元素在主轴头部显示。

值为 flex-end 所有子元素在主轴尾部显示。

值为 flex-center 所有子元素在主轴居中对齐。

值为 space-around 所有子元素平分剩余空间。

值为 space-between 所有子元素先两边贴边在平分剩余空间。

{% title h3, flex-wrap, warning %}
flex-wrap
控制子元素是否换行。

开启 flex 布局后默认为不换行。

如果想要换行效果设置 flex-wrap：wrap。
{% title h3, align-items, warning %}


align-items 设置侧轴子元素排列。

利用align-items 能够设置侧轴元素对齐的方式在子项为单项（单行）的时候使用。

align-items 的值为flex-start 表示从头开始。

align-items 的值为flex-end 表示从结尾开始。

align-items 的值为center 表示居中显示。

align-items 的值为stretch 会将子元素拉伸。

{% title h3, align-content , warning %}
align-content 设置侧轴子元素排列。

 Align-content 适应于换行（多行）的情况下（单行情况下无效），可以设置上对齐、下对齐、居中、拉伸以及平均分配剩余空间等属性值。

 Align-item 和 align-content 的区别单行找 align-items 多行找 align-content。
{% title h3, flex-flow, warning %}

 flex-flow 就是 flex-direction 和 flex-wrap 的合写。

{% title h3, order , warning %}

order是设置在items里面的属性,具体作用是可以决定本身的排列先后顺序。

子元素可以通过设置 order 数值的大小来设定在页面中出现的顺序，数值小的在前，数值大的在后。

{% title h3, align-self , warning %}

align-self 属性定义 flex 子项单独在侧轴（纵轴）方向上的对齐方式，并覆盖已有的 align-items 的值。

此属性接受与align-items特定项目的值相同的值。