---
title: CSS面试题的学与记：实现宽高比例自适应
date: 2018-05-09 22:22:57
updated: 2018-05-09 23:10:58
tags: CSS面试题
categories: [CSS, CSS面试题的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

主要是上次被面试官问到的这个问题，然后回答的并不是很好。所以，这里总结一下通过 `CSS` 方式实现固定宽高比例。

* 使用 `padding-top` 或 `padding-bottom`。
* 使用 `calc`。
* 使用 `vw` 新单位。
* 使用 `vw` 结合 `Grid` 布局。
* 未来原生方案 `aspect-ratio`.

<!-- more -->

第一种方式的原理是元素的 `padding` 和 `margin` 属性值为百分比的情况下，是参照父元素的宽度进行百分比计算的，元素的宽度也是。所以，让元素的宽度和 `padding-top` 按比例为父元素宽度的百分比，即可实现宽高比例自适应。

{% jsfiddle pgx15b7h result,html,css dark %}

优点是兼容性高，缺点是如果需要填充元素的话，只能设置 `position: absolute`，否则内容会溢出导致变形。

其它解决方案，强烈推荐https://www.w3cplus.com/css/aspect-ratio.html。这里不再重复描述。
