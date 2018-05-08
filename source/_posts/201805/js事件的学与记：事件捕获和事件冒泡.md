---
title: js事件的学与记：事件捕获和事件冒泡
date: 2018-05-06 15:53:33
updated: 2018-05-08 10:56:17
tags: [js事件, js面试题]
categories: [js事件的学与记, js面试题的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

面试的时候，或者自我基础总结的时候，经常会遇到以下的问题：

* 什么是事件捕获和事件冒泡？
* 事件捕获和事件冒泡顺序的优先级？
* 事件捕获和事件冒泡的使用场景是什么？
* 事件捕获单独的使用场景是什么？为什么会同时存在这两种事件传播的机制？

<!-- more -->

## 事件流程

在说到事件捕获和冒泡前，我们需要先了解一个事件的触发到底经历了哪些？

![事件触发流程](/images/eventflow.jpg)

如上图所示，一般事件触发的流程会经历这三个阶段：

* **捕获阶段**。事件对象通过目标的祖先从页面根元素传播到事件目标的父节点。
* **目标阶段**。事件对象到达事件目标的阶段，即 `event.target`。如果事件不冒泡，那么事件对象将在此阶段完成后停止。
* **冒泡阶段**。事件对象以捕获阶段相反的顺序，从目标的父节点开始，向上传播到页面根元素结束。

在事件触发的过程中，我们也可以通过[Event.eventPhase][event.eventphase]来得到当前事件执行的阶段。

```js
el.addEventListener('click', event => {
  console.log('current pahse:', event.eventPhase);
});
```

到这里为止，我们应该知道了一开始问题一的事件捕获和事件冒泡是啥了，它们用相反的事件传播顺序触发事件流。而且，根据上面的三个阶段，也应该知道问题二的答案。

> 问题：事件捕获和事件冒泡顺序的优先级？<br>
> 答案：先经历事件捕获，再经历事件冒泡。

## 设置事件触发的阶段

我们可以通过 `addEventListener` 绑定一个事件，并决定事件是在捕获阶段触发呢，还是在冒泡阶段触发。默认是冒泡阶段触发，可通过设置第三个参数为 `true` 来设置事件在捕获阶段触发。

{% jsfiddle xvbevmj5 js,html,css,result dark %}

```js
addEventListener(type, handler, useCapture);
addEventListener(type, handler, { capture });
```

另外，**阻止事件捕获和冒泡**，都可以通过 `event.stopPropagation()` 来达到阻止的效果。正确来说，若处于事件捕获阶段，然后调用它，那么事件流将在目标阶段结束，而不会执行事件冒泡的流程。若处于事件冒泡阶段，然后调用它，那么将阻止事件继续向上冒泡的过程。

## 事件代理或事件委托

事件代理和事件委托指的是同一个玩意，是一个很常见的事件冒泡使用场景。而且由于作为一个典型的事件性能优化手段，可供参考的资料有很多。所以，这里不做具体的描述。一个字，懒 -\_-||。

## 事件捕获的使用场景

接下来，我们重点讨论一下事件捕获的使用场景，这也是为什么为同时存在捕获和冒泡两种事件传播方式的原因吧。

个人目前想的到的使用场景主要有以下两个：

* 一个是**非冒泡事件的事件代理**。类似于 `focus`、`blur`、`mouseenter`等不能冒泡的事件类型，不能通过基于事件冒泡的事件代理方式来处理。那么可以通过基于事件捕获的事件代理来处理。但是，个人经验有限，实在是想不出具体实际的场景。
* 因为事件捕获阶段先于事件冒泡阶段，那么是不是可以在事件捕获阶段，充当一个拦截器，或过滤器的作用，当满足条件时，才继续执行事件冒泡阶段。这样的话，具体的判断逻辑放在事件捕获阶段，具体的事件处理逻辑放在事件冒泡阶段，职责分明，充当不同的角色。

至于，为什么会同时存在这两种事件传播的机制？这个问题撇开使用场景这方面原因，更多的应该是历史发展的原因。当初 Netscape Navigator 4 的事件传播方式是近似于事件捕获，而 IE 4 的正相反。最后，W3C DOM 标准，从设计思路上是更接近 IE 的设计的，即事件冒泡，只是加入了事件捕获阶段。

## 其它一些补充

上面讲到“非冒泡事件”，那么怎么来判断一个事件能否冒泡呢？答案是可通过 [event.bubbles][event.bubbles]。

```js
input.addEventListener('focus', event => {
  // focus事件不能冒泡
  console.log(event.bubbles); // false
});
```

`event.stopPropagation()` 方法兼容 `IE9+`，低于这个版本的 IE 浏览器，应使用 [event.cancelBubble][event.cancelbubble]。

```js
el.addEventListener('click', event => {
  if (typeof event.stopPropagation === 'function') {
    event.stopPropagation();
  } else {
    event.cancelBubble = true;
  }
});
```

## 相关阅读

> http://javascript.info/bubbling-and-capturing<br> https://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture

[event.eventphase]: https://developer.mozilla.org/en-US/docs/Web/API/Event/eventPhase
[event.bubbles]: https://developer.mozilla.org/en-US/docs/Web/API/Event/bubbles
[event.cancelbubble]: https://developer.mozilla.org/en-US/docs/Web/API/Event/cancelBubble
