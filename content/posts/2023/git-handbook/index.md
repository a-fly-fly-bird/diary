+++
title = 'Git Handbook'
date = 2023-12-15T22:49:08+08:00
draft = false
categories = ['后端']
tags = ['Git']
+++

# 起步

## 关于版本控制

​	版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。

### 分类

- 本地版本控制系统
- 集中化的版本控制系统
- 分布式版本控制系统

### Git历史

​	Git由Linux的创造者Lunus创造。

### Git是什么

1. 直接记录快照，而非差异比较

​	其他很多版本控制系统都是记录两个版本的差异，而在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。

**Git 更像是一个小型的文件系统。**

2. 近乎所有操作都是本地执行

3. Git保证完整性

   Git中的所有数据在存储前都会计算哈希值（校验和），并且以校验和来引用。

4. Git 一般只添加数据

### 三种状态

1. 已修改（modified）
2. 已暂存（staged）
3. 已提交（committed）

## 安装Git

MacOS：`brew install git `.

### 配置

Git有三个等级的配置文件：

1. system
2. user
3. local

优先级从上到下依次提高。

第一次使用Git前需要先配置：

```sh
git config --global user.name "terry"
git config --global user.email "abc@qq.com"
```

# Git基础

## 已追踪和未追踪

工作目录下的文件状态：已跟踪 或 未跟踪。

以跟踪的文件就是已经纳入Git版本控制系统的文件，在上一次的快照中有它们的记录。

除此之外，就是未跟踪的文件。

## 忽略文件

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。

规则如下：

- 支持**标准的glob模式**，递归应用在整个工作区。（根据维基百科的介绍，在计算机编程中 glob 模式表示带有通配符的路径名，是简化了的正则表达式）
- 匹配模式可以以（/）开头防止递归。
- 匹配模式可以以（/）结尾指定目录。

多级目录可以有多个忽略文件，同样是越靠近源文件的优先级越高。

## 删除文件

```sh
git rm xxx.file
```

上述命令会从已跟踪清单中移除，并从工作目录中移除该文件。

### 忘记添加`.gitignore`文件

`git rm --cached xxx.file`

该命令会将文件从已跟踪清单中移除，但是保留在工作目录。

git rm 命令后面可以列出文件或者目录的名字，也可以使用 glob 模式。

## 移动文件

Git能够推断出是改名而不是新文件。

可以通过`git mv file_from file_to**`或者**

```sh
mv file_from file_to
git rm file_from
git add file_to
```

两种方式Git都能意识到这是一次重命名。

## 查看提交历史

命令：`git log`

差异对比还是GUI工具好用，比如VS Code插件Git History。

比较有用的命令：`git log --oneline`

### 查看文件的提交记录

```sh
git log -- filename_or_path
```

## 撤销操作

实践下来，撤销有很多个命令：

- `git commit --amend`
- `git revert`
- `git reset`

`git commit --amend`可以修改提交信息，如果暂存区有新的文件，也会同时提交。

> “当你在修补最后的提交时，与其说是修复旧提交，倒不如说是完全用一个 新的提交 替换旧的提交，
> 理解这一点非常重要。”
>
> “修补提交最明显的价值是可以稍微改进你最后的提交，而不会让“啊，忘了添加一个文件”或者
> “小修补，修正笔误”这种提交信息弄乱你的仓库历史。”

## 取消暂存文件

如何把暂存区的文件重新放回工作区？

`git reset HEAD filename`

## 撤销对文件的修改

`git checkout -- file`

## 远程仓库的使用

如果你使用 clone 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。

查看远程仓库

`git remote -v`

### 添加远程仓库

`git remote add name url`

### 从远程仓库中抓取和拉取

`git fetch `

这个命令会访问远程仓库，从中拉取所有你还没有的数据。

必须注意 git fetch 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。当准备好时你必须手动将其合并入你的工作。

### 重命名远程分支

`git remote rename from to`

## 打标签

Git可以把历史中的某一提交打上标签，表示很重要。其实也相当于索引。

### 列出标签

`git tag`

Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。

轻量标签只是特定提交的引用，而附注标签相当于一个新的提交，有自己完整的信息，可以被校验。

### 附注标签

`git tag -a tag_name -m "info"`

`git show`命令可以查看标签和对应的提交信息。

### 轻量标签

`git tag tag_name`

### 后期打标签

`git tag -a tag_name commit_id`

### 共享标签

默认情况下，git push 命令并不会传送标签到远程仓库服务器上。

`git push origin tag_name` or `git push origin --tags`

### 删除标签

`git tag -d tag_name`

要删除远程标签:

`git push origin --delete tag_name`

### 检出到标签

`git checkout tag_name`

检出会导致分离头指针（detached HEAD），不要在检出的分支上新增修改。

# Git分支

Git鼓励在工作流程中频繁地使用分支与合并。

## 分支简介
在提交时，Git会保存一个提交对象。提交对象包含一个指向内容快照的指针，以及作者的姓名、邮箱、输入信息以及父对象指针。

暂存操作会为每一个文件计算校验和，然后把文件快照保存到Git仓库中。

提交时，Git 会先计算每一个子目录（本例中只有项目根目录）的校验和，
然后在 Git 仓库中这些校验和保存为树对象。随后，Git 便会创建一个提交对象，
它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。

现在，Git 仓库中有五个对象：三个 blob 对象（保存着文件快照）、一个 树 对象
（记录着目录结构和 blob 对象索引）以及一个 提交 对象（包含着指向前述树对象的指针和所有提交信息）。

### 首次提交的结构
![alt text](<_media/CleanShot 2024-02-15 at 13.14.54.png>)

### 后续结构
![alt text](<_media/CleanShot 2024-02-15 at 13.22.31.png>)

Snapshot 就是树对象。

## 分支创建
`git branch branch_name`

这会基于HEAD指向的提交对象创建一个新的指针。

## 分支切换
`git switch branch_name`

## 检出分支
`git checkout branch_name/commit_id`

作用是将`HEAD`指向对应的分支或者提交记录。

`git branch`输出的星号（*）就代表目前检出的分支（HEAD指针所指向的分支）。

## 暂存更改
`git stash`

取出更改: `git stash pop`

## 合并分支
`git merge branch_from`

这个命令会把`branch_from`的更改合并到当前分支。

### fast-forward
新的提交如果是在当前的提交记录上的更改(当前HEAD指向的提交对象是新提交的直接祖先)，那么只需要将指针直接移动到新的提交对象上即可，因为只需要移动指针，所以形象地称为“快进”（fast-forward）。

### 新建和删除分支的时机
修bug会新建hotfix分支，如果bug改好了合并之后就可以删除hotfix分支了，保持分支的干净。

### 合并提交
![alt text](<_media/CleanShot 2024-02-15 at 13.57.04.png>)

这种情况，Git会将三方合并的结果做一个新的快照并创建新的提交对象指向它。

![alt text](<_media/CleanShot 2024-02-15 at 13.58.37.png>)

## 冲突解决
如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。

这个时候需要你先手动解决冲突然后再合并。

`git status`可以查看因冲突而为合并的文件。

解决冲突后需要`git commit`来完成**合并提交**。

## 分支开发工作流
随着你的提交而不断右移的指针。
稳定分支的指针总是在提交历史中落后一大截，而前沿分支的指针往往比较靠前。

### 推送

![alt text](<_media/CleanShot 2024-02-15 at 14.12.08.png>)

因为一个分支很可能有多人在协作开发，所以在推送前一般要先拉取远程分支的变更，合并到本地，然后再推送。
```sh
git fetch origin
git merge origin/master
```
或者
```sh
git pull origin master
```
来将远程分支的更改同步到本地分支上。

`git pull`相当于`git fetch`+`git merge`命令。

## 跟踪分支
> 从一个远程跟踪分支检出一个本地分支会自动创建所谓的“跟踪分支”（它跟踪的分支叫做“上游分支”）。
> 跟踪分支是与远程分支有直接关系的本地分支。如果在一个跟踪分支上输入 git pull，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

## 删除远程分支
`git push origin --delete branch_name`

> 基本上这个命令做的只是从服务器上移除这个指针。Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的。

## 变基（Rebase）
变基的原理是首先找到这两个分支（即当前分支 experiment、变基操作的目标基底分支 master）
的最近共同祖先 C2，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，
然后将当前分支指向目标基底 C3, 最后以此将之前另存为临时文件的修改依序应用。有点类似Git的`cherry-pick`命令。

变基的好处是让提交历史更加整洁，像穿行一样。（有点类似fast-forward和合并提交的区别）

### 更有趣的变基例子

```sh
git rebase --onto master server client
```
上述命令可以从
![alt text](<_media/CleanShot 2024-02-15 at 16.18.04.png>)
变成
![alt text](<_media/CleanShot 2024-02-15 at 16.18.18.png>)

### 变基的风险

**如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基。**

变基的本质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。

问题在于如果你推送更改到了远程仓库，其他人基于这一提交记录进行了后续工作，如果你现在变基，也就是丢弃了这一记录，那么你就会**影响到他人的工作**。

具体案例请查看《Pro Git》一书的相关章节。

### 用变基解决变基
举个例子，如果遇到前面提到的有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交 那种情境，如果我们不是执行合并，而是执行 git rebase teamone/master, Git 将会：

- 检查哪些提交是我们的分支上独有的（C2，C3，C4，C6，C7）
- 检查其中哪些提交不是合并操作的结果（C2，C3，C4）
- 检查哪些提交在对方覆盖更新时并没有被纳入目标分支（只有 C2 和 C3，因为 C4 其实就是 C4'）
- 把查到的这些提交应用在 teamone/master 上面

可以从
![alt text](<_media/CleanShot 2024-02-15 at 16.43.42.png>)
变成
![alt text](<_media/CleanShot 2024-02-15 at 16.44.00.png>)

### 变基 vs. 合并

有一种观点认为，仓库的提交历史即是记录实际发生过什么。(合并更好)

另一种观点则正好相反，他们认为提交历史是项目过程中发生的事。（变基更好）

每个团队需求不同，可以制订符合实情的规范。

# 向一个项目贡献
影响因素：
1. 活跃贡献者的数量
2. 项目使用的工作流程
3. 提交权限

## 提交准则
1. 让每个提交成为一个逻辑上的独立变更集
2. 优质的提交信息（一般情况下，信息应当以少于 50 个字符（25个汉字）的单行开始且简要地描述变更，接着是一个空白行，再接着是一个更详细的解释。**相当于标题和正文。**）

# 维护项目
## 准备一次发布
Git会包含很多快照，但有的时候我们不需要知道所有记录，只需要最新的快照，比如CI/CD部署等等。

```sh
git archive master --prefix='project/' | gzip > `git describe master`.tar.g
```

或者如果还想要git，那么`git clone --depth=1 url`可以考虑一下。

# Git 工具
## 选择修订版本
commit_id 是一个40个字符的SHA-1散列值，但一般情况可以使用更简短的值就可以唯一确定一个commit_id。

## SHA-1 歧义
如果仓库里出现了两个不同的对象具有相同的SHA-1值怎么办？

如果你真的向仓库里提交了一个对象，它跟之前的某个 不同 对象的 SHA-1 值相同，
Git 会发现该对象的散列值已经存在于仓库里了，于是就会认为该对象被写入，然后直接使用它。

如果之后你想检出那个对象时，你将得到先前那个对象的数据。

但这种概率很小。

## 引用日志

Git 会在后台保存一个引用日志（reflog），引用日志记录了最近几个月你的 HEAD 和分支引用所指向的历史。

## 祖先引用
- `^`会被解析为该引用的上一次提交
- ^ 后面添加一个数字来指明想要 哪一个父提交——例如 d921970^2 代表 “d921970 的第二父提交”这个语法只适用于合并的提交，因为合并提交会有多个父提交。合并提交的第一父提交是你合并时所在分支（通常为 master），而第二父提交是你所合并的分支。
- `~n`同样指向第一父提交，会被解析为第一父提交的第一父提交的xxx(n次)

使用案例：
`HEAD~3^2`表示当前所在提交记录的第一父提交的第一父提交的第一父提交的第二父提交。

## 提交区间
可以一次指定多个提交，十分有用的语法。
### 双点
> 最常用的指明提交区间语法是双点。这种语法可以让 Git 选出在一个分支中而不在另一个分支中的提交。

```
git log master..terry
```

显示在`terry`分支中但没有在`master`分支的提交。如果留空了其中的一边，Git默认为HEAD。

`git log commit_id_1..commit_id_2` 表示commit_id_1和commit_id_2之间的提交，不包括commit_id_1。 这也很实用。

### 多点
Git 允许你在任意引用前加上 ^ 字符或者 --not 来指明你不希望提交被包含其中的分支。

以下命令等价：
- `git log refA..refB`
- `git log ^refA refB`
- `git log refB --not refA`

还可以超过两个引用：
- `git log refA refB ^refC`
- `git log refA refB --not refC` 

### 三点
三点语法可以选择出被两个引用之一包含但又不被两者同时包含的提交。

## stash

`git stash` and `git stash pop`

贮藏（stash）会处理工作目录的脏的状态——即跟踪文件的**修改与暂存的改动（注意也就是说不包括未追踪的文件，比如新创建还没有暂存的）**——然后将未完成的修改保存到一个栈上，而你可以在任何时候重新应用这些改动（甚至在不同的分支上）。

## 清除工作目录

`git clean`会移除没有忽略的未跟踪文件。

# 签署工作
Git的ShA-1可以保证每个提交对象是没有被篡改的，但是我们还需要保证这个提交是可信的（也就是作者而不是其他人提交的）。Git提供了GPG来签署和验证工作的方式。

可以签署标签和提交。

## 什么是GPG
[简明 GPG 概念](https://zhuanlan.zhihu.com/p/137801979)

简而言之，SSH登陆密钥可以做身份验证，鉴权，而...(看到了阮一峰老师的教程，有阮选阮。)

[阮一峰的网络日志 - GPG入门教程](http://ruanyifeng.com/blog/2013/07/gpg.html)

总而言之，GPG就是类似RSA等非堆成加密算法在现实中的应用，是一个工具，可以签名、加密等。类似的工具还有OpenSSL。

## Mac 安装

Mac的操作流程： [Methods of Signing with a GPG Key on MacOS](https://gist.github.com/troyfontaine/18c9146295168ee9ca2b30c00bd1b41e)

# 重写历史
## 修改最后一次提交信息

`git commit --amend`

## 修改多个提交信息

`git rebase -i HEAD~3`

实际上就是利用变基的方式，把`HEAD`的变基到`HEAD~3`上（重写）。

## 合并提交
也是借助`rebase`完成，由此可见，`rebase`是非常强大的工具。

# 子模块
Git 通过子模块来解决这个问题。
子模块允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。

这个使用的也很熟悉了，跳过。

# Git属性

你也可以针对特定的路径配置某些设置项，这样 Git 就只对特定的子目录或子文件集运用它们。

这些基于路径的设置项被称为 Git 属性，可以在你的目录下的 .gitattributes 文件内
进行设置（通常是你的项目的根目录）。

## 合并策略
通过 Git 属性，你还能对项目中的特定文件指定不同的合并策略。
一个非常有用的选项就是，告诉 Git 当特定文件发生冲突时不要尝试合并它们，而是直接使用你这边的内容。

# Git钩子
和其它版本控制系统一样，Git 能在特定的重要动作发生时触发自定义脚本。
有两组这样的钩子：客户端的和服务器端的。
客户端钩子由诸如提交和合并这样的操作所调用，而服务器端钩子作用于诸如接收被推送的提交这样的联网操作。

我知道的一个实践案例就是：[husky](https://github.com/typicode/husky)

# Git 内部原理
学习原理对于理解Git和更好地使用Git至关重要。

## Git对象
Git是一个内容寻址文件系统，也就是说，Git的核心是一个简单的键值对数据库。你可以向 **Git 仓库中插入任意类型的内容，它会返回一个唯一的键，通过该键可以在任意时刻再次取回该内容。**(这句话非常重要，比如分支实际上是文件，存储在`.git/refs/heads`里，文件里的内容就是提交对象的SHA-1散列值，可以直接用底层命令获取到对应的值，然后通过父提交，串起来。)

## 树对象
与之前了解的基本一致。

现在使用Git的频率变高了很多，使用场景也复杂了很多，根据需求总结一些常用的命令。虽然很多图形化工具已经非常还用了，但是命令行还是有类似`Write once, execute anywhere.`的优势。

# 常用命令
## 拉取分支

### 基础命令
```sh
git clone [repo-link]
```
### 拉取指定分支
```sh
git clone -b [branch-name] [repo-link]
```
### 拉取并重命名
有的分支，我可能会拉取几次到本地，为了避免命名重复，就需要拉取的时候重命名。
```sh
git clone -b [branch-name] [repo-link] [new-name]
```

## 撤销提交
分很多情况，比如是否已经提交，远程仓库是否对分支设置有限制策略等。适用命令有：`git reset`, `git revert`等。

### 已经提交PR并merge到不能直接操作的分支
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

#### -m parent-number 配置项

配置参考自：[git cherry-pick 教程](https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html)

> 如果原始提交是一个合并节点，来自于两个分支的合并，那么 Cherry pick 默认将失败，因为它不知道应该采用哪个分支的代码变动。
> 
> -m配置项告诉 Git，应该采用哪个分支的变动。它的参数parent-number是一个从1开始的整数，代表原始提交的父分支编号。
>
> 一般来说，1号父分支是接受变动的分支（the branch being merged into），2号父分支是作为变动来源的分支（the branch being merged from）。

## 查看分支非最新的版本

注意，是仅查看，不要修改。
```sh
git checkout <commit>
```

## 回到上一个分支

我们经常会切到其他分支，简单操作又切回dev分支。最简单的方法是：

```sh
git switch -
# or
git checkout -
# 但是推荐使用switch做分支的切换。
```
## merge remote的代码到当前分支

git pull 其实就是 git fetch 和 git merge FETCH_HEAD 的简写。
```sh
git pull origin [remote-branch-name]
```
比如
```sh
git pull origin master
```·
将远程主机 origin 的 master 分支拉取过来，与本地的当前所在分支合并。

# git 合并本地多个提交为一个提交并推送到远程

假设你需要merge前三个commit为一个单一的commit，运行如下命令：
```sh
git rebase -i HEAD~3
```
然后会进入一个界面，将除了第一个提交之外的`pick`改成`squash`，保存，然后修改提交信息即可。细节可以参考：[Combining Multiple Commits Into One Prior To Push](https://stackoverflow.com/a/5721879)

## git 本地分支管理
git 远程从主分支新建一个feature/fix分支后，本地克隆下来，然后最好不要直接就在这个分支上开始开发，把这个分支想象成自己的主分支，再新建分支进行开发，有时候会省很多事。
