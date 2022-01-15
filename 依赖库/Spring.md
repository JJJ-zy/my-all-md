# Spring

## IOC控制反转

### 概念

```tex
是一个理论，把对象的一系列操作交给容器来执行
控制：控制对象的赋值和对象之间的关系
反转：将对象的创建和赋值交给容器
正转：由开发人员，使用new方法创建对象，主动操作对象

减少对代码的改动，解耦合
```

### 技术实现

```tex
DI:依赖注入，只需要在程序中提供需要使用的对象名即可，不需要知道内部的创建过程
```

## 自动注入

```tex
autoWired自动注入
属性：
byName:属性名和bean id名一样时，自动将bean注入到属性中
byType:类中的属性的类型和bean的类型时一样的,或者是同源关系，即父子类或接口和实现类
```

## 组件扫描

```tex
context:component-scan   可以用分号或者逗号分隔包名，扫描多个包    也可以用父包名
context:property-placeholder:加载属性文件
```

## 注解

```tex
@Component
@repository
@service
@controller
```

#### 属性赋值

```tex
@Value() 简单类型赋值，是String类型的，可以在属性上赋值，也可以在set上赋值
@AutoWired()自动注入  默认byType
@AutoWired+@Qualifier(value="beanId") byName

AutoWired属性
required = true   表示引用类型赋值失败，程序中断执行
required = false  表示引用类型复制失败，程序继续执行

@Resource  jdk注解，优先使用byName,如果没有使用byType,和AutoWired功能一样
如果想只是用byName:属性name="bean"
```



# springAop

## 动态代理

```tex
InvocationHandler接口实现类写新增功能
proxy.new代理对象 （类加载器，类加载器，InvocationHander）
method.getName()

jdk动态代理，使用jdk中的Proxy,Method,InvocationHandler创建代理对象，jdk代理目标对象必须实现接口

cglib动态代理:第三方的工具库，创建代理对象，原理是继承，通过继承目标类创建代理对象，子类就是代理对象，要求目标类不能是final
```

### cglib

```tex
有接口不可以使用cglib代理

如果有接口的时候使用cglib代理，需要在配置文件中配置aop
<aop:aspectJ-autoproxy proxy-target-class="true" />
```



### 作用

```tex
不改变源代码的情况下，增加新功能
减少代码冗余
专注业务逻辑代码
解耦合，让业务功能和日志，事务非业务功能分离
```

## AOP

### 概念

```tex
面向切面编程，基于两种动态代理，Aop就是动态代理的规范化，将动态代理的实现步骤定义好了，统一的方式使用动态代理
```

### Aspect

```tex
切面，给方法增加的功能就叫切面
切面的特点，一般都是非业务方法，独立使用
```

### Orient

```tex
面向
```

### Programming

```tex
编程
```

### 怎么理解切面编程

```tex
需要分析功能需求，找出切面
合理的安排切面的执行位置
合理的安排切面执行在哪个类
```

### 术语

```tex
Aspect:切面，表示增强的功能，就是一堆代码，完成某个功能，非业务代码，常见切面功能:日志，事务，统计信息，参数检查，权限验证
JoinPoint:连接点:连接业务方法和切面的位置，某个类中的业务方法
Pointcut:切入点，加入切面功能的连接点
目标对象：给哪个类的方法增加功能，这个类就是目标对象
Advice:通知，通知表示切面功能执行的时间
```

### 实现

```tex
spring:spring内部实现了aop规范，主要处理事务
aspectJ:专门做aop的框架，使用方便，使用多，spring中集成了aspectJ
aspectJ实现aop的两种方式:
使用xml文件:配置全局事务
使用注解:增加aop功能
```

### aspectJ框架的使用

#### 通知

```tex
实际增强的逻辑部分称为通知,切面的执行时间
注解实现:
@Before
@AfterReturning
@Around
@AfterThrowing
@After
```

##### 前置通知

```tex
@before 没有返回值
```

##### 后置通知

```tex
@AfterReturning
属性:value:execution表达式
	returning="变量名"
有参数
能够获取目标对象的返回值到变量名中，方法中定义object 变量名属性，方法体中可以使用目标对象返回值

没有返回值
```

##### 环绕通知

```tex
@Around  和jdk动态代理相似
属性:value:execution表达式
	位置:在目标对象中执行的位置
	
功能最强
目标方法前后都能增强
控制目标方法是否被调用
修改原来目标方法的执行结果

参数ProceedingJoinPoint pj 相当于jdk动态代理中的Method，执行目标方法   proceedingJoinPoint继承JoinPoint

有返回值，就是目标方法的返回结果，可以被修改

pj.proceed()相当于method.invoke()
```



#### 切入点表示语法

```tex
execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))

execution(* com.zwj.cc.UserDao.add(参数))  *代表返回类型，权限修饰符可以省略
		
execution(* com.zwj.cc.UserDao.*())  增强所有方法

execution(* com.zwj.cc.*.*()) 增强包下所有类中的所有方法

```

#### 使用方法

```tex
导入aspectJ依赖包

创建目标对象接口和实现类
创建切面类
使用@Aspect注解声明切面类
使用五大通知
配置文件中创建目标对象和切面
声明自动代理配置器，创建代理对象<aop:aspectJ-autoproxy />


目标对象有接口时，优先使用jdk动态代理，没有接口的情况下使用cglib动态代理
```

##### JoinPoint

```tex
指定通知方法中的参数：joinPoint
获取业务功能方法的信息到切面方法中
public void myBefore(JoinPoint jp（必须放到第一个参数位置）){
	jp.getSignature().getName()  目标方法名称
}
```

##### pointcut

```tex
定义和管理切入点，重复的切入点表达式，可以使用pointcut进行定义

属性:
value:切入点表达式
位置:在自定义方法上边

方法上边有@pointcut注解，方法名就是切入点表达式的别名
```

![image-20210726091146592](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210726091146592.png)

# spring-mybatis

## 主要步骤

```tex
1.创建maven工程
2.导入spring依赖，mybatis依赖，spring-mybatis依赖，junit依赖，mysql依赖,spring-jdbc依赖，log4j依赖
3.创建包
4.创建log4j的配置文件
```

## 搭建过程

```tex
1.搭建applicationContext配置文件
	1)创建数据源，**必须导入spring-jdbc依赖**
        <bean id="DataSource" classs="alibaba">
	2)创建sqlSessionFactoryBean,配置mybatis
		<bean id="sqlSessionFactory" class="sqlSessionFactoryBean">
			<property name="dataSource">
			<property name="configLocation">
		</bean>
	3)创建MapperScannerConfigurer,自动将mapper对象放入spring容器
		<bean class="MapperScannerConfigurer">
			<property name="basePackage">
			<property name="sqlSessionFactoryBeanName">
		</bean>
2.开启组件扫描context:component-scan>
3.导入外部配置文件context:property-placeHolder>
4.@component/@service/@controller/@repository注解创建组件
5.@AutoWired自动注入，使用 构造方法或者set方法更稳定，否则容易为null
```

# spring事务

## 事务

```tex
事务是数据库操作的最基本单元,逻辑上的一组单元，要么都成功，要么都失败

事务的四个特性(ACID):
	原子性:要么都成功，要么都失败
	一致性:执行结果的一致性
	隔离性:事务与事务之间互不干扰
	持久性:对数据库数据的改变不会丢失
```

## 事务隔离级别

```tex
脏读:一个未提交事务读取到另一个未提交事务的数据
	例子，一个事务提交后回滚，另一个事务读取到提交后未回滚的数据   200-500-200   读取到500发生错误
	
不可重复读:一个未提交事务读取到一个已提交事务修改的数据
	例子:未提交的事务A两次获取的数据不一样，在获取期间事务b完成了对事务的更改
幻读:一个未提交事务读取到一个已提交事务增加的行
	例子:未提交的事务A第二次读取的数据比第一次多了一行第一次没有的数据
```



```tex
ISOLATION_DEFAULT默认事务隔离级别  MYSQL默认的是REPEATABLE_READ可重复读
ISOLATION_READ_UNCOMMITTED读未提交。未解决任何并发问题
ISOLATION_READ_COMMITTED读已提交。解决脏读，存在不可重复读和幻读
ISOLATION_REPEATABLE_READ可重复读，解决脏读，不可重复读，存在幻读
ISOLATION_SERIALIZABLE串行化，不存在并发问题
```

## 事务超时，一般不配置

## 事务的传播行为

```tex
有事务方法调用无事务方法
无事务方法调用有事务方法
有事务方法调用有事务方法

判断业务方法是不是有事务，是什么样的事务
(有/无)事务方法调用(有/无)事务方法的七种传播行为
PROPAGATION_REQUIRED指定的方法必须在事务内执行，若当前存在事务，就加入到当前事务中；若没有则新建到调用方法中，spring默认选择		行为

PROPAGATION_REQUIRED_NEW指定方法总是新建事务，当前事务挂起等待新事务执行完毕，有没有当前事务都会新建事务
PROPAGATION_SUPPORTS指定的方法支持当前事务，若当前没有事务，也可以以非事务状态运行

PROPAGATION_MANDATORY
PROPAGATION_NESTED
PROPAGATION_NEVER
PROPAGATION_NOT_SUPPORTED
```

## 事务回滚

```tex
事务回滚时机
业务方法运行时异常，spring执行回滚,调用事务管理器的rollback
业务方法执行成功，spring执行事务提交commit

```

## 注解实现

```tex
适合中小型项目，@Transactional注解是spring框架自己的注解，放在public方法上面，实现事务管理
属性
propagation:用于设置事务传播属性，该属性类型为propagation枚举，默认值为Propagation.REQUIRED
isolation:设置事务隔离级别，默认为Isolation.DEFAULT
readOnly:该方法对数据库是不是只读，boolean类型
timeout:设置超时时间，单位为秒，默认为-1，没有时间
rollbackFor:指定需要回滚异常类。类型为class[],当class[]中的异常类出现，即回滚
rollbackForClassName:指定需要回滚异常类。类型为string[],当string[]中的异常类出现，即回滚
noRollBackFor:指定不需要回滚异常类。类型为class[],当class[]中的异常类出现，即不回滚
noRollbackForClassName:指定不需要回滚异常类。类型为string[],当string[]中的异常类出现，即不回滚
```

### 使用步骤

```tex
1.需要声明事务管理器
	<bean id="" class="DataSourceTransactionManager">
2.开启事务注解驱动
	<tx:annotation-driven transaction-manager="事务管理器id">
3.spring实现事务管理的方式是通过aop将commit和rollback在业务方法执行前通过通知切入进去
```

## 配置文件实现

```tex
适合大型项目，<aop:config>,业务代码和事务配置完全分离
```

### 实现步骤

```xml
<!--导入依赖-->

<!--开启事务管理器-->
<bean id="" class="DataSourceTransationManager">

<!--声明事务隔离级别，传播行为，超时时间-->
<tx:advice id="" transation-manager="事务管理器id">
    <tx:attributes>
        <!--给具体的方法配置事务属性 name：方法名,可以使用通配符指定多个，也可以使用多个标签-->
    	<tx:method name="" propagation="" isolation="" and so on/>
    </tx:attributes>
</tx:advice>
    
<!--配置aop-->
<aop:config>
	<!--配置切入点表达式，指定哪些包中类需要使用事务,将事务方法切入到业务方法中-->
    <aop:pointcut id="" expression=""></aop:pointcut>
    
    <!--配置增强器，连接advice和pointcut-->  <!--确定advice的配置作用在那个类中的方法上-->
    <aop:advisor advice-ref="adviceID" pointcut-ref="pointcutID"></aop:advisor>
</aop:config>
```

