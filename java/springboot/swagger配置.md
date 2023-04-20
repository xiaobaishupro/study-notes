## swagger配置

#### 依赖：

```xml
<dependency>
    <groupId>com.spring4all</groupId>
    <artifactId>spring-boot-starter-swagger</artifactId>
    <version>1.5.1.RELEASE</version>
</dependency>
```

> 注意：可能会与springboot版本冲突

#### SwaggerConfig

```java
@EnableSwagger2
@Configuration
public class SwaggerConfig {
    @Bean
    public Docket docket() {
        // 创建一个 swagger 的 bean 实例
        return new Docket(DocumentationType.SWAGGER_2)
                // 配置基本信息
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example"))    //这个是重点
                .paths(PathSelectors.any())
                .build();
    }

    // 基本信息设置
    private ApiInfo apiInfo() {
        Contact contact = new Contact(
                "努力的派大星", // 作者姓名
                "https://gitee.com/a13657926270", // 作者网址
                "xj19148@163.com"); // 作者邮箱
        return new ApiInfoBuilder()
                .title("接口文档") // 标题
                .description("hello") // 描述
                .termsOfServiceUrl("--") // 跳转连接
                .version("1.0") // 版本
                .license("Swagger-的使用")
                .licenseUrl("https://gitee.com/a13657926270")
                .contact(contact)
                .build();
    }
}

```

文档地址：http://localhost:8080/swagger-ui.html

#### 注解

```java
@Api：                *用于类，标识这个类是swagger的资源
@ApiIgnore：          用于类，忽略该 Controller，指不对当前类做扫描
@ApiOperation：       *用于方法，描述 Controller类中的 method接口
@ApiParam：           *用于参数，单个参数描述，与 @ApiImplicitParam不同的是，他是写在参数左侧的。如（ 		                                                @ApiParam(name="userName",value="用户名")String userName）
@ApiModel：           用于类，表示对类进行说明，用于参数用实体类接收
@ApiProperty：        用于方法，字段，表示对model属性的说明或者数据操作更改
@ApiModelProperty
@ApiImplicitParam：   *用于方法，表示单独的请求参数
@ApiImplicitParams：  *用于方法，包含多个 @ApiImplicitParam
@ApiResponse：        用于方法，描述单个出参信息
@ApiResponses：       用于方法，包含多个@ApiResponse
@ApiError：           用于方法，接口错误所返回的信息
```

