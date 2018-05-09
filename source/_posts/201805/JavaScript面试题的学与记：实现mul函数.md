---
title: JavaScript面试题的学与记：实现mul函数
date: 2018-05-09 21:43:52
updated: 2018-05-09 23:31:30
tags: JavaScript面试题
categories: [面试题, JavaScript面试题的学与记]
copyright: '(c) 2018-present, WenKang Lin'
---

实现一个函数 `mul`，满足下面结果：

```js
mul(2); // 2
mul(2)(3); // 2 * 3 = 6
mul(2)(3)(4); // 2 * 3 * 4 = 24
mul(2)(3)(4)(5); // 2 * 3 * 4 * 5 = 120
mul(2)(3)(4)(5)(...n); // 2 * 3 * 4 * 5 * ... * n
```

考察对函数式编程中“柯里化”的概念，以及对 `toString` 的妙用。

<!-- more -->

```js
const mul = num1 => {
  let result = num1;
  const curry = num2 => {
    result *= num2;
    return curry;
  };
  curry.toString = () => result;
  return curry;
};

mul(2).toString(); // 2
mul(2)(3).toString(); // 6
mul(2)(3)(4).toString(); // 24
mul(2)(3)(4)(5).toString(); // 120
```

其实，不一定要用 `toString`，随便定义一个属性，是一个能够返回乘积结果的函数就 ok。当然，如果有一种方法，能够取消掉显式调用 `toString` 那就最好不过了。
