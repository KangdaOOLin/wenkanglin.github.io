---
title: hexo学习笔记
date: 2018-04-15 18:26:34
updated: 2018-04-21 20:07:19
tags: [hexo]
categories:
description: 关于hexo的学习笔记
copyright: (c) 2018-present, WenKang Lin
---

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
