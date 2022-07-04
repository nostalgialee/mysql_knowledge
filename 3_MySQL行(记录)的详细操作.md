# 3_MySQL行(记录)的详细操作 

增

```mysql
# 插入完整语法
insert into table_name (字段1, 字段2, ..) values(value1, value2,..);
insert into table_name values(value1, value2,..);

# 指定字段插入数据
insert into table_name (字段1, 字段2, ..) values(value1, value2,..);

# 插入多条记录
insert into table_name (字段1, 字段2, ..) values
(value1, value2,..),
(value1, value2,..),
(value1, value2,..),
...
;

# 插入查询结果
insert into 表名(字段1,字段2,字段3…字段n) 
                    select (字段1,字段2,字段3…字段n) from 表2
                    where …
                    

```



删

```mysql
# 删除符合条件的记录
delete from table_name where conition


```







改





查





权限管理

```mysql
#授权表
user #该表放行的权限，针对：所有数据，所有库下所有表，以及表下的所有字段
db #该表放行的权限，针对：某一数据库，该数据库下的所有表，以及表下的所有字段
tables_priv #该表放行的权限。针对：某一张表，以及该表下的所有字段
columns_priv #该表放行的权限，针对：某一个字段

#按图解释：
user：放行db1，db2及其包含的所有
db：放行db1，及其db1包含的所有
tables_priv:放行db1.table1，及其该表包含的所有
columns_prive:放行db1.table1.column1，只放行该字段


# 创建用户
create user 'name@ip地址' identified by '密码';

# 授权：对文件夹，对文件，对文件某一字段的权限
查看帮助：help grant
常用权限有：select,update,alter,delete
all可以代表除了grant之外的所有权限


#针对所有库的授权:*.*
grant select on * to 'name@ip地址'  identified by '密码';

#针对某一数据库：db1.*
grant select on db1.* to 'name@ip地址'  identified by '密码';

#针对某一个表：db1.t1
grant select on db1.t1 to 'name@ip地址'  identified by '密码';


#删除权限
revoke select on db1.* from 'name@ip地址';

```

