根据数据库进行代码反向生成
官网：
https://baomidou.com/pages/779a6e/

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