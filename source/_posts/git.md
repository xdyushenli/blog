---
title: git 不完全指北
date: 2020-04-21 08:13:51
tags: [ git ]
---
# 前言
本文不是入门教程，阅读本文需要对 git 有一定基础的了解。

# git 是什么？
git 是一种分布式版本控制软件。

# 为什么我们需要 git？
在团队协作开发时，常常需要对代码进行管理，不同的人可能负责不同的部分，每个人都能对代码进行修改。因此我们需要一种工具，帮助我们来进行代码管理工作，例如合并不同人写的代码、为每次修改加上说明信息等。
这个时候，就需要 git 出马了。

# git 基础知识
## git 中的文件状态
在 git 中，文件有四种状态。分别为：
* `untracked`: 未跟踪状态。
* `unmodified`: 未修改状态。
* `modified`: 已修改状态。
* `staged`: 已存储状态。

四种状态之间的变化关系如下所示：

![git 中的文件状态](/images/git/2.jpg)

## git 工作区、暂存区和版本库
首先来理解这三个概念：
* `工作区`：电脑中存在的、能看到的目录。
* `暂存区`：用于存放已经修改但尚未提交到仓库的文件，**git add**之后的文件会放在这里。一般存放在 `.git` 目录下的 `index` 文件中。
* `版本库`：在这里存放着已经提交到仓库的文件以及仓库的各种信息，**git commit**之后的文件会放在这里。在文件系统上表现为为 `.git` 隐藏目录。

下图为工作区、暂存区和版本库的变化关系：

![git 中工作区、暂存区和版本库的关系](/images/git/3.jpg)

## 自定义命令
git 中某些命令比较长，书写起来既容易出错，也很不方便。对于这个问题，我们可以通过指定命令别名来解决。
方法是修改 `.gitconfig` 文件的 `alias` 子命令，具体语法如下：
```shell
[alias]
    st = staus
    l = log
    # ...
```
`alias` 的意思为`别名，化名`。在上面的文件中，我们指定了 **git st** 即为 **git status**，**git l** 即为 **git log**。

# git 常用命令及详解
## git init
初始化 git 仓库。

## git status
查看当前仓库的信息，如文件状态、暂存区中存储了哪些文件等。

## git add <单个文件名 | .>
将文件添加到暂存区。

## git commit -m \<message\>
将暂存区中的文件提交到仓库。

## git log
查看**当前分支**的提交记录，可以看到什么人在什么时间提交了，并留下了哪些信息，还能查看每个 `commit` 对应的唯一哈希值。
**注意，虽然每个 commit ID 都很长，但是只需要前 7 位就能确定是哪一个 commit 了。**

## git reflog
查看所有的操作记录，这个命令常常用于回滚代码版本。

## git branch
查看所有分支。

## git clone \<远程仓库链接\>
将远程仓库克隆到本地。

## git push <远程仓库链接> \<branchName\>
将本地内容提交到远程仓库。其中，远程仓库链接常常用别名代替，比如 `origin`。
**另外，\<branchName\> 参数可以省略，其默认为当前分支名。**

## git checkout
* **git checkout \<branchName\>**：切换分支。
* **git checkout -b \<branchName\> \<template\>**：创建并切换到新分支。创建时可以指定分支名字并指定某个已存在分支作为模板。
其中，`<template>` 参数默认以当前分支为模板，**且新建的分支会继承原有分支的 commit 记录。**
* **git checkout -b \<branchName\> origin \<template\>**：以远程仓库中的某个分支作为模板，来新建本地分支。

## git reset
* **git reset <文件名>**：将文件从暂存区中删除。
* **git reset \<commit-id\> <参数>**：回退到之前某次 `commit` 的状态。 
 参数列表：
 * `--hard`：所有更改均不保存。
 * `--soft`：保留处于 `staged` 状态的更改。
 * `--mixed`：保留处于 `modified` 状态的更改，**该参数为默认参数**。

## git fetch
拉取远程仓库的信息，如分支等。使用了这个命令之后，才可以切换到远程仓库的一些分支。

## git merge \<branchName\>
**git merge \<branchName\>** 会将某个分支的 `commit` 合并到当前分支。**这里需要注意合并的是 `commit`，不会合并未提交的内容。**
在 `git merge` 时，被合并分支可能会与当前分支产生冲突，所以要解决冲突。
在 `git merge` 完成后，会自动在当前分支增添一条 `commit` 记录。这条记录与被合并的 `commit` 对应的记录一致。

## git pull
该命令的作用是拉取某个分支的最新版本并自动与本地版本进行 `merge`，相当于 `fetch + merge`。
* **git pull <远程主机名> <远程分支名>:<本地分支名>**：拉取远程特定分支，并与本地某个分支合并的命令。
* **git pull <远程主机名> <远程分支名>**：如果是与当前分支合并的话，本地分支名可以省略。

## git remote
* **git remote**：查看当前仓库的简写。
* **git remote add \<shortname\> \<url\>**：添加远程仓库，并指定简写名称。

## git rebase \<branchName\>
**git rebase** 命令最好的翻译是**变基**，这里的**变基**指的是**改变基础**。**简单来说，git rebase 的作用是重新排列 `commit`。**
这么说可能有点抽象，我们来考虑以下场景：

![git 中工作区、暂存区和版本库的关系](/images/git/1.PNG)

我们需要将新分支切换为以最新的 `master` 为模板生成的分支，这个时候可以在 `bc` 分支上执行 **git rebase master** 命令。
但这并不算完，因为可以看到，我们在 `bc` 分支还有两次提交，分别是 `commit 3`、`commit 4`。
第一次执行 **git rebase master** 后，我们需要处理冲突，合并 `commit 5`（`master` 分支最新的 `commit`）和`commit 3`（`bc` 分支在创建后的第一次 `commit`）。
之后需要不断合并，直到所有在 `bc` 分支创建后的 `commit` 都已被成功合并。
在合并下一个 `commit` 时，我们的命令会发生一点变化，不需要每次都加上 `master` 了，只需要加上 `--continue` 参数就好：
```shell
git rebase --continue
```

## git cherry-pick \<commit-id\>
关于这个命令，最恰当的翻译为**拣选**。用于将其他分支上的、单个 `commit` 修改，移植到当前的分支。
一个很常见的场景就是：想在某个稳定版本上，添加一个刚开发完成的版本中的功能。这时就可以使用 **git cherry-pick** 命令，将这个功能相关的 `commit` 提取出来，合入稳定版本的分支上。

## git stash
考虑这样一个场景：你在修改一个文件，修改到一半，决定回滚到上一次 `commit` 的版本。这时你首先想到的命令肯定是 **git reset**。但是这些修改全部丢弃掉又有点可惜，你决定将这些修改存储起来，以便下次还可以使用这些修改。存储这些修改所用的命令就是 **git stash**。
**git stash** 的本质是一个堆栈，只不过里面存储的是文件的修改情况。一个本地文件的更改被储藏时，被修改文件会恢复原样。等要恢复更改时，可以使用 **git stash pop**，把更改应用于文件。
简单来说，**git stash** 储藏更改，并去除相关文件中的更改，**git stash pop**恢复更改，并删除储藏堆栈中的更改。

下面是关于 **git stash** 命令的详解：
* **git stash**：保存当前的工作进度。会分别对暂存区和工作区的状态进行保存。
* **git stash save \<message\>**：保存当前工作进度，并为此次保存添加一些说明。这条命令实际上是第一条 **git stash** 命令的完整版。
* **git stash list**：显示保存所有进度的列表。此命令显然暗示了 **git stash** 可以多次保存工作进度，并用在恢复时候进行选择。
* **git stash pop [--index] [\<stash-id\>]**：
如果不使用任何参数，会恢复最新保存的工作进度（**仅工作区！**），并将恢复的工作进度从存储的工作进度列表中清除。    
如果提供 `stash-id` 参数（来自 **git stash list** 提供的信息），则会恢复 `stash-id` 对应的进度。恢复完毕也将从进度列表中删除对应进度。
如果提供 `--index` 参数，则除了恢复工作区的文件外，还会尝试恢复暂存区。
两个参数能同时使用。
* **git stash apply [--index] [\<stash-id\>]**：除了不删除恢复的进度之外，其余和 **git stash pop** 命令一样。
* **git stash drop [\<stash-id\>]**：删除一个存储的进度。如果不指定 `stash-id`，则默认删除最新的进度。
* **git stash clear**：删除所有存储的进度。

# 总结
git 中的命令还有很多，这里只涉猎了一小部分。
后续会结合实际工作需要，陆续添加对其他命令的说明，并在[场景应用](#场景应用)中添加对于实际问题的解决方案。
另外，有个小姐姐写了一篇用动态图展示 git 命令的[博客](https://dev.to/lydiahallie/cs-visualized-useful-git-commands-37p1)，~~不管是人还是博客都~~很值得一看。

# 参考资料
* [git 直到这些就够了](https://www.bilibili.com/video/BV1BE411g7SV?t=51)
* [Git – 储藏(git stash)本地更改](https://www.qikegu.com/docs/4379)
* [git stash 用于保存和恢复工作进度](https://gist.github.com/subchen/3409a16cb46327ca7691)
* [git stash 保存和恢复修改进度](http://blog.51yip.com/other/1945.html)

# 场景应用
## 在本地新建仓库，提交代码到某个已存在的远程仓库
具体场景如下：本地新建了一个仓库，进行了一些修改工作。现在想让其与某个远程仓库建立连接，能够将代码 `push` 到远程仓库。
需要执行以下命令：
```shell
# 设置仓库连接
git add remote <shortname> <远程仓库链接>
# 拉取远程更改，最好加上具体的远程分支名与本地分支名
git pull <远程仓库连接> <远程分支名>
```
首先我们告知本地 git 应当与哪个远程仓库建立联系。
由于本地仓库的修改进度落后于远程仓库，因此需要拉取远程仓库的修改，与本地修改进行合并。否则会遇到如下错误：
```shell
To https://git.coding.net/xxxx/xxx.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://git.coding.net/xxxx/xxx.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
之后便可以正常执行 `git add`、`git commit`、`git push` 等命令了。

## 在远程仓库创建新分支并提交
考虑以下场景：我们将远程仓库克隆到了本地，在本地新建了一个分支。但远程仓库中并没有这个分支，那么直接使用 **git push origin** 能够顺利提交吗？
答案是否定的。git 会提示我们当前分支缺乏上流分支：
```shell
fatal: The current branch <branchName> has no upstream branch.
```
这时候，我们需要在 push 的同时在远程仓库上创建一个分支，可以使用以下命令：
```shell
# 在远程仓库中创建该分支，并将代码提交到远程仓库
git push --set-upstream origin <banchName>
```

## 拉取远程仓库时提示本地未提交
这个问题可以使用 **git stash** 命令来解决，分别执行下列命令：
1. 使用 **git stash** 储存本地更改
2. **git pull** 拉取远程更改
3. **git stash pop** 在应用恢复更改
