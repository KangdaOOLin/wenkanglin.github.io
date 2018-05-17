---
title: JavaScript面试题的学与记：setTimeout和setInterval
date: 2018-05-12 08:37:24
updated: 2018-05-17 17:41:52
tags: JavaScript面试题
categories: [面试题, JavaScript面试题的学与记]
copyright: (c) 2018-present, WenKang Lin
---

关于两者的使用，这里不再赘述。而这里主要是想说明一下递归调用 `setTimeout` 和直接使用 `setInterval`，这两者表现有什么区别？例如：

```js
const fn = () => {
  // ...
  console.log('call fn');
  // ...
};

const handler = () => {
  fn();
  setTimeout(handler, 1000);
};

setTimeout(handler, 1000);
setInterval(fn, 1000);
```

<!-- more -->

## 区别

如上面代码所示，递归调用 `setTimeout` 也能够达到重复执行的效果。除此之外，和 `setInterval` 是否有本质上的区别呢？

答案是肯定有区别的。**`setTimeout` 能确保相邻两次的执行间隔不会被浪费，而 `setInterval` 可能会存在浪费某部分执行间隔的情况。**

上面的区别描述有些拗口，不是很容易理解。这里举一个形象的例子。由于厕所位置有限，而且每个位置最多只有一个人上，并假设每个人上厕所的时间都是 10 分钟。有些同事 A 们会在旁边一直等，而有些同事 B 们则会在 10 分钟之后再过来看看有没有位置。如果突然其中一个人上厕所的时间是 13 分钟，超过了 10 分钟。A 们因为一直在旁边等着，所以位置能够马上的被下一个同事使用；但 B 们在第一个 10 分钟，过来看过发现还是没有位置，然后走了，接着在下一个 10 分钟后才会过来。这时，就会产生 7 分钟的空闲浪费。这里，A 们就是 `setTimeout`，B 们就是 `setInterval`。

```js
setTimeout(handler, 1000);
setInterval(fn, 1000);
```

假设 `fn` 执行时间需要 1200 ms，0 - 1000 内执行第一次 `fn`。然后 1000 ms后，`setInterval` 发现前一次的 `fn` 还在执行，那么本次 1000 - 2000 的 `fn` 就会被忽略掉，然后在 2000 ms后再来。而 `setTimeout` 则会在 1200 ms后，立即执行下一次。

所以，存在超时情况下，选择使用递归 `setTimeout` 是最好的。并且，递归的过程中，也可以动态的修改下一步执行的时间间隔。

## 关于 this 的陷阱

我们知道，一个函数中关于 `this` 指向的问题。一般来说，在非严格模式下，指向的是 `window` 全局对象；严格模式下值是 `undefined`。但是，在 `setTimeout` 和 `setInterval` 回调函数中，**`this` 指向总是不变，指向的是 `window` 对象**（箭头函数除外）。

```js
'use strict';
setTimeout(function() {
  console.log(this === window); // true
});
```

并且，也无法通过 `apply` 或 `call` 或 `bind` 来直接指定 `setTimeout` 和 `setInterval` 函数执行的上下文。注意，这里说的是 `setTimeout` 和 `setInterval` 函数本身，而不是它们第一个参数（回调函数）。

```js
const obj = {
  name: 'obj name',
  handler() {
    console.log(this.name);
  }
};

// 由于回调函数中this指向的是window对象，所以等价于window.name
// obj.handler.bind(obj)能够正确输出
setTimeout(obj.handler);

// 但不能通过call直接作用于setTimeout本身来获得正确的输出
// 这里程序报错 Uncaught TypeError: Illegal invocation
setTimeout.call(obj, obj.handler);
```

## 相关阅读

> http://qingbob.com/difference-between-settimeout-setinterval/
> https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout
> https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval
