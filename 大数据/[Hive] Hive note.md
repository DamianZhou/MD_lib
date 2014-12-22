#Hive Note

[TOC]



































##Debug

### 1.在Hive执行HQL的时候，备注语句中也不能有 ';' ，否则会报错

```error
FAILED: 
ParseException line 7:41 cannot recognize input near '<EOF>' '<EOF>' '<EOF>' in column specification
```

###2.sublime 中编辑 linux shell，运行报语法错误
设置 unix 的换行符，对之前已经创建的windows下的文本没有转换作用。
**需要重新创建一个新文件！！！**然后复制粘贴即可，新文件中的换行符即是unix适用的换行符。

否则，执行脚本的时候会提示 语法错误。


### 3.执行包含 HQL 的脚本报 空指针异常
报错且表格中没有数据 

```error
FAILED: NullPointerException null
```
解决：
把脚本中的文件单独抽取出来执行一遍，找出问题的位置。

这次问题，是**由于编写 hive 的 hql 语句的时候，使用了中文的逗号！** 替换掉中文的逗号即可！

###4.加载数据的时候，数值类型为NULL,字符类型正常加载
源数据有问题，设置分隔符的时候，**数值类型的值含有空格**，导致值为NULL


###5.浮点数的比较
FLOAT 和 DOUBLE 在做比较的时候，有可能会出现 select * from table where a>b 的结果中出现 a=b 的数据。

对于 0.2这个值，float可能为0.2000001，而double为可能为0.200000000001，float类型的值可能比double的值大

解决方法：

 1. 使用 `TEXTFILE文件` 格式，Hive会将字符串格式的的"0.2"根据表模式中定义的字段的类型转换为真实的数字。
 
 2. 使用 `CAST`,显示的指出 float 类型。 e.g. cast(0.2 AS FLOAT)






##经验
### 用 刘厚暖 的 token 上传资料(不必要)
Linux 的主目录
/data/home/v_hnuanliu@webank.com 

右击窗口标签，选“connect SFTP Tab”，然后用
```
sftp > put E:\aaa.txt
```
上传到 Linux 主目录



##Hive 的配置

###Hive CLI 查看可用选项
查看当前Hive环境的可用CLI选项
```bash
$ hive --help --service cli
```
> 也可直接使用`hive -h`来输出，不过会提示空参数，不推荐。

hive 0.13.1的信息如下：
```xml
usage: hive
 -d,--define <key=value>          Variable subsitution to apply to hive
                                  commands. e.g. -d A=B or --define A=B
    --database <databasename>     Specify the database to use
 -e <quoted-query-string>         SQL from command line
 -f <filename>                    SQL from files
 -H,--help                        Print help information
 -h <hostname>                    connecting to Hive Server on remote host
    --hiveconf <property=value>   Use value for given property
    --hivevar <key=value>         Variable subsitution to apply to hive
                                  commands. e.g. --hivevar A=B
 -i <filename>                    Initialization SQL file
 -p <port>                        connecting to Hive Server on port number
 -S,--silent                      Silent mode in interactive shell
 -v,--verbose                     Verbose mode (echo executed SQL to the
                                  console)
```


###hive的预配置
hive CLI 的 `-i` 参数，允许用户指定一个文件，在CLI启动时，会在提示符出现之前先执行这个文件。
可以预先配置 本地运行模式、执行的字段显示 等等。

e.g. 设置一个配置文件 **init_hive.hql**

```hive配置
set hive.exec.mode.local.auto=true;
set hive.cli.print.header=false;
use hduser02db;
set hive.cli.print.current.db=true;
```
在启动hive的时候调用 `-i` 来配置环境。
```启动hive
$ hive -i /home/hduser02/uprr/zhoucd/myConfig/init_hive.hql
```

###hive显示当前的数据库名称
```hive
hive> set hive.cli.print.current.db=true;
hive (financials) > USE default;
hive (default) > set hive. cli. print. current. db=false;
hive> ...
```


hive的配置很多，在hive CLI 中输入
```hive
hive > set;
...
```
会打印出命名空间 `hivevar`、 `hiveconf`、 `system`、 `env` 中所有的变量信息。

添加 `-v` 标记，还会打印出 hadoop 中所定义的属性，例如控制 HDFS 和 MapReduce 的属性。
```hive
hive > set -v;
...
```

### 获取 hive 的配置中的属性名
当只记得大概的名字，不记得完整的属性名的时候，可以使用 grep 管道来获取属性的完整属性配置。
比如，如果要获取 warehouse 的路径，可以使用下面的命令：
```Bash
$ hive -S -e "set" | grep warehouse
hive.metastore.warehouse.dir=/user/hive/warehouse
hive.warehouse.subdir.inherit.perms=false
```

###Hive 提高SQL查询速度
启动**本地模式**，即使是分布式的环境，也使用本地模式进行查询。这对于小数据的操作来说，效果很明显。
```hive
hive > set hive.exec.mode.local.auto=true;
```

### Hive 开启行转列 显示/隐藏列标题
```Hive
hive > set hive.cli.print.header=true;  // 打印列名 
hive > set hive.cli.print.row.to.vertical=true; //开启行转列功能, 前提必须开启打印列名功能 
hive >  set hive.cli.print.row.to.vertical.num=1; // 设置每行显示的列数 
```

### 阻止JOIN 操作执行 笛卡尔积
```hive
hive > set hive.mapred.mode=strict;
```

### 自动执行 map-side JOIN
在Map端执行连接过程: 针对小表，直接在内存中逐一匹配，省略reduce。
```hive
hive > set hive.auto.convert.join=true ;
```
可以设置 小表 的标准，来限定 map-site JOIN 的启动。
Here is the default definition of the property (in bytes):
```hive
hive > set hive.mapjoin.smalltable.filesize = 25000000;
```

### Hive 强制添加 分区条件
这是一个安全措施。防止select语句对所所有分区进行操作而产生一个巨大的的MapReduce任务。

>如果对分区进行查询，而WHERE中没有添加分区过滤条件，就会禁止提交这个任务。

```Hive
#开启
hive> set hive.mapred.mode = strict;
#关闭
hive> set hive.mapred.mode = nostrict;
```










##hive 调用 命令
###在Hive CLI中调用 hadoop 的 dfs 命令
在Hive CLI 中执行 hadoop 的 dfs 命令，只需要将 hadoop 命令中的关键字 ”hadoop“去掉，然后使用分号结尾即可。
```hive
hive> dfs -ls / ;
Found 3 items
drwxr-xr-x - root supergroup 0 2011-08-17 16: 27 /etl
drwxr-xr-x - edward supergroup 0 2012-01-18 15: 51 /flag
drwxrwxr-x - hadoop supergroup 0 2010-02-03 17: 50 /users
```
提示：
>这种调用方式比在 bash shell 中执行 hadoop dfs ...  的命令更加高效，因为后者每次都会启动一个新的JVM进程，而Hive会在同一个进程中执行这些命令。

### 在Hive CLI中调用Bash脚本

只需要在命令前面加上叹号(！)并且以分号（;）结尾，就可以直接执行 bash 语句。

```hive
hive> ! /bin/echo "what up dog";
"what up dog"

hive> ! pwd;
/home/me/hiveplay
```

要在 Hive Shell 中执行一个脚本文件（*.sh等）,可以使用 `source` 命令。
例如，在hive shell 中调用withqueries.hql文件。
```hive
$ cat /path/to/file/withqueries.hql
SELECT x.* FROM src x;

$ hive
hive> source /path/to/file/withqueries.hql;
...
```


###在bash中执行hive HQL语句

在linux的bash环境中，使用 `hive -e "...";`即可执行hive的hql语句。
```hive
$ hive -e "SELECT * FROM mytable LIMIT 3";
OK
name1 10
name2 20
name3 30
Time taken: 4.955 seconds
```

添加 `-S` 选项，可以开启**静默模式**，在输出的结果中去掉 "OK","Time taken"等描述性字段，只显示查询的数据结果。

Hive会将输出写到 **标准输出** 中。也可以使用重定向，将数据输出到**本地文件系统**，而不是 HDFS 上面。

```hive
$ hive -S -e "select * FROM mytable LIMIT 3" > /tmp/myquery
$ cat /tmp/myquery
name1 10
name2 20
name3 30
```


###从文件中执行 Hive 语句
使用 `-f` 标示，可以执行制定文件的一个或多个查询语句。
>按照惯例，一般把 hive 的查询文件保存为 `.q` 或者 `.hql` 后缀名的文件。

```bash
$ hive -f /path/to/file/withqueries. hql
```
##Hive小技巧——搜索技术

### 筛选搜索结果

用正则表达式配置搜索的结果
```hive
hive> SHOW DATABASES LIKE 'h.*' ;
human_resources
hive> ...
```

###查看分区 partition
```Hive
hive> SHOW PARTITIONS tablename;

#如果有多个分区条件，查询特定分区
hive> show PARTITIONS tablename PARTITION(country="USA");
```

### case-when-else
简单的映射demo
```hive
SELECT ri_page_url, ri_referrer_url,
 CASE
  WHEN ri_referrer_url is NULL or ri_referrer_url = ‘’ THEN ‘DIRECT’
  WHEN ri_referrer_url in (‘mysite1. com’ , ’ mysite2. com’ , ’ mysite3. com’ ) THEN ‘INSITE’
  ELSE ri_referrer_url
 END as ri_referrer_url_classed
FROM
 referrer_identification;
```

### 清空数据表
```Hive
hive> truncate table **；
```

### 计算日期差

`datediff(date1,date2)`返回 date1 相对于 date2 的日期差。

```hive
select
	a.lplanrtnintdt,
	datediff("2014-11-28",a.lplanrtnintdt),
	datediff("2014-11-28","2013-11-11"),
	datediff("2013-11-11","2014-11-11")
from
	rrs_qls_t_tla_lncino_base_mid a;

a.lplanrtnintdt _c1     _c2     _c3
2014-11-24      4       382     -365
2014-11-24      4       382     -365
2014-11-24      4       382     -365
Time taken: 13.85 seconds, Fetched: 3 row(s)
```


##基础知识

####基础1： ORDER BY & SORT BY 区别

|名称|区别|
|:----|:----|
|ORDER BY|全局排序，针对所有数据进行排序| 
|SORT BY|局部排序，只针对每个`reduce`中的数据进行排序|

如果语句所使用的reduce的个数多余一个，两种方式所生成的结果顺序就会不一致。

>两种方式都可以在后面添加`ASC`(default)和`DESC`来控制升序降序。

ORDER BY 可能会导致运行时间特别长，如果属性`hive.mapred.mode`设置为**strict**，那么Hive要求这样的语句必须有`LIMIT`语句进行限制。 



####基础2： 含有SORT BY 的 DISTRIBUTE BY

DISTRIBUTE BY 和 GROUP BY 一样， 是控制map的输出在reduce中如何划分。
SORT BY 是控制reducer内的数据是如何排序的。

默认情况下，MapReduce框架会根据map输入的键key计算相应的哈希值，谈后照着得到的哈希值将键值对均匀的分发到多个reducer中去。

当我们使用 `Streaming特性`以及一些状态为 `UDAF表生成函数` 的查询的时候，默认的分发会发生重叠。

**使用案例**：
如果我们需要将相同股票交易码的数据在一起处理，我们可以使用DISTRIBUTE BY 来保证具有相同股票交易码的数据会分发到一个reduce中进行处理，然后使用SORT BY 来按照我们的期望对数据进行排序。

>注意： Hive 要求 DISTRIBUTE BY 语句要写在 SORT BY 之前！

>**DISTRIBUTE BY ... SORT BY **或者其简化版本 **CLUSTER BY**，会剥夺SORT BY 的并行性，但是这样可以实现输出文件的数据是`全局排序`的！




####基础3：UNION ALL
UNION ALL可以将2个或多个表进行合并。

每个UNION的子查询都必须具有
 1. 相同的列
 2. 对应字段的类型必须一致


UNION可以用于同一个源表的合并，逻辑上相当于将一个长的WHERE语句分割成多个UNION子查询。



####基础4：视图

当查询变得很长后者很复杂的时候，可以通过使用视图将这个查询语句分割成多个小的、更可控的片段，来降低语句的复杂度。

这一思想和编程语言中 **使用函数** 和 软件设计中的 **分层设计** 的概念是一致的。

>Hive暂不支持物化视图。
当一个查询引用视图时，这个视图所定义的查询语句会和用户的查询语句组合在一起，供Hive指定查询计划。从逻辑上讲，可以认为Hive先执行视图，然后使用这个结果进行后面的查询。

e.g.

原始的语句：
```sql
FROM (
 SELECT * FROM people JOIN cart
 ON (cart. people_id=people. id) WHERE firstname='john'
) a SELECT a. lastname WHERE a. id=3;
```
创建视图：
```sql
CREATE VIEW shorter_join AS
SELECT * FROM people JOIN cart
ON (cart. people_id=people. id) WHERE firstname='john' ;
```
原始的语句可以写成：
```sql
SELECT lastname FROM shorter_join WHERE id=3;
```

#####视图可以限制 基于条件过滤的数据
通过WHERE语句定制视图，只暴露局部数据给特定用户。

####基础5： 索引
Hive提供有限的索引功能。暂时还没有提供很多功能。

####基础6：Hive的锁机制
Hive的锁机制是通过zookeeper来实现的。需要安装配置zookeeper。

>Note:
如果配合HBase，zookeeper由HBase管理，Hive无需配置。

#####查看锁
查看当前所有的锁：` SHOW LOCKS`
```sql
hive> SHOW LOCKS;
default@people_20111230 SHARED
default@places SHARED
default@places@hit_date=20111230 SHARED
```

Hive还支持部分查询（focused queries）
e.g.
places 是一个==分区表==
```sql
hive> SHOW LOCKS places EXTENDED;
default@places SHARED
...
hive> SHOW LOCKS places PARTITION (...);
default@places SHARED
...
hive> SHOW LOCKS places PARTITION (...) EXTENDED;
default@places SHARED
...
```

Hive支持两种锁：

 1. A `shared lock 共享锁` is acquired when a table is read.只读操作获得共享锁。
Multiple, concurrent shared locks are allowed.并发的共享锁是允许的。
 2. An `exclusive lock 排他锁` is required for all other operations that modify the table in some way.读以外的所有操作将获得排他锁。
 They not only freeze out other table-mutating operations, they also prevent queries by other processes.不仅阻止别的修改表的操作，还阻止其他进程的查询操作。

对于分区表，当一个==分区==获得排他锁，这个==表本身==自动获得共享锁（只读），防止在操作的过程中被别的进程进行drop等操作。

#####管理和使用锁
You can also manage locks explicitly.

假设在一个Hive session中对表people添加排他锁：
```sql
hive> LOCK TABLE people EXCLUSIVE;
```

此时，另一个 Hive session 来查询这个people表
```sql
hive> SELECT COUNT(*) FROM people;

conflicting lock present for default@people mode SHARED
FAILED: Error in acquiring locks: locks on the underlying objects
cannot be acquired. retry after some time
```
The table can be unlocked using the `UNLOCK TABLE` statement, after which queries from other sessions will work again:
```sql
hive> UNLOCK TABLE people;
```







