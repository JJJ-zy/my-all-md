# Redis-review

## NoSQL

### 什么是NoSQL

```tex
Not Only SQL  不仅仅是SQL
```

### 特点

```tex
1.方便扩展(数据之间没有关系，扩展方便)
2.大数据量高性能(Redis一秒写80000次，读110000次 ，Nosql是缓存记录级，是一种细粒度的缓存，性能高)
3.数据类型是多样的(不需要设计数据库表)
```

### 传统关系型数据库(RDBMS)和NoSQL

```tex
RDBMS:
	结构化组织
	SQL
	数据和关系都在一个表中  行列
	操作语言，数据定义语言
	严格的一致性ACID
	基础的事务
	......
NoSQL:
	不仅仅是数据
	没有固定的查询语言
	键对值存储，列存储，文档存储，图形数据库(例如社交关系)
	最终一致性
	CAP定理 和BASE理论
	高性能，高可用，高可扩
	......
```

### 3V和3高

#### 大数据时代的3V：

1. 海量Volume
2. 多样Variety
3. 实时Velocity

#### 互联网需求的3高：

1. 高并发
2. 高可拓
3. 高性能

NoSQL和RDBMS联合使用



大型电商平台

```bash
#商品信息
	名称，价格，商家信息
	关系型数据库
#商品的描述，评论(多文字型数据)
	文档型数据库  MongoDB
#图片
	分布式文件系统 FastDFS
	淘宝的   TFS
	google  GFS
	Hadoop  HDFS
	阿里云   oos
#商品的关键字搜索
	搜索引擎 solr elasticsearch
	阿里 ：Isearch  多隆
#商品热门波段信息
	redis,tair,MemCache
#商品的交易
	第三方应用
```

### NoSQL四大分类

#### K-V键对值

```tex
新浪:Redis
美团:Redis+Tair
阿里，百度:Redis+MemeCache

```

#### 文档型数据库(Bson和Json) 

Bson是Json的二进制

```tex
MongoDB
	是一个基于分布式文件存储的数据库，C++编写，主要用来处理大量的文档
	是一个介于RDBMS和NoSQL中间的产品，MongoDB是非关系型数据库中功能最丰富，最像关系型数据库的非关系型数据库
	
ConthDB
```

#### 列存储数据库

```tex
HBase
应用场景:分布式文件系统
```

#### 图关系数据库

```tex
不是存储图片的，是存放关系的，比如朋友社交网络
Neo4j,InfoGrid
```

## Redis入门

```tex
Remote Dictionary Server   远程字典服务
是一个开源的用C语言编写的，可基于内存亦可持久化的K-V数据库，并提供多种语言的API
```

> **能干吗**？

```tex
1.内存存储，持久化，内存断电即失，所以持久化很重要(RDB AOF)
2.效率高，用于高速缓存
3.发布订阅系统（消息队列）
4.地图信息分析
5.计时器，计数器(浏览量)
...
```

> **特性**

```tex
1.多样的数据类型
2.持久化
3.集群
4.事务
...
```

> **学习用到的东西**

```tex
1.中文网站: http://www.redis.cn/
2.外网: https://redis.io/
3.狂神说公众号资料

windows版本在github下载,不推荐windows开发
```

### Redis安装到Linux

```tex
1.将压缩包放到服务器
2.使用tar -zxvf 命令解压压缩包
3.进入redis文件夹查看conf文件
4.安装基本环境 yum install gcc-c++
5.make命令 
	安装6.0.1版本报错解决
	升级gcc  centos7默认gcc版本为4.8.5
	#升级到 5.3及以上版本
        yum -y install centos-release-scl
        yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils

        scl enable devtoolset-9 bash    9版本
        
        退出xshell gcc版本恢复原来版本    gcc版本长期生效语句
        echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
        
        gcc -v查版本号
6.make install 安装程序
7.redis默认安装到/usr/local/bin
8.cp /usr/local/redis/redis6.0.6/redis.conf /usr/local/bin/myconfig 拷贝一份conf文件，使用副本保证安全
9.设置后台启动 redis.conf  中  daemonize改为yes
10.启动redis   redis-server myconfig/redis.conf  指定启动配置文件
11.连接redis客户端redis-cli -p 6379   
12.测试连接  ping->pong
13.连接后shutdown命令关闭redis-server
14.exit退出客户端
15.后面会使用单机多redis集群测试
```

### 测试性能

```tex
redis-benchmark是一个压力测试工具，官方自带的性能测试工具
```

![image-20210819144917975](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210819144917975.png)

![image-20210819145040864](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210819145040864.png)

### 基础知识

#### 默认16个数据库

```tex
默认有16个数据库，redis.conf中有databases 16

默认选择第0个

切换语句:select [index]

	dbsize查看数据库大小
```

#### 基本命令

```bash
get key   set key
#查询所有key
	keys *
#清空当前数据库
	flushdb
#清空所有数据库
	flushall
#判断key是否存在(可以判断多个key)
	exists key [key...]
#移动key到哪个数据库
	move [key] [db]
#设置过期时间
	expire [key] [seconds]
#查看过期时间
	ttl key
#查看数据类型
	type key
#删除key
	del key
```

#### Redis是单线程的

```tex
Redis基于内存操作，CPU不是Redis的瓶颈，内存和网络带宽才是Redis的瓶颈，多线程是基于cpu的，Redis单线程可以做，就不使用多线程了
Redis是C语言写的，官方提供的数据为100000+的QPS ,完全不比MemeCache差
```

> 单线程为什么还这么快

```tex
误区一:高性能的服务器一定是多线程的
误区二:多线程一定比单线程快
核心:redis将所有数据放在内存中，单线程效率最高，多线程(cpu会上下文切换，影响效率)，对于内存，没有上下文切换就是最快的
```

### 五大数据类型

#### String

```bash
#原value追加value  返回长度  如果key不存在 就set key
	append key value
#获取长度
	strlen key
#value值增加  只有当value为数字时
	incr key
#value值减少  只有当value为数字时
	decr key
#value值按步长增加
	incrby key [increment]
#value值按步长减少
	decrby key [increment]
#截取value值     start为0 end为-1 截取全部    end为-1表示从start截取到最后
	getrange key [start] [end]
#替换value值 从偏移量开始替换为newvalue
	setrange key [偏移量offset] [newvalue]
	
# NX XX
	NX 当key不存在时创建   setnx k1 v1 // set k1 v1 NX
	XX 当key存在时，替换value   set k1 v1 XX
# EX PX
	EX 设置过期时间秒为单位  setex k1 seconds v1 // set k1 v1 NX
	PX 设置过期时间毫秒为单位  set k1 v1 PX
	
#设置多个值  原子性 失败都失败，成功都成功
	mset k1 v1 k2 v2 k3 v3
#获取多个值
	mget k1 k2 k3

	msetex k1 v1 k2 v2  

#设置对象的两种方式
    set user:1 {name:zhangsan,age:20}
    mset user:1:name zhangsan user:1:age 20

    msetex user:1:name zhangsan user:1:age 20 1000  设置过期时间

#组合
	getset key value  (返回oldvalue 设置newvalue)
```

#### List

```tex
基本数据类型  列表
可以作为队列，也可以作为栈
```

```bash
#所有的list命令都是l开头的

#左设置值
	lpush value
#取值  左push倒序取值 最后一个为0
	lrange start end
#右设置值
	rpush value
#左移除
	lpop key
#右移除
	rpop key
#按下标查询
	lindex key index
#查询列表长度
	llen key
#移除列表指定值 列表value可以重复
	lrem key count(个数) value
#截取列表指定下标的值，并替换给当前key
	ltrim key start end
#移除列表右边第一个元素插入到一个列表最左边
	rpoplpush key key2
#向列表中指定下标的位置替换为value，下标需要存在
	lset key index value
#向列表中指定值的前面或者后面插入值
	linsert key before/after oldvalue newvalue
```

#### Set

```bash
#set中的值不能重复

#设置值
	sadd key [member...]
#查看值
	smembers key
#判断member是不是key中的成员
	sismember key member
#获取set集合中成员的个数
	scard key
#移除指定成员
	srem key member
#随机取出成员 默认取出一个
	srandmember key [count]
#随机删除成员,默认删除一个
	spop key [count]
#从key中移动成员到key2
	smove key key2 member
	
#共同好友的场景
#两个set集合中的不同成员
	sdiff key1 key2
#两个set集合中的相同成员
	sinter key1 key2
#两个set集合的并集
	sunion key1 key2
```

#### Hash

```bash
Map集合 key-map   本质还是一个K-V  只不过V是两个值  更适合对象的存储
#设置值 key map<field,value>
	hset key field value
#获取值
	hget key field
#设置多个值
	hmset key field1 value1 field2 value2
#获取多个值
	hmget key field1 field2
#获取全部值
	hgetall key
#删除某个值,可以删除多个
	hdel key field [field...]
#获取field的个数
	hlen key
#遍历所有field
	hkeys key
#判断hash中的field是否存在
	hexists key field
#获得所有的值
	hvals key
#value值自增
	hincrby key field increment
#nx仅当不存在的时候创建
	hsetnx key field value
	
```

#### Zset

```bash
有序集合，在set的基础上增加了一个值来做一些操作
#设置值
	zadd key score value
#获取值
	zrange key 0 -1 [withscores]
#倒叙获取值(从大到小)      
	zrevrange key start end 
#按score进行排序(从小到大)      withscores带着score一起输出
	zrangebyscore key min max   (-inf +inf)无限小无限大 [withscores]
#移除成员
	zrem key member
#获取集合中的个数
	zcard key
#返回score范围内的值
	zcount key min max
	
场景:班级成绩，带权重的信息，排行榜
```

### 三种特殊数据类型

#### geospatial地理位置

```bash
推算地理信息和距离，方圆几里的人
```

```bash
#设置地理位置   longtitude 经度   latitude 纬度
	geoadd key longtitude latitude member
#获取标准的地理空间字符串 没什么用
	geohash key member
#获取两地的距离
	geodist key member1 member2 m|km|ft(英尺)|mi(英里) 默认m
#获取经纬度
	geopos key member [member..]
#查询指定半径内所有空间元素的集合  radius半径
	georadius key longtitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
	WITHDIST: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保持一致。
	WITHCOORD: 将位置元素的经度和维度也一并返回
	ASC: 根据中心的位置， 按照从近到远的方式返回位置元素。
	DESC: 根据中心的位置， 按照从远到近的方式返回位置元素。
	COUNT <count>: 返回指定数量的元素，但执行速度不变，全部查询然后截取的
#查询指定半径内所有空间元素的集合 以member为中心
	georadiusbymember key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
	

#底层是zset，可以使用zset操作geo
#移除成员
	zrem key member
#查看元素 withscores带着score一起输出
	zrange key start end [withscores]
```

#### Hyperloglog基数统计

```bash
基数 == 集合中所有不重复的数+重复的数的一个
hyperloglog 的 内存是固定的 12kb 可以存放2^64个数据
#设置值
	pfadd key element [element...]
#获取值的数量
	pfcount key [key...]
#合并多个集合为一个新的集合
	pfmerge newkey key [key...]
```

#### Bitmap

> 位存储   0|1

统计用户信息 活跃/不活跃 、 打卡 、登录/未登录      0|1解决

操作二进位制进行记录

```bash
#记录一周打卡 offsetbit位置 value0/1
	setbit key offset value
#获取某一天
	getbit key offset
#获取所有为1的offset的个数
	bitcount key [start end]
```

### 事务

> Redis事务本质:一组命令的集合，串行执行，排队执行，一个事务中的所有命令被序列化排队执行，没有隔离级别的概念，排队执行，新的命令只会添加到最后面，不会产生脏读、不可重复读、幻读

>命令会先添加到事务队列中，不会直接执行，执行命令 EXEC
>
>一次性，顺序性，排他性  不受其他事务的影响

> 单条命令具有原子性 ，事务不具有原子性，执行过程中发生错误，不会回滚，官方认为，原子性的错误需要在代码中解决,提前发现，关闭事务队列，在执行时没有队列，事务就不会执行

#### 命令

```bash
#开启事务队列
	multi
#m命令入队
	正常命令
#放弃事务队列
	discard
#执行事务
	EXEC
```

> 编译型异常  命令使用错误，会导致事务结束

> 运行时异常 运行错误的命令跳过 运行正确的继续执行，事务不回滚

#### 监控 (锁)

悲观锁: 什么时候都会加锁

乐观锁:不会上锁，使用版本控制version,对比version    数据库操作时添加version字段   redis使用watch自动监视version             

```bash
#监视
	watch key
	被监视的key在事务中命令进行排队时，另外的客户端对key进行了修改，事务执行时就会失败
#取消对所有key的监视
	unwatch
	如果在执行WATCH命令后，exec命令或discard命令先被执行的话，那么就不需要再执行unwatch了
```

## Jedis

> 导入依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
</dependency>

new jedis()

jedis的事务
multi = jedis.multi();
multi.set().........

关闭连接
jedis.close()
```

## Springboot整合Jedis

> Spring Data 和 Spring Boot齐名

> SpringBoot 2.X之后,原来使用的jedis被替换为了lettuce

> jedis使用的直连，多个线程操作的话不安全，避免安全问题，需要使用jedis线程池

> lettuce:采用netty,实例可以在多个线程中进行共享，不存在线程不安全问题，可以减少线程数据  NIO模式

```java
配置文件
@EnableConfigurationProperties({RedisProperties.class})
 
配置时使用spring.redis.lettuce下的配置，springboot2.X之后默认使用lettuce   redis好多类没被实现
```

```java
@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
       //默认的RedisTemplate没有过多的设置，redis对象都是需要序列化的，以后自定义redisTemplate
        //两个泛型都是Object,使用时需要强转类型<String,Object>
        RedisTemplate<Object, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	//由于String类型经常被使用，所以分离出来一个Bean专门操作String类型数据
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
```

```java
		/**
         * opsForValue()    操作字符串    .方法
         * opsForList()     操作List
         * opsForSet()      操作set
         * opsForHash()     操作Hash
         * opsForZSet()     操作ZSet
         * opsForHyperLogLog()  操作基数统计
         * opsForGeo()      操作地理空间
         * opsForValue().setBit()  操作bitmap
         */

        redisTemplate.opsForValue().set("","");


		/**
         *常用操作可以直接使用redisTemplate操作
         * delete("")
         * discard()
         * exec()
         * multi();
         * move("",1);
         */

        redisTemplate.move("",1);

		//获取连接对象
        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
```

## 自定义RedisTemplate

```java
package com.zwj.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.ObjectMapper.DefaultTyping;
import com.fasterxml.jackson.databind.jsontype.impl.LaissezFaireSubTypeValidator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

/**
 * @Author zhang
 * @create 2020/5/8
 * @Description: 方法是自定义redis的配置类，自定义序列化器
 */
@Configuration
public class RedisConfig {

    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();

        template.setConnectionFactory(connectionFactory);
        //自定义Jackson序列化配置
        Jackson2JsonRedisSerializer jsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, DefaultTyping.NON_FINAL);
        jsonRedisSerializer.setObjectMapper(objectMapper);

        //key使用String的序列化方式
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        template.setKeySerializer(stringRedisSerializer);
        //hash的key也是用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value的key使用jackson的序列化方式
        template.setValueSerializer(jsonRedisSerializer);
        //hash的value也是用jackson的序列化方式
        template.setHashValueSerializer(jsonRedisSerializer);
        template.afterPropertiesSet();

        return template;

    }
}



对象类继承Serializable接口才允许被序列化
```

### 自定义redis工具类

```java
package com.zwj.util;

import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import javax.annotation.Resource;
import java.util.Collection;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@Component
public final class RedisUtil {

	@Resource
	private RedisTemplate<String, Object> redisTemplate;

	// =============================common============================

	/**
	 * 指定缓存失效时间
	 *
	 * @param key  键
	 * @param time 时间(秒)
	 */
	public boolean expire(String key, long time, TimeUnit timeUnit) {
		try {
			if (time > 0) {
				redisTemplate.expire(key, time, timeUnit);
			}
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}

	/**
	 * 根据key 获取过期时间
	 *
	 * @param key 键 不能为null
	 * @return 时间(秒) 返回0代表为永久有效
	 */
	public long getExpire(String key) {
		return redisTemplate.getExpire(key, TimeUnit.SECONDS);
	}


	/**
	 * 判断key是否存在
	 *
	 * @param key 键
	 * @return true 存在 false不存在
	 */
	public boolean hasKey(String key) {
		try {
			return redisTemplate.hasKey(key);
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 删除缓存
	 *
	 * @param key 可以传一个值 或多个
	 */
	@SuppressWarnings("unchecked")
	public void del(String... key) {
		if (key != null && key.length > 0) {
			if (key.length == 1) {
				redisTemplate.delete(key[0]);
			} else {
				redisTemplate.delete((Collection<String>) CollectionUtils.arrayToList(key));
			}
		}
	}


	// ============================String=============================

	/**
	 * 普通缓存获取
	 *
	 * @param key 键
	 * @return 值
	 */
	public Object get(String key) {
		return key == null ? null : redisTemplate.opsForValue().get(key);
	}

	/**
	 * 普通缓存放入
	 *
	 * @param key   键
	 * @param value 值
	 * @return true成功 false失败
	 */

	public boolean set(String key, Object value) {
		try {
			redisTemplate.opsForValue().set(key, value);
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 普通缓存放入并设置时间
	 *
	 * @param key   键
	 * @param value 值
	 * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
	 * @return true成功 false 失败
	 */

	public boolean set(String key, Object value, long time) {
		try {
			if (time > 0) {
				redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
			} else {
				set(key, value);
			}
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 递增
	 *
	 * @param key   键
	 * @param delta 要增加几(大于0)
	 */
	public long incr(String key, long delta) {
		if (delta < 0) {
			throw new RuntimeException("递增因子必须大于0");
		}
		return redisTemplate.opsForValue().increment(key, delta);
	}


	/**
	 * 递减
	 *
	 * @param key   键
	 * @param delta 要减少几(小于0)
	 */
	public long decr(String key, long delta) {
		if (delta < 0) {
			throw new RuntimeException("递减因子必须大于0");
		}
		return redisTemplate.opsForValue().increment(key, -delta);
	}


	// ================================Map=================================

	/**
	 * HashGet
	 *
	 * @param key  键 不能为null
	 * @param item 项 不能为null
	 */
	public Object hget(String key, String item) {
		return redisTemplate.opsForHash().get(key, item);
	}

	/**
	 * 获取hashKey对应的所有键值
	 *
	 * @param key 键
	 * @return 对应的多个键值
	 */
	public Map<Object, Object> hmget(String key) {
		return redisTemplate.opsForHash().entries(key);
	}

	/**
	 * HashSet
	 *
	 * @param key 键
	 * @param map 对应多个键值
	 */
	public boolean hmset(String key, Map<String, Object> map) {
		try {
			redisTemplate.opsForHash().putAll(key, map);
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * HashSet 并设置时间
	 *
	 * @param key  键
	 * @param map  对应多个键值
	 * @param time 时间(秒)
	 * @return true成功 false失败
	 */
	public boolean hmset(String key, Map<String, Object> map, long time, TimeUnit timeUnit) {
		try {
			redisTemplate.opsForHash().putAll(key, map);
			if (time > 0) {
				expire(key, time, timeUnit);
			}
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 向一张hash表中放入数据,如果不存在将创建
	 *
	 * @param key   键
	 * @param item  项
	 * @param value 值
	 * @return true 成功 false失败
	 */
	public boolean hset(String key, String item, Object value) {
		try {
			redisTemplate.opsForHash().put(key, item, value);
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}

	/**
	 * 向一张hash表中放入数据,如果不存在将创建
	 *
	 * @param key   键
	 * @param item  项
	 * @param value 值
	 * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
	 * @return true 成功 false失败
	 */
	public boolean hset(String key, String item, Object value, long time, TimeUnit timeUnit) {
		try {
			redisTemplate.opsForHash().put(key, item, value);
			if (time > 0) {
				expire(key, time, timeUnit);
			}
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 删除hash表中的值
	 *
	 * @param key  键 不能为null
	 * @param item 项 可以使多个 不能为null
	 */
	public void hdel(String key, Object... item) {
		redisTemplate.opsForHash().delete(key, item);
	}


	/**
	 * 判断hash表中是否有该项的值
	 *
	 * @param key  键 不能为null
	 * @param item 项 不能为null
	 * @return true 存在 false不存在
	 */
	public boolean hHasKey(String key, String item) {
		return redisTemplate.opsForHash().hasKey(key, item);
	}


	/**
	 * hash递增 如果不存在,就会创建一个 并把新增后的值返回
	 *
	 * @param key  键
	 * @param item 项
	 * @param by   要增加几(大于0)
	 */
	public double hincr(String key, String item, double by) {
		return redisTemplate.opsForHash().increment(key, item, by);
	}


	/**
	 * hash递减
	 *
	 * @param key  键
	 * @param item 项
	 * @param by   要减少记(小于0)
	 */
	public double hdecr(String key, String item, double by) {
		return redisTemplate.opsForHash().increment(key, item, -by);
	}


	// ============================set=============================

	/**
	 * 根据key获取Set中的所有值
	 *
	 * @param key 键
	 */
	public Set<Object> sGet(String key) {
		try {
			return redisTemplate.opsForSet().members(key);
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}


	/**
	 * 根据value从一个set中查询,是否存在
	 *
	 * @param key   键
	 * @param value 值
	 * @return true 存在 false不存在
	 */
	public boolean sHasKey(String key, Object value) {
		try {
			return redisTemplate.opsForSet().isMember(key, value);
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 将数据放入set缓存
	 *
	 * @param key    键
	 * @param values 值 可以是多个
	 * @return 成功个数
	 */
	public long sSet(String key, Object... values) {
		try {
			return redisTemplate.opsForSet().add(key, values);
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}


	/**
	 * 将set数据放入缓存
	 *
	 * @param key    键
	 * @param time   时间(秒)
	 * @param values 值 可以是多个
	 * @return 成功个数
	 */
	public long sSetAndTime(String key, long time, TimeUnit timeUnit, Object... values) {
		try {
			Long count = redisTemplate.opsForSet().add(key, values);
			if (time > 0) {
				expire(key, time, timeUnit);
			}
			return count;
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}


	/**
	 * 获取set缓存的长度
	 *
	 * @param key 键
	 */
	public long sGetSetSize(String key) {
		try {
			return redisTemplate.opsForSet().size(key);
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}


	/**
	 * 移除值为value的
	 *
	 * @param key    键
	 * @param values 值 可以是多个
	 * @return 移除的个数
	 */

	public long setRemove(String key, Object... values) {
		try {
			Long count = redisTemplate.opsForSet().remove(key, values);
			return count;
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}

	// ===============================list=================================

	/**
	 * 获取list缓存的内容
	 *
	 * @param key   键
	 * @param start 开始
	 * @param end   结束 0 到 -1代表所有值
	 */
	public List<Object> lGet(String key, long start, long end) {
		try {
			return redisTemplate.opsForList().range(key, start, end);
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}


	/**
	 * 获取list缓存的长度
	 *
	 * @param key 键
	 */
	public long lGetListSize(String key) {
		try {
			return redisTemplate.opsForList().size(key);
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}
	}


	/**
	 * 通过索引 获取list中的值
	 *
	 * @param key   键
	 * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
	 */
	public Object lGetIndex(String key, long index) {
		try {
			return redisTemplate.opsForList().index(key, index);
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}


	/**
	 * 将list放入缓存
	 *
	 * @param key   键
	 * @param value 值
	 */
	public boolean lSet(String key, Object value) {
		try {
			redisTemplate.opsForList().rightPush(key, value);
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 将list放入缓存
	 *
	 * @param key   键
	 * @param value 值
	 * @param time  时间(秒)
	 */
	public boolean lSet(String key, Object value, long time,TimeUnit timeUnit) {
		try {
			redisTemplate.opsForList().rightPush(key, value);
			if (time > 0) {
				expire(key, time,timeUnit);
			}
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}

	}


	/**
	 * 将list放入缓存
	 *
	 * @param key   键
	 * @param value 值
	 * @return
	 */
	public boolean lSet(String key, List<Object> value) {
		try {
			redisTemplate.opsForList().rightPushAll(key, value);
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}

	}


	/**
	 * 将list放入缓存
	 *
	 * @param key   键
	 * @param value 值
	 * @param time  时间(秒)
	 * @return
	 */
	public boolean lSet(String key, List<Object> value, long time,TimeUnit timeUnit) {
		try {
			redisTemplate.opsForList().rightPushAll(key, value);
			if (time > 0) {
				expire(key, time,timeUnit);
			}
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 根据索引修改list中的某条数据
	 *
	 * @param key   键
	 * @param index 索引
	 * @param value 值
	 * @return
	 */

	public boolean lUpdateIndex(String key, long index, Object value) {
		try {
			redisTemplate.opsForList().set(key, index, value);
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}


	/**
	 * 移除N个值为value
	 *
	 * @param key   键
	 * @param count 移除多少个
	 * @param value 值
	 * @return 移除的个数
	 */

	public long lRemove(String key, long count, Object value) {
		try {
			Long remove = redisTemplate.opsForList().remove(key, count, value);
			return remove;
		} catch (Exception e) {
			e.printStackTrace();
			return 0;
		}

	}

	// ===============================HyperLogLog=================================

	public long pfadd(String key, String value) {
		return redisTemplate.opsForHyperLogLog().add(key, value);
	}

	public long pfcount(String key) {
		return redisTemplate.opsForHyperLogLog().size(key);
	}

	public void pfremove(String key) {
		redisTemplate.opsForHyperLogLog().delete(key);
	}

	public void pfmerge(String key1, String key2) {
		redisTemplate.opsForHyperLogLog().union(key1, key2);
	}

}
```

## Redis.conf配置文件

> 大小写不敏感

> 网络

```bash
bind 127.0.0.1    #绑定的ip地址本机   *通配符
protected-mode    #保护模式，默认开启，不用动
port = 6379 	  #端口
```

> 通用general

```bash
daemonize yes		#是否开启后台守护线程，后台运行  默认no不开启，设置为yes开启后台运行
pidfile	/var/run/redis_6379.pid			# 如果以守护线程模式启动，就需要一个pid进程文件
loglever notice 	#日志级别:debug/verbose/notice/warning     默认notice生产环境
logfile ""			#日志文件路径 ,默认为空 标准输出
databases 16		#数据库数量，默认16个，从0开始
always-show-logo yes	#是否总是显示logo
```

> 快照snipshotting

```bash
#持久化，在规定的时间内，执行了多少次操作，则会持久化到文件  .rdb   .aof
#redis是内存数据库，不持久化就会失去数据
save 900 1			#900秒内，至少有一次key修改，进行持久化操作
save 300 10			#300秒内，至少有10次key修改，进行持久化操作
save 60 10000		#60秒内，至少有10000次key修改，进行持久化操作
#学习持久化后，会自定义

stop-writes-on-bgsave-error yes		#持久化出错后，是否还继续工作，默认yes工作

rdbcompression yes		#是否压缩rdb文件，需要消耗一些资源

rdbchecksum yes			#保存rdb文件时，进行错误检查校验

dbfilename dump.rdb		#rdb文件名

dir/.				#rdb文件保存目录，默认当前目录下 dir就是当前目录的意思
```

> replication  主从复制  后边讲主从复制

>安全 security

```bash
requirepass 123456   #可以设置redis密码    也可以在客户端中设置  config set requirepass 123456
```

> 客户端clients

```bash
maxclients 10000	#最大客户端设置，一般不设置，有默认值
maxmemory <bytes>	#配置最大内存容量，一般不设置，有默认值
maxmemory-policy noeviction	#内存达到上限后的处理策略,可以自己配置，一般不用
	#移除过期的key
	#.....
```

> append only mode   AOF

```BASH
appendonly no	#默认不开启aof,默认使用rdb,一般rdb就够用了
appendfilename "appendonly.aof"	#持久化文件的名字

#appendsync always	#每一次修改都同步总是执行,消耗性能
appendsync everysec	#每秒执行一次同步，可能会丢失数据
#appendsync no		#不执行，操作系统自动同步
```

## redis持久化

### RDB操作  redis database

```tex
redis会单独创建(fork)一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程全部结束，再将临时文件替换上次持久化好的文件。整个过程中，主进程不进行任何IO操作，保证了极高的性能，如果要对大规模数据进行恢复，且对数据的完整性要求不是很高，那么rdb效率要比aof要高，RDB的缺点就是最后一次持久化的数据可能会丢失，默认使用rdb，一般不需要改变配置
```

> RDB保存的文件是  dump.rdb

```bash
#redis是内存数据库，不持久化就会失去数据
save 900 1			#900秒内，至少有一次key修改，进行持久化操作
save 300 10			#300秒内，至少有10次key修改，进行持久化操作
save 60 10000		#60秒内，至少有10000次key修改，进行持久化操作

#触发rdb条件
save中的设置
flushall触发
关闭redis服务

#如何恢复rdb文件数据
只要将rdb文件放到redis启动的同级目录下，redis启动时会自动扫描dump.rdb文件,恢复数据
```

> 优缺点

```tex
优点:
	-适合大规模的数据恢复
	-对数据的完整性要求不高，效率高
缺点:
	-需要一定时间间隔进行持久化操作，一旦redis宕机，最后一次持久化数据非常容易丢失
	-fork子进程占用一定的内存空间
```

有时候会将rdb文件进行备份，防止数据丢失

![image-20210821195643719](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210821195643719.png)

### AOF操作 append only file

> 将所有执行的命令都记录下来，恢复的时候就将所有命令执行一遍

```tex
以日志的形式来记录每一个写操作，，将redis执行的所有修改操作指令记录下来(读操作不记录),只需追加文件，不许修改文件，redis启动时会读取该文件重构数据，换言之，redis重启的话就根据日志将所有指令重新执行一遍来恢复数据
```

> aof保存的是appendonly.aof

```bash
appendonly no	#默认不开启aof,默认使用rdb,一般rdb就够用了
appendfilename "appendonly.aof"	#持久化文件的名字

#appendsync always	#每一次修改都同步总是执行,消耗性能
appendsync everysec	#每秒执行一次同步，可能会丢失数据
#appendsync no		#不执行，操作系统自动同步
```

> 修复文件

```bash
#如果redis启动服务时，aof文件有错位，即被修改，redis服务无法启动，可以使用redis-check-aof --fix修复
redis-check-aof --fix appendonly.aof
```

> 优缺点

```tex
优点:
	-每一次修改都同步，文件完整性会更好  默认不开启  always
	-每秒同步一次，可能会丢失一秒的数据  everysec
	-从不同步，效率最高
缺点:
	-相对于数据文件大小来说，aof远远大于rdb，修复速度也比rdb慢
	-aof运行效率也要比rdb慢，所以默认rdb持久化
```

> 重写规则

```bash
no-appendfsync-on-rewrite no #默认不开启，当文件达到条件时，是否重写aof文件
	auto-aof-rewrite-percentage 100	#条件一
	auto-aof-rewrite-min-size 64mb #条件二
```

## redis发布订阅

```tex
redis发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送信息，订阅者(sub)接收信息
redis客户端可以订阅任意数量的频道
```

> 订阅/发布信息图

![image-20210822093216271](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210822093216271.png)

> 命令:这些命令被广泛用于构建即时通信应用，比如网络聊天室，实时广播和实时提醒等

```bash
#订阅一个或多个符合给定模式的频道
	psubscribe pattern [pattern...]
#查看订阅与发布系统状态
	pubsub subcommand [argument[argument...]]
#将信息发送到指定的频道
	publish channel message
#退订所有给定模式的频道
	punsubscribe [parttern[pattern...]]
#订阅给定的一个或多个频道的信息
	subscribe channel [channel...]
#退订给定的频道
	unsubscribe channel [channel...]
```

> 测试

```bash
#先订阅信息
	subscribe channeltext
#另一个客户端发布信息
	publish channeltext 123456
#订阅端接收信息
	123456
```

## 主从复制

> 概念

```tex
主从复制，是指将一台redis服务器的数据复制到其他的redis服务器，前者称为主节点(master/leader)后者称为从节点(slave/follower),数据复制是单向的，只能从主节点复制到从节点。Master执行写操作，slave执行读操作，读写分离

一个主节点可以有多个从节点，一个从节点只能有一个主节点
```

> 主从复制的作用

```tex
1.数据冗余:主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式
2.故障恢复:当主节点发生故障时，可以由节点提供服务，实现快速的服务恢复
3.负载均衡:在主从复制的基础上，配合读写分离可以由主节点提供写服务，由从节点提供读服务，分担服务器负担
4.高可用(集群)基石:主从复制是哨兵模式和集群能够实施的基础，因此主从复制是redis高可用的基石
```

### 环境配置

> 只配置从库，不用配置主库，因为redis本身就是主库

```bash
#查看当前库的replication信息
	info replication
```

> 模拟

```bash
开四个服务器客户端，一个用来测试
复制三份redis配置文件
	-设置三个端口号
	-设置pidfile名，防止重复
	-设置logfile名，防止重复
	-设置rdb文件名，防止重复
	
修改后启动三个服务

默认情况下，每一台服务都是主节点
只配置从机到主机就可以

#设置从机命令 认定哪个主机的哪个端口的redis服务为老大(主节点)
	slaveof host port
```

> 配置文件中可以配置

```bash
#配置从机找主机
	replicaof masterip masterport
#配置主机密码
	masterauth master-password
```

> 细节

```tex
主机写，从机只能读，主机中的数据都会被从机保存

主机断开，从机还是连接到主机，只是没办法进行写操作
主机宕机时，可以手动配置新主机，                      哨兵模式会监视自动替换主机

如果使用命令行配置的主从复制，重启就会恢复原样，需要在配置文件配死

从机关闭重启后，会发布同步命令，向主机索要数据
	-全量复制:从机接收到主机数据后，全部保存到内存中
	-增量复制:master继续将新收集到的数据发送给slave，完成同步
```

### 宕机重选主机

```bash
使用链路  主-》从-》从 替换 主-》从从
#主机宕机后，手动配置第一个从机
#或者从配置文件中注释replicaof配死
	slaveof no one
#但是老大回来后，需要重新配置,比较麻烦，使用哨兵模式，主-》从从  自动替换
```

### 哨兵模式(自动切换主机)sentinel

![image-20210822110950115](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210822110950115.png)

```tex
哨兵是一个特殊的模式，哨兵是一个独立的进程，独立运行。原理是哨兵通过向redis服务器发送命令，等待响应，从而监视多个服务器实例
```

![image-20210822111256703](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210822111256703.png)

```tex
监控redis运行状态
发现redis运行状态改变，通过发布订阅通知其他服务器修改配置文件

因为哨兵也可能会宕机，所以有了多哨兵模式，哨兵之间也互相监控
```

![image-20210822111728047](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210822111728047.png)

对新主机选举投票

#### 使用

```bash
1.创建sentinel,conf哨兵配置文件
2.配置文件
	#核心配置，master名字可以随便起，1代表开启投票机制
	sentinel monitor master 127.0.0.1 6379 1
3.启动
	redis-sentinel myconfig/sentinel.conf
4.如果master宕机，会投票随机选择主机
5.如果主机宕机后重启，就会变为新主机的从机
```

> 优缺点

```tex
优点:
	-哨兵模式，基于主从复制，有主从复制的优点
	-主从可以切换，故障可以转移，系统的可用性好
	-哨兵模式是主从复制的升级，从手动到自动
缺点:
	-redis不好在线扩容，一旦集群到达上限，扩容十分麻烦
	-实现真正的哨兵模式的配置十分复杂,有很多限制
```

> 哨兵配置

```bash
# Example   sentinel.conf
# 哨兵sentinel实例运行的端口   默认是26379，如果有哨兵集群，我们还需要配置每个哨兵端口
port 26379

#哨兵sentinel的工作目录
dir /tmp

#哨兵 sentine1 监控的redis主节点的 ip port   
# master-name  ，可以自己命名的主节点名字 只能由字母A-Z、数字0-9、这三个字符"  .   -  _ "组成。
# quorum配置多少个sentine1哨兵统- -认为master主节点失联那么这时客观上认为主节点失联了
# sentine1 monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster   127.0.0.1   6379   2

#当在Redis实例中开启了requirepass foobared 授权密码这样所有连接kedis实例的客户端都要提供密码
#设置哨兵sentinel连接主从的密码注意必须为主从设置- - 样的验证密码
# sentine1 auth-pass <master-name> <password>
sentine1 auth-pass mymaster MySUPER--secret-0123passwOrd

#指定多少毫秒之后主节点没有应答哨兵sentine1 此时哨兵主观上认为主节点下线默认30秒
# sentinel down-after-mi 11i seconds <master-name> <mi 11iseconds>
sentine1 down-after-mi 11iseconds mymaster 30000

#这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行同步，这个数字越小，完成fai lover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而 不可用。可以通过将这个值设为1来保证每次只有一个slave处于不能处理命令请求的状态。
# sentine1 paralle1-syncs <master-name> <numslaves>
sentine1 paralle1-syncs mymaster 1

#故障转移的超时时间failover-timeout 可以用在以下这些方面:
#1.同一个sentine1对同一 个master两次fai lover之间的间隔时间。
#2.当一个slave从一 个错误的master那里同步数据开始计算时间。直到s1ave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。
#4.当进行failover时，配置所有s1aves指向新的master所需的最大时间。不过，即使过了这个超时，slaves 依然会被正确配置为指向master,但是就不按parallel-syncs所配置的规则来了
#默认三分钟
# sentine1 failover-timeout <master-name> <milliseconds>
sentine1 fai lover-ti meout mymaster 180000

# SCRIPTS EXECUTION
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则:
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被-一个SIGKILL信号终止，之后重新执行。

#通知型脚本:当sentine1有任何警告级别的事件发生时(比如说redis实例的主观失效和客观失效等等)，将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等 方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一 个是事件的类型，一个是事件的描述。如果sentine1. conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentine1无法正常启动成功。
#通知脚本
# she11编程
# sentine1 notification-script <master-name> <script-path>
sentine1 notificati on-script mymaster /var/redis/notify. sh

#客户端重新配置主节点参数脚本
#当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
#以下参数将会在调用脚本时传给脚本: 
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
#目前<state>总是“failover",
# <role>是“Teader"或者"observer"中的-一个。
#参数from-ip， from-port， to-ip，to-port是用来和旧的master和新的master(即旧的s lave)通信的
#这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentine1 client-reconfig-script <master-name> <script-path>
sentine1 client-reconfig-script mymaster /var/redis/reconfig.sh #一般都是由运维来配置!
```

## 缓存穿透、雪崩和击穿

### 缓存穿透（查不到）

> 概念

```tex
用户查询数据，redis中没有，于是向数据库查询也没有，查询失败，当用户多的时候，缓存都没有命中，请求都到达数据库，会给数据库造成很大压力，造成数据库崩溃
```

> 解决方案

```tex
布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式进行保存，在控制层先进行校验，不符合则丢弃，从而避免了存储系统的压力
```

![image-20210822114944599](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210822114944599.png)

```tex
缓存空对象，当存储层不命中时，返回空值也短暂缓存起来，之后再获取从缓存中拿，避免了数据库压力

问题:
	-占用缓存空间
	-缓存层和持久层的数据同步不一致，对于保持一致性的业务有影响
```

![image-20210822115302129](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210822115302129.png)

### 缓存击穿（缓存过期，并发访问）

> 概念

```tex
热点数据在过期的瞬间，收到特别大的访问量，直接抵达数据库层，导致数据库压力过大崩溃
```

> 解决方案

```tex
设置缓存用不过期，效率低，缓存容量有限
```

```tex
加互斥锁
分布式锁:使用分布式锁，保证对于每一个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可，这种方式将压力给到了分布式锁，考验分布式锁
```

### 缓存雪崩

> 概念

```tex
缓存雪崩，是指某一个时间段，缓存集中失效，导致所有操作到达数据库
尤其是断电或者断网引起的非自愿雪崩
```

> 解决方案

```tex
多集群
```

```tex
限流降级，加锁或者队列控制对数据库读取的数量
```

```tex
数据预热，提前将可能被访问的数据访问一遍放入缓存，设置不同的过期时间
```

