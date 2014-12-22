#Hive 优化

[TOC]

使用`EXPLAIN`命令查看Hive是如何处理任务的。
添加 EXPLAIN 以后，会输出==抽象语法树==。
```sql
hive> EXPLAIN SELECT SUM(number) FROM onecol;
```

可以使用`EXPLAIN EXTENDED`来输出更详细的信息。

---

Hive job 是由很多的stages组成的，越复杂的job所需要的stages就越多。

A stage could be a ==MapReduce job==, a ==sampling stage==, a ==merge stage==, a ==limit stage==, or a stage for some other task Hive needs to do.

>默认情况下，Hive是`串行`执行stage的。可以配置并行执行。


##Limit 优化

在查询语句后面添加`limit`是常用的。但是，这只是过滤返回结果，整个查询还是完整执行了。

完整的查询是很浪费的，可以在测试的时候使用`limit`进行==抽样查询==（enable sampling of source data for use）。

配置`hive.limit.optimize.enable`属性，设置为true
```xml
<property>
 <name>hive.limit.optimize.enable</name>
 <value>true</value>
 <description>Whether to enable to optimization to
 try a smaller subset of data for simple LIMIT first. </description>
</property>
```
当将limit属性设置为true以后，就可以通过下面两个参数来控制操作了：

 1. `hive.limit.row.max.size`最大的行数
 2. `hive.limit.optimize.limit.file`最大的文件数

```xml
<property>
 <name>hive.limit.row.max.size</name>
 <value>100000</value>
 <description>When trying a smaller subset of data for simple LIMIT,
 how much size we need to guarantee each row to have at least.
 </description>
</property>

<property>
 <name>hive.limit.optimize.limit.file</name>
 <value>10</value>
 <description>When trying a smaller subset of data for simple LIMIT,
 maximum number of files we can sample. </description>
</property>
```

>注意：
这个方法有个`缺陷`，可能有一些需要用到的数据未参加计算，导致结果不准确。


##Join 优化

Just remind yourself that it’s important to know which table is the largest and

 1. put the largest table `last` in the JOIN clause
 2. use the `/* streamtable(table_name) */` directive.

###待补充

##Local 本地模式
Hive操作==小数据集==的时候，使用本地模式会减少处理时间。

设置`hive.exec.mode.local.auto`为true。

临时使用（只针对当前的Hive session）
```sql
hive > set hive.exec.mode.local.auto=true;
```

要一直开启本地模式，可以配置$HIVE_HOME/conf/hive-site.xml
```xml
<property>
 <name>hive.exec.mode.local.auto</name>
 <value>true</value>
 <description>
 Let hive determine whether to run in local mode automatically
 </description>
</property>
```


##Parallel Execution 并行执行
如果一些stages并不相互依赖，就可以并行执行，提高执行效率。

开启并行处理，设置参数`hive.exec.parallel`为true

```xml
<property>
 <name>hive.exec.parallel</name>
 <value>true</value>
 <description>Whether to execute jobs in parallel</description>
</property>
```

##Strict Mode 严格模式

避免执行一些未优化的语句。

配置`hive.mapred.mode`为strict即可。
```sql
hive > set hive.mapred.mode = strict
```

设置为strict以后，可以针对下面三个类型的查询做限制：

 1. 对于==分区表==，如果没有在where语句中对分区进行过滤（未限制范围），就不予执行。即，阻止扫描一个表所有分区的操作。
比如：
```sql
hive> SELECT DISTINCT(planner_id) FROM fracture_ins 
 > WHERE planner_id=5;
FAILED: Error in semantic analysis: No Partition Predicate Found for Alias "fracture_ins" Table "fracture_ins"
```
需要指定分区才能正确执行：
```sql
hive> SELECT DISTINCT(planner_id) FROM fracture_ins
 > WHERE planner_id=5 AND hit_date=20120101;
... normal results ...
```

 2. 有==order by==语句的查询，必须添加==limit==。
 因为ORDER BY会将所有的结果发送给一个单独的reducer来进行ordering操作，强制使用limit会避免reducer执行太长的时间。
比如：
```sql
hive> SELECT * FROM fracture_ins WHERE hit_date>2012 ORDER BY planner_id;
FAILED: Error in semantic analysis: line 1: 56 In strict mode,
limit must be specified if ORDER BY is present planner_id
```
To issue this query, add a LIMIT clause:
```sql
hive> SELECT * FROM fracture_ins WHERE hit_date>2012 ORDER BY planner_id
 > LIMIT 100000;
... normal results ...
```

 3. 避免 笛卡尔积。
 Hive本身并不会对join进行优化（不同于一般的关系数据库）.
 在使用Join的时候，必须使用`ON`来执行条件，不能用**where**来代替。
```sql
hive> SELECT * FROM fracture_act JOIN fracture_ads
 > WHERE fracture_act. planner_id = fracture_ads. planner_id;
FAILED: Error in semantic analysis: In strict mode, cartesian product is not allowed. If you really want to perform the operation,
+set hive. mapred. mode=nonstrict+
```
正确的执行：
```sql
hive> SELECT * FROM fracture_act JOIN fracture_ads
 > ON (fracture_act. planner_id = fracture_ads. planner_id);
... normal results ...
```

##控制Mapper和Reducer的数量

要确定最优的Mapper和Reducer个数，依赖于许多变量，比如输入的大小，对数据的操作等等。

 - **Hive根据输入的大小来分配Reducer的数量**。Hive is determining the number of reducers from the input size.
可以使用`dfs -count`来计算指定目录下文件的大小。（类似 Linux 的`du -s`）
e.g.
```bash
 $ hadoop dfs -count /user/media6/fracture/ins/* | tail -4
 1 8 2614608737 hdfs://.../user/media6/fracture/ins/hit_date=20120118
 1 7 2742992546 hdfs://.../user/media6/fracture/ins/hit_date=20120119
 1 17 2656878252 hdfs://.../user/media6/fracture/ins/hit_date=20120120
 1 2 362657644 hdfs://.../user/media6/fracture/ins/hit_date=20120121
```
默认情况下，`hive.exec.reducers.bytes.per.reducer`的大小是==1GB==
可以改变Hive的==Reducer阈值==。如下，设置为750M (原始单位？？)
```sql
hive> set hive.exec.reducers.bytes.per.reducer=750000000;
```
 - 有时候==Map阶段==会在处理过程中产生比 **输入数据** 多很多数据，这将导致根据 输入数据 生成的reducer偏少、每个reducer处理过多的数据。
一个较快捷的处理方式是，将reducer的数量设置为指定的值（setting the number of reducers to a fixed size, rather than allowing Hive to calculate the value. ）
通过调整`mapred.reduce.tasks`参数，在不同的数值下对集群进行测试，找到最合适的数目。
>测试调整的时候，需要考虑Hadoop的启动和调度时间，这对于small job影响很大。

 - 一个Hadoop集群中，Mapper和Reducer的坑位（slots）数量是有限的。如果有个一个很大的任务，占用过多的资源，别的任务就无法运行。
可以通过设置`hive.exec.reducers.max`属性来控制最大的reducer数量。
一个建议的大小是：
```
(Total Cluster Reduce Slots * 1.5) / (avg number of queries running)
```
其中，The 1.5 multiplier is a 容差系数（fudge factor） to prevent underutilization of the cluster.


##JVM 重用

 - 针对单个Job中有 ==多个==、==执行时间短==的task的情况，重用JVM可以大大提升效率。
 - 但是对于Job中有 ==单个task运行时间较长== 的情况，重用JVM 会使Job占用的JVM无法释放，不能被别的Job使用，影响使用效率。

The default configuration of Hadoop will typically launch map or reduce tasks in a `forked JVM`. 

JVM的启动会有很大的开销，特别是对于有多个task的job来说，开销很大。

重用系统可以让一个JVM实例在同一个Job中重用同一个JVM多次（Reuse allows a JVM instance to be reused up to N times for the same job）

可以在Hadoop配置文件 `mapredsite.xml`配置JVM的重用：
>How many tasks to run per jvm. 
If set to -1, there is no limit.
```xml
<property>
	<name>mapred.job.reuse.jvm.num.tasks</name>
 	<value>10</value>
 	<description>How many tasks to run per jvm. If set to -1, there is no limit.</description>
</property>
```

##添加索引 Indexes
Indexes 在 ==GROUP BY query==中能加快运算速度。

>Hive contains an implementation of `bitmap indexes` since` v0.8.0`. 

用到 bitmap indexes 的主要场景是，==指定的列==有==相对较少的值==（there are comparatively few values for a given column）

###待补充

##动态分区优化 Dynamic Partition Tuning
`Dynamic Partition Inserts`是一个很有用的特性。但是，如果 分区表 中 分区的数目 太多，这一操作会产生大量的输出处理。对于Hadoop来说，产生少量文件，
###待补充

##推测执行 Speculative Execution

Speculative Execution 是Hadoop的特性。

什么是推测执行？
推测执行(Speculative Execution)是指在分布式集群环境下，因为程序BUG，负载不均衡或者资源分布不均等原因，造成同一个job的多个task运行速度不一致，有的task运行速度明显慢于其他task（比如：一个job的某个task进度只有10%，而其他所有task已经运行完毕），则这些task拖慢了作业的整体执行进度。为了避免这种情况发生，**Hadoop会为该task启动备份任务，让该speculative task与原始task同时处理一份数据，哪个先运行完，则将谁的结果作为最终结果。**

推测执行优化机制采用了典型的以空间换时间的优化策略，它同时启动多个相同task（备份任务）处理相同的数据块，哪个完成的早，则采用哪个task的结果，这样可防止拖后腿Task任务出现，进而提高作业计算速度，但是，这样却会占用更多的资源，**在集群资源紧缺的情况下，设计合理的推测执行机制可在多用少量资源情况下，减少大作业的计算时间。**

>参考：http://dongxicheng.org/mapreduce-nextgen/yarnmrv2-mrappmaster-speculative-execution/

配置在 $HADOOP_HOME/conf/==mapredsite.xml==

```xml
<property>
 	<name>mapred.map.tasks.speculative.execution</name>
 	<value>true</value>
 	<description>If true, then multiple instances of some map tasks may be executed in parallel. </description>
</property>
<property>
 	<name>mapred.reduce.tasks.speculative.execution</name>
 	<value>true</value>
 	<description>If true, then multiple instances of some reduce tasks may be executed in parallel. </description>
</property>
```
Hive有单独的配置
```xml
<property>
 	<name>hive.mapred.reduce.tasks.speculative.execution</name>
 	<value>true</value>
 	<description>Whether speculative execution for reducers should be turned on. </description>
</property>
```
关于推测执行，难以给出具体的建议。

如果用户因为 ==输入数据量很大== 而需要执行很长时间的map或者reduce的tasks，那么启动推测执行造成的浪费是非常巨大的。
However, if you have long-running map or reduce tasks due to large amounts of input, the waste could be significant.


##在一个MapReduce中执行多个Group By
```xml
<property>
	<name>hive.multigroupby.singlemr</name>
	<value>false</value>
	<description>Whether to optimize multi group by query to generate single M/R job plan. If the multi group by query has common group by keys, it will be
optimized to generate single M/R job. </description>
</property>
```













