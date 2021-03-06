---
layout: post
title: '如何写这样的博客'
date: 2017-09-27
author: Me

categories: other
tags: jekyll images source-tree
---

> 第一篇博客,记录下如何写博客，以防长时间不写后重启.

# 安装Jekyll

## 安装Ruby
从 <https://rubyinstaller.org/downloads/>下载Ruby，
下载安装最新版，一切都是默认设置。我安装的是v2.4，而且也把
MSYS2 toolkit 给安装了。

## 安装jekyll插件

安装Ruby后，它自己带了gem，所以可以使用gem install命令来安装插件了

```bash
	gem install jekyll
	gem install jekyll-paginate
```
# 写博客
写一篇文章很简单，只是在_posts文件夹里添加一个符合格式的文本。
或者更加简单，复制原有的文本来修改。
注意文件名也按照这种规则。

# 本地预览
打开jekyll，命令如下：
```bash
	jekyll serve --watch
```
这样每改动一次文件，jekyll就自动更新网页。

# 使用Sourcetree上传博客

本地预览没有问题后，就可以在Sourcetree里面提交并push了。
网页文件默认是要push到master分支的，这样才能直接使用<https://hlpan.github.io>来访问。

# 使用七牛图床

使用VS code里面的插件来自动上传图片到图床并返回外链。
这个插件用着很方便，但是有点不稳定，期待后续更新了。
地址：<https://github.com/yscoder/vscode-qiniu-upload-image>
