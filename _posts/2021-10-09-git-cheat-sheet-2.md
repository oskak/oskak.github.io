---
title: Git Cheat Sheet 2
tags: [Git]
---

## 添加远程库
根据 GitHub 的提示，在本地的 git 仓库下运行命令：
```
$ git remote add origin <远程仓库地址>
```

添加后，远程库的名字就是 origin，这是 Git 默认的叫法。

把本地库的所有内容推送到远程库上：
```
$ git push -u origin master
```

## 解除本地和远程的绑定关系
先用 `git remote -v` 查看远程库信息：
```
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
```

然后根据名字删除，比如删除 `origin`：
```
$ git remote rm origin
```

## 创建与合并分支
一开始的时候，`master` 分支是一条线，Git 用 `master` 指向最新的提交，再用 `HEAD` 指向 `master`，就能确定当前分支，以及当前分支的提交点。当我们创建新的分支，例如 `dev` 时，Git 新建了一个指针叫 `dev`，指向 `master` 相同的提交，再把 `HEAD` 指向 `dev`，就表示当前分支在 `dev` 上。

Git 创建一个分支很快，因为除了增加一个 `dev` 指针，改改 `HEAD` 的指向，工作区的文件都没有任何变化。

假如我们在 `dev` 上的工作完成了，就可以把 `dev` 合并到 `master` 上。Git 怎么合并呢？最简单的方法，就是直接把 `master` 指向 `dev` 的当前提交，就完成了合并。

创建 `dev` 分支，然后切换到 `dev` 分支：
```
$ git checkout -b dev
Switched to a new branch 'dev'

or

$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

用 `git branch` 命令查看当前分支：
```
$ git branch
* dev
  master
```

`dev` 分支的工作完成，我们就可以切换回 `master` 分支，并把 `dev` 分支的工作成果合并到 `master` 分支上：
```
$ git checkout master
Switched to branch 'master'

$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

`git merge` 命令用于合并指定分支到当前分支。注意到上面的 `Fast-forward` 信息，Git 告诉我们，这次合并是“快进模式”，也就是直接把 `master` 指向 `dev` 的当前提交，所以合并速度非常快。

合并完成后，就可以放心地删除 `dev` 分支了：
```
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

最新版本的 Git 提供了新的 `git switch` 命令来切换分支。

创建并切换到新的 `dev` 分支：
```
$ git switch -c dev
```

直接切换到已有的 `master` 分支：
```
$ git switch master
```

## 解决冲突
现在，`master` 分支和 `feature1` 分支各自都分别有新的提交。这种情况下，Git 无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突：
```
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Git 告诉我们，`readme.txt` 文件存在冲突，必须手动解决冲突后再提交。`git status` 也可以告诉我们冲突的文件。Git 用 `<<<<<<<`，`=======`，`>>>>>>>` 标记出不同分支的内容。

用带参数的 `git log` 可以看到分支的合并情况：
```
$ git log --graph --pretty=oneline --abbrev-commit
*   cf810e4 (HEAD -> master) conflict fixed
|\  
| * 14096d0 (feature1) AND simple
* | 5dc6824 & simple
|/  
* b17d20e branch test
* d46f35e (origin/master) remove test.txt
* b84166e add test.txt
* 519219b git tracks changes
* e43a48b understand how stage works
* 1094adb append GPL
* e475afc add distributed
* eaadf4e wrote a readme file
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/900004111093344)
