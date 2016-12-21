---
title: shadowsocks配置
date: 2016-07-06 17:03:40
tags: [shadowsocks]
---

>所有教程请点击[影梭官方](http://www.iyingsuo.com/help.html)
[推荐shadowsocks购买链接](https://www.abclite.net/aff.php?aff=226)

以下介绍Ubuntu下使用教程，其他平台，参考[影梭官方](http://www.iyingsuo.com/help.html)和[shadowsocks](https://github.com/shadowsocks/shadowsocks-qt5/wiki/%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)

### 购买服务
   [点此购买](https://www.abclite.net/aff.php?aff=226)

### 安装 shadowsocks-gui
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
完成后在命令行输入以下命令即可启动 shadowsocks-gui
```
ss-qt5
```

### 配置 shadowsocks-gui

[官方使用手册](https://github.com/shadowsocks/shadowsocks-qt5/wiki/%E4%BD%BF%E7%94%A8%E6%89%8B%E5%86%8C)

可以通过导入配置的方式导入数据到 shadowsocks-gui，配置在购买的服务列表处导出，通过配置文件导入的可能端口信息缺失，此时

建议通过手机客户端扫描对应二维码以查看实际端口。配置完成后点击连接即可。

如果不想用 gui，可使用 [服务端配置](http://www.iyingsuo.com/download.html)

需要 Pyhton 2.6+，

1、Install Shadowsocks.
```
$ pip install shadowsocks
```
2、创建配置文件 config.json ，格式如下：
```
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":600,
    "method":"table"
}
```
在 config.json 所在目录执行以下命令启动 shadowsocks
```
sslocal -c config.json
```


### Chrome 配置

[下载插件](https://yunpan.cn/cBFZxVTrIpBEe )并安装
（提取码：358b）

1、下载完成后在 设置 >> 扩展，直接把该 .crx 文件拖入窗口然后安装即可

	 然后打开 Proxy SwitchySharp 的设置，新建一个情景，命名为 iyingsuo （任意取名），

	 并设置好端口1080，模式为 socks5，看到“Please select the type of the frofile”

	 处选择“Proxy Profile”，然后选择你的情景模式 iyingsuo，在 Proxy Servers 下面选择

	 SOCKS5协议，后面的server和port填写 shadowsocks-gui 中的配置，一般默认为 127.0.0.1

	 和 1080

2、选择模式

	 在刚才新建的情景模式附近找到自动转换模式，添加需要代理的网站，例如 google.com等，这样就不会出现打不开的情况。

	 比如你要打开谷歌，在你初次访问谷歌的时候，就会出现打不开，然后选择这个插件按钮的 add condition（添加规则），

	 把google.com添加到代理里面就可以打开了，其实就是提前加好需要代理的主机而已。

 [官方图文教程](http://www.iyingsuo.com/chrome-shadowsocks-tutorials.html)

### FireFox 配置

[下载插件](http://pan.baidu.com/s/1bn2Refd)，提取密码：5usw

安装插件之后，右键选择该插件，选择 “首选项”-->"代理服务"-->"添加代理"

代理取名任意，代理主机和端口为 shadowsocks-gui 中的配置，一般默认为 127.0.0.1

和 1080，点击“确定”完成配置。接下来添加规则订阅，，右键选择该插件，选择 “首选项”-->"代理规则"

-->"添加规则订阅"，勾选 gfwlist 并启用，点击“应用”--->"确认"。
