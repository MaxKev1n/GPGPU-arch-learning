# Git

## git cherry-pick

```
git cherry-pick [--edit] [-n] [-m <parent-number>] [-s] [-x] [--ff]
          [-S[<keyid>]] <commit>…
git cherry-pick (--continue | --skip | --abort | --quit)
```

给定一个或多个已有的commit，应用每一个commit引入的更改，并为每个commit记录一个新的commit。这要求working tree是干净的（在HEAD commit之后没有修改）

如果无法明确如何进行修改，那么将会：

1. **当前分支**和**HEAD**的指针将会停留在上一次成功的commit
2. **CHERRY_PICK_HEAD**引用将会指向难以应用更改的commit
3. **对于干净应用的更改，其路径将会在index file和working tree中同时更新**
4. **对于冲突的路径，index file将会记录最多三个版本，在git-merge的“TRUE MERGE”部分中被记录。working tree files将会包含又conflict markers括起来的冲突描述**
5. **没有其他的修改被产生**

### options

- **-e ，--edit：git cherry-pick将会让你在提交之前修改commit message**
- **--cleanup=<mode>：这个选项明确commit message进入commit machinery之前将会被如何清理**
- **-x：当记录commit时，向原始的commit message添加一行"(cherry picked from commit ...)"用于指示该更改是由哪条commit cherry-picked。这仅能在没有冲突的cherry picks中使用。**
- **-m <parent-number>，--mainline <parent-number>：由于无法确定通常合并的哪一侧属于mainline，你无法cherry-pick一个合并。这个选项用于指定一个mainline中的parent number（从1开始）并且允许cherry-pick去replay相对于这个parent的更改。**
- **-n，--no-commit：这个flag使得应用必要的修改来cherry-pick每一个命名的commit到你的working tree和index时没有任何commit。除此以外，当这个option被使用时，你的idnex不需要必须与HEAD commit匹配。**
- **-ff：如果现在的HEAD与被cherry-pick的commit的parent相同，那么将对这次commit使用fast forward。**
- **--allow-empty-message：这个option允许一个空message的commit。**

### example

![img](./figures/v2-983c857c55fa7cbeedb3c497c5b19de7_1440w.png)





添加图片注释，不超过 140 字（可选）

```
git checkout master
git cherry-pick commit5
```

![img](./figures/v2-7019287acac6a136713dc92d114118dc_1440w.png)





添加图片注释，不超过 140 字（可选）

**参考资料：**

- [**Git - git-cherry-pick Documentation (git-scm.com)**](https://git-scm.com/docs/git-cherry-pick)
- [**git cherry-pick 教程 - 阮一峰的网络日志 (ruanyifeng.com)**](https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html)

## git rebase

Rebasing是一种将一系列的commits移动或者合并到一个新的base commit的处理。git通过创建一个新的commit并且将其应用到指定的base。

![img](./figures/v2-4e575e2aef8af0518e3ce3bb7ad5dc90_1440w.png)





添加图片注释，不超过 140 字（可选）

使用rebase的一个主要原因是为了维护一个线性的project history。

不要rebase那些已经push到public repository的commits，rebase会用新的commits代替旧的commits，这会使得你的project history看起来突然消失了一部分。

标准模式下的git rebase将会自动地将会自动接受当前分支的commits，并且将它们应用到passed branch的头部。

```
git rebase <base>
```

运行带有*-i* flag的git rebase将会进行interactive rebasing session。与盲目地将所有的commits移动到新的base不同，interactive rebasing会允许你在处理过程中调整个别commits。

```
git rebase --interactive <base>
```

这将会打开一个editor，允许你为每个将要被rebase的commits键入命令，这些命令确定每个单独的commits将会被如何转换到新的base。

```
pick 2231360 some old commit
pick ee2adc2 Adds new feature


# Rebase 2cf755d..ee2adc2 onto 2cf755d (9 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### Advanced rebase application

```
git rebase --onto <newbase> <oldbase>
```

![img](./figures/v2-b0bdfb89257edba41add442dcdb65ebb_1440w.png)



```
git rebase --onto main featureA featureB
```

![img](./figures/v2-f7b8c87a52cc777e5801bdaf39fcfc46_1440w.png)



featureB以featureA为基础，但是它并不依赖于featureA的任何改变，并且能够作为main的分支。featureA是oldbase，main成为newbase，而featureB则是newbase的HEAD指向的引用。



### Recovering from upstream rebase

如果其他用户rebase并且强制推送到你正在提交的分支上时，git pull将提示覆盖你基于先前分支的任何提交。可以使用*git reflog*来获取远程分支的reflog。在该reflog上，你可以找到一个rebase前的ref。然后使用--onto来针对该reflog来对你的分支进行rebase。



### fixup commits & --autosquash

```
git commit --fixup <SHA>
```

--fixup将一个新的commit和一个已存在的commit相联系，因此当你在使用interactive rebase时，你不必为了squash而重排序任何commits，同时也不必改变任何commit message。

--autosquash是于rebase一起使用的一个flag。当你使用*git commit --fixup <SHA>*来提交你的更改时，你可以像正常一样interactive rebase，但是传递--autosquash的flag。现在，你不再需要做任何事，rebase将会以正确的顺序自动处理这些由--fixup创建的commits。

**参考资料：**

- [**git rebase | Atlassian Git Tutorial**](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)
- [**Fixing commits with git commit --fixup and git rebase --autosquash | Jordan Elver | Ruby on Rails Developer, Bristol, UK**](https://jordanelver.co.uk/blog/2020/06/04/fixing-commits-with-git-commit-fixup-and-git-rebase-autosquash/)



## squash

通过在git中使用*squash*，你可以将多个commits合并为一个。

>  **注意，没有git squash命令，squashing是一个option**



### Interactive Rebase

例如：

```
git rebase -i <base>
```

该命令将会产生一个editor窗口，用于选择如何操作你的commit history。如果你将一条或者多条line标记为squash，那么他们将会与上面一条commit相合并。



### Merge

squashing同样也是merging branches的一个选项

```
git merge --squash <branch>
```

与普通merge自动创建一个merge commit不同，--squash选项将会在你的working copy中留下一个local change，然后你可以自己将其提交。

**参考资料：**[How to Squash Commits in Git | Learn Version Control with Git (git-tower.com)](https://www.git-tower.com/learn/git/faq/git-squash)



## git log

```
git log <branchA>..<branchB>
```

该命令将会显示两个分支之间的不同。
