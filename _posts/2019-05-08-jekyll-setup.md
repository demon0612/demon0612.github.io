---
layout: post
title: Jekyll本地环境搭建
categories: Jekyll
description: Jekyll本地环境搭建
keywords: Jekyll, MacOS, Windows
---

MacOS和Windows本地生成网页，方便Markdown实时预览

---

## MacOS
### 安装Homebrew

如果安装了Xcode，则直接在terminal中运行：
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
否则先执行：
```
xcode-select --install
```

### 安装Ruby

使用brew安装ruby:
```
brew install ruby
```
安装完ruby，将自动安装包管理器gem。

### 安装Jekyll

#### 本地安装

gem安装bundler jekyll包：

```
gem install --user-install bundler jekyll #local install
```

将gem包存放目录加入PATH环境变量：

```
ruby -v
export PATH=$HOME/.gem/ruby/X.X.0/bin:$PATH
```

其中X.X改成ruby当前版本号。

#### 全局安装
此方法需要sudo权限，如果没有，选择本地安装。

```
## 系统版本>=10.14
sudo gem install bundler
sudo gem install

# 系统版本<10.14
sudo gem install bundler jekyll
```

## Windows安装(待补充)

## 本地运行
### 已有模板

强烈建议在GitHub上找一个可以用的模板，然后本地运行，推荐本人自己的[模板](https://github.com/demon0612/demon0612.github.io)，欢迎Fork。
```
bundle exec jekyll serve
```
在浏览器地址栏中输入http://127.0.0.1:4000/即可查看Jekyll运行结果。

### 新建模板(待补充)

新建Gemfile，加入以下内容：
```
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
```
在terminal中运行：
```
bundle install
```


## 常见问题

### 错误提示：
```
Error: No source of timezone data could be found.
```
### 解决办法：
修改Gemfile，添加如下一行：
```
gem 'tzinfo-data'
```


## 参考文档
[Homebrew官网](https://brew.sh/)

[Jekyll教程](https://jekyllrb.com/docs/installation/macos/)

[GitHub指南](https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll)


