+++
title = 'Advanced Css Study Notes Section II'
date = 2023-11-08T22:22:01+08:00
draft = false
categories = ['前端']
tags = ['CSS', 'Sass', '笔记', '学习']
series = ['前端基础从入门到入土']
series_order = 2
+++

## Three Pillars to Write Good HTML and CSS... And Build Good Websites

- Responsive Design
  - Fluid layouts
  - Media queries
  - Responsive images
  - Correct units
  - Desktop-first v.s. mobile-first
- Maintainable and Scalable Code
  - Clean
  - Easy-to-understand
  - Growth
  - Reusable
  - How to organize files
  - How to name classes
  - How to structure HTML
- Web Performance
  - Less HTTP requests
  - Less code
  - Compress code
  - Use a CSS preprocessor
  - Less images
  - Compress images

## 网页渲染过程

![hello world text](<css_render.png>)

这部分也是很大的内容，我之前正好做过相关的笔记，哪天有空也放上来。下面是我学习时从各种博客上抄来的图片汇总：

![Alt text](web_performance.001.jpeg)

## CSS Cascade(层叠) and Specificity(优先级)

如果不想看视频，也可以直接阅读文档：[Cascade, specificity, and inheritance](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance)。

CSS的全程是什么？层叠样式表。这部分讲的是**CSS的层叠规则**。CSS的优先级可以从这几个角度区分。

- Source order
- Specificity
- Importance

### Source order
CSS可以通过

- 行内样式（行间样式、内联样式、行嵌样式）
- 内部样式表
- 链入外部样式表
- 导入外部样式表

4种 CSS 引用方式。优先级总结就是**就近原则**，谁里元素近，谁就优先级高。


### Importance
其中Importance部分优先级从高到低分别为：

- 用户代理样式表中的 `!important` 声明
- 用户样式表中的 `!important` 声明
- 作者样式表中的 `!important` 声明
- 作者样式表中的常规声明（这些是我们 web 开发人员设置的样式）
- 用户样式表中的常规声明（由用户设置的自定义样式）
- 用户代理样式表中的声明（例如，浏览器的默认样式，在没有设置其他样式时使用）

**Google Chrome Dev Tools 中修改的也是作者样式表。**

阅读更多：
- https://juejin.cn/post/7225634879151849509
- https://juejin.cn/post/6926822995142918152

#### 什么是用户代理样式表
> 用户代理（User Agent，通常指浏览器）为HTML元素提供了一组默认的样式。这些样式在没有任何外部样式表的情况下应用于网页，以确保即使在没有CSS的情况下，网页内容依然具有基本的可读性和可访问性。用户代理样式因浏览器而异，通常包括基本的字体、颜色、边距等设置。

#### 什么是用户样式表
> 用户样式表是由用户自定义的样式表，可以用于覆盖用户代理样式和作者样式表中的某些样式。用户可以根据自己的偏好设置用户样式表，例如调整字体大小、颜色等。用户样式表主要用于改善网页的可访问性和可用性，满足个别用户的特殊需求。在现代浏览器中，用户可以通过安装扩展或修改浏览器设置来应用自定义的用户样式表。

#### 什么是作者样式表
> 作者样式表是由网页开发者创建的样式表，用于定义网页的布局、外观和交互。作者样式表通常是网页设计的主要组成部分，它可以覆盖用户代理样式和用户样式表中的样式。作者样式表可以包含内联样式、嵌入式样式和外部样式表，以及通过@import引入的其他样式表。

### 选择器优先级（Specificity）

选择器有很多种，元素选择器，类选择器，ID选择器，属性选择器，伪类选择器，伪元素选择器，组合选择器。

那怎么计算层叠最后的结果呢？其实就是给不同的选择器赋予不同的权重，最后累计求和，值越大优先级越高。当多个选择器应用于同一元素时，具有较高特指性的选择器将覆盖较低特指性的选择器。

- 内联样式的权重为 1000
- ID选择器的权重为 100
- 类选择器、属性选择器和伪类的权重为 10
- 元素选择器和伪元素的权重为 1

#### 例子
下面的例子，最后的四元组就是对应类型选择器的数量，与对应的权重相乘后相加即可得到最高优先级。（或者我们可以直接从高到低比较大小）

![Alt text](<CleanShot 2023-11-11 at 23.44.13.png>)

再看这个例子：

```css
#nav div.pull-right a.buttom{
  background-color: red;
}

#nav a.button:hover{
  background-color: yellow;
}
```

如果这两个组合选择器都指向同一个元素，那最后哪个会生效呢？我之前存在一个误区，认为`:hover`这种`行为`的时候，肯定是下面的选择器生效，会变成`yellow`，但实际上`:hover`是伪类，也是通过优先级计算的，优先级权重是10，但前者的权重结果是：`100+10+1` > 后者的结果`100+10`，所以即使鼠标移动到该元素上，也还是红色。

## CSS Value Processing

![Alt text](<CleanShot 2023-11-12 at 00.03.47.png>)

### 相对大小转换为绝对大小
![Alt text](<CleanShot 2023-11-12 at 00.08.11.png>)

### CSS Value Processing： What You Need To Know
- 每个属性都有默认值，used if nothing is declared
- 浏览器为每个页面声明了一个默认的root font-size
- 百分比和相对大小总是会换算为绝对大小

。。。翻译半天，感觉还不如直接看原文。

![Alt text](<CleanShot 2023-11-12 at 00.12.40.png>)

hello world: 310
hello world: 239
