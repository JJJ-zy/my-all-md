# yaml配置文件

## 	简介

```tex
一种标记语言，适合做以数据为中心的配置文件
```

## 	基本语法

```tex
1.key:value kv中间有空格，冒号空格
2.大小写敏感
3.使用缩进表示层级关系
4.缩进不允许使用tab，只能使用空格
5.缩进的空格数不重要，只要相同层级的元素对齐即可
6.#表示注释
7.''和""表示字符串内容  会被转义/不转义   双引号会将\n作为换行符
```

## 	数据类型

- 字面量,单个的不可再分的值   date,boolean,string,number,null

  ```xml
  k: v
  ```

  

- 对象:键对值的集合，map,hash,set,object

  ```xml
  行内写法：  k: {k1:v1,k2:v2,k3:v3}
  或
  k:
  	k1: v1
  	k2: v2
  	k3: v3
  ```

  

- 数组：一组按次序排列的值，array,list,queue

  ```xml
  行内写法： k: [v1,v2,v3]
  或
  k:
  	-v1
  	-v2
  	-v3
  ```

  

  ```xml
  <!--yaml配置文件中做提示-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  
  <!--不将配置提示处理器打包，不影响程序运行-->
  ```

  ![image-20210411111431191](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210411111431191.png)

  



# WEB开发

## 	静态资源路径

```tex
只要静态资源放在类路径下(resources)的static或public或resource或WEB-INF/resources中，就可以**直接**访问到
访问：根路径+资源名

但是，请求会先去找controller，没有对应的controller才会找静态资源，可以改变属性来调整静态资源访问

静态资源默认访问方式:/**
静态资源添加前缀,访问方式  ：  根路径+static-path-pattern+资源名   虚拟路径
spring.mvc.static-path-pattern = /resource/**;


指定haha文件夹为静态资源文件夹,其他文件夹中的静态资源就访问不到了
spring.resources.static-locations:
									[classpath:/haha/]
```

## 	webjars

```tex
将静态资源打包成jar包，可以使用maven导入，WEB-INF下有js等静态资源，可以自动映射到程序中，不需要再导入js文件

根路径+/webjars/**访问
```

## 	欢迎页配置

```tex
两种方式
	一、可以在静态资源文件夹中创建index.html,默认为欢迎页，但是不能设置访问前缀
	二、controller可以处理index的请求   和/请求
	
web favicon图标设置
将favicon.ico图片放到静态资源文件夹中就可以自动加载,但是不能设置访问前缀
```



## rest风格

```xml
springboot 自动配置了HiddenHttpMethodFilter对象，只需要选择开启
spring.mvc.hiddenMethod.filter.Enable = true 
put请求和delete请求本质是post请求  ， 使用_method = PUT/DELETE来说明是put还是delete

可以自定义HiddenHttpMethodFilter对象，可以用set方法改变_method属性名
```



## 常用参数注解

![image-20210411180702429](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210411180702429.png)

### @PathVariable

```tex
接受rest风格路径上的值
user/{id}
@PathVariable("id") String id
@PathVariable Map<String,String> m     Controller会将所有变量封装进map中
```





### @MatrixVariable  矩阵变量是用分号带的值

```tex
springboot默认不开启
原理：所有的路径都由UrlPathHelper进行管理
removeSemicolonContent支持矩阵变量，默认为true,移除分号后内容

开启
使用注解configuration+接口WebMvcConfigurer
配置类实现WebMvcConfigurer接口，实现ConfigurePathMatch方法
创建新的UrlPathHelper对象，使用set方法设置removeSemicolonContent为false
使用configurer.setUrlPathHelper()方法设置新的UrlPathHelper

开启2
使用Bean注解返回WebMvcConfigurer对象
return new WebMvcConfigurer(){
	和上面一样设置
}

矩阵变量名重复的情况，pathVar:是矩阵变量跟随的路径
```

![image-20210816085514223](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210816085514223.png)

![image-20210816085431289](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210816085431289.png)

![image-20210411182944828](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210411182944828.png)



## 内容协商

```tex
浏览器和后台协商传输的数据
根据请求头content-type来告诉后端可以接收的数据，后端进行匹配

jackson返回xml依赖包  application/xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
</dependency>


因为浏览器不方便改变content-type，只能通过ajax，所以springmvc中有属性
spring.mvc.contentnegotiation.favor-parameter=true  开启请求参数匹配  通过传输format参数
format=json // format=xml  只支持这两个
```



# 模板引擎thymeleaf

一、引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

二、springboot自动配置好了thymeleaf

```tex
自动配置好的策略：
	1.所有thymeleaf的配置值ThymeleafProperties
	2.配置好了SpringTemplateEngine  模板引擎
	3.配置好了ThymeleafViewResolver  视图解析器
```

```java
视图解析器前后缀

public static final String DEFAULT_PREFIX = "classpath:/templates/";

public static final String DEFAULT_SUFFIX = ".html";
```

```http
https://fanlychie.github.io/post/thymeleaf.html#2-1-3-%E2%80%A6中文文档
```

```tex
名称空间: xmlns:th="http://www.thymeleaf.org"
```



```html
${}取出作用域中的值
@{}拼接链接     @{/link}  在项目名后拼接/link没有项目名就直接localhost:8080/link
```

```html
//抽取公共部分
th:replace="~{文件名 :: #id名}"  将公共部分替换当前元素
th:include="~{文件名 :: fragment名}"   将公共部分的内容放入到标签内
th:insert=""   将公共部分直接加入到标签内部


~{文件名 :: #id名}
~{文件名 :: fragment名}

//公共部分语法
th:fragment = ""
id=""
```

## 变量表达式

```html
[[${变量}]]  thymeleaf变量表达式  变量带/不会被转义
```

## 遍历

```html
<tr th:each="item : ${items}">
    <td th:text="${user.name}"></td>
    <td>[[${user.age}]]</td>
</tr>


<!--thymeleaf的语法规则允许加状态，状态中包括index等,官网看-->
<tr th:each="item,status : ${items}">   
     <td th:text="${user.name}"></td>    
     <td>[[${user.age}]]</td>
</tr>
```



三、我们只需要直接开发页面

# 拦截器interceptor

```java
/**
*1.创建拦截器类继承HandlerInterceptor接口，重写方法，获取session
*2.创建mvcconfig配置类，继承WebMvcConfigurer接口，重写addInterceptors方法
*3.配置静态资源过滤
*/
```

```java
package com.zwj.interceptor;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;


/**
 * @author zwj
 * @date 2021/4/20 - 13:40
 */
@Slf4j
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        StringBuffer requestURL = request.getRequestURL();
        log.info("拦截路径{}",requestURL);

        //登录检查拦截器
        HttpSession session = request.getSession();
        Object userMsg = session.getAttribute("userMsg");
        if (userMsg != null){
            return true;
        }

        request.setAttribute("error","请先登录");
        request.getRequestDispatcher("/login").forward(request,response);
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

```java
package com.zwj.config;

import com.zwj.interceptor.LoginInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author zwj
 * @date 2021/4/20 - 13:45
 */
@Configuration
public class AdminWebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")   //配置所有资源走过滤器
                .excludePathPatterns("/","/login","/css/**","/fonts/**","/images/**","/js/**");
        									//配置不走过滤器的资源
    }
}

```

# 文件上传

```html
文件上传表单
<form th:action="@{/}" method="post" enctype="multipart/form-data">
	<input type="file" name="" multiple />  //multiple为多选文件上传
</from>
```

```java
/**
     * MultipartFile会自动封装传过来的文件
     * isEmpty()方法判断为空
     * transferTo()方法下载文件
     * @param email
     * @param username
     * @param headerImg
     * @param photos
     * @return
     */
@PostMapping("/upload")
    public String upload(@RequestParam("email") String email,
                         @RequestParam("username") String username,
                         @RequestPart("headerImg") MultipartFile headerImg,
                         @RequestPart("photos") MultipartFile[] photos) throws IOException {
        log.info("上传的信息：email={},username={},headerImg={},photos={}",
                email,username,headerImg.getSize(),photos.length);
        String name = headerImg.getOriginalFilename();
        System.out.println(name);
        if (!headerImg.isEmpty()){
            String originalFilename = headerImg.getOriginalFilename();
            headerImg.transferTo(new File("D:\\workspace_idea\\boot-web-002\\src\\main\\resources\\imgs\\"+originalFilename));
        }
        if (photos.length>0){
            for (MultipartFile photo : photos) {
                if (!photo.isEmpty()){
                    String originalFilename = photo.getOriginalFilename();
                    photo.transferTo(new File("D:\\workspace_idea\\boot-web-002\\src\\main\\resources\\imgs\\"+originalFilename));
                }
            }
        }
        return "index";
    }
```

```yaml
spring.servlet.multipart.max-file-size=10MB  单个文件最大限度
spring.servlet.multipart.max-request-size=100MB 单次请求最大限度
```



# error错误处理

```tex
springboot默认提供/error处理所有错误的映射，浏览器生成错误页面，客户端访问生成json

自定义error错误处理完全替换
实现ErrorController并注册该类型的Bean定义或者
添加ErrorAttributes类型的组件
------------------------------------------------------------不是一起的
定制错误页面，会被自动解析
静态资源文件夹下的error下的4xx,5xx页面  或者templete文件夹下的error
error/404.html   error/500.html
```

![image-20210817101158768](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817101158768.png)



# 原生WEB组件 使用   servlet,filter,listener

一、使用servlet API

```java
主配置类
@ServletComponentScan(basePackages = "com.zwj")

原生组件上方
@webServlet(urlPatterns = "/my")
@webFilter(urlPatterns = {"/css/*","/js/*"})
@webListener
```

推荐使用：

二、使用RegistrationBean

```java
不需要使用上述的注解，创建一个类，创建方法返回RegistrationBean对象，放到spring容器中
```



![image-20210420175559105](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210420175559105.png)

使用自己的Filter

![image-20210420175802456](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210420175802456.png)

![image-20210420175858166](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210420175858166.png)

# 定制化原理--自定义组件

```tex
1.创建配置类，使用@Bean注解添加组件，springboot会优先使用自定义组件
2.创建CustomizationBean类继承WebServerFactoryCustomizer<>接口
3.修改配置文件
4.web应用 实现WebMvcConfigurer  --@Bean,可以全面接管SpringMvc,所有规则全部可以重新配置，实现定制和扩展
		@EnableWebMvc 全面接管springMVC,底层规则全部没有
```

# 数据访问

1、数据源的自动配置

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```

2、数据库驱动

```xml
解决自动仲裁
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.22</version>
</dependency>

或
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<properties>
	<mysql.version>8.0.22</mysql.version>
</properties>
```

3、自动配置类

```java
1.DataSourceAutoConfiguration数据源自动配置
    数据源的相关属性 spring.datasource.
    没有datasource的前提下，才会自动配置数据库连接池
2.DataSourceTransactionManagerAutoConfiguration事务管理自动配置
3.JdbcTemplateAutoConfiguration 数据操作自动配置
    jdbcTemplate的相关属性 spring.jdbc.
4.JndiDataSourceAutoConfiguration jndi的自动配置
5.XADataSourceAutoConfiguration 分布式事务的自动配置
```

# Druid数据源

## 自定义

![image-20210817111237676](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817111237676.png)

## starter

```xml
<!--德鲁伊springboot整合-->
<!--https://github.com/alibaba/druid/blob/master/druid-spring-boot-starter/pom.xml-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```



![image-20210817111732741](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817111732741.png)

```tex
url = "druid/*"
```

![image-20210817113130559](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817113130559.png)



![image-20210817115501635](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817115501635.png)



# mybatis整合

```xml
<!--springboot mybatis整合-->
<!--https://github.com/mybatis/spring-boot-starter/blob/mybatis-spring-boot-2.1.4/mybatis-spring-boot-starter/pom.xml-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```

设置@mapper

```xml
@Mapper标记到mapper接口上,将mapper放入spring容器
```



application.yaml配置

```xml
 mybatis:
      config-location: classpath:mybatis/mybatis-config.xml
      mapper-locations: classpath:mybatis/mapper/*.xml
```

问题

```tex
报错：
Property 'configuration' and 'configLocation' can not specified with together

configuration和configLocation不能同时出现

    mybatis:
      config-location: classpath:mybatis/mybatis-config.xml
      mapper-locations: classpath:mybatis/mapper/*.xml
      configuration:
        map-underscore-to-camel-case: true  驼峰命名
        
application.yaml配置文件中配置了config-location的位置，就可以在配置文件中进行配置了，不需要在配置文件中写

可以选择在mybatis-config.xml中写配置，但要在application.yaml中指定配置文件地址
也可以选择不指定配置文件地址，直接在yaml中配置configuration
```

```xml
<!--insert中的属性useGeneratedKeys="true" 为使用自增主键  keyProperty="id" 自增主键名为id  -->
<insert id="insert" useGeneratedKeys="true" keyProperty="id">
	insert into user(name,password) values ("132","123")
</insert>
这样就不插入id值，就可以封装到对象中去实现insert(User user)方法
```

注解开发属性项放到@options(useGeneratedKeys="true")中



***@MapperScan("com.zwj.dao")放到主类上就可以实现不用每个mapper都标注@Mapper

最好每个mapper都标注

## 注解开发

```tex
@select("sql")
@update()
@delete()
@insert()


组合
@insert
@options(useGeneratedKeys="true",keyProperty="id")
```

# mybatis-plus

```xml
<!--导入依赖-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
包含mybatis包和扩展包和jdbc包
```

## 配置项

```tex
mybatis-plus
```

## 自动配置

```tex
SqlSessionFactory自动配置好
MapperLocations 自动配置好，有默认值  classPath*:/mapper/**/*.xml  任意路径下的mapper文件夹下任意路径下的.xml文件都是sql映射文件
```

## BaseMapper<T>接口

```tex
封装好了各种增删改查方法
继承该接口后，无需编写mapper.xml文件，即可获得CRUD功能
这个mapper支持id泛型
```

## 属性不存在注解

```tex
@TableField(exist = false)
private String userName;   该属性在数据库中不存在,不查询
```

![image-20210817153600130](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817153600130.png)

## service继承IService<T>接口

```tex
Iservice中有各种针对于service的增删改查方法
service实现类TserviceImpl继承类ServiceImpl<TMapper,T>

ServiceImpl<M extends BaseMapper<T>,T> implements IService<T>
默认实现了IService中的各种增删改查方法，继承这个类如果无特殊需求不需要重写方法
```



## 分页查询

```tex
userService中继承IService得到的方法
```

![image-20210817154201792](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210817154201792.png)