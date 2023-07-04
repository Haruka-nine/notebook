
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
  port: 8080  
spring:  
  #配置数据源  
  datasource:  
    #配置数据源类型  
     #单独数据源  
#    url: jdbc:mysql://![](file:///C:\Users\20527\AppData\Roaming\Tencent\QQTempSys\[5UQ[BL(6~BS2JV6W}N6[%S.png)rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_primary?characterEncoding=utf-8&useSSL=false  
#    driver-class-name: com.mysql.cj.jdbc.Driver  
#    username: haruka_nine  
#    password: Wang200301  
    #动态多数据源  
    dynamic:  
      # 设置默认的数据源或者数据源组,默认值即为master  
      primary: primary  
      # 严格匹配数据源,默认false.true未匹配到指定数据源时抛异常,false使用默认数据源  
      strict: false  
      datasource:  
        primary:  
          url: jdbc:mysql://![](file:///C:\Users\20527\AppData\Roaming\Tencent\QQTempSys\[5UQ[BL(6~BS2JV6W}N6[%S.png)rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_primary?characterEncoding=utf-8&useSSL=false  
          driver-class-name: com.mysql.cj.jdbc.Driver  
          username: haruka_nine  
          password: Wang200301  
          druid:  
            initial-size: 5  
            max-active: 20  
            min-idle: 5  
            max-wait: 5000  
            validation-query: SELECT 1  
            #这里的日志如果使用log4j会使用log4j的日志配置  
            filters: stat,slf4j,wall  
            stat:  
              #慢sql配置，如果一个sql超过下方配置的时间，就会生成Error日志，使用的是springboot的日志配置  
              log-slow-sql: true  
              db-type: mysql  
              slow-sql-millis: 2000  
        second:  
          url: jdbc:mysql://![](file:///C:\Users\20527\AppData\Roaming\Tencent\QQTempSys\[5UQ[BL(6~BS2JV6W}N6[%S.png)rm-cn-x0r3a6rao0007a2o.rwlb.rds.aliyuncs.com:3306/haruka_second?characterEncoding=utf-8&useSSL=false  
          driver-class-name: com.mysql.cj.jdbc.Driver  
          username: haruka_nine  
          password: Wang200301  
          druid:  
            initial-size: 5  
            max-active: 20  
            min-idle: 5  
            max-wait: 5000  
            validation-query: SELECT 1  
            filters: stat,slf4j,wall  
            stat:  
              log-slow-sql: true  
              db-type: mysql  
              slow-sql-millis: 2000  
  
    ### 连接池配置  
    druid:  
      filter:  
        slf4j:  
          enabled: true  
          statement-create-after-log-enabled: false  
          statement-close-after-log-enabled: false  
          result-set-close-after-log-enabled: false  
          result-set-open-after-log-enabled: false  
      stat-view-servlet: # 开启德鲁伊的监控页功能  
        enabled: true  
        login-username: haruka  
        login-password: wang200301  
        resetEnable: false  
        # IP白名单 (没有配置或者为空，则允许所有访问)  
        #allow: 127.0.0.1,![](file:///C:\Users\20527\AppData\Roaming\Tencent\QQ\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)192.168.163.1  
        # IP黑名单 (存在共同时，deny优先于allow)  
        #deny: ![](file:///C:\Users\20527\AppData\Roaming\Tencent\QQ\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)192.168.10.1  
      web-stat-filter: # 监控web  
        enabled: true  
        urlPattern: /*  
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*' #不统计这些请求数据  
  
  
  redis:  
    # 地址  
    host: ![](file:///C:\Users\20527\AppData\Roaming\Tencent\QQTempSys\[5UQ[BL(6~BS2JV6W}N6[%S.png)r-bp1o0l04d5ytwt4bsupd.redis.rds.aliyuncs.com  
    # 端口，默认为6379  
    port: 6379  
    # 数据库索引  
    database: 0  
    # 密码  
    password: haruka_nine:Wang200301  
    # 连接超时时间  
    timeout: 10s  
    lettuce:  
      pool:  
        # 连接池中的最小空闲连接  
        min-idle: 0  
        # 连接池中的最大空闲连接  
        max-idle: 8  
        # 连接池的最大数据库连接数  
        max-active: 8  
        # #连接池最大阻塞等待时间（使用负值表示没有限制）  
        max-wait: -1ms  
  
#MyBatis-Plus相关配置  
mybatis-plus:  
  global-config:  
    db-config:  
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)  
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)  
  #扫描mapper接口的xml文件路径  
  mapper-locations: classpath:com/haruka/mapper/xml/*.xml  
  configuration:  
    #配置日志  
    ### 开启打印sql配置  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
logging:  
  file:  
    path: logs
```


# 使用

如果对mapper不添加任何改变，就会使用默认配置源

想要使用其他配置源时，使用`@DS`标签

```java
@DS("second")  
public interface UserSecondMapper extends BaseMapper<User> {  
  
}
```

在mapper和service中加都可以，然后方法和类中加都可以，方法的优先级高于类