# RabbitMQ

## 概念

```tex
mq(message queue),本质是个队列，存放message的队列，跨进程的通信机制，用于上下游的信息传递
```

## 功能

### 流量削峰

```tex
系统同一时间只能处理一万订单量，当订单两万单是无法处理，使用消息队列做缓冲，将订单量分段
```

### 应用解耦

```tex
正常情况下，有一个系统故障，订单系统就无法完成，使用消息队列，将需要处理的内存缓存在消息队列中，当故障中断后，继续执行，用户没有中断体验
```

### ![image-20211115103256597](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115103256597.png)

### 异步处理

```tex
负责接收消息和发送消息
```

![image-20211115103753184](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115103753184.png)

## MQ分类

### activeMQ

![image-20211115103918910](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115103918910.png)

### kafka

![image-20211115104033476](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115104033476.png)

### RocketMQ

![image-20211115104323339](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115104323339.png)

### RabbitMQ

![image-20211115104513464](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115104513464.png)

## MQ选择

![image-20211122081504048](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211122081504048.png)

## RabbitMQ

### 概念

```tex
RabbitMQ是一个消息中间件，它接收并转发信息。可以把它当作一个快递站点，接收，存储，转发

AMQP(Advanced Message Queuing Protocol 高级消息队列协议),是一个网络协议，是应用层协议的一个开放标准，为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言的限制。2006年，AMQP规范发布，类比http
```

### 四大核心

```tex
生产者，交换机，队列，消费者
```

![image-20211122082324075](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211122082324075.png)

### 六大核心模式

![image-20211122082808798](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211122082808798.png)

```tex
简单模式、work queues、Publish/Subscribe发布与订阅模式、Routing路由模式、Topics主题模式、RPC远程调用模式 
```

![image-20220107131306442](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220107131306442.png)

### 原理

![image-20220107130844300](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220107130844300.png)

```tex
Broker:接收和分发消息的应用，RabbitMQ Server就是Message Broker

Virtual host:出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，类似网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ Server 提供的服务时，可以划分出多个Vhost,每个用户在自己的vhost创建exchange/queue等

Connection:publisher/consumer和broker之间的TCP连接

Channel:如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。channel是在connection内部建立的逻辑连接，如果应用程序支持多线程，通常每个Thread创建单独的channel进行通讯，AMQP method包含了channel id帮助客户端和message broker识别channel，所以channel之间是完全隔离的。channel作为轻量级的connection极大的减少了操作系统建立TCP connection的开销

Exchange:message到达broker的第一站，根据分发规则，匹配查询表中的routing key,分发消息到queue中去，常用的类型有：direct(point-to-point),topic(publish-subscribe) and fanout(multicast)

Binding:exchange和queue之间的虚拟连接，binding中可以包含routing key.Binding信息被保存到exchange中的查询表中，用于message的分发依据
```

> JMS

```tex
JMS即Java消息服务(JavaMessage Service)应用程序接口，是一个Java平台中关于面向消息中间件的API
```

![image-20220107131556883](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220107131556883.png)

### 安装

![image-20211122093247782](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211122093247782.png)

![image-20211122093720108](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211122093720108.png)

## Hello World

```tex
生产者  -》 队列 -》 消费者

防火墙  
	状态 systemctl status firewalld
	停用 systemtcl stop firewalld
	持久化当前状态 systemctl enable firewalld
```

## Work Queues

```tex
工作队列(又称任务队列)的主要思想时避免立即执行资源密集型任务，而不得不等待它完成。
相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列，在后台运行的工作
进程将弹出任务并最终执行作业，当有多个线程时，这些工作线程将一起处理这些任务
```

![image-20220108110521771](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220108110521771.png)

> 轮循分发任务

```tex
在这个案例中我们会启动两个工作线程，一个消息发送线程
```

### 消息应答

> 概念

```tex
消息应答就是消费者在接收到消息并且处理该消息后，告诉rabbitmq他已经处理完了，rabbitmq才可以把消息删除。防止信息的丢失
```

> 自动应答

```tex
消息发送后立即被认为已经传送成功，这种模式需要在 高吞吐量和数据传输安全性方面做权衡，因为这种模式如果消息在接受之前，消费者那边出现连接或channel关闭，那么消息就丢失了，另一方面，消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，有可能使消费者由于接收太多还来不及处理的消息，导致信息的积压，最终内存耗尽，被操作系统杀死，所以这种模式仅适用在消费者可以高效以某种速率处理这些消息的情况下使用
```

> 消息应答的方法

```java
Channel.basicAck  //用于肯定确认 告诉rabbitmq可以删除信息了
Channel.basicNack //用于否定确认 第三个参数不处理该消息了可以直接拒绝，可以将其丢弃了
Channel.basicReject  //用于否定确认 比Nack少一个参数，第二个参数不处理该消息了可以直接拒绝，可以将其丢弃了   缺少批量处理参数
```

> multiple批量处理参数

```tex
multiple的true和false代表不同意思
	true代表批量应答channel上未应答的消息
		比如说channel上有传送tag的消息5，6，7，8 当前tag是8 那么此时5-8的这些还未应答的消息都会被确认收到消息应答
	false 与上边相比
		只会应答tag=8的消息，其他不会被应答
```

> 消息自动重新入队

```tex
如果消费者由于某些原因失去连接(其通道已关闭，连接已关闭或TCP连接丢失),导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，并将对其重新入队，如果此时其他消费者可以处理，他将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息
```

> 手动应答实现

![image-20220108122540636](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220108122540636.png)

### RabbitMQ持久化

> 概念

```tex
确保RabbitMQ服务停掉以后消息生产者发送过来的信息不丢失，默认情况下RabbitMQ退出或某种原因崩溃时，它忽视队列和消息，所以需要将队列和消息持久化
```

> 队列如何实现持久化

```tex
声明队列的时候将durable参数设置为ture
但之前声明的同名队列的属性不会变，所以会报错，应该将之前的队列先删除
```

![image-20220108130947762](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220108130947762.png)

> 消息持久化

```tex
要想让消息实现持久化需要在消息生产者修改代码，MessageProoerties.PERSISTENT_TEXT_PLAIN添加这个属性
将消息标记为持久化并不能完全保证不会丢失信息。尽管它告诉RabbitMQ将信息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候，但还没有存储完，消息还在缓存的一个间隔点。此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，已经足够了
```

![image-20220108133514712](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220108133514712.png)

> 不公平分发

```tex
当两个消费者，一个干活快，一个干活慢的时候，如果采用轮询分发就会浪费干活快的资源
为了避免这种情况，我们可以设置参数  channel.basicQos(1)    0公平(默认)  1不公平
```

> 预取值

```tex
预取值，就是每个消费者之前消息排队的个数
本身消息的发送就是异步发送的，所以channel上的消息肯定不止一条，另外，消费者的手动确认本质上也是异步的，因此就存在一个未确认的消息缓冲区，因此希望开发人员能限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。
通过basicQos()方法设置 预取计数 值来完成的。该值定义通道上允许的未确认消息的最大数量，一旦数量达到配置的数量，RabbitMQ将停止接收更多的消息，除非有一条消息被确认，才会再接收一条
消息应答和Qos预取值对用户吞吐量有重大影响，增加预取值将提高向消费者传递消息的速度，虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的RAM消耗(随机存取存储器)，应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值时一个反复实验的过程，不同的负载取值也会不同。100-300范围内的值通常可提供最佳吞吐量，并且不会给消费者带来太大风险，预取值为1是最保守的，当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中，对于大多数应用来说，稍微高一点的值将是最佳的
```

## 发布确认

```tex
生产者将信道设置成confirm模式，一旦信道被设置成confirm模式，所有在信道上的消息都会被指派一个唯一的ID(从1开始)，一旦消息被投递到所有匹配的队列后，broker就会发送一个确认给生产者(唯一id),如果是可持久化的，就会在写入磁盘后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外，broker也可以设置basicAck的multiple域，表示到这个序列号之前的所有信息都已经得到了处理

confirm是异步的，生产者可以在等确认的同时发送下一条消息，收到确认消息后，生产者可以通过回调方法处理确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息
```

> 发布确认的策略

```tex
1.开启发布确认的方法
	发布确认默认是没有开启的，如果要开启需要调用方法 channel.confirmSelect
```



```tex
2.单个确认发布
	同步确认发布形式，只有发布一条消息得到确认后才可以发布下一条消息 
	waitForConfirmOrDie(long) 这个方法只有在消息被确认的时候才返回，如果在指定时间内这个消息没有被确认将抛出异常
	
	缺点：发布速度慢，这种方式每秒最多提供数百条吞吐量，对于某些应用程序来说已经够了
```



```tex
3.异步确认发布
	利用回调方法来达到消息可靠性的传递
	
	给每条消息设置一个序号，出问题的会将消息序号返回，不需要等待，异步返回
	
	如何处理异步未确认消息
        	最好的解决方案是把未确认的消息放到一个基于内存的能被发布线程访问的队列，比如说用ConcurrentLinkedQueue这个队列在confirm callbacks与发布线程之间进行消息的传递
```

![image-20220109113431602](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220109113431602.png)

## 交换机

```tex
将消息传达给多个消费者，这种模式称为"发布/订阅"
为了说明这种模式，构建一个简单的日志系统，由两个程序组成，第一个程序将发出日志消息，第二个程序是消费者，其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储到磁盘，另一个接收到消息后打印在控制台
```

![image-20220110113315767](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110113315767.png)

> 概念

```tex
RabbitMQ消息传递模型的核心思想是:生产者生产的消息从不会直接发送到队列。实际上，通常生产者甚至不知道这些消息传递到了哪个队列

不设置队列也会有默认交换机

生产者只能将消息发送到交换机。交换机工作的内容非常简单，接收消息，推入队列。交换机必须确切知道如何处理这些消息，是应该放到队列还是丢弃，由交换机的类型决定
```

> 交换机类型

```tex
直接(direct)(路由)、主题(topic)、标题(headers)、 扇出(fanout)(发布订阅)
```

> 无名exchanges（默认）

```tex
默认交换机，使用 ("")空串 识别
channel.basicPublish("","hello",null,message.getBytes())
参数:1.交换机名称；消息能路由发送到队列中其实是由routingKey(bindingKey)绑定key指定的，如果它存在的话
```

> 临时队列

```tex
每当我们连接Rabbit时，我们都需要一个全新的队列，为此我们可以创建一个具有随机名称的队列，或者让服务器为我们选择一个随机名称，其次，一旦我们断开了消费者连接，队列将自动删除
创建临时队列
	String queueName = channel.queueDeclare().getQueue();
```

> 绑定

```tex
exchanges和queue之间的桥梁
```

### fanout（发布订阅）

> 介绍

```tex
Fanout这种类型非常简单，它是将接收到的所有消息广播到它知道的所有队列中。系统中默认有些exchange类型
```

![image-20220110120012021](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110120012021.png)

> fanout实战

![image-20220110120125326](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110120125326.png)

### direct （路由模式）

> 介绍

```tex
绑定的routingkey不同时，每个消费者接收不同的日志消息，消息只去到它绑定的routingkey队列中去    （路由模式）

在下面图中，我们可以看到X绑定了两个队列，绑定类型是direct.队列Q1绑定键为orange,队列Q2绑定键有两个：一个绑定键为black,另一个绑定键为green
```

![image-20220110122624751](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110122624751.png)

> 多重绑定

![image-20220110122937309](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110122937309.png)

```tex
如果交换机在direct类型下，但是它绑定的多个队列的key相同，它的表现就和fanout有点类似了
```

> 实战

![image-20220110123101119](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110123101119.png)

### topic

> 介绍

```tex
topic交换机的routingkey不能随便写，
必须是一个单词列表，以点号分隔
例如：stock.usd.nyse
最多不能超过255字节

在这个规则列表中，有两个替换符
	* 可以代替一个单词
	# 可以替代0个或多个单词
	
例子:
	中间带orange的三个单词的字符串  *.orange.* 可以给中间带orange的队列都发消息
	
	*.*.rabbit
	lazy.#	lazy.开头的routingkey绑定的队列都发消息
	
*注意：当一个队列绑定键是#，那么这个队列将接收所有数据。就有点像fanout了
		如果队列绑定键当中没有#和*出现，那么该队列绑定类型就是direct了
```

![image-20220110131128371](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110131128371.png)

## 死信队列

> 概念

```tex
死信:无法被消费的消息
消费者在队列中取出消息时，由于某些原因导致queue中的某些消息不能被消费，这些消息如果没有后续的处理，就成了死信，有死信自然就有了死信队列

为了确保订单业务的消息数据不丢失，需要使用到rabbitMQ的死信队列机制，当消息消费发生异常时，将消息投入死信队列中
或者当用户下单后未支付自动失效的功能
```

> 来源

```tex
消息TTL过期
队列达到最大长度(队列满了，无法再添加数据到mq中)
消息被拒绝并且requeue = false
```

> 实战

![image-20220110135231365](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110135231365.png)

## 延迟队列

> 概念

```tex
延迟队列内部是有序的，延迟队列是希望队列中的元素到指定时间再取出或处理
```

> 使用场景

```tex
订单支付，未支付取消
新创建的商铺，十天内没有上传过商品，则自动发送消息提醒
用户注册后，三天未注册则短信提醒
用户发起退款后，一定时间内没有处理，联系相关人员
预定会议后，在时间点之前10分钟通知
```

### 队列TTL

> 架构图

```tex
创建两个队列QA和QB，两者队列TTL分别设置为10s和40s,然后再创建一个交换机X和死信交换机Y，它们的类型都是direct,创建一个死信队列QD
```



![image-20220110171335608](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110171335608.png)

### 插件延迟  （交换机延迟）

```tex
https://www.rabbitmq.com/community-plugins.html
rabbitmq_delayed_message_exchange
基于插件的延迟交换机，将数据保存到分布式表中进行延迟，时间到了将信息发布到队列
解决ttl队列不能按时间发布消息的缺点，ttl队列必须等第一个消息延迟完毕，才能进行第二个消息
```

## 发布确认高级

```tex
在生产环境中由于一些不明原因，导致rabbitmq重启，在RabbitMQ重启期间生产者消息投递失败，导致信息丢失，需要手动处理和修复，所以，如何进行rabbitmq的消息可靠投递呢？特别是在这样比较极端的情况，RabbitMQ集群不可用的时候，无法投递的消息该如何处理
```

![image-20220110193222353](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110193222353.png)

> 代码架构图

![image-20220110193239304](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110193239304.png)

> 配置文件

```bash
在配置文件中需要添加
	#启用交换机发布确认回调接口
	spring.rabbitmq.publisher-confirm-type=correlated
	
	参数：
		none 禁用发布确认模式，是默认值
		currelated 发布消息成功到交换器后回触发回调方法
		simple
			两种效果
			1.和correlated一样回触发回调方法
			2.发布消息成功后使用rabbitTemplat调用waitForConfirms或waitForConfirmsOrDie方法等待broker节点返回发送结果，根据结果来判定下一步的逻辑，要注意的是，waitForConfirmsOrDie方法如果返回false则会关闭channel,则接下来无法发送消息到broker 
```



> 回退消息

```bash
Mandatory参数
	在仅开启了生产者确认机制的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息，如果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道的，
	所以通过mandatory参数将不可达目的的消息返回给生产者
	
#开启发布回退
spring.rabbitmq.publisher-returns=true
```

```java
package com.zwj.springbootrabbitmq.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.ReturnedMessage;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;

/**
 * @author zwj
 * @date 2022/1/10 - 20:32
 *	发布确认高级回调方法
 */
@Slf4j
@Component
public class MyCallBack implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnsCallback{


    @Autowired
    private RabbitTemplate rabbitTemplate;

    //注入   函数式接口需要注入，才能用接口调用实现类
    @PostConstruct
    public void init(){
        //注入
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnsCallback(this);
    }

    /**
     * 交换机回调方法
     * 1.发消息 交换机接收到了 回调
     *      1.1 correlationData 保存回调信息的ID及相关信息
     *      1.2 交换机收到信息 ack = true
     *      1.3 cause null
     * 2.发消息 交换机接收失败了 回调
     *      2.1 correlationData 保存回调信息的ID及相关信息
     *      2.2 交换机收到消息 ack = false
     *      2.3 cause 失败的原因
     * */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack){
            log.info("交换机已经收到了id为：{}的消息",id);
        }else {
            log.info("交换机还未收到id为：{}的消息，由于原因：{}",id,cause);
        }

    }

    //只有在当消息传递过程中不可达目的地是将消息返回给生产者
    //只有不可达目的地的时候才进行回退

    @Override
    public void returnedMessage(ReturnedMessage returned) {
        log.error("消息{},被交换机{}退回，退回原因:{},路由key:{}",
                new String(returned.getMessage().getBody()),returned.getExchange(),returned.getReplyText(),
                returned.getRoutingKey());
    }
}
```

> 备份交换机

![image-20220110214717710](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110214717710.png)

```tex
备份交换机优先级高于消息回退
```

```java
package com.zwj.springbootrabbitmq.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author zwj
 * @date 2022/1/10 - 20:12
 * 发布确认高级
 */
@Configuration
public class ConfirmConfig {

    //交换机
    public static final String CONFIRM_EXCHANGE_NAME = "confirm_exchange";
    //队列
    public static final String CONFIRM_QUEUE_NAME = "confirm_queue";
    //routingKey
    public static final String CONFIRM_ROUTING_KEY = "key1";
    /**
     * */
    //备份交换机
    public static final String BACKUP_EXCHANGE_NAME = "backup_exchange";
    //备份队列
    public static final String BACKUP_QUEUE_NAME = "backup_queue";
    //报警队列
    public static final String WARNING_QUEUE_NAME = "warning_queue";

    //声明交换机
    @Bean
    public DirectExchange confirmExchange(){
//        return new DirectExchange(CONFIRM_EXCHANGE_NAME);
        //转向其他交换机
        return ExchangeBuilder.directExchange(CONFIRM_EXCHANGE_NAME).
                withArgument("alternate-exchange",BACKUP_EXCHANGE_NAME).build();
    }

    //声明队列
    @Bean
    public Queue confirmQueue(){
        return new Queue(CONFIRM_QUEUE_NAME);
    }

    //绑定
    @Bean
    public Binding queueBindingExchange(@Qualifier("confirmQueue") Queue confirmQueue,
                                        @Qualifier("confirmExchange") DirectExchange confirmExchange){
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with(CONFIRM_ROUTING_KEY);
    }

    //声明备份交换机
    @Bean
    public FanoutExchange backupExchange(){
        return new FanoutExchange(BACKUP_EXCHANGE_NAME);
    }

    //声明备份队列
    @Bean
    public Queue backupQueue(){
        return new Queue(BACKUP_QUEUE_NAME);
    }

    //声明报警队列
    @Bean
    public Queue warningQueue(){
        return new Queue(WARNING_QUEUE_NAME);
    }

    //绑定
    @Bean
    public Binding backupQueueBindingBackupExchange(@Qualifier("backupQueue") Queue backupQueue,
                                                    @Qualifier("backupExchange") FanoutExchange backupExchange){
        return BindingBuilder.bind(backupQueue).to(backupExchange);
    }

    @Bean
    public Binding warningQueueBindingBackupExchange(@Qualifier("warningQueue") Queue warningQueue,
                                                    @Qualifier("backupExchange") FanoutExchange backupExchange){
        return BindingBuilder.bind(warningQueue).to(backupExchange);
    }

}

```

## 其他知识点

### 幂等性

> 概念

```tex
用户对于同一操作发起的一次请求或多次请求的结果是一致的，不会因为多次点击而产生副作用
比如支付，用户支付后，返回结果时网络异常，此时钱已经扣了，用户第二次点击，进行第二次扣款，返回结果成功
以前的单应用系统中，我们只需要把数据操作放入事务中即可，发生错误立即回滚

消费者在消费MQ中的消息时，MQ已经将消息发送给消费者，消费者在给MQ返回ack时网络中断，故MQ未收到确认消息，该条消息会重新发给其他的消费者，或者在网络重连后发送给消费者，但实际上该消费者已成功消费了该条消息，造成消费者消费了重复的消息
```

> 解决思路

```tex
MQ消费者的幂等性的解决一般使用全局ID或者写个唯一标识比如时间戳，或者UUID或者订单消费者消费MQ中的消息也可利用MQ的该id来判断，或者可按自己的规则生成一个全局id,每次消费消息时用该id先判断该消息是否已消费过
```

> 消费端的幂等性保障

```tex
重复提交保证幂等性的常用两种操作
1.唯一ID+指纹码机制，利用数据库主键去重
	指纹码：我们的一些规则或者时间戳加别的服务给到的唯一信息码，它并不一定时系统生成的，基本都是由我们的业务规则拼接而来，但是一定要保证唯一性，然后就利用查询	语句进行判断这个id是否存在数据库中，优势是简单拼接就可以查重，劣势是高并发下，性能差
2.利用redis原子性去实现
	利用redis执行setnx，天然具有幂等性，从而实现不重复消费
```

### 优先级队列

> 使用场景

```tex
订单催付
	天猫或淘宝及时将订单推送给我们，如果用户在设定时间内未付款就会给用户推送一条短信提醒
	但是商家对于淘宝天猫来说分为大商家和小商家，大商家的订单要优先处理
	redis可以做定时轮询，但是没有优先级
	所以可以用rabbitmq设置优先级
	优先级队列(0-255)越大越优先执行
```

![image-20220110223646109](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110223646109.png)

> 代码

![image-20220110223719581](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110223719581.png)

```tex
注意： 要让队列实现优先级需要做的事情
	队列需要设置为优先级队列，消息需要设置消息的优先级，消费者需要等待消息已经发送到队列中才去消费，因为这样才有机会进行排序
```

```java
		//设置优先级
        Map<String,Object> arguments = new HashMap<>();
        arguments.put("x-max-priority",10);   //不要设置过大，浪费内存
		channel.queueDeclare(QUEUE_NAME,false,false,false,arguments);

		AMQP.BasicProperties properties = new
                        AMQP.BasicProperties().builder().priority(5).build();
        channel.basicPublish("",QUEUE_NAME,properties,message.getBytes(StandardCharsets.UTF_8));
```

### 惰性队列

```tex
正常情况：消息保存在内存上
惰性队列：消息保存在磁盘上
```

> 应用场景

```tex
数据量大，消费者宕机，存储在磁盘上

args.put("x-queue-mode","lazy")
```

![image-20220110225113947](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110225113947.png)

> 内存开销对比

![image-20220110225324790](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220110225324790.png)

## RabbitMQ集群

### 搭建集群

> 修改名称

```tex
1.修改3台电脑的主机名称
	vim /etc/hostname
2.配置各个节点的hosts文件，让各个节点都能互相识别对方 每个节点都写
	vim /etc/hosts
		ip1 node1
		ip2 node2
		ip3 node3
3.以确保各个节点的cookie文件使用的是同一个值
	在node1上执行远程操作命令
	scp /var/lib/rabbitmq/.erlang.cookie root@node2:/var/lib/rabbitmq/.erlang.cookie
	scp /var/lib/rabbitmq/.erlang.cookie root@node3:/var/lib/rabbitmq/.erlang.cookie
4.启动RabbitMQ服务，顺带启动Erlang虚拟机和RabbitMQ应用服务(在三台节点上分别执行)
	rabbitmq-server -detached
5.在节点2执行
	rabbitmqctl stop_app
	(rabbitmqctl stop 会关闭erlang虚拟机 ，rabbitmqctl stop_app只关闭rabbitmq服务)
	rabbitmqctl reset
	rabbitmqctl join_cluster rabbit@node1
	rabbitmqctl start_app(之启动应用服务)
6.在节点3执行
	rabbitmqctl stop_app
	(rabbitmqctl stop 会关闭erlang虚拟机 ，rabbitmqctl stop_app只关闭rabbitmq服务)
	rabbitmqctl reset
	rabbitmqctl join_cluster rabbit@node2
	rabbitmqctl start_app(之启动应用服务)
7.集群状态
	rabbitmq cluster_status
8.需要重新设置用户
	创建账号
		rabbitmqctl add_user admin 123
	设置用户角色
		rabbitmqctl set_user_tags admin administrator
	设置用户权限
		rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"
9.解除集群节点(节点2和节点3 都执行)
	rabbitmqctl stop_app
	rabbitmqctl reset
	rabbitmqctl start_app
	rabbitmqctl cluster_status  //检查状态
	rabbitmqctl forget_cluster_node rabbit@node2 (node1上执行)
```

