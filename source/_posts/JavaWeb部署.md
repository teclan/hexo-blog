---
title: JavaWeb部署
date: 2016-12-01 14:31:29
tags: [tomcat,jetty,sparkjava]
---

> [示例项目](https://github.com/teclan/teclan-web)

## JavaWeb 后台（JAVA程序）部署

### 路由配置
 必须实现 spark.servlet.SparkApplication，并且所有路由在

 init()方法中定义，否者部署时会报不是 Servlet 异常，具体路由

 定义可以参考 [sparkjava](http://sparkjava.com/)
```
public class Application implements SparkApplication {
    @Override
    public void init() {
        get("/hello", (req, res) -> "Hello World");
        get("/hello/:name", (request, response) -> {
            return "Hello: " + request.params(":name");
        });
    }
```

### WEB配置文件
在项目目录下创建 WEB-INF/web.xml，也可以在其他位置指定此文件（注意此文件的路径，后续需要用到），文件

内容如下：
```
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	version="3.0">
		<filter>
	    <filter-name>SparkFilter</filter-name>
	    <filter-class>spark.servlet.SparkFilter</filter-class>
	    <init-param>
	        <param-name>applicationClass</param-name>
	        <!-- 此处是路由配置的完整路径名称，此程序配置如下 -->
	        <param-value>teclan.web.Application</param-value>
	    </init-param>
	</filter>
	<filter-mapping>
	    <filter-name>SparkFilter</filter-name>
	    <url-pattern>/*</url-pattern>
	</filter-mapping>
</web-app>
```

### 项目配置
以Maven项目为例，pom.xml配置如下:
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>teclan.web</groupId>
	<artifactId>teclan-web</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<!-- 这里指定打包成 war 包 -->
	<packaging>war</packaging>
	<name>teclan-web</name>
	<url>http://maven.apache.org</url>
	<dependencies>
	   <!-- 这里添加项目的依赖 -->
		<dependency>
			<groupId>com.sparkjava</groupId>
			<artifactId>spark-core</artifactId>
			<version>2.5</version>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.1.3</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-war-plugin</artifactId>
				<version>2.1.1</version>
				<configuration>
				   <!-- 这里指定WEB配置文件，就是上一步的配置文件路径 -->
					<webXml>WEB-INF\web.xml</webXml>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```
### 打包

执行以下命令打包，如果没有错误，在target下就会生成我们的 war 包，为后续使用方便，

将war包改成一个简短的名字，例如 teclan.war。

```
mvn package -Dmaven.test.skip
```

### tomcat 部署

将 teclan.war 包复制到 tomcat 的 webapps 目录下，将原来的其他的东西全部删除，
如果在 linxu 下，在 tomcat 目录下执行
```
chmod +x bin/*sh
```
授予 bin 目录下的脚本执行权限，然后执行
```
./bin/startup.sh
```
即可启动 tomcat ,对于本示例，访问
http://localhost:8080/teclan/hello
即可映射到项目定义的 /hello 路由，返回 "Hello World",
可以执行
```
tail -f logs/catalina.out
```
查看 tomcat实时日志
访问的路由结构为: host:port/war-name/you-route
如果需要更改端口，更改 conf/server.xml中以下片段的端口部分即可```
  <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```
更改端口后可能需要 root 权限方可启动

### jetty 部署

将 teclan.war 包复制到 jetty 的 webapps 目录下，将原来的其他的东西全部删除，
如果在 linxu 下，在 jetty 目录下执行
```
java -jar start.jar
```
即可启动 jetty ,对于本示例，访问
http://localhost:8080/teclan/hello
即可映射到项目定义的 /hello 路由，返回 "Hello World"
如果需要更改端口，在 jetty/start.init 中添加
```
jetty.http.port=更改后的端口
```
访问的路由结构为: host:port/war-name/you-route

更改端口后可能需要 root 权限方可启动
