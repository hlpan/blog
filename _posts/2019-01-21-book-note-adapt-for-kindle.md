---
layout: post
title: '修改Kindle电子书中的批注，减少闪屏'
date: 2019-01-21
author: Me

categories: other
tags: kindle html css
---

> 替换掉电子书中批注图片，使之更加适合于在Kindle上阅读

# 批注的一般形式
批注的开始使用一个图片来表示其类型，比如《红楼梦》中用![2019-01-21-00010.gif](https://raw.githubusercontent.com/hlpan/blog_image/master/img/2019-01-21-00010.gif)
表示“眉批”，用![2019-01-21-00002.gif](https://raw.githubusercontent.com/hlpan/blog_image/master/img/2019-01-21-00002.gif)
表示其来源于“甲戌本”。

#Kindle刷新机制&问题
就拿《红楼梦脂评汇校本》来说，整篇中大约有几千处批注，平均每页都有。这样做可以有效地区分正文和批注内容，但是却引入了过多的图片。
使用过Kindle的朋友都有印象，它的刷新机制中有全刷和局部刷新，全刷时屏幕会由黑到白切换一下（电子墨水屏的闪屏现像），而局部刷新只刷新文字。
全局刷新会带来更好的显示效果，但屏幕闪一下可能会干扰阅读。因此Kindle Paperwhite 1代在默认设置下，每6次局部刷新会有1次全局刷新。
而使用了Carte1.2屏幕的Kindle Paperwhite 4代则最多能连续几十次局部刷新。但这都是建立在纯文字内容的前提下，一旦页面中出现文字，Kindle则立即使用全局刷新。

在这个机制的作用下，包含大量批注的电子书，在阅读时会经常全局刷新，如下图：
![全局刷新闪屏现象](https://raw.githubusercontent.com/hlpan/blog_image/master/img/636BFB03CA6FC5F64C71ACE8D4111EF7.gif)

# 解决办法
我给出的解决办法也比较简单，思路就是用文字来代替图片。
但为了使得文字也能有醒目的效果，我用css的装饰一下。
以![Snipaste_2019-01-21_13-08-09.jpg](https://raw.githubusercontent.com/hlpan/blog_image/master/img/Snipaste_2019-01-21_13-08-09.jpg)
为例，来演示。
## 在css文件中增加
```css
.note0 {
  border-radius: 0.3em 0 0 0.3em;
  color: white;
  background-color: black;
  font-family: STKai, "MKai PRC", Kai, "楷体";
}
.note1 {
  border-radius: 0 0.3em 0.3em 0;
  color: white;
  background-color: gray;
  font-family: STKai, "MKai PRC", Kai, "楷体";
}
```
## 在html文件修改
在html文件中，使用：
```html
<span class="small kt red"><span class="note0">甲</span><span class="note1">侧</span>
```
替换掉，
```html
<span class="small kt red"><img alt="甲戌本" class="font_patch" src="../images/00002.gif"/><img alt="侧批" class="font_patch" src="../images/00011.gif"/>
```

# 结果
## 静态图
修改前vs修改后
![图片形式](https://raw.githubusercontent.com/hlpan/blog_image/master/img/screenshot_2019_01_21T13_17_14%2B0800.png)
![纯文本形式](https://raw.githubusercontent.com/hlpan/blog_image/master/img/screenshot_2019_01_21T13_16_47%2B0800.png)

## 连续翻页无“闪屏”
![连续翻页无闪屏.gif](https://raw.githubusercontent.com/hlpan/blog_image/master/img/%E8%BF%9E%E7%BB%AD%E7%BF%BB%E9%A1%B5%E6%97%A0%E9%97%AA%E5%B1%8F.gif)

