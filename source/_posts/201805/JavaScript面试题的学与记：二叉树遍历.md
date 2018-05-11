---
title: JavaScript面试题的学与记：二叉树遍历
date: 2018-05-10 18:54:01
updated: 2018-05-11 14:44:43
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

首先，这里先定义一个二叉树结构的对象，下面的遍历都是基于这个对象去遍历。

```js
const binaryTree = {
  nodeValue: 1,
  left: {
    nodeValue: 2,
    left: {
      nodeValue: 4,
      left: {
        nodeValue: 8
      },
      right: {
        nodeValue: 9
      }
    },
    right: {
      nodeValue: 5
    }
  },
  right: {
    nodeValue: 3,
    left: {
      nodeValue: 6
    },
    right: {
      nodeValue: 7,
      left: {
        nodeValue: 10
      },
      right: {
        nodeValue: 11
      }
    }
  }
};
```

最终，根据上面的二叉树对象，形成如下图所示的二叉树。

![二叉树](/images/binary-tree.jpg)

## 前序遍历

遍历结果：

```bash
Pre-Order Traversal: [ 1, 2, 4, 8, 9, 5, 3, 6, 7, 10, 11 ]
```

### 前序遍历的递归实现

1. 若当前节点存在，则执行步骤 2；否则当前遍历结束。
2. 访问当前节点，记录到 nodeList。
3. 若当前节点的左子树存在，则递归遍历左子树。
4. 若当前节点的右子树存在，则递归遍历右子树。
5. 结束遍历。

```js
// nodeList依次存储遍历的节点值
// 为了代码简洁，在参数列表中进行了初始化nodeList
const preOrderRecursive = (node, nodeList = []) => {
  if (node) {
    nodeList.push(node.nodeValue);
    node.left && preOrderRecursive(node.left, nodeList);
    node.right && preOrderRecursive(node.right, nodeList);
  }
  return nodeList;
};

console.log('Pre-Order Traversal:', preOrderRecursive(binaryTree));
```

### 前序遍历的非递归实现

1. 初始化一个栈，并把根节点压入栈中。
2. 当栈非空时，循环执行步骤 3、4、5；否则栈为空，结束遍历。
3. 从栈中取一个值，并访问该节点。、，记录到 nodeList。
4. 若该节点的右子树存在，则将该右子树压入栈中。
5. 若该节点的左子树存在，则将该左子树压入栈中。

```js
const preOrder = (node, nodeList = []) => {
  if (!node) {
    return nodeList;
  }
  const stack = [node];
  while (stack.length) {
    node = stack.pop();
    nodeList.push(node.nodeValue);
    node.right && stack.push(node.right);
    node.left && stack.push(node.left);
  }
  return nodeList;
};

console.log('Pre-Order Traversal:', preOrder(binaryTree));
```

## 中序遍历

遍历结果：

```bash
In-Order Traversal: [ 8, 4, 9, 2, 5, 1, 6, 3, 10, 7, 11 ]
```

### 中序遍历的递归实现

1. 若当前节点存在，则执行步骤 2；否则当前遍历结束。
2. 若当前节点的左子树存在，则递归遍历左子树。
3. 访问当前节点。
4. 若当前节点的右子树存在，则递归遍历右子树。
5. 结束遍历。

```js
const inOrderRecursive = (node, nodeList = []) => {
  if (node) {
    node.left && inOrderRecursive(node.left, nodeList);
    nodeList.push(node.nodeValue);
    node.right && inOrderRecursive(node.right, nodeList);
  }
  return nodeList;
};

console.log('In-Order Traversal:', inOrderRecursive(binaryTree));
```

### 中序遍历的非递归实现

1. 初始化一个栈。若栈不为空，或当前节点存在，则执行步骤 2。
2. 若当前节点存在，执行步骤 3，否则执行步骤 4。
3. 把当前节点压入栈，并将左子树作为当前节点。回到步骤 2。
4. 从栈顶取值作为当前节点，并记录到 nodeList，最后把右子树作为当前节点。回到步骤 2。

```js
const inOrder = (node, nodeList = []) => {
  if (!node) {
    return nodeList;
  }
  const stack = [];
  while (stack.length || node) {
    if (node) {
      stack.push(node);
      node = node.left;
    } else {
      node = stack.pop();
      nodeList.push(node.nodeValue);
      node = node.right;
    }
  }
  return nodeList;
};

console.log('In-Order Traversal:', inOrder(binaryTree));
```

## 后序遍历

遍历结果：

```bash
Post-Order Traversal: [ 8, 9, 4, 5, 2, 6, 10, 11, 7, 3, 1 ]
```

### 后序遍历的递归实现

1. 若当前节点存在，则执行步骤 2；否则当前遍历结束。
2. 若当前节点的左子树存在，则递归遍历左子树。
3. 若当前节点的右子树存在，则递归遍历右子树。
4. 访问当前节点。
5. 结束遍历。

```js
const postOrderRecursive = (node, nodeList = []) => {
  if (node) {
    node.left && postOrderRecursive(node.left, nodeList);
    node.right && postOrderRecursive(node.right, nodeList);
    nodeList.push(node.nodeValue);
  }
  return nodeList;
};

console.log('Post-Order Traversal:', postOrderRecursive(binaryTree));
```

### 后序遍历的非递归实现

1. 初始化一个栈，把根节点压入栈中，使用 tmp 记录当前栈顶的节点。执行步骤 2。
2. 若栈不为空，tmp 记录当前栈顶节点。
3. 若 tmp 存在左子树，则把左子树节点压入栈，并回到步骤 2。
4. 若 tmp 存在右子树，则把右子树节点压入栈，并回到步骤 2。
5. 从栈顶取值，并记录到 nodeList，并把 tmp 作为当前节点。回到步骤 2。

```js
const postOrder = (node, nodeList = []) => {
  if (!node) {
    return nodeList;
  }
  const stack = [node];
  let tmp;
  while (stack.length) {
    tmp = stack[stack.length - 1];
    if (tmp.left && node !== tmp.left && node !== tmp.right) {
      stack.push(tmp.left);
    } else if (tmp.right && node !== tmp.right) {
      stack.push(tmp.right);
    } else {
      nodeList.push(stack.pop().nodeValue);
      node = tmp;
    }
  }
  return nodeList;
};

console.log('Post-Order Traversal:', postOrder(binaryTree));
```

## 广度优先遍历

遍历结果：

```bash
BFS Traversal: [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11 ]
```

递归实现和非递归实现原理一致，大体如下：

1. 初始化一个队列，并把根节点放入队列。执行步骤 2。
2. 若队列不为空，执行步骤 3。
3. 从队列取值并作为当前节点，并记录到 nodeList。执行步骤4。
4. 若当前节点左子树存在，放入队列；若右子树存在，放入队列。回到步骤 3。

### 广度优先遍历的递归实现

```js
const bfsRecursive = (node, nodeList = [], queue = [node]) => {
  if (!node) {
    return nodeList;
  }
  node = queue.shift();
  nodeList.push(node.nodeValue);
  node.left && queue.push(node.left);
  node.right && queue.push(node.right);
  queue.length && bfsRecursive(node, nodeList, queue);
  return nodeList;
};

console.log('BFS Traversal:', bfsRecursive(binaryTree));
```

### 广度优先遍历的非递归实现

```js
const bfs = (node, nodeList = []) => {
  if (!node) {
    return nodeList;
  }
  const queue = [node];
  while (queue.length) {
    node = queue.shift();
    nodeList.push(node.nodeValue);
    node.left && queue.push(node.left);
    node.right && queue.push(node.right);
  }
  return nodeList;
};

console.log('BFS Traversal:', bfs(binaryTree));
```

## 总结

对于前序遍历、中序遍历、后序遍历的递归实现思路是很相似的。唯一的不同就是：前序遍历把“访问当前节点”的步骤放到了左子树、右子树的前面；中序遍历则把它放到了左子树、右子树的中间；后序遍历则把它放到了最后。

所以，对于递归遍历来说，只需要针对遍历顺序把“访问当前节点”的这行代码顺序调整一下，即可实现遍历。

但对于非递归遍历来说，还是要慢慢的琢磨才能加深自己的理解。
