# SpringMVC

```tex
DisPatcherServlet
默认配置方式下，springmvc默认配置文件在WEB-INF下，默认名称为<servlet-name>-servlet.xml

扩展配置:
初始化参数
<init-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classPath:<param-value>
</init-param>

加载时机
<load-on-startup>1</load-on-startup>
```

## 使用步骤

```tex
1.导入springmvc依赖，jsp,servlet依赖
2.在web.xml中声明DispatcherServlet中央控制器对象
3.创建controller
4.使用@requestMapping注解,放在类上边表示访问路径公共部分
5.创建springmvc配置文件，创建组件扫描器，创建视图解析器
```

## post方式乱码过滤器

```tex
CharacterEncodingFilter

init-param初始化参数encoding为utf-8
设置forceRequestEncoding为true
设置forceResponseEncoding为true
```

# 注解

## 参数

### @requestParam

```tex
当请求参数名和controller方法参数不一致时，value="请求参数名"
required=false表示请求中可以没有此参数
required=true表示请求中必须有此参数，默认为true
```

![image-20210812115133149](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812115133149.png)

### 对象参数

```tex
请求参数会自动匹配对象中的属性
```

## @SessionAttributes

```tex

```

![image-20210812123212418](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812123212418.png)

## @InitBinder

![image-20210812165354278](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812165354278.png)

```tex
在@requestmapping之前执行，设置某个值不注入参数
```



## @DateTimeFormat

```tex
@DateTimeFormat("yyyy-mm-dd")
Date date

目标属性上边加上就可以，告诉注解传入值的格式
```

## @NumberFormat

```tex
@NumberFormat("#,###,###.##")
```



# 视图

## 视图解析器

```tex
InternalResourceViewResolver
```



## ModelAndView

```tex
同时传递数据和跳转
```

## String

```tex
只传数据（ajax）或者仅跳转
ajax返回值为void
```

## String+map/model/modelMap传递数据

```tex
public String xxx(Map map){return 视图}
```



# Ajax

## @ResponseBody

```tex
ajax返回对象类型的json数据使用@ResponseBody注解
```

### 使用步骤

```tex
1.导入jackson依赖
2.创建<mvc:annonation-driven />驱动，会自动生成HttpMessageConverter下的各种实现类对象
	消息转换器
	主要用来实现object向xml,text,json等的转变
	接口HttpMessageConverter 
3.在controller方法上使用@ResponseBody注解
	返回对象自动转变为json对象
```

![image-20210812102612190](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812102612190.png)

# restful风格

## @PathVariable()

```tex
@requestMapping("/xxx/{id}")
public String xxx(@PathVariable("id")Integer id)
```

![image-20210812104307524](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812104307524.png)

# HiddenHttpMethodFilter

```tex
将post请求转换为put和delete请求

使用_method参数选择请求方式,值为DELETE/PUT
```

# @ModelAttribute

```tex
在同一个控制器中，标注了@ModelAttribute的方法实际上会在@RequestMapping方法之前被调用。

例子
@ModelAttribute的方法将数据对象放入map中
springmvc表单数据传过来时，会先将map中的对象提出，将表单数据赋值
使用map中赋完值的数据对象给@RequestMapping方法的对象参数赋值

@ModelAttribute
public void xxx(@RequestParam(name="id",required=false) Integer id,Map map){
	if(id != null){
		查询数据库为id的数据放到map中，当数据更改表单提交时，如果有数据为空，就使用原始数据
		map.put("user",user)
	}
}

@RequestMapping()
public String xxx(@ModelAttribute("user") User user)

在map中找不到user就会去@SessionAttribute中找，SessionAttribute中没有就会报错，可以把sessionAttribute的value值与方法参数值设置不一样
```

# mvc:view-controller

![image-20210812145302950](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812145302950.png)





# 重定向



![image-20210812145908430](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812145908430.png)

# JSR303

![image-20210812170106572](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812170106572.png)

![image-20210812170128625](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812170128625.png)

# 文件上传

![image-20210812170406344](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812170406344.png)

![image-20210812170659984](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812170659984.png)





![image-20210812170903323](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210812170903323.png)



# JSON乱码解决

```xml
<mvc:annotation-driven>
    <!--    json乱码解决-->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

# 匹配不到试图解决

![image-20210815112936217](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210815112936217.png)