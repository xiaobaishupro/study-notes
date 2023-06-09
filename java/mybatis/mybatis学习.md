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

映射

```xml
<resultMap id="personResultMap" type="com.example.mybatis_plus.entity.Person">
    <id property="personId" column="person_id"/>
    <result property="personName" column="person_name"/>
    <result property="personAge" column="person_age"/>
    <result property="personEmail" column="person_email"/>
</resultMap>
```

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

if

```xml
<select id="getList" resultType="com.example.mybatis_plus.entity.Person" parameterType="com.example.mybatis_plus.entity.Person">
    select * from person
    <where>
        <if test="personName != null and personName != ''">
            and person_name like concat('%',#{personName},'%')
        </if>
        <if test="personEmail != null and personEmail != ''">
            and person_email like concat('%',#{personEmail},'%')
        </if>
    </where>
</select>
```

choose

```xml
<select id="getList1" resultType="com.example.mybatis_plus.entity.Person"
        parameterType="com.example.mybatis_plus.entity.Person">
    select * from person
    <where>
        <choose>
            <when test="personName != null and personName != ''">
                and person_name like concat('%',#{personName},'%')
            </when>
            <when test="personEmail != null and personEmail != ''">
                and person_email like concat('%',#{personEmail},'%')
            </when>
            <otherwise>
                and 1=0;
            </otherwise>
        </choose>
    </where>
```

set

```xml
<update id="update">
    update person
    <set>
        <if test="personName != null and personName != ''">
            person_name = #{personName},
        </if>
        <if test="personAge != null and personAge != ''">
            person_Age =#{personAge},
        </if>
        <if test="personEmail != null and personEmail != ''">
            person_email =#{personEmail},
        </if>
    </set>
      where person_id = #{personId}
</update>
```

foreach

```xml
<select id="getListByIds" resultType="com.example.mybatis_plus.entity.Person">
    select * from person where person_id in
    <foreach collection="list" item="item" open="(" close=")" separator="," index="index">
    #{item}
    </foreach>
</select>
```

详细学习：https://mybatis.org/mybatis-3/zh/dynamic-sql.html

##### 5.sqlsession

SqlSession是一个会话，相当于JDBC中的一个Connection对象，是整个Mybatis运行的核心。SqlSession接口提供了查询，插入，更新，删除方法，Mybatis中所有的数据库交互都由SqlSession来完成，分析和理解SqlSession的运行原理是学习整个Mybatis的重点。

使用 MyBatis 的主要 Java 接口就是 SqlSession。你可以通过这个接口来执行命令，获取映射器实例和管理事务。在介绍 SqlSession 接口之前，我们先来了解如何获取一个 SqlSession 实例。SqlSessions 是由 SqlSessionFactory 实例创建的。SqlSessionFactory 对象包含创建 SqlSession 实例的各种方法。而 SqlSessionFactory 本身是由 SqlSessionFactoryBuilder 创建的，它可以从 XML、注解或 Java 配置代码来创建 SqlSessionFactory。

> 后续使用都是配合spring框架，SqlSession 将被依赖注入框架创建并注入，所以你不需要使用 SqlSessionFactoryBuilder 或者 SqlSessionFactory

详细学习：https://mybatis.org/mybatis-3/zh/java-api.html#sqlSessions

### mybatis提升

##### 1.Mybatis的三种mappers注册方式

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

##### 2.mybatis缓存

**一级缓存**：SqlSession级别的缓存，缓存的数据只在SqlSession内有效。

如果sqlSession去执行commit操作（插入、更新、删除），会清除当前sqlsession一级缓存。

默认开启一级缓存。

关闭：

```xml
<setting name="localCacheScope" value="STATEMENT"/>
```

**二级缓存**：mapper级别的缓存，同一个namespace公用这一个缓存，所以对SqlSession是共享的，二级缓存需要我们手动开启。

1.在 mybatis-config.xml 的配置文件中进行显示配置

```xml
<settings>
      <!--显示的开启全局缓存-->
      <setting name="cacheEnabled" value="true"/>
</settings>

```

2.在 Mapper.xml 文件中添加cache标签

```xml
<!--在当前 Mapper.xml文件开启二级缓存-->
<cache/>
```

3.在mapper接口上添加**@CacheNamespace**

**总结**：

- 二级缓存是基于namespace级别的，在同一个Mapper下有效
- 所有的数据都会先放在一级缓存中
- 只有当会话提交或关闭时，才会提交到二级缓存中
- 当 Mybatis 调用 Dao 层查询数据库时，先查询二级缓存，二级缓存中无对应数据，再去查询一级缓存，一级缓存中也没有，最后去数据库查找。

##### 3.分页插件

示例

```xml
<!--MyBatis分页插件-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.2</version>
</dependency>
```



```java
@GetMapping
public Result getList(Person person, @RequestParam Integer pageNum, @RequestParam Integer pageSize){
    PageHelper.startPage(pageNum, pageSize);
    List<Person> list = personMapper.getList(person);
    PageInfo<Person> info = new PageInfo<>(list);
    return Result.suc(info);
}
```

##### 4.${}和#{}

`#{}`占位符会被解析为**JDBC**中的预编译语句（**Prepared Statement**）中的参数占位符`?`，一个`#{}`会被解析为一个`?`。

`${}`占位符标识的位置会直接被替换为入参，是简单的字符串替换。

\#方式能够很大程度防止sql注入，一般能用#的就别用$，**MyBatis排序时使用order by 动态参数时需要注意，用$而不是#**

##### 5.高级映射

###### 联表查询

1.一对一映射association

```xml
<resultMap id="personResultMap" type="com.example.mybatis_plus.entity.Person">
    <id property="personId" column="person_id"/>
    <result property="personName" column="person_name"/>
    <result property="personAge" column="person_age"/>
    <result property="personEmail" column="person_email"/>
    <association property="user"  javaType="com.example.mybatis_plus.entity.User">
        <id property="id" column="id"/>
        <result property="age" column="age"/>
        <result property="name" column="name"/>
        <result property="email" column="email"/>
    </association>
</resultMap>
<select id="getList" resultMap="personResultMap"
        parameterType="com.example.mybatis_plus.entity.Person">
    <include refid="select1"></include>
    <where>
        <if test="personName != null and personName != ''">
            and person_name like concat('%',#{personName},'%')
        </if>
        <if test="personEmail != null and personEmail != ''">
            and person_email like concat('%',#{personEmail},'%')
        </if>
    </where>
</select>
```

也可以

```xml
<resultMap id="personResultMap" type="com.example.mybatis_plus.entity.Person">
    <id property="personId" column="person_id"/>
    <result property="personName" column="person_name"/>
    <result property="personAge" column="person_age"/>
    <result property="personEmail" column="person_email"/>
    <association property="user" column="user_id" resultMap="userResultMap">
    </association>
</resultMap>
<resultMap id="userResultMap" type="com.example.mybatis_plus.entity.User">
    <id property="id" column="id"/>
    <result property="age" column="age"/>
    <result property="name" column="name"/>
    <result property="email" column="email"/>
</resultMap>
<select id="getList" resultMap="personResultMap"
        parameterType="com.example.mybatis_plus.entity.Person">
    <include refid="select1"></include>
    <where>
        <if test="personName != null and personName != ''">
            and person_name like concat('%',#{personName},'%')
        </if>
        <if test="personEmail != null and personEmail != ''">
            and person_email like concat('%',#{personEmail},'%')
        </if>
    </where>
</select>
```

sql

```xml
<sql id="select1">
    select person_id,
           person_name,
           person_age,
           person_email,
           u.id,
           u.age,
           u.name,
           u.email
    from person p
             left join user u on u.id = p.user_id
</sql>
```

```xml
<resultMap id="personResultMap" type="com.example.mybatis_plus.entity.Person" autoMapping="true">
</resultMap>
```

autoMapping="true"没写的自动映射

2.一对多关系，collection，例如一个作者对应多本书，书中需要存储作者id

```xml
        <!--使用collection属性，ofType为集合内元素的类型-->
        <collection property="bookList" ofType="dulk.learn.mybatis.pojo.Book" columnPrefix="book_">
            <id property="id" column="id"/>
            <result property="name" column="name" />
            <result property="price" column="price" />
        </collection>
```



###### 嵌套查询

```xml
<resultMap id="bookResultMap" type="com.example.mybatis_plus.entity.Book" autoMapping="true"> 
    <association property="user" column="user_id"   select="getUser" fetchType="lazy"/>
</resultMap>
<select id="getUser" resultType="com.example.mybatis_plus.entity.User">
    select * from `user` where id = #{userId}
</select>
<select id="getByUserId" resultMap="bookResultMap">
    select * from book where user_id = #{userId}
</select>
```

开启懒加载，局部的加载策略的优先级高于全局的加载策略

```xml
fetchType="lazy"
```
