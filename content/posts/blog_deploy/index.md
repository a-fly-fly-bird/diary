+++
title = 'Hugo博客部署'
date = 2023-10-19T21:05:31+08:00
draft = false
categories = ['折腾']
tags = ['博客', '部署', 'Hugo', 'Github']
series = ['博客搭建到发布全流程']
series_order = 2
+++

仅仅是在Github Pages部署博客就有好几种方式，分别为：
- 本地打包
- Github Actions打包

以及

- 部署到同一仓库
- 部署到不同仓库

这里我主要记录一下我用的本地打包并部署到同一仓库的方法。

## 本地打包 + 部署到同一仓库

### 为什么要本地打包

说来话长，我使用的Hugo主题使用Github Actions打包总是会报错，但是本地打包又没有问题，网络上也没有搜到报错的解决方案。尝试几天解决无果后，遂退而求其次选择本地打包。

### 打包和部署
首先在`config.toml`中把添加如下一行把`build`的分支从`/public`改到`/docs`。
```toml
publishDir = "docs"
```
然后在项目根目录打开终端输入
```sh
hugo
```
完成打包。打包完成后可以看到多了一个`/docs`文件夹（注意不要把这个文件夹加入`.gitignore`中）。接下来需要同时把打包后的结果push到远程分支（Github分支任意命名即可，不用遵守`xxx.github.io`的规则）。

推送完成后进入Github对应分支的设置界面，点击`Pages`界面，把第三点设置为图上对应格式。如果有域名的话，在第四点那个填上对应域名。（域名这块我也是看的别人的博客，感兴趣的话网上很多教程）

![Alt text](<github_page_setting.png>)

如果前面的设置都没有问题的话，接下来在`Github`的导航栏上点击`Action`，就能看到`All workflows`列表下有一个正在运行的`workflows`, 这是Github Actions自带的`pages build and deployment workflow`，也是为什么你把静态网页推动到Github仓库后就能访问到个人网站的原因。Github Actions提供了一个完整的CI/CD流程，如果对Github Actions想有更深入的了解的话，可以去参考官网文档。但对于只想搭建个人博客的同学来说，这已经足够应对了。

### 访问
如果你之前没有填写自定义域名，那么你的博客地址是：`<github user name>.github.io/<repository name>`。比如如果你的Github用户名是`abc`,你这个仓库的名字是`blog`，那么你的访问地址就是：`abc.github.io/blog`。

如果你之前填写了自定义域名，那么访问地址就是自定义域名。

## Github Actions打包 + 部署到同一仓库
### Github Actions打包
这个方法主要区别是要写一个配置文件，但幸运的是，早已经有人写好了，我们直接复制别人写好的配置即可。配置在仓库目录 `.github/workflows` 下，以 `.yml` 为后缀即可，比如`.github/workflows/blog-deploy.yml`。

```yml
name: deploy

on:
    push:
    workflow_dispatch:
    schedule:
        # Runs everyday at 8:00 AM
        - cron: "0 0 * * *"

jobs:
    build:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout
              uses: actions/checkout@v3
              with:
                  submodules: true
                  fetch-depth: 0

            - name: Setup Hugo
              uses: peaceiris/actions-hugo@v2
              with:
                  hugo-version: "latest"

            - name: Build Web
              run: hugo

            - name: Deploy Web
              uses: peaceiris/actions-gh-pages@v3
              with:
                  PERSONAL_TOKEN: ${{ secrets.PERSONAL_TOKEN }}
                  EXTERNAL_REPOSITORY: pseudoyu/pseudoyu.github.io
                  PUBLISH_BRANCH: main
                  PUBLISH_DIR: ./public
                  commit_message: ${{ github.event.head_commit.message }}
```
上面的配置文件还设置了定时任务，如果不要可以删除这段：
```yml
        workflow_dispatch:
            schedule:
                # Runs everyday at 8:00 AM
                - cron: "0 0 * * *"
```

如果想了解细节，参考：
- [https://github.com/actions/checkout](https://github.com/actions/checkout)
- [https://github.com/peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo)
- [https://github.com/peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)

### 部署
部署和上面的类似，只是分支从`main`变成了`gh-pages`分支，所以设置那里上图第三点需要把分支设置为`gh-pages`分支，目录设置为根目录。这样每次本地更新完内容后就直接推送到远程分支，等待Github Action完成打包和部署即可。

