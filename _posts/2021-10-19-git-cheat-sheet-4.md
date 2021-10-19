---
title: Git Cheat Sheet 4
tags: [Git]
---

## Rebase
分支变成了这样：
```
$ git log --graph --pretty=oneline --abbrev-commit
* d1be385 (HEAD -> master, origin/master) init hello
*   e5e69f1 Merge branch 'dev'
|\  
| *   57c53ab (origin/dev, dev) fix env conflict
| |\  
| | * 7a5e5dd add env
| * | 7bd91f1 add new env
| |/  
* |   12a631b merged bug fix 101
|\ \  
| * | 4c805e2 fix bug 101
|/ /  
* |   e1e9c68 merge with no-ff
|\ \  
| |/  
| * f52c633 add merge
|/  
*   cf810e4 conflict fixed
```

从实际问题出发，看看怎么把分叉的提交变成直线。

现在我们本地分支比远程分支超前3个提交：
```
$ git log --graph --pretty=oneline --abbrev-commit
*   e0ea545 (HEAD -> master) Merge branch 'master' of github.com:michaelliao/learngit
|\  
| * f005ed4 (origin/master) set exit=1
* | 582d922 add author
* | 8875536 add comment
|/  
* d1be385 init hello
...
```

输入命令 `git rebase`。再用 `git log` 看看：
```
$ git log --graph --pretty=oneline --abbrev-commit
* 7e61ed4 (HEAD -> master) add author
* 3611cfe add comment
* f005ed4 (origin/master) set exit=1
* d1be385 init hello
...
```

原本分叉的提交现在变成一条直线了。最后，通过 `push` 操作把本地分支推送到远程。远程分支的提交历史也是一条直线。总结：`rebase` 操作可以把本地未 `push` 的分叉提交历史整理成直线。

## 标签管理
切换到需要打标签的分支上：
```
$ git checkout master
```

打一个新标签：
```
$ git tag v1.0
```

查看所有标签：
```
$ git tag
v1.0
```

默认标签是打在最新提交的 commit 上的。如果要给历史 commit 打上标签，找到历史提交的 `<commit id>`，然后打上就可以了：
```
$ git tag v0.9 f52c633
```

查看标签详细信息：
```
$ git show v0.9
commit f52c63349bc3c1593499807e5c8e972b82c8f286 (tag: v0.9)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:56:54 2018 +0800

    add merge

diff --git a/readme.txt b/readme.txt
...
```

创建带有说明的标签：
```
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

删除一个标签：
```
$ git tag -d v0.1
Deleted tag 'v0.1' (was f15b0dd)
```

推送某个标签到远程：
```
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
 * [new tag]         v1.0 -> v1.0
```

一次性推送全部尚未推送到远程的本地标签：
```
$ git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
 * [new tag]         v0.9 -> v0.9
```

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
```
$ git tag -d v0.9
Deleted tag 'v0.9' (was f52c633)
```

然后，从远程删除：
```
$ git push origin :refs/tags/v0.9
To github.com:michaelliao/learngit.git
 - [deleted]         v0.9
```

## 忽略特殊文件
不需要从头写 `.gitignore` 文件，GitHub 已经为我们准备了各种配置文件，见 [github/gitignore](https://github.com/github/gitignore)。

忽略文件的原则：
- 忽略操作系统自动生成的文件，比如缩略图等；
- 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如 Java 编译产生的 `.class` 文件；
- 忽略带有敏感信息的配置文件，比如存放口令的配置文件。

有些时候，你想添加一个文件到 Git，但发现添加不了，原因是这个文件被 `.gitignore` 忽略了。如果你确实想添加该文件，可以用 `-f` 强制添加到 Git：
```
$ git add -f App.class
```

或者你发现，可能是 `.gitignore` 写得有问题，需要找出来到底哪个规则写错了：
```
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
```

在 `.gitignore` 中注明这些例外的文件：
```
# 排除所有.开头的隐藏文件:
.*
# 排除所有.class文件:
*.class

# 不排除.gitignore和App.class:
!.gitignore
!App.class
```

## 自定义 Git
让 Git 显示颜色：
```
$ git config --global color.ui true
```

配置别名：
```
$ git config --global alias.st status
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
```

配置 Git 的时候，加上 `--global` 是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。每个仓库的 Git 配置文件都放在 `.git/config` 文件中。当前用户的 Git 配置文件放在用户主目录下的一个隐藏文件 `.gitconfig` 中。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/900062620154944)
