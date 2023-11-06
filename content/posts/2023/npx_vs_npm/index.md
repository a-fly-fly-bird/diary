+++
title = 'npm，npx和packages.json'
date = 2023-11-06T00:01:36+08:00
draft = false
categories = ['前端']
tags = ['npm', 'npx']
+++

## 参考文档
- [什么是 npm —— 写给初学者的编程教程](https://www.freecodecamp.org/chinese/news/what-is-npm-a-node-package-manager-tutorial-for-beginners/)
- [npm docs](https://docs.npmjs.com/cli/v10/configuring-npm/package-json#browser)
- [npm 和 npx 的区别是什么](https://www.freecodecamp.org/chinese/news/npm-vs-npx-whats-the-difference/)
- [package.json 详解](https://juejin.cn/post/6844904006746112007)
- [package.json 自定义字段](https://juejin.cn/s/package.json%20自定义字段)
- [npx 使用教程](https://www.ruanyifeng.com/blog/2019/02/npx.html)
- [npm scripts 使用指南](https://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)


## npm
npm是node.js中的包管理器，npm 使 JavaScript 开发人员可以快速方便地共享软件包。

npm 由两个主要部分组成:

- 用于发布和下载程序包的 CLI（命令行界面）工具
- 托管 JavaScript 程序包的在线存储库

npm 本身并不运行任何软件包。如果你想使用 npm 运行一个包，你必须在 `package.json` 文件中指定这个包。

当可执行文件通过 npm 包安装时，npm 会创建链接指向它们。这些包不是安装为全局可执行文件，而是安装在：

- 本地安装的链接是在 `./node_modules/.bin/` 目录下创建的
- 全局安装会在全局 bin/ 目录下创建链接（例如：Linux 上的 `/usr/local/bin` 或 Windows 上的 `%AppData%/npm`）

要用 npm 执行一个包，你必须输入本地路径,比如要执行刚`npm install prettier`安装的`prettier`来美化代码，你需要：
```sh
./node_modules/.bin/prettier 。 --check
```

或者通过在脚本部分的 package.json 文件中添加一个本地安装的软件包来运行它:
```json
{
  "name": "your-application",
  "version": "1.0.0",
  "scripts": {
    "prettier-code": "prettier . --write"
  }
}
```
然后用`npm run prettier-code`来运行这个脚本。

## npx

### 主要功能
npx 想要解决的主要问题，就是**调用项目内部安装的模块**。

还是以上文的`prettier`为例，
```sh
./node_modules/.bin/prettier . --check
```
现在只需要写成
```sh
npx prettier . --check
```
> npx 的原理很简单，就是运行的时候，会到`node_modules/.bin`路径和环境变量`$PATH`里面，检查命令是否存在。
> 由于 npx 会检查环境变量$PATH，所以系统命令也可以调用。
>
`npx ls` 就等于 `ls`。

### 执行未安装的软件包
如果只想使用一次某些软件包（不管是出于测试还是什么目的），那么你没有必要本地全局安装。npx可以实现把该软件包下载到一个临时的目录，执行完成就删除软件包，降低磁盘占用。

npx的这一功能 使测试一个 Node.js 包或模块的不同版本变得非常容易。（不过我暂时没有什么使用场景）

## package.json
每个 JavaScript 项目（无论是 Node.js 还是浏览器应用程序）都可以被当作 npm 软件包，并且通过  `package.json`  来描述项目和软件包信息。当运行  `npm init`  初始化 JavaScript/Node.js 项目时，将生成  `package.json`  文件。

> 项目的 package.json 是配置和描述如何与程序交互和运行的中心。 npm CLI（和 yarn）用它来识别你的项目并了解如何处理项目的依赖关系。package.json 文件使 npm 可以启动你的项目、运行脚本、安装依赖项、发布到 NPM 注册表以及许多其他有用的任务。 npm CLI 也是管理 package.json 的最佳方法，因为它有助于在项目的整个生命周期内生成和更新 package.json 文件。

`package.json`里有很多字段，如果需要的时候最好的办法还是查阅官方文档：[Specifics of npm's package.json handling](https://docs.npmjs.com/cli/v10/configuring-npm/package-json#browser)。

我这里记录一下几个我觉得很常用的部分。

### main
```sh
{
    "main": "src/index.js",
}
```

> The main field is a module ID that is the primary entry point to your program. That is, if your package is named foo, and a user installs it, and then does require("foo"), then your main module's exports object will be returned.
> This should be a module relative to the root of your package folder.

也就是说，`main`字段定义了项目的入口点，当导入此包的时候，`main`指定的入口文件中的`module.exports`中的内容会被返回。


### scripts

> The "scripts" property of your package.json file supports a number of built-in scripts and their preset life cycle events as well as arbitrary scripts. These all can be executed by running npm run-script \<stage> or npm run \<stage> for short. 

npm默认提供了一些脚本和生命周期钩子，不过我们完全可以修改以及创建自定义的脚本。

npm脚本实际上就是shell脚本，每当执行npm run，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。

#### Life Cycle Scripts
npm 脚本有pre和post两个钩子。举例来说，build脚本命令的钩子就是prebuild和postbuild。
```json
{
    "scripts":{
        "prebuild": "echo I run before the build script",
        "build": "cross-env NODE_ENV=production webpack",
        "postbuild": "echo I run after the build script"
    }
}
```
用户执行npm run build的时候，会自动按照下面的顺序执行。

npm run prebuild && npm run build && npm run postbuild

#### 创建自定义脚本
```sh
{
    "scripts":{
        "prepare": "husky install"
    }
}
```
### dependencies
这是 package.json 中最重要的字段之一，它列出了项目使用的所有依赖项（项目所依赖的外部代码）。

### devDependencies
与 dependencies 字段类似，但是这里列出的包仅在开发期间需要，而在生产中不需要。再生产环境中可以通过`npm install --production`来减少安装包体积。

### peerDependencies, overrides...
还有很多字段，也是非常实用的。但这部分一般遇不到，你只需要遇到的时候知道怎么搜索对应的关键字即可。

### 自定义字段
在 package.json 文件中，还可以自定义字段（这些字段不会影响到 npm 对包的处理，主要是用来存储信息，特定的软件包可以读取）。

要自定义字段，只需要在 package.json 文件中添加新的键值对即可。

```json
{
    "name": "angular-love-pdf",
    "version": "0.0.0",
    "lint-staged": {
    "**/*": "prettier --write --ignore-unknown"
  },
}
```

# 总结

我觉得想学习JavaScript的工程化应用，首先必须学会使用npm和`packages.json`。通过这篇文章，我也加深了自己对这些概念的理解。
