---
title: js事件的学与记：addEventListener
date: 2018-05-06 21:34:34
updated: 2018-05-08 16:39:04
tags: js事件
categories: [JavaScript, js事件的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

不得不说，`addEventListener` 是一个非常常用的 api，可能也都不陌生。但今天补知识点的时候，发现并没有掌握的非常全面。所以，这里我要好好的补一补关于它的知识点了。

<!-- more -->

使用语法：

> target.addEventListener(type, listener[, options]);
> target.addEventListener(type, listener[, useCapture]);

接下来，我将详细地对 listener 和 options 进行说明。

## listener 监听器

使用过 `addEventListener` 的人会觉得这有什么好说的，不就是一个回调函数吗？之前我也是这么觉得，但今天补完相关知识点之后，才发现 listener 还有一种用法。

listener 这个参数可以有以下几种形式：

* 函数类型。大家用的比较多的回调函数。
* 对象类型。是的，没错，可以是一个对象，如空对象。但重点是，如果该对象实现了[EventListner][eventlistener]接口，也是可以正常的注册监听事件的。
* null 值，即可以为空。

{% jsfiddle y0n69wtL js,html,result dark %}

## options

根据上面的使用语法，第三个参数可以是 useCapture，一个布尔值，表示是否在事件捕获阶段触发事件，默认值为 false，即默认在事件冒泡阶段触发事件。

除此之外，第三个参数还可以是一个详细的选项 options，一个对象，其中可供选择的属性有：

* **capture**，作用同 useCapture。
* **once**，一个布尔值，表示注册的监听事件 listener 是否只执行一次，执行一次后被注销，默认为 false。
* **passive**，一个布尔值，表示是否禁用 `event.preventDefault()`。默认值为 false。如果值为 true，并且监听事件内部调用了 `preventDefault`，那么将会发出警告。

前面两个选项比较容易理解，这里重点说明一下 `passive`，以及它的作用。

总结一句话，`passive` 的作用是**提升页面滑动的流畅度**。如果我们在 touchstart 事件调用 `preventDefault` 会怎样呢？这时页面会禁止，不会滚动或缩放。那么问题来了：浏览器无法预先知道一个监听器会不会调用 `preventDefault`，它需要等监听器执行完后，再去执行默认行为，而监听器执行是要耗时的，这样就会导致页面卡顿。

> 当你触摸滑动页面时，页面应该跟随手指一起滚动。而此时你绑定了一个 touchstart 事件，你的事件大概执行 200 毫秒。这时浏览器就犯迷糊了：如果你在事件绑定函数中调用了 preventDefault，那么页面就不应该滚动，如果你没有调用 preventDefault，页面就需要滚动。但是你到底调用了还是没有调用，浏览器不知道。只能先执行你的函数，等 200 毫秒后，绑定事件执行完了，浏览器才知道，“哦，原来你没有阻止默认行为，好的，我马上滚”。此时，页面开始滚。

而且 Chrome 做了统计：

> 在 Android 版 Chrome 浏览器的 touch 事件监听器的页面中，80% 的页面都不会调用 preventDefault 函数来阻止事件的默认行为。在滑动流畅度上，有 10% 的页面增加至少 100ms 的延迟，1% 的页面甚至增加 500ms 以上的延迟。

也就是说，当浏览器等待执行事件的默认行为时，大部分情况是白等了。如果 Web 开发者能够提前告诉浏览器：“我不调用 preventDefault 函数来阻止事件事件行为”，那么浏览器就能快速生成事件，从而提升页面性能。

这也正是 `passive` 的作用。我们可以通过传递 `passive` 为 true 来明确告诉浏览器，事件处理程序不会调用 `preventDefault` 来阻止默认滑动行为。

在 Chrome 浏览器中，如果发现耗时超过 100 毫秒的非 `passive` 的监听器，会在 DevTools 里面警告你设置 `{ passive: true }`。

对于那些不支持 `passive`，或者只支持第三个参数为布尔类型的浏览器来说，如果冒然设置第三个参数为 `options` 对象格式的话，会隐式的把它转换为 true，即 `useCapture` 为 true。这可能不是我们想要看到的，所以可以通过以下这段代码，做一个 polyfill：

```js
let supportPassive = false;

try {
  const opts = Object.defineProperty({}, 'passive', {
    get() {
      supportPassive = true;
    }
  });
  window.addEventListener('testPassive', null, opts);
  window.removeEventListener('testPaiive', null, opts);
} catch (err) {}

target.addEventListener(
  'touchstart',
  handler,
  supportPassive ? { passive: true } : false
);
```

上面的代码原理是：如果浏览器支持第三个参数为 `options` 对象格式的话，在注册自定义事件 `testPassive`时，那么肯定会读取参数 `opts` 中的 `passive` 属性，那么就会触发预先设置好的 `getter`，使得 `supportPassive = true`。这样的话，就可以判断浏览器是否支持 `passive` 了。

## 注册多个事件

相比较于用 `on + eventType` 的形式来全局注册事件，例如 `onClick`，`addEventListener` 的好处是可以对同一个事件类型注册多个事件监听器。另外，**重复注册一个相同的事件监听器，也只会执行一次**。

```js
const handler = () => console.log('page clicked');

// 虽然下面注册了两次click事件，但由于事件监听器handler是同一个，所以只会执行一次。
window.addEventListener('click', handler);
window.addEventListener('click', handler);
```

## 事件注销

> target.removeEventListener(type, listener[, options]);
> target.removeEventListener(type, listener[, useCapture]);

可以通过 `removeEventListener` 来注销之前注册的监听事件，但需要满足以下三点条件，才能成功注销事件：

* target 必须存在，并且之前注册过这种类型的事件。
* 需要被删除的 listener 事件监听器必须和注册的事件监听器是同一个。这种情况下，需要把之前注册的事件存储为一个变量，否则直接使用匿名函数，肯定不是同一个事件监听器。
* 如果注册事件的同时设置了 `useCapture` 或者 `options`，那么注销事件时，也必须带上相同的这些参数，否则将不会注销成功。

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener<br> https://github.com/WICG/EventListenerOptions/blob/gh-pages/explainer.md<br> https://zhuanlan.zhihu.com/p/24555031<br>

[eventlistener]: https://developer.mozilla.org/en-US/docs/Web/API/EventListener
