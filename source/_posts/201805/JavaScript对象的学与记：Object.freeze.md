---
title: JavaScript对象的学与记：Object.freeze
date: 2018-05-02 17:11:53
updated: 2018-05-09 22:32:59
tags: JavaScript对象
categories: [JavaScript, JavaScript对象的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

众所周知，`es5`中新增了`Object.freeze`用来“冻结”对象的属性，阻止对象新增属性，修改属性的值，来达到类似于`writable: false`和`configurable: false`的效果。但是，要注意的是，对象中嵌套对象并不能被冻结。

<!-- more -->

例如，存在如下对象：

```js
const obj = {
  age: 23,
  type: {
    name: 'lwk'
  }
};

Object.freeze(obj);
```

其中，`obj.type`依旧可以添加属性，修改属性的值：

```js
obj.type.name = 'lwk2';
obj.newAttr = '001';

console.log(obj.type); // { name: 'lwk2', newAttr: '001' }
```

那么有什么办法，来真正实现对象的“冻结”呢？

![思考](/images/sikao.jpg)

首先，先不妨思考一下，使用属性描述符`writable`和`configurable`是否可行？

答案是不可行，因为属性描述符虽然可以防止属性值被修改或删除，但不能阻止新增对象属性。

那么，`Object.seal`呢？

答案也是不行，因为`Object.seal`可以阻止新增对象属性，或者删除属性，但不能阻止修改属性值。

最终，我所能想到的是，递归调用`Object.freeze`，例如：

```js
const freezeObject = obj => {
  Object.freeze(obj);
  Object.keys(obj).forEach(key => {
    typeof obj[key] === 'object' && Object.freeze(obj[key]);
  });
};
```
