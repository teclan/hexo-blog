---
title: JAVA内存分析
date: 2018-01-31 20:44:38
tags: [JAVA,内存分析]
---

## jconsole

jconsole是一个用java自带的GUI程序，用来监控VM，并可监控远程的VM，非常易用，而且功能非常强。
使用方法：
命令行进入JAVA_HOME\bin\，执行 jconsole.exe,在打开的界面选择需要的进程即可。需要注意，如果电脑上存在多个JVM，需要在我们程序引用的JVM里面执行jconsole.exe（或者与其位数、版本一致）方可正常使用，否则出现认证异常或版本不一致的异常。
或者在命令输入
```
jconsole [PID]

# 例如

jconsole 2548
```
直接进入对应程序的监控页面。

## jmap

其中jmap是java自带的工具,要注意的是在使用CMS GC 情况下，jmap -heap的执行有可能会导致JAVA 进程挂起,使用方法：命令行进入JAVA_HOME\bin\，执行

```
# 查看整个JVM内存状态
jmap -heap [pid]

# 查看JVM堆中对象详细占用情况
jmap -histo [pid]

# 导出整个JVM 中内存信息
jmap  -dump:format=b,file=path\to\your\file.bin  [PID]
或
jmap  -dump:live,format=b,file=path\to\your\file.bin  [PID] 指定是存活的对象

#例如
jmap  -dump:format=b,file=G:\内存分析\heap-19031.bin  2548

导出的 heap 文件可使用 jhat 分析，命令如下
jhat -J-Xmx512m <heap dump file>

jmap -histo pid>a.log可以将其保存到文本中去

```

## Java VisualVM

对于使用命令行远程监控jvm 太麻烦 。 在jdk1.6 中 Oracle 提供了一个新的可视化的。 JVM 监控工具 Java VisualVM 。jvisualvm.exe 在JDK 的 bin 目录下。启动 Java VisualVM 后可以看到窗口左侧 “应用程序 ”栏中有“ 本地 ”、“远程 ” 、“快照 ”三个项目。
  “本地 ”下显示的是在 localhost 运行的 Java 程序的资源占用情况，如果本地有 Java 程序在运行的话启动 Java VisualVM 即可看到相应的程序名，点击程序名打开相应的资源监控菜单，以图形的形式列出程序所占用的 CPU 、 Heap 、 PermGen 、类、线程的 统计信息。
  “远程” 项下列出的远程主机上的 Java 程序的资源占用情况，但需要在远程主机上运行 jstatd 守护程序

## jstack
  jstack 是sun JDK 自带的工具，通过该工具可以看到JVM 中线程的运行状况，包括锁等待，线程是否在运行
执行 jstack [pid] ,线程的所有堆栈信息
"http-8080-10" daemon prio=10 tid=x0a949bb60 nid=0x884  waiting for monitor entry [...]

"http-8080-10" 这个线程处于等待状态。 waiting for monitor entry 如果在连续几次输出线程堆栈信息都存在于同一个或多个线程上时，则说明系统中有锁竞争激烈，死锁，或锁饿死的想象。

“http-8080-11” daemon prio=10 tix=xxx nid=xxx in object.wait() [...]
 java.lang.Thread.State:waiting (on object monitor)
该表示http-8080-11的线程处于对象的Wait 上，等待其他线程的唤醒，这也是线程池的常见用法。

“Low Memory Detector”daemon prio=10 tix=xx nid=xxx runnable [...] java.lang.Thread.State:runnable
表示“Low Memory Detector” 的线程处于Runable状态，等待获取ＣＰＵ的使用权.

## jstat

stat是JDK自带的一个轻量级小工具。全称“Java Virtual Machine statistics monitoring tool”，它位于java的bin目录下，主要利用JVM内建的指令对Java应用程序的资源和性能进行实时的命令行的监控，包括了对Heap size和垃圾回收状况的监控。可见，Jstat是轻量级的、专门针对JVM的工具，非常适用。

jstat工具特别强大，有众多的可选项，详细查看堆内各个部分的使用量，以及加载类的数量。使用时，需加上查看进程的进程id，和所选参数。以下详细介绍各个参数的意义。

jstat -class pid:显示加载class的数量，及所占空间等信息。

jstat -compiler pid:显示VM实时编译的数量等信息。

jstat -gc pid:可以显示gc的信息，查看gc的次数，及时间。其中最后五项，分别是young gc的次数，young gc的时间，full gc的次数，full gc的时间，gc的总时间。

jstat -gccapacity:可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old内纯的占用量。

jstat -gcnew pid:new对象的信息。

jstat -gcnewcapacity pid:new对象的信息及其占用量。

jstat -gcold pid:old对象的信息。

jstat -gcoldcapacity pid:old对象的信息及其占用量。

jstat -gcpermcapacity pid: perm对象的信息及其占用量。

jstat -util pid:统计gc信息统计。

jstat -printcompilation pid:当前VM执行的信息。

除了以上一个参数外，还可以同时加上 两个数字，如：jstat -printcompilation 3024 250 6是每250毫秒打印一次，一共打印6次，还可以加上-h3每三行显示一下标题。
