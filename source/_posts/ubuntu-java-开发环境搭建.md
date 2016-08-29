---
title: ubuntu java 开发环境搭建
date: 2016-07-12 10:20:13
tags: [ubuntu,java,ssh,git]
---

>ubuntu16.04 使用 apt 代替 apt-get


# Git
## 安装
```bash
sudo apt-get install git
```
## 生成key
设置你的邮箱信息
```bash
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# Creates a new ssh key, using the provided email as a label
Generating public/private rsa key pair.
```
设置你的配置目录，默认为 ~/.ssh/
```bash
$ Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
```
设置密码，默认为空，一直按回车即可
```bash
$ Enter passphrase (empty for no passphrase): [Type a passphrase]
$ Enter same passphrase again: [Type passphrase again]
```
# ssh
## 安装
一般成功装了git之后，ssh也已经安装，如果没有安装，则需要手动安装
```bash
sudo apt-get install openssh-server
```
## 确认sshserver是否启动了
```bash
ps -e | grep ssh
```
如果只有ssh-agent那ssh-server还没有启动，需要/etc/init.d/ssh start，如果看到sshd那说明ssh-server已经启动了
ssh-server配置文件位于/ etc/ssh/sshd_config，在这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号，如222。然后重启SSH服务：
```bash
sudo /etc/init.d/ssh resart
```
## 配置使登录时间更短
```bash
sudo vim /etc/ssh/sshd_config
```
找到 GSSAPI options 这一节，将下面两行注释掉：
```bash
#GSSAPIAuthentication yes
#GSSAPIDelegateCredentials no
```
然后重新启动 ssh 服务即可：
```bash
sudo /etc/init.d/ssh resart
```
# maven
## 下载
[点此下载](http://maven.apache.org/download.cgi)
## 配置
在置文件中添加（形如）：
```bash
M2="/path/to/maven"   
PATH="$M2/bin/:...."
```
使配置生效：
```bash
source .zshrc #具体配置文件根据自己情况指定
```
# jdk
## 下载
[点此下载](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
## 配置
解压后拷至某个目录后，在配置文件添加（形如）：
```bash
JAVA_HOME="/path/to/jdk"
PATH="$JAVA_HOME/bin:..."
```
修改默认jdk
```bash
sudo update-alternatives --install /usr/bin/java java /path/to/jdk/bin/java 300
sudo update-alternatives --install /usr/bin/javac javac /path/to/jdk/bin/javac 300
sudo update-alternatives --config java
```
# eclipse
## 下载
[点此下载](http://www.eclipse.org/)
