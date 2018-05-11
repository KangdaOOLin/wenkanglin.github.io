---
title: JavaScript面试题的学与记：DOM树遍历
date: 2018-05-11 15:27:31
updated: 2018-05-11 15:43:30
tags: [JavaScript面试题, 算法]
categories: [面试题, JavaScript面试题的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

DOM 树遍历，可以说是多叉树遍历的典型案例了。接下来记录一下：

* 深度优先遍历的递归实现。
* 深度优先遍历的非递归实现。
* 广度优先遍历的递归实现。
* 广度优先遍历的非递归实现。

<!-- more -->

假设，我们的页面结构如下：

```html
<body>
  <div>
    <p>213</p>
    <p>
      <span>112</span>
      <a href="#">no link</a>
    </p>
    <span>test</span>
    <div>
      <a href="#">no link too</a>
    </div>
    <a href="#">no link too too</a>
  </div>
</body>
```

## 深度优先遍历的递归实现

```js
const dfsRecursive = (node, nodeList = [node]) => {
  if (!node) {
    return nodeList;
  }
  [].forEach.call(node.children, child => {
    nodeList.push(child);
    dfsRecursive(child, nodeList);
  });
  return nodeList;
};

// [body, div, p, p, span, a, span, div, a, a, script]
console.log(dfsRecursive(document.body));
```

## 深度优先遍历的非递归实现

```js
const dfs = (node, nodeList = []) => {
  if (!node) {
    return nodeList;
  }
  const stack = [node];
  while (stack.length) {
    node = stack.pop();
    nodeList.push(node);
    for (let i = node.children.length - 1; i >= 0; i--) {
      stack.push(node.children[i]);
    }
  }
  return nodeList;
};

// [body, div, p, p, span, a, span, div, a, a, script]
console.log(dfs(document.body));
```

## 广度优先遍历的递归实现

```js
const bfsRecursive = (node, nodeList = [], queue = [node]) => {
  if (!node) {
    return nodeList;
  }
  node = queue.shift();
  nodeList.push(node);
  [].forEach.call(node.children, child => queue.push(child));
  queue.length && bfsRecursive(node, nodeList, queue);
  return nodeList;
};

// [body, div, script, p, p, span, div, a, span, a, a]
console.log(bfsRecursive(document.body));
```

## 广度优先遍历的非递归实现

```js
const bfs = (node, nodeList = []) => {
  if (!node) {
    return nodeList;
  }
  const queue = [node];
  while (queue.length) {
    node = queue.shift();
    nodeList.push(node);
    [].forEach.call(node.children, child => queue.push(child));
  }
  return nodeList;
};

// [body, div, script, p, p, span, div, a, span, a, a]
console.log(bfs(document.body));
```

## 总结

深度优先遍历使用栈，广度优先遍历使用队列。
