# Redis2

## redis6事务操作和锁机制

### 定义

```tex
Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
Redis事务的主要作用就是串联多个命令防止别的命令插队
```



### Multi,Exec,discard

```tex
从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，知道输入Exec后，Redis会将之前的命令队列中的命令依次执行
组队的过程中可以通过discard来放弃组队
```

![image-20210513120833164](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210513120833164.png)

### 事务的错误处理

```tex
组队中某个命令出现了错误报告，执行时整个所有队列都会被取消
```

![image-20210513121337636](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210513121337636.png)

```tex
如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚
```

![image-20210513121523523](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210513121523523.png)

### 事务冲突问题（悲观锁和乐观锁）

#### 悲观锁

```tex
悲观锁(Pessimistic Lock),顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block(阻塞)直到拿到锁，传统的关系型数据库里面就用到了很多这种机制，比如行锁，表锁等，读锁，写锁等，都是在操作前先上锁
```

![image-20210513123100123](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210513123100123.png)

#### 乐观锁

```tex
乐观锁(Optimistic Lock),顾名思义，就是很乐观，每次拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号控制。乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种
check-and-set机制实现事务的
```



![image-20210513123240517](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210513123240517.png)

##### WATCH key [key...]

```tex
在执行multi之前，先执行watch key1 [key2],可以监视一个或多个key,如果在事务执行之前这个或这些key时被其他命令所改动，那么事务将被打断
```

watch对key加乐观锁

两个终端multi创建事务

一个终端改变key后，另一个终端再修改就会报错，因为版本号不同了

##### unwatch

```tex
取消WATCH命令对所有key的监视
如果在执行WATCH命令后，exec命令或discard命令先被执行的话，那么就不需要再执行unwatch了
```

### Redis事务三特性

```tex
1、单独的隔离操作
	事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
2、没有隔离级别的概念
	队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
3、不保证原子性
	事务中如果有一条命令执行失败，其后的命令仍然会被执行，不会回滚
```



### Redis事务秒杀案例