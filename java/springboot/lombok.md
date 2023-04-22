## Lombok 简介

Lombok 是一个 Java 库，能自动插入编辑器并构建工具，简化 Java 开发。通过添加注解的方式，不需要为类编写 getter/setter/equals 等方法，同时可以自动化日志变量。简而言之：Lombok 能以简单的注解形式来简化 Java 代码，提高开发人员的开发效率。

## 插件安装

由于 Lombok 会自动帮我们生成一些代码，这些代码在 source 阶段是没有的，编译之后才会用，为了避免开发工具如 IDEA 报错，一般在使用 Lombok 的时候还需要安装一个 IDEA 插件。

安装方式非常简单，直接在 IDEA 市场搜索 Lombok，点击 install 按钮即可，安装完成后，重启 IDEA。

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```



Lombok 主要包含如下一些内容：

- val：类似于前端 js 中的 var。
- @NotNull：这个用于方法参数，可以自动校验方法参数是否为空。
- @Cleanup：这个用在局部变量上，在变量使用结束时会自动调用 close 方法释放资源，典型用法就是 IO 流的相关操作中使用该注解。
- @Getter 用在字段或者类上，当用在字段上时会自动生成字段的 getter；当用在类上时，会自动生成该类所有非静态字段的 getter，使用该注解还可以调整 getter 方法的访问级别。
- @Setter：同上。
- @ToString：这个注解用在类上，自动生成类的 toString 方法，可以做一些定制，比如不使用某个字段，不调用 getter 等。
- @EqualsAndHashCode：这个注解用在类上，自动生成类中所有非静态非瞬时字段（添加了 `@Transient` 注解的字段为瞬时字段）的 equals 方法和 hashCode 方法。
- @NoArgsConstructor：这个注解用在类上，会自动生成一个无参构造函数。
- @AllArgsConstructor：这个注解用在类上，会自动生成一个包含所有参数的构造函数。
- @RequiredArgsConstructor：这个注解也是作用在类上，它会为 final 字段和标记了 @NotNull 的字段生成构造函数。
- @Data：这个注解也是用在类上，使用该注解相当于同时应用了 `@Getter`、`@Setter`、`@ToString`、`@EqualsAndHashCode`、`@RequiredArgsConstructor`。如果已经定义了一个构造方法，就不会再自动生成构造方法了。
- @Value：这个注解用在类上，和 @Data 类似，但是用于不可变类型。生成的类和所有字段都设置为 final，所有字段都为 private，自动生成 Getter 但是没有 Setter，会生成初始化所有字段的构造函数。相当于同时应用了 `final @ToString`、 `@EqualsAndHashCode`、 `@AllArgsConstructor`、`@FieldDefaults(makeFinal = true, level = AccessLevel.PRIVATE)` 和 `@Getter`。
- @CommonsLog：日志注解，相当于定义变量 `log = org.apache.commons.logging.LogFactory.getLog(LogExample.class)`。
- @JBossLog：日志注解：相当于定义变量 `log = org.jboss.logging.Logger.getLogger(LogExample.class)`。
- @Log：日志注解，相当于定义变量 `log = java.util.logging.Logger.getLogger(LogExample.class.getName())`。
- @Log4j：日志注解，相当于定义变量 `log = org.apache.log4j.Logger.getLogger(LogExample.class)`。
- @Log4j2：日志注解，相当于定义变量 `log = org.apache.logging.log4j.LogManager.getLogger(LogExample.class)`。
- @Slf4j：日志注解，相当于定义变量 `log = org.slf4j.LoggerFactory.getLogger(LogExample.class)`。
- @XSlf4j：日志注解，相当于定义变量 `log = org.slf4j.ext.XLoggerFactory.getXLogger(LogExample.class)`。