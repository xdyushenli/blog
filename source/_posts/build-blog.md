---
title: 博客快速搭建指南(Hexo + github)
date: 2019-01-20 14:59:46
tags: [ github, hexo ]
---
# 前言
该文章旨在帮助大家利用Hexo和github快速搭建自己的博客。整个流程在一个小时之内即可完成。

# 准备阶段
## 什么是Hexo？
Hexo是一款基于Node.js的静态博客框架，依赖少且易于使用，可以方便的生成静态网页托管在GitHub上，是搭建博客的首选框架。
## 安装git
### Linux下安装git
Debian或Ubuntu可直接通过sudo apt-get install git安装。其他Linux版本可先到官网下载源码，之后解压安装。
### Mac OS下安装git
推荐方法是直接从App Store安装Xcode，Xcode中集成了git。Xcode是Apple官方IDE，功能非常强大。好用的IDE加白送的插件，血赚啊。
### Windows下安装git
在Windows下安装git，可直接从官网下载安装程序[国内镜像](https://pan.baidu.com/s/1kU5OCOB#list/path=%2Fpub%2Fgit)，然后按默认选项安装即可。安装后，运行cmd，输入`git --version`，出现类似`git version xxxxx`字段就说明安装成功。

## 安装Node.js
Hexo是基于[Node.js](https://nodejs.org/en/download/)。Node.js可从官网下载安装。注意安装时要勾选将Node.js添加到环境变量。安装后，运行cmd，输入`node -v`，出现`v x.xx.x`字段说明安装成功。

## 安装Hexo
在git和Node.js都安装完成后，便可以来安装Hexo了。使用npm命令安装Hexo客户端，在cmd输入
> npm install -g hexo-cli 

等待安装完成即可。为了检测安装的成果，可以生成一网站雏形，在cmd依次输入以下命令
> hexo new test&emsp;#创建一篇空白博客
> hexo g&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#生成博客静态网页
> hexo s&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#在本地服务器预览 

之后打开浏览器，访问localhost:4000，即可看到网站雏形。

# 配置Hexo
为了使你的博客可以通过github pages在线访问，必须创建一与你的github用户名同名的仓库，仓库名格式为`xxxxxx(username).github.io`。在Hexo客户端安装完成后，初始化博客。该命令会在cmd所在的当前目录下创建一个名为blog的文件夹。
> hexo init blog 

进入blog目录，找到`_config.yml`文件。这是整个个人博客的配置文件。下面要做的是将这个博客与你的github关联起来。打开配置文件，找到Deployment部分（一般在最下面），并更改为如下配置。
> type: gitrepo: #仓库链接
> branch: master 

此时所有的配置就基本都已完成。但如果我们想将本地仓库部署到github上，还需git部署插件。在cmd中输入
> npm install hexo-deployer-git - -save

安装完成后，我们便可以通过以下命令将本地仓库上传到github上。
> hexo d

上传完成后，打开浏览器，输入你放置博客的仓库链接，会发现网站已上线，可通过网络访问。此时个人博客就搭建完成啦。之后想写新博客时，可先在本地使用`hexo new 博客标题`创建并编辑新的md文件，之后通过`hexo g`和`hexo d`生成博客静态网页并部署到github上即可。

# 常见问题
1. 如何为文章添加多个标签？答：使用以下语法`[tag1, tag2]`。注意逗号后面也要加空格。
2. 首页标签或文章数目不对怎么修正？答：按顺序使用以下命令。
> hexo clean&emsp;#清除public文件夹及数据库文件
> hexo g&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#生成博客静态网页
> hexo d&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#上传博客至github

3. 如何使Hexo编译时跳过某些文件？答：打开`_config.yaml`文件，找到`skip_render`选项。使用时应注意，此选项是以`source文件夹`为相对路径的。该选项最终是由`minimatch`这一npm包执行匹配的，因此具体设置可以参考`minimatch`的文档。
> skip_render: .html # 跳过所有html文件
> skip_render: [.html, *.css] # 跳过所有html和css文件
> skip_render: _posts/test-post.md # 这将会忽略对 'test-post.md' 的渲染
> skip_render: _posts\/0* # 跳过_posts文件夹下所有以0开头的文件

4. 博文中出现的所有的代码必须采用四个空格缩进的语法或三个`\``包裹，不然会出现编译错误！

5. 使用`hexo d`命令部署后部署无效，且无法在`github`上找到相关的`commit`记录？
> 删除根目录下的`.deploy_git`文件夹和`public`文件夹，再重新提交。

6. 如何插入图片?答：使用如下语法`![](/images/img.png)`即可。在`hexo`中, 图片引用根目录是`source`文件夹。

7. 如何给文章中的标题前自动加上序号?
答: 安装 `hexo-heading-index` 插件, 并在顶层 `config.yaml` 中添加如下配置。如果不起作用, 就先使用 `hexo clean` 命令清理后再重新生成文件。
```
heading_index:
  enable: true
  index_styles: "{1} {1} {1} {1} {1} {1}"
  connector: "."
  global_prefix: ""
  global_suffix: ". "
```