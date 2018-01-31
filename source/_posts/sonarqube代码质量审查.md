---
title: sonarqube代码质量审查
date: 2018-01-31 20:46:25
tags: [sonarqube,代码质量]
---


## 安装 sonarqube

进入[官网](https://www.sonarqube.org)下载最新的安装包，解压后选择与系统匹配的启动文件启动即可，在浏览器中输入 http://localhot:9000 进入sonarqube服务页面

## 配置

### 服务端口和URL配置

进入sonarqube 的 conf 目录，打开 sonar.properties，如下配置：

```
sonar.search.javaAdditionalOpts=-Des.network.host=127.0.0.1
sonar.web.host=0.0.0.0  
# 服务地址 localhot:9000/sonarqube,可选默认http://localhot:9000
sonar.web.context=/sonarqube  
# 服务端口
sonar.web.port=9000
```

### sonarqube 数据库配置

sonarqube支持h2，Oracle,Mysql，PostgreSql，SQLServer数据库，以PostgreSql配置为例，先创建一个空数据库，例如名为`sonar`,配置如下:

```
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonar
sonar.jdbc.username=postgres
sonar.jdbc.password=123456
sonar.sorceEncoding=UTF-8

# 以下是 sonarqube 服务的登录配置
sonar.login=admin
sonar.password=admin
```


### elasticsearch 配置

elasticsearch 配置可保持默认，若有改动需要在 sonar.properties 做相应修改，详情参见sonar.properties注释

### Maven 配置

配置 Maven 的配置文件 setting.xml,内容如下:

```
<settings>
    <pluginGroups>
        <pluginGroup>org.sonarsource.scanner.maven</pluginGroup>
    </pluginGroups>
    <profiles>
        <profile>
            <id>sonar</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <! sonarqube服务地，参考端口和URL配置，两处要一致 -->
                <sonar.host.url>
                   http://localhot:9000/sonarqube
                </sonar.host.url>
            </properties>
        </profile>
     </profiles>
</settings>
```

### 插件安装

sonarqube 的功能扩展需要插件支持，插件可在
[https://docs.sonarqube.org/display/PLUG/Plugin+Library](https://docs.sonarqube.org/display/PLUG/Plugin+Library)
查看下载，下载后的插件（.jar）放至sonarqube目录的 extensions\plugins 目录下，重启 sonarqube 即可。

## 代码分析

在项目根目录下执行
```
mvn sonar:sonar
```
等待执行完成，打开 [http://localhot:9000/sonarqube]( http://localhot:9000/sonarqube)，登录之后即可查看代码分析结果
