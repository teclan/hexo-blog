---
title: maven多模块
date: 2016-07-07 18:03:12
tags: [maven]
---
# Maven 多模块

[项目示例](https://github.com/teclan/teclan-parent)

 ## 新建 Maven 父项目
 根据需要删除目录下面的src/,只保留pox.xml,将
 ```Java
 <packaging>jar</packaging>
 ```
 改成
 ```Java
 <packaging>pom</packaging>
 ```
 设置父项目版本和编码，子项目版本一般跟父项目一致，故使用父项目的配置即可
 ```Java
 <properties>
 	   <teclan.version>0.1.0-SNAPSHOT</teclan.version>
     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
 </properties>
 ```
## 新建子项目(Module)
 配置同普通maven项目，例如创建示例Module项目 model-Demo1 和 model-Demo2,
 则在父项目的pom.xml中可看到增加如下内容
 ```Java
 <modules>
    <module>module-demo1</module>
  	<module>module-demo2</module>
  </modules>
  ```
# 依赖
子项目用到的共同依赖可以放到父项目中，如果子项目与父项目的依赖组件相同，但是版本号不同，
那么子项目会调用子项目的依赖组件

# 测试
在父项目下执行
```bash
$ mvn test
```
会执行所以子项目的单元测试，例如示例项目中：
```bash
$ path/to/project  mvn test
```
结果如下：
![](https://img.alicdn.com/imgextra/i4/1095268166/TB2q4wWspXXXXbIXXXXXXXXXXXX_!!1095268166.png)
# 打包
```bash
$ path/to/project mvn package -Dmaven.test.skip=True
```
每个子项目单独打出jar,其他配置待后续更新...
