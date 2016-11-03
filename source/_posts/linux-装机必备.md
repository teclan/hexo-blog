---
title: linux 装机必备
date: 2016-11-03 20:39:28
tags: [linux，oh-my-zsh]
---
# 系统设置打不开
重装桌面
```
apt-get install ubuntu-desktop
```
# 右键打开终端
安装 nautilus-open-terminal
```
apt-get install nautilus-open-terminal
```
# 使用 oh-my-zsh
```
apt-get install zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# oh-my-zsh 替换 系统 bash
chsh -s /bin/zsh
```
# 推荐

## tilda
```
apt-get install tilda
```
## htop
```
apt-get install htop
```
## 全屏启动终端
system-->All Setings-->Keyboard-->Shortcuts-->Custom Shortcuts,
点击加号（+）添加快捷键名称和对应的命令，完成后选择应用。双击新建快捷键右侧设置快捷键
