---
title: DOM对象的学与记：NodeList和HTMLCollection
date: 2018-05-05 11:08:03
updated: 2018-05-05 15:42:18
tags: DOM对象
categories: DOM对象的学与记
copyright: '(c) 2018-present, WenKang Lin'
---

`NodeList` 是一系列页面节点的集合，相比较于 `HTMLCollection` 的一系列页面元素的集合，从它们字面上的含义也都可以看出两者的不同。前者节点不仅仅包括元素节点，还包括文本节点、属性节点、注释节点等，而后者质保卡元素节点。

<!-- more -->

也就是说，`NodeList` 的每一项都是 `Node` 对象类型，而 `HTMLCollection` 的每一项是 `HTMLElement` 对象类型。例如：

```js
const childNodes = document.body.childNodes;
const children = document.body.children;

console.log(childNodes instanceof NodeList); // true
console.log(children instanceof HTMLCollection); // true
console.log(childNodes.item(0) instanceof Node); // true
console.log(children.item(0) instanceof HTMLElement); // true
```

## 节点类型

一般来说，判断节点的类型，是通过节点的 `nodeType` 属性来判断，常用的节点类型有：

| 节点类型 | nodeType的值 | 常量                |
| -------- | ------------ | ------------------- |
| 元素节点 | 1            | Node.ELEMENT_NODE   |
| 属性节点 | 2            | Node.ATTRIBUTE_NODE |
| 文本节点 | 3            | Node.TEXT_NODE      |
| 注释节点 | 8            | Node.COMMENT_NODE   |

```js
console.log(document.body.nodeType === 1); // true
console.log(document.body.nodeType === Node.ELEMENT_NODE); // true
```

### NodeList 和 HTMLCollection的相似点

* 类数组数据结构类型，都可通过 `length` 获取集合的长度。
* 都可通过数组下标的形式，或者 `item` 来获取集合的某一项。并且，数组下标若为负数或超过集合长度，那么返回 `undefined`；相同情况下，通过 `item` 访问返回 `null`。

类数组结构，可通过一系列方式使用数组遍历：

```js
const childNodes = document.body.childNodes;
const children = document.body.children;

// 通过es6的Array.from，转化为数组
Array.from(childNodes);
Array.from(children)

// 通过es6的数组解构赋值，转化为数组
[...childNodes];
[...children];

// 通过apply或call
Array.prototype.slice.call(childNodes, 0);
Array.prototype.forEach.call(children, () => {});
```

都可通过数组下标或者 `item` 来访问集合的某一项：

```js
console.log(childNodes[-1]); // undefined
console.log(children[-1]); // undefined
console.log(childNodes.item(childNodes.length + 1)); // null
console.log(children.item(-1)); // null
```

### NodeList 和 HTMLCollection的不同点

* 开头讲到的，`NodeList` 集合包括所有的节点，即包括了元素。
* `NodeList`虽然是类数组，但本身支持 `forEach`, `keys`, `values`, `entries`方法。
* `HTMLCollection` 有一个 `namedItem` 的方法，参数为字符串，用于查找集合中存在元素的 `id` 和字符串相等的元素。

`NodeList` 中的 `keys`, `values`, `entries`方法，返回的是一个迭代器，这和数组的方法不太一样。

```js
const childNodes = docuemnt.body.childNodes;

childNodes.forEach(node => console.log(node));

// 使用for...of遍历迭代器，或者next
// key是无符号的整数，0....n
for (const key of childNodes.keys()) {
  console.log(key);
}

for (const node of childNodes.values()) {
  console.log(node);
}
```

`namedItem` 方法，在 MDN 中解释是：

> Returns the specific node whose ID or, as a fallback, name matches the string specified by name. Matching by name is only done as a last resort, only in HTML, and only if the referenced element supports the name attribute. Returns null if no node exists by the given name.

上面描述中，不但可以通过元素的 `id` 来查找，也可以通过 `name` 属性来查找，前提是元素支持该属性。然而，本人亲自在 chrome 浏览器中试了一下，查找支持 `name` 属性的 `input`，不起作用。可能是不同浏览器存在差异性。

另外，`namedItem` 方法返回的结果在不同的浏览器下也存在[差异性](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection#Browser_compatibility)。

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/NodeList
> https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection
