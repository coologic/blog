---
title: " python使用PyMySQL实现mysql增删改查及防注入\t\t"
tags:
  - mysql
  - pymysql
  - python
url: 1052.html
id: 1052
categories:
  - 数据库
date: 2018-03-28 20:29:17
---

介绍
--

pymysql是python中访问mysql的库。由于要在阿里的函数计算中访问mysql，对于云数据库RDS阿里没有提供python内置模块，需要使用自定义模块。最后选用的pymysql

连接
--

try:
  db = pymysql.connect(host="XXAAcom", 
                       user="AA", 
                            password="XX", 
                            db="dbname",
                            charset='utf8mb4',
                            connect_timeout=1,
                            cursorclass=pymysql.cursors.DictCursor)
except pymysql.OperationalError as e:
  return "connect error:%s"%(e)

connect_timeout设置的是超时时间，单位秒，可以给小数，比如0.1则为100ms 连接超时会给出pymysql.OperationalError错误类型，注意此错误类型的具体内容会显示host地址。

增删改查
----

pymysql的一切操作基于游标cursor，首先通过db.cursor()获取一个，然后进行sql执行。 执行sql命令：cursor.execute(sql\_text,param)，其中sql\_text为标准sql语句,可以使用%s等占位符标记各个变量，并在param中给出参数值。 对于增、删、改，在执行完execute后需要执行db.commit()，提交数据使修改操作生效。 对于查，执行完execute后在cursor中存储了结果，可以通过cursor.fetchall()获取所有结果，可以直接打印，打印效果为json格式。fetchone获取返回的第一个结果，fetchmany(n)获取n行结果。 除execute以外还有executemany可以执行多次相同sql不同参数的操作，范例如下(增删改只提供了增，另外两个只需要修改sql字符串即可)：

try:
  db = pymysql.connect(…………)
except Exception as e:
  return "connect error:%s"%(e)
cursor = db.cursor()
insert_sql = "INSERT INTO test(id,text,time) VALUES (%s,%s,NOW())"
try:
  cursor.execute(insert_sql,(1003,"zltest"))
  db.commit()
except Exception as e:
  db.rollback()
  return "insert sql run error:%s"%(e,)
finally:
  cursor.close()
  
cursor2=db.cursor()  
select_sql="select * from test where test.id = %s"
try:
  cursor2.execute(select_sql,(1000,))
  row = cursor2.fetchone()
  return row
except Exception as e:
  db.rollback()
  return "select sql run error:%s"%(e,)
finally:
  cursor2.close()

db.close()
return "run ok"

> 字段id是int类型，在sql字符串中任何类型都可以用%s占位，但是在execute的参数中，一定要保证传入的类型与字段类型一致

防止SQL注入
-------

对于上述的例子： insert\_sql = "INSERT INTO test(id,text,time) VALUES (%s,%s,NOW())" 可以通过python的语法动态构成sql命令： insert\_sql = "INSERT INTO test(id,text,time) VALUES (%s,%s,NOW())"%(id\_value,text\_value) id\_value和text\_value为用户传入的内容 使用python可能会出现注入风险，当然可以自行检查传入的内容是否符合要求 建议直接使用pymysql的execute方法，在传入insert_sql字符串后第二个参数传入需要替换的参数，pymysql会自行对特殊符号进行转义处理，范例见上