---
layout: post
title: mysql库表操作
category: 技术
tags: mysql
keywords: mysql
---

## MySQL库表操作
首先，要连接MySQL服务器，`$mysql -h host -u user -p `。

### 库的操作

查看当前数据库 `>SELECT database();`
显示当前时间 `>SELECT now();`
显示用户名　`>SELECT user();`
显示版本 `>SELECT version();`

创建库
　　create database  [if not exists] 数据库名　数据库选项;
　　数据库选项：
　　　　CHARACTER SET charset_name　--  用于为表指定一个默认字符集
　　　　COLLATE collation_name　--  校对规则，指定默认排序

查看已有库　`>SHOW databases [LIKE 'pattern'];`
查看当前库信息　`>SHOW CREATE database 数据库名;`
修改库选项信息　`>ALTER database 库命　选项信息;`
删除库　`>DROP database [IF EXISTS] 数据库名`　-- 同时删除该数据库相关目录及其内容


### 表的操作

创建表
　　create [temporary] table [if not exists] [库名.]表名　（表的结构定义）[表选项]
　　　　每个字段都必须有数据类型
　　　　最后一个字段后不能有逗号
　　　　temporary 临时表，会话结束表自动消失
　　　　对于字段的定义：
　　　　字段名　数据类型　[NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE [KEY] | [PRIMARY] KEY] [COMMENT 'string']
　　　　表选项　　
　　　　1. 字符集
　　　　CHARSET = charset_name 如果表没有设定，则使用数据字符集
　　　　2. 存储引擎
　　　　　　ENGINE = engine_name
　　　　　　ENGINE = engine_name
　　　　　　表在管理数据时采用的不同的数据结构，结构不同会导致处理方式、提供的特性操作等不同
　　　　　　常见的引擎：InnoDB MyISAM Memory/Heap BDB Merge Example CSV MaxDB Archive
　　　　　　不同的引擎在保存表的结构和数据时采用不同的方式
　　　　　　MyISAM表文件含义：.frm表定义，.MYD表数据，.MYI表索引
　　　　　　InnoDB表文件含义：.frm表定义，表空间数据和日志文件
　　　　　　`>SHOW ENGINES` -- 显示存储引擎的状态信息
　　　　　　`>SHOW ENGINE 引擎名 {LOGS|STATUS}` -- 显示存储引擎的日志或状态信息

　　　　DATA DIRECTORY = '目录' -- 数据文件目录
　　　　INDEX DIRECTORY = '目录' -- 索引文件目录
　　　　COMMENT = 'string' -- 表注释
　　　　PARTITION BY -- 分区选项

查看所有表
　　`SHOW TABLES[ LIKE 'pattern']`
　　`SHOW TABLES FROM 表名`

查看表结构
　　`SHOW CREATE TABLE 表名`
　　`DESC 表名 / DESCRIBE 表名 / EXPLAIN 表名 / SHOW COLUMNS FROM 表名 [LIKE 'PATTERN']`
　　`SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern']`

修改表
　　`ALTER TABLE 表名 表的选项`　-- 修改表本身的选项

 对表进行重命名
　　RENAME TABLE 原表名　TO 新表名
　　RENAME TABLE 原表名　TO 库名.新表名

修改表的字段结构
　　ALTER TABLE 表名　操作名
　　　　操作名
　　　　　　ADD [COLUMN] 字段名 -- 增加字段　ALTER TABLE users ADD age INT NOT NULL DEFAULT 0;
　　　　　　　　AFTER 字段名 -- 增加在该字段名后面  ALTER TABLE users ADD age INT NOT NULL DEFAULT 0 AFTER id;
　　　　　　　　FIRST -- 增加在第一个 ALTER TABLE users ADD age INT NOT NULL DEFAULT 0 FIRST;
　　　　　　ADD PRIMARY KEY（字段名） -- 创建主键 ALTER TABLE users ADD PRIMARY key (id);
　　　　　　ADD UNIQUE [索引名]（字段名）--创建唯一索引 ALTER TABLE users ADD UNIQUE index_image (image);
　　　　　　ADD INDEX [索引名]（字段名）--创建普通索引 ALTER TABLE users ADD INDEX index_image (image);
　　　　　　DROP　[COLUMN] 字段名 -- 删除字段 ALTER TABLE users DROP image;
　　　　　　MODIFY　[COLUMN] 字段名 字段属性　-- 支持对字段属性进行修改，不能修改字段名(所有原有属性也需写上)ALTER TABLE users MODIFY image vachar(128);
　　　　　　CHANGE　[COLUMN] 原字段名 新字段名 字段属性　-- 支持对字段名修改
　　　　　　DROP PRIMARY KEY    -- 删除主键(删除主键前需删除其AUTO_INCREMENT属性)
　　　　　　DROP INDEX 索引名    -- 删除索引

删除表　`>DROP TABLE [IF EXISTS] 表名`
清空数据表　`>TRUNCATE [TABLE] 表名`
复制表结构和数据　`>CREATE TABLE 表名　LIKE 要复制的表名`
检查表示否有错误　`>CHECK TABLE 表名[,表名]`
优化表　`>OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name]`
修复表　`>REPAIR [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... [QUICK] [EXTENDED] [USE_FRM]`
分析表　`>ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...`

### 数据操作
1. 增
　　INSERT [INTO] 表名 [(字段列表)] VALUES (值列表)[, (值列表), ...]
　　　　-- 如果要插入的值列表包含所有字段并且顺序一致，则可以省略字段列表。
　　　　-- 可同时插入多条数据记录！
　　　　REPLACE 与 INSERT 完全一样，可互换。
　　INSERT [INTO] 表名 SET 字段名=值[, 字段名=值, ...]
2. 查
　　SELECT 字段列表 FROM 表名[ 其他子句]
　　　　-- 可来自多个表的多个字段
　　　　-- 其他子句可以不使用
　　　　-- 字段列表可以用*代替，表示所有字段
3. 删
　　DELETE FROM 表名[ 删除条件子句]
　　　　没有条件子句，则会删除全部
4. 改
　　UPDATE 表名 SET 字段名=新值[, 字段名=新值] [更新条件]
