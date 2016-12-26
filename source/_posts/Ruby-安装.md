---
title: Ruby 安装
date: 2016-12-26 09:45:01
tags: [ruby]
---

## 源码安装Ruby
直接官网下载源码压缩包
### 安装依赖
```
sudo apt-get install gcc g++ make automake autoconf curl-devel openssl-devel zlib-devel httpd-devel apr-devel apr-util-devel sqlite-devel

sudo apt-get install ruby-rdoc ruby-devel
```
解压缩下载的ruby源码，进入压缩目录依次执行
```
./configure
make
make install
```
安装成功后执行以下命令查看安装的ruby版本
```
ruby -v
```



可选rbenv安装或ruby-build安装
## rbenv安装Ruby
 >点击[此处](https://github.com/rbenv/rbenv)访问项目或直接访问 https://github.com/rbenv/rbenv

### 克隆rbenv源码&编译

克隆 rbenv 至 ~/.rbenv （非root用户）
```
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
```

### 编译
```
$ cd ~/.rbenv && src/configure && make -C src
```
### 配置环境变量
以下 $HOME 表示当前用户的绝对路径，例如用户dev，$HOME则对应/home/dev
#### centOS

```
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >>  ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc
```
#### Ubuntu
```
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc
```
#### Zsh
```
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc
```
### 初始化
```
~/.rbenv/bin/rbenv init
```
### 确认安装
重启终端，执行
```
type rbenv
```
输出如下
```
rbenv is hashed (/home/dev/.rbenv/bin/rbenv)
```
或
```
rbenv is a function
```
## 安装指定版本Ruby
查看Ruby版本列表
```
rbenv install -l
```
找到需要的版本后执行 install，例如
```
rbenv install 2.0.0-p247
```

## 使用ruby-build安装Ruby
[项目地址](https://github.com/rbenv/ruby-build) https://github.com/rbenv/ruby-buil

 ruby-build可以作为rbenv插件安装，也可作为独立的工具

### 克隆源码（作为插件安装）
```
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```
使配置生效（不同环境配置文件可能不同）
```
source ~/.bashrc
```
### 作为独立工具
```
git clone https://github.com/rbenv/ruby-build.git
cd ruby-build
./install.sh
```
#### 使用 ruby-build安装ruby
```
ruby-build 2.2.0 ~/local/ruby-2.2.0
```

## 安装Gems
### 直接安装
安装Ruby之后，执行以下命令安装gems
```
sudo gem install bundler
```
执行以下命令查看安装gems版本
```
gem env home
```
### 源码安装
[下载源码](https://www.ruby-lang.org/en/downloads/) https://www.ruby-lang.org/en/downloads/
进入解压缩后的目录执行
```
ruby setup.rb
```
