# 简介

Swagger官网 ：[http://swagger.io/](http://swagger.io/)

Swagger官方文档：[https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X](https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X)

SpringFox是 spring 社区维护的一个项目（非官方），帮助使用者将 swagger2 集成到 Spring 中。常常用于 Spring 中帮助开发者生成文档，并可以轻松的在spring boot中使用。

也就是我们在spring中使用的swagger其实是使用的是springFox

springfox地址：[https://github.com/springfox/springfox](https://github.com/springfox/springfox)



> [!info] springBoot2.6以上
Springfox使用的路径匹配是基于AntPathMatcher的，而Spring Boot 2.6.X使用的是PathPatternMatcher
所以会出问题
2.6以上版本使用swagger需要在配置文件中添加
spring.mvc.pathmatch.matching-strategy=ANT_PATH_MATCHER

# swagger2

这里建议2.9.2版本----原因：好看QAQ

## 安装和配置

### 添加依赖包

```HTML
 <!--springboot 集成 swagger-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
            <exclusions>
                <exclusion>
                    <groupId>io.swagger</groupId>
                    <artifactId>swagger-annotations</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>io.swagger</groupId>
                    <artifactId>swagger-models</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-annotations</artifactId>
            <version>1.5.21</version>
        </dependency>
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-models</artifactId>
            <version>1.5.21</version>
        </dependency>
```

> [!note] 报错解决
为什么上边的依赖这么长？
因为默认的io.swagger中的两个是1.5.20版本的，这个版本在打开swagger的web界面时，有可能会丢出一个异常，虽然并不会影响运行，但能解决还是解决

异常原因：1.5.20的一个bug
1.5.20版本中的example只判断了是否为null，没有判断是否为空串；
会报java.lang.NumberFormatException: For input string: ""错误，出错的原因呢是因为 空字符串`""`无法转成`Number`。
1.5.21版本对两者都进行了判断。

#### 配置swagger

要使用swagger，我们必须对swagger进行配置，我们需要创建一个swagger的配置类，比如可以命名为SwaggerConfig.java

```Java
@Configuration // 标明是配置类  
@EnableSwagger2 //开启swagger功能  
public class SwaggerConfig2 {  
    @Bean  
    public Docket createRestApi() {  
        return new Docket(DocumentationType.SWAGGER_2)  // DocumentationType.SWAGGER_2 固定的，代表swagger2  
//                .groupName("分布式任务系统") // 如果配置多个文档的时候，那么需要配置groupName来分组标识  
                .apiInfo(apiInfo()) // 用于生成API信息  
                .select() // select()函数返回一个ApiSelectorBuilder实例,用来控制接口被swagger做成文档  
                .apis(RequestHandlerSelectors.basePackage("com.example.controller")) // 用于指定扫描哪个包下的接口  
                .paths(PathSelectors.any())// 选择所有的API,如果你想只为部分API生成文档，可以配置这里  
                .build();  
    }  
  
    /**  
     * 用于定义API主界面的信息，比如可以声明所有的API的总标题、描述、版本  
     * @return  
     */  
    private ApiInfo apiInfo() {  
        return new ApiInfoBuilder()  
                .title("XX项目API") //  可以用来自定义API的主标题  
                .description("XX项目SwaggerAPI管理") // 可以用来描述整体的API  
                .termsOfServiceUrl("") // 用于定义服务的域名  
                .version("1.0") // 可以用来定义版本。  
                .build(); //  
    }  
}
```

#### 测试

端口号换成自己项目的端口号
[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)

### 注解使用

#### 接口注解

@API和@ApiOperation ，@ApiParam

```Java
@Api(tags = "讲师管理")  
@RestController  
@RequestMapping("/eduService/teacher")  
@CrossOrigin  //跨域处理  
public class EduTeacherController {  
    //把service注入  
    @Autowired  
    private EduTeacherService teacherService;  
  
    //1.查询讲师表所有数据  
    //rest风格  
    @ApiOperation(value = "所有讲师列表")  
    @GetMapping("findAll")  
    public R findAllTeacher(){  
        //调用service的方法实现查询所有操作  
        List<EduTeacher> list = teacherService.list(null);  
        return R.ok().data("items",list);  
    }
}
```

@API放到类上，表示这是一个接口组，tags表明接口组的名字
@ApiOperation放到接口上，表示这是一个接口，value表明接口名字
@ApiParam放在接口的的参数

![[Pasted image 20221002150626.png]]

#### 实体类注解

@ApiModel和@ApiModelProperty

对实体类进行标注，在接口的请求参数是实体类时可以显示标注

```Java
@Data  
@EqualsAndHashCode(callSuper = false)  
@Accessors(chain = true)  
@ApiModel(value="EduTeacher对象", description="讲师")  
public class EduTeacher implements Serializable {  
  
    private static final long serialVersionUID = 1L;  
  
    @ApiModelProperty(value = "讲师ID")  
    @TableId(value = "id", type = IdType.ID_WORKER_STR)  
    private String id;  
  
    @ApiModelProperty(value = "讲师姓名")  
    private String name;  
  
    @ApiModelProperty(value = "讲师简介")  
    private String intro;  
  
    @ApiModelProperty(value = "讲师资历,一句话说明讲师")  
    private String career;  
  
    @ApiModelProperty(value = "头衔 1高级讲师 2首席讲师")  
    private Integer level;  
  
    @ApiModelProperty(value = "讲师头像")  
    private String avatar;  
  
    @ApiModelProperty(value = "排序")  
    private Integer sort;  
  
    @ApiModelProperty(value = "逻辑删除 1（true）已删除， 0（false）未删除")  
    @TableLogic  
    private Integer isDeleted;  
  
    @ApiModelProperty(value = "创建时间")  
    @TableField(fill = FieldFill.INSERT)  
    private Date gmtCreate;  
  
    @ApiModelProperty(value = "更新时间")  
    @TableField(fill = FieldFill.INSERT_UPDATE)  
    private Date gmtModified;  
  
  
}
```

标注后效果

![[Pasted image 20221002151946.png]]

- @ApiModel：用来标类

  - 常用配置项：

    - value：实体类简称

    - description：实体类说明

- @ApiModelProperty：用来描述类的字段的意义。

  - 常用配置项：

    - value：字段说明

    - example：设置请求示例（Example Value）的默认值，如果不配置，当字段为string的时候，此时请求示例中默认值为"".

    - name：用新的字段名来替代旧的字段名。

    - allowableValues：限制值得范围，例如`{1,2,3}`代表只能取这三个值；`[1,5]`代表取1到5的值；`(1,5)`代表1到5的值，不包括1和5；还可以使用infinity或-infinity来无限值，比如`[1, infinity]`代表最小值为1，最大值无穷大。

    - required：标记字段是否必填，默认是false,

    - hidden：用来隐藏字段，默认是false，如果要隐藏需要使用true，因为字段默认都会显示，就算没有`@ApiModelProperty`

#### 其他

当参数和返回值不是实体类时
对于非实体类参数，可以使用`@ApiImplicitParams`和`@ApiImplicitParam`来声明请求参数。
`@ApiImplicitParams`用在方法头上，`@ApiImplicitParam`定义在`@ApiImplicitParams`里面，一个`@ApiImplicitParam`对应一个参数。
`@ApiImplicitParam`常用配置项：

- name：用来定义参数的名字，也就是字段的名字,可以与接口的入参名对应。**如果不对应，也会生成，所以可以用来定义额外参数！**

- value：用来描述参数

- required：用来标注参数是否必填

- paramType有path,query,body,form,header等方式，但对于对于非实体类参数的时候，常用的只有path,query,header；body和form是不常用的。body不适用于多个零散参数的情况，只适用于json对象等情况。【如果你的接口是`form-data`,`x-www-form-urlencoded`的时候可能不能使用swagger页面API调试，但可以在后面讲到基于BootstrapUI的swagger增强中调试，基于BootstrapUI的swagger支持指定`form-data`或`x-www-form-urlencoded`】

![[Pasted image 20221002152910.png]]

swagger无法对非实体类的响应进行详细说明，只能标注响应码等信息。是通过`@ApiResponses`和`@ApiResponse`来实现的。

![[Pasted image 20221002152853.png]]



### 配置拦截器放行

如果我们配置了拦截器吗，就会导致swagger被拦截，不能访问，需要配置拦截器放行

```Java
/**
 * 拦截器配置
 *
 * @author liuShuai
 */
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
   
    @Bean
    public TokenInterceptor tokenInterceptor() {
        return new TokenInterceptor();
    }
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry
                .addInterceptor(tokenInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/user/login")
                .excludePathPatterns("/user/downloadExcel")
                //对swagger界面进行放行
                .excludePathPatterns("/swagger-resources/**", "/webjars/**", "/v2/**", "/swagger-ui.html/**");
    }
    //添加swagger资源访问
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }
}
```



### 整合spring Security注意

在Spring Boot整合Spring Security和Swagger的时候，需要配置拦截的路径和放行的路径，注意是放行以下几个路径。

```Java
.antMatchers("/swagger**/**").permitAll() 
.antMatchers("/webjars/**").permitAll() 
.antMatchers("/v2/**").permitAll()
.antMatchers("/doc.html").permitAll() // 如果你用了bootstarp的Swagger UI界面，加一个这个。
```

### swagger的安全管理

- 如果你整合了权限管理，可以给swagger加上权限管理，要求访问swagger页面输入用户名和密码，这些是spring security和shiro的事了，这里不讲。

- 如果你仅仅是不想在正式环境中可以访问，可以在正式环境中关闭Swagger自动配置，这就不会有swagger页面了。在配置文件上使用`@Profile({"dev","test"})`注解来限制只在dev或者test下启用Swagger自动配置。
然后在Spring Boot配置文件中修改当前profile`spring.profiles.active=release`，重启之后，此时无法访问`http://localhost:8080/swagger-ui.html`

# Swagger3



## 引入依赖

springfox家的swagger3兼容swagger2的注解，所以这里升级使用的是springfox实现的swagger3！还有一个springdoc实现的是不兼容的！注意。

```HTML
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

## 配置类

```Java
@Configuration
@EnableOpenApi
public class SwaggerConfig {
    @Bean
    public Docket desertsApi(){
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.atxins.controller"))
                .paths(PathSelectors.any())
                .build()
                .groupName("default")
                .enable(true);
    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("微服务（GMS） API说明文档")
                .description("微服务（GMS） API说明文档")
                .contact(new Contact("GMS", "https://blog.csdn.net/Achard_Wang", "787579251@qq.com"))
                .termsOfServiceUrl("https://www.zybuluo.com/mdeditor#2281023-full-reader")
                .version("1.0")
                .build();
    }
}
```



## 注解对比

这里使用的springfox的swagger3,注解是兼容swagger2的注解的

||||
|-|-|-|
|Swagger3|Swagger2|注解说明|
|@Tag(name = “接口类描述”)|@Api|Controller 类|
|@Operation(summary =“接口方法描述”)|@ApiOperation|Controller 方法|
|@Parameters|@ApiImplicitParams|Controller 方法|
|@Parameter(description=“参数描述”)|@ApiImplicitParam@ApiParam|Controller 方法上 @Parameters 里Controller 方法的参数|
|@Parameter(hidden = true)@Operation(hidden = true)@Hidden|@ApiIgnore|排除或隐藏api|
|@Schema|@ApiModel@ApiModelProperty|DTO实体DTO实体属性|

## 拦截器放行

```Java
@Configuration
public class WebMvcConfig extends WebMvcConfigurationSupport {
 
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/swagger-ui/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/springfox-swagger-ui/");
        super.addResourceHandlers(registry);
    }
 
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor()
                .addPathPatterns("/**")
                .excludePathPatterns("/swagger**/**")
                .excludePathPatterns("/webjars/**")
                .excludePathPatterns("/v3/**")
                .excludePathPatterns("/doc.html");
        super.addInterceptors(registry);
    }
}
```



## 生产环境禁用swgger3

```XML
springfox.documentation.enabled=false
```

## springboot2.6.x版本问题

但swagger3和spring boot 2.6.x的版本冲突，原因在开头已经说过
所以对2.6.x需要做出修改
第一步
spring的配置文件修改

```YAML
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher

```

第二步
swagger配置类修改

```Java
/***
 * @author qingfeng.zhao
 * @date 2022/3/26
 * @apiNote
 */
@EnableOpenApi
@Configuration
public class SpringFoxSwaggerConfig {
    /**
     * 配置基本信息
     * @return
     */
    @Bean
    public ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Swagger Test App Restful API")
                .description("swagger test app restful api")
                .termsOfServiceUrl("https://github.com/geekxingyun")
                .contact(new Contact("技术宅星云","https://xingyun.blog.csdn.net","fairy_xingyun@hotmail.com"))
                .version("1.0")
                .build();
    }

    /**
     * 配置文档生成最佳实践
     * @param apiInfo
     * @return
     */
    @Bean
    public Docket createRestApi(ApiInfo apiInfo) {
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo)
                .groupName("SwaggerGroupOneAPI")
                .select()
                .apis(RequestHandlerSelectors.withClassAnnotation(RestController.class))
                .paths(PathSelectors.any())
                .build();
    }

    /**
    * 增加如下配置可解决Spring Boot 6.x 与Swagger 3.0.0 不兼容问题
    **/
    @Bean
    public WebMvcEndpointHandlerMapping webEndpointServletHandlerMapping(WebEndpointsSupplier webEndpointsSupplier, ServletEndpointsSupplier servletEndpointsSupplier, ControllerEndpointsSupplier controllerEndpointsSupplier, EndpointMediaTypes endpointMediaTypes, CorsEndpointProperties corsProperties, WebEndpointProperties webEndpointProperties, Environment environment) {
        List<ExposableEndpoint<?>> allEndpoints = new ArrayList();
        Collection<ExposableWebEndpoint> webEndpoints = webEndpointsSupplier.getEndpoints();
        allEndpoints.addAll(webEndpoints);
        allEndpoints.addAll(servletEndpointsSupplier.getEndpoints());
        allEndpoints.addAll(controllerEndpointsSupplier.getEndpoints());
        String basePath = webEndpointProperties.getBasePath();
        EndpointMapping endpointMapping = new EndpointMapping(basePath);
        boolean shouldRegisterLinksMapping = this.shouldRegisterLinksMapping(webEndpointProperties, environment, basePath);
        return new WebMvcEndpointHandlerMapping(endpointMapping, webEndpoints, endpointMediaTypes, corsProperties.toCorsConfiguration(), new EndpointLinksResolver(allEndpoints, basePath), shouldRegisterLinksMapping, null);
    }
    private boolean shouldRegisterLinksMapping(WebEndpointProperties webEndpointProperties, Environment environment, String basePath) {
        return webEndpointProperties.getDiscovery().isEnabled() && (StringUtils.hasText(basePath) || ManagementPortType.get(environment).equals(ManagementPortType.DIFFERENT));
    }
}


```

## 访问

路径：[http://localhost:6001/swagger-ui/index.html](http://localhost:6001/swagger-ui/index.html)

![[Pasted image 20221002153911.png]]
但建议使用swgger注解，openAPI3本人测试有一些bug，可能是我环境问题？

