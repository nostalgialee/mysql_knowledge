# 2_MySql的库表详细操作



## 一.库操作

```mysql
# 1.创建数据库
create database database_name charset utf8;
# 数据库命名规则 -- 基本上跟python或者js的命名规则一样
			# 可以由字母、数字、下划线、＠、＃、＄
			# 区分大小写
			# 唯一性
			# 不能使用关键字如 create select
			# 不能单独使用数字
			# 最长128位



# 2.数据库相关操作
# 查看数据库
show databases;
# 选择数据库
use database_name;
# 删除数据库
drop database data_name;

# 修改数据库 ******************************这个不熟悉
alter database data_name charset utf8;


```





## 二.表操作  ---  表本质上就是磁盘上的文件

### 1.存储引擎 --- 表的类型 （挖个坑）

​		**存储引擎即表类型**，mysql根据不同的表类型会有不同的处理机制。
详细查看文章：_MySQL之存储引擎 或者
https://www.cnblogs.com/clschao/articles/9953550.html

```mysql
# 查看 mysql 的所有引擎
show engines;

# 查看当前正在使用的引擎
show variables like "storage_engine%";

```



### 2.表介绍

表相当于文件，表中的每一条记录相当于文件中的一行内容，表中的一条记录有对应的标题，成为表的字段.





### 3.创建表

```mysql
# 建表语法

```







### 4.查看表结构





### 5.MySql的基础数据类型





### 6.表的完整性约束





### 7.修改表 alter table





### 8.复制表

















## ps:

```mysql

# SQL语言一共分为4大类：数据定义语言DDL，数据操纵语言DML，数据查询语言DQL，数据控制语言DCL




```

