# springboot注解篇

## ioc注解

#### @Configuration

基于@Component实现，表明当前类是spring的一个配置类，作用是替代spring的applicationContext.xml。

#### @ComponentScan

用于指定创建容器时要扫描的包。

#### @Repository

@Repository 只能标注在 DAO 类上，这是因为该注解的作用不只是将类识别为Bean，同时它还能将所标注的类中抛出的数据访问异常封装为 Spring 的数据访问异常类型。 Spring本身提供了一个丰富的并且是与具体的数据访问技术无关的数据访问异常结构，用于封装不同的持久层框架抛出的异常，使得异常独立于底层的框架。

Spring 2.5 在 @Repository的基础上增加了功能类似的额外三个注解：@Component、@Service、@Constroller，它们分别用于软件系统的不同层次：

- **@Component** 是一个泛化的概念，仅仅表示一个组件 (Bean) ，可以作用在任何层次。
- **@Service** 通常作用在业务层，但是目前该功能与 @Component 相同。
- **@Constroller** 通常作用在控制层，但是目前该功能与 @Component 相同。

#### @Controller

标识一个Spring类是Spring MVC controller处理器

@Controller类中的方法可以直接通过返回String跳转到jsp、ftl、html等模版页面。在方法上加@ResponseBody注解，也可以返回实体对象。

#### @ResponseBody

@ResponseBody的作用其实是将java对象转为json格式的数据。一般在异步获取数据时使用。

#### @RestController

@RestController是@Controller和@ResponseBody的结合体，两个标注合并起来的作用。

@RestController类中的所有方法只能返回String、Object、Json等实体对象，不能跳转到模版页面。

@Controller, @RestController的共同点: 都是用来表示Spring某个类的是否可以接收HTTP请求

#### @Mapper

@Mapper是mybatis自身带的注解。在spring程序中，mybatis需要找到对应的mapper，在编译时生成动态代理类，与数据库进行交互，这时需要用到@Mapper注解

但是有时候当我们有很多mapper接口时，就需要写很多@Mappe注解，这样很麻烦，有一种简便的配置化方法便是在启动类上使
用@MapperScan注解。

@mapper和@Repository比较：

相同点：@Mapper和@Repository都是作用在dao层接口，使得其生成代理对象bean，交给spring 容器管理
对于mybatis来说，都可以不用写mapper.xml文件

不同点：

- @Mapper不需要配置扫描地址，可以单独使用，如果有多个mapper文件的话，可以在项目启动类中加入@MapperScan(“mapper文件所在包”)

- @Repository不可以单独使用，否则会报错误，要想用，必须配置扫描地址

  ```xml
  <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <property name="basePackage" value="sen.yuhuang.dao"/>
  </bean>
  ```

#### @MapperScan

指定要变成实现类的接口所在的包，然后包下面的所有接口在编译之后都会**生成相应的实现类**，也可以在启动类上

```java
@EnableTransactionManagement
@Configuration
@MapperScan("com.xj.demo.**.mapper*") // 扫描 Mapper 文件夹 【注：根据自己的项目结构配置】
public class MybatisPlusConfig {
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }
}
```

#### @Bean

Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的容器中。

**Component和Bean**

1.Component注解表明一个类会作为组件类，并告知Spring要为这个类创建bean。

2.Bean注解告诉Spring这个方法将会返回一个对象，这个对象要注册成Spring应用上下文中的bean。通常方法体包含了最终产生bean实例的逻辑。

区别：

- Component通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中
- Bean注解通常是我们在标有该注解的方法中定义产生这个bean的逻辑。
- Component用在类上，Bean用在方法上。

#### @Autowired

依赖注入，默认按类型装配，@Qualifier和Autowired配合使用，指定bean的名称，也可以做到按名称装配。

三种装配方式：

- 属性注入

  ```java
  @Autowired
  private UserService userService;
  ```

- 构造器注入

  ```java
  private final UserService userService;
  
  public UserController(UserService userService) {
      this.userService = userService;
  }
  ```

- 方法注入

  ```java
  private UserService userService;
      @Autowired
      public void setUserService(UserService userService){
          this.userService = userService;
      }
  ```

#### @Qualifier

通过将 `@Qualifier` 注解与我们想要使用的特定 **Spring bean** 的名称一起进行装配，**Spring** 框架就能从多个相同类型并满足装配要求的 **bean** 中找到我们想要的。

```java
 @Component
 @Qualifier("userService")
@Autowired
@Qualifier("userService")
```



#### @Resource

@Resource默认按名称装配，当找不到与名称匹配的bean时，才会按类型装配。

#### @Value

由于@Autowired、@Qualifier、@Resource三者自动装配只能针对于注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现。因此有了@Value这个注解，@Value专门用来服务基本类型和String类型。

```java
@Value("#{2*3}")  //#写法 表示6
private int age;

@Value("178")    //普遍写法 178
private int height;

@Value("${man.weight}")  //SpEL的写法一般操作配置文件中数据
private int weight;
```

#### @Import

[参考](https://www.cnblogs.com/qdhxhz/p/16791753.html)

@Import是一个非常有用的注解，它的长处在于你可以通过配置来控制是否注入该Bean，也可以通过条件来控制注入哪些Bean到Spring容器中。

比如我们熟悉的：`@EnableAsync` 、`@EnableCaching`、`@EnableScheduling`等等统一采用的都是借助@Import注解来实现的。

#### @RequestParam

@RequestParam主要用于将请求参数区域的数据映射到控制层方法的参数上

```java
public String queryUserName(@RequestParam String userName)
```

参数：value：参数名字，即入参的请求参数名字，如username表示请求的参数区中的名字为username的参数的值将传入；

required：是否必须，默认是true，表示请求中一定要有相应的参数，否则将报404错误码；

defaultValue：默认值，表示如果请求中没有同名参数时的默认值，默认值可以是SpEL表达式，如“#{systemProperties['java.vm.version']}”。

#### @PathVariable

绑定URI模板变量值

```java
@RequestMapping(value="/users/{userId}/topics/{topicId}")  
public String test(   @PathVariable(value="userId") int userId,    @PathVariable(value="topicId") int topicId)  
```

#### @CookieValue

@CookieValue用于将请求的Cookie数据映射到功能处理方法的参数上。

```java
public String test(@CookieValue(value="JSESSIONID", defaultValue="") String sessionId) 
```

@CookieValue也拥有和@RequestParam相同的三个参数，含义一样。

#### @RequestHeader

@RequestHeader用于将请求的头信息区数据映射到功能处理方法的参数上。

```java
@RequestMapping(value="/header")  
public String test(  
       @RequestHeader("User-Agent") String userAgent,  
       @RequestHeader(value="Accept") String[] accepts)
```

@RequestHeader也拥有和@RequestParam相同的三个参数，含义一样。

#### @ModelAttribute

绑定请求参数到命令对象

#### @SessionAttributes

绑定命令对象到session