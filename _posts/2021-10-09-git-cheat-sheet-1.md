---
title: Git Cheat Sheet 1
tags: [Git]
---

## 集中式 vs 分布式
Linus 一直痛恨的 CVS 及 SVN 都是集中式的版本控制系统，而 Git 是分布式版本控制系统，集中式和分布式版本控制系统有什么区别呢？

先说集中式版本控制系统，版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。

那分布式版本控制系统与集中式版本控制系统有何不同呢？首先，分布式版本控制系统没有“中央服务器”，每个人的电脑上都是一个完整的版本库。你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。

和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库。集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但这个服务器的作用仅仅是用来方便“交换”大家的修改，没有它大家也一样干活，只是交换修改不方便而已。

## 创建版本库
版本库又名仓库（Repository），可以简单理解成一个目录，这个目录里面的所有文件都可以被 Git 管理起来，每个文件的修改、删除，Git 都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

选择一个合适的地方，创建一个空目录：
```
$ mkdir learngit
$ cd learngit
```

通过 `git init` 命令把这个目录变成 Git 可以管理的仓库：
```
$ git init
```

所有的版本控制系统，其实只能跟踪**纯文本**文件的改动，Git 也不例外。图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化。

因为文本是有编码的，比如中文有常用的 `GBK` 编码，日文有 `Shift_JIS` 编码，如果没有历史遗留问题，建议使用标准的 `UTF-8` 编码。所有语言使用同一种编码，既没有冲突，又被所有平台所支持。

## 版本回退
用 `git log` 命令查看代码提交的历史记录：
```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

`git log` 命令显示从最近到最远的提交日志。如果嫌输出信息太多，可以加上 `--pretty=oneline`：
```
$ git log --pretty=oneline
1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) append GPL
e475afc93c209a690c39c13a46716e8fa000c366 add distributed
eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0 wrote a readme file
```

指定回到过去/未来的某个版本：
```
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL
```

Git 提供了一个命令 `git reflog` 用来记录你的每一次命令：
```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

## 工作区和暂存区
工作区（Working Directory）：电脑里能看到的目录。工作区有一个隐藏目录 `.git`，这个不算工作区，而是 Git 的版本库。Git 的版本库里存了很多东西，其中最重要的就是称为 stage（或者叫 index）的暂存区，还有 Git 为我们自动创建的第一个分支 master，以及指向 master 的一个指针 HEAD。

`git add` 命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行 `git commit` 就可以一次性把暂存区的所有修改提交到分支。

## 撤销修改
`git checkout -- <file>` 可以丢弃工作区的修改（让这个文件回到最近一次 `git commit` 或 `git add` 时的状态）：
```
$ git checkout -- readme.txt
```

现在假定是凌晨3点，你不但写了一些胡话，还 `git add` 到暂存区了。`git reset HEAD <file>` 可以把暂存区的修改撤销掉（unstage），重新放回工作区：
```
$ git reset HEAD readme.txt
Unstaged changes after reset:
M	readme.txt
```

`git reset` 命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用 `HEAD` 时，表示最新的版本。再用 `git status` 查看一下，现在暂存区是干净的，工作区有修改。还记得如何丢弃工作区的修改吗？

现在，假设你不但改错了东西，还从暂存区提交到了版本库，怎么办呢？还记得**版本回退**一节吗？可以回退到上一个版本。不过，这是有条件的，就是你还没有把自己的本地版本库推送到远程。

## 删除文件
第一种情况，确实要从版本库中删除该文件。那就用命令 `git rm` 删掉，并且 `git commit`：
```
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

另一种情况是删错了。因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
```
$ git checkout -- test.txt
```

`git checkout` 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416)
