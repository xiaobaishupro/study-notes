## springcloud学习

优秀网站：理论理解：https://juejin.cn/post/6869264020205993997

https://www.cnblogs.com/xuwujing/p/10273989.html

#### 什么是微服务架构？

根据业务拆分软件模块，每个模块单独运行，每个模块本身是单体。

#### 什么是分布式系统？

如果若干个程序如果部署在若干台不同的计算机上，它们通过网络相互协作最终完成一个服务，那么这些程序组成的系统就可以称为分布式系统。

#### 什么是集群？

计算机集群简称集群是一种计算机系统，它通过一组松散集成的计算机软件和/或硬件连接起来高度紧密地协作完成计算工作。在某种意义上，他们可以被看作是一台计算机。集群系统中的单个计算机通常称为节点，通常通过局域网连接，但也有其它的可能连接方式。集群计算机通常用来改进单个计算机的计算速度和/或可靠性。一般情况下集群计算机比单个计算机，比如工作站或超级计算机性能价格比要高得多

集群技术特点：

- 通过**多台计算机完成同一个工作**，达到更高的效率。

- **两机或多机内容、工作过程等完全一样**。如果一台死机，另一台可以起作用。

  简单理解集群：同一个业务，部署在多个服务器上(不同的服务器运行同样的代码，干同一件事)

#### 分布式和集群的区别

分布式是以缩短单个任务的执行时间来提升效率的，而集群则是通过提高单位时间内执行的任务数来提升效率。

分布式：一个任务分成3个子任务，由3个服务器分别完成3个子任务；每个服务器只能完成某个特定的子任务。例如：客人点菜，需要买菜，炒菜，上菜三个子任务完成，那么由3个人分别完成这三个任务，一个人买菜，一个人炒菜，一个人上菜，这就是分布式。
集群：一个任务可以切成3个任务，由3个服务共同完成这3个任务；每个服务器都能独立完成该任务。例如：客人点菜，店里有三个人，他们每个人都会买菜炒菜和上菜，他们共同完成任务，一个人炒青菜，一个人炖汤，一个人卤肉，这就是集群

#### Spring Cloud与Spring Boot

springboot用来构建微服务，当微服务过多，Spring Cloud来治理服务。

需要治理的问题：

- 怎么解决服务之间的相互调用
- 假如在服务之间的相互调用中，某个服务因为网络或其他原因导致很久没有返回结果，应该怎么处理
- 各个模块的配置如何统一的管理
- 如何屏蔽我的真实访问路径，就是在我的真实访问路径包装一层，类似于虚拟手机号

#### springcloud简介

Spring Cloud是一系列框架的[有序集合](https://baike.baidu.com/item/有序集合/994839?fromModule=lemma_inlink)。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用Spring Boot的开发风格做到一键启动和部署。Spring Cloud并没有重复制造轮子，它只是将各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。

一系列治理微服务的框架集合。

#### springcloud组件（spring Cloud Netflix家族）

- Eureka 注册中心 主管服务的发现与相互调用
- Feign 远程调用者
- Ribbon 负载均衡器
- zuul 网关
- config 配置中心
- Hystrix 熔断器

##### 注册中心 Eureka

> Eureka专门用于给其他服务注册的称为Eureka Server(服务注册中心)，其余注册到Eureka Server的服务称为Eureka Client。

Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

Eureka Client是一个[java](https://baike.baidu.com/item/java/85979?fromModule=lemma_inlink)客户端，用于简化与Eureka Server的交互，客户端同时也就是一个内置的、使用轮询(round-robin)负载算法的[负载均衡器](https://baike.baidu.com/item/负载均衡器/8852239?fromModule=lemma_inlink)

在应用启动后，将会向Eureka Server发送心跳,默认周期为30秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认90秒)。

Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端缓存机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。综上，Eureka通过心跳检查、[客户端缓存](https://baike.baidu.com/item/客户端缓存/10237000?fromModule=lemma_inlink)等机制，确保了系统的高可用性、灵活性和可伸缩性。

##### 什么是 RestTemplate?

**`RestTemplate`是`Spring`提供的一个访问Http服务的客户端类**，怎么说呢？就是微服务之间的调用是使用的 `RestTemplate` 。比如这个时候我们 消费者B 需要调用 提供者A 所提供的服务我们就需要这么写。如我下面的伪代码。

```java
@Autowired
private RestTemplate restTemplate;
// 这里是提供者A的ip地址，但是如果使用了 Eureka 那么就应该是提供者A的名称
private static final String SERVICE_PROVIDER_A = "http://localhost:8081";

@PostMapping("/judge")
public boolean judge(@RequestBody Request request) {
    String url = SERVICE_PROVIDER_A + "/service1";
    return restTemplate.postForObject(url, request, Boolean.class);
}
```

##### 负载均衡之 Ribbon

**为什么需要 Ribbon？**

`Ribbon` 是 `Netflix` 公司的一个开源的负载均衡 项目，是一个客户端/进程内负载均衡器，**运行在消费者端**。

**nginx 和 Ribbon 的对比**

提到 **负载均衡** 就不得不提到大名鼎鼎的 `Nignx` 了，而和 `Ribbon` 不同的是，它是一种**集中式**的负载均衡器。

何为集中式呢？简单理解就是 **将所有请求都集中起来，然后再进行负载均衡**。

`Nginx` 是接收了所有的请求进行负载均衡的，而对于 `Ribbon` 来说它是在消费者端进行的负载均衡。

在 `Nginx` 中请求是先进入负载均衡器，而在 `Ribbon` 中是先在客户端进行负载均衡才进行请求的。

> 1，nginx 是服务器端的负载均衡器，所有请求发送到nginx之后，nginx通过反向代理的功能分发到不同的服务器，做负载均衡
>
> 2，ribbon是客户端的负载均衡器，他是通过将eureka注册中心上的服务，读取下来，缓存在本地，本地通过轮询算法，实现客户端的负载均衡

##### 什么是 Open Feign

了 `Eureka`，`RestTemplate`，`Ribbon` 我们就可以😃愉快地进行服务间的调用了，但是使用 `RestTemplate` 还是不方便，我们每次都要进行这样的调用。

```java
@Autowired
private RestTemplate restTemplate;
// 这里是提供者A的ip地址，但是如果使用了 Eureka 那么就应该是提供者A的名称
private static final String SERVICE_PROVIDER_A = "http://localhost:8081";

@PostMapping("/judge")
public boolean judge(@RequestBody Request request) {
    String url = SERVICE_PROVIDER_A + "/service1";
    // 是不是太麻烦了？？？每次都要 url、请求、返回类型的 
    return restTemplate.postForObject(url, request, Boolean.class);
}
```

样每次都调用 `RestRemplate` 的 `API` 是否太麻烦，我能不能像**调用原来代码一样进行各个服务间的调用呢？**

💡💡💡聪明的小朋友肯定想到了，那就用 **映射** 呀，就像域名和IP地址的映射。我们可以将被调用的服务代码映射到消费者端，这样我们就可以 **“无缝开发”**啦。

OpenFeign 也是运行在消费者端的，使用 Ribbon 进行负载均衡，所以 OpenFeign 直接内置了 Ribbon。

在导入了 `Open Feign` 之后我们就可以进行愉快编写 `Consumer` 端代码了。

```java
// 使用 @FeignClient 注解来指定提供者的名字
@FeignClient(value = "eureka-client-provider")
public interface TestClient {
    // 这里一定要注意需要使用的是提供者那端的请求相对路径，这里就相当于映射了
    @RequestMapping(value = "/provider/xxx",
    method = RequestMethod.POST)
    CommonResponse<List<Plan>> getPlans(@RequestBody planGetRequest request);
}
```

