# _mysql 触发器



## 1. MySQL[触发器](https://so.csdn.net/so/search?q=触发器&spm=1001.2101.3001.7020)的概念与作用

**触发器概念**：触发器是一种特殊的[存储过程](https://so.csdn.net/so/search?q=存储过程&spm=1001.2101.3001.7020)，它会在 **试图更改触发器所保护的数据时** 自动执行。

1.在MySQL中，只有执行insert,delete,update操作时才能触发触发器的执行；
2.触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作；
3.使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发

```mysql
# 触发器 与 存储过程 的区别：

# same:
	1.触发器是一种特殊的存储过程
	2.触发器 / 存储过程 都是一个能够完成特定功能、存储在数据库服务器上的SQL片段。


# difference:
	1.触发器不需要调用，当对数据库中的数据 执行 DML 操作的时候会自动触发 sql 片段，无需手动调用
	2.存储过程调用时 需要调用 sql 片段
```



#### 触发器的特性

```mysql
# 1.触发条件：insert delete update
# 2.触发时间：在增删改 前 或者 后
# 3.触发频率：针对每一行执行
# 4.触发器定义在边上，附着在表上
```



#### 触发器的作用

```mysql
# 1.安全性。能够基于数据库的值使用户具有操作数据库的某种权利。
	能够基于时间限制用户的操作，比如不同意下班后和节假日改动数据库数据。
	能够基于数据库中的数据限制用户的操作，比如不同意股票的价格的升幅一次超过10%。


# 2.审计。能够跟踪用户对数据库的操作
	审计用户操作数据库的语句。
	把用户对数据库的更新写入审计表


# 3.实现复杂的数据完整性规则
	实现非标准的数据完整性检查和约束。触发器可产生比规则更为复杂的限制。与规则不同，触发器能够引用列或数据库对象。比如，触发器可回退不论什么企图吃进超过自己保证金的期货。
	提供可变的缺省值。


# 4.实现复杂的非标准的数据库相关完整性规则。触发器能够对数据库中相关的表进行连环更新。比如，在auths表author_code列上的删除触发器可导致对应删除在其他表中的与之匹配的行。

	在改动或删除时级联改动或删除其他表中的与之匹配的行。

	在改动或删除时把其他表中的与之匹配的行设成NULL值。

	在改动或删除时把其他表中的与之匹配的行级联设成缺省值。

	触发器可以拒绝或回退那些破坏相关完整性的变化，取消试图进行数据更新的事务。当插入一个与其主健不匹配的外部键时，这样的触发器会起作用。比如，可以在books.author_code 列上生成一个插入触发器，假设新值与auths.author_code列中的某值不匹配时，插入被回退。


# 5.同步实时地复制表中的数据。

# 6.自己主动计算数据值，假设数据的值达到了一定的要求，则进行特定的处理。
	比如，假设公司的帐号上的资金低于5万元则马上给財务人员发送警告数据。
```



### 1.1创建触发器

```mysql
# 创建只有一个执行语句的触发器
create trigger 触发器名 before|after 触发事件
	on table_name for each row
	执行语句;


# 创建多个执行语句的触发器
create trigger 触发器名 before|after 触发事件
	on table_name for each row
	begin
		执行语句列表
	end;

```





```mysql
# 实例

create database if not exists mydb01_trigger;

use mydb01_trigger;

-- 用户表

create table if not exists user(
 uid int primary key auto_increment,
 username varchar(50) not null,
 password varchar(50) not null
)default charset=utf8;

-- 用户信息操作日志表
create table if not exists user_logs(
 id int primary key auto_increment,
 time timestamp,
 log_text varchar(100)
 )default charset=utf8;
 
-- 需求1:当user表添加一行数据，则会自动在user_log添加日志记录
-- 定义触发器： trigger_test1
create trigger trigger_test1 after insert on user for each row
insert into user_logs values(NULL,now(),'new');

-- 在user表添加数据，让触发器自动执行
insert into user values(2,'zbb','123456');


```





#### 1.2 触发器类型NEW和OLD的使用

`Mysql` 中定义了 new 和 old，用来表示触发器的所在表中，触发了触发器的那一行数据，来引用触发器中发生变化的记录内容，具体地

```mysql
# insert型触发器 
	new 表示将要或者已经新增的数据


# update型触发器
	old 表示 修改之前的数据
	new 表示将要或者已经修改的数据

# delete型触发器
	old 表示将要或者已经删除的数据
	

```

![](E:\learning_document\database_mysql\pictures\存储器new_old.png)



```mysql

create database if not exists mydb01_trigger;

use mydb01_trigger;

-- 用户表

create table if not exists user(
 uid int primary key auto_increment,
 username varchar(50) not null,
 password varchar(50) not null
)default charset=utf8;

-- 用户信息操作日志表

create table if not exists user_logs(
 id int primary key auto_increment,
 time timestamp,
 log_text varchar(255)
 )default charset=utf8;
 
 
 
-- 需求1:当user表添加一行数据，则会自动在user_log添加日志记录
-- 定义触发器： trigger_test1
create trigger trigger_test1 after insert on user for each row insert into user_logs 
values(NULL,now(),'new');



-- 在user表添加数据，让触发器自动执行
insert into user values(3,'zbb','123456');
 
 
 
 ##################################################################################################
 
 -- NEW和OLD
 -- insert 触发器
 -- NEW
 -- 定义触发器： trigger_test2
 
drop trigger trigger_test1 # 删除触发器 trigger_test1

create trigger trigger_test2 after insert on user for each row
insert into user_logs values(NULL,now(),concat('有新用户添加,信息为：',NEW.username,NEW.password));

insert into user values(4,'abb','123456');


 -- update 触发器
 -- NEW
 -- 定义触发器： trigger_test3
 -- OLD
drop trigger trigger_test2
create trigger trigger_test3 after update on user for each row
insert into user_logs values(NULL,now(),concat('有用户信息修改,旧数据是：',OLD.uid,OLD.username,OLD.password));

update user set password = '00000' where uid=3;


 -- NEW
 drop trigger trigger_test3
create trigger trigger_test4 after update on user for each row
insert into user_logs values(NULL,now(),concat('有用户信息修改：新数据是',NEW.uid,NEW.username,NEW.password));

update user set password = '666666' where uid=3;


-- delete类型触发器
-- OLD
create trigger trigger_test5 after delete on user for each row
insert into user_logs values(NULL,now(),concat('有用户被删除，删除信息为：',OLD.uid,OLD.username,OLD.password));

# 删除数据 触发触发器
delete from user where uid=3;


```













# 参考

https://www.cnblogs.com/mengfanrong/p/3851410.html
https://www.bilibili.com/video/BV1iF411z7Pu?p=126&spm_id_from=pageDriver