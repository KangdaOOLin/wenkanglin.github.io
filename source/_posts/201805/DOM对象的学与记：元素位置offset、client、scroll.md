---
title: DOM对象的学与记：元素位置offset、client、scroll
date: 2018-05-20 14:35:03
updated: 2018-05-21 18:21:14
tags: DOM对象
categories: [JavaScript, DOM对象的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

之前，我在 {% post_link DOM对象的学与记：元素宽高offset、client、scroll %} 这篇文章中介绍了关于元素宽高的几种属性/方法。在这里，接着往下介绍和元素位置有关的几种属性/方法：

* offsetLeft/offsetTop
* clientLeft/clientTop
* scrollLeft/scrollTop
* getBoundingClientRect

想要获取元素的 left 和 top 位置信息，必须要有一个参照物，要么是视窗 viewport，要么是页面文档 document，要么是其它如父元素之类的。

<!-- more -->

## offsetLeft/offsetTop

> Math.round(相对于 offsetParent 的左偏移量) = offsetLeft
> Math.round(相对于 offsetParent 的上偏移量) = offsetTop

通过 offsetLeft/offsetTop，返回的 left 和 top 位置信息，是参照于元素的 `offsetParent`。那么，问题来了：

> offsetParent 是什么？和 parentElement 有什么区别？
> 作用和 parentElement 差不多，返回该元素的最近的一个父元素。offsetParent 相比于后者，并不是一个标准规范的属性。还有一个最明显的区别就是，若当前元素 `display: none`，即使元素的 offsetParent 存在，此时 offsetParent 返回还是为 null。

这里列出需要关注的重点：

* offsetLeft/offsetTop 是**只读属性**。
* 参照物是父元素 `offsetParent`，而非视窗，页面文档。若父元素是 body，那么相当于参照于页面文档。
* 受 `display: none` 影响，offsetLeft/offsetTop 返回值为 0。
* offsetLeft/offsetTop **返回值总是为整数**。
* 若元素内容存在多行的情况，offsetLeft/offsetTop 总是返回第一行的 left 和 top 值。

对于最后一点，可能描述的不是很清楚，还是根据实际 demo 来理解。

{% jsfiddle 3yrwrcw5 result,html,css,js dark %}

上面的 demo，我们要获取蓝色边框 `span` 元素的 offsetLeft/offsetTop，这里该元素存在多行的情况。从第二行开始，`offsetLeft` 相对于父元素 `offsetParent` 应该是 0。但最终显示的 `offsetLeft` 并不是 0。

所以，这里多行情况下，元素的 offsetLeft/offsetTop 总是以第一行的值为准的。

## clientLeft/clientTop

> Math.round(元素border-left-width) + 左边垂直滚动条宽度 = clientLeft
> Math.round(元素border-top-width) = clientTop

这里同样列出需要关注的重点：

* **只读属性**。
* 参照物可以说是元素本身的 `border` 属性。
* 受 `display: none` 影响，clientLeft/clientTop 返回值为 0。
* **返回值总是为整数**。
* 若为行内元素，或 `display: inline`，那么 clientLeft/clientTop 返回值为 0。

大部分情况下，clientLeft/clientTop 的值是元素左边框/上边框的大小。而 `clientLeft` 稍微特殊一点，如果文字的方向是 `rtl`，并在左边出现垂直滚动条的时候，`clientLeft` 的值还要加上滚动条的宽度。

{% jsfiddle fvo6wtyL result,html,css,js dark %}

## scrollLeft/scrollTop

> 元素滚动的左偏移量 = scrollLeft
> 元素滚动的上偏移量 = scrollTop

这里同样列出需要关注的重点：

* **可读可写属性**。例如 `el.scrollTop = 100` 动态设置元素的滚动偏移量。
* **返回值不是整数，而是精确的数值**。
* 若元素不能滚动（无滚动条），那么 scrollLeft/scrollTop 返回值为 0。
* 受 `display: none` 影响，scrollLeft/scrollTop 返回值为 0。

scrollLeft/scrollTop 作为可写属性，一般情况下，若设置的值为负数，会以 0 来处理；若设置的值超过了最大的滚动偏移量，会以最大的滚动偏移量来处理。

上面说的是一般情况下，非一般情况下，当元素设置的方向为 `rtl`，且出现水平滚动条时，此时元素靠在最右边，`scrollLeft` 的值为 0。当元素水平滚动后，`scrollLeft` 的值会负数增加。此时，`scrollLeft` 最大值为 0，若手动赋值超过 0 的数值，将会以 0 来处理；若设置的值为负数，且没有超过最大的水平滚动偏移量，将能够正确处理。

:warning: Chrome 浏览器下不会出现 `scrollLeft` 负数的情况，将正常处理。

{% jsfiddle bqf7e8fc result,html,css,js dark %}

## getBoundingClientRect

> const { left, top, right, bottom } = el.getBoundingClientRect();

这里同样列出需要关注的重点：

* **只读属性**。
* **参照物是视窗 viewport**。
* **返回值是精确的数值**。
* 受 `display: none` 影响，left, top, right, bottom 的值将会为 0。

## 总结

| 对比项                  | offset         | client | scroll     | getBoundingClientRect |
| ----------------------- | :------------: | :----: | :--------: | :-------------------: |
| 是否可写                | ✘              | ✘      | ✔          | ✘                     |
| 参照物                  | `offsetParent` | border | 滚动偏移量 | 视窗 viewport         |
| 返回值是否精确          | ✘              | ✘      | ✔          | ✔                     |
| 受 `display: none` 影响 | ✔              | ✔      | ✔          | ✔                     |

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetLeft
> https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetTop
> https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetParent
> https://developer.mozilla.org/en-US/docs/Web/API/Element/clientLeft
> https://developer.mozilla.org/en-US/docs/Web/API/Element/clientTop
> https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollLeft
> https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollTop
> https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect
