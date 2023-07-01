# 概述

`RestTemplate`是执行HTTP请求的同步阻塞式的客户端，它在HTTP客户端库（例如JDK HttpURLConnection，Apache HttpComponents，okHttp等）基础封装了更加简单易用的模板方法API。也就是说RestTemplate是一个封装，底层的实现还是java应用开发中常用的一些HTTP客户端。但是相对于直接使用底层的HTTP客户端库，它的操作更加方便、快捷，能很大程度上提升我们的开发效率。

`RestTemplate`作为spring-web项目的一部分，在Spring 3.0版本开始被引入。RestTemplate类通过为HTTP方法（例如GET，POST，PUT，DELETE等）提供重载的方法，提供了一种非常方便的方法访问基于HTTP的Web服务。如果你的Web服务API基于标准的RESTful风格设计，使用效果将更加的完美。

> 根据Spring官方文档及源码中的介绍，RestTemplate在将来的版本中它可能会被弃用，因为他们已在Spring 5中引入了WebClient作为非阻塞式Reactive HTTP客户端。但是RestTemplate目前在Spring 社区内还是很多项目的“重度依赖”，比如说Spring Cloud。另外，RestTemplate说白了是一个客户端API封装，和服务端相比，非阻塞Reactive 编程的需求并没有那么高。



# 基本使用

## 非Sping环境

> [JSONPlaceholder](http://jsonplaceholder.typicode.com/)是一个提供免费的在线REST API的网站，我们在开发时可以使用它提供的url地址测试下网络请求以及请求参数。或者当我们程序需要获取一些模拟数据、模拟图片时也可以使用它。



RestTemplate是spring的一个rest客户端，在spring-web这个包下。这个包虽然叫做spring-web，但是它的RestTemplate可以脱离Spring 环境使用。

```XML
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-web</artifactId>
  <version>5.2.6.RELEASE</version>
</dependency>
```



测试一下Hello world，使用RestTemplate发送一个GET请求，并把请求得到的JSON数据结果打印出来。

```Java
@Test
public void simpleTest()
{
    RestTemplate restTemplate = new RestTemplate();
    String url = "http://jsonplaceholder.typicode.com/posts/1";
    String str = restTemplate.getForObject(url, String.class);
    System.out.println(str);
}
```



## spring 环境

将maven坐标从spring-web换成spring-boot-starter-web

```XML
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

```



将RestTemplate配置初始化为一个Bean。这种初始化方法，是使用了JDK 自带的HttpURLConnection作为底层HTTP客户端实现。我们还可以把底层实现切换为Apache HttpComponents，[okHttp](https://so.csdn.net/so/search?q=okHttp&spm=1001.2101.3001.7020)等，我们后续章节会为大家介绍。



```Java
@Configuration
public class ContextConfig {

    //默认使用JDK 自带的HttpURLConnection作为底层实现
    @Bean
    public RestTemplate restTemplate(){
        RestTemplate restTemplate = new RestTemplate();
        return restTemplate;
    }
}
```

在需要使用RestTemplate 的位置，注入并使用即可。

```Java
  @Resource //@AutoWired
  private RestTemplate restTemplate;
```



# 底层HTTP客户端类库的切换

## 分析

RestTemplate有一个非常重要的类叫做HttpAccessor，可以理解为用于HTTP接触访问的基础类。

- RestTemplate 支持至少三种HTTP客户端库。

  - SimpleClientHttpRequestFactory。对应的HTTP库是java JDK自带的HttpURLConnection。

  - HttpComponentsAsyncClientHttpRequestFactory。对应的HTTP库是Apache HttpComponents。

  - OkHttp3ClientHttpRequestFactory。对应的HTTP库是OkHttp

- java JDK自带的HttpURLConnection是默认的底层HTTP实现客户端

- SimpleClientHttpRequestFactory，即java JDK自带的HttpURLConnection不支持HTTP协议的Patch方法，如果希望使用Patch方法，需要将底层HTTP客户端实现切换为Apache HttpComponents 或 OkHttp

- 可以通过设置setRequestFactory方法，来切换RestTemplate的底层HTTP客户端实现类库。



## 实现切换

从开发人员的反馈，和网上的各种HTTP客户端性能以及易用程度评测来看，[OkHttp](https://so.csdn.net/so/search?q=OkHttp&spm=1001.2101.3001.7020) 优于 Apache HttpComponents、Apache HttpComponents优于HttpURLConnection。所以我个人更建议大家将底层HTTP实现切换为okHTTP。

### okHTTP

首先通过maven坐标将okHTTP的包引入到项目中来

```XML
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>4.7.2</version>
</dependency>

```

如果是spring 环境下通过如下方式使用OkHttp3ClientHttpRequestFactory初始化RestTemplate bean对象。

```Java
@Configuration
public class ContextConfig {
    @Bean("OKHttp3")
    public RestTemplate OKHttp3RestTemplate(){
        RestTemplate restTemplate = new RestTemplate(new OkHttp3ClientHttpRequestFactory());
        return restTemplate;
    }
}
```

如果是非Spring环境，直接`new RestTemplate(new OkHttp3ClientHttpRequestFactory()`之后使用就可以了



### Apache HttpComponents

```XML
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.12</version>
</dependency>
```

使用HttpComponentsClientHttpRequestFactory初始化RestTemplate bean对象

```Java
@Bean("httpClient")
public RestTemplate httpClientRestTemplate(){
    RestTemplate restTemplate = new RestTemplate(new HttpComponentsClientHttpRequestFactory());
    return restTemplate;
}
```



# 实现问题

### Post上传文件

注意：上传文件时的value为`FileSystemResource`

```Java
@Test
    public void testPostFile(){
 
        RestTemplate restTemplate = new RestTemplate();
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.MULTIPART_FORM_DATA);
        MultiValueMap<String, Object> map= new LinkedMultiValueMap<>();
        map.add("id", "1");
        map.add("file",new FileSystemResource("/Users/aihe/code/init/src/test/java/me/aihe/RestTemplateTest.java"));
        HttpEntity<MultiValueMap<String, Object>> request = new HttpEntity<>(map, headers);
        String fooResourceUrl = "http://test.aihe.space/";
        ResponseEntity<String> response = restTemplate.postForEntity(fooResourceUrl, request, String.class);
        System.out.println(response.getStatusCode());
        System.out.println(response.getBody());
    }
 
```

