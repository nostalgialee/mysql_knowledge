# 7_Navicat工具、pymysql模块



## 一、 IDE工具介绍





## 二 、pymysql模块

​		pymysql就是用来在python程序中如何操作mysql，它和mysql自带的那个客户端还有navicat是一样的，本质上就是一个套接字客户端，只不过这个套接字客户端是在python程序中用的，既然是客户端套接字，应该怎么用，是不是要连接服务端，并且和服务端进行通信啊，让我们来学习一下pymysql这个模块

**一 链接、执行sql、关闭（游标）**

```mysql
#链接
    conn=pymysql.connect( #得到conn这个连接对象
        host='localhost', # 本机上测试时ip地址可以写localhost或者自己的ip地址或者127.0.0.1
        port=3306, # 指定ip地址和端口
        user='root',
        password='123',
        database='student',

        # 要指定字符集。不然会出现乱码
        charset='utf8' #指定编码为utf8的时候，注意没有-，别写utf-8
    ) 


#游标
    cursor=conn.cursor() # 相当于mysql自带的那个客户端的游标mysql> 在这后面输入指令，回车执行
    #  获取字典数据类型表示的结果：
    # {'sid': 1, 'gender': '男', 'class_id': 1, 'sname': '理解'} {'字段名':值}
    #cursor=conn.cursor(cursor=pymysql.cursors.DictCursor) 


# 然后给游标输入sql语句并执行sql语句execute
    sql='select * from userinfo where name="%s" and password="%s"' %(user,pwd) 
    # 注意%s需要加引号，执行这句sql的前提是医药有个userinfo表，里面有name和password两个字段，还有一些数据，自己添加数据昂


# 执行sql语句，返回sql查询成功的记录数目
    res=cursor.execute(sql)
    print(res) #一个数字
    # 返回结果 res 是个数字，是受sql语句影响到的记录行数，其实除了受影响的记录的条数之外，这些记录的数据也都返回了给游标,这个就相当于我们subprocess模块里面的管道PIPE，乘放着返回的数据

    # all_data=cursor.fetchall()  #获取返回的所有数据，注意凡是取数据，取过的数据就没有了，结果都是元祖格式的 

    # many_data=cursor.fetchmany(3) #一下取出3条数据，

    # one_data=cursor.fetchone()  #按照数据的顺序，一次只拿一个数据，下次再去就从第二个取了，因为第一个被取出去了，取一次就没有了，结果也都是元祖格式的


      fetchone：(1, '男', 1, '理解')  fetchone：(2, '女', 1, '钢蛋')  fetchall：((3, '男', 1, '张三'), (4, '男', 1, '张一')）
    #上面fetch的结果都是元祖格式的，没法看出哪个数据是对应的哪个字段，这样是不是不太好看，想一想，我们可以通过python的哪一种数据类型，能把字段和对应的数据表示出来最清晰，当然是字典{'字段名':值}

    #我们可以再创建游标的时候，在cursor里面加上一个参数：cursor=conn.cursor(cursor=pymysql.cursors.DictCursor)获取的结果就是字典格式的，fetchall或者fetchmany取出的结果是列表套字典的数据形式

                                                                    
上面我们说，我们的数据取一次是不是就没有了啊，实际上不是的，这个取数据的操作就像读取文件内容一样，每次read之后，光标就移动到了对应的位置，我们可以通过seek来移动光标同样，我们可以移动游标的位置，继续取我们前面的数据,通过cursor.scroll(数字，模式)，第一个参数就是一个int类型的数字，表示往后移动的记录条数，第二个参数为移动的模式，有两个值：absolute：绝对移动，relative：相对移动
                                                                        
	# 绝对移动：它是相对于所有数据的起始位置开始往后面移动的#相对移动：他是相对于游标的当前位置开始往后移动的#绝对移动的演示#print(cursor.fetchall())#cursor.scroll(3,'absolute') #从初始位置往后移动三条，那么下次取出的数据为第四条数据#print(cursor.fetchone())
	# 相对移动的演示#print(cursor.fetchone())#cursor.scroll(1,'relative') #通过上面取了一次数据，游标的位置在第二条的开头，我现在相对移动了1个记录，那么下次再取，取出的是第三条，我相对于上一条，往下移动了一条#print(cursor.fetchone())


cursor.close() #关闭游标
conn.close()   #关闭连接

if res:
    print('登录成功')
else:
    print('登录失败')
```





**二 execute()之sql注入**	

```mysql
import pymysql

conn = pymysql.connect(
    host='127.0.0.1',
    port=3306,
    user='root',
    password='666',
    database='crm',
    charset='utf8'
)


# 案例1
sql = "select * from userinfo where username='%s' and password='%s';"%(uname,pword)
res = cursor.execute(sql)
if res:
    print('登陆成功')
else:
    print('用户名和密码错误！')

# 假如按照如下输入
>>>请输入用户名：chao' -- xxx
>>>请输入密码：
>>>登陆成功

用户名错误，密码未输入为何成功？？？ # -- 在 sql 中是注释的含义 
此时uname这个变量等于什么，等于chao' -- xxx
# sql语句被这个字符串替换为：
	select * from userinfo where username='chao' -- xxx' and password=''; 
# 然后后面--在sql语句里面是注释的意思, 拿到的sql语句是
	select * from userinfo where username='chao';
# 语句执行了，发现能够找到对应的记录，因为有用户名为chao的记录，然后他就登陆成功了，跳过了认证环节。




# 案例2
>>>请输入用户名：xxx' or 1=1 -- xxxxxx
>>>请输入密码：

登陆成功

我们只输入了一个 xxx' 加or 加 1=1 加 -- 加任意字符串
select * from userinfo where username='xxx' or 1=1 -- xxxxxx' and password='';

看上面被执行的sql语句你就发现了，or 后面跟了一个永远为真的条件，那么即便是username对不上，但是or后面的条件是成立的，也能够登陆成功。



# 你不能输入一些特殊的符号，因为有些特殊符号可以改变sql的执行逻辑，其实不光是--，还有一些其他的符号也能改变sql语句的执行逻辑
```



**上面两个例子就是两个sql注入的问题**
	在服务端来解决sql注入的问题：**不要自己来进行sql字符串的拼接了**，pymysql能帮我们拼接，他能够防止sql注入，所以以后我们再写sql语句的时候按下面的方式写：

```mysql
之前我们的sql语句是这样写的：
sql = "select * from userinfo where username='%s' and password='%s';"%(uname,pword)

# 改为：
sql = "select * from userinfo where username=%s and password=%s;"
cursor.execute(sql,[uname,pword])
# 本质也是帮你进行了字符串的替换，只不过它会将uname和pword里面的特殊字符给过滤掉。

```







**三 增、删、改：conn.commit()**

```mysql
import pymysql
# 链接
conn=pymysql.connect(
	host='localhost',
    port='3306',
    user='root',
    password='123',
    database='crm',
    charset='utf8'
)
# 游标
cursor=conn.cursor()

# 执行sql语句
# part1
    sql='insert into userinfo(name,password) values("root","123456");'
    #执行sql语句，返回sql影响成功的行数
    res=cursor.execute(sql)
    print(res)# print(cursor.lastrowid) #返回的是你插入的这条记录是到了第几条了


# part2
    sql='insert into userinfo(name,password) values(%s,%s);'
    res=cursor.execute(sql,("root","123456")) #执行sql语句，返回sql影响成功的行数
    print(res)
# 还可以进行更改操作：
    # res=cursor.excute("update userinfo set username='taibaisb' where id=2")
    # print(res) #结果为1


# part3
sql='insert into userinfo(name,password) values(%s,%s);'
res=cursor.executemany(sql,[("root","123456"),("lhf","12356"),("eee","156")]) 
# 执行sql语句，返回sql影响成功的行数，一次插多条记录
print(res)

# 上面的几步，虽然都有返回结果，也就是那个受影响的函数res，
# 但是并没有保存到数据库里面，

# 必须执行conn.commit, 注意是conn，不是cursor，
# 执行这句提交后才发现表中插入记录成功，没有这句，上面的这几步操作其实都没有成功保存。
conn.commit() 

cursor.close()
conn.close()
```



**四 查：fetchone，fetchmany，fetchall**

```mysql
import pymysql
#链接
conn=pymysql.connect(
    host='localhost',
    user='root',
    password='123',
    database='egon'
)

#游标
cursor=conn.cursor()

#执行sql语句
sql='select * from userinfo;'
# 执行sql语句，返回sql影响成功的行数rows,将结果放入一个集合，等待被查询
rows=cursor.execute(sql) 

# cursor.scroll(3,mode='absolute') # 相对绝对位置移动
# cursor.scroll(3,mode='relative') # 相对当前位置移动

# one data
res1=cursor.fetchone() # (1, 'root', '123456')
res2=cursor.fetchone() # (2, 'root', '123456')
res3=cursor.fetchone() # (3, 'root', '123456')


# many data
res4=cursor.fetchmany(2) # 参数是取出几条数据
# ((4, 'root', '123456'), (5, 'root', '123456'))


# all data
res5=cursor.fetchall() # 取出剩余的所有数据
#  ((6, 'root', '123456'), (7, 'lhf', '12356'), (8, 'eee', '156'))



print(res1)
print(res2)
print(res3)
print(res4)
print(res5)
print('%s rows in set (0.00 sec)' %rows)



conn.commit() # 提交后才发现表中插入记录成功
cursor.close()
conn.close()

'''

((4, 'root', '123456'), (5, 'root', '123456'))
((6, 'root', '123456'), (7, 'lhf', '12356'), (8, 'eee', '156'))
rows in set (0.00 sec)
'''

```



**五 获取插入的最后一条数据的自增ID**

```mysql
import pymysql
conn=pymysql.connect(host='localhost',user='root',password='123',database='egon')
cursor=conn.cursor()

sql='insert into userinfo(name,password) values("xxx","123");'
rows=cursor.execute(sql)

# 获取插入的最后一条数据的自增ID
print(cursor.lastrowid) # 在插入语句后查看 

conn.commit()

cursor.close()
conn.close()
```

