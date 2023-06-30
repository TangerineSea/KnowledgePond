# MyBatis项目搭建

## 1、导入依赖

```xml
 <!-- 导入依赖 -->
    <dependencies>
        <!-- mysql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <!-- mybatis -->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
        <!-- junit -->
        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>


    <!-- 在build中配置resources，来防止我们资源导出失败的问题 -->
    <build>
        <resources>
            <resource>
                <directory>src/main/resource</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

## 2、编写数据库文件db.properties

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
jdbc.username=root
jdbc.password=123456
```

## 3、编写核心配置文件mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- configuration核心配置文件 -->
<configuration>
    <!-- 引入外部配置文件 -->
    <properties resource="db.properties"/>
    
    <settings>
        <!-- 标准的日志工厂的实现 -->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <!-- 显示的开启全局缓存 -->
        <setting name="cacheEnabled" value="true"/>
    </settings>

    <!-- 可以给实体类起别名 -->
    <typeAliases>
        <package name="com.kuang.pojo"/>
    </typeAliases>
    
	<!-- 配置环境 -->
    <environments default="development">
        <!-- 配置id为development的环境 -->
        <environment id="development">
            <!-- 配置的事务类型 -->
            <transactionManager type="JDBC"/>
            <!-- 配置数据源（连接池） -->
            <dataSource type="POOLED">
                <!-- 配置连接数据库的4个基本信息 -->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 每一个Mapper.xml都需要在Mybatis核心配置文件中注册！ -->
    <mappers>
        <mapper class="com.kuang.dao.UserMapper"/>
    </mappers>
</configuration>
```

## 4、编写实体类 User

```java
package com.kuang.pojo;

public class User {
    private int id;
    private String name;
    private String pwd;

    // 无参构造
    public User() {
    }

    // 有参构造
    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    // get、set
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

```

## 5、编写接口 UserMapper

```java
package com.kuang.dao;

import com.kuang.pojo.User;

import java.util.List;
import java.util.Map;

public interface UserMapper {

    // 模糊查询
    List<User> getUserLike(String value);

    // 查询全部用户
    List<User> getUserList();

    // 根据id查询用户
    User getUserById(int id);

    User getUserById2(Map<String,Object> map);

    // 添加一用户
    int addUser(User user);

    // 万能的Map
    User addUser2(Map<String,Object> map);

    // 修改用户
    int updateUser(User user);

    // 删除用户
    int deleteUser(int id);
}

```

## 6、编写接口的xml文件 UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件 -->

<!-- namespace：命名空间，绑定一个对应的Dao/Mapper接口 -->
<mapper namespace="com.kuang.dao.UserMapper">

    <!-- 模糊查询 -->
    <select id="getUserLike" resultType="com.kuang.pojo.User">
        select * from mybatis.user where name like "%"#{value}"%"
    </select>

    <!-- select查询语句 -->
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>

    <!-- 根据id查询用户 -->
    <select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
        select * from mybatis.user where id = #{id}
    </select>

    <select id="getUserById2" parameterType="map" resultType="com.kuang.pojo.User">
        select * from mybatis.user where id = #{helloId} and name=#{name}
    </select>

    <!-- 添加一个用户 -->
    <insert id="addUser" parameterType="com.kuang.pojo.User">
        insert into mybatis.user(id, name, pwd) values(#{id},#{name},#{pwd});
    </insert>

    <!-- 万能的Map     传递map的key-->
    <insert id="addUser2" parameterType="map">
        insert into mybatis.user(id, name, pwd) VALUES (#{userid},#{userName},#{passWord});
    </insert>

    <!-- 修改用户 -->
    <update id="updateUser" parameterType="com.kuang.pojo.User">
        update mybatis.user set name=#{name},pwd=#{pwd} where id=#{id};
    </update>

    <!-- 删除用户 -->
    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id=#{id};
    </delete>
</mapper>
```

## 7、写个工具类（可不写） MybatisUtils

```java
package com.kuang.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

// sqlSessionFactory --> sqlSession
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    // 静态块，初始就加载
    static {
        try {
            // 使用Mybatis第一步：获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // 既然有了SqlSessionFactory，顾名思义，我们就可以从中获得SqlSession的实例了。
    // SqlSession完全包含了面向数据库执行SQL命令所需的所有方法。
    public static SqlSession getSqlSession(){
        /*
        正常写的代码为：
        SqlSession sqlSession = sqlSessionFactory.openSession();
        return sqlSession;
        将这两段代码优化为下面的代码：return sqlSessionFactory.openSession();
         */
        return sqlSessionFactory.openSession();
    }
}

```

## 8、编写测试类

```java
package com.kuang.dao;

import com.kuang.pojo.User;
import com.kuang.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.After;
import org.junit.Test;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class UserDaoTest {
    // 第一步：获得SqlSession对象
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    // getMapper
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    // 模糊查询测试
    @Test
    public void getUserLike(){
        List<User> userList = mapper.getUserLike("李");
        for (User user : userList){
            System.out.println(user);
        }
    }


    // 查询全部用户测试
    @Test
    public void test(){
        // 第一步：获得SqlSession对象
//        SqlSession sqlSession = MybatisUtils.getSqlSession();

        // 执行SQL
        // 方式一：getMapper
//        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUserList();

        // 方式二：
//        List<User> userList = sqlSession.selectList("com.kuang.dao.UserMapper.getUserList");

        for (User user : userList) {
            System.out.println(user);
        }

    }

    // 根据id查询用户测试
    @Test
    public void getUserById(){
        User user = mapper.getUserById(1);
        System.out.println(user);
    }

    @Test
    public void getUserById2(){
        Map<String,Object> map = new HashMap<String, Object>();
        map.put("helloId",1);
        mapper.addUser2(map);
    }

    // 增删改需要提交事务
    // 添加一个用户测试
    @Test
    public void addUser(){
        mapper.addUser(new User(6,"嘻嘻","123456"));
    }

    // 万能的Map
    @Test
    public void addUser2(){
        Map<String,Object> map = new HashMap<String, Object>();
        map.put("userid",5);
        map.put("userName","Hello");
        map.put("userWord","23232323");
        mapper.addUser2(map);
    }

    // 修改用户测试
    @Test
    public void updateUser(){
        mapper.updateUser(new User(4,"哈哈","222222"));
//        User user = mapper.getUserById(4);
//        System.out.println(user);
    }

    // 删除用户测试
    @Test
    public void deleteUser(){
        int res = mapper.deleteUser(6);
    }

    @After
    public void destroy(){
        // 提交事务
        sqlSession.commit();
        // 关闭连接
        sqlSession.close();
    }
}

```



# Spring项目搭建



# SpringMVC项目搭建

## 1、springmvc核心配置文件的编写applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
    <context:component-scan base-package="com.kuang.controller"/>
    <!-- 静态资源过滤 -->
    <mvc:default-servlet-handler/>
    <!-- JSON乱码问题配置 -->
    <mvc:annotation-driven>
        <mvc:message-converters>
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

    <!-- 视图借些器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!-- 后缀 -->
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 拦截器配置 -->
    <mvc:interceptors>
        <mvc:interceptor>
            <!-- 包括这个请求下面的所有的请求 / -->
            <mvc:mapping path="/**"/>
            <bean class="com.kuang.config.MyInterceptor"/>
        </mvc:interceptor>
        <mvc:interceptor>
            <!-- 包括这个请求下面的所有的请求 / -->
            <mvc:mapping path="/**"/>
            <bean class="com.kuang.config.LoginInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>

    <!-- 文件上传配置 -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 请求的编码格式，必须和jsp的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
        <property name="maxUploadSize" value="10485760"/>
        <property name="maxInMemorySize" value="40960"/>
    </bean>
</beans>

```

## 2、web.xml的编写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 1.注册Servlet -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 通过初始化参数指定SpringMVC配置文件的位置，进行关联 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!-- 启动顺序，数字越小，启动越早 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    
    <!-- 所有请求都会被springmvc拦截 -->
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```



# Spring整合MyBatis

## 1、整合实现一

1. 引入Spring配置文件beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
```

2. 配置数据源替换mybaits的数据源

```xml
<!--配置数据源：数据源有非常多，可以使用第三方的，也可使使用Spring的-->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</bean>
```

3. 配置SqlSessionFactory，关联MyBatis

```xml
<!--配置SqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--关联Mybatis-->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <property name="mapperLocations" value="classpath:com/kuang/dao/*.xml"/>
</bean>
```

4. 注册sqlSessionTemplate，关联sqlSessionFactory；

```xml
<!--注册sqlSessionTemplate , 关联sqlSessionFactory-->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <!--利用构造器注入-->
    <constructor-arg index="0" ref="sqlSessionFactory"/>
</bean>
```

5. 增加Dao接口的实现类；私有化sqlSessionTemplate

```java
public class UserDaoImpl implements UserMapper {
    //sqlSession不用我们自己创建了，Spring来管理
    private SqlSessionTemplate sqlSession;
    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }
    public List<User> selectUser() {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```

6. 注册bean实现

```xml
<bean id="userDao" class="com.kuang.dao.UserDaoImpl">
    <property name="sqlSession" ref="sqlSession"/>
</bean>
```

7. 测试

```java
@Test
public void test2(){
	ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
	UserMapper mapper = (UserMapper) context.getBean("userDao");
	List<User> user = mapper.selectUser();
	System.out.println(user);
}
```

结果成功输出！现在我们的Mybatis配置文件的状态！发现都可以被Spring整合！

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.kuang.pojo"/>
    </typeAliases>
</configuration>
```



## 2、整合实现二

mybatis-spring1.2.3版以上的才有这个 .

官方文档截图 :

dao继承Support类 , 直接利用 getSqlSession() 获得 , 然后直接注入SqlSessionFactory . 比起方式1 , 不需要管理SqlSessionTemplate , 而且对事务的支持更加友好 . 可跟踪源码查看

![ssm项目整合图1](https://github.com/TangerineSea/KnowledgePond/raw/074208a5d7eed039aceb613df278c1d7059470ed/picture/ssm%E9%A1%B9%E7%9B%AE%E6%95%B4%E5%90%881.png)

测试：

​		将我们上面写的UserDaoImpl修改一下

```java
public class UserDaoImpl extends SqlSessionDaoSupport implements UserMapper {
    public List<User> selectUser() {
        UserMapper mapper = getSqlSession().getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```

修改bean的配置

```xml
<bean id="userDao" class="com.kuang.dao.UserDaoImpl">
    <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```

测试

```java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    UserMapper mapper = (UserMapper) context.getBean("userDao");
    List<User> user = mapper.selectUser();
    System.out.println(user);
}
```



# 整合SSM

### 1、环境要求

环境：

 - IDEA
 - MySQL 8.0.23
 - Tomcat 9
 - Maven 3.6

要求：

- 需要熟练掌握MySQL数据库，Spring，JavaWeb及MyBatis知识，简单的前端知识；

### 2、数据库环境

创建一个存放书籍数据的数据库表

```sql
CREATE DATABASE `ssmbuild`;
USE `ssmbuild`;
DROP TABLE IF EXISTS `books`;
CREATE TABLE `books` (
  `bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
  `bookName` VARCHAR(100) NOT NULL COMMENT '书名',
  `bookCounts` INT(11) NOT NULL COMMENT '数量',
  `detail` VARCHAR(200) NOT NULL COMMENT '描述',
  KEY `bookID` (`bookID`)
) ENGINE=INNODB DEFAULT CHARSET=utf8
INSERT  INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`)VALUES 
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```

### 3、基本环境搭建

新建一Maven项目！ ssmbuild ， 添加web的支持

导入相关的pom依赖！

```xml
<dependencies>
    <!--Junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <!--数据库驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!-- 数据库连接池 -->
    <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.5.2</version>
    </dependency>
    <!--Servlet - JSP -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
    <!--Mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.2</version>
    </dependency>
    <!--Spring-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>
</dependencies>
```

Maven资源过滤设置

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

建立基本结构和配置框架！

- com.kuang.pojo
- com.kuang.dao
- com.kuang.service
- com.kuang.controller
- mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    
</configuration>
```

- applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    
</beans>
```

### 4、Mybatis层编写

数据库配置文件 **db.properties**

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=123456
```

IDEA关联数据库

编写MyBatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.kuang.pojo"/>
    </typeAliases>
    <mappers>
        <mapper resource="com/kuang/dao/BookMapper.xml"/>
    </mappers>
</configuration>
```

编写数据库对应的实体类 com.kuang.pojo.Books

使用lombok插件！

```java
package com.kuang.pojo;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Books {
    private int bookID;
    private String bookName;
    private int bookCounts;
    private String detail;
}
```

编写Dao层的 Mapper接口！

```java
package com.kuang.dao;
import com.kuang.pojo.Books;
import java.util.List;
public interface BookMapper {
    //增加一个Book
    int addBook(Books book);
    //根据id删除一个Book
    int deleteBookById(int id);
    //更新Book
    int updateBook(Books books);
    //根据id查询,返回一个Book
    Books queryBookById(int id);
    //查询全部Book,返回list集合
    List<Books> queryAllBook();
}
```

编写接口对应的 Mapper.xml 文件。需要导入MyBatis的包；

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.dao.BookMapper">
    <!--增加一个Book-->
    <insert id="addBook" parameterType="Books">
        insert into ssmbuild.books(bookName,bookCounts,detail)
        values (#{bookName}, #{bookCounts}, #{detail})
    </insert>
    <!--根据id删除一个Book-->
    <delete id="deleteBookById" parameterType="int">
        delete from ssmbuild.books where bookID=#{bookID}
    </delete>
    <!--更新Book-->
    <update id="updateBook" parameterType="Books">
        update ssmbuild.books
        set bookName = #{bookName},bookCounts = #{bookCounts},detail = #{detail}
        where bookID = #{bookID}
    </update>
    <!--根据id查询,返回一个Book-->
    <select id="queryBookById" resultType="Books">
        select * from ssmbuild.books
        where bookID = #{bookID}
    </select>
    <!--查询全部Book-->
    <select id="queryAllBook" resultType="Books">
        SELECT * from ssmbuild.books
    </select>
</mapper>
```

编写Service层的接口和实现类

接口：

```java
package com.kuang.service;
import com.kuang.pojo.Books;
import java.util.List;
//BookService:底下需要去实现,调用dao层
public interface BookService {
    //增加一个Book
    int addBook(Books book);
    //根据id删除一个Book
    int deleteBookById(int id);
    //更新Book
    int updateBook(Books books);
    //根据id查询,返回一个Book
    Books queryBookById(int id);
    //查询全部Book,返回list集合
    List<Books> queryAllBook();
}
```

实现类：

```java
package com.kuang.service;
import com.kuang.dao.BookMapper;
import com.kuang.pojo.Books;
import java.util.List;
public class BookServiceImpl implements BookService {
    //调用dao层的操作，设置一个set接口，方便Spring管理
    private BookMapper bookMapper;
    public void setBookMapper(BookMapper bookMapper) {
        this.bookMapper = bookMapper;
    }
    public int addBook(Books book) {
        return bookMapper.addBook(book);
    }
    public int deleteBookById(int id) {
        return bookMapper.deleteBookById(id);
    }
    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }
    public Books queryBookById(int id) {
        return bookMapper.queryBookById(id);
    }
    public List<Books> queryAllBook() {
        return bookMapper.queryAllBook();
    }
}
```

**OK，到此，底层需求操作编写完毕！**

### 5、Spring层

配置**Spring整合MyBatis**，我们这里数据源使用c3p0连接池；

我们去编写Spring整合Mybatis的相关的配置文件； **spring-dao.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 配置整合mybatis -->
    <!-- 1.关联数据库文件 -->
    <context:property-placeholder location="classpath:database.properties"/>
    <!-- 2.数据库连接池 -->
    <!--数据库连接池
        dbcp  半自动化操作  不能自动连接
        c3p0  自动化操作（自动的加载配置文件 并且设置到对象里面）
    -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 配置连接池属性 -->
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>
    <!-- 3.配置SqlSessionFactory对象 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>
    <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
    <!--解释 ： https://www.cnblogs.com/jpfss/p/7799806.html-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.kuang.dao"/>
    </bean>
</beans>
```

**Spring整合service层**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 扫描service相关的bean -->
    <context:component-scan base-package="com.kuang.service" />
    <!--BookServiceImpl注入到IOC容器中-->
    <bean id="BookServiceImpl" class="com.kuang.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>
    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource" />
    </bean>
</beans>
```

Spring层搞定！再次理解一下，Spring就是一个大杂烩，一个容器！对吧！

### 6、SpringMVC层

**web.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--DispatcherServlet-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <!--一定要注意:我们这里加载的是总的配置文件，之前被这里坑了！-->   
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!--encodingFilter-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
        </filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!--Session过期时间-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

**spring-mvc.xml**

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
    https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!-- 配置SpringMVC -->
    <!-- 1.开启SpringMVC注解驱动 -->
    <mvc:annotation-driven />
    <!-- 2.静态资源默认servlet配置-->
    <mvc:default-servlet-handler/>
    <!-- 3.配置jsp 显示ViewResolver视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>
    <!-- 4.扫描web相关的bean -->
    <context:component-scan base-package="com.kuang.controller" />
</beans>
```

**Spring配置整合文件，applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <import resource="spring-dao.xml"/>
    <import resource="spring-service.xml"/>
    <import resource="spring-mvc.xml"/>
</beans>
```

**配置文件，暂时结束！Controller 和 视图层编写**

BookController 类编写 ， 方法一：查询全部书籍

```java
@Controller
@RequestMapping("/book")
public class BookController {
    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;
    @RequestMapping("/allBook")
    public String list(Model model) {
        List<Books> list = bookService.queryAllBook();
        model.addAttribute("list", list);
        return "allBook";
    }
}	
```

编写首页 **index.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>
    <title>首页</title>
    <style type="text/css">
        a {
            text-decoration: none;
            color: black;
            font-size: 18px;
        }
        h3 {
            width: 180px;
            height: 38px;
            margin: 100px auto;
            text-align: center;
            line-height: 38px;
            background: deepskyblue;
            border-radius: 4px;
        }
    </style>
</head>
<body>
<h3>
    <a href="${pageContext.request.contextPath}/book/allBook">点击进入列表页</a>
</h3>
</body>
</html>
```

书籍列表页面 **allbook.jsp**

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>书籍列表</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表 —— 显示所有书籍</small>
                </h1>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-4 column">
            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增</a>
        </div>
    </div>
    <div class="row clearfix">
        <div class="col-md-12 column">
            <table class="table table-hover table-striped">
                <thead>
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名字</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                    <th>操作</th>
                </tr>
                </thead>
                <tbody>
                <c:forEach var="book" items="${requestScope.get('list')}">
                    <tr>
                        <td>${book.getBookID()}</td>
                        <td>${book.getBookName()}</td>
                        <td>${book.getBookCounts()}</td>
                        <td>${book.getDetail()}</td>
                        <td>
                            <a href="${pageContext.request.contextPath}/book/toUpdateBook?id=${book.getBookID()}">更改</a> |
                            <a href="${pageContext.request.contextPath}/book/del/${book.getBookID()}">删除</a>
                        </td>
                    </tr>
                </c:forEach>
                </tbody>
            </table>
        </div>
    </div>
</div>
```

BookController 类编写 ， 方法二：添加书籍

```java
@RequestMapping("/toAddBook")
public String toAddPaper() {
    return "addBook";
}
@RequestMapping("/addBook")
public String addPaper(Books books) {
    System.out.println(books);
    bookService.addBook(books);
    return "redirect:/book/allBook";
}
```

添加书籍页面：**addBook.jsp**

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>新增书籍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>新增书籍</small>
                </h1>
            </div>
        </div>
    </div>
    <form action="${pageContext.request.contextPath}/book/addBook" method="post">
        书籍名称：<input type="text" name="bookName"><br><br><br>
        书籍数量：<input type="text" name="bookCounts"><br><br><br>
        书籍详情：<input type="text" name="detail"><br><br><br>
        <input type="submit" value="添加">
    </form>
</div>
```

BookController 类编写 ， 方法三：修改书籍

```java
@RequestMapping("/toUpdateBook")
public String toUpdateBook(Model model, int id) {
    Books books = bookService.queryBookById(id);
    System.out.println(books);
    model.addAttribute("book",books );
    return "updateBook";
}
@RequestMapping("/updateBook")
public String updateBook(Model model, Books book) {
    System.out.println(book);
    bookService.updateBook(book);
    Books books = bookService.queryBookById(book.getBookID());
    model.addAttribute("books", books);
    return "redirect:/book/allBook";
}
```

修改书籍页面 **updateBook.jsp**

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>修改信息</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">
    <div class="row clearfix">
        <div class="col-md-12 column">
            <div class="page-header">
                <h1>
                    <small>修改信息</small>
                </h1>
            </div>
        </div>
    </div>
    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
        <input type="hidden" name="bookID" value="${book.getBookID()}"/>
        书籍名称：<input type="text" name="bookName" value="${book.getBookName()}"/>
        书籍数量：<input type="text" name="bookCounts" value="${book.getBookCounts()}"/>
        书籍详情：<input type="text" name="detail" value="${book.getDetail() }"/>
        <input type="submit" value="提交"/>
    </form>
</div>
```

BookController 类编写 ， 方法四：删除书籍

```java
@RequestMapping("/del/{bookId}")
public String deleteBook(@PathVariable("bookId") int id) {
    bookService.deleteBookById(id);
    return "redirect:/book/allBook";
}
```

**配置Tomcat，进行运行！**

到目前为止，这个SSM项目整合已经完全的OK了，可以直接运行进行测试！这个练习十分的重要，大家需要保证，不看任何东西，自己也可以完整的实现出来！

**项目结构图**

![ssm项目整合图2](https://github.com/TangerineSea/KnowledgePond/raw/074208a5d7eed039aceb613df278c1d7059470ed/picture/ssm%E9%A1%B9%E7%9B%AE%E6%95%B4%E5%90%882.png)

### 7、小结及展望

这个是同学们的第一个SSM整合案例，一定要烂熟于心！

SSM框架的重要程度是不言而喻的，学到这里，大家已经可以进行基本网站的单独开发。但是这只是增删改查的基本操作。可以说学到这里，大家才算是真正的步入了后台开发的门。也就是能找一个后台相关工作的底线。

或许很多人，工作就做这些事情，但是对于个人的提高来说，还远远不够！

我们后面还要学习一些 SpringMVC 的知识！

- Ajax 和 Json
- 文件上传和下载
- 拦截器

SpringBoot、SpringCloud开发！

### 8、实现查询书籍功能

前端页面增加一个输入框和查询按钮

```html
<div class="col-md-4 column"></div>
<div class="col-md-4 column">
    <form class="form-inline" action="/book/queryBook" method="post" style="float: right">
        <input type="text" name="queryBookName" class="form-control" placeholder="输入查询书名" required>
        <input type="submit" value="查询" class="btn btn-primary">
    </form>
</div>
```

编写查询的Controller

```java
@RequestMapping("/queryBook")
public String queryBook(String queryBookName,Model model){
    System.out.println("要查询的书籍:"+queryBookName);
    //业务逻辑还没有写
    return "allBook";
}
```

由于底层没有实现，所以我们要将底层代码先搞定

Mapper接口

```java
//根据id查询,返回一个Book
Books queryBookByName(String bookName);
```

Mapper.xml

```xml
<!--根据书名查询,返回一个Book-->
<select id="queryBookByName" resultType="Books">
    select * from ssmbuild.books
    where bookName = #{bookName}
</select>
```

Service接口

```java
//根据id查询,返回一个Book
Books queryBookByName(String bookName);
```

Service实现类

```java
public Books queryBookByName(String bookName) {
    return bookMapper.queryBookByName(bookName);
}
```

完善Controller

```java
@RequestMapping("/queryBook")
public String queryBook(String queryBookName,Model model){
    System.out.println("要查询的书籍:"+queryBookName);
    Books books = bookService.queryBookByName(queryBookName);
    List<Books> list = new ArrayList<Books>();
    list.add(books);
    model.addAttribute("list", list);
    return "allBook";
}
```

测试，查询功能OK！

无聊优化！我们发现查询的东西不存在的时候，查出来的页面是空的，我们可以提高一下用户的体验性！

在前端添加一个展示全部书籍的按钮

![ssm项目整合图3](https://github.com/TangerineSea/KnowledgePond/raw/074208a5d7eed039aceb613df278c1d7059470ed/picture/ssm%E9%A1%B9%E7%9B%AE%E6%95%B4%E5%90%88%E5%9B%BE3.png)

```java jsp
2. 并在后台增加一些判断性的代码！
   ```java
   @RequestMapping("/queryBook")
   public String queryBook(String queryBookName,Model model){
       System.out.println("要查询的书籍:"+queryBookName);
       //如果查询的数据存在空格，则优化
       Books books = bookService.queryBookByName(queryBookName.trim()); 
       List<Books> list = new ArrayList<Books>();
       list.add(books);
       //如果没有查出来书籍，则返回全部书籍列表
       if (books==null){
           list = bookService.queryAllBook();
           model.addAttribute("error", "没有找到本书！");
       }
       model.addAttribute("list", list);
       return "allBook";
   }
```
3. 将错误信息展示在前台页面！完整的查询栏代码
   ```jsp
   <div class="row">
       <div class="col-md-4 column">
           <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增</a>
           <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/allBook">显示全部书籍</a>
       </div>
       <div class="col-md-8 column">
           <form class="form-inline" action="/book/queryBook" method="post" style="float: right">
               <span style="color:red;font-weight: bold">${error}</span>
               <input type="text" name="queryBookName" class="form-control" placeholder="输入查询书名" required>
               <input type="submit" value="查询" class="btn btn-primary">
           </form>
       </div>
   </div>
```

