# 简介

Apache Log4j2 是对Log4j 的升级版本，参考了logback 的一些优秀的设计，并且修复了一些问题，因此带来了一些重大的提升，主要有：

- 异常处理，在logback中，Appender中的异常不会被应用感知到，但是在log4j2中，提供了一些异常处理机制。

- 性能提升，log4j2 相较于log4j 和 logback 都具有明显的性能提升，有18倍性能提升，后面会有官方测试的数据。

- 自动重载配置，参考了logback的设计，当然会提供自动刷新参数配置，最实用的就是我们在生产上可以动态的修改日志的级别而不需要重启应用。

- 无垃圾机制，log4j2 在大部分情况下，都可以使用其设计的一套无垃圾机制【对象重用、内存缓冲】，避免频繁的日志收集导致的 jvm gc。


官网：[https://logging.apache.org/log4j/2.x/](https://logging.apache.org/log4j/2.x/)


# 导入

 

> log4j2既可以作为日志实现，也可以作为日志门面来使用，不过在平时开发中，大家都习惯于将log4j2作为日志实现，slf4j作为日志门面来结合使用。





```XML

        <!-- log4j2 日志门面 -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>2.11.1</version>
        </dependency>
        <!-- log4j2 日志实面 -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.11.1</version>

```



```XML
        <!-- 使用slf4j 作为日志门面 -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.26</version>
        </dependency>
        <!-- 使用 log4j2 的适配器进行绑定 -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-slf4j-impl</artifactId>
            <version>2.9.1</version>
        </dependency>

```



```XML
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <!--去掉-->
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
```



# 使用



一般使用

```Java
public class Slf4jTest {
 
    // 为了保证使用时，不需要每次都去创建logger 对象，我们声明静态常量
    public static final Logger LOGGER = LoggerFactory.getLogger(Slf4jTest.class);
 
    LOGGER.info("message");

}
```



正常使用，通过lombok化简

```Java
@Slf4j
public class Slf4jTest {
    log.info("message");
}
```



# 配置文件

这里是我自己的配置文件，springBoot中可以彩色输出，并且会把日志分级别保存在项目根目录的log文件夹

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval="5" 自动加载配置文件的间隔时间，不低于 5秒；生产环境中修改配置文件，是热更新，无需重启应用-->
<configuration status="OFF" monitorInterval="5">
    <appenders>

        <Console name="Console" target="SYSTEM_OUT">
            <!--只接受程序中INFO级别的日志进行处理-->
            <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${PID:-} [%15.15t] %-40.40logger{39} : %m%n"/>
        </Console>

        <!--处理DEBUG级别的日志，并把该日志放到logs/debug.log文件中-->
        <!--打印出DEBUG级别日志，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileDebug" fileName="./logs/debug.log"
                     filePattern="logs/$${date:yyyy-MM}/debug-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="DEBUG"/>
                <ThresholdFilter level="INFO" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <!--按照时间差分，正式上线可以开启，开发没必要开启-->
<!--                <TimeBasedTriggeringPolicy/>-->
            </Policies>
        </RollingFile>

        <!--处理INFO级别的日志，并把该日志放到logs/info.log文件中-->
        <RollingFile name="RollingFileInfo" fileName="./logs/info.log"
                     filePattern="logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <!--只接受INFO级别的日志，其余的全部拒绝处理-->
                <ThresholdFilter level="INFO"/>
                <ThresholdFilter level="WARN" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <!--按照时间差分，正式上线可以开启，开发没必要开启-->
<!--                <TimeBasedTriggeringPolicy/>-->
            </Policies>
        </RollingFile>

        <!--处理WARN级别的日志，并把该日志放到logs/warn.log文件中-->
        <RollingFile name="RollingFileWarn" fileName="./logs/warn.log"
                     filePattern="logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="WARN"/>
                <ThresholdFilter level="ERROR" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>

        <!--处理error级别的日志，并把该日志放到logs/error.log文件中-->
        <RollingFile name="RollingFileError" fileName="./logs/error.log"
                     filePattern="logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log.gz">
            <ThresholdFilter level="ERROR"/>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <!--按照时间差分，正式上线可以开启，开发没必要开启-->
<!--                <TimeBasedTriggeringPolicy/>-->
            </Policies>
        </RollingFile>

        <!--druid的日志记录追加器-->
        <RollingFile name="druidSqlRollingFile" fileName="./logs/druid-sql.log"
                     filePattern="logs/$${date:yyyy-MM}/api-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <!--按照时间差分，正式上线可以开启，开发没必要开启-->
<!--                <TimeBasedTriggeringPolicy/>-->
            </Policies>
        </RollingFile>
    </appenders>

    <loggers>
        <root level="DEBUG">
            <appender-ref ref="Console"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
            <appender-ref ref="RollingFileDebug"/>
        </root>

        <!--记录druid-sql的记录-->
        <logger name="druid.sql.Statement" level="debug" additivity="false">
            <appender-ref ref="druidSqlRollingFile"/>
        </logger>

        <!--log4j2 自带过滤日志-->
        <Logger name="org.apache.catalina.startup.DigesterFactory" level="error" />
        <Logger name="org.apache.catalina.util.LifecycleBase" level="error" />
        <Logger name="org.apache.coyote.http11.Http11NioProtocol" level="warn" />
        <logger name="org.apache.sshd.common.util.SecurityUtils" level="warn"/>
        <Logger name="org.apache.tomcat.util.net.NioSelectorPool" level="warn" />
        <Logger name="org.crsh.plugin" level="warn" />
        <logger name="org.crsh.ssh" level="warn"/>
        <Logger name="org.eclipse.jetty.util.component.AbstractLifeCycle" level="error" />
        <Logger name="org.hibernate.validator.internal.util.Version" level="warn" />
        <logger name="org.springframework.boot.actuate.autoconfigure.CrshAutoConfiguration" level="warn"/>
        <logger name="org.springframework.boot.actuate.endpoint.jmx" level="warn"/>
        <logger name="org.thymeleaf" level="warn"/>
    </loggers>
</configuration>
```



# 异步日志

Log4j2提供了两种实现日志的方式，一个是AsyncAppender，一个是通过AsyncLogger

使用异步日志首先要引入异步的依赖



```XML
        <!--异步日志依赖 -->
        <dependency>
            <groupId>com.lmax</groupId>
            <artifactId>disruptor</artifactId>
            <version>3.3.4</version>
        </dependency>
```

##  **AsyncAppender**

**AsyncAppender：**这种使用方式较为简单，只需要在我们上述的Appender中加入以下标签即可：

```XML
<Async name = "Async">
    <AppenderRef ref = "file"/>
</Async>
```



## AsyncLogger

这种异步方式是官方推荐的异步日志方式，它可以使得Logger.log返回的更快。有两种选择：全局异步和混合异步

全局异步：所有日志都进行异步记录，在配置文件上不用做任何改动，只需要添加`log4j2.component.properties`配置：

```XML
Log4jContextSelector = org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
```

局部异步：我们可以根据项目的实际情况将日志分为异步输出是同步输出。

```XML
    <!--logger定义-->
    <Loggers>
        <!--自定义异步logger对象
        includeLocation = "false" 关闭日志记录行好信息
        additivity = "false" 不再继承rootlogger对象-->
        <AsyncLogger name = "com.liubujun" level = "trace" includeLocation = "false" additivity = "false">
            <AppenderRef ref="Console"/>
        </AsyncLogger>
 
        <Root level = "trace">
            <AppenderRef ref  ="Console"/>
        </Root>
    </Loggers>
```

将以上配置配置到我们之前的log4j2.xml配置文件中，将之前的logger定义替换。此时我们com.liubujun日志时异步的，root日志是同步的。



使用异步日志需要注意的问题：

- 如果使用异步日志，AsyncAppender、AsyncLogger 和全局日志，不要同时出现。性能会和AsyncAppender 一致，降至最低。

- 设置 includeLocation=false，打印位置信息会急剧降低异步日志的性能，比同步日志还要慢。

- 在配置文件的日志格式中配置位置信息（ %C or %class, %F or %file, %l or %location, %L or %line, %M or %method）后，同步日志可以直接打印位置信息，而异步日志则必须配置includeLocation="true"才能打印位置信息。同时打印位置信息会产生很大的开销，尤其对于异步日志的性能影响很大。

# 无垃圾记录


垃圾收集暂停是延迟峰值的常见原因，并且对于许多系统而言，花费大量精力来控制这些暂停。许多日志库在稳定日志记录器期间分配临时对象，如日志事件对象，字符串，字符数组，字节数组。这会对垃圾收集器造成压力并增加GC暂停发生的频率。

从版本2.6开始，默认情况下Log4j以“无垃圾模式”运行，其中重用对象和缓冲区，并且尽可能的不分配临时对象，还有一个“低垃圾模式”，但不是完全无垃圾，不使用ThreadLocal字段。

Log4j2.6中的无垃圾日志记录部分通过重用ThreadLocal字段中的对象来使用，部分在将文本转换字符时重用缓冲区来实现。



