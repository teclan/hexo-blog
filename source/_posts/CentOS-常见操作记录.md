---
title: CentOS 常见操作记录
date: 2016-12-26 09:42:55
tags: [CentOS]
---
# 网络配置
## ip配置
网络配置文件路径:
```
/etc/sysconfig/network-scripts
```
例如：ifcfg-eth1，内容如下：
```
DEVICE=eth1
BOOTPROTO="static"
IPADDR="172.19.1.70"
BROADCAST="172.19.1.255"
GATEWAY="172.19.1.1"
HWADDR="6C:0B:84:41:54:CD"
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=yes
NETMASK="255.255.255.0"
```
，其中HWADDR必须为实际的MAC地址，可通过以下命令查看：
```
ifconfig
```
## hosts文件配置
文件路径:
```
/etc/hosts
```
# 启用图形化界面
在命令行输入以下命令
```
startx
```
设置开机默认进入图形化界面,修改配置文件 /etc/inittab，将 3 改成 5，原始文件
```
# inittab is only used by upstart for the default runlevel.
#
# ADDING OTHER CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
#
# System initialization is started by /etc/init/rcS.conf
#
# Individual runlevels are started by /etc/init/rc.conf
#
# Ctrl-Alt-Delete is handled by /etc/init/control-alt-delete.conf
#
# Terminal gettys are handled by /etc/init/tty.conf and /etc/init/serial.conf,
# with configuration in /etc/sysconfig/init.
#
# For information on how to write upstart event handlers, or how
# upstart works, see init(5), init(8), and initctl(8).
#
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
#
id:3:initdefault:                
```
修改后的文件
```
# inittab is only used by upstart for the default runlevel.
#
# ADDING OTHER CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
#
# System initialization is started by /etc/init/rcS.conf
#
# Individual runlevels are started by /etc/init/rc.conf
#
# Ctrl-Alt-Delete is handled by /etc/init/control-alt-delete.conf
#
# Terminal gettys are handled by /etc/init/tty.conf and /etc/init/serial.conf,
# with configuration in /etc/sysconfig/init.
#
# For information on how to write upstart event handlers, or how
# upstart works, see init(5), init(8), and initctl(8).
#
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
#
id:5:initdefault:              
```
