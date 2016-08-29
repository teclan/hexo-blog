---
title: Jekyll Github pages 搭建博客
date: 2016-07-03 17:04:37
tags: [Jekyll,Github-pages]
---

# 安装Ruby
 [查看官网文档](https://www.ruby-lang.org/zh_cn/documentation/installation/)

# 安装Gems
 [rubyGems官方文档](https://rubygems.org/pages/download)

# 安装 Jekyll
```bash
$ gem install jekyll
```
# 简单测试
```bash
$ jekyll new my-awesome-site
$ cd my-awesome-site
$ path/to/my-awesome-site/jekyll serve
```
打开浏览器 http://localhost:4000 即可查看

# 主题设置
 [挑选主题](http://jekyllthemes.org/)
 克隆主题
 在主题项目下执行
 ```bash
 $ bundle install
 $ jekyll serve
 ```
 即可看到主题效果，查看READM.md修改相关配置，应用主题

 # 附：jekyll 搭建博客与 Hexo 不同，jekylly 需要提交整个项目到远程仓库
