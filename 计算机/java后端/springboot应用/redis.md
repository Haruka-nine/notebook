# Redis

## 依赖引入

```xml
<!--redis连接的依赖-->
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>  
<!--连接池配置，导入就启用连接池，不导入就不启用--> 
<dependency>  
    <groupId>org.apache.commons</groupId>  
    <artifactId>commons-pool2</artifactId>  
</dependency>
```
## 配置文件
```properties
#redis服务器地址
spring.redis.host=192.168.10.10  
#redis服务器端口
spring.redis.port=6379  
#密码
spring.redis.password=wang200301  
#redis数据库索引
spring.redis.database=0  
# 连接超时时间（毫秒
spring.redis.timeout=1800000  
# 连接池配置  #连接池最大连接数（使用负值表示没有限制
spring.redis.lettuce.pool.max-active=20  
#最大阻塞等待时间（使用负值表示没有限制）  
spring.redis.lettuce.pool.max-wait=-1  
#连接池中最大空闲连接
spring.redis.lettuce.pool.max-idle=5  
#连接池中最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

## 使用
```java
@RestController  
@RequestMapping("/redisTest")  
public class RedisTestController {  
  
    @Autowired  
    private StringRedisTemplate redisTemplate;  
  
    @GetMapping  
    public String testRedis(){  
        User user = new User("cv战士", 12);  
        redisTemplate.opsForValue().set("user222", String.valueOf(user));  
        return redisTemplate.opsForValue().get("user222");  
    }  
}
```


## Redis序列化配置

### String序列化

直接使用 StringRedisTemplate

### FastJson序列化

这里使用的是FastJson2
```java
  
@Configuration  
public class RedisConfig {  
  
    @Bean  
    @SuppressWarnings(value = { "unchecked", "rawtypes" })  
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory)  
    {  
        RedisTemplate<Object, Object> template = new RedisTemplate<>();  
        template.setConnectionFactory(connectionFactory);  
  
        GenericFastJsonRedisSerializer serializer = new GenericFastJsonRedisSerializer();  
        // 使用StringRedisSerializer来序列化和反序列化redis的key值  
        template.setKeySerializer(new StringRedisSerializer());  
        template.setValueSerializer(serializer);  
  
        // Hash的key也采用StringRedisSerializer的序列化方式  
        template.setHashKeySerializer(new StringRedisSerializer());  
        template.setHashValueSerializer(serializer);  
  
        template.afterPropertiesSet();  
        return template;  
    }  
}
```

### Jackson2Json序列化

```java
@Configuration  
public class RedisConfig {  
	@Bean  
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory)  
	{  
	    RedisTemplate<Object, Object> template = new RedisTemplate<>();  
	    template.setConnectionFactory(connectionFactory);  
	  
	    GenericJackson2JsonRedisSerializer serializer = new GenericJackson2JsonRedisSerializer();  
	    // 使用StringRedisSerializer来序列化和反序列化redis的key值  
	    template.setKeySerializer(new StringRedisSerializer());  
	    template.setValueSerializer(serializer);  
	  
	    // Hash的key也采用StringRedisSerializer的序列化方式  
	    template.setHashKeySerializer(new StringRedisSerializer());  
	    template.setHashValueSerializer(serializer);  
	  
	    template.afterPropertiesSet();  
	    return template;  
	}
}
```

>[!note] 反序列化问题
>jackson进行反序列化如果出现无法解析的字符串,不会自动忽略，而是会报异常
>需要在实体类中添加@JsonIgnoreProperties(ignoreUnknown = true)
>目前看FastJson没有这个问题

## 序列化解读
主要以这两个序列化器来说
GenericJackson2JsonRedisSerializer 和Jackson2JsonRedisSerializer

这两个功能差不多是吧，但如果使用 Jackson2JsonRedisSerializer，泛型不写的话，反序列化会出错
因为Jackson2JsonRedisSerializer并不知道反序列化成什么类
此类的构造函数中有一个类型参数，必须提供要序列化对象的类型信息(.class对象)。
也就是，这个序列化器需要我们指定类，也就是不能作为全局序列化类，但换来的是高性能

我们推荐使用GenericJackson2JsonRedisSerializer，不需要我们指定类，在存储时会把类的信息带过去

## springboot缓存原理

在Spring Boot中，数据的缓存管理存储依赖于Spring框架中cache相关的org.springframework.cache.Cache和org.springframework.cache.CacheManager缓存管理器接口。

如果程序中没有定义类型为CacheManager的Bean组件或者是名为cacheResolver的CacheResolver缓存解析器，Spring Boot将尝试选择并启用以下缓存组件（按照指定的顺序）：

（1）Generic

（2）JCache (JSR-107) (EhCache 3、Hazelcast、Infinispan等)

（3）EhCache 2.x

（4）Hazelcast

（5）Infinispan

（6）Couchbase

（7）Redis

（8）Caffeine

（9）Simple 

上面按照Spring Boot缓存组件的加载顺序，列举了支持的9种缓存组件，在项目中添加某个缓存管理组件（例如Redis）后，Spring Boot项目会选择并启用对应的缓存管理器。如果项目中同时添加了多个缓存组件，且没有指定缓存管理器或者缓存解析器（CacheManager或者cacheResolver），那么Spring Boot会按照上述顺序在添加的多个缓存中优先启用指定的缓存组件进行缓存管理。 

刚刚讲解的Spring Boot默认缓存管理中，没有添加任何缓存管理组件能实现缓存管理。这是因为开启缓存管理后，Spring Boot会按照上述列表顺序查找有效的缓存组件进行缓存管理，如果没有任何缓存组件，会默认使用最后一个Simple缓存组件进行管理。Simple缓存组件是Spring Boot默认的缓存管理组件，它默认使用内存中的ConcurrentMap进行缓存存储，所以在没有添加任何第三方缓存组件的情况下，可以实现内存中的缓存管理，但是我们不推荐使用这种缓存管理方式 

>[!note] 整合提示
>也就是说，我们如果什么缓存组件都不引入，就会默认使用最后一个Simple，这个是缓存在内存中
>如果我们引入多个缓存组件，但什么并不配置CacheManager来指定缓存组件，那么就会默认按照上方的顺序来使用最前的缓存组件
>推荐使用：引入redis等缓存组件，然后配置RedisCacheManager,来指定我们使用redis用来缓存




## redis缓存整合

springboot整合redis，使用注解配置

修改配置文件，添加缓存设置
```java
@EnableCaching  //开启缓存  
@Configuration  
public class RedisConfig {  
  
    @Bean  
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory)  
    {  
        RedisTemplate<Object, Object> template = new RedisTemplate<>();  
        template.setConnectionFactory(connectionFactory);  
  
        GenericJackson2JsonRedisSerializer serializer = new GenericJackson2JsonRedisSerializer();  
        // 使用StringRedisSerializer来序列化和反序列化redis的key值  
        template.setKeySerializer(new StringRedisSerializer());  
        template.setValueSerializer(serializer);  
  
        // Hash的key也采用StringRedisSerializer的序列化方式  
        template.setHashKeySerializer(new StringRedisSerializer());  
        template.setHashValueSerializer(serializer);  
  
        template.afterPropertiesSet();  
        return template;  
    }  
  
    @Bean  
    public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {  
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();  
        GenericJackson2JsonRedisSerializer jackson2JsonRedisSerializer = new GenericJackson2JsonRedisSerializer();  
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()  
                .entryTtl(Duration.ofDays(7)) // 7天缓存失效  
                // 设置key的序列化方式  
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))  
                // 设置value的序列化方式  
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))  
                // 不缓存null值  
                .disableCachingNullValues();  
        RedisCacheManager redisCacheManager = RedisCacheManager.builder(connectionFactory)  
                .cacheDefaults(config)  
                .transactionAware()  
                .build();  
  
  
        return redisCacheManager;  
    }  
}
```

**记得在config类上添加EnableCaching表示开启了缓存**

然后就能在service上添加注解，表示缓存

**@Cacheable**
根据方法对其返回结果进行缓存，下次请求时，如果缓存存在，则直接读取缓存数据返回；如果缓存不 存在，则执行方法，并把返回的结果存入缓存中。一般用在查询方法上。
![[Pasted image 20221103223019.png]]

**@CachePut**
使用该注解标志的方法，每次都会执行，并将结果存入指定的缓存中。其他方法可以直接从响应的缓存 中读取缓存数据，而不需要再去查询数据库。一般用在新增方法上。
![[Pasted image 20221103223154.png]]

**@CacheEvict**
使用该注解标志的方法，会清空指定的缓存。一般用在更新或者删除方法上
![[Pasted image 20221103223216.png]]
