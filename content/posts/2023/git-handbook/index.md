+++
title = 'Git Handbook'
date = 2023-12-15T22:49:08+08:00
draft = false
categories = ['后段']
tags = ['Git']
+++

现在使用Git的频率变高了很多，使用场景也复杂了很多，根据需求总结一些常用的命令。虽然很多图形化工具已经非常还用了，但是命令行还是有类似`Write once, execute anywhere.`的优势。

# TL;DR;
```sh

```

# 拉取分支

## 基础命令
```sh
git clone [repo-link]
```
## 拉取指定分支
```sh
git clone -b [branch-name] [repo-link]
```
## 拉取并重命名
有的分支，我可能会拉取几次到本地，为了避免命名重复，就需要拉取的时候重命名。
```sh
git clone -b [branch-name] [repo-link] [new-name]
```

# 撤销提交
分很多情况，比如是否已经提交，远程仓库是否对分支设置有限制策略等。适用命令有：`git reset`, `git revert`等。

## 已经提交PR并merge到不能直接操作的分支
对于Github来说，可以直接在页面上进行Undo操作，[Reverting a pull request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/reverting-a-pull-request)。所以下面的策略是针对没有这一特性的Bitbucket。
两种办法：
1.  [How to undo a merge on Bitbucket?](https://stackoverflow.com/a/37036587)
2.  [Revert a merged pull request on Bitbucket](https://stackoverflow.com/a/38302342)

第一种使用限制很多而且非常危险，更推荐使用第二种，`revert`。关于这几个支持回溯的命令的区别，参考[atlassian docs: Resetting, checking out & reverting](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting)。

在提交pr的分支（the branch being merged into）或者从被merge的分支（the branch being merged from）新建一个fix分支，然后切到该分支
```sh
git switch [branch-name]
git pull
git revert -m 1 [hash of merged commit]
git push
git push
```
然后重新提交PR。

使用这个命令也要很谨慎，最好不要走到这一步，因为根据[git-revert - Revert some existing commits](https://git-scm.com/docs/git-revert#Documentation/git-revert.txt--mparent-number)：
> Reverting a merge commit declares that you will never want the tree changes brought in by the merge. As a result, later merges will only bring in tree changes introduced by commits that are not ancestors of the previously reverted merge. This may or may not be what you want.
> 
详情参考：[revert-a-faulty-merge.txt](https://github.com/git/git/blob/master/Documentation/howto/revert-a-faulty-merge.txt)

### -m parent-number 配置项
配置参考自：[git cherry-pick 教程](https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html)

> 如果原始提交是一个合并节点，来自于两个分支的合并，那么 Cherry pick 默认将失败，因为它不知道应该采用哪个分支的代码变动。
> 
> -m配置项告诉 Git，应该采用哪个分支的变动。它的参数parent-number是一个从1开始的整数，代表原始提交的父分支编号。
>
> 一般来说，1号父分支是接受变动的分支（the branch being merged into），2号父分支是作为变动来源的分支（the branch being merged from）。

# 查看分支非最新的版本
注意，是仅查看，不要修改。
```sh
git checkout <commit>
```

# 回到上一个分支
我们经常会切到其他分支，简单操作又切回dev分支。最简单的方法是：

```sh
git switch -
# or
git checkout -
# 但是推荐使用switch做分支的切换。
```
# merge remote的代码到当前分支
git pull 其实就是 git fetch 和 git merge FETCH_HEAD 的简写。
```sh
git pull origin [remote-branch-name]
```
比如
```sh
git pull origin master
```
将远程主机 origin 的 master 分支拉取过来，与本地的当前所在分支合并。


