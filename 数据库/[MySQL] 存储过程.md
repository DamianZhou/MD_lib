#MySQL 存储过程

[TOC]


- - -
存储过程如同一门程序设计语言，同样包含了数据类型、流程控制、输入和输出和它自己的函数库。

##基本语法

###1.创建存储过程
```sql
CREATE PROCEDURE存储过程名 (参数列表)
	BEGIN
         SQL语句代码块
	END
```
说明：

 1. 由括号包围的参数列**必须总是存在**。如果没有参数，也该使用一个**空参数列()**。
 2. 每个参数默认都是一个`IN参数`。要指定为其它参数，可在参数名之前使用关键词 `OUT`或`INOUT`


在mysql客户端定义存储过程的时候使用`delimiter命令`来把语句定界符从`;`变为`//`。

当使用delimiter命令时，你应该避免使用反斜杠(‘"’)字符，因为那是MySQL的转义字符。
e.g.
```mysql
mysql> delimiter //
mysql> CREATE PROCEDURE simpleproc (OUT param1 INT)
    -> BEGIN
    ->   SELECT COUNT(*) INTO param1 FROM t;
    -> END
    -> //
Query OK, 0 rows affected (0.00 sec)
```

###2.调用存储过程

```sql
CALL 存储过程名(参数列表)
```

 - CALL语句调用一个先前用`CREATE PROCEDURE`创建的程序。
 - CALL语句可以用声明为`OUT`或的`INOUT`参数的参数给它的调用者传回值。
 - 存储过程名称后面`必须加括号`，哪怕该存储过程没有参数传递

###3.修改存储过程
```sql
ALTER PROCEDURE 存储过程名SQL语句代码块
```
这个语句可以被用来改变一个存储程序的特征。

###4.删除存储过程

```sql
DROP PROCEDURE  IF  EXISTS 存储过程名
```

 - **不能在一个存储过程中删除另一个存储过程**，只能调用另一个存储过程

###5.其他常用命令
```sql
show procedure status
```
显示数据库中所有存储的存储过程基本信息，包括所属数据库，存储过程名称，创建时间等
```sql
show create procedure sp_name
```
显示某一个mysql存储过程的详细信息

- - -

##参考链接
http://blog.csdn.net/myron_sqh/article/details/13633239