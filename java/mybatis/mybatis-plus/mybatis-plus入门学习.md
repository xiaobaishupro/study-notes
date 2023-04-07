## mybatis-plus入门学习

官网学习：https://baomidou.com/

### 简介                                                         

[MyBatis-Plus (opens new window)](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis (opens new window)](https://www.mybatis.org/mybatis-3/)的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

### 基础

和mybatis一样，主要学习不一样的东西

### 不一样的

#### 启动项目配置

##### 依赖

启动

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3</version>
</dependency>
```

自动生成代码

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.1</version>
</dependency>
```

```xml
    <dependency>
        <groupId>org.freemarker</groupId>
        <artifactId>freemarker</artifactId>
        <version>2.3.28</version>
    </dependency>
```

##### yml

```yml
    # mybatis-plus相关配置
    mybatis-plus:
      # xml扫描，多个目录用逗号或者分号分隔（告诉 Mapper 所对应的 XML 文件位置）
      mapper-locations: classpath:**/*Mapper.xml
      configuration:
        #        日志
        log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
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
```

> 注意：
>
> ```yml
> mybatis-plus:
> configuration:
>  logImpl: org.apache.ibatis.logging.slf4j.Slf4jImpl # 默认输出到日志文件
>  logImpl: org.apache.ibatis.logging.stdout.StdOutImpl #默认输出到控制台
> ```
>
> 

#### 注解

官网学习：https://baomidou.com/pages/223848/

掌握常用的就行。

> 注意：注解里面的配置的优先级大于yml中global-config的配置，一般都有默认配置，除非特殊要求才会在注解中配置

##### 常用注解

###### 1.@tableName

@Tablename注解注解用来将指定的数据库表和 bean进行映射。多用于项目中entity包下 实体类中

```java
@Tablename("book")
public class Book {
}
```

###### 2.@tableId

**写法：**@TableId(value=“数据库主键字段”,type = IdType.六种类型之一)

type默认IdType.ASSIGN_ID，基于雪花算法的策略生成数据id，与数据库id是否设置自增无关

```java
@TableId(value = "book_id", type = IdType.AUTO)
private Integer bookId;
```

| Auto          | 数据库自增                  |
| ------------- | --------------------------- |
| Input         | 自行输入                    |
| ID_Worker     | 分布式全局唯一ID 长整型类型 |
| UUID          | 32位UUID字符串              |
| NONE          | 无状态                      |
| ID_WORKER_STR | 分布式全局唯一ID 字符串类型 |

###### 3.@TableField

主要用来解决实体类的字段名与数据库中的字段名不匹配的问题（数据库user_addr，字段useraddr未驼峰）。

```java
@TableField("book_name")
private String bookName;
```

###### 4.@TableLogic

> ①逻辑删除
>
> 物理删除：真实删除，将对应数据从数据库中删除，之后查询不到此条被删除的数据
>
> 逻辑删除：假删除，将对应数据中代表是否被删除字段的状态修改为“被删除状态”，之后在数据库 中仍旧能看到此条数据记录
>
> 使用场景：可以进行数据恢复 
>
> ②实现逻辑删除
>
> 数据库中创建逻辑删除状态列，设置默认值为0

```java
@TableLogic
private Integer isDeleted;
```

> 测试删除功能，真正执行的是修改 
>
> UPDATE t_user SET is_deleted=1 WHERE id=? AND is_deleted=0
>
> 测试查询功能，被逻辑删除的数据默认不会被查询
>
> SELECT id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0

###### 5.@EnumValue

```java
@Getter
public enum SexEnum {
    MALE(1, "男"),
    FEMALE(2, "女");
    @EnumValue
    private Integer sex;
    private String sexName;

    SexEnum(Integer sex, String sexName) {
        this.sex = sex;
        this.sexName = sexName;
    }
}
```

@EnumValue所标识的属性值会存储到数据库，相当于语句INSERT INTO t_user ( username, age, sex ) VALUES ( ?, ?, ? )

##### 其他注解

###### 1.@Version

@Version就是实现乐观锁的重要注解，当要更新数据库中的数据时，例如价格，version 就会加 1，如果where语句中的version版本不对，则更新失败。

#### 代码生成

官网学习：https://baomidou.com/pages/779a6e/

最好配置一套适用于自己的模板

也可以使用mybatisx插件带有该功能，需要在idea中连接数据库，鼠标右键点击表就有该功能。

还可以用这个：https://github.com/davidfantasy/mybatis-plus-generator-ui

#### crud

官网学习：https://baomidou.com/pages/49cc81/#service-crud-%E6%8E%A5%E5%8F%A3

很多自带的增删改查方法，简单的操作不需要写重复代码了，自行一个个试试功能。

mapper

```java
public interface UserMapper extends BaseMapper<User> {
}
```

service

```java
public interface UserService extends IService<User> {
}
```

impl

```java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {}
```

#### 条件构造器

官网学习：https://baomidou.com/pages/10c804/#abstractwrapper

#### 插件

