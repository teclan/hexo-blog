---
title: Hexo Github pages 搭建博客
date: 2016-07-03 16:01:11
tags: [Hexo,Github-pages]
---

# 安装Nodejs
[下载 Nodejs ](https://nodejs.org/en/)

# 配置环境变量（ubuntu）

在环境配置文件中添加（形如）：
```bash
Nodejs="/path/to/nodejs"   
PATH="$Nodejs/bin/:...."  
```
使配置生效：
```bash
source .zshrc #具体配置文件根据自己情况指定
```
# 使用cnpm替换npm(npm需要扶墙)
了解[淘宝源](https://npm.taobao.org)
执行
```bash
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```
#安装Hexo
```bash
$ cnpm install -g hexo
```
#创建一个博客项目目录（例如：blog)
在目录中执行
```bash
$ hexo init
```
Hexo随后会自动在目标文件夹建立网站所需要的所有文件

#部署
```bash
$ hexo g
$ hexo s
```
如果没有错误，在浏览器输入localhost:4000即可查看，
如果有错，访问[常见错误以及解决方法](https://hexo.io/docs/troubleshooting.html)

# 设置主题
[查看主题](https://hexo.io/themes)
右键选择 主题 下方蓝色字体，选择“open new link in new tab”,打开主题项目
克隆主题 例如：git clone git@github.com:Kaijun/hexo-theme-huxblog.git themes/hux
在themes/hux下执行
```bash
$ npm install hexo-renderer-sass --save
$ npm install hexo-renderer-jade --save
```
然后在根目录修改根目录中的 _config.yml 如下，将theme后面的文件名修改为hux，其他配置请查看主题目录下的README.md。
重新部署即可，如果不生效，先clean
```bash
$ hexo clean
$ hexo g
$ hexo s
```
如果还有问题，请查看[常见错误以及解决方法](https://hexo.io/docs/troubleshooting.html)
