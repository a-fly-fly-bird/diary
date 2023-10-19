+++
title = 'Hugo博客搭建'
date = 2023-10-14T17:30:08+08:00
draft = false
categories = ['折腾']
tags = ['博客', 'Hugo']
series = ['博客搭建到发布全流程']
series_order = 1
+++

## 前言

还记得第一次搭建博客是在大二，当时一个人带着电脑到教室学习算法，但是遇到苦难习惯性地开始不务正业，去折腾一些更简单的东西来寻求心理安慰。兜兜转转几年过去了，前前后后折腾了不少东西，但是技术也没有精进多少。现在想来，没有去挑战困难是大学最后悔的一件事。另外一件遗憾的事是很少记录生活，一些当初很感动的事情，现在也越来越模糊了。

这也是我这次重新搭建博客的初衷，记录下自己的脚步，希望未来读档的时候不至于指针越界。

## 静态博客主流方案

| 工具 | Jekyll                                               | Hexo ｜  Hugo              |
| ---- | ---------------------------------------------------- | -------------------------- | ------------------------------------------------------------------------ |
| 介绍 | Jekyll 是一个静态网站生成器，基于Ruby语言。          | 快速、简洁且高效的博客框架。 | Hugo是由Go语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署。 |
| 优点 | GitHub 支持好，历史最长，文档完备。          | 国人开发，入门简单。       | 基于Golang，生成速度快，社区活跃。                                                   |
| 缺点 | 基于Ruby，在三种生成器中速度最慢。需要一定Ruby基础。 | 基于Node.js，生成速度一般。  | 入门简单，深入需要阅读英文文档。                                         |

曾经在V2EX看到过一个帖子说：
> 一个是一群搞前端的人做的，一个是一群搞后端的人做的，特色都十分鲜明。

深度体验下来，我觉得说的很有道理。

## Hugo 安装
Hugo官方文档地址：[Quick start](https://gohugo.io/getting-started/quick-start/)。安装照着教程来就可以了。

如果使用Homebrew安装只需要：
```sh
brew install hugo
```
即可完成。

安装完成后输入:
```sh
hugo version 
```
来确认是否安装完成。

## 新建Hugo网站
```sh
hugo new site site_name
cd site_name
git init
```
然后选择使用git submodule的方式来安装主题（还可以用go module的方式，不过我更喜欢git submodule）。

## 设置主题
接下来去[Hugo主题网站](https://themes.gohugo.io)挑选自己喜欢的主题。个人比较喜欢简洁现代风一点的：
- blowfish
- mini
- doIt

这里我选择的是[blowfish](https://themes.gohugo.io/themes/blowfish/)。

```sh
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish
```
接下来就是主题相关的配置了，你可以像我一样在终端的博客根目录输入`code .`进入VS Code进行配置。需要参考[blowfish官方文档](https://blowfish.page/docs/welcome/)(英文文档，需要适应一下)。

根据文档内容，首先把现在根目录的`config.toml`文件删除，然后拷贝`themes/blowfish/config/_default/`目录下的所有`toml`后缀的文件到博客根目录下的`config/_default/`目录中。

然后你就能启动项目看见主题的效果了：
```sh
hugo server
```
通过 [http://localhost:1313](http://localhost:1313) 访问。

## 添加文章
首先先不考虑目录结构和文章分类等问题，跟着敲一遍下列命令新建第一篇文章：
```sh
hugo new content posts/my-first-post.md
```
Hugo会在`content/posts`目录下新建`my-first-post.md`文件，你可以随便写入些内容，符合markdown格式即可。比如:

```md
+++
title = '需要写点什么东西'
date = 2023-10-14T11:56:21+08:00
draft = true
+++

不仅需要输入，更需要输出。做一个生产者。
```
然后在终端输入:
```sh
# -D 意味着生成的内容包括草稿,如上所示，元数据里我加了一行draft = true，表明这个文件是一个草稿，如果不加-D参数的话不会被生成。
hugo server -D
```
命令重新生成并启动自带的服务器。通过 [http://localhost:1313](http://localhost:1313) 访问查看效果。

## 打包
命令很简单：
```sh
hugo
```
打包的资源放在`/public`目录下。

## 接下来
好了，这就是Hugo搭建博客的主要过程，但是如果要想写的得心应手，其实还需要阅读一下Hugo关于内容管理和模板等部分的文档。

如果想要发布到网络上，还需要自己购买服务器，申请备案等等。不过GitHub Pages给我们提供了另一种选择，你可以不需要服务器，不需要购买域名，甚至不需要多少额外的配置就可以把博客发布到互联网上。

欢迎阅读这个系列的下一章节。
