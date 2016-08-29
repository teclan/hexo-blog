---
title: linux 常见命令
date: 2016-07-09 15:43:22
tags: [linux]
---

# 文件和目录

## 进入目录
```bash
cd /home    #进入/home目录
cd ..   #返回上一级目录
cd ../    #返回上一级
cd ../..    #返回上两级目录

cd    #进入用户主目录
cd  ~   #进入用户主目录
cd  ~username   #进入用户主目录,username为用户名
```

## 查看路径
```bash
pwd   #查看当前路径
```
## 查看文件信息
```bash
ls    #查看目录中的文件,不显示隐藏文件
ls -a   #查看当前目录的所有文件，包括隐藏文件
ls -l   #查看当前目录文件的详细信息,隐藏文件不显示，可以使用 ll 代替
ls -al  #查看当前目录所有文件的详细信息，包括隐藏文件
ls *(0-9)*    #显示名包含数字的文件或目录

tree    #查看当前目录的树形结构(需安装)
```
## 目录
```bash
mkdir dir1    #创建文件夹 dir1
mkdir -p dir1/dir2/dir3   #递归创建目录 dir1/dir2/dir3,不存在的目录会创建，已创建的忽略
mkdir dir1 dir2   #同时创建多个目录

rmdir dir1  #删除空目录 dir1
rmdir dir1 dir2   #删除多个空目录

rm -rf  dir1  #删除目录dir1和里面的所有内容  
```
## 文件
```bash
touch file1   ##创建空文件 file1
echo “some content”>> file1  # 向file1追加内容，file1必须是文本文件
rm file1    #删除文件file1
rm -rf file1 #删除文件file1,file1可以是目录

cat file1 #从第一个字节开始正向查看文件的内容
tac file1 #从最后一行开始反向查看一个文件的内容
more file1 #查看一个长文件的内容
less file1 #类似于 'more' 命令，但是它允许在文件中和正向操作一样的反向操作
head -n file1 #查看一个文件的前n行
tail -n file1 #查看一个文件的最后n行
tail -f /var/log/messages #实时查看被添加到一个文件中的内容
```
## 复制
```bash
cp fileName1 fileName2    #在当前目录复制fileName1,并命名为fileName2
cp fileName1 dir1   #复制当期目录的文件file1Name1到dir1目录，如果dir1不是目录，则意义同上
cp fileName1 dir1/fileName2   #复制当期目录的文件file1Name1到dir1目录,并将文件名字改成fileName2,如果dir1下存在同名文件，将被覆盖
cp -r dir1  dir2    #将dir1目录下的所有文件复制到dir2目录下
cp dir1/* dir2    #将dir1目录下的所有文件复制到dir2目录下
cp dir1/* .   #复制dir1目录下的所有文件到当前工作目录，注意符号"."
cp -a dir1/ .   #复制dir1目录下的所有文件到当前工作目录，注意符号"."
```
## 移动
```bash
mv fileName1 fileName2    #移动fileName1到fileName2,就是重命名文件
mv dir1 dir2 #移动dir1到dir2,就是重命名文件夹
```
## 权限
```bash
chmod ugo+rwx dir1  #设置目录的所有人(u)、群组(g)以及其他人(o)以读（r ）、写(w)和执行(x)的权限
chmod go-rwx dir1   #删除群组(g)与其他人(o)对目录的读写执行权限
chown user1 file1   #改变一个文件的所有人属性
chown -R user1 dir1  #改变一个目录的所有人属性并同时改变改目录下所有文件的属性
```
### r,w,x权限的定义:
#### 文件
r:读取文件的内容
w:编辑,新增,修改文件的内容(但不含删除该文件的权限)
x:可执行
#### 目录
r:读取目录结构(查询目录下的文件名)
w:更改目录结构(创建文件或目录,删除文件或子目录,重命名文件或子目录,移动文件或目录)
x:能否进入目录将目录变成工作目录,只有w权限没有x权限,是不能进入目录的,也就无从谈起对目录进行写操作了,
  一般要给开放目录给任何人浏览时,至少给r-x权限


## 打包压缩
### zip
常见参数：
-m     将文件压缩之后，删除源文件
-o     将压缩文件内的所有文件的最新变动时间设为压缩时候的时间
-q     安静模式，在压缩的时候不显示指令的执行过程
-r     将指定的目录下的所有子目录以及文件一起处理
-t 日期     把压缩文件的最后修改日期设为指定的日期，日期格式为mmddyyyy
```bash
zip -r java.zip /home/dev/Codes/java  #将/home/dev/Codes/java下的所有内容压缩成当前目录下的java.zip,会显示文件添加过程
zip -rq java.zip /home/dev/Codes/java   #将/home/dev/Codes/java下的所有内容压缩成当前目录下的java.zip,不显示文件添加过程
```
### unzip
可以用来解压 .jar文件
常见参数：
-c 将解压缩的结果显示到屏幕上，并对字符做适当的转换。
-f 更新现有的文件。
-l 显示压缩文件内所包含的文件。
-p 与-c参数类似，会将解压缩的结果显示到屏幕上，但不会执行任何的转换。
-t 检查压缩文件是否正确。
-u 与-f参数类似，但是除了更新现有的文件外，也会将压缩文件中的其他文件解压缩到目录中。
-v 执行是时显示详细的信息。
-z 仅显示压缩文件的备注文字。
-a 对文本文件进行必要的字符转换。
-b 不要对文本文件进行字符转换。
-C 压缩文件中的文件名称区分大小写。
-j 不处理压缩文件中原有的目录路径。
-L 将压缩文件中的全部文件名改为小写。
-M 将输出结果送到more程序处理。
-n 解压缩时不要覆盖原有的文件。
-o 不必先询问用户，unzip执行后覆盖原有文件。
-P<密码> 使用zip的密码选项。
-q 执行时不显示任何信息。
-s 将文件名中的空白字符转换为底线字符。
-V 保留VMS的文件版本信息。
-X 解压缩时同时回存文件原来的UID/GID。
```bash
unzip java.zip  #解压java.zip到当前目录
unzip java.zip dir1 #解压java.zip到dir1
```
### tar
常见参数：
-c 产生新的包或称建立压缩档案
-f 指定包含的文件
-r 增加文件
-u 更新文件
-t 列出文件
-x 解压缩
-z 调用gzip压缩
-j 调用bzip2压缩
-Z 调用compress压缩

```bash
tar -cvf /tmp/etc.tar /etc #将/etc目录下的所有文件打包到/tmp/etc.tar,不压缩
tar -zcvf /tmp/etc.tar.gz /etc #将/etc目录下的所有文件打包到/tmp/etc.tar,以gzip压缩
tar -jcvf /tmp/etc.tar.bz2 /etc #将/etc目录下的所有文件打包到/tmp/etc.tar,以 bzip2 压缩
tar -cf all.tar *.jpg   #将当前目录所有.jpg的文件打成一个名为all.tar的包
tar -rf all.tar *.gif   #将所有.gif的文件增加到all.tar的包里面
tar -uf all.tar logo.gif  #更新原来tar包all.tar中logo.gif文件
tar -zxvf /tmp/etc.tar.gz etc/passwd #至解压/tmp/etc.tar.gz 内的 etc/passwd
tar -xf all.tar  #解出all.tar包中所有文件

tar -czf all.tar.gz *.jpg   #将所有.jpg的文件打成一个tar包，并且将其用gzip压缩，生成一个gzip压缩过的包
tar -xzf all.tar.gz  #将上面产生的包解开

tar -cjf all.tar.bz2 *.jpg   #将所有.jpg的文件打成一个tar包，并且将其用bzip2压缩，生成一个bzip2压缩过的包
tar -xjf all.tar.bz2  #将上面产生的包解开

tar -cZf all.tar.Z *.jpg   #将所有.jpg的文件打成一个tar包，并且将其用compress压缩，生成一个uncompress压缩过的包
tar -xZf all.tar.Z #将上面产生的包解开
```
### rar
```bash
rar a all *.jpg 　#这条命令是将所有.jpg的文件压缩成一个rar包，名为all.rar，该程序会将.rar扩展名将自动附加到包名后。
unrar e all.rar   #将all.rar中的所有文件解压
```
### 文本处理
```bash
grep Aug /var/log/messages #在文件 '/var/log/messages'中查找关键词"Aug"
grep ^Aug /var/log/messages #在文件 '/var/log/messages'中查找以"Aug"开始的词汇
grep [0-9] /var/log/messages #选择 '/var/log/messages' 文件中所有包含数字的行
grep Aug -R /var/log/* #在目录 '/var/log' 及随后的目录中搜索字符串"Aug"
sed 's/stringa1/stringa2/g' #example.txt 将example.txt文件中的 "string1" 替换成 "string2"
sed '/^$/d' example.txt #从example.txt文件中删除所有空白行
sed '/ *#/d; /^$/d' example.txt #从example.txt文件中删除所有注释和空白行
echo 'esempio' | tr '[:lower:]' '[:upper:]' #合并上下单元格内容
sed -e '1d' result.txt #从文件example.txt 中排除第一行
sed -n '/stringa1/p' #查看只包含词汇 "string1"的行
sed -e 's/ *$//' example.txt #删除每一行最后的空白字符
sed -e 's/stringa1//g' example.txt #从文档中只删除词汇 "string1" 并保留剩余全部
sed -n '1,5p;5q' example.txt #查看从第一行到第5行内容
sed -n '5p;5q' example.txt# 查看第5行
sed -e 's/00*/0/g' example.txt #用单个零替换多个零
cat -n file1 #标示文件的行数
cat example.txt | awk 'NR%2==1' #删除example.txt文件中的所有偶数行
echo a b c | awk '{print $1}' #查看一行第一栏
echo a b c | awk '{print $1,$3}' #查看一行的第一和第三栏
paste file1 file2 #合并两个文件或两栏的内容
paste -d '+' file1 file2 #合并两个文件或两栏的内容，中间用"+"区分
sort file1 file2 #排序两个文件的内容
sort file1 file2 | uniq #取出两个文件的并集(重复的行只保留一份)
sort file1 file2 | uniq -u #删除交集，留下其他的行
sort file1 file2 | uniq -d #取出两个文件的交集(只留下同时存在于两个文件中的文件)
comm -1 file1 file2 #比较两个文件的内容只删除 'file1' 所包含的内容
comm -2 file1 file2 #比较两个文件的内容只删除 'file2' 所包含的内容
comm -3 file1 file2 #比较两个文件的内容只删除两个文件共有的部分
```


## 安装包
### rpm安装
```bash
rpm -ivh package.rpm #安装一个rpm包
rpm -ivh --nodeeps package.rpm #安装一个rpm包而忽略依赖关系警告
rpm -U package.rpm #更新一个rpm包但不改变其配置文件
rpm -F package.rpm #更新一个确定已经安装的rpm包
rpm -e package_name.rpm #删除一个rpm包
rpm -qa #显示系统中所有已经安装的rpm包
rpm -qa | grep httpd #显示所有名称中包含 "httpd" 字样的rpm包
rpm -qi package_name #获取一个已安装包的特殊信息
rpm -ql package_name #显示一个已经安装的rpm包提供的文件列表
rpm -qc package_name #显示一个已经安装的rpm包提供的配置文件列表
rpm -q package_name --whatrequires #显示与一个rpm包存在依赖关系的列表
rpm -q package_name --whatprovides #显示一个rpm包所占的体积
rpm -q package_name --scripts #显示在安装/删除期间所执行的脚本l
rpm -q package_name --changelog #显示一个rpm包的修改历史
rpm -qf /etc/httpd/conf/httpd.conf #确认所给的文件由哪个rpm包所提供
rpm -qp package.rpm -l #显示由一个尚未安装的rpm包提供的文件列表
rpm --import /media/cdrom/RPM-GPG-KEY #导入公钥数字证书
rpm --checksig package.rpm #确认一个rpm包的完整性
rpm -qa gpg-pubkey #确认已安装的所有rpm包的完整性
rpm -V package_name #检查文件尺寸、 许可、类型、所有者、群组、MD5检查以及最后修改时间
rpm -Va #检查系统中所有已安装的rpm包- 小心使用
rpm -Vp package.rpm #确认一个rpm包还未安装
rpm2cpio package.rpm | cpio --extract --make-directories *bin* #从一个rpm包运行可执行文件
rpm -ivh /usr/src/redhat/RPMS/`arch`/package.rpm #从一个rpm源码安装一个构建好的包
rpmbuild --rebuild package_name.src.rpm #从一个rpm源码构建一个 rpm 包
```
### yum安装
Fedora, RedHat及类似系统
```bash

yum install package_name #下载并安装一个rpm包
yum localinstall package_name.rpm #将安装一个rpm包，使用你自己的软件仓库为你解决所有依赖关系
yum update package_name.rpm #更新当前系统中所有安装的rpm包
yum update package_name #更新一个rpm包
yum remove package_name #删除一个rpm包
yum list #列出当前系统中安装的所有包
yum search package_name #在rpm仓库中搜寻软件包
yum clean packages #清理rpm缓存删除下载的包
yum clean headers #删除所有头文件
yum clean all #删除所有缓存的包和头文件
```
### deb
Debian, Ubuntu 以及类似系统
```bash
dpkg -i package.deb #安装/更新一个 deb 包
dpkg -r package_name #从系统删除一个 deb 包
dpkg -l #显示系统中所有已经安装的 deb 包
dpkg -l | grep httpd #显示所有名称中包含 "httpd" 字样的deb包
dpkg -s package_name #获得已经安装在系统中一个特殊包的信息
dpkg -L package_name #显示系统中已经安装的一个deb包所提供的文件列表
dpkg --contents package.deb #显示尚未安装的一个包所提供的文件列表
dpkg -S /bin/ping #确认所给的文件由哪个deb包提供
```
### apt
Debian, Ubuntu 以及类似系统，ubuntu16.04开始已经将apt-get替换成apt。
```bash
apt-get install package_name #安装/更新一个 deb 包
apt-cdrom install package_name #从光盘安装/更新一个 deb 包
apt-get update #升级列表中的软件包
apt-get upgrade #升级所有已安装的软件
apt-get remove package_name #从系统删除一个deb包
apt-get check #确认依赖的软件仓库正确
apt-get clean #从下载的软件包中清理缓存
apt-cache search searched-package #返回包含所要搜索字符串的软件包名称
```
# 文件系统
## 初始化一个文件系统
```bash
mkfs /dev/hda1 #在hda1分区创建一个文件系统
mke2fs /dev/hda1 #在hda1分区创建一个linux ext2的文件系统
mke2fs -j /dev/hda1 #在hda1分区创建一个linux ext3(日志型)的文件系统
mkfs -t vfat 32 -F /dev/hda1 #创建一个 FAT32 文件系统
fdformat -n /dev/fd0 #格式化一个软盘
mkswap /dev/hda3 #创建一个swap文件系统
```
## SWAP文件系统
```bash
mkswap /dev/hda3 #创建一个swap文件系统
swapon /dev/hda3 #启用一个新的swap文件系统
swapon /dev/hda2 /dev/hdb3 #启用两个swap分区
```
## 备份
```bash
dump -0aj -f /tmp/home0.bak /home #制作一个 '/home' 目录的完整备份
dump -1aj -f /tmp/home0.bak /home #制作一个 '/home' 目录的交互式备份
restore -if /tmp/home0.bak #还原一个交互式备份
```
# 网络
```bash
ifconfig #显示网络信息，相当于windows的ipconfig
ifconfig eth0 #显示指定以太网卡的配置
ifup eth0 #启用一个 'eth0' 网络设备
ifdown eth0 #禁用一个 'eth0' 网络设备
ifconfig eth0 192.168.1.1 netmask 255.255.255.0 #设置网卡信息
ifconfig eth0 promisc 设置 'eth0' #成混杂模式以嗅探数据包 (sniffing)
dhclient eth0 #以dhcp模式启用 'eth0'
```
# 系统
## 软件查询
### 查询版本
```bash
aptitude show python #查询python版本，需要先install  aptitude
```
或者
```bash
dpkg -l python #查询python版本
```
### 查询路径
```bash
dpkg -L python #查询python安装路径
```
或
```bash
whereis python #查询python安装路径
```


## 其他
```bash
fdisk -l #查看硬盘分区
ps aux #列出所有进程
ps aux | grep  agent #列出名字中带有”agent“的进程
kill pid #结束id为pid的进程
kill -9 pid #强制结束id为pid的进程
reboot #重启系统
poweroff #关机
init 0 #关机
halt #关机，但是需要手动切断电源，不推荐使用
shutdown -h now #立即关机，后面的now可以替换成时间
shutdown -r now #重启系统

whereis cmd #例如 whereis ls，查看ls命令所在位置

find / -name * #查询/下所有文件，同 ls /

useradd user1 #添加用户user1
userdel user1 #删除用户user1
passwd user1 #给user1设置密码

su - #切换root
su -  user1 #从root切换成user1

who #查看登录情况

echo #显示环境变量信息，如 echo JAVA_HOME

grep -ri "some" #在当前目录下查看带有”some”的文件内容，包括目录和文件按名，文本文件内容
```
