---
title: DOM对象的学与记：innerHTML、innerText、outerHTML、outerText
date: 2018-05-07 11:17:59
updated: 2018-05-08 16:38:24
tags: DOM对象
categories: [JavaScript, DOM对象的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

一对 inner，一对 outer，长得差不多。其实还算不会那么容易搞混，根据名称就能猜出大概的用途了吧。作为基础的知识点，这里做一个详细的笔记~~

四者都可读可写，读的是当前节点或文本的内容，写的是覆盖当前节点或文本的内容。一个 inner，一个 outer，前者表示只对当前元素内部的节点或文本进行读写，不包括自身；后者则包括自身。

<!-- more -->

## innerHTML

获取 body 下所有子节点内容：

```js
const htmlContent = document.body.innerHTML;
```

覆盖&设置 body 下所有子节点内容：

```js
document.body.innerHTML = '<div>hello world</div>';
```

用在读的操作时，则会返回当前元素所有的子节点内容，包括文本节点、元素节点，注释节点等。但返回的并不是一组集合对象，而是解析完之后的字符串。

```js
console.log(typeof document.body.innerHTML); // string
```

用在写的操作时，具体的经历步骤如下：

* 获取写入的字符串，并解析成 HTML 或 XML，最终生成一个 [DocumentFragment][documentfragment]。
* 若写入的字符串是一个 `<template>` 元素，那么使用 `<template>` 属性 `content` 的值作为 `DocumentFragment` 的内容，并最终用于目标元素的内容。
* 对于其他情况，将使用 `DocumentFragment` 的内容作为目标元素的内容。

发现 `innerHTML` 不太容易报错吧，耐艹，下面的代码都能够正常运行：

```js
// 可以写入文本节点
document.body.innerHTML = 'some test';
// 可以写入自定义标签。IE 浏览器可能会有问题
document.body.innerHTML = '<test>some test content</test>';
// 可以写入非正常闭合的标签
document.body.innerHTML = '</p>some test content</p>';
// 可以写入子元素的内容，或者父元素的内容
document.body.innnerHTML = element.innerHTML;
```

如果插入的不是一个字符串类型，会自动通过 `toString` 方法进行隐式转换，例如插入一个对象：

```js
const obj = {};

document.body.innerHTML = obj;
// 应该等价于
document.body.innerHTML = obj.toString();
```

可以插入一个空字符串，起到清空元素内容的作用；

```js
// 清空 body 元素的内容
document.body.innerHTML = '';
```

最后，并不需要担心类似于 `xss` 的安全问题，即使写入一段脚本代码，最后也不会执行：

```js
document.body.innerHTML = '<script>window.alert(234);</script>';
```

## innerText

和 `innerHTML` 不同的是，写入的是一段文本节点。

```js
document.body.innerText = 'some text content';
```

即使，写入的字符串是一段元素内容，但最终也会被转化，所以也不用担心安全方面的问题，很适合于显示评论内容的使用场景。下面示例在页面中将会显示包括标签的文本内容，而不是解析成段落。

```js
document.body.innerText = '<p>hello world</p>';
```

## outerHTML

和 `innerHTML` 不同的是，写入的字符串会最终作用于元素本身，即包括元素本身，都会被替换掉。

```js
// p 标签本身也会被替换成 div
p.outerHTML = '<div>some div...</div>';
```

可用做删除元素本身：

```js
p.outerHTML = ''; // 删除p元素
```

如果元素不存在父元素，那么使用 `outerHTML`，将会抛出异常：

```js
const div = document.createElement('div');
div.outerHTML = 'hello world';
```

> Uncaught DOMException: Failed to set the 'outerHTML' property on 'Element': This element has no parent node.

另外，需要额外注意的是，**被替换后，变量本身指向的元素属性，都还是之前的元素属性，并不会更新**。

```js
p.outerHTML = '<div>some content</div>';
console.log(p.nodeName); // P，而不是 DIV
```

## outerText

不是一个标准的 api。用在读取的操作时，返回值同 `innerText`，并没有差别。用在写的操作时，包括元素本身被替换成所设置的文本内容。

```js
console.log(p.outerText); // 结果同p.innerText
p.outerText = 'some text';
```

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML<br> https://developer.mozilla.org/en-US/docs/Web/API/Node/innerText<br> https://developer.mozilla.org/en-US/docs/Web/API/Element/outerHTML<br> https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/outerText

[documentfragment]: https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment
