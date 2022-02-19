# JVM

## 什么是垃圾，怎么定位垃圾（野指针，并发问题）

![image-20220217162141625](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220217162141625.png)

![image-20220217163329057](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220217163329057.png)

```tex
每个线程都会有一个自己维护的栈内存，每调用一个方法，就会在栈中创建一个栈帧，当方法执行完后，会自动弹出，不需要手动清理
堆内存中放置new出来的对象，不会自动清理,堆内存是公共的，不是单个线程自己的
```

```bash
# 野指针
- 两个指向对象的指针，一个释放了，另一个不知道拿来用
- 同一个指针，不同位置
- 不再指向任何对象的指针
- NullPointExcetion空指针
# 并发问题
- 多线程指向同一块内存
```

```bash
# 内存泄漏
	有一块内存没有被删除，也没办法访问
# 内存溢出
	多个内存泄漏导致内存空间被占用，导致内存不够用
```

## GC(Garbage Collection)垃圾收集器

```tex
应用线程只管分配内存，垃圾回收器线程负责回收
```

![image-20220217165913226](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220217165913226.png)

```bash
# 什么是垃圾
	- 没有任何引用的内存区域内的内容就是垃圾
	- 顺着根往下找，找不到的就是垃圾
# gc 引用计数法
	每增加一条引用就增加1,减少一条引用就减1
# 清除垃圾
	1.Mark-Sweep 标记清除  标记垃圾然后清除    缺点： 内存碎片化
	2.Copying  拷贝清除   将内存分为两部分，将一部分的不需要清除的部分拷贝到另一部分，然后将原来的部分全部删除    缺点 ： 浪费内存
	3.Mark-Compact  标记整理 清除垃圾时顺便整理  缺点：效率低
# 针对每种垃圾回收算法，将内存划分为多个区域
	年轻代 eden survivor
	老年代 tenured
	gc规则：
		1.创建的对象会被放在eden区，当经历15次gc后还没被嘎调，就会转移到老年代
		2.新生代gc规则：
			- 第一次gc会将eden区中不会被清除的对象拷贝到survivor1,将eden区全部清除
			- 之后gc会将eden区和其中一个有数据的survivor区中的数据不会被清除的拷贝到另外一个survivor中，将原来有数据的区域全部清除
	
```

![image-20220217172804984](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220217172804984.png)

==垃圾回收器的迭代(背)==

![image-20220217172557476](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220217172557476.png)

```bash
随着内存的增加升级
1.Serial 单线程 STW  停止当前业务，清理垃圾  初代，内存小的时候    年轻代叫Serial  老年代叫Serial Old
2.Parallel 多线程 STM 停止当前业务，清理垃圾  内存扩大了，单线程不行了   年轻代叫Parallel  老年代叫Parallel Old
```

```bash
# 查询当前版本JDK自带的垃圾回收器版本
java -XX:+PrintCommandLineFlags -version
```

![image-20220217175124040](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220217175124040.png)