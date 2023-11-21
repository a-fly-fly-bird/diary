+++
title = 'VS Code工具 使用记录'
date = 2023-11-12T00:29:25+08:00
draft = false
categories = ['折腾']
tags = ['IDE', '博客']
+++

开个新坑，为了减少Hugo上传到仓库的图片大小，在插件市场看到个很符合我需求的东西：
[Learn Image 插件压缩图片](https://learn.microsoft.com/en-us/contribute/content/docs-authoring/image-compression)，顺便来了兴趣系统地学习了解一下VS Code的整个配置和个性化。

# Visual Studio Code 教程
VS Code是一个非常还用的编辑器，可以进行非常强大的自定义，具体参考：[Visual Studio Code 官网教程](https://code.visualstudio.com/docs/editor/codebasics)

# VS Code Vim
我一直对Vim编辑器有着一种独特的青睐，但苦于其学习成本和使用成本，投入了大量时间精力却没有得到什么回报。而[Vim for Visual Studio Code](https://github.com/VSCodeVim/Vim)的出现，提供了一个很好的平衡。

VS Code 的 Learn VIM 插件总结的很好：
> Visual Studio Code is superb. It offers an unparalleled user experience with great support for many languages and development ecosystem. It comes with great defaults and is super easy to use and get started with.
>
> Vim is awesome. It modal nature and text editing features make it unique amongst other editors. Vim offers a complete different level of text editing proficiency, speed and accuracy from anything else out there.
> 
> The combination of both couldn't be anything less than amazingly superbsome.

# 代码提示快捷键

默认的Trigger代码提示快捷键：

- `mac`: `cmd+space`
- `win`: `ctrl+space`

都是非常容易和系统快捷键冲突的。所以需要修改。去VS Code修改快捷键的界面，输入`Trigger Suggest`可以找到当前的触发代码提示的快捷键。可以看到默认有几个，如果你觉得一个都不好用，就修改自定义。如果你习惯了`cmd + i` 或者`ctrl + i`等已有方案，就可以不用修改了。修改也很简单，点击前面的笔图标，直接在键盘输入你要设置的快捷键方式，然后按enter即可。


