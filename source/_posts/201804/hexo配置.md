---
title: hexo配置
date: 2018-04-28 13:44:55
updated: 2018-04-28 18:13:43
tags: hexo
categories: hexo
copyright: '(c) 2018-present, WenKang Lin'
---

初次使用`hexo`配置自己的博客网站，接下来记录自己对于`hexo`配置作用的理解和说明。配合[hexo 文档](https://hexo.io/docs/)，一步一步配置自己的博客。

<!-- more -->

## language 和 timezone

国内的话，一般这样设置：

```yml
language: zh-CN
timezone: Asia/Shanghai
```

`language`字段可以配置多个值，例如：

```yml
language: [zh-CN, en]
```

另外，如果使用的`hexo`主题支持`i18n`的话，根据`language`的设置会自动切换相关的语言。

## permalink

若`permalink`中的某一个链接分割(segment)没有定义，那么值为`undefined`。比如：

```yml
permalink: :lang/:title/
```

上面示例中，显示的地址链接将会是`www.xxx.com/undefined/post-title`。

若`lang`定义但没有赋值，那么值会是`null`。比如：

```yml
permalink: :lang/:title/
permalink_defaults:
  lang:
```

上面示例中，显示的地址链接将会是`www.xxx.com/null/post-title`。

可以设置空字符串，例如：

```yml
permalink: :lang/:title/
permalink_defaults:
  lang: ''
```

上面的示例中，如果没有设置[front-matter](#front-matter)，那么显示的地址链接将会是`www.xxx.com/post-title`。可是，需要注意的是，通过`hexo generate`生成页面时，因为是根据`permalink`动态生成目录结构，此时`lang`为空字符串，文件夹名称会自动转化为`null`。

:warning: `permalink`的末尾必须带上`/`，否则：

* 通过`hexo serve`访问进去的不是博客页面，而是直接下载页面了。
* 通过`hexo generate`生成的不是`.html`文件，而是无后缀的文件。

## permalink_defaults

从作用上来讲，`permalink_defaults`的作用主要有：

* 设置`permalink`的默认值。
* 可添加自定义的`permalink`链接分割(segment)。

例如，添加自定义的`lang`字段：

```yml
permalink: :lang/:title
permalink_defaults:
  lang: zh
```

## skip_render

`skip_render`的主要作用是忽略渲染`source_dir`文件夹下面的被匹配文件，即在执行`hexo serve`或者`hexo generate`命令时，被匹配的文件不会被编译转化。根据文件的类型，会如下处理：

* 若是匹配到了`.md`文件，则会忽略它们，不进行渲染编译。
* 若是匹配到了其他文件，例如`*.html`文件，则会复制它们到`public_dir`，否则会编译渲染。

如果`skip_render`的值有`*`符号，则需要使用字符串的形式，否则报错。例如：

```yml
skip_render: '**/*.md'
```

如果`skip_render`的值是具体的路径名称，则不需要字符串。例如：

```yml
skip_render: _post/index.html
```

如果是多项值，可以是数组的形式，也可以是列表形式。例如：

```yml
skip_render: ['**/*.{md,html}', _post/test.md]
```

```yml
skip_render:
  - '**/*.{md,html}'
  - _post/test.md
```

:warning: 如果发现`skip_render`作用效果无效，可以执行`hexo clean`，然后再尝试。

## index_generator

首页配置，主要有：

* **path**: 设置首页的路径，默认为空字符串。
* **per_page**: 设置首页博客文章每页显示的数量，设置`0`，是禁用分页功能。若设置为负数，将不能正常显示页面。默认为`10`。
* **order_by**: 博客文章排序方式，默认值是`-date`，即按最近日期依次排列。

关于`path`，若设置不为空，例如：

```yml
index_generator:
  path: 'test'
```

那么，首页路径将会是`http://domain.com/test`。这时需要注意的是直接访问`http://domain.com`将会访问不了页面。

## deploy

```yml
deploy:
  type: git
  repo: git@github.com:wenkanglin/wenkanglin.github.io.git
  message: ':rocket: Site update on {{ now("YYYY-MM-DD HH:mm:ss") }}'
```

部署你的本地博客至远程仓库中，例如`git`。其中：

* **type**: 指的是远程仓库的类型。
* **repo**: 远程仓库的地址。
* **branch**: 远程仓库的分支，默认是`master`分支。
* **message**: 提交时的消息，作用同等于`git commit -m`。

:warning: 部署前根据类型需要安装依赖，例如`hexo-deployer-git`。
