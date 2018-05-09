---
title: JavaScript事件的学与记：浏览器环境下注册自定义事件
date: 2018-05-08 16:54:41
updated: 2018-05-09 22:32:34
tags: JavaScript事件
categories: [JavaScript, JavaScript事件的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

通过本文章来了解一下，怎么在浏览器环境下，通过注册自定义事件。一般分为 3 个步骤：

* 首先，通过[event][event]或者[CustomEvent][customevent]注册一个自定义事件。
* 然后，通过[addEventListener][addeventlistener]订阅自定义事件。
* 最后，通过[dispatchEvent][dispatchevent]触发自定义事件。

<!-- more -->

## Event

> event = new Event(typeArg, eventInit);

`eventInit` 作为一个可选配置对象，有以下三个属性：

* **bubbles**：表示事件是否可以冒泡，默认为 false。
* **cancelable**：表示事件是否可以被取消掉，默认为 false。可取消的话，则可以使用 `preventDefault` 阻止默认事件；否则报错。
* **composed**：和影子 DOM 有关的选项，不是很了解。默认为 false。

通过 `Event` 构造函数实例化一个自定义事件，例如：

```js
const event = new Event('xiba');
```

当然也可以实例化一个更加具体的 `Event`，例如 `MouseEvent`、`InputEvent`等等：

```js
const event = new MouseEvent('xiba');
```

另外，虽然也可以通过[document.createEvent][createevent]创建一个自定义事件对象：

```js
const event = document.createEvent('MouseEvent');
```

但是，必须通过 [event.initEvent][initevent]进行初始化，否则不能触发该自定义事件，报错信息如下：

> Uncaught DOMException: Failed to execute 'dispatchEvent' on 'EventTarget': The event provided is uninitialized.

因为，`event.initEvent` 处于被弃用的状态，所以不建议使用 `document.createEvent`。

## CustomEvent

> event = new CustomEvent(typeArg, customEventInit);

作为 `Event` 的一个原型继承的对象，使用方式唯一不太一样的在于 `customEventInit`，比 `Event` 多了一个属性：

* **detail**：默认为 null，可用于事件的自定义数据，能在事件监听器里面通过 `event.detail` 访问自定义数据。

相比于`Event`，建议通过 `CustomEvent` 去自定义事件。虽然功能相差不大，但后者无疑更具备语义化。

## dispatchEvent

在调用这个方法之前，**必须要订阅自定义事件**，才能确保触发的自定义事件能够被监听到。

```js
const event = new CustomEvent('test');
element.addEventListener('test', () => {
  console.log('test custom event');
});
element.dispatchEvent(event);
```

`dispatchEvent` 的返回值是一个布尔值，表示是否已经触发。如果触发的事件 `cacelable` 为 true，并且执行监听事件时，执行了 `preventDefault`，那么 `dispatchEvent` 的返回值为 false；否则为 true。

```js
const cancelled = !element.dispatchEvent(event);

if (cancelled) {
  console.log('event cancelled');
} else {
  console.log('event not cancelled');
}
```

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/Event<br> https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent<br> https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent<br> https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events

[event]: https://developer.mozilla.org/en-US/docs/Web/API/Event
[customevent]: https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent
[addeventlistener]: https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener
[dispatchevent]: https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent
[initevent]: https://developer.mozilla.org/en-US/docs/Web/API/Event/initEvent
[createevent]: https://developer.mozilla.org/en-US/docs/Web/API/Document/createEvent
