## mybatis学习

参考：

https://mybatis.org/mybatis-3/zh/getting-started.html

http://notes.xiyankt.com/#/%E6%8C%81%E4%B9%85%E5%8C%96%E6%A1%86%E6%9E%B6/mybatis

### mybatis入门

##### 1.不用springboot的自动配置

依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <!--mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!--mybatis-->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

mybatis-config

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- #开启mybatis驼峰式命名规则自动转换 -->
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/study_xj"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="mapper/userMapper.xml"/>
    </mappers>
</configuration>
```

测试一下：

```java
@Controller
public class controller {

    @GetMapping("/list")
    @ResponseBody
    public List<User> getList() {
        InputStream inputStream = null;
        List<User> user = null;
        try {
            inputStream = Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
            try (SqlSession session = sqlSessionFactory.openSession()) {
                UserMapper mapper = session.getMapper(UserMapper.class);
                user = mapper.getList();
                System.out.println(user);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return user;
    }
}
```

sqlSession.commit();提交事务，涉及增删改操作时

##### 2.核心配置文件

mybatis-config.xml

configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
	environment（环境变量）
		transactionManager（事务管理器）
		dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
需要详细学习看：https://mybatis.org/mybatis-3/zh/configuration.html

##### 3.xml映射文件

SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：

- `cache` – 该命名空间的缓存配置。

- `cache-ref` – 引用其它命名空间的缓存配置。

- `resultMap` – 描述如何从数据库结果集中加载对象，是最复杂也是最强大的元素。

- `parameterMap` – 老式风格的参数映射。此元素已被废弃，并可能在将来被移除！请使用行内参数映射。文档中不会介绍此元素。

- `sql` – 可被其它语句引用的可重用语句块。

- `insert` – 映射插入语句。

- `update` – 映射更新语句。

- `delete` – 映射删除语句。

- `select` – 映射查询语句。

  详细学习：https://mybatis.org/mybatis-3/zh/sqlmap-xml.html

##### 4.动态sql

动态 SQL 是 MyBatis 的强大特性之一。如果你使用过 JDBC 或其它类似的框架，你应该能理解根据不同条件拼接 SQL 语句有多痛苦，例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL，可以彻底摆脱这种痛苦。

使用动态 SQL 并非一件易事，但借助可用于任何 SQL 映射语句中的强大的动态 SQL 语言，MyBatis 显著地提升了这一特性的易用性。

如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

模板：

详细学习：https://mybatis.org/mybatis-3/zh/dynamic-sql.html

##### 5.sqlsession

SqlSession是一个会话，相当于JDBC中的一个Connection对象，是整个Mybatis运行的核心。SqlSession接口提供了查询，插入，更新，删除方法，Mybatis中所有的数据库交互都由SqlSession来完成，分析和理解SqlSession的运行原理是学习整个Mybatis的重点。

使用 MyBatis 的主要 Java 接口就是 SqlSession。你可以通过这个接口来执行命令，获取映射器实例和管理事务。在介绍 SqlSession 接口之前，我们先来了解如何获取一个 SqlSession 实例。SqlSessions 是由 SqlSessionFactory 实例创建的。SqlSessionFactory 对象包含创建 SqlSession 实例的各种方法。而 SqlSessionFactory 本身是由 SqlSessionFactoryBuilder 创建的，它可以从 XML、注解或 Java 配置代码来创建 SqlSessionFactory。

> 后续使用都是配合spring框架，SqlSession 将被依赖注入框架创建并注入，所以你不需要使用 SqlSessionFactoryBuilder 或者 SqlSessionFactory

详细学习：https://mybatis.org/mybatis-3/zh/java-api.html#sqlSessions

### mybatis提升

##### spring Mybatis的三种mappers注册方式

- 第一种注册方式（每一个xml文件都在核心配置文件中注册）

```xml
   <mappers>
        <mapper resource="com/cling/mapper/UserMapper.xml"></mapper>
    </mappers>
```

- 第二种注册方式(类注册)

```xml
<mappers>
    <mapper class="com.cling.mapper.UserMapper"></mapper>
</mappers>
```

- 第三种注册方式（包扫描）

```xml
<mappers>
    <package name="com.cling.mapper"/>
</mappers>
```

当然也可以在springmvc中注册

```xml
 <!--在applicationcontext_mapper.xml中注册mapper.xml 文件-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
 <!-- 包名是mapper接口所在的包名。 MapperScannerConfigurer会扫描这个包中
 的所有接口，把每个接口都执行一次getMapper()方法，得到每个接口的mapper对象。
 创建好的mapper对象放入到spring的容器中的。mapper对象的默认名称是 接口名首字母小写 -->
        <property name="basePackage" value="com.chen.mapper"/>
    </bean>

```

springboot中注册mapper

> 方式一：@Mapper
>
> 方式二：@MapperScan
>
> ```java
> // @MapperScan: 找到mapper接口和mapper文件
> // basePackages：mapper接口所在的包名(可指定多个包)
> @SpringBootApplication
> @MapperScan(basePackages = {"com.xj.mapper"})
> public class Application {
> }
> ```
>
> 方式三：yml中配置：
>
> #mapper文件的自定义位置
> mybatis.mapper-locations=classpath:mapper/*.xml
