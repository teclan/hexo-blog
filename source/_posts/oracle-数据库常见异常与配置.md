---
title: oracle 数据库常见异常与配置
date: 2017-06-20 19:37:14
tags: [oracle]
---

### 连接被拒绝

错误报告-
Caused by: java.sql.SQLException: Listener refused the connection with the following error:
ORA-12519, TNS:no appropriate service handler found

解决办法：

1、查看当前连接数
```
select count(*) from v$process;
```
2、查看数据库允许的最大连接数
```
select value from v$parameter where name = 'processes';
```
3、修改最大连接数（重启生效）:
```
alter system set processes = 300 scope = spfile;
```

### 模式空间不足

错误报告 -
ORA-01653: 表 SYSTEM.XNV73 无法通过 8 (在表空间 SYSTEM 中) 扩展
ORA-06512: 在 line 24
01653. 00000 -  "unable to extend table %s.%s by %s in tablespace %s"
*Cause:    Failed to allocate an extent of the required number of blocks for
           a table segment in the tablespace indicated.
*Action:   Use ALTER TABLESPACE ADD DATAFILE statement to add one or more
           files to the tablespace indicated.


解决办法：
1、查看指定模式的磁盘存储路径
```
SELECT FILE_NAME, BYTES FROM DBA_DATA_FILES WHERE TABLESPACE_NAME = 'SYSTEM'; # 形如：// /u01/app/oracle/oradata/XE/system.dbf
```
2、添加磁盘空间（添加的磁盘空间不能任意设置，必须是数据库目录下，建议与默认位置相同）
```
ALTER TABLESPACE SYSTEM ADD DATAFILE '/u01/app/oracle/oradata/XE/system01.dbf' SIZE 500M;
```



### 创建用户与授权

1、创建用户
```
 create user 用户名 identified by 口令;
 ```
 2、权限与角色
 > oracle为兼容以前版本，提供三种标准角色（role）:connect/resource和dba。
 三种标准角色：
      1). connect role(连接角色)
      临时用户，特指不需要建表的用户，通常只赋予他们connect role.connect是使用oracle简单权限，这种权限只对其他用户的表有访问权限，
      包括select/insert/update和delete等。拥有connect role 的用户还能够创建表、视图、序列（sequence）、簇（cluster）、
      同义词(synonym)、回话（session）和其他  数据的链（link）。
      2). resource role(资源角色)
      更可靠和正式的数据库用户可以授予resource role。resource提供给用户另外的权限以创建他们自己的表、序列、过程(procedure)、触发器(trigger)、索引(index)和簇(cluster)。
      3). dba role(数据库管理员角色)
      dba role拥有所有的系统权限，包括无限制的空间限额和给其他用户授予各种权限的能力。system由dba用户拥有。
      此外，用户还可以在oracle创建自己的role。用户创建的role可以由表或系统权限或两者的组合构成。为了创建role，用户必须具有create role系统权限。

2.1、授权
```
 grant connect, resource to 用户名;
```
2.2、撤销权限

```
 revoke connect, resource from 用户名;
```
2.3、创建角色
```
 create role 角色名;
 ```
2.4、授权角色
```
grant select on 表名 to 角色名;
```
例如：
```
 grant select on 表名 to testRole; # 现在，拥有testRole角色的所有用户都具有对 [表名] 的select查询权限
 ```
2.5、删除角色
```
 drop role 角色名;
 ```
