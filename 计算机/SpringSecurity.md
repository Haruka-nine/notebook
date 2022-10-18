# 简介

​ **Spring Security** 是 Spring 家族中的一个安全管理框架。相比与另外一个安全框架**Shiro**，它提供了更丰富的功能，社区资源也比Shiro丰富。

​ 一般来说中大型的项目都是使用**SpringSecurity** 来做安全框架。小项目有Shiro的比较多，因为相比与SpringSecurity，Shiro的上手更加的简单。

​ 一般Web应用的需要进行**认证**和**授权**。

​ **认证：验证当前访问系统的是不是本系统的用户，并且要确认具体是哪个用户**

​ **授权：经过认证后判断当前用户是否有权限进行某个操作**

​ 而认证和授权也是SpringSecurity作为安全框架的核心功能。


# 依赖引入

我们先创建一个springboot的工程，然后引入springsecurity依赖（这是被springboot版本仲裁的）

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

```

 引入依赖后我们在尝试去访问之前的接口就会自动跳转到一个SpringSecurity的默认登陆页面，默认用户名是user,密码会输出在控制台。

必须登陆之后才能对接口进行访问。

# 认证

## 登陆校验流程
![[Pasted image 20221012090617.png]]

## 原理初探

### SpringSecurity完整流程

SpringSecurity的原理其实就是一个过滤器链，内部包含了提供各种功能的过滤器。这里我们可以看看入门案例中的过滤器。

![[Pasted image 20221012091322.png]]

图中只展示了核心过滤器，其它的非核心过滤器并没有在图中展示。

**UsernamePasswordAuthenticationFilter**:负责处理我们在登陆页面填写了用户名密码后的登陆请求。入门案例的认证工作主要有它负责。

**ExceptionTranslationFilter：** 处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException 。

**FilterSecurityInterceptor：** 负责权限校验的过滤器。


### 认证流程

![[Pasted image 20221012094117.png]]


概念速查:

Authentication接口: 它的实现类，表示当前访问系统的用户，封装了用户相关信息。

AuthenticationManager接口：定义了认证Authentication的方法

UserDetailsService接口：加载用户特定数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。

UserDetails接口：提供核心用户信息。通过UserDetailsService根据用户名获取处理的用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authentication对象中。


## 解决问题

### 思路分析
登录：
- 自定义登录接口
	- 调用ProviderManager的方法进行认证 如果认证通过生成jwt
	- 把用户信息存入redis中
- 自定义UserDetailsService
	- 在这个实现类中去查询数据库

校验：
- 定义Jwt认证过滤器
	- 获取token
	- 解析token获取其中的userid
	- 从redis中获取用户信息
	- 存入SecurityContextHolder

### 准备工作

添加依赖
```xml
<!--redis依赖-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>  
	<groupId>com.alibaba</groupId>  
	<artifactId>fastjson</artifactId>  
	<version>2.0.14</version>  
</dependency>  
<!--jwt依赖-->  
<dependency>  
	<groupId>io.jsonwebtoken</groupId>  
	<artifactId>jjwt</artifactId>  
	<version>0.9.1</version>  
</dependency>

```

设置redis使用fastjson序列化

>[!note] 序列化器
>如果想要使用fastjson官方提供的序列化器而不想自己定义，使用GenericFastJsonRedisSerializer，可以满足大多数需求
>更加具体可以查看fastjson官方文档


```java
package com.haruka.tokendemo.config;  
  
import com.alibaba.fastjson.support.spring.GenericFastJsonRedisSerializer;
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.data.redis.connection.RedisConnectionFactory;  
import org.springframework.data.redis.core.RedisTemplate;  
import org.springframework.data.redis.serializer.StringRedisSerializer;  
  
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

然后还有创建一些工具类，创建响应类ResponseResult，然后就是数据库的代码生成实体类和mapper等等一系列springboot的准备工作

### 实现

#### 数据库校验用户


我们从上方默认的流程图中可以看出来，默认的UserDetailsService的实现类是从内存中读取的用户信息，这肯定不对

我们也要实现UserDetailsService接口，重写其中的方法。用户名从数据库中查询用户信息

```java
@Service  
public class UserDetailsServiceImpl implements UserDetailsService {  
  
    @Autowired  
    private UserMapper userMapper;  
  
    @Override  
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {  
  
        //查询用户信息  
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();  
        queryWrapper.eq(User::getUserName,username);  
        User user = userMapper.selectOne(queryWrapper);  
        //如果没有查询到用户就抛出异常  
        if (Objects.isNull(user)){  
            throw new RuntimeException("用户名或密码错误");  
        }  
        //TODO 查询对应的权限信息  
  
  
        //把数据封装成UserDetails返回  
        return new LoginUser(user);  
    }  
}
```

上方需要返回 UserDetails类，我们也需要实现UserDetails类

>[!note] 调用
>SpringSecruity按照流程会调用UserDetailsService的实现类去获取用户信息，返回一个带有用户信息的UserDetails的实现类，之后获取信息就会调用UserDetails实现类中的方法


UserDetail实现类，修改属性的获取从我们自己user实体
```java
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class LoginUser implements UserDetails {  
    private User user;  
    @Override  
    public Collection<? extends GrantedAuthority> getAuthorities() {  
        return null;  
    }  
  
    @Override  
    public String getPassword() {  
        return user.getPassword();  
    }  
  
    @Override  
    public String getUsername() {  
        return user.getUserName();  
    }  
  
    @Override  
    public boolean isAccountNonExpired() {  
        return true;  
    }  
  
    @Override  
    public boolean isAccountNonLocked() {  
        return true;  
    }  
  
    @Override  
    public boolean isCredentialsNonExpired() {  
        return true;  
    }  
  
    @Override  
    public boolean isEnabled() {  
        return true;  
    }  
}
```

当前阶段如果想要测试的话，我们需要在数据库的密码前加 **{noop}**,表示你是明文的密码(涉及到下一步的密码加密)

#### 密码加密存储
![[Pasted image 20221012183415.png]]

实际项目中我们不会把密码明文存储在数据库中。

默认使用的PasswordEncoder要求数据库中的密码格式为：{id}password 。它会根据id去判断密码的加密方式。但是我们一般不会采用这种方式。所以就需要替换PasswordEncoder。

我们一般使用SpringSecurity为我们提供的BCryptPasswordEncoder。

我们只需要使用把BCryptPasswordEncoder对象注入Spring容器中，SpringSecurity就会使用该PasswordEncoder来进行密码校验。

我们可以定义一个SpringSecurity的配置类，SpringSecurity要求这个配置类要继承WebSecurityConfigurerAdapter。

```JAVA

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

}

```

注意，就算明文相同，但两次加密得出的密文也不相同

```java
@Test  
public void TestBCryptPasswordEncoder(){  
    BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();  
    String password1 = passwordEncoder.encode("wang200301");  
    String password2 = passwordEncoder.encode("wang200301");  
    System.out.println(password1);  
    System.out.println(password2);  
    System.out.println(passwordEncoder.matches("wang200301",
    "$2a$10$rcGVzZP.n7ZjPQhAFXr.U.kZFqNI.C6VfUn7S0nlwksUmZgLQiPS2"));
}

$2a$10$mNCD/FVvNstiUR7nHOhC6ujmmW8J37KqShIIduc5up46DaISGu3YS
$2a$10$rcGVzZP.n7ZjPQhAFXr.U.kZFqNI.C6VfUn7S0nlwksUmZgLQiPS2
true
```

>[!note] 加密方法
>BCryptPasswordEncoder有两个主要方法，encode和matches
>encode是对明文加密成密文，并且每次加密都不一样
>matches是比较明文和密文是否相同
>也就是说，我们知道密文并不能推出明文，只能使用matches方法和明文比较是否相同


在java版本在9及以上版本时，使用jjwt需要额外引入依赖
```xml
<dependency>  
    <groupId>javax.xml.bind</groupId>  
    <artifactId>jaxb-api</artifactId>  
</dependency>
```

在低版本这个jar包是携带的，但高版本的java移除了这个java包


#### 登陆接口

接下我们需要自定义登陆接口，然后让SpringSecurity对这个接口放行,让用户访问这个接口的时候不用登录也能访问。
(毕竟我们总不能我们自定义的登陆接口访问需要密码吧。。。。)

在接口中我们通过AuthenticationManager的authenticate方法来进行用户认证,所以需要在SecurityConfig中配置把AuthenticationManager注入容器。

认证成功的话要生成一个jwt，放入响应中返回。并且为了让用户下回请求时能通过jwt识别出具体的是哪个用户，我们需要把用户信息存入redis，可以把用户id作为key。

登陆接口的controller 和serviceImpl

```java
  
@RestController  
public class LoginController {  
  
    @Autowired  
    private LoginService loginService;  
  
    @PostMapping("/user/login")  
    public ResponseResult login(@RequestBody User user){  
        //登陆  
        return loginService.login(user);  
    }  
}
```

```java
  
@Service  
public class LoginServiceImpl implements LoginService {  
  
    @Autowired  
    private AuthenticationManager authenticationManager;  
  
    @Autowired  
    private RedisCache redisCache;  
  
    @Override  
    public ResponseResult login(User user) {  
        //AuthenticationManager authenticate 进行用户认证  
  
        //接口需要调用authenticationManager去进行下一步实现  
        //根据流程图,默认的filter将name和password封装为authentication类然后传给authenticationManager  
        //我们自定义的接口也需要传这个类，这里使用authentication的其中一个实现类UsernamePasswordAuthenticationToken  
  
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(user.getUserName(),user.getPassword());  
  
        //这个方法就是执行下面的流程，去数据库查询用户，并进行密码认证  
        Authentication authenticate = authenticationManager.authenticate(authenticationToken);  
  
        //如果认证没通过，给出对应提示  
        if (Objects.isNull(authenticate)){  
            throw  new RuntimeException("登陆失败");  
        }  
  
        //如果认证通过了，使用userid生成一个jwt  
  
        //通过debug可以看到上方返回的authenticate的Principal就是UserDetails的实现类LoginUser，所以我们对其强转  
        LoginUser loginUser = (LoginUser)authenticate.getPrincipal();  
  
        String userId = loginUser.getUser().getId().toString();  
  
        String jwt = JwtUtil.createJWT(userId);  
        //把完整的用户信息存入redis,userid作为key  
  
        redisCache.setCacheObject("login:"+userId,loginUser);  
  
        //将生成的jwt作为token返回给前端  
        Map<String,String> map = new HashMap<>();  
        map.put("token",jwt);  
        return new ResponseResult<>(200,"登陆成功",map);  
    }  
}
```


>[!note] 几个注意点
>我们需要在配置类暴露AuthenticationManager这个bean，并且将登陆的接口放行
>jwt的密钥不能有`"-"和"_"`,否则会报错


配置类设置(增加两个操作)
```java
@Configuration  
public class SecurityConfig extends WebSecurityConfigurerAdapter {  
  
  
    @Bean  
    public PasswordEncoder passwordEncoder(){  
        return new BCryptPasswordEncoder();  
    }  
  
    //将AuthenticationManager暴露  
    @Bean  
    @Override    public AuthenticationManager authenticationManagerBean() throws Exception {  
        return super.authenticationManagerBean();  
    }  
  
    //配置将登陆接口放行  
    @Override  
    protected void configure(HttpSecurity http) throws Exception {  
        http  
                //关闭csrf  
                .csrf().disable()  
                //不通过Session获取SecurityContext  
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)  
                .and()  
                .authorizeRequests()  
                // 对于登录接口 允许匿名访问  
                .antMatchers("/user/login").anonymous()  
                // 除上面外的所有请求全部需要鉴权认证  
                .anyRequest().authenticated();  
    }  
}
```


#### 认证过滤器
我们需要自定义一个过滤器，这个过滤器会去获取请求头中的token，对token进行解析取出其中的userid。

使用userid去redis中获取对应的LoginUser对象。

然后封装Authentication对象存入SecurityContextHolder

>[!note] 过滤器和拦截器的区别
>过滤器：对请求在进入后Servlet之前或之后进行处理。
>拦截器：对请求在handler【Controller】前后进行处理。
>![[Pasted image 20221013074341.png]]


>[!note] Filter接口问题
>不同的servlet中，过滤器可能会调用多次，所以我们使用spring提供给我们的Filter 的一个实现类OncePerRequestFilter
>确保Filter之只调用一次

>[!note] 用户信息需要存入SecurityContextHolder
>过滤器链的FilterSecurityInterceptor是去进行认证的，他的信息获取就是从SecurityContextHolder中进行获取，来看你是否认证通过，所以我们需要存入


过滤器代码
```java
@Component  
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {  
  
    @Autowired  
    public RedisCache redisCache;  
  
    @Override  
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {  
        //获取token  
        String token = request.getHeader("token");  
        if (!StringUtils.hasText(token)) {  
            //放行，我们不需要进行解析，直接让其进入信息获取  
            filterChain.doFilter(request, response);  
  
            //这里必须加return,因为我们放行后调用api完成会回来执行过滤器链接下来的方法  
            return;  
        }  
        //解析token  
        String userId;  
        try {  
            Claims claims = JwtUtil.parseJWT(token);  
            userId = claims.getSubject();  
  
        } catch (Exception e) {  
            throw new RuntimeException("token非法");  
        }  
        //从redis中获取用户信息  
        String redisKey = "login:" + userId;  
        LoginUser loginUser = redisCache.getCacheObject(redisKey);  
        if (Objects.isNull(loginUser)){  
            throw new RuntimeException("用户未登陆");  
        }  
        //存入SecurityContextHolder  
  
        //这里需要用三个参数，因为三个参数会将其中一个是否认证的属性定义为true，表示我们这是一个已认证的请求  
        //TODO 获取权限信息，封装到Authentication  
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(loginUser, null, null);  
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);  
  
        //放行  
        filterChain.doFilter(request,response);  
    }  
}
```

我们需要将这个过滤器放到springSecurity的过滤器链中

这个在springSecurity的配置类中进行设置

```java
@Override  
protected void configure(HttpSecurity http) throws Exception {  
    //配置将登陆接口放行  
    http  
            //关闭csrf  
            .csrf().disable()  
            //不通过Session获取SecurityContext  
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)  
            .and()  
            .authorizeRequests()  
            // 对于登录接口 允许匿名访问  
            .antMatchers("/user/login").anonymous()  
            // 除上面外的所有请求全部需要鉴权认证  
            .anyRequest().authenticated();  
    //将我们的自定义过滤器添加到UsernamePasswordAuthenticationFilter这个过滤器之前  
    http  
            .addFilterBefore(jwtAuthenticationTokenFilter,UsernamePasswordAuthenticationFilter.class);  
}
```

#### 退出登录
我们只需要定义一个登陆接口，然后获取SecurityContextHolder中的认证信息，删除redis中对应的数据即可。

```java
 @Override
    public ResponseResult logout() {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        LoginUser loginUser = (LoginUser) authentication.getPrincipal();
        Long userid = loginUser.getUser().getId();
        redisCache.deleteObject("login:"+userid);
        return new ResponseResult(200,"退出成功");
    }
```

# 授权
## 权限系统的作用
例如一个学校图书馆的管理系统，如果是普通学生登录就能看到借书还书相关的功能，不可能让他看到并且去使用添加书籍信息，删除书籍信息等功能。但是如果是一个图书馆管理员的账号登录了，应该就能看到并使用添加书籍信息，删除书籍信息等功能。

总结起来就是不同的用户可以使用不同的功能。这就是权限系统要去实现的效果。

我们不能只依赖前端去判断用户的权限来选择显示哪些菜单哪些按钮。因为如果只是这样，如果有人知道了对应功能的接口地址就可以不通过前端，直接去发送请求来实现相关功能操作。

所以我们还需要在后台进行用户权限的判断，判断当前用户是否有相应的权限，必须具有所需权限才能进行相应的操作。

## 授权基本流程

在SpringSecurity中，会使用默认的FilterSecurityInterceptor来进行权限校验。在FilterSecurityInterceptor中会从SecurityContextHolder获取其中的Authentication，然后获取其中的权限信息。当前用户是否拥有访问当前资源所需的权限。

所以我们在项目中只需要把当前登录用户的权限信息也存入Authentication。

然后设置我们的资源所需要的权限即可。

## 授权实现

### 限制访问资源所需权限
 SpringSecurity为我们提供了基于注解的权限控制方案，这也是我们项目中主要采用的方式。我们可以使用注解去指定访问对应的资源所需的权限。

 但是要使用它我们需要先开启相关配置。
 在配置类上添加，开启功能
```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```

然后就可以使用对应的注解 @PreAuthorize

```java
@RestController  
public class HelloController {  
    @RequestMapping("hello")  
    @PreAuthorize("hasAuthority('test')")  
    public String hello (){  
        return "hello";  
    }  
}
```

### 封装权限信息

我们前面在写UserDetailsServiceImpl的时候说过，在查询出用户后还要获取对应的权限信息，封装到UserDetails中返回。

我们先直接把权限信息写死封装到UserDetails中进行测试。

我们之前定义了UserDetails的实现类LoginUser，想要让其能封装权限信息就要对其进行修改。

```java
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class LoginUser implements UserDetails {  
    private User user;  
  
    private List<String> permissions;  
  
    @JSONField(serialize = false)  //这个注解表示不会序列化到redis中，为了安全考虑  
    private List<GrantedAuthority> authorities;  
  
    public LoginUser (User user,List<String> permissions){  
        this.user = user;  
        this.permissions = permissions;  
    }  
  
    @Override  
    public Collection<? extends GrantedAuthority> getAuthorities() {  
  
        if (authorities!=null){  
            return authorities;  
        }  
        this.authorities = new ArrayList<>();  
        //把permissions中String类型的权限类型封装成实现类  
        for (String permission : permissions) {  
            SimpleGrantedAuthority authority = new SimpleGrantedAuthority(permission);  
            authorities.add(authority);  
        }  
        return authorities;  
    }  
  
    @Override  
    public String getPassword() {  
        return user.getPassword();  
    }  
  
    @Override  
    public String getUsername() {  
        return user.getUserName();  
    }  
  
    @Override  
    public boolean isAccountNonExpired() {  
        return true;  
    }  
  
    @Override  
    public boolean isAccountNonLocked() {  
        return true;  
    }  
  
    @Override  
    public boolean isCredentialsNonExpired() {  
        return true;  
    }  
  
    @Override  
    public boolean isEnabled() {  
        return true;  
    }  
}
```

LoginUser修改完后我们就可以在UserDetailsServiceImpl中去把权限信息封装到LoginUser中了。我们写死权限进行测试，后面我们再从数据库中查询权限信息。

```java
  
@Service  
public class UserDetailsServiceImpl implements UserDetailsService {  
  
    @Autowired  
    private UserMapper userMapper;  
  
    @Override  
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {  
  
        //查询用户信息  
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();  
        queryWrapper.eq(User::getUserName,username);  
        User user = userMapper.selectOne(queryWrapper);  
        //如果没有查询到用户就抛出异常  
        if (Objects.isNull(user)){  
            throw new RuntimeException("用户名或密码错误");  
        }  
        //TODO 查询对应的权限信息  
        List<String> list = new ArrayList<>(Arrays.asList("test","admin"));  
  
        //把数据封装成UserDetails返回  
        return new LoginUser(user,list);  
    }  
}
```


### 从数据库查询权限信息

#### RBAC权限模型
RBAC权限模型（Role-Based Access Control）即：基于角色的权限控制。这是目前最常被开发者使用也是相对易用、通用权限模型
![[Pasted image 20221013144409.png]]


我们创建上面的5张对应的表，然后进行多表联查，查出用户对应的权限
sql语句如下

```sql
SELECT 
	DISTINCT m.`perms`
FROM
	sys_user_role ur
	LEFT JOIN `sys_role` r ON ur.`role_id` = r.`id`
	LEFT JOIN `sys_role_menu` rm ON ur.`role_id` = rm.`role_id`
	LEFT JOIN `sys_menu` m ON m.`id` = rm.`menu_id`
WHERE
	user_id = 2
	AND r.`status` = 0
	AND m.`status` = 0
```

我们使用mybatis随便创建一个表的实体和mapper，因为是多表联查，所以mybatis-plus自带的方法没办法用，我们需要自己写xml，这也是随便的原因

mapper中定义方法

在xml中写语句、
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sangeng.mapper.MenuMapper">
 
 
    <select id="selectPermsByUserId" resultType="java.lang.String">
        SELECT
            DISTINCT m.`perms`
        FROM
            sys_user_role ur
            LEFT JOIN `sys_role` r ON ur.`role_id` = r.`id`
            LEFT JOIN `sys_role_menu` rm ON ur.`role_id` = rm.`role_id`
            LEFT JOIN `sys_menu` m ON m.`id` = rm.`menu_id`
        WHERE
            user_id = #{userid}
            AND r.`status` = 0
            AND m.`status` = 0
    </select>
</mapper>
```


我们查询时直接使用mapper的selectPermsByUserId方法直接查询就可以了

# 自定义失败处理

我们还希望在认证失败或者是授权失败的情况下也能和我们的接口一样返回相同结构的json，这样可以让前端能对响应进行统一的处理。要实现这个功能我们需要知道SpringSecurity的异常处理机制。

在SpringSecurity中，如果我们在认证或者授权的过程中出现了异常会被ExceptionTranslationFilter捕获到。在ExceptionTranslationFilter中会去判断是认证失败还是授权失败出现的异常。

如果是认证过程中出现的异常会被封装成AuthenticationException然后调用**AuthenticationEntryPoint** 对象的方法去进行异常处理。

如果是授权过程中出现的异常会被封装成AccessDeniedException然后调用**AccessDeniedHandler**对象的方法去进行异常处理。

所以如果我们需要自定义异常处理，我们只需要自定义AuthenticationEntryPoint和AccessDeniedHandler然后配置给SpringSecurity即可。

自定义实现类

```java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.FORBIDDEN.value(), "权限不足");
        String json = JSON.toJSONString(result);
        WebUtils.renderString(response,json);
 
    }
}
 
```

```java
 
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.UNAUTHORIZED.value(), "认证失败请重新登录");
        String json = JSON.toJSONString(result);
        WebUtils.renderString(response,json);
    }
}
 
```

将其配置给SpringSecurity

```java
    @Autowired
    private AuthenticationEntryPoint authenticationEntryPoint;
 
    @Autowired
    private AccessDeniedHandler accessDeniedHandler;
<!--~~~~
 
	然后我们可以使用HttpSecurity对象的方法去配置。
 
~~~~java-->
        http.exceptionHandling().authenticationEntryPoint(authenticationEntryPoint).
                accessDeniedHandler(accessDeniedHandler);
```


>[!note] 异常处理
>springSecurity内部抛出的异常都不会被springboot的全局异常捕获到，会被springSecurity的异常处理filter捕获到
>所以我们才需要配置springSecurity的异常处理方法
>不然其默认的方法只会返回一个错误，没有内容


# 跨域
浏览器出于安全的考虑，使用 XMLHttpRequest对象发起 HTTP请求时必须遵守同源策略，否则就是跨域的HTTP请求，默认情况下是被禁止的。 同源策略要求源相同才能正常进行通信，即协议、域名、端口号都完全一致。

前后端分离项目，前端项目和后端项目一般都不是同源的，所以肯定会存在跨域请求的问题。

所以我们就要处理一下，让前端能进行跨域请求。

先对SpringBoot配置，运行跨域请求
这里使用重写WebMvcConfigurer的方式

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
 
    @Override
    public void addCorsMappings(CorsRegistry registry) {
      // 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的header属性
                .allowedHeaders("*")
                // 跨域允许时间
                .maxAge(3600);
    }
}
```

开启SpringSecurity的跨域访问

由于我们的资源都会收到SpringSecurity的保护，所以想要跨域访问还要让SpringSecurity运行跨域访问。

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();
 
        //添加过滤器
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
 
        //配置异常处理器
        http.exceptionHandling()
                //配置认证失败处理器
                .authenticationEntryPoint(authenticationEntryPoint)
                .accessDeniedHandler(accessDeniedHandler);
 
        //允许跨域
        http.cors();
    }
 
```

# 遗留小问题

## 其他权限校验方法

我们前面都是使用@PreAuthorize注解，然后在在其中使用的是hasAuthority方法进行校验。SpringSecurity还为我们提供了其它方法例如：hasAnyAuthority，hasRole，hasAnyRole等。

这里我们先不急着去介绍这些方法，我们先去理解hasAuthority的原理，然后再去学习其他方法你就更容易理解，而不是死记硬背区别。并且我们也可以选择定义校验方法，实现我们自己的校验逻辑。

hasAuthority方法实际是执行到了SecurityExpressionRoot的hasAuthority，大家只要断点调试既可知道它内部的校验原理。

它内部其实是调用authentication的getAuthorities方法获取用户的权限列表。然后判断我们存入的方法参数数据在权限列表中。

hasAnyAuthority方法可以传入多个权限，只有用户有其中任意一个权限都可以访问对应资源

```java
    @PreAuthorize("hasAnyAuthority('admin','test','system:dept:list')")
    public String hello(){
        return "hello";
    }
```


hasRole要求有对应的角色才可以访问，但是它内部会把我们传入的参数拼接上 **ROLE_** 后再去比较。所以这种情况下要用用户对应的权限也要有 **ROLE_** 这个前缀才可以。

hasAnyRole 有任意的角色就可以访问。它内部也会把我们传入的参数拼接上 **ROLE_** 后再去比较。所以这种情况下要用用户对应的权限也要有 **ROLE_** 这个前缀才可以。

## 自定义权限校验方法

ex就代表这个bean的名字就为ex
```java
@Component("ex")
public class SGExpressionRoot {
 
    public boolean hasAuthority(String authority){
        //获取当前用户的权限
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        LoginUser loginUser = (LoginUser) authentication.getPrincipal();
        List<String> permissions = loginUser.getPermissions();
        //判断用户权限集合中是否存在authority
        return permissions.contains(authority);
    }
}
```

 在SPEL表达式中使用 @ex相当于获取容器中bean的名字为ex的对象。然后再调用这个对象的hasAuthority方法

```java
    @RequestMapping("/hello")
    @PreAuthorize("@ex.hasAuthority('system:dept:list')")
    public String hello(){
        return "hello";
    }
```

## 基于配置的权限控制

我们前边主要的权限控制都是使用注解的

我们也可以在配置类中使用使用配置的方式对资源进行权限控制。

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                .antMatchers("/testCors").hasAuthority("system:dept:list222")
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();
 
        //添加过滤器
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
 
        //配置异常处理器
        http.exceptionHandling()
                //配置认证失败处理器
                .authenticationEntryPoint(authenticationEntryPoint)
                .accessDeniedHandler(accessDeniedHandler);
 
        //允许跨域
        http.cors();
    }
```

## CSRF
CSRF是指跨站请求伪造（Cross-site request forgery），是web常见的攻击之一。

https://blog.csdn.net/freeking101/article/details/86537087

SpringSecurity去防止CSRF攻击的方式就是通过csrf_token。后端会生成一个csrf_token，前端发起请求的时候需要携带这个csrf_token,后端会有过滤器进行校验，如果没有携带或者是伪造的就不允许访问。

我们可以发现CSRF攻击依靠的是cookie中所携带的认证信息。但是在前后端分离的项目中我们的认证信息其实是token，而token并不是存储中cookie中，并且需要前端代码去把token设置到请求头中才可以，所以CSRF攻击也就不用担心了。


## 认证成功处理器

实际上在UsernamePasswordAuthenticationFilter进行登录认证的时候，如果登录成功了是会调用AuthenticationSuccessHandler的方法进行认证成功后的处理的。AuthenticationSuccessHandler就是登录成功处理器。

我们也可以自己去自定义成功处理器进行成功后的相应处理。

这个登陆成功处理器就是一种简单的方案
>[!note] 注意
>我们使用我们前面的token方案时，配置的过滤器和配置文件就会直接消除这个UsernamePasswordAuthenticationFilter
>这个UsernamePasswordAuthenticationFilter的方案只是一种思路

直接引入简单的依赖

配置文件
```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
 
    @Autowired
    private AuthenticationSuccessHandler successHandler;
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin().successHandler(successHandler);
 
        http.authorizeRequests().anyRequest().authenticated();
    }
}
 
```

成功处理器
```java
@Component
public class SGSuccessHandler implements AuthenticationSuccessHandler {
 
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        System.out.println("认证成功了");
    }
}
 
```

## 认证失败处理器

同样，我们使用这种简单方案进行认证时，也会有认证失败处理器

实际上在UsernamePasswordAuthenticationFilter进行登录认证的时候，如果认证失败了是会调用AuthenticationFailureHandler的方法进行认证失败后的处理的。AuthenticationFailureHandler就是登录失败处理器。

我们也可以自己去自定义失败处理器进行失败后的相应处理。

处理器
```java
@Component
public class SGFailureHandler implements AuthenticationFailureHandler {
    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
        System.out.println("认证失败了");
    }
}
```

配置文件

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
 
    @Autowired
    private AuthenticationSuccessHandler successHandler;
 
    @Autowired
    private AuthenticationFailureHandler failureHandler;
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
//                配置认证成功处理器
                .successHandler(successHandler)
//                配置认证失败处理器
                .failureHandler(failureHandler);
 
        http.authorizeRequests().anyRequest().authenticated();
    }
}
 
```

## 登出成功处理器

处理器
```java
@Component
public class SGLogoutSuccessHandler implements LogoutSuccessHandler {
    @Override
    public void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        System.out.println("注销成功");
    }
}
 
```

配置文件
```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
 
    @Autowired
    private AuthenticationSuccessHandler successHandler;
 
    @Autowired
    private AuthenticationFailureHandler failureHandler;
 
    @Autowired
    private LogoutSuccessHandler logoutSuccessHandler;
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()
//                配置认证成功处理器
                .successHandler(successHandler)
//                配置认证失败处理器
                .failureHandler(failureHandler);
 
        http.logout()
                //配置注销成功处理器
                .logoutSuccessHandler(logoutSuccessHandler);
 
        http.authorizeRequests().anyRequest().authenticated();
    }
}
```