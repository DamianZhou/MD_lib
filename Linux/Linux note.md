#Linux 学习笔记

[TOC]


## Linux 常用小功能

###获取参数的长度

```bash
# 定义输入参数的长度
var_length=`echo $1|wc -L`
```

`wc`命令

### 获取字符串指定位置的字符
```bash
# 获取参数第5位的字符，用于判断 格式
b5=`echo $1|cut -b 5`
```
`cut`命令

###读取文件
```bash
#读取本路径下面，config.txt文件的第二行数据 
config_dir=`sed -n -e'2p' config.txt`
```
`sed`命令

### 年份日期处理
`awk`命令 相当于 split 命令，选取分隔以后的各个片段。

```bash
#截取年变量
year=`echo $1|awk -F "-" '{print $1}'`
#截取月变量
month=`echo $1|awk -F "-" '{print $2}'`
#截取天变量
day=`echo $1|awk -F "-" '{print $3}'`

#修改参数的格式
a=$year$month$day

#获取本月的最后一天
last_day=`cal $month $year|xargs|awk '{print $NF}'`

#拼本月的最后一天日期
last_date=$year$month$last_day

#定义下个月的1号日期  e.g.2014-12-01
next_month_day=`date -d"$last_date 1days" "+%Y-%m-%d"`

#下个月份
month1=`echo $next_month_day|awk -F "-" '{print $2}'`
#下个月的年份
year1=`echo $next_month_day|awk -F "-" '{print $1}'`

#定义下个月的天数
next_day_num=`cal $month1 $year1|xargs|awk '{print $NF}'`
```

















