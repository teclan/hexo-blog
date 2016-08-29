---
title: mysql 中文乱码问题
date: 2016-08-29 16:36:46
tags: [mysql,编码]
---

### 错误详情
```
ERROR 1366 (HY000): Incorrect string value:xxx
```
### 解决方案
#### 重建数据库
```
create database db_name;//此时使用的是默认编码，插入中文会乱码
alter database db_name default character set gb2312 default collate gb2312_chinese_ci;//修改数据库的设置字符集和字符校对规则,之后中文不再乱码
```
#### 建表时指定编码和检验规则，例如
```
create table table_name(
   id int primary key,
   name varchar(100) character set gb2312 collate gb2312_chinese_ci
)default character set gb2312 default collate gb2312_chinese_ci;
```

#### 其他资料
```
# 查看MySQL能够支持的多种字符集：
show character set;

# 查看字符集的校对规则：
show collation;
show collation like 'gb%';

# 每个字符集有一个默认校对规则。
# 创建数据库时设置字符集和字符校对规则：
create database db_name default character set utf8 default collate utf8_general_ci;

# 修改数据库的设置字符集和字符校对规则：
alter database db_name default character set gb2312 default collate gb2312_chinese_ci;

# 修改表的字符集与表的字符校对规则：
alter table table_name default character set utf8 default collate utf8_general_ci;

# 修改表的列的字符集与表的列的字符校对规则：
alter table table_name modify name varchar(100) character set utf8 collate utf8_general_ci;

# 查看表的字符集与字符校对规则：
show create table table_name;

# 查看字符集系统变量：
show variables like 'character_set_%';
# 查看校对规则系统变量：
show variables like 'collation_%';

# 字符集系统变量介绍：
character_set_server：默认的内部操作字符集
character_set_client：客户端来源数据使用的字符集
character_set_connection：连接层字符集
character_set_results：查询结果字符集
character_set_database：当前选中数据库的默认字符集
character_set_system：系统元数据(字段名等)字符集

```
