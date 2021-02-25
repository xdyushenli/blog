---
title: git 的合并策略
date: 2021-02-01 08:13:51
tags: [ git ]
---
从开始接触 git 以来已经过了很久了，但是就在前两天，发生了一件让我有些摸不着头脑的事。我与同事在一个仓库的同一个分支上一起开发，在同步代码的时候，我执行了 git pull --rebase，但神奇地发现自己在本地更改的代码居然被改回去了！因此我决定好好探究一下 git pull 命令以及背后的 git 的合并策略。

# 参考资料
- [git tutorials: git pull](https://www.atlassian.com/git/tutorials/syncing/git-pull#:~:text=The%20git%20pull%20command%20is,repository%20to%20match%20that%20content.&text=Once%20the%20content%20is%20downloaded,point%20at%20the%20new%20commit.)