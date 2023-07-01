
# xml路径问题

mybatis的mapper不能满足我们的要求，或者需要多表联查时
我们需要写xml，但这个xml的路径在哪里呢？

## 默认路径

>mybatis-plus的默认路径为
>`mapper-locations: classpath*:/mapper/**/*.xml`
>也就是说直接放在resources的mapper文件夹下就行


## 自定义路径

哎，我觉得放在resources中太远了，看的不舒服

我就想和我的mapper放在一起，那就需要做一些配置

1.maven默认只会加载java文件夹中的java文件，其他文件不会加载，所以我们就先要让maven加载你的xml文件
```xml
<build>  
	<resources>  
		<!-- 这个意思是让maven加载java中的xml文件 -->  
		<resource>  
			<directory>src/main/java</directory>  
			<includes>  
				<include>**/*.xml</include>  
			</includes>  
		</resource>  
		<!--如果之配置上边，就会导致下边的默认设置被覆盖，所以需要加上-->  
		<resource>  
			<directory>src/main/resources</directory>  
			<includes>  
				<include>**/*.*</include>  
			</includes>  
			<filtering>false</filtering>  
		</resource>  
	</resources>
</build>
```

2.修改mybatis-plus的默认路径为你的路径
```properties
mybatis-plus.mapper-locations=classpath:com/harukanine/eduService/mapper/xml/*.xml
```




