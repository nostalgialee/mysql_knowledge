### 2_MySql的库表详细操作



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
alter database data_name ...;



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
create table table_name (
	字段名, 类型[长度 约束条件],
	字段名, 类型[长度 约束条件],
	字段名, 类型[长度 约束条件],
	字段名, 类型[长度 约束条件],
    ...
);

#注意：
	# 1. 在同一张表中，字段名是不能相同
	# 2. 宽度和约束条件可选、非必须，宽度指的就是字段长度约束，例如：char(10)里面的10
	# 3. 字段名和类型是必须的

ex:
create table tb1 (
	id int(10) primary key;
    name varchar(10) 
    sex enum('male','female'),
    ...
);


# 查看xx库下所有表名
show tables;

```





### 4.查看表结构

```mysql
# 查看表的结构
desc table_name;

# 查看表的详细情况，可加\G
show create table table_name;
show create table table_name\G;


```





### 5.MySql的基础数据类型

###### 	1.整数

```mysql
tinyint
	# 1字节
	# PS： MySQL中无布尔值，使用tinyint(1)构造。

smallint
	# 2字节

mediumint
	# 3字节
	
int
	# 4字节
	# int(宽度) unsigned
	# int的存储宽度是4个Bytes，即32个bit，即2**32
	# 对 int 约束长度无效

bigint
	# 8字节
	
# 整形类型，其实没有必要指定显示宽度，使用默认的就ok
# 对于整型来说，数据类型后面的宽度并不是存储长度限制，而是显示限制
用zerofill测试整数类型的显示宽度
    mysql> create table t7(x int(3) zerofill);
    mysql> insert into t7 values
        -> (1),
        -> (11),
        -> (111),
        -> (1111);
    mysql> select * from t7;
    +------+
    | x    |
    +------+
    |  001 |
    |  011 |
    |  111 |
    | 1111 | #超过宽度限制仍然可以存
    +------+
    
# 假如：int(8)，那么显示时不够8位则用0来填充，够8位则正常显示，
# 通过zerofill来测试，存储长度还是int的4个字节长度。默认的显示宽度就是能够存储的最大的数据的长度
# int无符号类型，那么默认的显示宽度就是int(10)，有符号的就是int(11)
	# 因为：int  
		   # 有符号：
            #         -2147483648 ～ 2147483647 (11位)
            # 无符号：
            #        0 ～ 4294967295 (10位)
```

![](E:\learning_document\database_mysql\pictures\基础数据类型_整数.png)

#### 	

MySQL的mode设置博客：<https://www.cnblogs.com/clschao/articles/9962347.html>

#### 	

###### 	2.浮点型

```mysql
# float 4字节

	# 单精度浮点数（非准确小数值），

	# m是整数部分+小数部分的总个数，d是小数点后个数。m最大值为255，d最大值为		30，例如：float(255,30)

	# 随着小数变多，精度变低



# double 8字节

	# 双精度浮点数（非准确小数值）

	# m是整数部分+小数部分的总个数，d是小数点后个数。m最大值也为255，

	# d最大值也为30	

	# 随着小数的增多，精度比float要高，但也会变得不准确 



# decimal[(m[,d])] [unsigned] [zerofill]

	# 准确的小数值，m是整数部分+小数部分的总个数（负号不算），d是小数点后个			数。 m最大值为65，d最大值为30。比float和double的整数个数少，
	# 但是小数位数都是30位

	# 精确度：
#               随着小数的增多，精度始终准确
#               对于精确数值计算时需要用此类型
#               decimal能够存储精确值的原因在于其内部按照字符串存储。


```







###### 	3.位类型

###### 	4.日期类型

```mysql
###### date
	# 数据：
		# yyyy-mm-dd(1000-01-01/9999-12-31)


time
	# 数据：
		# HH:MM:SS （范围：'-838:59:59'/'838:59:59'）


datetime
	# 数据：	
		# YYYY-MM-DD HH:MM:SS（范围：1000-01-01 00:00:00/9999-12-31 23:59:59)



timestamp
	# 数据：
		# YYYYMMDD HHMMSS（范围：1970-01-01 00:00:00）
		# TIMESTAMP的时间范围是1970——2038

year
	# 数据：
		# xxxx (1901-2155)
		# 无论year指定何种宽度，最后都默认是year(4)

		
# ============注意啦，注意啦，注意啦===========
#    1. 单独插入时间时，需要以字符串的形式，按照对应的格式插入
#    2. 插入年份时，尽量使用4位值
#    3. 插入两位年份时，<=69，以20开头，比如50,  结果2050      
#                    >=70，以19开头，比如71，结果1971
#    4.无需任何设置，datetime 和 timestamp 在传空值的情况下自动传入当前时间

```



###### 	5.字符串类型

```mysql
#注意：char和varchar括号内的参数指的都是字符的长度

char
	# 定长，浪费空间，存取速度快
 	# 存储：字符长度范围：0-255（一个中文是一个字符，是utf8编码的3个字节）
        # 存储char类型的值时，会往右填充空格来满足长度
        # 例如：指定长度为10，存>10个字符则报错(严格模式下)，存<10个字符则用空格填充直到凑够10个字符存储
        set sql_mode='NO_ENGINE_SUBSTITUTION'; # 设置非严格模式，但是存不全。
	# 检索：
        # 在检索或者说查询时，查出的结果会自动删除尾部的空格，如果你想看到它补全空格之后的内容，除非我们打开
        # pad_char_to_full_length SQL模式（SET sql_mode = 'strict_trans_tables,PAD_CHAR_TO_FULL_LENGTH';）





varchar
	# 变长，精准，节省空间，存取速度慢
	# 字符长度范围：0-65535（如果大于21845会提示用其他类型 。mysql行最大限制为65535字节
	# 存储：
        # varchar类型存储数据的真实内容，不会用空格填充，如果'ab  ',尾部的空格也会被存起来
        # 强调：varchar类型会在真实数据前加1-2Bytes的前缀，该前缀用来表示真实数据的bytes字节数（1-2Bytes最大表示65535个数字，正好符合mysql对row的最大字节限制，即已经足够使用）
        # 如果真实的数据<255bytes则需要1Bytes的前缀（1Bytes=8bit 2**8最大表示的数字为255）
        # 如果真实的数据>255bytes则需要2Bytes的前缀（2Bytes=16bit 2**16最大表示的数字为65535）
	
	# 检索：
        # 尾部有空格会保存下来，在检索或者说查询时，也会正常显示包含空格在内的内容
        



# 总结：
	# 1.char: mysql存储的时候会将不足规定长度的数据在后面使用空格补全，再存储到硬盘中，读取的时候会去掉空格
```



​		char和varchar性能对比：

```mysql
# 以char(5)和varchar(5)来比较，加入我要存三个人名：sb，ssb1，ssbb2
　　　　char：
　　　　　　# 优点：简单粗暴，不管你是多长的数据，我就按照规定的长度来存，5个5个的存，三个人名就会类似这种存储：sb ssb1 ssbb2，中间是空格补全，取数据的时候5个5个的取，简单粗暴速度快
　　　　　　# 缺点：貌似浪费空间，并且我们将来存储的数据的长度可能会参差不齐
　　　　　　
　　　　　　

　　　　varchar类型不定长存储数据，更为精简和节省空间
		  # varchar在存数据的时候，会在每个数据前面加上一个头，这个头是1-2个bytes的数据，这个数据指的是后面跟着的这个数据的长度，1bytes能表示2**8=256，两个bytes表示2**16=65536，能表示0-65535的数字
		  # varchar在存储：
		  # 1bytes+sb+1bytes+ssb1+1bytes+ssbb2，所以存的时候会比较麻烦，导致效率比char慢，取的时候也慢，先拿长度，再取数据。



# 当你存的数据正好是你规定的字段长度的时候，varchar反而占用的空间比char要多。
```





###### 	6.枚举类型与集合类型

```mysql
enum 
	# 单选 只能在给定的范围内选一个值，如性别 sex 男male/女female


set 
	# 多选 在给定的范围内可以选择一个或一个以上的值（爱好1,爱好2,爱好3...）


```





### 6.表的完整性约束

```mysql

not null 
default
	
unique:
	# 方式二：
    create table department2(
    id int,
    name varchar(20),
    comment varchar(100),
    constraint uk_name unique(name) # 设置唯一的另一种方式
    );
	# 联合唯一
	unique(xx,xy)

auto_increment
		# 1.自增
		create table student(
			id int primary key auto_increment,)
		# 2.对于自增的字段，在用delete删除后，再插入值，该字段仍按照删除前的位置继续增长
		# 3.应该用truncate清空表，比起delete一条一条地删除记录，truncate是直接清空表，在删除大表时用它
		# 4.修改 自增字段的起始值 
		alter table student auto_increment=3;
		# 5.可以创建表时指定auto_increment的初始值，注意初始值的设置为表选项，应该放到括号外
        create table student(
        id int primary key auto_increment,
        name varchar(20),
        sex enum('male','female') default 'male'
        )auto_increment=3;

unsigned

zerofill

primary key

foreign key

```



**foreign key**

```mysql
# 外键

# 1.一对多
举例子 员工信息表有三个字段：工号  姓名  部门
create table department(
	id int primary key auto_increment,
    dep_name char(10),
    dep_comment char(60)
)

create table empolyee(
	id int primary key auto_increment,
	name char(16),
    gender enum('male', 'female') not null default 'male',
    dep_id int, 
    foreign key(dep_id) references dep(id), # 设置外键
    on delete cascade # 级联删除
    on update cascade # 级联更新
)

# 2.多对多
举例子 作者与书籍
# 解决方案： 第三张表
create table author(
	id int primary key auto_increment,
	name char(16)
)

create table book(
	id int primary key auto_increment,
	bname char(16),
    price int
)

# 第三张表
create table anthor2book(
	id int primary key auto_increment,
	author_id int,
	book_id int,
	foreign key(author_id) references author(id)
	on delete cascade # 级联删除
	on update cascade, # 级联更新
	# foreign key(book_id) references book(id)
    
    # 指定外键的名字
    constraint fk_book foreign key(book_id) references book(id)
    
	on delete cascade # 级联删除
	on update cascade, # 级联更新
)


# 删除外键字段
	# 删除外键关联
	alter table table_name drop foreign key fk_name
	# 删除外键字段
	alter table table_name drop 字段



# 3.一对一
# foreign key + unique 实现 一对一



# 外键的约束条件
	# 模式一： district 严格约束（默认的 ），父表不能删除或者更新已经被子表数据引用的记录
	# 模式二：cascade 级联模式：父表的操作，对应的子表关联的数据也跟着操作 。
	# 模式三：set null：置空模式，父表操作之后，子表对应的数据（外键字段）也跟着被置空。
	on delete 模式 
	on update 模式;
	# 通常的一个合理的约束模式是：删除的时候子表置空；更新的时候子表级联。
	# 注意：删除置空的前提条件是 外键字段允许为空，不然外键会创建失败。





# 了解：
	# 将来你们接触某一些大型项目的时候，尽量不要给表建立外键关系，因为外键直接在数据库级别就变成耦合的了，那么我们要拓展或者删除或者更改某些数据库或者数据表的时候，拓展起来就比较难，我们可以自己从自己的程序代码的逻辑层面上将这些关联关系建立好，有很多公司就是这么做的，利于拓展，如果我们加了很多的foreign key ，那么当你想删除一个表的时候，可能会牵一发而动全身，了解一下就可以了
	
	
```







### 7.修改表 alter table





### 8.复制表

















## ps:

```mysql

# SQL语言一共分为4大类：数据定义语言DDL，数据操纵语言DML，数据查询语言DQL，数据控制语言DCL




```

