---
title: oracle学习笔记
date: 2016-10-09 13:41:12
tags: [oracle]
---

##### 连接数据库
```
# 使用操作系统认证连接数据库，无需密码
>sqlplus "/as sysdba"
```
##### 查看数据库动态表实例
若查询结果为 OPEN 则说明数据库运行正常。oracle动态表通常以v$开头，动态表不允许手工更改，动态表属于字典表
```
 select status from v$instance;
```
##### 关闭和启动数据库
```
# 关闭数据库
 shutdown immediate
# 启动数据库
 startup
```
##### 查看当前数据库名称
```
 show parameter db_name
```
##### 查询某个用户的状态
字典表 dba_users 存放所有用户的信息，oracle中所有英文字母不区分大小写，但如果是在代码中，建议都用大写字母
```
# 查询用户 scott 的状态
 select username,account_status from dba_users where username='scott'
```
##### 查看表有哪些列
```
# 查看表 user_tables 有哪些列
 desc user_tables;
```
##### 解锁用户
```
# 解锁 SCOTT
 alter user scott account unlock;
# 验证是否已经解锁,如果结果提示账户到期，那么使用旧密码登录后并设置新密码即可解除到期状态
 select username,account_status from dba_users where username='scott'
```
##### 查看数据库用户
```
 show user
```
##### 使用普通用户登陆数据库
```
# 使用scott登录,默认密码tiger
 sqlplus scott/tiger
# 如果已经使用其他用户连接数据库，则可以使用以下命令切换至scott
 conn  scott/tiger
# 如果账户到期，提示 the password has expired,需要重新设置密码,假设设置新口令为 cat
新口令:cat
重新键入新口令:cat
# 之后会提示口令已更改，并已经连接数据库
# 验证当前用户
 show user
```
##### 更改会话时间格式
```
# 更改会话时间格式为 YYYY-MM-DD，默认格式是美国时间格式
 alter session set nls_date_format='YYYY-MM-DD';
```
##### 查询数据库当前时间
dual 是oracle中的虚表，设计系统常量，计算等时可以使用dual表
```
 select sysdate from dual;
```
##### 把表数据复制到一张新表
> 这种方式只是单纯的复制数据，表的约束并没有复制过去

```
# 创建一张新表 business_copy,并且将 business 表中的全部数据复制到 business_copy
 create table business_copy as select * from business;
```
##### 从一个表将数据导入另一个表
> 要求两个表的字段，顺序要一致

```
# business_copy 的数据倒入 business
 insert into business (colume1,colume2,...,columen) select * from business_copy;
```
##### 添加表字段
```
# 给表 business 添加字段 manager
 alter table business add (manager varchar2(50));
```
##### 修改表字段
```
# 给表 business 字段 manager 最大长度为 100
 alter table business modify (manager varchar2(100));
```
##### 删除表字段
```
# 删除表 business 字段 manager
 alter table business drop column manager;
```
##### 创建数据库用户
```
create user teclan identified by 123456;
```
##### 授权连接权限
```
# 赋予用户 teclan 连接数据库权限
grant connect to teclan;
```
##### 授权查询权限
```
# 赋予 teclan 对 business 表查询权限，假设 business 属于scott,即使 teclan
# 有查询权限，在查询时也必须指定表的schema SCOTT
grant select on business to teclan
```
##### 取消查询权限
```
# 取消 teclan 对 business 表的查询权限,该命令由管理员或授权者执行
revoke select on business to teclan
```
##### 多权限授予
```
# 赋予 teclan 对 business 表查询,插入，删除，更新权限
grant select,insert,delete,update on business to teclan;
```
##### 删除用户
```
# 删除用户 teclan
drop user teclan;
# 删除用户 teclan 以及关联的表
drop user teclan cascade;
```
##### 查看当前用户权限
```
select * from session_privs;
```
##### 查看当前用户角色
```
select * from user_role_prives;
```

##### 函数示例
函数一般用于数据查询，分析，处理
```
CREATE OR REPLACE get_sal(emp_no IN NUMBER)
RETURN NUMBER
LS  emp_sal NUMBER(7,2)
BEGIN
  SELECT sal INTO emp_sal FROM emp WHERE empno = emp_no;
  RETURN(emp_sal);
END;
```
##### 调用函数
```
select get_sal(10086) from dual;
```
##### 存储过程示例
存储过程一般是要执行的SQL组合
```
create or replace procedure deleteInfo(empid in number)
begin
  delete from emp where emp.id = empid;
  commit;
end deleteInfo;
```
##### 调用存储过程
```
execute deleteInfo(1352);
```
##### 创建索引
```
create index id_business on business(id);
```
##### 同义词
同义词（Synonym），即别名，分私有别名和公共别名，如访问表必须加上用户名，可以使用别名来解决

```
# 查询是否具有创建别名的权限,查询结果为空则表示无权限
select * from session_privs where privilege like '%SYNONYM%';
# 如果没有权限，赋予 teclan 创建别名权限(私有)，需要管理员权限
grant create any synonym to teclan

# 创建别名(私有)
create synonym bs for business;
# 通过别名查询
select * from bs;

# 如果没有权限，赋予 teclan 创建别名权限(公共)，需要管理员权限
grant create public synonym to teclan

# 创建别名（公共）
create public synonym busin for teclab.business
# 通过别名查询,当前用户不是teclan的情况
select * from busin;
```
##### 查询表空间信息
```
select tablespace_name as "表空间名称"，
       block_size/1024 as "数据块存储大小（KB）",
       satatus         as "表空间的状态",
       contents        as "表空间类型",
       logging         as "是否有日志记录",
from dba_tablespace;
```
##### 查看数据说明文件
```
select t.tablespace_name as "表空间名称",
       t.file_name       as "数据文件路径",
       t.bytes           as "数据文件大小（MB）",
       t.autoextendsible as "数据文件是否自动扩展",
       t.maxbytes        as "数据文件最大（MB）",
from dba_data_files t;
```
##### 查看重做日志
```
# STATUS：STALE为已经提交到数据中，空白为正在使用该文件
select * from v$logfile;
```
##### 恢复刚才受影响的一条数据
```
# 假设先执行如下sql，删除一条记录
delete from emp where ename='FORD';
# 以 SYS 登录数据库，或者对 flashback_transaction_query 有查询权限的用户登录，该表属于 SYS
# undo_sql 即为恢复的sql，将其复制到到命令窗口执行即可
select t.start_timestamp,
       t.commit_timestamp,
       t.logon_user,
       t.operation,
       t.table_name,
       t.table_owner,
       t.undo_sql
from flashback_transaction_query where t.table_name = 'EMP';
```
##### 恢复被大量受影响的记录
```
# 假设 emp 表有100万条记录，执行了 update emp set job = 'analyst';
# 如果按照恢复一条数据的做法，那么将需要操作100万次，这个方法至少手工是不可能的
# 我们假设 update emp set job = 'analyst'; 是在10分钟之前执行的
# 先查看当前系统时间,假设当前时间为 2016-10-11 14：20：00
select sysdate from dual;

# 查询 emp 表 10分钟前的数据,如果时间确定正确，查询到的数据就应该是执行 update emp set job = 'analyst'; 的数据
select * from emp as of timestamp to_timestamp('2016-10-11 14：10：00','yyyy-mm-dd hh24:mi:ss');

# 使用数据库的闪回功能恢复数据
# 更改 emp 表的行好
alter table emp enable row movement；
# 恢复到10分钟前的数据
flasshback table emp to timestamp to_timestamp('2016-10-11 14：10：00','yyyy-mm-dd hh24:mi:ss');
```
##### 恢复删除的表
```
# 删除 emp表（可以是非空表）
drop table emp;

# 查看回收站中是否存在 emp表，一般情况下可以省略此操作
select * from user_recyclebin order by droptime desc;
或
select * froom recyclebin order by droptime desc;

# 使用闪回恢复表
flashback table emp to before drop;
```































```
