---
layout: post
title: 如何剖斯特
date: 2025-05-07 00:02 +0800
pin: true
author: nagi
---


## jekyll-compose
[![Static Badge](https://img.shields.io/badge/Jekyll-Compose-55acee)](https://github.com/jekyll/jekyll-compose)
> **Naming and Path**
> 
> Create a new file named YYYY-MM-DD-TITLE.EXTENSION and put it in the _posts of the root directory. Please note that the EXTENSION must be one of md and markdown. If you want to save time of creating files, please consider using the plugin Jekyll-Compose to accomplish this.

在本章节中我将说明如何通过安装`jekyll-compose`并使用shell方便的管理文章，也就是说，Streamline your writing in Jekyll with some commands.当然安装之后，你也可以通过`bundle exec jekyll help`自行查看命令。

### 环境
先在`Gemfile`中添加依赖，并用`bundle`安装。
```
gem 'jekyll-compose', group: [:jekyll_plugins]
```

### 草稿
这个命令用于生成`_drafts`文件夹并生成`./draft/how-to-post.md`草稿。以下三者等价，都是生成新文章在草稿文件目录下。
```shell
bundle exec jekyll draft "how-to-post"

# or by using the compose command with draft specified
bundle exec jekyll compose "My new draft" --draft

# or by using the compose command with the drafts collection specified
bundle exec jekyll compose "My new draft" --collection "drafts"

```

### 发布
用shell发布的好处是会**自动更新发布时间**，表现为在题目前接上日期，以及文章内yaml中添加时间数据。

第一种方式是直接发布，以下三者等价。
```shell
bundle exec jekyll post "My New Post"

# or specify a custom format for the date attribute in the yaml front matter
bundle exec jekyll post "My New Post" --timestamp-format "%Y-%m-%d %H:%M:%S %z"

# or by using the compose command
bundle exec jekyll compose "My New Post"

# or by using the compose command with the posts collection specified
bundle exec jekyll compose "My New Post" --collection "posts"
```
或者从草稿中发布。
```shell
bundle exec jekyll publish _drafts/my-new-draft.md

# or specify a specific date on which to publish it
bundle exec jekyll publish _drafts/my-new-draft.md --date 2014-01-24
# or specify a custom format for the date attribute in the yaml front matter
bundle exec jekyll publish _drafts/my-new-draft.md --timestamp-format "%Y-%m-%d %H:%M:%S %z"
```
### 回溯、改名
你可以将发布的文章退回草稿状态，表现为文章回到`./_draft/`并因此离开`./post/`而不可见。
```shell
bundle exec jekyll unpublish _posts/2014-01-24-my-new-draft.md
```
compose提供三种对`published`文章的改名用途：改名、改日期、改为今天。
```shell
bundle exec jekyll rename _posts/2014-01-24-my-new-draft.md "My New Post"
# or specify a specific date

bundle exec jekyll rename _posts/2014-01-24-my-new-post.md "My Old Post" --date "2012-03-04"

# or specify the current date
bundle exec jekyll rename _posts/2012-03-04-my-old-post.md "My New Post" --now

```
对于`draft`文章提供简单改名
```shell
bundle exec jekyll rename _drafts/my-new-draft.md "My Renamed Draft"

# or rename it back
bundle exec jekyll rename _drafts/my-renamed-draft.md "My new draft"
```

### 另外
你也可以直接在根目录生成文章，按照`_cofig.yml`对`scope.path: ""的permalink`的默认映射规则，你将在`/:title/`找到他，也就是。