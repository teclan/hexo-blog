---
title: Mavn打包记录
date: 2016-07-04 11:44:50
tags: [Maven,打包]
---

# 不带依赖打包
配置文件 pox.xml 中添加插件
```bash
<build>
 <plugins>  
   <plugin>
     <groupId>org.apache.maven.plugins</groupId>
     <artifactId>maven-jar-plugin</artifactId>
     <version>2.4</version>
      <configuration>
         <archive>
            <manifest>
              <addClasspath>true</addClasspath>
              <classpathPrefix>lib/</classpathPrefix>
              <mainClass>teclan.maven.Main</mainClass>
            </manifest>
         </archive>
     </configuration>  
	</plugin>  
 </plugins>
</build>  
  ```

  在项目目录下执行以下命令完成打包
  ```bash
  $ mvn package
  ```
  或者
  ```bash
  $ mvn assembly:assembly
  ```
  跳过测试打包执行
  ```bash
  $ mvn package -Dmaven.test.skip=true
  ```

  其中
  ```Java
    <mainClass>teclan.maven.Main</mainClass>  
  ```
  表示默认执行的是 teclan.maven.Main 类下面的 main 方法，执行命令：
  ```bash
  $ java -jar your-jar.jar
  ```


# 带依赖打包
## 1、方式一
   配置文件 pox.xml 中添加插件
```Java
<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-assembly-plugin</artifactId>  
    <version>2.3</version>  
    <configuration>  
        <appendAssemblyId>false</appendAssemblyId>  
        <descriptorRefs>  
            <descriptorRef>jar-with-dependencies</descriptorRef>  
        </descriptorRefs>  
        <archive>  
            <manifest>  
                <mainClass>teclan.maven.Main</mainClass>  
            </manifest>  
        </archive>  
    </configuration>  
    <executions>  
        <execution>  
            <id>make-assembly</id>  
            <phase>package</phase>  
            <goals>  
                <goal>assembly</goal>  
            </goals>  
        </execution>  
    </executions>  
</plugin>  
```
打包命令同上,依赖的包都在打包后的 .jar 文件里面，解压以后可以看到。
## 1、方式二
   配置文件 pox.xml 中添加插件
```Java
<build>  
    <plugins>  
        <!-- The configuration of maven-jar-plugin -->  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-jar-plugin</artifactId>  
            <version>2.4</version>  
            <!-- The configuration of the plugin -->  
            <configuration>  
                <!-- Configuration of the archiver -->  
                <archive>  
                    <!-- 生成的jar中，不要包含pom.xml和pom.properties这两个文件  -->  
                    <addMavenDescriptor>false</addMavenDescriptor>  
                    <!-- Manifest specific configuration -->  
                    <manifest>  
                        <!--是否要把第三方jar放到manifest的classpath中 -->  
                        <addClasspath>true</addClasspath>  
                        <!-- 生成的manifest中classpath的前缀，因为要把第三方jar放到lib目录下，所以classpath的前缀是lib/-->  
                        <classpathPrefix>lib/</classpathPrefix>  
                        <!-- 应用的main class -->  
                        <mainClass>teclan.mavenpackage.App</mainClass>  
                    </manifest>  
                </archive>  
                <!-- 过滤掉不希望包含在jar中的文件-->  
                <excludes>  
                    <exclude>${project.basedir}/xml/*</exclude>  
                </excludes>  
            </configuration>  
        </plugin>  

        <!-- The configuration of maven-assembly-plugin -->  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-assembly-plugin</artifactId>  
            <version>2.4</version>  
            <!-- The configuration of the plugin -->  
            <configuration>  
                <!-- Specifies the configuration file of the assembly plugin -->  
                <descriptors>  
                  <!-- 注意这个文件，要创建正确的文件或指向正确的文件 -->  
                    <descriptor>src/main/assembly/package.xml</descriptor>  
                </descriptors>  
            </configuration>  
            <executions>  
                <execution>  
                    <id>make-assembly</id>  
                    <phase>package</phase>  
                    <goals>  
                        <goal>single</goal>  
                    </goals>  
                </execution>  
            </executions>  
        </plugin>  
    </plugins>  
</build>
```
之后创建src/main/assembly/package.xml，内容如下：
```Java
<assembly>  
    <id>bin</id>  
    <!-- 最终打包成一个用于发布的zip文件 -->  
    <formats>  
        <format>zip</format>  
    </formats>  

    <!-- Adds dependencies to zip package under lib directory -->  
    <dependencySets>  
        <dependencySet>  
            <!--不使用项目的artifact，第三方jar不要解压，打包进zip文件的lib目录-->  
            <useProjectArtifact>false</useProjectArtifact>  
            <outputDirectory>lib</outputDirectory>  
            <unpack>false</unpack>  
        </dependencySet>  
    </dependencySets>  

    <fileSets>  
        <!-- 把项目相关的说明文件，打包进zip文件的根目录 -->  
        <fileSet>  
            <directory>${project.basedir}</directory>  
            <outputDirectory>/</outputDirectory>  
            <includes>  
                <include>README*</include>  
                <include>LICENSE*</include>  
                <include>NOTICE*</include>
                <include>*.xml</include>   
            </includes>  
        </fileSet>  

        <!-- 把项目的配置文件，打包进zip文件的config目录 -->  
        <fileSet>  
            <directory>${project.basedir}/src/main/config</directory>  
            <outputDirectory>config</outputDirectory>  
            <includes>  
                <include>*.xml</include>  
                <include>*.properties</include>  
            </includes>  
        </fileSet>  

        <!-- 把项目的脚本文件目录（ src/main/scripts ）中的启动脚本文件，打包进zip文件的bin目录 -->  
        <fileSet>  
            <directory>${project.build.scriptSourceDirectory}</directory>  
            <outputDirectory>bin</outputDirectory>  
            <includes>  
                <include>startup.*</include>  
            </includes>  
        </fileSet>  

        <!-- 把项目的脚本文件（ src/main/scripts 除了启动脚本文件），打包进zip文件的scripts目录 -->  
        <fileSet>  
            <directory>${project.build.scriptSourceDirectory}</directory>  
            <outputDirectory>scripts</outputDirectory>  
            <excludes>  
                <exclude>startup.*</exclude>  
            </excludes>  
        </fileSet>  

        <!-- 把项目自己编译出来的jar文件，打包进zip文件的根目录 -->  
        <fileSet>  
            <directory>${project.build.directory}</directory>  
            <outputDirectory></outputDirectory>  
            <includes>  
                <include>*.jar</include>  
            </includes>  
        </fileSet>  
    </fileSets>  
</assembly>  
```
打包命令同上，在target目录下面会生成一个.jar和.zip文件，.jar文件是不不带依赖，解压.zip文件可以看到 /lib , /config
/scripts 和一个 .jar 以及项目说明文件，可以根据需要修改配置文件。 
