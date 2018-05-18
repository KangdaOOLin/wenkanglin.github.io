---
title: JavaScript事件的学与记：stopPropagation和stopImmediatePropagation
date: 2018-05-18 16:19:06
updated: 2018-05-18 16:54:34
tags: [JavaScript事件, JavaScript面试题]
categories:
  - [JavaScript, JavaScript事件的学与记]
  - [面试题, JavaScript面试题的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

`stopPropagation` 大家都比较熟悉，但 `stopImmediatePropagation` 是什么呢？和前者有啥区别呢？面试官如是问道。

> Prevents other listeners of the same event from being called.

上面是 MDN 上关于 `stopImmediatePropagation` 的描述，翻译过来就是“阻止同事件类型的其它监听器被触发”。

<!-- more -->

根据实际的例子，理解起来可能会更加容易。假设如下代码：

```html
<body>
  <button id="btn">click me to stop propagation</button>
</body>
```

```js
const btn = document.querySelector('#btn');

btn.addEventListener('click', event => {
  window.alert('btn click 1');
});

btn.addEventListener('click', event => {
  window.alert('btn click 2');
});

document.body.addEventListener('click', () => {
  window.alert('body click');
});
```

点击按钮，body 也会触发点击事件。若想要在点击按钮时，阻止 body 点击事件触发，可以使用 `stopPropagation`。这里按钮注册了两个点击事件，随便给其中一个事件使用 `stopPropagation` 都可以阻止冒泡。

```js
const btn = document.querySelector('#btn');

btn.addEventListener('click', event => {
  window.alert('btn click 1');
  event.stopPropagation();
});

btn.addEventListener('click', event => {
  window.alert('btn click 2');
});

document.body.addEventListener('click', () => {
  window.alert('body click');
});
```

如上所示，点击按钮时，body 就不会再触发点击事件了，但按钮的另一个点击事件还是会触发。所以，**若想保持事件触发的唯一性**，那么就可以使用 `stopImmediatePropagation` 来阻止。这样，按钮的另一个点击事件也就不会再触发了。

```js
const btn = document.querySelector('#btn');

btn.addEventListener('click', event => {
  window.alert('btn click 1');
  event.stopImmediatePropagation();
});

btn.addEventListener('click', event => {
  window.alert('btn click 2');
});

document.body.addEventListener('click', () => {
  window.alert('body click');
});
```

{% jsfiddle 3dbwuL8m html,js,result dark %}

## 总结

`stopImmediatePropagation` 既能阻止事件向父元素冒泡，也能阻止元素同事件类型的其它监听器被触发。而 `stopPropagation` 只能实现前者的效果。

## 相关阅读

> https://developer.mozilla.org/en-US/docs/Web/API/Event/stopPropagation
> https://developer.mozilla.org/en-US/docs/Web/API/Event/stopImmediatePropagation
