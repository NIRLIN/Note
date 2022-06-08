# Redis学习笔记

[TOC]

## 1.NoSQL概述

### 1.为什么要用NoSQL

发展路径

1. 90年代，单机 MySQL 的美好时代，一个网站的访问量一般都不大，用单个数据库完全可以轻松应付。

2. Memcached(缓存)+MySQL+垂直拆分

   后来，随着访问量的上升，几乎大部分使用MySQL架构的网站在数据库上都开始出现了性能问题，web程序不再仅仅专注在功能上，同时也在追求性能。程序员们开始大量的使用缓存技术来缓解数据库的压力，优化数据库的结构和索引。开始比较流行的是通过文件缓存来缓解数据库压力，但是当访问量继续增大的时候，多台web机器通过文件缓存不能共享，大量的小文件缓存也带了了比较高的IO压力。在这个时候，Memcached就自然的成为一个非常时尚的技术产品。

3. Mysql主从读写分离

   由于数据库的写入压力增加，Memcached只能缓解数据库的读取压力。读写集中在一个数据库上让数据库不堪重负，大部分网站开始使用主从复制技术来达到读写分离，以提高读写性能和读库的可扩展性。Mysql的master-slave模式成为这个时候的网站标配了。

4. 分库分表+水平拆分+mysql集群

   在Memcached的高速缓存，MySQL的主从复制，读写分离的基础之上，这时MySQL主库的写压力开始出现瓶颈，而数据量的持续猛增，由于MyISAM在写数据的时候会使用表锁，在高并发写数据的情况下会出现严重的锁问题，大量的高并发MySQL应用开始使用InnoDB引擎代替MyISAM。

   同时，开始流行使用分表分库来缓解写压力和数据增长的扩展问题。这个时候，分表分库成了一个热门技术，是面试的热门问题也是业界讨论的热门技术问题。也就在这个时候，MySQL推出了还不太稳定的表分区，这也给技术实力一般的公司带来了希望。虽然MySQL推出了MySQL Cluster集群，但性能也不能很好满足互联网的要求，只是在高可靠性上提供了非常大的保证。
   

存在以下情况就需要更换原有的单机架构

1. 数据量太大，一台机器无法容纳
2. 数据的索引（B+Tree ），一台机器无法容纳
3. 访问量（读写混合），一台机器无法容纳



具体可参看这篇文章

什么是NoSQL，为什么要使用NoSQL？
版权声明：本文为CSDN博主「曲健磊」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/a909301740/java/article/details/80149552

### 2.什么是NoSQL
NoSQL=Not Only SQL（不仅仅是SQL）

泛指非关系型数据库。随着互联网web2.0网站的兴起，传统的关系数据库在处理web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，出现了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题。

**NoSQL特点**

1. 方便扩展（数据之间没有关系，很好扩展）

2. 大数据量高性能（一秒写8万次，读11万次。NoSQL的缓存记录级是一种细粒度的缓存，性能比较高

3. 数据类型是多型的！（不需要事先设计数据库，随取随用 ）

4.  特点

   - 没有固定查询语言
   - 键值对储存，列储存，文档储存，图形数据库（社交关系）
   - 最终一致性
   - SPA定理和BASE定理

5. **3V+3高**

   - 大数据时代的3V：主要是描述问题
     - 海量-Volume
     - 多样-Variety
     - 实时-Velocity
   - 大数据时代的3高：主要是对程序的要求
     - 高并发
     - 高可托
     - 高性能

   **电商服务**
   
   ```bash
   1.基本信息 -MySql、Oracle
   2.描述或评论 -MongoDB
   3.图片 -FastDFS（分布式文件系统）、TFS（淘宝）、GFS（Google）、HDFS（hadoop）、oss（阿里云）
   4.搜索 -solr、elasticsearch、ISearch
   5.波段信息 -Redis、Tair、Memecache
6.支付服务
   ```

   


### 3.NoSQL四大分类

1. K-V键值对
   - 新浪：Redis
   - 美团：Redis+Tair
   - 阿里、百度：Redis+Memecache
2. 文档型数据库（Bson格式，是Json的二进制形式）
   - MongoDB（一般必须要掌握）
     - MongoDB是一个基于分布式文件存储的数据库，C++编写，主要用来处理大量文档
     - MongoDB是一个介于关系型数据库和非关系型数据库中间的产品
     - MongoDB是非关系型数据库中功能最丰富的，结构最像关系型数据库
   - ConthDB
3. 列存储数据库
   - HBase
   - 分布式文件系统
4. 图关系数据库（存放关系）
   - Neo4j、infoGrid

## 2.Redis入门

### 1.概述

   #### 1.什么是Redis

Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

#### 2.Redis能干什么

1. 内存储存、持久化，内存是断电即失的，因此持久化是非常重要的（rdb，aof）
2. 效率高，可用于高速缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器、计数器（浏览量）
6. 更多

#### 3.特性


1. 多样的数据类型
2. 持久化
3. 集群
4. 事务

#### 4.安装

1. 下载

   由于docker环境已经准备好了，所以我们只需要下载redis即可

   **docker pull redis**

   可使用`docker inspect redis`可以下在json数据中找到‘ENV’中找到redis版本

2. 启动

   1. 交互式启动：docker run -ti redis
   2. 守护式启动：docker run -tid redis
   3. 交互式启动加上端口映射：docker run -ti -p 6379:6379 redis
   4. 守护式启动加上端口映射：docker run -tid -p 6379:6379 redis

3. yum install gcc-c++、make、make install

   由于是容器创建的redis，所以是已经准备好的，可以直接使用

4. 如果是使用docker，则需要使redis在后台运行

   1. 交互式启动后使用ctrl+P+Q退出主页面
   2. 守护式启动不需要其余操作

   使用 docker exec -it 容器id /bin/bash

5. 切换到redis默认安装路径 /usr/local/bin

6. 配置redis.config.

7. 启动redis

   可选择启动的配置文件：redis-server redis.config

   由于是docker启动，这一步略过

8. 连接redis

   redis-cli -p 6379

9. 关机

   1. 关闭redis：shutdown
   2. 退出：exit

#### 5.测试性能

```bash
测试：100个并发连接 1000000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

```bash
100000 requests completed in 3.65 seconds #100000个请求3秒完成
100 parallel clients #100个连接
3 bytes payload	#每个请求3个字节
keep alive: 1 #服务端数量
```



### 2.基础知识

#### 1.Redis基础

1. redis有16个数据库，默认选择第0个

2. 选择数据库：select index

3. 获得数据库大小：DBSIZE

4. 设置键值对：set key value

5. 获得键值对：get key

6. 获取键类型：type key

7. 查看所有键值对中的键-key：keys *

8. 删除一个或多个键，返回删除的键的个数：del key [key...] 　　 

9. 移动键到指定库：move key num

10. 设置键过期时间：EXPIRE key 10

11. 显示过期时间：ttl key

12. 清空当前库：flushdb

13. 清空所有库：FLUSHALL

14. redis是单线程的

    redis非常快，官方表示redis是基于内存操作的，cpu不是redis的性能瓶颈，redis的瓶颈是根据机器的内存和网络带宽，既然可以使用单线程，就使用了单线程

15. redis使用单线程为什么这么快

    redis是将所有数据放在内存中的，因此使用单线程去操作是最快的，多线程反而会浪费更多的资源在上下文切换中

16. redis命令不区分大小写，键区分大小写

#### 基础Redis-Key

```shell
keys *				# 查看所有key
set key value		# 设置key
get key				# 得到key的值
EXISTS key			# 判断键是否存在
move key num		# 移动key到指定数据库
EXPIRE key time		# 设置过期时间
ttl name			# 查看当前key的剩余时间
type key 			# 查看指定键的值得类型

```



#### 2.五大数据类型

##### 1.String（字符串）

- [APPEND key value追加一个值到key上](http://www.redis.cn/commands/append.html)
- [BITCOUNT key [start end\]统计字符串指定起始位置的字节数](http://www.redis.cn/commands/bitcount.html)
- [BITFIELD key [GET type offset\] [SET type offset value] [INCRBY type offset increment] [OVERFLOW WRAP|SAT|FAIL]Perform arbitrary bitfield integer operations on strings](http://www.redis.cn/commands/bitfield.html)
- [BITOP operation destkey key [key ...\]Perform bitwise operations between strings](http://www.redis.cn/commands/bitop.html)
- [BITPOS key bit [start\] [end]Find first bit set or clear in a string](http://www.redis.cn/commands/bitpos.html)
- [DECR key整数原子减1](http://www.redis.cn/commands/decr.html)
- [DECRBY key decrement原子减指定的整数](http://www.redis.cn/commands/decrby.html)
- [GET key返回key的value](http://www.redis.cn/commands/get.html)
- [GETBIT key offset返回位的值存储在关键的字符串值的偏移量。](http://www.redis.cn/commands/getbit.html)
- [GETRANGE key start end获取存储在key上的值的一个子字符串](http://www.redis.cn/commands/getrange.html)
- [GETSET key value设置一个key的value，并获取设置前的值](http://www.redis.cn/commands/getset.html)
- [INCR key执行原子加1操作](http://www.redis.cn/commands/incr.html)
- [INCRBY key increment执行原子增加一个整数](http://www.redis.cn/commands/incrby.html)
- [INCRBYFLOAT key increment执行原子增加一个浮点数](http://www.redis.cn/commands/incrbyfloat.html)
- MGET key [ [key ...\]获得所有key的值](http://www.redis.cn/commands/mget.html)
- [MSET key value [key value ...\]设置多个key value](http://www.redis.cn/commands/mset.html)
- [MSETNX key value [key value ...\]设置多个key value,仅当key存在时](http://www.redis.cn/commands/msetnx.html)
- [PSETEX key milliseconds valueSet the value and expiration in milliseconds of a key](http://www.redis.cn/commands/psetex.html)
- [SET key value [EX seconds\] [PX milliseconds] [NX|XX]设置一个key的value值](http://www.redis.cn/commands/set.html)
- [SETBIT key offset valueSets or clears the bit at offset in the string value stored at key](http://www.redis.cn/commands/setbit.html)
- [SETEX key seconds value设置key-value并设置过期时间（单位：秒）](http://www.redis.cn/commands/setex.html)
- [SETNX key value设置的一个关键的价值，只有当该键不存在](http://www.redis.cn/commands/setnx.html)
- [SETRANGE key offset valueOverwrite part of a string at key starting at the specified offset](http://www.redis.cn/commands/setrange.html)
- [STRLEN key获取指定key值的长度](http://www.redis.cn/commands/strlen.html)

##### 2.List（列表）

1. 添加值：
   1. lpush key value **注：左push **
   2. rpush key value  **注：右push **
2. 获取范围值：lrange key start end
3. 移除值
   1. lpop key 
   2. rpop key
4. 获取指定下标值：lindex key index
5. 返回列表长度：llen key
6. 从列表中删除元素：LREM key count value
7. 修剪到指定范围内的清单：LTRIM key start stop
8. 删除列表中的最后一个元素，将其追加到另一个列表：RPOPLPUSH source destination
9. 设置队列里面一个元素的值：LSET key index value
10. 在列表中的另一个元素之前或之后插入一个元素：LINSERT key BEFORE|AFTER pivot value

**小结**

1. list实际上是一个链表，before node after，left，right都可以插入值
2. 如果key不存在，创建新的链表
3. 如果key存在，新增内容
4. 如果移除了所有值，空链表也代表不存在
5. 在两边插入或者改动值，效率最高，中间元素效率相对较低

##### 3.Set（集合）

**set中的值不可重复，无序不重合**

1. SADD key member [member ...] ：添加一个或者多个元素到集合(set)里
2. SMEMBERS key：获取集合里面的所有元素
3. SISMEMBER key member：确定一个给定的值是一个集合的成员
4. SCARD key：获取集合里面的元素数量
5. SREM key member [member ...]：从集合里删除一个或多个元素
6. SRANDMEMBER key [count]：从集合里面随机获取一个元素
7. SPOP key [count]：删除并获取一个集合里面的元素
8. SMOVE source destination member：移动集合里面的一个元素到另一个集合
9. SDIFF key [key ...]：获得队列不存在的元素
10. SINTER key [key ...]：获得两个集合的交集

##### 3.  Hash（哈希）

实质是**key map<key value>**

1. HDEL key field [field ...]
   删除一个或多个Hash的field
2. HEXISTS key field
   判断field是否存在于hash中
3. HGET key field
   获取hash中field的值
4. HGETALL key
   从hash中读取全部的域和值
5. HINCRBY key field increment
   将hash中指定域的值增加给定的数字
6. HINCRBYFLOAT key field increment
   将hash中指定域的值增加给定的浮点数
7. HKEYS key
   获取hash的所有字段
8. HLEN key
   获取hash里所有字段的数量
9. HMGET key field [field ...]
   获取hash里面指定字段的值
10. HMSET key field value [field value ...]
    设置hash字段值
11. HSET key field value
    设置hash里面一个字段的值
12. HSETNX key field value
    设置hash的一个字段，只有当这个字段不存在时有效
13. HSTRLEN key field
    获取hash里面指定field的长度
14. HVALS key
    获得hash的所有值
15. HSCAN key cursor [MATCH pattern] [COUNT count]
    迭代hash里面的元素

##### 4.Zset

1. ZADD key [NX|XX] [CH] [INCR] score member [score member ...]
   添加到有序set的一个或多个成员，或更新的分数，如果它已经存在
2. ZCARD key
   获取一个排序的集合中的成员数量
3. ZCOUNT key min max
   返回分数范围内的成员数量
4. ZINCRBY key increment member
   增量的一名成员在排序设置的评分
5. ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]
   相交多个排序集，导致排序的设置存储在一个新的关键
6. ZLEXCOUNT key min max
   返回成员之间的成员数量
7. ZPOPMAX key [count]
   Remove and return members with the highest scores in a sorted set
   ZPOPMIN key [count]
   Remove and return members with the lowest scores in a sorted set
   ZRANGE key start stop [WITHSCORES]
   根据指定的index返回，返回sorted set的成员列表
8. ZRANGEBYLEX key min max [LIMIT offset count]
   返回指定成员区间内的成员，按字典正序排列, 分数必须相同。
9. ZREVRANGEBYLEX key max min [LIMIT offset count]
   返回指定成员区间内的成员，按字典倒序排列, 分数必须相同
10. ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
    返回有序集合中指定分数区间内的成员，分数由低到高排序。
11. ZRANK key member
    确定在排序集合成员的索引
12. ZREM key member [member ...]
    从排序的集合中删除一个或多个成员
13. ZREMRANGEBYLEX key min max
    删除名称按字典由低到高排序成员之间所有成员。
14. ZREMRANGEBYRANK key start stop
    在排序设置的所有成员在给定的索引中删除
15. ZREMRANGEBYSCORE key min max
    删除一个排序的设置在给定的分数所有成员
16. ZREVRANGE key start stop [WITHSCORES]
    在排序的设置返回的成员范围，通过索引，下令从分数高到低
17. ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
    返回有序集合中指定分数区间内的成员，分数由高到低排序。
18. ZREVRANK key member
    确定指数在排序集的成员，下令从分数高到低
19. ZSCORE key member
    获取成员在排序设置相关的比分
20. ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight [weight ...]] [AGGREGATE SUM|MIN|MAX]
    添加多个排序集和导致排序的设置存储在一个新的关键
21. ZSCAN key cursor [MATCH pattern] [COUNT count]
    迭代sorted sets里面的元素

#### 3.三种特殊数据类型

##### 1.geospatial

1. GEOADD key longitude latitude member [longitude latitude member ...]
   添加一个或多个地理空间位置到sorted set
2. GEOHASH key member [member ...]
   返回一个标准的地理空间的Geohash字符串
3. GEOPOS key member [member ...]
   返回地理空间的经纬度
4. GEODIST key member1 member2 [unit]
   返回两个地理空间之间的距离
5. GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
   查询指定半径内所有的地理空间元素的集合。
6. GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
   查询指定半径内匹配到的最大距离的一个地理空间元素。

##### 2.Hyperloglog

1. PFADD key element [element ...]
   将指定元素添加到HyperLogLog
2. PFCOUNT key [key ...]
   Return the approximated cardinality of the set(s) observed by the HyperLogLog at key(s).
3. PFMERGE destkey sourcekey [sourcekey ...]
   Merge N different HyperLogLogs into a single one.

#### 4.事务

**redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行中会按照 顺序执行**

一次性、顺序性、排他性！执行一些列的命令

**redis的事务没有隔离级别的概念**

所有的命令在事务中，并没有直接被执行，只有发起执行命令的时候才会执行：Exec

**redis的单条命令保证原子性，但是事务不保证原子性**

redis的事务

- 开启事务：multi
- 命令入队
- 执行事务：exec

放弃事务：discard

**异常**

- 编译型异常（代码有问题，命令有错）：事务中的所有命令都不会被执行
- 运行时异常（1/0）：如果事务队列存在语法性，那么执行命令的时候，其他命令是可以被正常执行的，错误命令抛出异常

#### 5.Jedis

##### 1.简介

什么是jedis：jedis是redis官方推荐的java连接开发工具！使用java操作redis中间件，如果要使用redis就必须要对jedis十分熟悉。

##### 2.依赖

```xml
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.68</version>
</dependency>
```

##### 3.测试连接

```java
public class TestPing {
    public static void main(String[] args) {
        Jedis jedis=new Jedis("39.100.232.173",6379);
        System.out.println(jedis.ping());
        System.out.println(jedis.set("asd", "asdas"));
        System.out.println(jedis.get("asd"));
    }
}
```

### 3.SpringBoot整合Redis

   springboot使用springdata进行操纵数据库

​	**在springboot2.x后，原来的中间件jedis被lettuce替换**

- jedis：采用直连，多个线程操作时不安全。如果想要避免不安全，需要使用jedis poll连接池，又会引发BIO问题
- lettuce：采用netty，实例可以在多个线程中进行共享，不存在线程不安全问题，可以减少线程数据，像NIO模式。



### 4.持久化

#### 1.RDB

RDB是将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复。

持久化过程：

当满足save的条件时，比如更改了1个key，900s后会将数据写入临时文件，持久化完成后将临时文件替换旧的dump.rdb。（存储数据的节点是到触发时间时的的节点，也就是对Key有个计数）

使用RDB恢复数据：

自动的持久化数据存储到dump.rdb后。实际只要重启redis服务即可完成（启动redis的server时会从dump.rdb中先同步数据），所以说当我们在关停redis服务的时候，只需要在/redis-3.2.12/src目录下查看是否存在dump.rdb，如果存在，则下次启动redis服务的时候，数据会由redis自动恢复。（如果不存在，那一定是配置文件 redis.conf出了错）

#### 2.AOF

AOF是将执行过的指令记录下来，数据恢复时按照从前到后的顺序再将指令执行一遍，实现数据恢复。

其中，AOF默认关闭，开启方法，修改配置文件redis.conf：appendonly yes

### 4.Redis主从复制

https://www.cnblogs.com/kismetv/p/9236731.html

#### 1.概述

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点(master)，后者称为从节点(slave)；数据的复制是单向的，只能由主节点到从节点。

默认情况下，每台Redis服务器都是主节点；且一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。

**主从复制的作用**

主从复制的作用主要包括：

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。
4. 高可用基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是Redis高可用的基础。

#### 2.复制原理

**全量复制和部分复制**

在Redis2.8以前，从节点向主节点发送sync命令请求同步数据，此时的同步方式是全量复制；在Redis2.8及以后，从节点可以发送psync命令请求同步数据，此时根据主从节点当前状态的不同，同步方式可能是全量复制或部分复制。后文介绍以Redis2.8及以后版本为例。

1. 全量复制：用于初次复制或其他无法进行部分复制的情况，将主节点中的所有数据都发送给从节点，是一个非常重型的操作。
2. 部分复制：用于网络中断等情况后的复制，只将中断期间主节点执行的写命令发送给从节点，与全量复制相比更加高效。需要注意的是，如果网络中断时间过长，导致主节点没有能够完整地保存中断期间执行的写命令，则无法进行部分复制，仍使用全量复制。



**主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费事费力，还会造成一段时间内服务不可用。**这不是一种推荐的方式，更多时候，我们优先考虑**哨兵模式**。

## 一、哨兵模式概述

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。**

![img](https:////upload-images.jianshu.io/upload_images/11320039-57a77ca2757d0924.png?imageMogr2/auto-orient/strip|imageView2/2/w/507/format/webp)

Redis哨兵



这里的哨兵有两个作用

- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。
- 当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式。

用文字描述一下**故障切换（failover）**的过程。假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。这样对于客户端而言，一切都是透明的。



作者：秃头哥编程
链接：https://www.jianshu.com/p/06ab9daf921d
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

















