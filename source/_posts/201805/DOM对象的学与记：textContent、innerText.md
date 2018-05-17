---
title: DOM对象的学与记：textContent、innerText
date: 2018-05-12 00:38:28
updated: 2018-05-17 17:38:33
tags: DOM对象
categories: [JavaScript, DOM对象的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

我们对 `textContent` 和 `innerText` 可能并不陌生，也都用过。但两者的区别呢？接下来，在这里将细数这两者的区别？

<!-- more -->

第一点不同，**innerText 对 CSS 样式敏感**。

假设存在以下的 HTML 结构和样式：

```html
<div>
  <p style="display: none">hidden text</p>
  <p style="visibility: hidden">hidden text too</p>
</div>
```

那么，`innerText` 将获取不了文本内容，而 `textContent` 可以。所以，`innerText` 会受 CSS 样式的影响，并且访问 `innerText` 会导致页面的回流/重绘。

所以，考虑性能方面的优化，选择 `textContent` 会更好。另外考虑到是不是要获取隐藏元素的文本内容，来选择使用哪一个。

第二点不同，**textContent 包含的内容多**。

`textContent` 一个绝对的优势就是能够获取到页面大部分所有的文本内容，包括 `<style>` 和 `<script>` 标签里面的内容，而 `innerText` 就不行。

第三点不同，**textContent 返回的文本格式不会被破坏**。

`textContent` 返回的文本会保留空行以及换行符产生的换行，而 `innerText` 会过滤掉这些。

最后一点不同，**document.textContent 和 document.innerText**。

```js
console.log(document.textContent); // null
console.log(document.innerText); // undefined
```

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent
> https://developer.mozilla.org/en-US/docs/Web/API/Node/innerText
