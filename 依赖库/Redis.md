

# Redis

Redis有些特殊，临时性和永久性兼具。Redis首先把数据保存在内存中，在满足特定条件（默认是 15分钟一次以上，5分钟内10个以上，1分钟内10000个以上的键发生变更）的时候将数据写入到硬盘中，这样既确保了内存中数据的处理速度，又可以通过写入硬盘来保证数据的永久性，这种类型的数据库特别适合处理数组类型的数据。

## NoSql数据库(not only sql)

### 技术

![image-20210427181903209](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210427181903209.png)

### 简介

```tex
1.非关系型数据库，key+value存储，不遵循sql标准，不支持事务中ACID，但可以处理事务，远超sql性能
2.适用场景
	对数据高并发的读写
	海量数据的读写
	对数据高扩展性的
3.不适用场景
	需要事务支持
	结构化查询存储，处理复杂的关系
```

### 数据结构存储持久化数据

![image-20210427191850541](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210427191850541.png)

## 下载

![image-20210427230840106](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210427230840106.png)

```tex
设置后台启动
[root@localhost etc]# vi redis.conf
[root@localhost etc]# cd /usr/local/bin
[root@localhost bin]# redis-server /etc/redis.conf
[root@localhost bin]# ps -ef | grep redis
[root@localhost bin]# redis-cli
127.0.0.1:6379> ping
pong
127.0.0.1:6379> shutdown/exit

kill -9 端口号  杀进程
```

## 相关知识

```tex
dbsize:查看key数量
flushdb清空当前库
flushall通杀全部库
```

串行  vs  多线程+锁（memcache） vs 单线程+多路io复用(redis)

```tex
1、执行 vi world.txt  进入编辑器（默认命令模式），

2、点击a或i进入编辑模式，敲入内容：hello linux world !
3、然后按键盘上的esc键退出编辑模式（进入到命令模式），

4、最后敲冒号：，

5、再敲wq保存并退出。

-------
wq解释为：write quite
不想保存，q
强制退出 q!
————————————————
版权声明：本文为CSDN博主「_挪亚_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zengmingen/article/details/50802555
```



## key操作

```tex
set key value 存key-value

keys *查看当前库所有的key (匹配keys*1)
exists key 判断某个key是否存在
type key查看你的key是什么类型
del key删除指定的key
unlink key根据value选择非阻塞删除，仅将keys从keyspace元数据中删除，真正的删除会在后续异步删除
expire key 10 10秒钟：为给定的key设置过期时间
ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期

select 切换数据库   共有默认的16个数据库 0-15，密码一样
dbsize:查看key数量
flushdb清空当前库
flushall通杀全部库

```

## 数据类型

### String类型

```tex
Redis最基本的数据类型
String类型是二进制安全的，意味着value中可以放各种数据，包括jpg图片
最大可放置512MB
```

#### 常用命令

```tex
set key value
    *NX:当数据库中key不存在时，可以将key-value添加到数据库
    *XX:当数据库中key存在时，可以将key-value添加到数据库，与NX参数互斥
    *EX:key的超时秒数
    *PX:key的超时毫秒数，与EX互斥
get key查询对应键值
append key value将给定的<value>追加到原值的末尾
strlen key获得值的长度
setnx key value只有key不存在时，设置key的值  == set key value NX
incr key将key中存储的数字值加1，只能对数值操作，如果为空，新增值为1
decr key将key中存储的数字值减1，只能对数值操作，如果为空，新增值为-1
incrby/decrby key step步长  使key中存储的数值按步长增或减

mset key1 value1 key2 value2  同时存储多个键值对
mget key1 key2 同时获取多个键值value
msetnx key1 value1 key2 value2 同时设置多个键值对，当且仅当所有给定的key都不存在，有一个失败都失败

getrange key <起始位置><结束位置>  获得值的范围，类似java中的subString  前后都包含
setrange key <起始位置> value  从起始位置开始加入value

setex key <过期时间> value 给键对值设置过期时间
getset key value 以旧换新  获取旧值，设置新值
```

原子操作

```tex
原子操作指不会被线程调度机制打断的操作，这种操作一旦开始，就不会中途切换线程

在单线程中，能够在单条指令中完成的操作都可以认为是原子操作，因为中断只能发生在指令之间

在多线程中，不会被线程调度机制打断的操作就叫原子操作

Redis单命令的原子性就得益于他的单线程
```

#### 数据结构

```tex
简单的动态字符串SDS(Simple Dynamic String) 是可以修改的字符串，内部结构实现类似ArrayList,采用预分配冗余空间的方式来减少内存的频繁分配
```

<img src="C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210428153609062.png" alt="image-20210428153609062" style="zoom:50%;" />

### List列表

#### 简介

```tex
单键多值
redis列表是简单的字符串列表，按照插入顺序排序，可以添加数据到头部或者尾部
底层是一个双向链表，对两端操作性能好，对中间性能差
```

#### 常用命令

```tex
lpush/rpush key value1 value2 value3   从列表左边或右边添加一个或多个值
lpop/rpop key  从列表左边/右边吐出一个值  值在键在，值空键没
rpoplpush key1 key2 从key1列表右边取出一个值放到key2的左边

lrange key <start> <stop>  按照索引下标获取元素，从左到右 0  -1  表示取出所有
lindex key index  按照索引下标获取元素  index元素个数 从左到右
llen key  获取列表长度

linsert key before/after value newvalue  在value的前/后面插入newvalue
lrem key n value  从左边删除n个value  从左到右 value要删除的value值
lset key index value 将列表key下标为index的值换成value
```

#### 数据结构

![image-20210428171600138](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210428171600138.png)

### set集合

#### 简介

```tex
与list相似，具有自动排重功能
Redis的set集合是string类型的无序集合,他的底层其实是一个value为null的hash表，所以添加删除查找的复杂度都是O(1)
```

#### 常用命令

```tex
sadd key value1 value2  将一个或多个value存到集合中，相同值忽略
smembers key 取出该集合的所有值
simember key value  判断集合key是否含有该value值  ，有1  无0
scard key  返回该集合的元素个数
srem key value1 value2  删除集合中的某个元素
spop key  随机吐出集合中的某个值
srandmember key n  随机从集合中取出n个值，不会从集合中删除
smove source destination value  把source集合中的一个值移动到destination集合
sinter key1 key2  返回两个集合的交集元素
sunion  key1 key2  返回两个集合中的并集元素
sdiff key1 key2  返回两个集合中的差集元素   key1-key2  不包含key2-key1
```

#### 数据结构

hash表

### ![image-20210428172933566](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210428172933566.png)



### 哈希

#### 简介

```tex
hash是一个键对值集合
hash是一个string类型的field和value的映射表，hash特别适合存储对象，类似java里面的Map<String,Object>
用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value来存储主要有两种存储方式
```

![image-20210510135839132](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510135839132.png)

![image-20210510135859172](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510135859172.png)



![image-20210510135413045](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510135413045.png)



#### 常用命令

```tex
hset <key> <field> <value>    给key集合中的field键赋值
hget <key1> <field>   从key1集合field中取出value

hmset key1 field1 value1 field2 value2    批量设置value的值
hexists key1 field  查看hash表key中，filed是否存在

hkeys key 列出该hash集合中的所有field
hvals key 列出该hash集合中的所有value

hincrby key field increment 为hash表 key的域 field的值上加上增量1 -1
hsetnx key field value将hash表key中的域field的值设置为value，当且仅当field不存在

```

### Zset有序集合

#### 简介

```tex
与set相似，是一个没有重复元素的集合
不同之处是有序集合的每个成员都关联了一个评分(score),这个评分(score)被用来按照从最低分到最高分的方式排列集合中的成员。
集合中的成员是唯一的，但是评分可以是重复的。
因为元素是有序的，所以可以根据评分(score)或次序(position)来获取一个范围的元素
访问有序集合的中间元素也是非常快的，因此能够使用有序集合作为一个没有重复元素成员的智能列表
```

#### 常用命令

```tex
zadd key score1 value1 score2 value2   将一个或多个member元素及其score值加入到有序集合key中
zrange key start stop [WITHSCORES] 返回有序集合key中，下标在start和stop中间的元素，WITHSCORES可以让分数和值一起返回到结		果集
zrangebyscore key min max [WITHSCORES] [limit offset count] 返回有序结合key中，所有score值介于min,max之间（包括min		和max）的成员,有序集合成员按score值递增(从小到大)排列
zrevrangebyscore key max min [WITHSCORES] [limit offset count] 返回有序结合key中，所有score值介于min,max之间（包括		min和max）的成员,有序集合成员按score值递减(从大到小)排列
zincrby key increment value 为value的score加上增量
zrem key value   删除该集合下，指定值的元素
zcount key min max   统计该集合，分数区间内的元素个数
zrank key value   返回该值在集合中的排名，从0开始
```

#### 数据结构

![image-20210510143547504](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510143547504.png)



![image-20210510143713583](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510143713583.png)

![image-20210510143725111](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510143725111.png)

## 配置文件介绍

### ###Units 单位###

配置大小单位，开头定义了一些基本的度量单位，只支持byte,不支持bit,大小写不敏感

### ###INCLUDE###

类似jsp中的include,多实例的情况可以把公用的配置文件提取出来

### ###NETWORK###

网络相关配置

![image-20210510145358771](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510145358771.png)

4.3.2protected-mode  yes/no

yes只能本机访问，远程不能访问

no远程可以访问

![image-20210510145701990](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510145701990.png)

![image-20210510145920102](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510145920102.png)

4.3.6 tcp-keepactive  默认300秒检测一次连接是否使用，不使用就断开

### ###security###

设置密码



![image-20210510150325071](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510150325071.png)

### ###LIMITS###

![image-20210510150419351](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510150419351.png)

![image-20210510150432151](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510150432151.png)

![image-20210510150446531](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210510150446531.png)

## Redis发布和订阅

![image-20210511113417754](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511113417754.png)

![image-20210511113624330](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511113624330.png)

![image-20210511113641314](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511113641314.png)

## 新数据类型

### BitMaps

#### 简介

![image-20210511113943251](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511113943251.png)

#### 常用命令

```tex
setbit key offset value   设置BitMaps中的某个偏移量的值（0或1） offset：偏移量从0 开始

getbit key offset  获取bitmaps中偏移量的值 ，offset：偏移量从0 开始

bitcount key [start end] 统计字符串从start字节到end字节比特值为1 的数量

bitop and(or/not/xor) <destkey> [key...]   bitop是一个复合操作，它可以做多个BitMaps的and(交集),or(并集),not(非),xor(异或)操作并将结果保存在destkey中
```

![image-20210511115144811](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115144811.png)

![image-20210511115241482](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115241482.png)

![image-20210511115255939](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115255939.png)

![image-20210511115308650](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115308650.png)

### HyperLogLog

#### 简介

![image-20210511115443378](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115443378.png)

![image-20210511115538306](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115538306.png)

![image-20210511115458098](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115458098.png)

![image-20210511115556849](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511115556849.png)

#### 常用命令

```tex
pfadd key element [element...]  添加指定元素到HyperLogLog中,成功返回1，失败返回0

pfcount key [key...] 计算HLL的近似基数，可以计算多个HLL，比如用HLL存储每天的UV，计算一周的UV可以使用七天的UV合并计算

pfmerge destkey sourcekey [sourcekey]  将一个或多个HLL合并后的结果存储到另一个HLL中，比如每月活跃用户可以使用每天的活跃用		户合并计算
```

### Geospatial

#### 简介

![image-20210511120303303](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511120303303.png)

#### 常用命令

```tex
geoadd key <longitude> <latitude> <member> [longitude latitude member] 添加地理位置（经度，纬度，名称）
```

![image-20210511120603500](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511120603500.png)

![image-20210511120623842](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511120623842.png)

```tex
geopos key member [member..]   获取指定地区的经纬度
```

![image-20210511120745193](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511120745193.png)

```tex
geodist key member1 member2 [m/km/ft/mi]  获取两个位置之间的直线距离
m:米
km:千米
mi:英里
ft:英尺
默认使用米
```

```tex
georadius key <longitude> <latitude> radius m/km/mi/ft  以给定的经纬度为中心，找出某一半径内的元素   radius:距离
```

## Jedis

```xml
 <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.3.0</version>
        </dependency>
```







![image-20210511144230513](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511144230513.png)



![image-20210511144244083](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210511144244083.png)

### 手机验证码

![image-20210512101612596](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210512101612596.png)

## Springboot整合redis

```xml
<!--springboot  redis整合-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!--spring2.x 集成redis所需 依赖-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.6.0</version>
</dependency>
```



![image-20210512105158538](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210512105158538.png)

![image-20210512105221800](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210512105221800.png)

![image-20210512105231715](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210512105231715.png)