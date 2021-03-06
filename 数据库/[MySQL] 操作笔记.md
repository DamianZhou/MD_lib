#MySQL操作笔记

[TOC]


- - -
>插播知识：MySQL语句规范
 1. 关键字 和 函数名称 全部大写
 2. 数据库名称、表名称、字段名称全部小写
 3. SQL语句 必须以 分号`;`结尾

##基本操作
[Windows]启动/关闭MySQL
```cmd
net start mysql
net stop  mysql
```
[Linux]启动/关闭MySQL
```bash
service mysqld start
service mysqld stop
```
连接MySQL
```cmd
mysql -h地址 -P端口 -u用户名 -p密码
```

 - 参数说明：
|参数|备注|
|---|---|
|`-h`,--host = name     | 服务器IP地址
|`-P`,--port=#          | 服务器端口
|`-u`,--user=name       | 用户名
|`-p`,--password[=name] | 密码
|`-D`,--database = name | 打开指定数据库
|`--delimiter` = name   | 指定分隔符
|`--prompt`=name        | 设置提示符
|`-V`,--version         | 输出版本信息

修改MySQL的提示符("mysql>"部分)

 1. 连接客户端的时候通过参数指定
```
mysql -uroot -p --prompt \h
```
`\h`表示服务器名称
 
 2. 链接客户端以后，通过prompt命令修改
```
mysql>prompt myName
mysql>prompt \u@\h (\d)>
```
以下是提示符可以添加的参数：
|参数|备注|
|---|---|
|`\D` |完整的日期
|`\d` |当前数据库
|`\h` |服务器名称
|`\u` |当前用户



退出MySQL
```bash
mysql> exit
mysql> quit
mysql> \q
```

跳过权限验证登录MySQL 
```cmd
mysqld --skip-grant-tables
```
修改root密码，密码加密函数password()
```cmd
update mysql.user set password=password('root');
```
显示哪些线程正在运行
```sql
SHOW PROCESSLIST 
```
显示变量信息
```sql
SHOW VARIABLES
```
显示当前MySQL 版本、用户、时间、当前数据库

```sql
mysql>SELECT VERSION();
mysql>SELECT USER();
mysql>SELECT NOW();
mysql>SELECT DATABASE();
```






## 数据库操作
查看当前数据库
```sql
    select database();
```
显示当前时间、用户名、数据库版本
```sql
    select now(), user(), version();
```
创建库
```sql
    create database [ if not exists ] 数据库名 数据库选项
```
数据库选项：
```sql
    CHARACTER SET charset_name
    COLLATE collation_name
```
查看已有库,可以通过`like`筛选合适的数据库
```sql
    show databases [ like 'pattern']
```
查看当前库信息
```sql
    show create database 数据库名
```
修改库的选项信息
```sql
    alter database 库名 选项信息
```
删除库
```sql
    drop database[ if exists] 数据库名
```        
同时删除该数据库相关的目录及其目录内容

## 表的操作

###创建表
```sql
create [temporary] tablename [ if not exists] [库名.]表名 ( 
	表的结构定义 
 )[ 表选项]
```

 - e.g.
```sql
CREATE TABLE IF NOT EXISTS tb1(
username VARCHAR(20),
age TINYINT UNSIGNED,
salary FLOAT(8,2) UNSIGNED
);
```
从别的表获取数据创建新的表，直接赋值 brand_name：
```sql
CREATE TABLE IF NOT EXISTS table_goods_brand(
	brand_id smallint unsigned primary key auto_increment,
	brand_name varchar(40) not null
)SELECT brand_name from table_goods group by brand_name;
```

对于==字段==的定义：
```
字段名 数据类型 [NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE [KEY] | [PRIMARY] KEY] [COMMENT 'string']
```

==表选项==:
 - 字符集
        CHARSET = charset_name
	如果表没有设定，则使用数据库字符集
 - 存储引擎
        ENGINE = engine_name    
    表在管理数据时采用的不同的数据结构，结构不同会导致处理方式、提供的特性操作等不同
        
 >`常见的引擎`：InnoDB MyISAM Memory/Heap BDB Merge Example CSV MaxDB Archive
  
 不同的引擎在保存表的结构和数据时采用不同的方式
        
    - MyISAM表文件含义：.frm表定义，.MYD表数据，.MYI表索引
    - InnoDB表文件含义：.frm表定义，表空间数据和日志文件

 ```sql
	SHOW ENGINES -- 显示存储引擎的状态信息
    SHOW ENGINE 引擎名 {LOGS|STATUS} -- 显示存储引擎的日志或状态信息
 ```
 - 数据文件目录
        DATA DIRECTORY = '目录'
 - 索引文件目录
        INDEX DIRECTORY = '目录'
 - 表注释
        COMMENT = 'string'
 - 分区选项
        PARTITION BY ... (详细见手册)


###查看表
```sql
SHOW TABLES[ LIKE 'pattern']
SHOW TABLES FROM 表名
```
查看表结构
```sql
SHOW CREATE TABLE 表名    （信息更详细）
DESC 表名 / DESCRIBE 表名 / EXPLAIN 表名 / SHOW COLUMNS FROM 表名 [LIKE 'PATTERN']
SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern']
```
###修改表
修改表本身的选项
```sql
ALTER TABLE 表名 表的选项
```
 - e.g.
 ```sql
ALTER TABLE tablename ENGINE=MYISAM;
 ```

对表进行重命名
```sql
RENAME TABLE 原表名 TO 新表名
RENAME TABLE 原表名 TO 库名.表名    （可将表移动到另一个数据库）
```
修改表的字段机构
```sql
ALTER TABLE 表名 操作名
```
  - 操作名
```sql
ADD [COLUMN] 字段名         -- 增加字段
ADD [COLUMN] AFTER 字段名   -- 表示增加在该字段名后面
ADD [COLUMN] FIRST         -- 表示增加在第一个
ADD PRIMARY KEY(字段名)     -- 创建主键
ADD UNIQUE [索引名] (字段名) -- 创建唯一索引
ADD INDEX [索引名] (字段名)  -- 创建普通索引
DROP[ COLUMN] 字段名        -- 删除字段
MODIFY[COLUMN] 字段名 字段属性  -- 支持对字段属性进行修改，不能修改字段名(所有原有属性也需写上)
CHANGE[COLUMN] 原字段名 新字段名 字段属性  -- 支持对字段名修改
DROP PRIMARY KEY           -- 删除主键(删除主键前需删除其AUTO_INCREMENT属性)
DROP INDEX 索引名           -- 删除索引
DROP FOREIGN KEY 外键       -- 删除外键
```

####添加索引 Index

 1. 添加PRIMARY KEY（主键索引）
	```sql
    ALTER TABLE `table_name ADD` PRIMARY KEY ( `column` )
	```

 2. 添加UNIQUE(唯一索引)
	```sql
    ALTER TABLE `table_name` ADD UNIQUE (`column`)
	```

 3. 添加INDEX(普通索引)
	```sql
    ALTER TABLE `table_namesqlsqlsqlsql` ADD INDEX index_name ( `column` )
	```
 4. 添加FULLTEXT(全文索引) 
    ```sql
    ALTER TABLE `table_name` ADD FULLTEXT ( `column`) 
    ```
 5. 添加多列索引
    ```sql
    ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3` )
    ```

###清空&删除表
清空表数据
```sql
TRUNCATE [TABLE] 表名
```
删除表数据
```sql
DROP TABLE[ IF EXISTS] 表名 ...
```


###复制表结构和数据
复制表结构
```sql
CREATE TABLE 表名 LIKE 要复制的表名
```
复制表结构和数据
```sql
CREATE TABLE 表名 [AS] SELECT * FROM 要复制的表名
```
###检查表是否有错误
```sql
CHECK TABLE tbl_name [, tbl_name] ... [option] ...
```
###优化表
```sql
OPTIMIZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
```
###修复表
```sql
REPAIR [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ... [QUICK] [EXTENDED] [USE_FRM]
```
###分析表
```sql
ANALYZE [LOCAL | NO_WRITE_TO_BINLOG] TABLE tbl_name [, tbl_name] ...
```

##数据操作
###增
```sql
INSERT [INTO] 表名 [(字段列表)] VALUES (值列表)[, (值列表), ...]
```
 - 如果要插入的值列表包含所有字段并且顺序一致，则可以省略字段列表（若省略列名，则对所有列赋值。）
 - 可同时插入多条数据记录！
 - 可以用==表达式==来赋值
 - 若删除自动编号的记录，然后再插入新数据，新数据会从最大的编号后开始插入。

 以下插入可以使用==子查询==。
```sql
INSERT [ INTO ] tbl_name SET col_name={expr | DEFAULT},...
```
   - e.g.
```sql
insert users set username='Damian' , passward='123'
```

 将查询结果插入数据表
```sql
INSERT [ INTO ] tbl_name [ ( col_name,... ) ] SELECT ...
```
   - e.g.
```sql
insert test (username) select username from users where age >20;
```

###查
```sql
SELECT 字段列表 FROM 表名[ 其他子句]

SELECT [all|distinct] select_expr[ ,select_expr...][
FROM table_references
[WHERE where_condition]
[GROUP BY { col_name | position } [ ASC | DESC ],... ]
[HAVING where_condition] 
[ORDER BY {col_name | expr | position } [ ASC | DESC ],... ]
[LIMIT { [ offset, ] row_count | row_count OFFSET offset }]
]
```
 a. select_expr
  - 可以用 * 表示所有字段。
  ```sql
  select * from tb;
  ```
  - 可以使用==表达式==（计算公式、函数调用、字段也是个表达式）
  ```sql
  select stu, 29+25, now() from tb;
  ```
  - 可以为每个列使用别名。适用于简化列标识，避免多个列标识符重复。
  使用 `as `关键字，也可省略 as.
  ```sql
  select stu+10 as add10 from tb;
  ```

b. from 子句

 - 用于标识查询来源。
 - 可以为表起别名。使用as关键字。
    ```sql
    select * from tb1 as tt, tb2 as bb;
    ```
 - from子句后，可以同时出现多个表。
   多个表会横向叠加到一起，而数据会形成一个`笛卡尔积`。
    ```sql
    select * from tb1, tb2;
    ```

c. where 子句
 - 从from获得的数据源中进行筛选。
 - 整型1表示真，0表示假。
 - 表达式由运算符和运算数组成。
    - 运算数：变量（字段）、值、函数返回值
    - 运算符：
     
        =, <=>, <>, !=, <=, <, >=, >, !, &&, ||, 
        in (not) null, (not) like, (not) in, ==(not) between and==, is (not), and, or, not, xor
        
        is/is not 加上ture/false/unknown，检验某个值的真假
        <=>与<>功能相同，<=>可用于null比较


d. group by 子句, 分组子句
 - group by 字段/别名 [排序方式]
   分组后会进行排序。
   升序：==ASC==，降序：==DESC==

e. having 子句，条件子句
   - 与 where 功能、用法相同，执行时机不同。
    |where|having|
    |---|---| 
    |where 在开始时执行检测数据，对==原数据==进行过滤。|having 对==筛选出的结果==再次进行过滤。|
    |where 字段必须是数据表存在的。|having 字段必须是查询出来的|
    |where 不可以使用字段的别名。因为执行WHERE代码时，可能尚未确定列值。|having 可以使用字段的别名。|
    |where 不可以使用合计函数。|一般需用合计函数才会用 having|
   
   - SQL标准要求HAVING必须引用==GROUP BY子句中的列==或用于==合计函数中的列==。

f. order by 子句，排序子句
 - 
 ```sql
 order by 排序字段/别名 排序方式 [,排序字段/别名 排序方式]...
 ```
    升序：ASC，降序：DESC
    支持多个字段的排序。

g. limit 子句，限制结果数量子句
  - 仅对处理好的结果进行数量限制。
  将处理好的结果的看作是一个集合，按照记录出现的顺序，索引从0开始。
  ```sql
     select ... limit 起始位置, 获取条数
  ```
    省略第一个参数，表示从索引0开始。limit 获取条数

h. distinct, all 选项
 - distinct 去除重复记录
 - 默认为 all, 全部记录


####Union
将多个select查询的结果组合成一个结果集合。
```sql
SELECT ... UNION [ALL|DISTINCT] SELECT ...
```    
==默认 DISTINCT 方式==，即所有返回的行都是唯一的

建议，对每个SELECT查询加上小括号包裹。

- ORDER BY 排序时，需加上 LIMIT 进行结合。
- 需要各select查询的字段数量一样。
- 每个select查询的字段列表(数量、类型)应==一致==，因为结果中的字段名以第一条select语句为准。

####子查询
子查询需用括号包裹。
 - from型
    from后要求是一个表，必须给子查询结果取个别名。
    - 简化每个查询内的条件。
    - from型需将结果生成一个临时表格，可用以原表的锁定的释放。
    - 子查询返回一个表
表型子查询。
```sql
select * from (select * from tb where id>0) as subfrom where id>1;
```
 - where型
    - 子查询返回一个值，标量子查询。
    - 不需要给子查询取别名。
    - where子查询内的表，不能直接用以更新。
```sql
    select * from tb where money = (select max(money) from tb);
```
   - 列子查询
        如果子查询结果返回的是一列。
        使用 in 或 not in 完成查询
        exists 和 not exists 条件
        
        ==如果子查询返回数据，则返回1或0。==常用于判断条件。
        ```sql
        select column1 from t1 where exists (select * from t2);
        ```
   - 行子查询
        查询条件是一个行。
        ```sql
        select * from t1 where (id, gender) in (select id, gender from t2);
        ```
        行构造符：(col1, col2, ...) 或 ROW(col1, col2, ...)
        行构造符通常用于与对能返回两个或两个以上列的子查询进行比较。

     - 特殊运算符
    != all()     相当于 not in
    = some()     相当于 in。any 是 some 的别名
    != some()    不等同于 not in，不等于其中某一个。
    all, some    可以配合其他运算符一起使用。


####JOIN
将多个表的字段进行连接，可以指定连接条件。

 - 内连接(inner join)
   默认就是内连接，可省略inner。
    只有数据存在时才能发送连接。即连接结果不能出现空行。
    
    on 表示连接条件。其条件表达式与where类似。也可以省略条件（表示条件永远为真）
    也可用where表示连接条件。
    还有 using, 但需字段名相同。 using(字段名)

    - 交叉连接 `cross join`
        即，没有条件的内连接。
        ```sql
        select * from tb1 cross join tb2;
        ```


###删
```sql
DELETE FROM 表名[ 删除条件子句]
```
没有条件子句，则会删除全部
###改
```sql
UPDATE 表名 SET 字段名=新值[, 字段名=新值] [更新条件]
```



##字符集编码
 - MySQL、数据库、表、字段均可设置编码
 - 数据编码与客户端编码不需一致

查看所有字符集编码项
```sql
SHOW VARIABLES LIKE 'character_set_%'
    character_set_client        客户端向服务器发送数据时使用的编码
    character_set_results       服务器端将结果返回给客户端所使用的编码
    character_set_connection    连接层编码
```
指定编码值
```sql
SET 变量名 = 变量值
```
 - e.g.
```SQL
set character_set_client = gbk;
set character_set_results = gbk;
set character_set_connection = gbk;
SET NAMES GBK;    -- 相当于完成以上三个设置
```

`校对集`,校对集用以排序

查看所有字符集
```sql
SHOW CHARACTER SET [LIKE 'pattern'] / SHOW CHARSET [LIKE 'pattern']
```
查看所有校对集
```sql
SHOW COLLATION [LIKE 'pattern']        
    charset 字符集编码        设置字符集编码
    collate 校对集编码        设置校对集编码
```    

##数据类型（列类型）
1. 数值类型
 a. 整型
   |类型|            字节|        范围（有符号位）|
   |---|---|
   | tinyint|        1字节|    -128 ~ 127        无符号位：0 ~ 255
   | smallint|    2字节   | -32768 ~ 32767
   | mediumint|    3字节   | -8388608 ~ 8388607
   | int       |     4字节|
   | bigint     |   8字节|
   | int(M) |   M表示总位数|
   
    - 默认存在符号位，unsigned 属性修改
    - 显示宽度，如果某个数不够定义字段时设置的位数，则前面以0补填，zerofill 属性修改
        例：int(5)    插入一个数'123'，补填后为'00123'
    - 在满足要求的情况下，越小越好。
    - 1表示bool值真，0表示bool值假。==MySQL没有布尔类型，通过整型0和1表示。==常用tinyint(1)表示布尔型。

 b. 浮点型
    |类型             |   字节 |       范围|
    |----|---|---|
    |float(单精度)   |     4字节|
    |double(双精度) |   8字节|
    浮点型既支持符号位 unsigned 属性，也支持显示宽度 zerofill 属性。
    >不同于整型，浮点型前后均会补填0.
    
    定义浮点型时，需指定总位数和小数位数。
        float(M, D)        double(M, D)
        M表示总位数，D表示小数位数。
        M和D的大小会决定浮点数的范围。不同于整型的固定范围。
        M既表示总位数（不包括小数点和正负号），也表示显示宽度（所有显示符号均包括）。
    支持科学计数法表示。
    浮点数表示近似值。

 c. 定点数
 |类型             |描述|
    |----|---|
   | decimal|    -- 可变长度
    |decimal(M, D)|    M也表示总位数，D表示小数位数。
    保存一个精确的数值，不会发生数据的改变，不同于浮点数的四舍五入。
    将浮点数转换为==字符串==来保存，每9位数字保存为4个字节。

2. 字符串类型
  a.char, varchar 
    
   - char    定长字符串，速度快，但浪费空间
   - varchar    变长字符串，速度慢，但节省空间
   
    M表示能存储的最大长度，此长度是字符数，非字节数。
    不同的编码，所占用的空间不同。
   - char,最多255个字符，与编码无关。
   - varchar,最多65535字符，与编码有关。
    一条有效记录最大不能超过65535个字节。
        utf8 最大为21844个字符，gbk 最大为32766个字符，latin1 最大为65532个字符
   - varchar 是变长的，需要利用存储空间保存 varchar 的长度，如果数据小于255个字节，则采用一个字节来保存长度，反之需要两个字节来保存。
   - varchar 的最大有效长度由最大行大小和使用的字符集确定。
    最大有效长度是65532字节，因为在varchar存字符串时，第一个字节是空的，不存在任何数据，然后还需两个字节来存放字符串的长度，所以有效长度是64432-1-2=65532字节。
    >    例：若一个表定义为 CREATE TABLE tb(c1 int, c2 char(30), c3 varchar(N)) charset=utf8; 问N的最大值是多少？ 
    答：(65535-1-2-4-30*3)/3

  b. blob, text 
    blob 二进制字符串（字节字符串）
        tinyblob, blob, mediumblob, longblob
    text 非二进制字符串（字符字符串）
        tinytext, text, mediumtext, longtext
    text 在定义时，不需要定义长度，也不会计算总长度。
    text 类型在定义时，不可给default值

  c. binary, varbinary 
    类似于char和varchar，用于保存二进制字符串，也就是保存字节字符串而非字符字符串。
    char, varchar, text 对应 binary, varbinary, blob.

3. 日期时间类型
    一般用==整型==保存时间戳，因为PHP可以很方便的将时间戳进行格式化。
    |名称|大小|描述|范围|
    |----|----|----|----|
    |datetime    |8字节    |日期及时间       | 1000-01-01 00:00:00 到 9999-12-31 23:59:59
    |date        |3字节    |日期          |  1000-01-01 到 9999-12-31
    |timestamp   | 4字节   | 时间戳        |19700101000000 到 2038-01-19 03:14:07
    |time        |3字节    |时间          |  -838:59:59 到 838:59:59
    |year        |1字节    |年份          |  1901 - 2155

    日期可以有多种格式：
    datetime     “YYYY-MM-DD hh:mm:ss”
	timestamp    “YY-MM-DD hh:mm:ss”,“YYYYMMDDhhmmss”

4. 枚举和集合
  a.枚举(enum)
		enum(val1, val2, val3...)
    
    - 在已知的值中进行单选。最大数量为65535.
    - 枚举值在保存时，以2个字节的整型(smallint)保存。每个枚举值，按保存的位置顺序，从1开始逐一递增。
    - 表现为字符串类型，存储却是整型。
    - NULL值的索引是NULL。
    - 空字符串错误值的索引值是0。

  b.集合（set）
		set(val1, val2, val3...)
    e.g.
    	create table tab ( gender set('男', '女', '无') );
    	insert into tab values ('男, 女');
    最多可以有64个不同的成员。以bigint存储，共8个字节。采取位运算的形式。
    当创建表时，SET成员值的尾部空格将自动被删除。

##列属性（列约束）
1. 主键
    - 能唯一标识记录的字段，可以作为主键。
    - 一个表只能有一个主键。
    - 主键具有唯一性。
    - 声明字段时，用 `primary key` 标识。
        也可以在字段列表之后声明
        例：
        	create table tab ( id int, stu varchar(10), primary key (id));
    - 主键字段的值不能为null。
    - 主键可以由多个字段共同组成。此时需要在字段列表后声明的方法。
        例：create table tab ( id int, stu varchar(10), age int, primary key (stu, age));

2. unique 唯一索引（唯一约束）
    使得某字段的值也不能重复。
    
3. null 约束
    null不是数据类型，是列的一个属性。
    表示当前列是否可以为null，表示什么都没有。
    null, 允许为空。默认。
    not null, 不允许为空。
    ```sql
    insert into tab values (null, 'val');
    -- 此时表示将第一个字段的值设为null, 取决于该字段是否允许为null
   	```
4. default 默认值属性
    当前字段的默认值。
    
    强制使用默认值:
   ```sql
   insert into tab values (default, 'val');
   ```
   将当前时间的时间戳设为默认值:	
   ```sql
   create table tab ( add_time timestamp default current_timestamp );
   ```     
   current_date, current_time

5. auto_increment 自动增长约束
    - ==自动增长必须为索引==（主键或unique）
    - 只能存在一个字段为自动增长。
    - 默认为1开始自动增长。
  
  可以通过表属性 `auto_increment = x`进行设置，或 
  ```sql
  alter table tbl auto_increment = x;
  ```
6. comment 注释
  例：
  ```sql
  create table tab ( id int ) comment '注释内容';
  ```
7. foreign key 外键约束
  用于限制主表与从表数据完整性。
  ```sql
  alter table t1 add constraint `t1_t2_fk` foreign key (t1_id) references t2(id);
  -- 将表t1的t1_id外键关联到表t2的id字段。
  -- 每个外键都有一个名字，可以通过 constraint 指定
   ```
  存在外键的表，称之为`从表（子表）`，外键指向的表，称之为`主表（父表）`。
  作用：保持数据一致性，完整性，主要目的是控制存储在外键表（从表）中的数据。

  MySQL中，可以对InnoDB引擎使用外键约束。
  语法：
    ```sql
   foreign key (外键字段） references 主表名 (关联字段) [主表记录删除时的动作] [主表记录更新时的动作]
    ```
    此时需要检测一个从表的外键需要约束为主表的已存在的值。外键在没有关联的情况下，可以设置为null.前提是该外键列，没有not null。

    可以不指定主表记录更改或更新时的动作，那么此时主表的操作被拒绝。
    
    如果指定了 on update 或 on delete：在删除或更新时，有如下几个操作可以选择：
    1. `cascade`，级联操作。主表数据被更新（主键值更新），从表也被更新（外键值更新）。主表记录被删除，从表相关记录也被删除。
    2. `set null`，设置为null。主表数据被更新（主键值更新），从表的外键被设置为null。主表记录被删除，从表相关记录外键被设置成null。但注意，要求该外键列，没有not null属性约束。
    3. `restrict`，拒绝父表删除和更新。

    注意，外键只被InnoDB存储引擎所支持。其他引擎是不支持的。














- - -


#参考链接
http://www.cnblogs.com/shockerli/p/1000-plus-line-mysql-notes.html