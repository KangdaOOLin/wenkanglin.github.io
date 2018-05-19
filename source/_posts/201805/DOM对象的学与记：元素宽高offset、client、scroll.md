---
title: DOM对象的学与记：元素宽高offset、client、scroll
date: 2018-05-19 17:02:57
updated: 2018-05-19 21:07:07
tags: DOM对象
categories: [JavaScript, DOM对象的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

原生 JavaScript 获取元素的宽高，一般能想得到的是以下这几种方式：

* offsetWidth/offsetHeight
* clientWidth/clientHeight
* scrollWidth/scrollHeight
* getBoundingClientRect

以前总对上面这几个感觉很烦躁ヽ(`Д´)ﾉ，现在也很烦~~。这里痛定思痛，静下心来好好巩固一下这几个知识点。

<!-- more -->

## offsetWidth/offsetHeight

> CSS width属性值 + 左右padding + 左右border + 垂直滚动条宽度 = offsetWidth
> CSS height属性值 + 上下padding + 上下border + 水平滚动条高度 = offsetHeight

这里列出需要关注的重点：

* offsetWidth/offsetHeight 是**只读属性**，手动赋值虽不会报错，但也不会生效。所以，也就不能通过这两个属性动态的修改元素的宽高。
* offsetWidth/offsetHeight **返回值总是整数**，而不是精确的浮点数，四舍五入取整数。
* 若元素 `display: none`，那么 offsetWidth/offsetHeight 为 0。
* offsetHeight 的值**不包括伪元素的高度**，例如 `:before` 和 `:after`。
* 若元素内部包含未清除浮动的子元素，那么将不包含这些浮动元素的宽高。

{% jsfiddle x2v5b7pj html,css,js,result dark %}

从上面的 demo 可以发现，设置的元素宽度是 100 px，即使出现了垂直滚动条，但 `offsetWidth` 返回值依旧是 100，说明包含滚动条的宽度。并且，虽然元素存在 `:before` 和 `:after` 伪元素，但 `offsetHeight` 的返回值并不受它们的影响。

## clientWidth/clientHeight

> CSS width属性值 + 左右padding - 垂直滚动条宽度 = clientWidth
> CSS height属性值 + 上下padding - 水平滚动条高度 = clientHeight

这里同样列出需要关注的重点：

* **只读属性**。
* **返回值总是整数**。
* 若元素 `display: none`，那么 clientWidth/clientHeight 也是为 0。
* clientHeight 的值同样**不包括伪元素的高度**。
* 受浮动元素的影响。
* clientWidth/clientHeight **不能正确获取行内元素的宽高，返回值为 0**。

相比于 offsetWidth/offsetHeight，clientWidth/clientHeight 不包含滚动条和 border 的宽高。除此之外，最明显的一点就是上面列出的最后一点，很容易被忽略掉，使用时务必小心。

{% jsfiddle qth2mj0w html,css,js,result dark %}

## scrollWidth/scrollHeight

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetWidth
> https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetHeight
> https://developer.mozilla.org/en-US/docs/Web/API/Element/clientWidth
> https://developer.mozilla.org/en-US/docs/Web/API/Element/clientHeight
> https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollWidth
> https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollHeight
