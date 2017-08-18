---
title: DB2常见错误与解决方案
date: 2017-08-18 21:50:51
tags: [DB2]
---
## windows db2 命令行工具使用

>在控制台中输入 db2cmd 即可启动 db2 命令行工具。

### 连接数据库
```
db2 connect to database_name user user_name using your_password
```
### 执行脚本
```
db2 -td@ -vf path_to_you_sql_file
```
其中 @ 表示脚本文件的语句结束符为 @，因为 SQL 脚本中默认的语句是分号（;），如果
不重新指定的话，在执行到第一分号的时候就不往下执行，因为，在SQL脚本文件中的最后一
个字符必须是 @。v表示将执行的脚本内容在终端中输出，f表示从文件读取SQL，后面的参数
是SQL脚本的绝对路径。

### 调用存储过程
```
db2 call procedure_name  # 存储过程不带参数可以直接写存储过程名，不用带括号
```

## SQLCODE=-964日志文件满的问题

```
# 先连接数据库，依次执行以下命令即可
db2 update db cfg for MY_DATABASE using LOGFILSIZ 7900  
db2 update db cfg for MY_DATABASE using LOGPRIMARY 30  
db2 update db cfg for MY_DATABASE using LOGSECOND 20
db2 "force application all"
db2stop  
db2start
# 重启结束后继续后续操作
```
