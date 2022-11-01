# SpringCloud
![[Pasted image 20220926082938.png]]

## 组件学习目标
服务注册中心 ：Nacos
服务调用 :LoadBalancer，Openfeign
服务降级 : sentinel
服务网关 :gateway
服务配置: Nacos
服务总线 : Nacos

## Gateway

>官网：https://spring.io/projects/spring-cloud-gateway


### 简介
SpringCloud Gateway是SpringCloud的一个全新项目，基于Spring5.O+Springboot 2.0和ProjectReactor等技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的API路由管理方式。

SpringCloudGateway作为SpringCloud生态系统中的网关，目标是替代Zuul,在SpringCloud2.0以上版本中，没有对新版本的zuul2.0以上最新高性能版本进行集成，仍然还是使用的Zuul 1.x非Reactor模式的老版本。而为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而webFlux框架底层则使用了高性能的Reactor模式通信框架Netty。

springCloudGateway的目标提供统一的路由方式且基于Filter链的方式提供了网关基本的功能，例如：安全，监控/指标，和限流。

一方面因为Zuul 1.0已经进入了维护阶段，而且Gateway是SpringCloud团队研发的是亲儿子产品，值得信赖。而且很多功能比zull用起来都简单便捷。

Gateway是基于异步非阻塞模型上进行开发的，性能方面不需要担心。虽然Netflix早就发布了最新的Zuul2.x，但Spring Cloud貌似没有整合计划。而且Netflix相关组件都宣布进入维护期；不知前景如何？

多方面综合考虑Gateway是很理想的网关选择。

### 是什么
Gateway特性：

- 基于SpringFramework5，ProjectReactor和SpringBoot 2.0进行构建；
- 动态路由：能够匹任何请求属性；
- 可以对路由指定Predicate（断言）和Filter（过滤器）
- 集成Hystrix的断路器功能；
- 集成SpringCloud服务发现功能；
- 易于编写的Predicate（断言）和Filter（过滤器）
- 请求限流功能；
- 支持路径重写。

![[Pasted image 20221003224217.png]]


GateWay的三大核心概念：

- Route(路由）：路由是构建网关的基本模块，它由ID、目标URI、一系列的断言和过滤器组成，如果断言为true则匹配该路由
- Predicate(断言）：参考的是Java8的java.util.function.predicate。开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由匹配条件
- Filter(过滤）：指的是spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。
例子：通过断言虽然进来了，但老师要罚站10min（过滤器操作），然后才能正常坐下听课

### 示例工程

导入依赖
```xml
<dependencies>  
    <!--gateway-->  
    <dependency>  
        <groupId>org.springframework.cloud</groupId>  
        <artifactId>spring-cloud-starter-gateway</artifactId>  
    </dependency>  
    <!--eureka-client gateWay网关作为一种微服务，也要注册进服务中心。哪个注册中心都可以，如zk-->  
    <!--这里使用nacos-->  
    <dependency>  
        <groupId>com.alibaba.cloud</groupId>  
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>  
    </dependency>  
    <!-- gateway和spring web+actuator不能同时存在，即web相关jar包不能导入 -->  
    <dependency>  
        <groupId>org.projectlombok</groupId>  
        <artifactId>lombok</artifactId>  
        <optional>true</optional>  
    </dependency>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-test</artifactId>  
        <scope>test</scope>  
    </dependency>  
    <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->  
        <groupId>com.atguigu.springcloud</groupId>  
        <artifactId>cloud-api-commons</artifactId>  
        <version>1.0-SNAPSHOT</version>  
    </dependency>  
</dependencies>

```

配置文件

```yml
server:  
  port: 9527  
spring:  
  application:  
    name: cloud-gateway  
  cloud:  
    nacos:  
      discovery:  
        server-addr: localhost:8848  
    ## GateWay配置  
    gateway:  
      routes: #多个路由  
        - id: payment_routh  # 路由ID ， 没有固定的规则但要求唯一，建议配合服务名  
          uri: http://localhost:8001  # 匹配后提供服务的路由地址 #uri+predicates  # 要访问这个路径得先经过9527处理  
          predicates:  
            - Path=/payment/get/**  # 断言，路径相匹配的进行路由  
  
        - id: payment_routh2  # 路由ID ， 没有固定的规则但要求唯一，建议配合服务名  
          uri: http://localhost:8001  # 匹配后提供服务的路由地址  
          predicates:  
            - Path=/payment/lb/**  # 断言，路径相匹配的进行路由  
management:  
  endpoints:  
    web:  
      exposure:  
        include: '*'
```

>上分是通过配置文件配置了多个路由，url匹配服务地址，predicates匹配相应的服务，将匹配断言的对应的请求转发


主程序加入nacos注册注解
```java
@SpringBootApplication  
@EnableDiscoveryClient  
public class GatewayMain9527 {  
    public static void main(String[] args) {  
        SpringApplication.run(GatewayMain9527.class,args);  
    }  
}
```

我们在启动8001的工程，启动nacos，再启动9527.我们访问9527的http://localhost:9527/payment/get/1其实就是访问http://localhost:8001/payment/get/1

### 使用config类配置
```java
@Configuration
public class GateWayConfig{
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();

        // 分别是id，本地址，转发到的地址
        routes.route("path_route_atguigu",
                r -> r.path("/guonei").uri("http://news.baidu.com/guonei")
                    ).build();//JDK8新特性

        return routes.build();
    }
}

```

就是说我们在访问 http://localhost:9527/guonei 时，就会转发给http://news.baidu.com/guonei

### 动态配置路由
上述的两种配置我们配置的路由地址都是死的，如果我们有同一服务有多个服务器
我们需要实现 根据注册服务名调用实现负载均衡

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud: 
    nacos:  
      discovery:  
        server-addr: localhost:8848 
    gateway:
      discovery:
        locator:
          enabled: true # 开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh  # 路由ID ， 没有固定的规则但要求唯一，建议配合服务名
         # uri: http://localhost:8001  # 匹配后提供服务的路由地址 
          uri: lb://CLOUD-PROVIDER-SERVICE # lb 属于GateWay 的关键字，代表是动态uri，即代表使用的是服务注册中心的微服务名，它默认开启使用负载均衡机制 
          predicates:
            - Path=/payment/get/**  # 断言，路径相匹配的进行路由

        - id: payment_routh2  # 路由ID ， 没有固定的规则但要求唯一，建议配合服务名
        #  uri: http://localhost:8001  # 匹配后提供服务的路由地址
          uri: lb://CLOUD-PROVIDER-SERVICE
          predicates:
            - Path=/payment/lb/**  # 断言，路径相匹配的进行路由

# uri: lb://CLOUD-PROVIDER-SERVICE  解释：lb 属于GateWay 的关键字，代表是动态uri，即代表使用的是服务注册中心的微服务名，它默认开启使用负载均衡机制 
```

我们首先需要开启路由的动态创建功能
![[Pasted image 20221004090725.png]]
然后uri使用 lb添加关键字，代表使用动态uri

断言一样，就是将写死的地址改为注册中心的注册名

### Gateway:Predicate

>注意到上面yml配置中，有个predicates 属性值。

- After Route Predicate
- Before Route Predicate
- Between Route Predicate
- Cookie Route Predicate
- Header Route Predicate
- Host Route Predicate
- Method Route Predicate
- Path Route Predicate
- Query Route Predicate

predicates下面可以有多个属性，表示多个属性与操作为true这个路由才生效

详情看官网文档，这里简单写一下一些属性的使用

predicates属性下的After属性

```yml
predicates:
	- Path=/payment/lb/** 
	- After=2020-02-21T15:51:37.485+08:00[Asia/Shanghai] # 会在这个时间之后这个路由才生效

```

predicates属性下的Cookie属性

```yml
predicates:
	- Cookie=username,zzyy,ch.p # 需要有这个Cookie值才生效  最后一个是正则表达式
	# curl 地址 --cookie "a=b"

```

predicates属性下的Header属性

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: header_route
        uri: https://example.org
        predicates:
        - Header=X-Request-Id, \d+ #请求头需要包含这个属性，并且为整数
```


### Filter

路由过滤器可用于修改进入的HTTP请求和返回的Http响应，路由过滤器只能指定路由进行使用

生命周期
- pre：业务之前
- post：业务之后

种类
- GatewayFilter: 单一的
- GlobalFilter: 全局的

有很多过滤器，详情看官网

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: add_request_header_route
        uri: https://example.org
        filters:
        - AddRequestHeader=X-Request-red, blue # 添加了个请求头X-Request-red

```

主要是配置全局自定义过滤器：

场景：
-   全局日志记录
-   统一网关鉴权
-   。。。

主要接口 GlobalFilter,Ordered

```java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter,Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange,GatewayFilterChain chain) {

        log.info("********** come in MyLogGateWayFilter:  "+new Date());

        String uname = exchange.getRequest().getQueryParams().getFirst("uname");

        //合法性检验
        if(uname == null) {
            log.info("*******用户名为null，非法用户，o(╥﹏╥)o，请求不被接受");
            //设置 response 状态码   因为在请求之前过滤的，so就算是返回NOT_FOUND 也不会返回错误页面
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            //完成请求调用
            return exchange.getResponse().setComplete();
        }

        return chain.filter(exchange);//过滤链放行
    }

    // 返回值是加载顺序，一般全局的都是第一位加载
    @Override
    public int getOrder() {
        return 0;
    }
}

```



## openFeign

### Feign能干什么
Feign旨在使编写JavaHttp客户端变得更容易。

前面在使用Ribbon+RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接囗会被多处调用，所以通常都会针对每个微服务自行封装些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它（以前是Dao接口上面标注Mapper注解，现在是一个微服务接口上面标注一个Feign注解即可，即可完成对服务提供方的接口绑定，简化了使用Springcloud Ribbon时，自动封装服务调用客户端的开发量。

feign服务调用自动负载均衡，采用轮询

### openFeign服务调用

首先需要注册中心配置，我们进行服务调用肯定需要注册中心，这里使用nacos，不再说

直接写出额外的openfeign的配置

依赖
```xml
<!-- Open Feign，他里面也有ribbon，所以有负载均衡 -->
 <dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

由于Spring Cloud [Feign](https://so.csdn.net/so/search?q=Feign&spm=1001.2101.3001.7020)在Hoxton.M2 RELEASED版本之后不再使用Ribbon而是使用spring-cloud-loadbalancer，所以不引入spring-cloud-loadbalancer会报错.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

主启动类添加 **@EnableFeignClients** //关键注解

新建一个service

**@PathVariable的名称一定要写，不写会有问题**
```java
@Component //别忘了添加这个
@FeignClient(value = "CLOUD-PROVIDER-SERVICE")  //服务名称，要和nacos上面的一致才行
public interface PaymentFeignService
{
    //这个就是provider 的controller层的方法定义。
    @GetMapping(value = "/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);

    @GetMapping(value = "/payment/feign/timeout")
    public String paymentFeignTimeout();
}
/*
在提供端有：
	@GetMapping(value = "/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id)
    {
        Payment payment = paymentService.getPaymentById(id);

        if(payment != null)
        {
            return new CommonResult(200,"查询成功,serverPort:  "+serverPort,payment);
        }else{
            return new CommonResult(444,"没有对应记录,查询ID: "+id,null);
        }
    }
*/

```

我们的controller
```java
//使用起来就相当于是普通的service。
@RestController
public class CustomerFeignController {

    @Resource
    private PaymentFeignService paymentFeignService;//动态代理

    @GetMapping("/customer/feign/payment/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id){
        return paymentFeignService.getPaymentById(id);
    }
}

```


### 超时控制

>Openfeign默认超时等待为一秒，在消费者里面配置超时时间

```yml
#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000

```

### 日志增强

Feign共了日志打印功能，我们可以诵过配置来调整日志级别，从而了解Feign中Tttp请求的细节。

说白了就是对Feign接口的调用情况进行监控和输出。


日志级别：

-   NONE.默认的，不显示任何日志；
-   BASIC，仅记录请求方法、URL、响应状态码及执行时间；
-   HEADERS：除了BASIC中定义的信息之外，还有请求和响应的头信息
-   FULL:除了HEADERS中定义的信息之外，还有请求和响应的正文及元数据。

配置类
```java
package com.atguigu.springcloud.config;

import feign.Logger;

@Configuration
public class FeignConfig{
    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}

```


开启日志打印
```yml
logging: 
	level: 
	# feign日志以什么级别监控哪个接口 
		com.atguigu.springcloud.service.PaymentFeignService: debug
```

这两个是否需要都配置暂且不清楚，感觉一个是全局，一个是单独

---
# spring cloud alibaba
各个版本对照:https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E
![[Pasted image 20220926214811.png]]

## alibaba 依赖引入
将其添加到父工程中，子组件的依赖不需要引入版本
```xml
<dependency>  
  <groupId>com.alibaba.cloud</groupId>  
  <artifactId>spring-cloud-alibaba-dependencies</artifactId>  
  <version>2021.0.4.0</version>  
  <type>pom</type>  
  <scope>import</scope>  
</dependency>
```

## Nacos
### 简介
Nacos 前四个字母分别为Naming 和 Confinguration的前两个字母，最后的s为Service

Nacos是一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台

Nacos = Eureka + Config + Bus

能替代Eureka做服务注册中心，代替Config做服务配置中心

支持AP和CP模式的切换

官网地址 ：https://nacos.io/zh-cn/index.html

### 安装及运行

先从官网下载nacos，解压到没有中文的目录下。
新版的默认启动方式是集群启动，所以不能直接点击bin目录下的startup.cmd
需要在bin目录使用cmd命令
``startup.cmd -m standalone``

命令运行成功后直接访问    http://localhost:8848/nacos

默认的账户密码都是nacos

进入后就可以看到nacos的界面

### 服务注册中心

#### spring boot启动配置管理

先在项目模块中引入服务注册依赖
```xml
<dependency>  
    <groupId>com.alibaba.cloud</groupId>  
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>  
</dependency>
```
然后在配置文件中添加配置
```yml
server:
  port: 9001
spring:
  application:
    name: nacos-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

并且在启动类添加  *@EnableDiscoveryClient* 注解

>[!note] @EnableDiscoveryClient
>而在新版本的 Spring Cloud 中，这个注解不再是一个必须的步骤，我们只需要通过配置项就可以开启 Nacos 的功能。
>而配置项是默认开启的
>也就是说，高版本中，引入nacos的依赖就是默认注册的

### RestTemplate请求转发

添加配置项
```java
@Configuration  
public class ApplicationContextConfig {  
    /*在spring 容器中放入RestTemplate*/  
    @Bean  
    public RestTemplate getRestTemplate(){  
        return new  RestTemplate();  
    }  
}
```

使用：
```java
@Resource  
private RestTemplate restTemplate;  
  
@GetMapping("/consumer/payment/create")  
public CommonResult<Payment> create(Payment payment){  
    return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment, CommonResult.class);  
}
```

postForObject就是发送post请求，以此类推

发送到PAYMENT_URL是在注册中心中的服务名

### 负载均衡

由于 Netflix Ribbon 进入停更维护阶段，因此 SpringCloud 2020.0.1 版本之后 删除了eureka中的ribbon，替代ribbon的是spring cloud自带的LoadBalancer，默认使用的是**轮询**的方式 新版本的 Nacos discovery 都已经移除了 Ribbon ，此时我们需要引入 loadbalancer 代替，才能调用服务提供者提供的服务

在2020版本后使用负载均衡需要使用 Spring Cloud Load Balancer
1.pom添加依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

### 服务配置中心

#### 基础配置

创建配置中心模块

引入依赖
```xml
<!-- 以 nacos 做服务配置中心的依赖 -->  
<dependency>  
    <groupId>com.alibaba.cloud</groupId>  
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>  
</dependency>  
  
<!-- springcloud alibaba nacos 依赖 -->  
<dependency>  
    <groupId>com.alibaba.cloud</groupId>  
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>  
</dependency>
```

主类也要加 *@EnableDiscoveryClient*

Nacos同springcloud-config一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉去配置后，才能保证项目的正常启动

springboot的配置文件的加载是存在优先级顺序的，bootstrap优先级高于application

所以我们配置两个yml文件，重点的全局的放在bootstrap，自己的放在application

在业务类上添加 *@RefreshScope  //支持Nacos的动态刷新*  ,实现配置的自动刷新

在配置文件bootstrap.yml中添加注册中心和配置中心的地址
```yml
spring:  
  application:  
    name: nacos-config-client
cloud:  
  nacos:  
    discovery:  
      server-addr: localhost:8848 #Nacos服务注册中心地址  
    config:  
      server-addr: localhost:8848 #Nacos作为配置中心地址  
      file-extension: yaml #指定yaml格式的配置
```

在application.yml中要配置
```yml
spring:  
  profiles:  
    active: dev # 表示开发环境  
    #active: test # 表示测试环境  
    #active: info
```


***一定要配置！！！***

为什么呢？

在 Nacos Spring Cloud 中，`dataId` 的完整格式如下：

``${prefix}-${spring.profiles.active}.${file-extension}``

-   `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix`来配置。
-   `spring.profiles.active` 即为当前环境对应的 profile，详情可以参考 [Spring Boot文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html#boot-features-profiles)。 **注意：当 `spring.profiles.active` 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}.${file-extension}`,但这里不建议为空，建议严格遵守公式进行配置**
-   `file-exetension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension` 来配置。目前只支持 `properties` 和 `yaml` 类型。

根据公式
我们上方的实例的dataId就为
*nacos-config-client-dev.yaml*

***注意***
spring boot2.4之后适配的spring cloud默认不会读取bootstrap，需要引入依赖进行开启
```xml
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-bootstrap</artifactId>  
</dependency>
```
==当然，上方的两个配置文件我们写在一起也可以，只是了解一下bootstrap QAQ==
在使用配置的类上添加`@RefreshScope` 

通过 Spring Cloud 原生注解 `@RefreshScope` 实现配置自动更新：

也就是说nacos的配置一更新，这边就会自动刷新

#### 分类配置

- 问题1：实际开发者，通常一个系统会准备dev/test/prod环境。如何保证环境启动时服务能正确读取nacos上相应环境的配置文件
	- 用namespace区分环境
- 问题2：一个大型分布式微服务系统有很多微服务子项目，每个微服务项目又都会有相应的开发环境、测试环境、预发环境、正式环境。那怎么对微服务配置进行管理呢？
	- 用group把不同的微服务划分到同一个分组里面去
![[Pasted image 20220927164618.png]]
默认：Namespace=public,Group = DEFAULT_GROUP,默认Cluster=DEFAULT

Namespace主要用来实现隔离，比方说我们现在有三个环境，开发、测试、生产环境，我们就可以创建三个Namespace,不同的Namespace之间是隔离的

Group可以把不同的微服务划分到同一个分组里面去

Service就是微服务，一个service可以包含多个cluster集群，nacos默认cluster是DEFAULT，Cluster是对指定微服务的一个虚拟划分。

比方说为了容灾，将service微服务分别部署在了杭州机房和广州机房，这是就可以给杭州机房的service微服务起一个集群名称HZ

给广州的service微服务起一个集群名称GZ，还可以尽量让同一个机房的微服务互相调用，以提升虚拟。

最后instance就是微服务的实例

分类配置可以有不同的方案
##### DataID方案
指定spring.profile.active和配置文件的dataID来使不同环境下读取不同的配置
配置空间+配置分组+新建dev和test两个dataid：就是创建后不同的两个文件名nacos-config-client-dev.yaml、nacos-config-client-test.yaml
通过IDEA里的spring.profile.active属性就能进行多环境下配置文件的读取

也就是根据不同环境如（dev或者test等），进行不同的dataID设置来进行不同文件的读取配置
##### Group方案
使用Group方案就需要在nacos新建配置中不使用默认Group,使用自定义的组，如（DEV_GROUP）
-   在nacos创建配置文件时，给文件指定分组。
-   在IDEA中该group内容
```YML
spring:  
  application:  
    name: nacos-config-client  
  cloud:  
    nacos:  
      discovery:  
        server-addr: localhost:8848 #Nacos服务注册中心地址  
      config:  
        server-addr: localhost:8848 #Nacos作为配置中心地址  
        file-extension: yaml #指定yaml格式的配置  
        group: TEST_GROUP  #指定group
```
-   实现的功能：当修改开发环境时，只会从同一group中进行切换。
##### Namespace方案
- 这个是不允许删除的，可以创建一个新的命名空间，会自动给创建的命名空间一个流水号。
- 在nacos新建命名空间，自动出现7d8f0f5a-6a53-4785-9686-dd460158e5d4
- 在IDEA的yml中指定命名空间namespace: 7d8f0f5a-6a53-4785-9686-dd460158e5d4

最后，dataid、group、namespace 三者关系如下：（不同的dataid，是相互独立的，不同的group是相互隔离的，不同的namespace也是相互独立的）

#### nacos集群/持久化（很重要）

集群部署官网文档：https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html

单机模式支持mysql：在0.7版本之前，在单机模式时nacos使用**嵌入式数据库**（derby，他的pom里有这个依赖）实现数据的存储，不方便观察数据存储的基本情况（多个实例各自独立，数据不统一）。0.7版本增加了支持mysql数据源能力

我们写在nacos的配置之所以下次启动还有，就是因为内置数据库，但我们集群配置需要保证数据统一性，需要将其配置为使用同一个数据库，***也就是使用外置数据源***

##### 1.数据库建立
数据库需要根据nacos进行创建

``create database nacos_config``

然后使用nacos/conf/nacos-mysql.sql中的sql语句进行建表

##### 2.更改配置文件
（这里是单机启动的nacos，linux的好像是默认解锁的）
打开nacos/conf/application.properties文件
将31行左右的配置解锁，进行修改，配置数据库账号和密码还有数据库名

```properties
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
 spring.datasource.platform=mysql

### Count of DB:
 db.num=1

### Connect URL of DB:
 db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
 db.user.0=root
 db.password.0=wang200301
```

配置好后重启，就会切换为本地数据库

##### linux集群配置
这里感觉可以先学习doker和nginx，先搁置
这里有一篇很好的docker 博客文章，可以进行参考
https://blog.csdn.net/m0_53151031/article/details/123118920

这篇写了不使用docker的集群化配置
https://blog.csdn.net/qq_51277752/article/details/125744997

## Sentinel

### 是什么

>sentinel在 springcloud Alibaba 中的作用是实现`熔断`和`限流`。类似于Hystrix豪猪

官网：https://github.com/alibaba/Sentinel
中文文档：https://sentinelguard.io/zh-cn/docs/logs.html

==spring boot cloud  Sentinel 使用文档:https://github.com/alibaba/spring-cloud-alibaba/wiki/Sentinel==

### 安装

dashboard下载地址：https://github.com/alibaba/Sentinel/releases

### 运行

Sentinel 分为两个部分:

核心库（Java 客户端）不依赖任何框架/库，能够运行于所有 Java 运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
控制台（Dashboard）基于 Spring Boot 开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。

运行dashboard前提
>1.java8环境OK
>2.8080端口不能被占用

命令就是运行dashboard的jar包
java -jar sentinel-dashboard-1.8.5.jar

然后访问8080端口就可以看到sentinel的dashboard界面
账号密码都是sentinel

### 演示工程

先启动nacos

新建模块 `cloudalibaba-sentinel-service8401` ，使用nacos作为服务注册中心
添加依赖
```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

yml配置添加sentinel
```java
server:
  port: 8401
spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        # 服务注册中心 # sentinel注册进nacos
        server-addr: localhost:8848
    sentinel:
      transport:
        # 配置 Sentinel Dashboard 的地址
        dashboard: localhost:8080
        # 默认8719 ，如果端口被占用，端口号会自动 +1，直到找到未被占用的端口，提供给 sentinel 的监控端口
        port: 8719
        
management:
  endpoints:
    web:
      exposure:
        include: '*'
```
Sentinel可以对service进行监控、熔断、降级

1.8版本以下，没访问时再sentinel里是看不到监控的应用的，因为是懒加载，需要访问一次

1.8版本以上直接就可以监控到

### 流控规则

#### 基本介绍
![[Pasted image 20221002205944.png]]

---
进一步的解释说明
- 资源名：唯一名称，默认请求路径
- 针对来源：sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
- 阈值类型/单机值：
	- QPS（每秒钟的请求数量）：当调用该api就QPS达到阈值的时候，进行限流
	- 线程数．当调用该api的线程数达到阈值的时候，进行限流
- 是否集群：不需要集群
- 流控模式：
	- 直接：api达到限流条件时，直接限流。分为QPS和线程数
	- 关联：当关联的资到阈值时，就限流自己。别人惹事，自己买单
	- 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）【api级别的针对来源】
- 流控效果：
	- 快速失败：直接抛异常
	- warm up：根据codeFactor（冷加载因子，默认3）的值，从阈值codeFactor，经过预热时长，才达到设置的QPS阈值
	- 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS,否则无效

![[Pasted image 20221002210401.png]]

#### 流控模式-直接：
我们先只针对/testA请求进行限制

**QPS类型** -- 单个进程每秒请求服务器的成功次数
![[Pasted image 20221003142913.png]]

>限流表现：当超过阀值，就会被降级。
>1s内多次刷新网页，localhost:8401/testA
>返回Blocked by Sentienl(flow limiting)

也就是只允许1s有1次请求，超过就会直接失败，并且返回Blocked by Sentienl(flow limiting)

这里超过阈值就直接返回Blocked by Sentienl(flow limiting)，我们能返回自己的错误信息吗？
也就是说我们可以自定义失败返回吗？

**线程数**

就是同时能处理的请求线程阈值
比如处理一个请求需要1s，设置阈值为1，那么这个请求进来正在处理，那么别的请求在这个请求正在处理时进来就会直接失败

#### 流控模式-关联

-   当与A关联的资源B达到阀值后，就限流A自己
-   B惹事，A挂了。支付达到阈值，限流下单接口。B阈值达到1，A就挂
-   用post访问B让B忙，访问A发现挂了

就是我达到阈值了，并不限流我，而是限流你
应用场景：当支付接口达到阈值时，限流下订单接口，防止连坐
![[Pasted image 20221003145645.png]]
这里b的阈值达到，b不会挂，但a会挂

#### 流控效果–预热Warm up

默认的直接失败是直接返回错误信息

预热:访问数量慢慢升高

公式:阈值除以coldFactor（默认3），经过预热时长后才会达到阈值。

默认coldFactor（冷加载因子）为3，即请求QPS从阈值/3开始，经预热时长逐渐升至阈值

![[Pasted image 20221003150801.png]]
如上图配置 

10 /3  = 3，所以你的单机阈值就是3，当QPS达到3时，需要等待5s，从3慢慢过渡到10

**超过阈值就会失败，预热时阈值会慢慢增加**

#### 流控效果–排队等待
匀速排队（Ru1eConstant.CONTROL_BEHAVIOR_RATE_LIMITER）方式会严格控制请求通过的间隔时间，即让请求以均匀的速度通过对应的是漏桶算法。详细文档可以参考流量控制-匀速器模式，具体的例子可以参见PaceFlowDemo
![[Pasted image 20221003151608.png]]
**匀速排队，让请求以均匀的速度通过，阈值类型必须设置为QPS,否则无效**
![[Pasted image 20221003151456.png]]
/testA每秒1次请求，超过的话就排队，等待的超时时间为20000毫秒，超时返回失败

这种方式主要用于处理间隔性突发的流量，伊消息列。想象一下这样的场景，在某一秒有大量的请求到来，而接下来的月秒则处于空闲状态，我们希系统能够在接下来的空闲期间逐渐处理这些请求，而不是第一秒就拒绝多余的请求

### 熔断降级

#### 降级策略
sentinel熔断降级会在调用链路中某个资源出现不稳定状态时（例如调用超时或异常比例升高），对这个资源的调用进行限制，让请求快速失败，避免影响到其它的资源而导致级联错误。

当资源被降级后，在接下来的降级时间窗囗之内，对该资源的调用都自动熔断（默认行为是抛出DegradeException)。

>慢调用比例
>
选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。

>异常比例（秒级）
>
>当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。

>异常数(分钟级)
>
>当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。


### 热点key限流

![[Pasted image 20221003162920.png]]
何为热点？热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的TopK数据，并对其访问进行限制。比如：

- 商品ID为参数，统计一段时间内最常购买的商品ID并进行限制
- 用户ID为参数，针对一段时间内频繁访问的用户ID进行限制
参数限流会统计传入参数中的参数，并根据配置流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效。

>@SentinelResource ：处理的是Sentine1控制台配置的违规情况，有blockHandler方法配置的兜底处理
>也就是说熔断了，你还访问，或者限流访问失败的话 我们才会执行兜底方法

>@RuntimeException：int age=10/0，这个是java运行时报出的运行时异异常RunTimeException，@Sentine1Resource不管,java会返回错误界面，并不会启动兜底方法返回

实例代码
```java
@GetMapping("/testhotkey")  
@SentinelResource(value = "testhotkey", blockHandler = "deal_testhotkey")  
//这个value是随意的值，并不和请求路径必须一致  
//在填写热点限流的 资源名 这一项时，可以填 /testhotkey 或者是 @SentinelResource的value的值  
public String testHotKey(  
        @RequestParam(value="p1", required = false) String p1,  
        @RequestParam(value = "p2", required = false) String p2  
){  
    return "testHotKey__success";  
}  
  
//类似Hystrix 的兜底方法  
public String deal_testhotkey(String p1, String p2, BlockException e){  
    return "testhotkey__fail";  
}
```

但使用@SentinelResource，我们配置规则时使用的资源名就需要使用testhotkey，也就是@SentinelResource的value值

并且热点限流我们必须配置@SentinelResource，因为热点限流并没有自定义的兜底方法
也就是说，出现异常会把异常返回给前台，这很不好，所以我们一定要自己配置热点限流的兜底方法

参数索引的参数是根据我们后台程序的顺序的排列的，并不是前台，上方后台使用p1，p2,我们设置监视0的参数索引就是监视p1

**参数例外项**

![[Pasted image 20221003165912.png]]
这是基本的参数，我们只能控制几秒内这个参数的阈值，就是1秒内有几个参数

但我们想设置这个参数值的阈值，比如p1=5不被限流，其他被限流

![[Pasted image 20221003170113.png]]
这里配置就是当p1为5的时候，他的阈值就不是1了而是200，就是一个例外

### 系统规则
Sentinel 系统自适应保护从整体维度对应用入口流量进行控制，结合应用的 Load、总体平均 RT、入口 QPS 和线程数等几个维度的监控指标，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

![[Pasted image 20221003171054.png]]
我们之前的配置，都是对每个请求进行的配置

我们这里是对整个系统进行设置![[Pasted image 20221003171249.png]]
>一般配置在网关或者入口应用中，但是这个东西有点危险，一但值不合适，就相当于系统瘫痪。


### @SentinelResource

@SentinelResource 注解，主要是指定资源名（也可以用请求路径作为资源名），和指定降级处理方法的。

```java
public class RateLimitController {  
  
    @GetMapping("/byResource")                //处理降级的方法名  
    @SentinelResource(value = "byResource", blockHandler = "handleException")  
    public CommonResult byResource(){  
        return new CommonResult(200, "按照资源名限流测试0K", new Payment(2020L,"serial001"));  
    }  
  
    //CommonResult是我们自定义的返回类  
    //降级方法  
    public CommonResult handleException(BlockException e){  
        return new CommonResult(444, e.getClass().getCanonicalName() + "\t 服务不可用");  
    }  
}
```

>问题1
>我们一旦使用@SentinelResource，建议不要再通过Url的方式配置资源名了，只有使用value的值配置资源名才会使用自定义的兜底方法，否则还会使用基本的兜底方法
>并且再热点规则中使用url的资源名还会导致规则失效
>@SentinelResource如果没有配置兜底方法，依旧使用基本的兜底方法


---
很明显，上方的代码处理方式和业务代码耦合在一起，不直观，而且每个业务配置一个兜底方法，代码膨胀

解决方法：**自定义全局BlockHandler处理类**

```java
public class CustomerBlockHandler {  
  
    public static CommonResult handlerException(BlockException exception) {  
        return new CommonResult(4444,"按客戶自定义,global handlerException----1");  
    }  
  
    public static CommonResult handlerException2(BlockException exception) {  
        return new CommonResult(4444,"按客戶自定义,global handlerException----2");  
    }  
}
```

我们将多个兜底方法卸载一个全局类中，降低耦合
使用
```java
@GetMapping("/rateLimit/customerBlockHandler")  
@SentinelResource(value = "customerBlockHandler",  
        blockHandlerClass = CustomerBlockHandler.class,  
        blockHandler = "handlerException2")  
public CommonResult customerBlockHandler() {  
    return new CommonResult(200,"按客戶自定义",new Payment(2020L,"serial003"));  
}
```


### 服务熔断功能

sentinel 整合openFeign+fallback

熔断异常可以由blockHandler配置的兜底方法捕获，返回自定义兜底方法界面
但java程序异常不会，会返回异常界面，但我们不能这样返回

我们需要配置fallback

>业务异常会被 fallback 处理，返回我们自定义的提示信息，而如果给它加上流控，并触发阈值，只能返回sentinel默认的提示信息。

```java
	@GetMapping("/consutomer/payment/get/{id}")
    @SentinelResource(value = "fallback", fallback = "handleFallback") //fallback只处理业务异常
    public CommonResult getPayment(@PathVariable("id")Long id){
        if(id >= 4){
            throw new IllegalArgumentException("非法参数异常...");
        }else {
            return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
        }
    }

    //兜底方法
    public CommonResult handleFallback(@PathVariable("id")Long id, Throwable e){
        return new CommonResult(414, "---非法参数异常--", e);
    }

```

fallbacke 和 blockHandler可以一起配置
```java
  //@SentinelResource(value = "fallback", fallback = "handleFallback") //fallback只处理业务异常
    @GetMapping("/consutomer/payment/get/{id}")
    @SentinelResource(value = "fallback", blockHandler = "handleblockHandler", fallback = "handleFallback")
    public CommonResult getPayment(@PathVariable("id")Long id){
        if(id >= 4){
            throw new IllegalArgumentException("非法参数异常...");
        }else {
            return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
        }
    }
    //====fallback
    public CommonResult handleFallback(@PathVariable("id")Long id, Throwable e){
        return new CommonResult(414, "---非法参数异常--form fallback的提示", e);
    }

    //====blockHandler                                       blockHandler的方法必须有这个参数
    public CommonResult handleblockHandler(@PathVariable("id")Long id, BlockException e){
        return new CommonResult(414, "---非法参数异常--", e);
    }

```

>业务异常由fallback处理
>blockHandler处理熔断异常
>但很显然，QPS的熔断高于业务异常，也就是blockHandler高于fallback


### 异常忽略
这是 @SentinelResource 注解的一个值：
```java
@SentinelResource(value = "byResource", blockHandler = "handleException",fallback = "handleFallback"，exceptionsToIgnore = {IllegalArgumentException.class})
```

这样，fallback的兜底方法就不会捕获到这个IllegalArgumentException.class的方法，直接返回异常界面

### 全局降级

>上面是单个进行 fallback 和 blockhandler 的测试，下面是整合 openfeign 实现把降级方法解耦。和Hystrix 几乎一摸一样！

---
首先，我们访问一个服务消费者的一个方法，对这个方法的处理使用在每个方法上加入 fallback 和 blockhandler进行熔断和限流控制，之后这个服务消费者要去访问服务提供者，我们需要进行请求转发，我们原来用RestTemplate，但RestTemplate如果目标服务器关闭，只能返回超时界面，不能对我们的服务消费者服务进行降级并执行兜底服务，所以这里用feign进行请求转发

且feign自带负载均衡，方式为轮询方式


在消费者上添加依赖
```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

yml配置

```java
# 激活Sentinel对Feign的支持
feign:
  sentinel:
    enabled: true

```

主启动类添加注解 ： **@EnableFeignClients 激活open-feign**

>**使用feign后，controller就不去使用RestTemplate,而是去调用service

service
```java
@FeignClient(value = "nacos-payment-provider", fallback = PaymentServiceImpl.class)  //目标的属性名
public interface PaymentService {

    @GetMapping("/payment/get/{id}")  //目标提供者的url
    public CommonResult paymentSql(@PathVariable("id")Long id);
}

```

这里其实就是直接写目标controller的方法，只是去除方法体

service 实现类：
```java
@Component
public class PaymentServiceImpl implements PaymentService {

    @Override
    public CommonResult paymentSql(Long id) {
        return new CommonResult(414, "open-feign 整合 sentinel 实现的全局服务降级策略",null);
    }
}

```

这里写的就是fallback的兜底方法


  **总结:** 这种全局熔断，是针对 “访问提供者” 这个过程的，只有访问提供者过程中发生异常才会触发降级，也就是这些降级，是给service接口上这些提供者的方法加的，以保证在远程调用时能顺利进行。

### 持久化

>Sentinel一旦我们重启业务，我们配置的各种规则就会消失

我们可以将限流配置规则持久化进Nacos保存，只要刷新服务的某个rest地址，sentinel控制台的限流规则就能看到，只要Nacos
里的配置不删除，针对服务上的sentinel上的流控规则持续有效

我们有必要将其的配置持久化

我们这里选择将其持久化到nacos中，因为nacos之前已经实现持久化，也就连带实现持久化

添加依赖
```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

添加配置文件

```yml
spring:  
  application:  
    name: cloudalibaba-sentinel-service  
  cloud:  
    nacos:  
      discovery:  
        # 服务注册中心 # sentinel注册进nacos  
        server-addr: localhost:8848  
    sentinel:  
      transport:  
        # 配置 Sentinel Dashboard 的地址  
        dashboard: localhost:8080  
        # 默认8719 ，如果端口被占用，端口号会自动 +1，直到找到未被占用的端口，提供给 sentinel 的监控端口  
        port: 8719  
      datasource:  
        ds1:  
          nacos:  
            server-addr: localhost:8848  
            data-id: ${spring.application.name}  
            group-id: DEFAULT_GROUP  
            data-type: json  
            rule-type: flow
```

ds1其实就是随便起的名字，详情看git文档
![[Pasted image 20221003205748.png]]
![[Pasted image 20221003205803.png]]

去nacos上创建一个dataid ,名字和yml配置的一致，json格式，内容如下：

```json
[
    {
        "resource": "/testA",
        "limitApp": "default",
        "grade": 1,
        "count": 1,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": false
    }
]

```

resource：资源名称
limitApp：来源应用
grade：阈值类型，0表示线程数，1表示QPS，
count：单机阈值，
strategy：流控模式，0表示直接，1表示关联，2表示链路

controlBehavior:流控效果，0表示快速失败，1表示Warm Up,2表示排队等待；

cIusterM0de是否集群。

然后启动服务，我们就会看到Sentinel中有这个配置

>但这太坑了！我们在Sentinel对配置进行修改后并不会同步回nacos，这就表示，我们在配置后只能读，不能改，这就是一个启动时读取一次的配置文件