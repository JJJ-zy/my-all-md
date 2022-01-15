# SSM整合

一、搭建环境

​	数据库建表

​	maven仓库

​	idea

二、搭建结构

​	entity

​	mapper

​	service

​	controller

三、创建entity实体对象

四、创建mybatis配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!--是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <typeAliases>
        <typeAlias type="com.zwj.entity.Books" alias="Books"></typeAlias>
    </typeAliases>
    <mappers>
        <mapper class="com.zwj.mapper.BookMapper"></mapper>
    </mappers>
</configuration>
```



五、创建Mapper接口和Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zwj.mapper.BooksMapper">
    <insert id="addBook" parameterType="Books">
        insert into books(book_name,book_counts,detail)
        values (#{bookName},#{bookCounts},#{detail})
    </insert>
    <delete id="delBookById" parameterType="int">
        delete from books where book_id=#{bid}
    </delete>
    <update id="updateBook" parameterType="Books">
        update books
        set book_name=#{bookName},book_counts=#{bookCounts},detail=#{detail}
        where book_id=#{bookId}
    </update>
    <select id="queryBookById" parameterType="int" resultType="Books">
        select * from books where book_id=#{bid}
    </select>
    <select id="queryBooks" resultType="Books">
        select * from books
    </select>
</mapper>
```



六、创建spring-mybatis配置文件配置Mapper层

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties" />

    <!--数据库连接池-->
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource">
        <property name="driverClassName" value="${driver}"></property>
        <property name="url" value="${url}"></property>
        <property name="username" value="${username}"></property>
        <property name="password" value="${password}"></property>
    </bean>

    <!--配置sqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactory">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>

        <!--绑定配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>

    <!--配置dao接口扫描包，实现Dao接口动态注入到Spring容器中-->
    <!--反射机制动态创建实现类注入到spring中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--注入SessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sessionFactory"></property>
        <!--扫描包-->
        <property name="basePackage" value="com.zwj.mapper"></property>
    </bean>


</beans>
```



七、创建service层接口和对象

```java
public class BookServiceImpl implements BookService{

    private BookMapper bookMapper;
    public void setBookMapper(BookMapper bookMapper){
        this.bookMapper = bookMapper;
    }

    @Override
    public int addBook(Books books) {
        return bookMapper.addBook(books);
    }

    @Override
    public int delBookById(int id) {
        return bookMapper.delBookById(id);
    }

    @Override
    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }

    @Override
    public Books queryBookById(int id) {
        return bookMapper.queryBookById(id);
    }

    @Override
    public List<Books> queryBooks() {
        return bookMapper.queryBooks();
    }
}
```



八、创建spring配置文件配置service

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--扫描service下面的包-->
    <context:component-scan base-package="com.zwj.service" />

    <!--创建Service对象，注入属性-->
    <bean class="com.zwj.service.BookServiceImpl" id="service">
        <property name="bookMapper" value="bookMapper"></property>
    </bean>

    <!--创建事务-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    
    <!--aop将事务切入到具体方法-->

</beans>
```



九、添加web

十、在web.xml文件中添加DispatcherServlet和CharacterEncodingFilter

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <filter>
        <filter-name>filter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>

        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>filter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>

</web-app>
```



十一、配置springMVC配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--开启默认映射器和适配器-->
    <mvc:annotation-driven/>
    <!--默认静态资源过滤器-->
    <mvc:default-servlet-handler/>
    <!--开启注解扫描-->
    <context:component-scan base-package="com.zwj.controller"/>
    
    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>


</beans>
```



十二、创建controller对象