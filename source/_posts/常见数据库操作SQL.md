---
title: 常见数据库操作SQL
date: 2016-07-15 13:48:40
tags: [Oracle,达梦,sqlserver2k,sqlserver2k+,DB2,mysql,kingbase]
---

> 操作表，序列，触发器等时最好都加上schema修饰，以下部分sql中为清晰起见，省略schema修饰。
  以下 triggerNameForInsert，triggerNameForUpdatet,triggerNameForDelete 均指代
  触发器名，并且包含schema修饰。

# Oracle
>owner 是对应的schema，要大写,url中的参数依次是ip,端口，数据库名，以下所有SQL是根据本人用到的做的记录，需要时请自行调整更改。

## Jdbc驱动
```
oracle.jdbc.driver.OracleDriver
```
## url
```
jdbc:oracle:thin:@%s:%d:%s
```
## 查询表主键
```
SELECT column_name FROM user_cons_columns WHERE constraint_name = (
SELECT constraint_name FROM user_constraints WHERE table_name= '%s'
AND constraint_type ='P')；
```
## 查询表字段类型
```
select column_name, data_type from all_tab_columns where owner = '%s' and table_name = '%s'；
 ```
## 获取所有表
```
select table_name from all_tables where owner=upper('%s')；
```
## 查询表是否存在
```
SELECT 1 FROM user_tables WHERE table_name='%s'；
```
## 查询序列是否存在
```
select SEQUENCE_NAME from user_sequences where SEQUENCE_NAME = '%s'；
```
## 创建序列
```
CREATE SEQUENCE \"%s\".\"%s\" INCREMENT BY 1 START WITH 0 MINVALUE 0 NOMAXVALUE；
```
## 删除序列
```
DROP SEQUENCE %s.%s；
```
## 创建表
```
CREATE TABLE %s.%s (
  id              NUMBER          NOT NULL PRIMARY KEY,
  dbName          VARCHAR2(50)    NOT NULL,
  tableName       VARCHAR2(50)    NOT NULL,
  pkNames         VARCHAR2(2000)  NOT NULL,
  newPkValues     VARCHAR2(2000),
  oldPkValues     VARCHAR2(2000),
  action          VARCHAR2(20)    NOT NULL
  )；
```
## 查询表上是否存在某个触发器
```
SELECT trigger_name FROM all_triggers WHERE owner='%s' AND table_name='%s' AND trigger_name='%s'；
```
## 查询外键
```
select ucc.constraint_name from user_cons_columns ucc
join user_constraints uc on ucc.owner=uc.owner and ucc.constraint_name= uc.constraint_name
join user_constraints uc_pk ON uc.r_owner= uc_pk.owner and uc.r_constraint_name=uc_pk.constraint_name
where uc.constraint_type='R' and ucc.table_name='%s'；
```
## 关闭外键约束
```
alter table %s.%s disable constraint %s；#最后一个参数是某个外键约束
```
## 启用外键约束
```
alter table %s.%s enable constraint %s；#最后一个参数是某个外键约束
```

## 触发器
设1.10创建的表记为 events,用来记录没一条记录的变化，events_seq是存在的一个序列，设有表teclan
```
create table teclan
(
  id number,
  name varchar(10),
  age number,
  sex varchar(10)
);
alter table taclan add  primary key(id,name);
```
在表teclan上创建触发器，用来记录所有记录的变化，信息存到events表。
### insert触发器
```
CREATE OR REPLACE TRIGGER triggerNameForInsert  #triggerNameForInsert为触发器名，自定义
AFTER INSERT ON teclan
FOR EACH ROW
BEGIN
INSERT INTO events VALUES (events_seq.nextval ,'xe','teclan','ID,NAME',(:NEW."ID"||','||:NEW."NAME"),
(:OLD."ID"||','||:OLD."NAME"),'INSERT');
END;
```
### update触发器
```
CREATE OR REPLACE TRIGGER triggerNameForUpdatet
AFTER UPDATE ON teclan
FOR EACH ROW
BEGIN
INSERT INTO events VALUES (events_seq.nextval ,'xe','teclan','ID,NAME',(:NEW."ID"||','||:NEW."NAME"),
(:OLD."ID"||','||:OLD."NAME"),'UPDATE');
END;
```
### delete触发器
```
CREATE OR REPLACE TRIGGER triggerNameForDelete
AFTER DELETE ON teclan
FOR EACH ROW
BEGIN INSERT INTO events VALUES (events_seq.nextval ,'xe','teclan','ID,NAME',(:NEW."ID"||','||:NEW."NAME"),
(:OLD."ID"||','||:OLD."NAME"),'DELETE');
END;
```
### 删除触发器
```
drop trigger triggerName; #triggerName为指定要删除的触发器
```
## 查询数据库中各个表的记录数
### 创建存储过程
```
create or replace function count_rows(table_name in varchar2,owner in varchar2 default null)
return number
authid current_user
IS
   num_rows number;
   stmt varchar2(2000);
begin
   if owner is null then
      stmt := 'select count(*) from "'||table_name||'"';
   else
      stmt := 'select count(*) from "'||owner||'"."'||table_name||'"';
   end if;
   execute immediate stmt into num_rows;
   return num_rows;
end;
```
### 查询
```
select table_name, count_rows(table_name)  nrows from user_tables order by nrows desc;
```


# 达梦数据库
  除以下特别指出外，其余参考Oracle

  > sysobjects.object_name 为库模式，user_constraints.table_name 为表名

## 查询主键
```
SELECT column_name FROM user_cons_columns WHERE constraint_name = (select sysobjects.name from
sysobjects ,all_objects,user_constraints where object_type='SCH' and object_name = '%s'
and sysobjects.schid = all_objects.object_id  and type$='TABOBJ' and sysobjects.subtype$='CONS'
and user_constraints.table_name= '%s' AND user_constraints.constraint_type ='P'
and user_constraints.constraint_name = sysobjects.name)
```
## 查询所有表
```
select sysobjects.name from sysobjects,all_objects where object_type='SCH' and object_name = '%s'
and sysobjects.schid = all_objects.object_id and sysobjects.type$='SCHOBJ' and sysobjects.subtype$='UTAB'
```


# Sqlserver2k
> o.name 指定表名,sqlserver默认的schema是dbo

## Jdbc驱动
```
com.microsoft.jdbc.sqlserver.SQLServerDriver
```
## url
```
jdbc:microsoft:sqlserver://%s:%d;DatabaseName=%s
```
## 查询主键
```
SELECT c.name AS name FROM sysobjects o INNER JOIN sysindexes x ON o.id = x.id INNER
JOIN syscolumns c ON o.id = c.id INNER
JOIN sysindexkeys xk ON o.id = xk.id
AND x.indid = xk.indid AND c.colid = xk.colid
AND x.keycnt >= xk.keyno WHERE o.name = '%s' AND (o.type IN ('U')) AND (x.status & 32 = 0)
AND (CONVERT(bit, (x.status & 0x800) / 0x800) = 1);
```
## 查询表字段类型
```
select o.name as table_name, c.name as column_name, t.name as data_type
from syscolumns c, sysobjects o, systypes t where o.name = '%s' and c.id = o.id and c.xtype = t.xtype；
```
## 获取所有表
```
select table_name from information_schema.tables where table_type='base table' and table_schema='%s'";
```
## 查询表是否存在
```
SELECT name FROM dbo.sysobjects WHERE name='%s'；
```
## 创建表
```
CREATE TABLE %s.%s
(
id INTEGER IDENTITY(1,1) NOT NULL PRIMARY KEY, #自增字段
dbName     VARCHAR(30),
tableName  VARCHAR(30),
pkNames    VARCHAR(2000),
newPkValues   VARCHAR(2000),
oldPkValues   VARCHAR(2000),
action     VARCHAR(20)
)；
```
## 查询外键
```
select distinct a.table_name, b.column_name,b.constraint_name,object_name(rkeyid) r_table_name  
from information_schema.table_constraints a, information_schema.constraint_column_usage b, sysforeignkeys c
where a.constraint_type='foreign key' and a.constraint_name= b.constraint_name and object_name(fkeyid)=a.table_name
and object_name(constid)=a.constraint_name and a.table_name='%s'；
```
## 关闭外键约束
```
alter table %s nocheck constraint %s；#最后一个参数为某个外键约束
```
## 启用为外键约束
```
alter table %s check constraint %s;#最后一个参数为某个外键约束
```
## 查询表上面是否存在某个触发器
```
SELECT name FROM dbo.sysobjects WHERE type='TR' AND name='%s'；
```
## 创建触发器
设3.7创建的表为events，且有表teclan如下：
```
create table teclan
(
id int,
name varchar(10),
age int,
sex varchar(10)
);
alter table teclan add primary key(id,name);
```
### insert触发器
```
CREATE TRIGGER triggerNameForInsert
ON dbo.teclan
FOR INSERT
AS
DECLARE
@newid VARCHAR(20),@oldid VARCHAR(20),
@newname VARCHAR(20),@oldname VARCHAR(20)
BEGIN
select @newid=CONVERT(VARCHAR(200),id) from inserted
select @oldid=CONVERT(VARCHAR(200),id) from deleted
select @newname=CONVERT(VARCHAR(200),name) from inserted
select @oldname=CONVERT(VARCHAR(200),name) from deleted  
INSERT INTO dbo.events(dbName, tableName, pkNames, newPkValues, oldPkValues, action)
VALUES ('testdb', 'teclan', 'id,name', @newid+','+@newname, @oldid+','+@oldname , 'INSERT')
END;
```
### update触发器
```
CREATE TRIGGER triggerNameForUpdatet
ON dbo.teclan
FOR UPDATE
AS
DECLARE
@newid VARCHAR(20),@oldid VARCHAR(20),
@newname VARCHAR(20),@oldname VARCHAR(20)
BEGIN
select @newid=CONVERT(VARCHAR(200),id) from inserted
select @oldid=CONVERT(VARCHAR(200),id) from deleted
select @newname=CONVERT(VARCHAR(200),name) from inserted
select @oldname=CONVERT(VARCHAR(200),name) from deleted  
INSERT INTO dbo.events(dbName, tableName, pkNames, newPkValues, oldPkValues, action)
VALUES ('testdb', 'teclan', 'id,name', @newid+','+@newname, @oldid+','+@oldname , 'UPDATE')
END;
```
### delete触发器
>关于sqlserver，delete操作如果按insert和update方式创建触发器，那么在执行delete from table 删除表全部数据
或者带条件删除某个区间时，触发器只能记录到最后一条数据，为解决这个问题，需要重构触发器，以下是我的方案：
当delete操作发生后，我们判断数据库中是否存在 {表名_delete_tmp} 这样一个表，如果存在则将其删掉，之后使用
select pknames into {表名_delete_tmp} from deleted,将delete表中的主键值复制到  {表名_delete_tmp}，之后再
遍历 {表名_delete_tmp} ，依次获取对应的主键值，再存入events表。
特别注意，在inserted和deleted表中不允许使用text,ntext和image这三个字段，意味着如果表中存在这三个字段中的一个
或多个字段作为主键时，这个触发器就会无法创建，建议使用varchar（max）代替text和ntext，使用varbinary代替image，微软建议
尽量不使用text,ntext和image这三个字段。

```
create trigger triggerNameForDelete
on dbo.teclan
after DELETE
as
DECLARE
@i int set @i=(select count(table_name) from information_schema.tables where table_type='base table' and table_schema='dbo' and table_name='teclan_delete_tmp')
if @i=1
  drop table teclan_delete_tmp ;
select id,name into teclan_delete_tmp from deleted;
declare @rows int  
declare @id varchar(500)
declare @name varchar(500)
set @rows=(select count(*) from teclan_delete_tmp)
while @rows>0
begin  
  set @id=(select top 1 id from teclan_delete_tmp)  
  set @name=(select top 1 name from teclan_delete_tmp)  
  insert into dbo.events (dbName,tableName,pkNames,newPkValues,oldPkValues,action)
  values ('testdb','teclan','id,name','null',@id+','+@name,'DELETE');
  delete from teclan_delete_tmp where  id=@id and  name=@name  
  set @rows=(select count(*) from teclan_delete_tmp)
end;
drop table teclan_delete_tmp
```
### 删除触发器
```
drop trigger triggerName;#triggerName是要删除的触发器名
```

# sqlserver2k+
> table_catalog 对应的数据库名，object_id('%s') 对应参数是表名，
除以下特别指定的，其他SQL参考sqlserver2k。

## Jdbc驱动
```
com.microsoft.sqlserver.jdbc.SQLServerDriver
```
## url
```
jdbc:sqlserver://%s:%d;DatabaseName=%s
```
## 获取所有表
```
select name from sys.tables;
```
## 查询主键
```
select name from syscolumns where id=object_id('%s') and colid in(select keyno from sysindexkeys where id=object_id('%s'));
```
或
```
SELECT a.column_name FROM information_schema.key_column_usage a
INNER JOIN information_schema.table_constraints b
ON (a.constraint_name = b.constraint_name)
WHERE a.table_schema = '%s' AND b.table_schema = '%s'
AND b.table_catalog = '%s' AND b.table_name = '%s'
AND b.constraint_type = 'PRIMARY KEY';
```
## 查询触发器是否存在
```
# b.name 对应触发器名，a.name 对应schema
SELECT b.name FROM sys.schemas a INNER JOIN sys.objects b ON a.schema_id=b.schema_id WHERE b.type='TR' AND b.name='%s' AND a.name='%s';
```
## 查询外键
```
# oSub.name 对应表名
select oSub.name as table_name, fk.name as constraint_name, SubCol.name as column_name, oMain.name as r_table_name,
MainCol.name as r_column_name from sys.foreign_keys fk
join sys.all_objects oSub  on (fk.parent_object_id = oSub.object_id)
join sys.all_objects oMain on (fk.referenced_object_id = oMain.object_id)
join sys.foreign_key_columns fkCols  on (fk.object_id = fkCols.constraint_object_id)
join sys.columns SubCol ON (oSub.object_id = SubCol.object_id and fkCols.parent_column_id = SubCol.column_id)
join sys.columns MainCol on (oMain.object_id = MainCol.object_id and fkCols.referenced_column_id = MainCol.column_id)
where oSub.name='%s';
```
# DB2
## Jdbc驱动
```
COM.ibm.db2os390.sqlj.jdbc.DB2SQLJDriver
```
## url
```
jdbc:db2://%s:%s/%s
```
## 查询主键
```
SELECT a.colname FROM SYSCAT.keycoluse AS a INNER JOIN SYSCAT.tabconst AS b ON (a.constname = b.constname)
WHERE a.tabschema = '%s' AND b.tabschema = '%s' AND a.tabname = '%s' AND b.tabname = '%s' AND b.type = 'P';
```
## 查询字段类型
```
select column_name, data_type from sysibm.columns where table_name = upper('%s')；
```
## 获取所有表
```
# creator 对应schema
SELECT name FROM SYSIBM.SYSTABLES WHERE creator='%s' ORDER BY name；
```
## 查询表是否存在
```
# tabschema 对应schema,tabname 对应表名
SELECT tabname FROM SYSCAT.tables WHERE tabschema='%s' AND tabname='%s';
```
## 创建表
```
CREATE TABLE %s.%s
(
  id INTEGER  NOT NULL GENERATED ALWAYS AS IDENTITY (START WITH 0, INCREMENT BY 1, NO CACHE ) ,#严格自增字段
dbName VARCHAR(20),
tableName VARCHAR(20),  
pkNames VARCHAR(255),
newPkValues VARCHAR(255),
oldPkValues VARCHAR(255),
action VARCHAR(20),
PRIMARY KEY (id)
);
```
## 查询外键
```
select r.reftabname as table_name, k.colname as column_name, r.constname as constraint_name
from syscat.references as r,syscat.keycoluse as k
where r.tabname=k.tabname and k.constname=r.constname and r.tanschema=’%s’ and r.reftabname='%s';
```
## 关闭外键约束
```
alter table %s alter foreign key %s not enforced；#最后一个参数是某个外键约束
```
## 启用外键约束
```
alter table %s alter foreign key %s enforced；#最后一个参数是某个外键约束
```
## 查询触发器是否存在
```
# trigschema 对应 schema, trigevent 对应触发器名
SELECT trigname FROM SYSCAT.triggers WHERE trigschema='%s' AND trigname='%s' AND trigevent ='%s'；
```
## 触发器
> DB2 触发器名字不能超过18个字符,触发器SQL最后的 “END” 后面不加分号，activejdbc中加了分号会报错，其他工具请
自己测试调整。特别注意：
1:如果表中自增列使用的是 GENERATED ALWAYS AS IDENTITY (START WITH 0, INCREMENT BY 1,
  NO CACHE)声明,则数据库将严格保证该列满足自增约束(唯一性),用户不可指定该列的值,无法插入数据。
2:如果表中自增列使用的是 GENERATED BY DEFAULT AS IDENTITY (START WITH 0, INCREMENT
  BY 1, NO CACHE)声明,则允许指定此列的值。

设5.7创建的表为EVENTS，且有表teclan如下（DEV为schema）：
```
create table teclan
(
id bigint,
name integer,
age bigint,
sex clob
);
alter table teclan add primary key(id,name);
```
### insert触发器
```
CREATE TRIGGER triggerNameForInsert
AFTER INSERT ON DEV.TECLAN
REFERENCING  NEW AS N  FOR EACH ROW MODE DB2SQL
BEGIN ATOMIC
INSERT INTO DEV.EVENTS (dbName, tableName, pkNames, newPkValues, oldPkValues, action) VALUES ('DB2', 'TECLAN', 'ID,NAME', CHAR(N.ID)||','||CHAR(N.NAME), null,'INSERT');
END
```
### update触发器
```
CREATE TRIGGER triggerNameForUpdatet
AFTER UPDATE ON DEV.TECLAN
REFERENCING  NEW AS N OLD AS O  
FOR EACH ROW MODE DB2SQL BEGIN ATOMIC
INSERT INTO DEV.EVENTS (dbName, tableName, pkNames, newPkValues, oldPkValues, action) VALUES ('DB2', 'TECLAN', 'ID,NAME', CHAR(N.ID)||','||CHAR(N.NAME), CHAR(O.ID)||','||CHAR(O.NAME),'UPDATE');
END
```
### delete触发器
```
CREATE TRIGGER triggerNameForDelete
AFTER DELETE ON DEV.TECLAN
REFERENCING  OLD AS O  FOR EACH ROW MODE DB2SQL
BEGIN ATOMIC INSERT INTO DEV.EVENTS (dbName, tableName, pkNames, newPkValues, oldPkValues, action) VALUES ('DB2', 'TECLAN', 'ID,NAME', null, CHAR(O.ID)||','||CHAR(O.NAME),'DELETE');
END
```
### 删除触发器
```
drop  trigger triggerName;#triggerName 为要删除的触发器名
```
# mysql
## Jdbc驱动
```
com.mysql.jdbc.Driver
```
## url
```
jdbc:mysql://%s:%d/%s
```
## 获取所有表
```
select table_name from information_schema.tables where table_schema  ='%s';
```
## 查询表是否存在
```
SELECT TABLE_NAME AS name FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_NAME='%s';
```
## 查询表字段类型
```
select column_name,data_type from information_schema.columns where table_schema='%s' and table_name='%s';
```
## 查询主键
```
select concat(c.column_name) as 'column_name' from information_schema.table_constraints as t,
information_schema.key_column_usage as c where t.table_name=c.table_name and t.table_name='%s'
and t.table_schema='%s'；
```
## 创建表
```
CREATE TABLE %s.%s
(
  id         BIGINT        NOT NULL PRIMARY KEY     AUTO_INCREMENT,#自增字段
  dbName     VARCHAR(30),
  tableName  VARCHAR(30),
  pkNames    VARCHAR(2000),
  newPkValues   VARCHAR(2000),
  oldPkValues   VARCHAR(2000),
  action     VARCHAR(20)
  );
```
## 关闭外键约束
```
set foreign_key_checks=0;
```
## 启用外键约束
```
set foreign_key_checks=1;
```
## 查询触发器是否存在
```
# EVENT_OBJECT_TABLE 对应表名
SELECT TRIGGER_NAME FROM INFORMATION_SCHEMA.TRIGGERS WHERE TRIGGER_SCHEMA='%s'
AND EVENT_OBJECT_TABLE='%s' AND TRIGGER_NAME='%s'
```
## 触发器
设6.7创建的表为events,且有表teclan如下,schema与数据库名均为testdb:
```
create table teclan
(
id int,
name varchar(10),
age int,
sex varchar(10)  
);
alter table teclan add primary key(id,name);
```
### insert触发器
```
CREATE TRIGGER triggerNameForInsert
AFTER INSERT ON testdb.teclan
FOR EACH ROW
INSERT INTO testdb.events (dbName,tableName,pkNames,newPkValues,oldPkValues,action) VALUES('testdb','teclan','id,name',(concat_ws(',',new.id,new.name)),(null),'INSERT');
```
### update触发器
```
CREATE TRIGGER triggerNameForUpdatet
AFTER UPDATE ON testdb.teclan
FOR EACH ROW
INSERT INTO testdb.events (dbName,tableName,pkNames,newPkValues,oldPkValues,action) VALUES('testdb','teclan','id,name',(concat_ws(',',new.id,new.name)),(concat_ws(',',old.id,old.name)),'UPDATE');
```
### delete触发器
```
CREATE TRIGGER triggerNameForDelete
AFTER DELETE ON testdb.teclan
FOR EACH ROW
INSERT INTO testdb.events (dbName,tableName,pkNames,newPkValues,oldPkValues,action) VALUES('testdb','teclan','id,name',(null),(concat_ws(',',old.id,old.name)),'DELETE');
```
### 删除触发器
```
drop  trigger triggerName;#triggerName为要删除的触发器
```
# kingbase
## Jdbc驱动
```
jdbc:kingbase://%s:%d/%s
```
## url
```
com.kingbase.Driver
```
## 获取所有表
```
SELECT table_name FROM information_schema.tables WHERE table_schema='%s';
```
## 查询表是否存在
```
SELECT table_name FROM information_schema.tables WHERE table_schema='%s' AND table_name='%s';
```
## 查询表字段类型
```
select column_name, data_type from information_schema.columns where table_schema = '%s' and table_name = '%s'
```
## 创建序列
```
CREATE SEQUENCE %s.%s START 1 INCREMENT 1;
```
## 删除序列
```
drop SEQUENCE %s.%s;
```
## 创建表
```
CREATE TABLE %s.%s
(
  id INT DEFAULT NEXTVAL('%s.%s'),
  dbName VARCHAR(20),
  tableName VARCHAR(20),
  pkNames VARCHAR(255),
  newPkValues VARCHAR(255),
  oldPkValues VARCHAR(255),
  action VARCHAR(20),
  PRIMARY KEY(id)
);
```
## 查询主键
```
# table_catalog 对应数据库名
SELECT a.column_name FROM information_schema.key_column_usage a
INNER JOIN information_schema.table_constraints b
ON (a.constraint_name = b.constraint_name)
WHERE a.table_schema = '%s' AND b.table_schema = '%s'
AND b.table_catalog = '%s' AND b.table_name = '%s'
AND b.constraint_type = 'PRIMARY KEY';
```
## 查询外键
```
# nspname 对应schema
select sys_class.relname as table_name,sys_attribute.attname as column name,fk.conname as
constraint_name form (select unnest(sys_constraint.conkey),conname,sys_constraint.conrelid,
sys_constraint.confrelid form sys_constraint,sys_namespace where sys_constraint.connamespace
=sys_namespace.oid and sys_namespace.nspname=’%s’ and contype=’f’ )fk,sys_attribute,sys_class,all_constraints
where fk.unnest=sys_attribute.attnum and fk.conrelid=sys_attribute.attrelid and
fk.confrelid=sys_class.oid and fk.conname=all_constraints.constaint_name and sys_class.relname='%s';
```
## 关闭外键约束
```
alter table %S alter foreign key %s not enforced;#最后一个参数为某个外键约束
```
## 启用外键约束
```
alter table %s alter foreign key %s enforced;
```
## 触发器
设7.8创建的表为events,且有表teclan如下("PUBLIC" 为schema,"TECLAN" 为表名)：
```
create table teclan
(
id int,
name varchar(10),
age int,
sex varchar(10)
);
alter table teclan add primay key(id,name);
```
### insert触发器
```
CREATE OR REPLACE TRIGGER triggerNameForInsert
AFTER INSERT ON PUBLIC."TECLAN"
FOR EACH ROW AS
BEGIN
INSERT INTO PUBLIC.EVENTS (dbName, tableName, pkNames, newPkValues, oldPkValues,action)
VALUES ('TECLAN','TECLAN','ID,NAME',(NEW."ID"||','||NEW."NAME"),(null),'INSERT');
END;
```
### update触发器
```
CREATE OR REPLACE TRIGGER PIXIU_SYNC_TECLAN_UPDATE
AFTER UPDATE ON PUBLIC."TECLAN"
FOR EACH ROW AS
BEGIN
INSERT INTO PUBLIC.EVENTS (dbName, tableName, pkNames, newPkValues, oldPkValues,action)
VALUES ('TECLAN','TECLAN','ID,NAME',(NEW."ID"||','||NEW."NAME"),(OLD."ID"||','||OLD."NAME"),'UPDATE');
END;
```
### delete触发器
```
CREATE OR REPLACE TRIGGER PIXIU_SYNC_TECLAN_DELETE
AFTER DELETE ON PUBLIC."TECLAN"
FOR EACH ROW AS
BEGIN I
NSERT INTO PUBLIC.EVENTS (dbName, tableName, pkNames, newPkValues, oldPkValues,action)
VALUES ('TECLAN','TECLAN','ID,NAME',(null),(OLD."ID"||','||OLD."NAME"),'DELETE');
END;
```
### 删除触发器
```
drop trigger triggerName; #triggerName 为指定删除的触发器
```
