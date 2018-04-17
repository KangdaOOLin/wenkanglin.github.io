---
title: hexo学习笔记
date: 2018-04-15 18:26:34
updated: 2018-04-16 11:49:27
tags: [hexo]
categories:
description: 关于hexo的学习笔记
copyright: (c) 2018-present, WenKang Lin
---

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

## --config

若不想默认使用`_config.yml`的配置的话，可以通过`--config`指定另外的配置文件，支持`yml`和`json`格式。并且，`--config`支持指定多个配置文件，格式如下：

```bash
hexo serve --config config.json,config.yml
```

如上面的情况下，后面的配置`config.yml`的优先级更高，会覆盖前面`config.json`存在相同的配置。最后，会自动生成`_multiconfig.yml`文件。

:warning: 指定多个配置的情况下，符号`,`两边不能留下空格。

## layout

在`hexo`中，内置的`layout`有以下几种：

* **post**: 默认值。若不指定`layout`的话，新建的`.md`文件将会放在`_posts`文件夹下面。
* **draft**: 从字面意思很容易理解，作为一种“草稿”，用于文章未完成阶段，且不想部署上去。`.md`文件将会放在`_drafts`文件夹下面。
* **page**: 创建时，文件结构不同于上面，会以`title`的名称创建一个文件夹，路径如`<title>/index.md`。

可以修改默认的`layout`，只需配置`default_layout`，如设置默认为`draft`：

```yml
default_layout: draft
```

另外，`draft`草稿默认是不会被编译生成的。若需要展示草稿的话，需要配置`render_drafts`，如：

```yml
render_drafts: true
```

或者在执行`hexo server`或者`hexo generate`命令时，使用`--draft`全局选项，如：

```bash
# hexo s或者hexo serve或者hexo server是等价的
hexo serve --draft

# hexo g是hexo generate的简写
hexo g --draft
```

`layout`的值为`page`的情况下，假设`title`为`first-page`，生成的目录结构如下：

```shell
source
└── first-page
    └── index.md
```

## global options

常用的全局选项有以下：

* **--safe**: 禁用所用的插件和脚本，可用于排查因插件而产生的问题。
* **--debug**: 使用调试模式，在控制台显示日志信息，并生成`debug.log`日志文件。
* **--silent**: 使用静默模式，不会在控制台显示任何信息。
* **--draft**: 展示`_drafts`中的草稿。

## front-matter

`font-matter`的作用是展示博客文章的具体信息，放在`.md`文件的开头，信息包括：

* **layout**: [博客的`layout`](#layout)。
* **title**: 博客的标题。
* **date**: 博客创建日期。
* **updated**: 博客最近更新的日期。
* **comments**: 是否开启博客评论的功能。
* **tags**: 博客的标签。
* **categories**: 博客的类目。
* **permalink**: 博客链接格式，将会覆盖配置文件中`permalink`的设置。

`font-matter`结合`hexo`的主题，可以在博客网站中清晰展示一篇文章的信息。有`yml`和`json`两种格式，推荐使用`yml`格式：

```yml
---
title: My First Blog
date: 2018-04-14 15:30:32
updated: 2018-04-15 12:01:31
tags: [tag1, tag2, tag3]
categories:
- category1
- category2
- [category3, subCategory]
---
```

如上面所示，`tags`和`categories`若是使用`yml`格式的话，可以是数组格式，也可以是列表格式。

## help

通过`hexo help`获取命令和选项的列表。通过`hexo help <command>`获取具体某个命令的使用方式，例如：

```bash
hexo help publish
```

## new

```bash
hexo new [layout] <title> [options]
```

具体关于`layout`的信息，可以查看[layout 介绍](#layout)。指定创建的文章的`layout`，可以如下：

```bash
hexo new draft your-blog-title
```

:warning: 关于`title`字段，需要注意的是，多个值最好不要通过空格隔开，虽然可以成功创建，但创建的文件会取最后一个值。例如：

```bash
hexo new my first blog
```

上面的例子，创建的文件名称将会是`blog.md`，而不是`my-first-blog.md`。想要如预期处理，需要用字符串的形式。例如：

```bash
hexo new "my first blog"
```

关于`hexo new`命令中的`options`，有以下：

* **-p, --path**: 设置创建的文章的路径和名称。
* **-r, --replace**: 如果创建的文章已存在，是否覆盖？
* **-s, --slug**: 设置创建的文章的文件名称。
* 可添加自定义的选项，并会显示在[front-matter](#front-matter)。

其中，关于`--path`和`--slug`，很容易让人迷糊。两者主要的区别，根据下面的例子就知道了。

```bash
hexo new hello-world --path parent/child
```

上面的例子，文章最终创建的路径是`/source/_posts/parent/child.md`，文章标题是`hello-world`。

```bash
hexo new hello-world --slug parent/child
```

而上面的例子，文章最终创建的路径是`/source/_posts/parent-child.md`，文章标题是`hello-world`。

关于第四点自定义选项，假设执行如下命令：

```bash
hexo new hello-world --lang zh
```

将会生成`hello-world.md`，并且文件头部的`front-matter`将会显示`lang`的信息，可能如下：

```markdown
---
title: hello-world
date: 2018-04-15 14:45:20
lang: zh
---
```

## publish

```bash
hexo publish [layout] <filename>
```

通过上面的命令，把位于`_drafts`下的博客文件，发布到[layout](#layout)下面。其中若`layout`的值为`draft`，会被忽略，不会起到任何作用。如果未在`_drafts`文件夹下找到指定的`filename`，则报错。
