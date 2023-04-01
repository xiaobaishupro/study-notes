# springboot配置篇

### mybatis

```properties
# mybatis
mybatis.mapper-locations=classpath:mapper/*.xml
mybatis.type-aliases-package=com.xj.xj.domain
```

mybatis.type-aliases-package ：指定POJO扫描包来让mybatis自动扫描到自定义的POJO

mybatis.mapper-locations：实现mapper接口配置见mapper和接口的绑定

```
resultType 返回类型
<select id="getNameById" resultType="com.example.demo_1.entity.User">
    select name from user where id=#{id}
</select>
```



### springmvc

[Spring注解之@validated的使用](https://www.cnblogs.com/liaojie970/p/9036349.html)

1.spring-boot中可以用@validated来校验数据，如果数据异常则会统一抛出异常，方便异常中心统一处理。

2.Spring团队建议：“在bean中始终使用基于构造函数的依赖注入。始终对强制依赖项使用断言”。
意思是说，用@AutoWired的注入时，尽量用基于构造函数的依赖注入，而不是变量的方式注入。
这就是构造函数方式的依赖注入。@Resource用在变量上比较好，@Autowired用于构造器注入较好。

3.在lombok包下

使用`@Builder`注解，就可以在创建新实例的时候这样写：

```java
Ming mingA = Ming.builder().build();
Ming mingB = Ming.builder()
  	.age(11)
  	.build();
Ming mingD = Ming.builder()
  	.age(11)
  	.name("小明")
  	.build();

```

@SuperBuilder父类的属性可以这样赋值

@RequestParm

这个注解可以帮助我们从前端获取传递的值，值得注意的一个点是前端的form表单中input中的name的值必须和@RequestParm()中的值一样

@PathVariable是用来接收前端传递给后端的参数，并将其传递到url路径中，restful风格

```java
@GetMapping("/hello/{id}")
public String getNameById(@PathVariable("id") Integer id){
    return userService.getNameById(id).getName();
}
```

@RequestBody

@RequestBody是用来接收前端传递给后端的json文件，一般映射到实体类中。

@Param的作用就是给参数命名，比如在mapper里面某方法A（int id），当添加注解后A（@Param("userId") int id），也就是说外部想要取出传入的id值，只需要取它的参数名userId就可以了。将参数值传如SQL语句中，通过#{userId}进行取值给SQL的参数赋值。比如（@Param（“userName”））-->xml中sql语句#{userName}

@Param("user") User u-->\#{user.userName}

========================

# idea配置

ctrl+shift+r全局搜索，找到接口对应的类；

DTO:数据传输对象，主要用于外部接口参数传递封装，接口与接口进行传递使用。

VO：视图对象，主要用于给前端返回页面参数使用。

DO：数据对象，主要用于数据库层传递。

DTO转DO：接口接收参数将参数转化为数据库实体操作数据库使用。

DO转DTO：将数据库层实体转化为DTO返回给客户端。