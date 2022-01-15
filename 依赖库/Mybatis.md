# Mybatis   

**持久化层**

**下载地址**GitHub

## 全局配置文件

- configuration（配置）
  - [properties（属性）](https://mybatis.org/mybatis-3/zh/configuration.html#properties)
  - [settings（设置）](https://mybatis.org/mybatis-3/zh/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  - environments（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.org/mybatis-3/zh/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.org/mybatis-3/zh/configuration.html#mappers)

### properties引入配置文件

```tex
resource:引入类路径下的资源文件
uri:引入网络路径或磁盘路径下的资源
```

### settings

```tex
mapUnderscoreToCamelCase 驼峰命名，默认false
```

### typeAliases别名配置

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

当这样配置时，`Blog` 可以用在任何使用 `domain.blog.Blog` 的地方。

每一个在包 `domain.blog` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。

```java
@Alias("author")
public class Author {
    ...
}
```

![image-20210524165311499](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210524165311499.png)

### typehandlers类型处理器

### plugins插件

### environments环境配置

```tex
默认使用的环境environment ID（比如：default="development"） 可以有多个环境，开发环境，测试环境
每个 environment 元素定义的环境 ID（比如：id="development"）。
事务管理器的配置（比如：type="JDBC"）。
数据源的配置（比如：type="POOLED"）
```

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

### Mappers

```tex
resource:类路径下xml资源
url: 磁盘路径下xml资源
class:接口实现类完全限定类名 ，xml文件和接口在同一包下且同名，或者使用注解
```

```tex
<!-- 将包内的映射器接口实现全部注册为映射器 -->批量注册，xml文件和接口在同一包下且同名，或者使用注解
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```



## mapper配置文件

### insert

```xml
useGeneratedKeys  生成自增主键值，传给keyProperty属性定义的成员变量，默认false
keyProperty  放置useGeneratedKey获取值所传给的类属性名

selectKey  生成非自增主键值
```

### 参数

```tex
单个参数parameterType
多个参数使用param注解，或者封装成map
复杂参数User类型的参数对象传递到了语句中，会查找 id、username 和 password 属性，然后将它们的值传入预处理语句的参数中。
```

### #{}

规定参数的一些规则

```tex
javaType、jdbcType、mode(存储过程)、numericScale、resultMap、typeHandler、jabcTypeName、expression
```

![image-20210525134120095](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210525134120095.png)

![image-20210525134205028](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210525134205028.png)

### 返回list集合

```tex
select查询出list集合，返回类型使用list集合元素类型  resultType =  com.zwj.entity.User
```

### 返回map集合

使用集合元素类型

![image-20210525134650085](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210525134650085.png)



普通map返回类型用map

### resultMap

```tex
type:自定义规则的java类型   类全限定类名  
id:resultMap引用的名称

column:指定哪一列
property:指定列对应的类属性名


标签
<result></result>  可以用于全部属性

<id></id>  主键封装

没指定的规则会自动封装，但最好全部自定义封装
```

#### 级联查询

```tex
1.使用对象.属性名的方式进行赋值
2.使用association进行关联查询
	property:需要联合查询的属性名
	javaType:返回类型
	
	标签
	<result></result>  可以用于全部属性
	<id></id>  主键封装
	
3.使用association进行分步查询
	property:需要分步查询的属性名
	select : 分步查询mapper方法的全限定类名加方法名
	column:指定将哪一列的值传给这个方法
	
	延迟加载
	settings:
	lazyLoadingEnabled:延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。boolean，特定关联关系中可通过设置 					**fetchType** 属性来覆盖该项的开关状态。
	aggressiveLazyLoading:开启时，任一方法的调用都会加载该对象的所有延迟加载属性。 否则，每个延迟加载属性会按需加载,flase按			需加载

4.collection关联集合
	property:需要联合查询的属性名
	ofType:集合中元素类型
	标签
	<result></result>  可以用于全部属性
	<id></id>  主键封装
	标签
	<result></result>  可以用于全部属性
	<id></id>  主键封装
	
5.fetchType:默认lazy开启懒加载    eager立即加载

6.多列传值使用map
	column="{id=id,name=name}" 
```

### discriminator鉴别器

```tex
判断某列的值，然后根据某列的值改变封装行为
javaType:列值对应的java类型
column:列名

标签
case:属性 value:下标   resultType:返回类型

```

![image-20210525145912019](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210525145912019.png)

![image-20210525150037561](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210525150037561.png)

## 动态sql

### if

### ![image-20210531095759173](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210531095759173.png)

```tex
OGNL
```

![image-20210531100313188](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210531100313188.png)

![image-20210531100225434](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210531100225434.png)



​	

![image-20210531095928270](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210531095928270.png)

​		[HTML转义字符对照表 (oschina.net)](https://tool.oschina.net/commons?type=2)



### where

解决字符串拼接  and/or等字符 问题

```tex
where标签将if判断语句全部包含，解决问题  ，and要放在条件头部
```



### trim

```tex
属性：
	prefix:前缀，trim标签体中是整个字符串拼串后的结果，prefix给整个串加个前缀
	prefixOverrides:前缀覆盖,去掉整个字符串前面多余的字符
	suffix:后缀，trim标签体中是整个字符串拼串后的结果，suffix给整个串加个后缀
	suffixOverrides:后缀覆盖，去掉整个字符串后面多余的字符
	
	例子：prefix = "where" prefixOverrides="and" suffix="" suffixOverrides="and"
```



### choose(when , otherwise)

相当于switch-case

![image-20210603163959751](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210603163959751.png)



### set       (还可以trim版本set)

封装修改条件

![image-20210603164304488](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210603164304488.png)



### foreach

```tex
属性:
	collection:指定遍历的集合，list类型的参数会被封装成map,map的key叫做list
	item:将遍历出的元素赋值给指定的变量
	separator:元素之间的分隔符
	open:遍历出所有结果拼接一个开始的字符
	close:拼接一个结束的字符
	index:遍历list的时候是索引，遍历map的时候是key
```

![image-20210603164820769](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210603164820769.png)



#### 批量插入

```sql
两种方式
1.
insert into user(name,password) values (?),(?),(?)
批量传入？位置的参数进行批量插入

2.使用多个语句，分号分隔，连接数据库时，allowMultiQuerys=true,设置sql语句可以分号隔开使用
	insert into user(name,password) values (?);
	insert into user(name,password) values (?);
	
	
	
```

### 内置参数

```tex
不只是方法传递过来的参数可以被判断
mybatis的两个内置参数也可以

_parameter:整个参数
			一个参数：_parameter
			多个参数：封装为map，_parameter就是这个map

_databaseId:如果配置了databaseIdProvider标签
			_databaseId就是当前数据库的别名
```

![image-20210603170438096](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210603170438096.png)



### bind

可以将OGNL表达式中的值绑定到一个变量中，方便引用

做拼串问题时可以使用

![image-20210603170820766](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210603170820766.png)

### sql标签   抽取重用sql

```tex
使用include标签引用sql片段
refid属性放置sql id

include标签下的property标签可以放置属性，include引用的sql中可以通过${}获取到
```

![image-20210603171020494](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210603171020494.png)

## 逆向工程

### mybatis generator(MBG)

```tex
官网配置文件xml
```

![image-20210815093724764](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815093724764.png)

![image-20210815093757074](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815093757074.png)

![image-20210815093851600](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815093851600.png)

![image-20210815093924062](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815093924062.png)

![image-20210815094003996](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815094003996.png)

![image-20210815094050881](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815094050881.png)



### 运行

![image-20210815094134373](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815094134373.png)