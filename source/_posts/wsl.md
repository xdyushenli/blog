---
title: wsl下ubuntu常见问题的解决方法
date: 2020-07-14 09:45:31
tags: [ linux, wsl, ubuntu ]
---
# 前言
记录使用 wsl 过程中遇到的问题以及解决方法。**只对 wsl 下的 ubuntu 有效，真正的 ubuntu 系统不一定适用。**

# npm 安装错误
## 命令行错误输出
命令行输出的错误信息有两种，一种是：

```shell
npm ERR! code ENOENT
npm ERR! syscall rename
npm ERR! path /home/user/.nvm/versions/node/v14.4.0/lib/node_modules/expo-cli/node_modules/css-tree
npm ERR! dest /home/user/.nvm/versions/node/v14.4.0/lib/node_modules/expo-cli/node_modules/.css-tree.DELETE
npm ERR! errno -2
npm ERR! enoent ENOENT: no such file or directory, rename '/home/user/.nvm/versions/node/v14.4.0/lib/node_modules/expo-cli/node_modules/css-tree' -> '/home/user/.nvm/versions/node/v14.4.0/lib/node_modules/expo-cli/node_modules/.css-tree.DELETE'
npm ERR! enoent This is related to npm not being able to find a file.
npm ERR! enoent
npm ERR! A complete log of this run can be found in:
npm ERR!     /home/user/.npm/_logs/2020-07-14T01_16_32_597Z-debug.log
```

还有一种错误输出如下：

```shell
todo 遇上了再写上
```

## 错误原因
在之前遇上这种错误的原因是安装了多个 node 版本，既通过 `apt-get` 命令安装了 node，又安装了 nvm，导致系统的 node 版本不统一。
**推荐只安装 nvm 来管理 node！**

## 解决方案
遇到该问题可以依次尝试如下四种解决方法：
* 解决 node 版本冲突。
使用 `apt remove` 命令移除 nodeJS，这样的话系统上就只剩下 nvm 安装的 nodeJS，冲突就解决了。

* 切换到低版本的 node
v10 版本的 node 较为稳定。可以通过以下命令 `nvm use v10` 切换到 v10 版本的 node。

* 重新执行 `npm install [package-name]` 命令。

* 重启电脑。**重启大法好！**

# Have broken packages!
todo