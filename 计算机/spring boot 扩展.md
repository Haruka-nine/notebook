# Swagger
Swagger官网 ：http://swagger.io/  

Swagger官方文档：https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X

SpringFox是 spring 社区维护的一个项目（非官方），帮助使用者将 swagger2 集成到 Spring 中。常常用于 Spring 中帮助开发者生成文档，并可以轻松的在spring boot中使用。

也就是我们在spring中使用的swagger其实是使用的是springFox

springfox地址：https://github.com/springfox/springfox

swagger2和swagger3由一些区别，但3在面对spring boot2.6.0以上版本是出现问题
原因：Spring Boot 2.6.0开始使用基于PathPatternParser的路径匹配，而Springfox版本一直没有更新还是使用的AntPathMatcher导致了这个问题。
个人觉得应该使用swagger2，闲的蛋疼可以使用swagger3,而且swagger3的一些注解还有bug
## swagger2
这里建议2.9.2版本----原因：好看QAQ

### 安装和配置
#### 添加依赖包

```xml
<!--swagger-->  
<dependency>  
    <groupId>io.springfox</groupId>  
    <artifactId>springfox-swagger2</artifactId>  
    <version>2.9.2</version> 
</dependency>  
<dependency>  
    <groupId>io.springfox</groupId>  
    <artifactId>springfox-swagger-ui</artifactId>  
    <version>2.9.2</version>
</dependency>
```

#### 配置swagger
要使用swagger，我们必须对swagger进行配置，我们需要创建一个swagger的配置类，比如可以命名为SwaggerConfig.java

```java
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
http://localhost:8080/swagger-ui.html

### 注解使用

#### 接口注解
@API和@ApiOperation ，@ApiParam
```java
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
```java
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

-   @ApiModel：用来标类
    -   常用配置项：
        -   value：实体类简称
        -   description：实体类说明
-   @ApiModelProperty：用来描述类的字段的意义。
    -   常用配置项：
        -   value：字段说明
        -   example：设置请求示例（Example Value）的默认值，如果不配置，当字段为string的时候，此时请求示例中默认值为"".
        -   name：用新的字段名来替代旧的字段名。
        -   allowableValues：限制值得范围，例如`{1,2,3}`代表只能取这三个值；`[1,5]`代表取1到5的值；`(1,5)`代表1到5的值，不包括1和5；还可以使用infinity或-infinity来无限值，比如`[1, infinity]`代表最小值为1，最大值无穷大。
        -   required：标记字段是否必填，默认是false,
        -   hidden：用来隐藏字段，默认是false，如果要隐藏需要使用true，因为字段默认都会显示，就算没有`@ApiModelProperty`

#### 其他
当参数和返回值不是实体类时
对于非实体类参数，可以使用`@ApiImplicitParams`和`@ApiImplicitParam`来声明请求参数。  
`@ApiImplicitParams`用在方法头上，`@ApiImplicitParam`定义在`@ApiImplicitParams`里面，一个`@ApiImplicitParam`对应一个参数。
`@ApiImplicitParam`常用配置项：

-   name：用来定义参数的名字，也就是字段的名字,可以与接口的入参名对应。**如果不对应，也会生成，所以可以用来定义额外参数！**
-   value：用来描述参数
-   required：用来标注参数是否必填
-   paramType有path,query,body,form,header等方式，但对于对于非实体类参数的时候，常用的只有path,query,header；body和form是不常用的。body不适用于多个零散参数的情况，只适用于json对象等情况。【如果你的接口是`form-data`,`x-www-form-urlencoded`的时候可能不能使用swagger页面API调试，但可以在后面讲到基于BootstrapUI的swagger增强中调试，基于BootstrapUI的swagger支持指定`form-data`或`x-www-form-urlencoded`】
![[Pasted image 20221002152910.png]]

swagger无法对非实体类的响应进行详细说明，只能标注响应码等信息。是通过`@ApiResponses`和`@ApiResponse`来实现的。
![[Pasted image 20221002152853.png]]

### 整合spring Security注意
在Spring Boot整合Spring Security和Swagger的时候，需要配置拦截的路径和放行的路径，注意是放行以下几个路径。
```java
.antMatchers("/swagger**/**").permitAll() 
.antMatchers("/webjars/**").permitAll() 
.antMatchers("/v2/**").permitAll()
.antMatchers("/doc.html").permitAll() // 如果你用了bootstarp的Swagger UI界面，加一个这个。
```

### swagger的安全管理
-  如果你整合了权限管理，可以给swagger加上权限管理，要求访问swagger页面输入用户名和密码，这些是spring security和shiro的事了，这里不讲。
- 如果你仅仅是不想在正式环境中可以访问，可以在正式环境中关闭Swagger自动配置，这就不会有swagger页面了。在配置文件上使用`@Profile({"dev","test"})`注解来限制只在dev或者test下启用Swagger自动配置。  
然后在Spring Boot配置文件中修改当前profile`spring.profiles.active=release`，重启之后，此时无法访问`http://localhost:8080/swagger-ui.html`

## Swagger3

### 引入依赖
```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

### 配置类
```java
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
但swagger3和spring boot 2.6.x的版本冲突，原因在开头已经说过
所以对2.6.x需要做出修改
第一步
spring的配置文件修改
```yml
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher

```
第二步
swagger配置类修改
```java
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

### 访问

路径：http://localhost:6001/swagger-ui/index.html
![[Pasted image 20221002153911.png]]
但建议使用swgger注解，openAPI3本人测试有一些bug，可能是我环境问题？

# 自定义异常配置

## 全局异常处理
```java
@Slf4j  
@ControllerAdvice  
public class GlobalExceptionHandler {  
    //指定出现什么异常执行这个方法  
    @ExceptionHandler(Exception.class)  //这里的意思是什么异常都执行,全局异常处理  
    @ResponseBody   //为了返回数据  
    public R error(Exception e){  
        e.printStackTrace();  
        return R.error().message("执行了全局异常处理");  
    }  
  
    @ExceptionHandler(ArithmeticException.class)  //存在特殊异常处理时，执行特定异常，不执行全局异常处理  
    @ResponseBody   //为了返回数据  
    public R error(ArithmeticException e){  
        e.printStackTrace();  
        return R.error().message("执行了ArithmeticException异常处理");  
    }  
  
    //自定义异常  
    @ExceptionHandler(GuliException.class)  //存在特殊异常处理时，执行特定异常，不执行全局异常处理  
    @ResponseBody   //为了返回数据  
    public R error(GuliException e){  
        log.error(e.getMessage());  
        e.printStackTrace();  
        return R.error().code(e.getCode()).message(e.getMsg());  
    }  
}
```

>添加上述的类可以管理springboot的异常处理
>@ExceptionHandler可以决定捕获什么异常


第一个就是捕获异常，执行的全局异常处理方法，第二个就是捕获特殊异常也就是指定异常
第三个是指定的自定义异常
***存在特殊异常处理时，执行特定异常，不执行全局异常处理  ***

## 自定义异常类
所以我们自定义异常
```java
@Data  
@EqualsAndHashCode(callSuper = false)  
@AllArgsConstructor //有参构造方法  
@NoArgsConstructor  //无参构造方法  
public class GuliException extends RuntimeException {  
    @ApiModelProperty(value = "状态码")  
    private Integer code;  
    private String msg; //异常的信息  
}
```

上述的全局异常处理中添加自定义异常处理，然后我们使用时抛出自己的自定义异常，更清除的处理结果

# Lombok

maven版本引入地址：https://www.projectlombok.org/setup/maven

```xml
<dependencies>
	<dependency>
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<version>1.18.24</version>
		<scope>provided</scope>
	</dependency>
</dependencies>
```

## 介绍
Lombok能以简单的注解形式来简化java代码，提高开发人员的开发效率。例如开发中经常需要写的javabean，都需要花时间去添加相应的getter/setter，也许还要去写构造器、equals等方法，而且需要维护，当属性多时会出现大量的getter/setter方法，这些显得很冗长也没有太多技术含量，一旦修改属性，就容易出现忘记修改对应方法的失误。

Lombok能通过注解的方式，在编译时自动为属性生成构造器、getter/setter、equals、hashcode、toString等方法。出现的神奇就是在源码中没有getter和setter方法，但是在编译生成的字节码文件中有getter和setter方法。这样就省去了手动重建这些代码的麻烦，使代码看起来更简洁些。
## 常用注解

@Data注解：在JavaBean或类JavaBean中使用，这个注解包含范围最广，它包含getter、setter、NoArgsConstructor注解，即当使用当前注解时，会自动生成包含的所有方法；

@getter注解：在JavaBean或类JavaBean中使用，使用此注解会生成对应的getter方法；

@setter注解：在JavaBean或类JavaBean中使用，使用此注解会生成对应的setter方法；

@NoArgsConstructor注解：在JavaBean或类JavaBean中使用，使用此注解会生成对应的无参构造方法；

@AllArgsConstructor注解：在JavaBean或类JavaBean中使用，使用此注解会生成对应的有参构造方法；

@ToString注解：在JavaBean或类JavaBean中使用，使用此注解会自动重写对应的toStirng方法；

@EqualsAndHashCode注解：在JavaBean或类JavaBean中使用，使用此注解会自动重写对应的equals方法和hashCode方法；

@Slf4j：在需要打印日志的类中使用，当项目中使用了slf4j打印日志框架时使用该注解，会简化日志的打印流程，只需调用info方法即可；

@Log4j：在需要打印日志的类中使用，当项目中使用了log4j打印日志框架时使用该注解，会简化日志的打印流程，只需调用info方法即可；
使用：log.info("hello,{}",a)         //a='world',log.info 鼓励使用占位符

在使用以上注解需要处理参数时，处理方法如下（以@ToString注解为例，其他注解同@ToString注解）：

@ToString(exclude="column")

意义：排除column列所对应的元素，即在生成toString方法时不包含column参数；

@ToString(exclude={"column1","column2"})

意义：排除多个column列所对应的元素，其中间用英文状态下的逗号进行分割，即在生成toString方法时不包含多个column参数；

@ToString(of="column")

意义：只生成包含column列所对应的元素的参数的toString方法，即在生成toString方法时只包含column参数；；

@ToString(of={"column1","column2"})

意义：只生成包含多个column列所对应的元素的参数的toString方法，其中间用英文状态下的逗号进行分割，即在生成toString方法时只包含多个column参数；

# Easy Excel

## 介绍
EasyExcel是一个基于Java的、快速、简洁、解决大文件内存溢出的Excel处理工具。  
他能让你在不用考虑性能、内存的等因素的情况下，快速完成Excel的读、写等功能。

官网：https://easyexcel.opensource.alibaba.com/

github地址：https://github.com/alibaba/easyexcel

这里解释3.1.1
## 实体类
- [@ExcelProperty](https://gitee.com/mirrors/easyexcel/blob/master/easyexcel-core/src/main/java/com/alibaba/excel/annotation/ExcelProperty.java) 可设置字段。
![[Pasted image 20221002223105.png]]
```java
@Data
@EqualsAndHashCode
public class DemoData {
    // @ExcelProperty(index = 0)
    private String string;
    // @ExcelProperty("日期标题")
    private Date date;
    // @DateTimeFormat("yyyy-MM-dd HH:mm:ss")
    // private String dateStr;
    private Double doubleData;
}

```

## 自定义转换器
```java
public class CustomStringStringConverter implements Converter<String> {
    @Override
    public Class<?> supportJavaTypeKey() {
        return String.class;
    }

    @Override
    public CellDataTypeEnum supportExcelTypeKey() {
        return CellDataTypeEnum.STRING;
    }

    /** 这里读的时候会调用 */
    @Override
    public String convertToJavaData(ReadConverterContext<?> context) {
        return "自定义：" + context.getReadCellData().getStringValue();
    }

    /** 这里是写的时候会调用 */
    @Override
    public WriteCellData<?> convertToExcelData(WriteConverterContext<String> context) {
        return new WriteCellData<>(context.getValue());
    }
}

```

## 监听器
>监听器的问题：
>根源是ReadListener，我了解的更下层的是AnalysisEventListener，还有 [PageReadListener](https://gitee.com/mirrors/easyexcel/blob/master/easyexcel-core/src/main/java/com/alibaba/excel/read/listener/PageReadListener.java)是是已经被封装的
>监听器不能被springboot管理（因为被spring管理就是单例，而读取多个文件调用同一个监听器会分不清是那个一个文件），这意味这**springboot的自动填充等功能我们都不能用**，所以我们想要操作数据库需要将service或者dao当作参数传进来。并且我们每次使用监听器**都需要new**

最简单的监听器实现
```java
// 有个很重要的点 DemoDataListener 不能被spring管理，要每次读取excel都要new,然后里面用到spring可以构造方法传进去
@Slf4j
public class DemoDataListener implements ReadListener<DemoData> {

    /**
     * 每隔5条存储数据库，实际使用中可以100条，然后清理list ，方便内存回收
     */
    private static final int BATCH_COUNT = 100;
    /**
     * 缓存的数据
     */
    private List<DemoData> cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
    /**
     * 假设这个是一个DAO，当然有业务逻辑这个也可以是一个service。当然如果不用存储这个对象没用。
     */
    private DemoDAO demoDAO;

    public DemoDataListener() {
        // 这里是demo，所以随便new一个。实际使用如果到了spring,请使用下面的有参构造函数
        demoDAO = new DemoDAO();
    }

    /**
     * 如果使用了spring,请使用这个构造方法。每次创建Listener的时候需要把spring管理的类传进来
     *
     * @param demoDAO
     */
    public DemoDataListener(DemoDAO demoDAO) {
        this.demoDAO = demoDAO;
    }

    /**
     * 这个每一条数据解析都会来调用
     *
     * @param data    one row value. Is is same as {@link AnalysisContext#readRowHolder()}
     * @param context
     */
    @Override
    public void invoke(DemoData data, AnalysisContext context) {
        log.info("解析到一条数据:{}", JSON.toJSONString(data));
        cachedDataList.add(data);
        // 达到BATCH_COUNT了，需要去存储一次数据库，防止数据几万条数据在内存，容易OOM
        if (cachedDataList.size() >= BATCH_COUNT) {
            saveData();
            // 存储完成清理 list
            cachedDataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);
        }
    }

    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        // 这里也要保存数据，确保最后遗留的数据也存储到数据库
        saveData();
        log.info("所有数据解析完成！");
    }

    /**
     * 加上存储数据库
     */
    private void saveData() {
        log.info("{}条数据，开始存储数据库！", cachedDataList.size());
        demoDAO.save(cachedDataList);
        log.info("存储数据库成功！");
    }
}
```

AnalysisEventListener重写了一些ReadListener的方法，但invoke和doAfterAllAnalysed并没有动，所以基本使用起来和ReadListener一样，我们还需要重写每次读取和读取完成方法
这里给出本人一个项目中的使用

```java
public class SubjectExcelListener extends AnalysisEventListener<SubjectData> {  
    //因为SubjectExcelListener不能交给spring进行管理，需要自己new，不能注入其他对象  
    //不能实现数据库操作  
    public EduSubjectService subjectService;  
  
    public SubjectExcelListener(EduSubjectService subjectService) {  
        this.subjectService = subjectService;  
    }  
  
    public SubjectExcelListener() {  
    }  
  
    //读取excel内容，一行一行进行读取  
    @Override  
    public void invoke(SubjectData data, AnalysisContext context) {  
        if (data==null){  
            throw new GuliException(20001,"文件数据为空");  
        }  
  
        //一行一行读取，每次读取有两个值，第一个值一级分类，第二个值二级分类  
        EduSubject existOneSubject = this.existOneSubject(subjectService, data.getOneSubjectName());  
        if (existOneSubject==null){  
            //没有相同一级分类,进行添加  
            existOneSubject = new EduSubject();  
            existOneSubject.setParentId("0");  
            existOneSubject.setTitle(data.getOneSubjectName());  
            subjectService.save(existOneSubject);  
        }  
  
        String pid = existOneSubject.getId();  
        //添加二级分类  
        //判断二级分类是否重复  
        EduSubject existTwoSubject = this.existTwoSubject(subjectService, data.getTwoSubjectName(), pid);  
        if (existTwoSubject==null){  
            //没有相同二级分类  
            existTwoSubject = new EduSubject();  
            existTwoSubject.setParentId(pid);  
            existTwoSubject.setTitle(data.getTwoSubjectName());  
            subjectService.save(existTwoSubject);  
        }  
  
    }  
  
    //判断一级分类不能重复添加  
    private EduSubject existOneSubject(EduSubjectService eduSubjectService,String name){  
        QueryWrapper<EduSubject> wrapper = new QueryWrapper<>();  
        wrapper.eq("title",name);  
        wrapper.eq("parent_id","0");  
        EduSubject oneSubject = eduSubjectService.getOne(wrapper);  
        return oneSubject;  
    }  
  
    //判断二级分类不能重复添加  
    private EduSubject existTwoSubject(EduSubjectService eduSubjectService,String name,String pid){  
        QueryWrapper<EduSubject> wrapper = new QueryWrapper<>();  
        wrapper.eq("title",name);  
        wrapper.eq("parent_id",pid);  
        EduSubject twoSubject = eduSubjectService.getOne(wrapper);  
        return twoSubject;  
    }  
  
    @Override  
    public void doAfterAllAnalysed(AnalysisContext context) {  
  
    }  
}
```

而[PageReadListener](https://gitee.com/mirrors/easyexcel/blob/master/easyexcel-core/src/main/java/com/alibaba/excel/read/listener/PageReadListener.java)，是被封装过的方法，是每读取100条数据，然后进行消费
我们需要给其传入一个消费者`Consumer<List<T>> consumer`,结果就是每次都把数据给消费者,具体查看源码

```java
@Test
public void simpleRead() {
	// 这个消费者在 PageReadListener 的 invoke、doAfterAllAnalysed 中会被调用。
	Consumer<List<DemoData>> consumer = dataList -> {
	    dataList.forEach(demoData -> log.info("读取到一条数据{}", JSON.toJSONString(demoData)));
	};
	// 用上面的消费者创建监听器
	PageReadListener<DemoData> myListener = new PageReadListener<>(consumer);
	// 开始读取
	EasyExcel.read(fileName, DemoData.class, myListener).sheet().doRead();
}

```

## 读取的流程

一般先有实体类，在实体类上加入注解

然后写监听器，或者用现成的[PageReadListener](https://gitee.com/mirrors/easyexcel/blob/master/easyexcel-core/src/main/java/com/alibaba/excel/read/listener/PageReadListener.java)

在使用处new 监听器，传入service，然后调用

```java
EasyExcel.read(fileName, DemoData.class, myListener).sheet().doRead();
```

## 读取的一些情况

### 多行头，也就是需要跳过N行
使用headRowNumber(7)
```java
EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet()  
// 这里可以设置1，因为头就是一行。如果多行头，可以设置其他值。不传入也可以，因为默认会根据DemoData 来解析，他没有指定头，也就是默认1行  
.headRowNumber(1).doRead();
```

### 同步的返回
也就是不想一行一行的处理，想要直接获得一整个数据列表
**不推荐使用，如果数据量大会把数据放到内存里**

```java
    /**
     * 同步的返回，不推荐使用，如果数据量大会把数据放到内存里面
     */
    @Test
    public void synchronousRead() {
        String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
        // 这里 需要指定读用哪个class去读，然后读取第一个sheet 同步读取会自动finish
        List<DemoData> list = EasyExcel.read(fileName).head(DemoData.class).sheet().doReadSync();
        for (DemoData data : list) {
            LOGGER.info("读取到数据:{}", JSON.toJSONString(data));
        }
        // 这里 也可以不指定class，返回一个list，然后读取第一个sheet 同步读取会自动finish
        List<Map<Integer, String>> listMap = EasyExcel.read(fileName).sheet().doReadSync();
        for (Map<Integer, String> data : listMap) {
            // 返回每条数据的键值对 表示所在的列 和所在列的值
            LOGGER.info("读取到数据:{}", JSON.toJSONString(data));
        }
    }
```

其他情况，具体看官网介绍，这里只是理解概念流程


# Mybatis-plus代码生成

根据数据库进行代码反向生成
官网：https://baomidou.com/pages/779a6e/

## 依赖导入
```xml
<!--mybatis-plus-->
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-boot-starter</artifactId>  
    <version>3.5.2</version>  
</dependency>  
<!--代码生成器-->
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-generator</artifactId>  
    <version>3.5.3</version>  
</dependency>  
<!--代码生成器所需依赖-->
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-freemarker</artifactId>  
</dependency>
```


## 代码实现

有很多设置可调，具体看官网设置，下方仅为参考
```java
package com.haruka.tokendemo;  
  
import com.baomidou.mybatisplus.generator.FastAutoGenerator;  
import com.baomidou.mybatisplus.generator.config.OutputFile;  
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;  
import org.junit.jupiter.api.Test;  
  
import java.util.Collections;  
  
public class mybatis {  
    @Test  
    public void run(){  
        FastAutoGenerator.create("jdbc:mysql://localhost:3306/spring?characterEncoding=utf-8&serverTimezone=UTC", "root", "wang200301")  
                .globalConfig(builder -> {  
                    builder.author("haruka_nine") // 设置作者  
                            .enableSwagger() // 开启 swagger 模式  
                            .fileOverride() // 覆盖已生成文件(这个设置已被废弃)  
                            .outputDir("C:\\Users\\YYM\\Desktop\\java\\SpringSecurity\\TokenDemo\\src\\main\\java"); // 指定输出目录  
                })  
                .packageConfig(builder -> {  
                    builder.parent("com.haruka") // 设置父包名  
                            .moduleName("tokendemo") // 设置父包模块名  
                            .pathInfo(Collections.singletonMap(OutputFile.xml, "C:\Users\YYM\Desktop\java\SpringSecurity\TokenDemo\src\main\resources\com\haruka\tokendemo\mapper")); // 设置mapperXml生成路径  
                })  
                .strategyConfig(builder -> {  
                    builder.addInclude("sys_user") // 设置需要生成的表名  
                            .addTablePrefix("t_", "c_","sys_"); // 设置过滤表前缀  
                    builder.serviceBuilder()  
                            .formatServiceFileName("%sService"); //去掉service前的i
                    builder.entityBuilder()  
                            .enableLombok();   //使用lombok
                    builder.controllerBuilder()  
                            .enableHyphenStyle()  
                            .enableRestStyle();  
  
                })  
                .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker引擎模板，默认的是Velocity引擎模板  
                .execute();  
  
    }  
}
```

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
# 连接超时时间（毫秒）  
spring.redis.timeout=1800000  
  
# 连接池配置  
#连接池最大连接数（使用负值表示没有限制）  
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
    @SuppressWarnings(value = { "unchecked", "rawtypes" })  
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory)  
    {  
        RedisTemplate<Object, Object> template = new RedisTemplate<>();  
        template.setConnectionFactory(connectionFactory);  

        Jackson2JsonRedisSerializer serializer = new Jackson2JsonRedisSerializer<>(Object.class);   
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

# FastJson

>[!note] fastjson版本
>现在有1.x，12兼容版，2.x版本
>1.x版本截至版本1.2.83,兼容版可能有bug，2.x将持续更新

1.x版本引入依赖

```xml
<dependency>  
    <groupId>com.alibaba</groupId>  
    <artifactId>fastjson</artifactId>  
    <version>1.2.83</version>  
</dependency>
```
https://github.com/alibaba/fastjson


2.x版本引入依赖

```xml
<dependency>  
    <groupId>com.alibaba.fastjson2</groupId>  
    <artifactId>fastjson2</artifactId>  
    <version>2.0.15</version>  
</dependency>  
<!--spring中需要引入额外依赖-->
<dependency>  
    <groupId>com.alibaba.fastjson2</groupId>  
    <artifactId>fastjson2-extension</artifactId>  
    <version>2.0.15</version>  
</dependency>
```

https://github.com/alibaba/fastjson2

# mybatis-plus

## 逻辑删除

首先，数据库的默认值需要设置为逻辑未删除值，比如0

### 3.1.1版本以上

```yaml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag #这个和@TableLogic相似，3.3.0后有一个就行，3.3.0前必须有@TableLogic，这个有没有都行，建议直接使用@TableLogic，这个不用管
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

在实体类上逻辑删除属性上添加@TableLogic

### 3.1.1版本以下

除了需要配置以上版本的，还需要引入逻辑删除插件

```java
@Configuration  
@MapperScan("com.atguigu.cmsService.mapper")  
public class mybatisConfig {  
    //逻辑删除插件  
    @Bean  
    public ISqlInjector sqlInjector() {  
        return new LogicSqlInjector();  
    }  
}
```

## 分页插件

需要引入分页插件

```java
@Configuration  
@MapperScan("com.atguigu.eduService.mapper")  
public class mybatisConfig {  
    /**  
     * 分页插件  
     */  
    @Bean  
    public PaginationInterceptor paginationInterceptor() {  
        return new PaginationInterceptor();  
    }  
}
```

引入分页插件后就可以直接使用page进行查询
```java
Page<CrmBanner> pageBanner = new Page<>(page,limit);  
bannerService.page(pageBanner,null);  
return R.ok().data("item",pageBanner.getRecords()).data("total",pageBanner.getTotal());
```

不引入分页插件service的page方法就是查询全部。

## 自动填充插件

配置插件

```java
@Component  
public class MyMetaObjectHandler implements MetaObjectHandler {  
    @Override  
    public void insertFill(MetaObject metaObject) {  
        //属性名称，不是字段名称  
        this.setFieldValByName("gmtCreate", new Date(), metaObject);  
        this.setFieldValByName("gmtModified", new Date(), metaObject);  
    }  
    @Override  
    public void updateFill(MetaObject metaObject) {  
        this.setFieldValByName("gmtModified", new Date(), metaObject);  
    }  
}
```

插件使用
注意更新时间要用INSERT_UPDATE，否则第一次插入这个更新时间会因没有值报错

```java
@ApiModelProperty(value = "创建时间")  
@TableField(fill = FieldFill.INSERT)  
private Date gmtCreate;  
  
@ApiModelProperty(value = "更新时间")  
@TableField(fill = FieldFill.INSERT_UPDATE)  
private Date gmtModified;
```