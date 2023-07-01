
在Mapper上添加@Mapper标注，在springboot启动时，就会有拦截器拦截到这个，然后对bean生成bean

但每个mapper接口都添加@Mapper如果有很多，我们一个一个添加太麻烦，可以直接在配置类或者启动类添加@MapperScan("mapper的包名")

设置完成之后就会吧mapper里的接口实例化；
总之，这两个其实差不多，我们必须设置其中一个就行