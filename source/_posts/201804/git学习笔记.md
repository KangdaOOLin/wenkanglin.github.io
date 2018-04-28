---
title: git学习笔记
date: 2018-04-28 18:10:32
updated: 2018-04-28 18:14:59
tags: git
categories: git
copyright: '(c) 2018-present, WenKang Lin'
---

发现经常遗忘常用的`git`命令操作，这里留一个笔记。

<!-- more -->

## 一些名词解释

* `untracked files`字面意思是未跟踪的文件，表示之前从未提交过的文件，例如新建的文件。
* `unstaged files`表示那些之前提交过的文件，但目前已修改但未添加到提交列表里面的文件，即没有`git add`。
* 暂存区，指的是通过`git add`操作，把文件存放到暂存区。通过`git commit`把暂存区的所有修改提交到分支，而那些不在暂存区的修改并不能提交。

## git init

通过`git init`把当前的项目设置为 git repo，生成`.git`文件夹。

## git add

通过`git add`把文件添加到暂存区，如果想要添加一个已被忽略的文件，可以通过参数`-f`强制性添加忽略的文件，例如：`git add -f readme.txt`。

## git commit -a

`git commit -a -m`相当于`git add .`和`git commit -m`，表示添加更改的文件并提交。但注意的是，对于`untracked files`来说无效。

## git status

`git status`可以查看当前状态，列出哪些是`untracked files`状态的文件，哪些是`not staged`的文件，哪些是`changes to be commited`（已添加但未提交的文件）。例如：

```shell
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test2.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt
```

这里表示，`test.txt`文件已添加(staged)，但未提交(commit)；`test2.txt`文件是未跟踪的文件，即新的文件；`readme.txt`是已修改，但未添加(unstaged)。

## git reset HEAD <file>

`git reset HEAD`可以重置所有已添加(staged)的文件的状态为 unstaged，即取消`git add`。`<file>`表示可以指定特定的文件名，可以是多个文件名，用空格隔开。

## git checkout -- <file>

`git checkout -- <file>`可以重置未添加(unstaged)但已修改(modified)的文件为未修改的状态。如果是已添加的文件(staged)，请使用`git reset HEAD`。可以使用`git checkout .`重置所有文件。

* 一种是自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
* 一种是已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。而添加（新增）的文件，并不能还原。

`git checkout src/*`可以使用类似于 glob 形式，把一个文件夹下面的修改文件重置。

## git diff

使用`git diff`查看和对比所有修改的内容。可以使用`git diff <file>`查看对比特定的文件内容。

## git log

使用`git log`查看所有的提交记录，包括 commit id，提交信息，日期和作者，可以添加参数`--pretty=oneline`，简化显示提交记录，只单行显示 commit id 和提交信息。

`git log --graph`可以查看分支合并图，从而基本了解分支提交合并的情况。

`git log --abbrev-commit`会简化 commit id 成 6 位的形式。

## git reset

前面提到通过`git reset HEAD`来重置所有的 staged 状态的文件为 unstaged 状态。其中`HEAD`表示当前版本，也就是最新的提交。上一个版本就是`HEAD@{1}`，上上个版本就是`HEAD@{2}`，上 100 个版本就是`HEAD@{100}`。

所以，可以通过`git reset HEAD@{1} --hard`来回退到上一个版本，回退到上几个版本可以以此类推。

这样，就可以理解前面的`git reset HEAD`，表示回退到当前版本，可以起到`staged -> unstaged`的作用了。

除了通过`HEAD`来回退版本之外，还可以通过具体的 commit id 来回退到某一个版本，例如`git reset --hard 3f23444cef0eeb9d32d1a9aded9808ac52b93589`。如果在回退到上一个版本的情况下，想要恢复到新版本的话，可以通过 commit id 来操作。

## git reflog

和`git log`不同的是，`git reflog`可以查看所有提交记录的 commit id，而`git log`不能查看回退到上一个版本后最新版本的 commit id。

## git rm

通过`git rm`删除文件并提交到暂存区，注意的是物理删除，会直接从磁盘删除该文件。并且，要注意的是，如果在删除之前有修改的内容没有提交，删除之后恢复文件的话，只能恢复最新版本，修改的内容并不能还原。

不物理删除，可以通过`git rm -r --cached <file>`。该指令可以用在：要忽略之前已提交过的文件，这种情况下。所有的文件，可以通过`git rm -r --cached .`。

## 关联远程仓库

`git remote add origin git@xxxxx.git`，即把本地的仓库关联到远程仓库。

## git upstream

`git branch --set-upstream local_branch origin/origin_branch`可以把本地分支`local_branch`关联到远程分支`origin_branch`。也可以通过简写`-u`，例如：`git branch -u local_branch origin/origin_branch`。

`git branch -u origin/dev`，会把当前所在的本地分支关联到远程分支上（前提是远程分支已经存在）。

`git push -u origin master`，表示推送的同时，关联远程分支。

`git branch --unset-upstream`取消当前分支的 upstream，即取消关联。

`git branch --upset-upstream <branch>`取消特定分支的 upstream。

## 查看 upstream

可以在`.git/config`文件下面查看，或者`git remote show origin`。

## git branch

`git branch`可以查看所有本地分支，展示如下（\*符号表示当前分支）

```shell
* dev
  master
```

`git branch -a`可以查看所有的分支，包括本地和远程分支。

`git branch 分支名`可以创建新的本地分支。

`git branch -d 分支名`删除本地分支。

`git branch -D 分支名`强行删除一个没有被合并过的分支。

`git push -d origin dev`删除一个远程分支。

## git checkout 分支

`git checkout dev`，把`HEAD`指向分支`dev`，即改变当前分支为`dev`（前提是`dev`分支存在）。

`git checkout -b test`，创建分支`test`，并指向它，相当于：

```shell
git branch test
git checkout test
```

## git merge

`git merge dev`，把本地分支`dev`的内容合并到当前分支（把 HEAD 指针指向`dev`）。

`git merge origin/dev`，把远程分支`dev`的内容合并到当前分支。（建议使用这条指令合并，而不是上条）。

上面的合并，一般都是`Fast forward`模式，合并速度比较快。如果合并过程中，发现当前分支和被合并分支有一个文件都修改过，会进行智能合并，这个过程中，就不是`Fast forward`模式了。如果存在交叉情况，则会提示合并冲突，可以通过`git status`了解情况。

产生冲突的情况下，解决冲突后，继续`git add`和`git commit`。

`git merge origin/dev --no-commit`合并时不自动提交。

## Fast forward

该合并模式下，合并分支之后把分支删除，则会丢掉分支信息。例如：`master`分支创建一个新的分支`dev`，通过`Fast forward`把`dev`分支合并到`master`中，然后删除`dev`分支，那么通过`git log --graph`就会查看不了`dev`分支的情况了。

可以使用`--nof-ff`，让不使用`Fast forward`模式，或者直接在 git 全局配置中默认禁用该模式：

```shell
git config branch.master.mergeoptions "--no-ff"
```

## git pull 和 git fetch

两者不同之处在于，`git pull`相当于`git fetch` + `git merge`。

## git stash

`git stash`把当前分支的所作修改存储起来，这样就可以去解决临时的其他问题。

`git stash list`查看所有的的临时存储。

`git stash apply`恢复最近入栈的存储，而且存储记录不会删除。

`git stash apply stash@{n}`恢复指定的存储，同样存储记录不会删除。

`git stash drop stash@{n}`删除一个存储记录，不加上`stash@{n}`的话，默认是删除`stash@{0}`。

`git stash pop`恢复最近入栈的存储，并删除存储记录。

`git stash pop stash@{n}`恢复指定存储，并删除。

注意，可以某些命令行，`stash@{n}`需要用双引号包裹起来。

## git tag

通过`git tag v1.0.0`创建一个标签，使用`git tag`查看所有的标签。前面操作是对当前的 commit 打上标签。如果需要针对之前的提交打上标签，可以使用`git tag v1.0.0 commit-id`来完成。

通过`git show v1.0.0`来查看标签的信息。

通过`git tag -a v1.0.0 -m "version 1.0.0 released" commit-id`来创建带有描述的标签，其中`-m`指定说明文字。

通过`git tag -d v1.0.0`来删除本地标签。

通过`git push origin v1.0.0`把标签推送到远程，通过`git push origin --tags`一次推送所有未推送过的本地标签。

删除一个远程标签，可以先删除本地标签，然后通过`git push origin :refs/tags/v1.0.0`删除远程的标签。

## git config

`git config --global user.name wenkanglin`诸如此操作来设置 git 全局。然后可以通过`git config --list`列出所有已有的配置和配置值。

其中全局配置文件存放在`~用户名/.gitconfig`，项目的配置文件存放在项目的`.git/config`文件下。

## git alias

可以给`git status`中的`status`配置别名，其他命令也可以。通过`git config --global alias.st status`给`git status`配置了一个别名：`git st`。

```shell
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
```

## git show commit-id

通过`git show 46508f9`来显示某次提交的具体信息（文件的增删改）。

## git import from existing git repo

```shell
git remote rename origin old-origin
git remote add origin git@hszn1507-0029.sz.kingdee.net:yfe/test.git
git push -u origin --all
git push -u origin --tags
```

## 只提交修改/删除的文件

通过`git add . --update`或者`git add . -u`，只会把修改的或者删除的文件提交到暂存区，新添加的文件不能被添加。

## git add --interactive 交互界面

通过`git add --interactive`或者`git add -i`进入交互界面，然后比如选择 2，或者 u，然后可以挑选你需要提交到缓存区的文件的序号。

## 显示某个文件的提交历史记录

通过`git log --patch <file>`或者`git log -p <file>`来显示某一个文件的提交记录。

## git clone

克隆仓库，并存放在指定路径下：`git clone @url <path>`

克隆某个远程分支：`git clone -b branch @url`或者`git clone --branch branch @url`。
