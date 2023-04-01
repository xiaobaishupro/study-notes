## 整合mybatis-plus（quickstart）

#### 1.pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.10</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.xj</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>demo</description>

    <properties>
        <java.version>1.8</java.version>
        <mybatis-plus-boot-starter.version>2.2.0</mybatis-plus-boot-starter.version>
        <mysql.version>5.1.47</mysql.version>
        <commons-lang3.version>3.6</commons-lang3.version>
        <hutool-all.version>4.6.2</hutool-all.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- mybatis-plus begin =================================== -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>${mybatis-plus-boot-starter.version}</version>
        </dependency>
        <!-- mybatis-plus end -->

        <!-- ========================= 数据库相关 ========================== -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>
        <!-- 阿里数据库连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.18</version>
        </dependency>

        <!-- ========================= 常用库依赖 ========================== -->
        <!-- lombok插件 -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!-- Hutool工具类 -->
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>${hutool-all.version}</version>
        </dependency>
        <!-- StringUtils工具类 -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>${commons-lang3.version}</version>
        </dependency>
    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

#### 2.application.yml

```yml
# 配置端口
server:
  port: 8080
  servlet:
    #    context-path: /api
    application-display-name: demo

spring:
  application:
    name: demo
  profiles:
    active: dev
  # 配置数据源
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/study_xj?allowMultiQueries=true&useUnicode=true&characterEncoding=UTF8&zeroDateTimeBehavior=convertToNull&useSSL=false # MySQL在高版本需要指明是否进行SSL连接 解决则加上 &useSSL=false
    name: demo
    username: root
    password: root
    # 使用druid数据源
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    filters: stat
    maxActive: 20
    initialSize: 1
    maxWait: 60000
    minIdle: 1
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: select 'x'
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    maxOpenPreparedStatements: 20

management:
  security:
    enabled: false

# mybatis-plus相关配置
mybatis-plus:
  # xml扫描，多个目录用逗号或者分号分隔（告诉 Mapper 所对应的 XML 文件位置）
  mapper-locations: classpath:**/*Mapper.xml
  # 以下配置均有默认值,可以不设置
  global-config:
    #主键类型  0:"数据库ID自增", 1:"用户输入ID",2:"全局唯一ID (数字类型唯一ID)", 3:"全局唯一ID UUID";
    id-type: 0
    #字段策略 0:"忽略判断",1:"非 NULL 判断"),2:"非空判断"
    field-strategy: 2
    #驼峰下划线转换
    db-column-underline: true
    #刷新mapper 调试神器
    refresh-mapper: false
    #数据库大写下划线转换
    #capital-mode: true
    #序列接口实现类配置
    #key-generator: com.baomidou.springboot.xxx
    #逻辑删除配置
    #logic-delete-value: 0 # 逻辑已删除值(默认为 1)
    #logic-not-delete-value: 1 # 逻辑未删除值(默认为 0)
    #自定义填充策略接口实现
    #    meta-object-handler: com.xj.config.MyMetaObjectHandler
    #自定义SQL注入器
    #sql-injector: com.baomidou.springboot.xxx
  configuration:
    # 是否开启自动驼峰命名规则映射:从数据库列名到Java属性驼峰命名的类似映射
    map-underscore-to-camel-case: true
    cache-enabled: false
    # 如果查询结果中包含空值的列，则 MyBatis 在映射的时候，不会映射这个字段
    #    call-setters-on-nulls: true
    # 这个配置会将执行的sql打印出来，在开发或测试的时候可以用
    #    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    # 解决oracle更新数据为null时无法转换报错，mysql不会出现此情况
    jdbc-type-for-null: 'null'
```

#### 3.MybatisPlusConfig

```java
@EnableTransactionManagement
@Configuration
@MapperScan("com.xj.demo.**.mapper*") // 扫描 Mapper 文件夹 【注：根据自己的项目结构配置】
public class MybatisPlusConfig {
    /**
     * mybatis-plus分页插件<br>
     * 文档：https://mp.baomidou.com/guide/page.html <br>
     */
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }
}
```

#### 4.user实体类

```java
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
@TableName("user")
public class User extends Model<User> {

    @TableId(value="user_id", type= IdType.AUTO)
    private Integer userId;

    @TableField(value="user_name")
    private String userName;

    @Override
    protected Serializable pkVal() {
        return this.userId;
    }
}
```

#### 5.userMapper

```java
public interface UserMapper extends BaseMapper<User> {
}
```

#### 6.userService

```java
public interface UserService extends IService<User> {
}
```

#### 7.userServiceImpl

```java
@Service
public class userServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
}
```

#### 8.测试

```java
@SpringBootTest
class DemoApplicationTests {


    /**
     * 查
     */
    @Test
    void select() {
        User user = new User();
        user.setUserId(3);
        User user1 = user.selectById();
        System.out.println(user1);
    }

    /**
     * 查所有
     */
    @Test
    void selectAll() {
        User user = new User();
        List<User> users = user.selectAll();
        users.forEach(System.out::println);
    }

    /**
     * 添加
     */
    @Test
    void add() {
        User user = User.builder()
                .userId(9)
                .userName("哆啦a梦")
                .build();
        user.insert();
    }

    /**
     * 修改
     */
    @Test
    void update(){
        User user = User.builder()
                .userId(5)
                .userName("哆啦a梦yy")
                .build();
        user.updat
            eById();
    }

    /**
     * 删除
     */
    @Test
    void delete(){
        User user = new User();
        user.setUserId(3);
        user.deleteById();
    }

    /**
     * 分页查询
     */
    @Test
    void testSelectAllPage() throws Exception{
        User user = new User();
        Page<User> page = user.selectPage(new Page<User>(1, 10), null);
        System.out.println(page);
    }

}
```