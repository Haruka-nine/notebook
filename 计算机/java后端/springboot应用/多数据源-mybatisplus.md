
# 依赖引入
```xml
<dependency>  
<groupId>com.baomidou</groupId>  
<artifactId>mybatis-plus-boot-starter</artifactId>  
<version>3.5.1</version>  
</dependency>  
<dependency>  
<groupId>com.mysql</groupId>  
<artifactId>mysql-connector-j</artifactId>  
</dependency>  
<dependency>  
<groupId>com.baomidou</groupId>  
<artifactId>dynamic-datasource-spring-boot-starter</artifactId>  
<version>3.5.0</version>  
</dependency>
```

这里以mysql为例，引入mysql的驱动

# 配置文件
```yml
#配置端口  
server:  
	port: 8080  
spring:  
	#配置数据源  
	datasource:   
	#动态多数据源  
		dynamic:  
		# 设置默认的数据源或者数据源组,默认值即为master  
			primary: primary  
			# 严格匹配数据源,默认false.true未匹配到指定数据源时抛异常,false使用默认数据源 
			strict: false  
			datasource:  
				primary:  
					url: jdbc:mysql://rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_primary?characterEncoding=utf-8&useSSL=false  
					driver-class-name: com.mysql.cj.jdbc.Driver  
					username: haruka_nine  
					password: Wang200301  
				second:  
					url: jdbc:mysql://rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_second?characterEncoding=utf-8&useSSL=false  
					driver-class-name: com.mysql.cj.jdbc.Driver  
					username: haruka_nine  
					password: Wang200301
```


# 添加druid连接池

## 增加依赖
```xml
<!-- druid数据库链接池-->  
<dependency>  
<groupId>com.alibaba</groupId>  
<artifactId>druid-spring-boot-starter</artifactId>  
<version>1.1.20</version>  
</dependency>
```

## 配置文件增加

```yml
#配置端口  
server:  
	port: 8080  
spring:  
	#配置数据源  
	datasource:   
	#动态多数据源  
		dynamic:  
		# 设置默认的数据源或者数据源组,默认值即为master  
			primary: primary  
			# 严格匹配数据源,默认false.true未匹配到指定数据源时抛异常,false使用默认数据源 
			strict: false  
			datasource:  
				primary:  
					url: jdbc:mysql://rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_primary?characterEncoding=utf-8&useSSL=false  
					driver-class-name: com.mysql.cj.jdbc.Driver  
					username: haruka_nine  
					password: Wang200301  
				second:  
					url: jdbc:mysql://rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_second?characterEncoding=utf-8&useSSL=false  
					driver-class-name: com.mysql.cj.jdbc.Driver  
					username: haruka_nine  
					password: Wang200301
		### 连接池配置  
		druid:  
			stat-view-servlet: # 配置监控页功能  
				enabled: true  
				login-username: haruka  
				login-password: wang200301  
				resetEnable: false  
			web-stat-filter: # 监控web  
				enabled: true  
				urlPattern: /*  
				exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'  
			initial-size: 50  
			max-active: 200  
			min-idle: 50  
			max-wait: 50  
			validation-query: SELECT 1

```


# 使用

如果对mapper不添加任何改变，就会使用默认配置源

想要使用其他配置源时，使用`@DS`标签

```java
@DS("second")  
public interface UserSecondMapper extends BaseMapper<User> {  
  
}
```

