---
title: Git Cheat Sheet 3
tags: [Git]
---

## 分支管理策略
通常，合并分支时，如果可能，Git 会用 `Fast forward` 模式，但这种模式下，删除分支后，会丢掉分支信息。如果要强制禁用 `Fast forward` 模式，Git 就会在 `merge` 时生成一个新的 `commit`，这样，从分支历史上就可以看出分支信息。

准备合并 `dev` 分支，请注意 `--no-ff` 参数，表示禁用`Fast forward`：
```
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

合并后，我们用 `git log` 看看分支历史：
```
$ git log --graph --pretty=oneline --abbrev-commit
*   e1e9c68 (HEAD -> master) merge with no-ff
|\  
| * f52c633 (dev) add merge
|/  
*   cf810e4 conflict fixed
...
```

## 实际开发中的分支管理
首先，`master` 分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；干活都在 `dev` 分支上，也就是说，`dev` 分支是不稳定的，到某个时候，比如1.0版本发布时，再把 `dev` 分支合并到 `master` 上，在 `master` 分支发布1.0版本；每个人都在 `dev` 分支上干活，每个人都有自己的分支，时不时地往 `dev` 分支上合并就可以了。

## Bug 分支
当你接到修复一个 bug 的任务时，很自然地，你想创建一个分支 `issue-101` 来修复它，但是，等等，当前正在 `dev` 上进行的工作还没有提交。并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该 bug，怎么办？Git 还提供了一个 `stash` 功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
```
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

首先确定要在哪个分支上修复 bug，假定需要在 `master` 分支上修复，就从 `master` 创建临时分支：
```
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

你可以多次 stash，恢复的时候，先用 `git stash list` 查看，然后恢复指定的 stash，用命令：
```
$ git stash apply stash@{0}
```

同样的 bug，要在 `dev` 上修复，我们只需要把 `4c805e2 fix bug 101` 这个提交所做的修改“复制”到 `dev` 分支。Git 提供了一个 `cherry-pick` 命令，让我们能复制一个特定的提交到当前分支：
```
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

既然可以在 `master` 分支上修复 bug 后，在 `dev` 分支上可以“重放”这个修复过程，那么直接在 `dev` 分支上修复 bug，然后在 `master` 分支上“重放”行不行？当然可以，不过你仍然需要 `git stash` 命令保存现场，才能从 `dev` 分支切换到 `master` 分支。

## Feature 分支
开发完新 feature，切回 `dev`，准备合并。就在此时，接到上级命令，因经费不足，新功能必须取消。虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：
```
$ git branch -D feature-vulcan
```

## 多人协作
用 `git remote -v` 显示远程库的信息：
```
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```

上面显示了可以抓取和推送的 `origin` 的地址。如果没有推送权限，就看不到 `push` 的地址。

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git 就会把该分支推送到远程库对应的远程分支上：
```
$ git push origin dev
```

并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？
- `master` 分支是主分支，因此要时刻与远程同步；
- `dev` 分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug 分支只用于在本地修复 bug，有时就没必要推到远程了；
- feature 分支是否推到远程，取决于你是否和你的伙伴合作在上面开发。

现在，你的伙伴要在 `dev` 分支上开发，就必须创建远程 `origin` 的 `dev` 分支到本地，于是他用这个命令创建本地 `dev` 分支：
```
$ git checkout -b dev origin/dev
```

```
$ git push origin dev
```

你的伙伴已经向 `origin/dev` 分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送。你推送失败，因为你的伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git 提示我们，先用 `git pull` 把最新的提交从 `origin/dev` 抓下来，然后，在本地合并，解决冲突，再推送：
```
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```

`git pull` 也失败了，原因是没有指定本地 `dev` 分支与远程 `origin/dev` 分支的链接，根据提示，设置 `dev` 和 `origin/dev` 的链接：
```
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

通常来说，多人协作的工作模式可以这样概括：
- 首先，可以试图用 `git push origin <branch-name>` 推送自己的修改；
- 如果推送失败，则因为远程分支比你的本地更新，需要先用 `git pull` 试图合并；
- 如果合并有冲突，则解决冲突，并在本地提交；
- 没有冲突或者解决冲突后，再用 `git push origin <branch-name>` 推送成功。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600/1216289527823648)
