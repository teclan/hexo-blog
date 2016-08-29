---
title: ssh配置
date: 2016-07-06 17:03:40
tags: [ssh]
---

>仅介绍Linux下的方式

# Chrome 插件
[下载插件](https://yunpan.cn/cBFZxVTrIpBEe )并安装
（提取码：358b）

# 安装shadowsocks
```bash
$ sudo -H pip install shadowsocks
```

# 加载配置

配置文件格式如下（例如取名为：config.json）：
```bash
{
	"server":"xx.xx.xx.xx",
	"server_port":xx
	"local_address":"0.0.0.0",
	"local_port":1080,
	"password":"password",
	"timeout":300,
	"method":"aes-256-cfb",
	"fast_open":false,
	"workers":1
}
```
其中 method 是加密字段，根据购买的服务填写。
```bash
$ sslocal -c config.json
```

# 插件配置

打开安装的插件，设置如下，应用即可
```bash
Scheme	   Protocol	   Server    	Port
(default)	 SOCKS5      127.0.0.1  1080
```
