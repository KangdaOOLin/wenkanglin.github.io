---
title: JavaScript面试题的学与记：二叉树遍历
date: 2018-05-10 18:54:01
updated: 2018-05-11 01:10:08
tags: [JavaScript面试题, 算法]
categories: [面试题, JavaScript面试题的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

不论是二叉树，还是多叉树，遍历的种类一般为：

* 深度优先遍历（Depth-First-Search，简称 DFS）
* 广度优先遍历（Breadth-First-Search，简称 BFS）

其中，二叉树的遍历，又可以对深度优先遍历进一步划分：

* 前序遍历（Pre-Order Traversal）
* 中序遍历（In-Order Traversal）
* 后序遍历（Post-Order Traversal）

这里，总结一下二叉树遍历所有遍历方式中递归/非递归实现和原理。递归实现方式逻辑相对更加简单，但性能比起非递归实现更差，非递归实现逻辑相对来说更加复杂难懂。

<!-- more -->

首先，这里先定义一个二叉树结构的对象，下面的遍历都是基于这个对象去遍历：

```js
const binaryTree = {
  nodeValue: 6,
  left: {
    nodeValue: 5,
    left: {
      nodeValue: 4,
      right: {
        nodeValue: 8
      }
    },
    right: {
      nodeValue: 3,
      left: {
        nodeValue: 7
      }
    }
  },
  right: {
    nodeValue: 2,
    right: {
      nodeValue: 1
    }
  }
};
```

## 前序遍历

### 前序遍历的递归实现

1. 若当前节点存在，则执行步骤 2；否则当前遍历结束。
2. 访问当前节点。
3. 若当前节点的左子树存在，则递归遍历左子树。
4. 若当前节点的右子树存在，则递归遍历右子树。
5. 结束遍历。

```js
// nodeList依次存储遍历的节点值
const DLRWithRecursive = (node, nodeList = []) => {
  if (node.nodeValue) {
    nodeList.push(node.nodeValue);
    // 依次递归遍历节点的左子树
    node.left && DLRWithRecursive(node.left, nodeList);
    // 然后依次递归遍历节点的右子树
    node.right && DLRWithRecursive(node.right, nodeList);
  }
  return nodeList;
};

console.log('Pre-Order Traversal:', DLRWithRecursive(binaryTree));
```

遍历结果：

```bash
Pre-Order Traversal: [ 6, 5, 4, 8, 3, 7, 2, 1 ]
```

### 前序遍历的非递归实现

1. 初始化一个栈，并把根节点压入栈中。
2. 当栈非空时，循环执行步骤 3、4、5；否则栈为空，结束遍历。
3. 从栈中取一个值，并访问该节点。
4. 若该节点的右子树存在，则将该右子树压入栈中。
5. 若该节点的左子树存在，则将该左子树压入栈中。

```js
const DLR = (node, nodeList = []) => {
  const stack = [node];
  while (stack.length) {
    const item = stack.pop();
    nodeList.push(item.nodeValue);
    item.right && stack.push(item.right);
    item.left && stack.push(item.left);
  }
  return nodeList;
};

console.log('Pre-Order Traversal:', DLR(binaryTree));
```

## 中序遍历

### 中序遍历的递归实现

1. 若当前节点存在，则执行步骤 2；否则当前遍历结束。
2. 若当前节点的左子树存在，则递归遍历左子树。
3. 访问当前节点。
4. 若当前节点的右子树存在，则递归遍历右子树。
5. 结束遍历。

```js
const LDRWithRecursive = (node, nodeList = []) => {
  if (node.nodeValue) {
    node.left && LDRWithRecursive(node.left, nodeList);
    nodeList.push(node.nodeValue);
    node.right && LDRWithRecursive(node.right, nodeList);
  }
  return nodeList;
};

console.log('In-Order Traversal:', LDRWithRecursive(binaryTree));
```

这里，你会发现，对于前序遍历、中序遍历，还有后面的后序遍历的递归实现思路是很相似的。唯一的不同就是：前序遍历把“访问当前节点”的步骤放到了左子树、右子树的前面；中序遍历则把它放到了左子树、右子树的中间；后序遍历则把它放到了最后。

所以，对于递归遍历来说，只需要针对遍历顺序把“访问当前节点”的这行代码顺序调整一下，即可实现遍历。

### 中序遍历的非递归实现

1. 初始化一个栈，把根节点压入栈中，并标记当前节点（item）。
2. 当栈非空时，执行步骤 3；否则栈为空，结束遍历。
3. 若当前节点（item）存在左子树且没有被标记过，则执行步骤 4，否则执行步骤 5。
4. 对当前节点（item）进行标记；然后将当前节点的左子树赋值给当前节点（item = item.left）；最后将当前节点（item）压入栈中。回到步骤 3。
5. 清理当前节点（item）的标记，从栈中取出一个节点并赋值给当前节点（item = stack.pop），并访问该节点；若当前节点（item）的右子树存在，则将该右子树压入栈中。回到步骤 3。

```js
const LDR = (node, nodeList = []) => {
  const stack = [node];
  let item = node;
  while (stack.length) {
    if (item.left && !item.marked) {
      item.marked = true;
      stack.push((item = item.left));
      continue;
    }
    item.marked && delete item.marked;
    item = stack.pop();
    nodeList.push(item.nodeValue);
    item.right && stack.push(item.right);
  }
  return nodeList;
};

console.log('In-Order Traversal:', LDR(binaryTree));
```

## 后序遍历

### 后序遍历的递归实现

1. 若当前节点存在，则执行步骤 2；否则当前遍历结束。
2. 若当前节点的左子树存在，则递归遍历左子树。
3. 若当前节点的右子树存在，则递归遍历右子树。
4. 访问当前节点。
5. 结束遍历。

```js
const LRDWithRecursive = (node, nodeList = []) => {
  if (node.nodeValue) {
    node.left && LRDWithRecursive(node.left, nodeList);
    node.right && LRDWithRecursive(node.right, nodeList);
    nodeList.push(node.nodeValue);
  }
  return nodeList;
};

console.log('Post-Order Traversal:', LRDWithRecursive(binaryTree));
```

### 后序遍历的非递归实现

1. 初始化一个栈，把根节点压入栈中，并标记当前节点（item）。
2. 当栈非空时，执行步骤 3；否则栈为空，结束遍历。
3. 若当前节点（item）存在左子树且没有被标记过，则执行步骤 4；若左子树被标记过，右子树存在且没有被标记过，则执行步骤 5；否则执行步骤 6。
4. 对当前节点（item）进行标记；然后将当前节点的左子树赋值给当前节点（item = item.left）；最后将当前节点（item）压入栈中。回到步骤 3。
5. 对当前节点（item）标记为 right；然后将当前节点的右子树赋值给当前节点（item = item.right）；最后将当前节点（item）压入栈中。回到步骤 3。
6. 清理当前节点（item）的标记，从栈中取出一个节点并访问该节点；然后将当前栈顶节点赋值给当前节点（item）；最后回到步骤 3。

```js
const LRD = (node, nodeList = []) => {
  const stack = [node];
  let item = node;
  while (stack.length) {
    if (item.left && !item.marked) {
      item.marked = true;
      stack.push((item = item.left));
      continue;
    }
    if (item.right && item.marked !== 'right') {
      item.marked = 'right';
      stack.push((item = item.right));
      continue;
    }
    const target = stack.pop();
    target.marked && delete target.marked;
    nodeList.push(target.nodeValue);
    item = stack.length ? stack[stack.length - 1] : null;
  }
  return nodeList;
};

console.log('Post-Order Traversal:', LRD(binaryTree));
```

## 广度优先遍历

## 广度优先遍历的递归实现

```js
const BFSWithRecursive = (node, nodeList = [node.nodeValue], queue = [node]) => {
  node.left && queue.push(node.left);
  node.right && queue.push(node.right);
};
```

## 广度优先遍历的非递归实现
