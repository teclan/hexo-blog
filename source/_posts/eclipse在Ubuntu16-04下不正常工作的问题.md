---
title: eclipse在Ubuntu16.04下不正常工作的问题
date: 2016-07-04 10:51:36
tags: [eclipse,ubuntu]
---

# 解决办法

```bash
$ cd /path/to/eclipse
$ export SWT_GTK3=0
$ ./eclipse
```
推荐使用命令行的方式，网上其他的解决方法例如将
```bash
--launcher.GTK_versions
```
添加到eclipse目录下的eclipse.ini文件的 -launcher.appendVmargs 之前
```bash
-startup
plugins/org.eclipse.equinox.launcher_1.3.100.v20150511-1540.jar
plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.300.v20150602-1417
-product
org.eclipse.epp.package.java.product
--launcher.defaultAction
openFile
-showsplash
org.eclipse.platform
--launcher.XXMaxPermSize
256m
--launcher.defaultAction
openFile
--launcher.GTK_versions
--launcher.appendVmargs
-vmargs
-Dosgi.requiredJavaVersion=1.7
-XX:MaxPermSize=256m
-Xms256m
-Xmx1024m
```
发现还是不太稳定，经常用不了，也有推荐加到 -launcher.defaultAction 之前，但是都不稳定
<br>
如果不想重装系统的话，推荐命令行的方式，等待Ubuntu升级解决这个问题
