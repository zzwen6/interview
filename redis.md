redis

#### 1.Redis持久化存储(AOF与RDB两种模式)

RDB, 快照。是在某个时间点生成一个rdb文件，来替换上欠生成的数据文件，达到数据恢复的手段。
优点：使用单独的子进程来进行持久化，主进程不会进行任何IO操作，保证了redis的高性能。
缺点：RDB是间隔一段时行进行执行久，如何两段时间发生故障，数据会丢失。所以这种方式适用于数据要求不严谨的时候。

配置：
```
# 持久化生成的文件
dbfilename dump.rdb
# 生成目录
dir ./
#save时间，以下分别表示更改了1个key时间隔900s进行持久化存储；更改了10个key300s进行存储；更改10000个key60s进行存储。
save 900 1
save 300 10
save 60 10000

```

AOF,将格式化的指令的方式追加到操作日志文件中,文件中从中保存了所有的redis操作。如何需要恢复数据会一条条的执行aof文件的指令。

优点：可以保证数据的高完整性，如果设置追加指令时间为1s，那么最多数据丢失只能是1s内的数据； 且日志写入不完整支持用redis-check-aof进行修复； aof文件没被rewrite之前可以删除某些命令，如误操作 flushall
缺点：aof生成比rdb大，如果数据多恢复慢。

AOF持久化方式默认关闭，需要配置：
```
##此选项为aof功能的开关，默认为“no”，可以通过“yes”来开启aof功能  
##只有在“yes”下，aof重写/文件同步等特性才会生效  
appendonly yes  

##指定aof文件名称  
appendfilename appendonly.aof  

##指定aof操作中文件同步策略，有三个合法值：always everysec no,默认为everysec  
appendfsync everysec  
##在aof-rewrite期间，appendfsync是否暂缓文件同步，"no"表示“不暂缓”，“yes”表示“暂缓”，默认为“no”  
no-appendfsync-on-rewrite no  

##aof文件rewrite触发的最小文件尺寸(mb,gb),只有大于此aof文件大于此尺寸是才会触发rewrite，默认“64mb”，建议“512mb”  
auto-aof-rewrite-min-size 64mb  

##相对于“上一次”rewrite，本次rewrite触发时aof文件应该增长的百分比。  
##每一次rewrite之后，redis都会记录下此时“新aof”文件的大小(例如A)，那么当aof文件增长到A*(1 + p)之后  
##触发下一次rewrite，每一次aof记录的添加，都会检测当前aof文件的尺寸。
auto-aof-rewrite-percentage 100  
--------------------- 
作者：刘广顺 
来源：CSDN 
原文：https://blog.csdn.net/qq_33285112/article/details/78860023 
版权声明：本文为博主原创文章，转载请附上博文链接！
```




