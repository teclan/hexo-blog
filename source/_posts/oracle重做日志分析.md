---
title: oracle重做日志分析
date: 2017-08-18 22:00:04
tags: [oracle,日志]
---
## 日志文件的块大小查询

日志文件的块大小可以通过如下方法来查询：
```
select distinct block_size from v$archived_log;
```

## 清理脏块

将所有的脏块给清到磁盘上
```
alter system flush buffer_cache
```

## 查询日志分组信息

```
select * from v$log;
```

## 查询当前日志分组

```
select * from v$log where status='CURREN';
```

## 创建日志分析存储过程以及查询常见信息

```
# 加载字典至文件 /u01/app/logminer/dictionary.ora，需要目录和文件的所有者所数组是 oracle：dba
exec dbms_logmnr_d.build( 'dictionary.ora', '/u01/app/logminer'); # 可选

# 加载日志
exec dbms_logmnr.add_logfile('/usr/lib/oracle/xe/app/oracle/flash_recovery_area/XE/onlinelog/o1_mf_1_djfw9s0s_.log', dbms_logmnr.NEW);

# 再加载日志，可选
exec dbms_logmnr.add_logfile('/usr/lib/oracle/xe/app/oracle/flash_recovery_area/XE/onlinelog/o1_mf_1_djfw9s0s_.log', dbms_logmnr.ADDFILE);

# 启动logminer
dbms_logmnr.start_logmnr(DictFileName=>'/u01/app/logminer/dictionary.ora');
```
至此，存储已经创建好，根据需要，字典可以选择加载或不加载，一般在是在执行DDL之后需要重新加载字典，接下来查询常见信息

```
select SCN,INFO,to_char(timestamp,'yyyy-mm-dd hh24:mi:ss'),table_name,OPERATION,SQL_REDO,SQL_UNDO,status from v$logmnr_contents;
```

## 测试记录


### 只用 ID 作为条件且只更新 LOB 字段（SQL_REDO 和 SQL_UNDO 中缺失 条件）

#### 无主键表
```
create table event
(
 id number,
 name varchar2(20),
 a_blob blob,
 a_clob  clob
);

insert into event (id,name,a_blob,a_clob) values(1,'11','01010101010101','test-clob');

update event set a_blob = null where id =3;

## where 后面的条件缺失
----- sql_redo = update "SYSTEM"."EVENT" set "A_BLOB" = NULL where ;
----- sql_undo = update "SYSTEM"."EVENT" set "A_BLOB" = NULL where ;
```

#### 有主键表

```
create table event
(
 id number primary key,
 name varchar2(20),
 a_blob blob,
 a_clob  clob

);


insert into event (id,name,a_blob,a_clob) values(1,'11','01010101010101','test-clob');

update "SYSTEM"."EVENT" set "A_BLOB" = NULL where ;

### where 后面的条件缺失
-----> sql_redo = update "SYSTEM"."EVENT" set "A_BLOB" = NULL where ;
-----> sql_undo = update "SYSTEM"."EVENT" set "A_BLOB" = NULL where ;
```
> 综上，可以排除未设主键的原因

```
update event set a_blob = null,name = 'xx' where id =1;

--sql_redo :  update "SYSTEM"."EVENT" set "NAME" = 'xx' where "NAME" = '11';
--sql_redo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "NAME" = 'xx';

--sql_undo :  update "SYSTEM"."EVENT" set "NAME" = '11' where "NAME" = 'xx';
--sql_undo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "NAME" = 'xx';
```
> 条件中虽然 ID 字段缺失，但是被更新的字段除了 Lob 之外其对于的旧值出现在 where 子句中，有个猜想，如下测试：

```
update event set a_blob = null,id = 1 where id =1;

--sql_redo :  update "SYSTEM"."EVENT" set "ID" = '1', "A_BLOB" = EMPTY_BLOB() where "ID" = '1';
--sql_redo : update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "ID" = '1';

--sql_undo :  update "SYSTEM"."EVENT" set "ID" = '1', "A_BLOB" = NULL where "ID" = '1';
--sql_undo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "ID" = '1'

```
> 再如：

```
update event set a_blob = null,name='bbb',id = 1 where id =1;

--sql_redo :  update "SYSTEM"."EVENT" set "ID" = '1', "NAME" = 'bbb', "A_BLOB" = EMPTY_BLOB() where "ID" = '1' and "NAME" = 'aaa';
--sql_redo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "ID" = '1' and "NAME" = 'bbb';

--sql_undo :  update "SYSTEM"."EVENT" set "ID" = '1', "NAME" = 'aaa', "A_BLOB" = NULL where "ID" = '1' and "NAME" = 'bbb';
--sql_undo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "ID" = '1' and "NAME" = 'bbb';
```
> 通过测试发现，除了 ID 字段，所有被修改的非 LOB 字段的旧值都会出现在 SQL_REDO 和 SQL_UNDO 的 where 子句中。
  为了避免在单独的以 ID 作为标识修改 LOB 字段时导致的在 SQL_REDO 和 SQL_UNDO 中条件缺失的问题，应该在 set 子句
  中指定 ID 字段的值，尽管 ID 字段并没有修改



```
update event set a_blob = null,name = '1-1',id = 1 where id =1;

  ## 注意此处的 EMPTY_BLOB()
 --sql_redo :  update "SYSTEM"."EVENT" set "ID" = '1', "NAME" = '1-1', "A_BLOB" = EMPTY_BLOB() where "ID" = '1' and "NAME" = 'xx';
 --sql_redo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "ID" = '1' and "NAME" = '1-1';

 --sql_undo :  update "SYSTEM"."EVENT" set "ID" = '1', "NAME" = 'xx', "A_BLOB" = NULL where "ID" = '1' and "NAME" = '1-1';
 --sql_undo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "ID" = '1' and "NAME" = '1-1';
```

```
update event set a_blob = null,a_clob = null ,name = '1-1',id = 1 where id =1;

  ## 注意此处的 EMPTY_BLOB()
--sql_redo :  update "SYSTEM"."EVENT" set "ID" = '1', "NAME" = '1-1', "A_BLOB" = EMPTY_BLOB() where "ID" = '1' and "NAME" = '1-1';
--sql_redo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL, "A_CLOB" = NULL where "ID" = '1' and "NAME" = '1-1';

--sql_undo :  update "SYSTEM"."EVENT" set "ID" = '1', "NAME" = '1-1', "A_BLOB" = NULL where "ID" = '1' and "NAME" = '1-1';
--sql_undo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL, "A_CLOB" = NULL where "ID" = '1' and "NAME" = '1-1';

```

#### 更新BLOB字段内容(BLOB字段无法通过 SQL_UNDO 还原)
```

>表中 id 为 1 的记录字段 a_blob 非空：insert into event (id,name,a_blob,a_clob) values(1,'11','01010101010101','test-clob');

update event set a_blob = '1010101',name = 'aaa' where id =1;

--sql_redo :  update "SYSTEM"."EVENT" set "NAME" = 'aaa', "A_BLOB" = EMPTY_BLOB() where "NAME" = '1-1';
--sql_redo :  update "SYSTEM"."EVENT" set "A_BLOB" = HEXTORAW('01010101') where "NAME" = 'aaa';

--sql_undo :  update "SYSTEM"."EVENT" set "NAME" = '1-1', "A_BLOB" = NULL where "NAME" = 'aaa';
## 最终只是把 a_blob 的设置为 NULL，并不是原来的 '01010101010101'
--sql_undo :  update "SYSTEM"."EVENT" set "A_BLOB" = NULL where "NAME" = 'aaa';

如果使用 activeJDBC 形式 ，执行 update event set a_blob = 'read bytes[] from a file',name = 'aaa' where id =1;
日志中根本就没有出现 a_blob 字段
-- sql_undo :  update "SYSTEM"."EVENT" set "ID" = '1' where "ID" = '1';
```

#### sqldeveloper 和 jdbc 执行含 LOB 字段（超过4K）的 insert 对比

> 含 LOB 字段的 insert 操作，数据库均会先执行 insert 非 LOB 字段的内容，之后再 update LOB 字段的内容。
  使用 sqldeveloper 界面 insert 含 LOB 字段的记录时，日志中的 insert 和 update 的 SCN 是相同的，而
  使用 java 代码通过 activeJDBC insert 含 LOB 字段的记录时，日志中的 insert 和 update 的 SCN 是不
  相同的。
