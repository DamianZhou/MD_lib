#HBase 简明语法
[TOC]

---

## HBase Shell

打开shell
```
$ hbase shell
HBase Shell; enter 'help<RETURN>' for list of supported commands.
Type "exit<RETURN>" to leave the HBase Shell
Version 0.92.0, r1231986, Mon Jan 16 13:16:35 UTC 2012
hbase(main):001:0>
```

创建table

>创建table的时候至少要指定一个列族。
>每个table至少要有一个列族。

```
hbase(main):001:0> create 'users', 'info'
0 row(s) in 0.1200 seconds
hbase(main):002:0>
```

列出当前数据库中所有的table
```
hbase(main):002:0> list
TABLE
users
1 row(s) in 0.0220 seconds
hbase(main):003:0>
```

查看table信息
```
hbase(main):003:0> describe 'users'
DESCRIPTION ENABLED
{ NAME => 'users', FAMILIES => [{NAME => 'info', true
BLOOMFILTER => 'NONE', REPLICATION_SCOPE => '0
', COMPRESSION => 'NONE', VERSIONS => '3', TTL
=> '2147483647', BLOCKSIZE => '65536', IN_MEMOR
Y => 'false', BLOCKCACHE => 'true'}]}
1 row(s) in 0.0330 seconds
hbase(main):004:0>
```