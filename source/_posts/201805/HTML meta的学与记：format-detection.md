---
title: HTML meta的学与记：format-detection
tags: HTML meta
categories: [HTML, HTML meta的学与记]
copyright: '(c) 2018-present, WenKang Lin'
date: 2018-05-03 16:41:20
updated: 2018-05-09 22:30:00
---

在做移动端页面的过程中，可能都遇到过 `moble safari` 会自动识别一串数字为电话号码，并自动生成拨号的链接。如下图所示：

![拨号链接](/images/telephone-no.jpg)

<!-- more -->

这可能并不是我们所期望的，需要关闭这个功能。

```html
<meta name="format-detection" content="telephone=no">
```

如上面代码所示，通过 `format-detection` 这个 meta 字段，在 content 设置 `telephone=no`，来禁用 `mobile safari` 自动识别电话号码的功能。

另外，除了电话号码外，邮箱、地址、日期、链接这些字段也同样可以开启格式化保护：

```html
<meta name="format-detection" content="date=no">
<meta name="format-detection" content="address=no">
<meta name="format-detection" content="email=no">
<meta name="format-detection" content="url=no">
```

或者直接在 content 属性中，添加多个值：

```html
<meta name="format-detection" content="telephone=no, address=no, email=no, date=no, url=no">
```
