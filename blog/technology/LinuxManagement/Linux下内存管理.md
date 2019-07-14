
```
# 查看次要页故障和主要页故障出现的频率，MINFL显示出次要故障的数目，MAJFL表示了主要故障的数目。
$  ps -eo min_flt,maj_flt,cmd

#  只查看某一进程的故障数量统计。1 代表该进程的PID
$ ps -o min_flt,maj_flt 1

# 也可以添加其他字段进行查看
$ ps -o min_flt,maj_flt,cmd,args,uid,gid 1

```

free意味现在立刻可以用，available意味可以用，但是需要一些时间先进行释放空间。
``` 
[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1966         254        1374           2         337        1564
Swap:          2097           6        2091

```

