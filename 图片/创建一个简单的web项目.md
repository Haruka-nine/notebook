
## 明确前后端交互

基础的web项目需要完成基本的两点：
- 前端可以有呈现数据的页面
- 后端可以提供给前端需要的数据

其余的在现阶段并不需要考虑

大家可以找一些项目教程去观看，或者一些框架教程，我们可以接用其的思路和一些资源
比如交易项目的ui设计、首页图片这种明显需要设计师的东西，就可以找专门的交易开源项目，使用其静态资源
我们的项目就是借用了b站教程的静态资源


## javaweb

我们可以考虑直接使用最基本的javaweb，这将并不使用任何框架，后端使用servlet，前端使用动态语言jsp，**一步到位，前后端在一起**。学习可以在b站上搜索javaweb

【尚硅谷丨2022版JavaWeb教程(全新技术栈,全程实战)】 https://www.bilibili.com/video/BV1AS4y177xJ/?share_source=copy_web&vd_source=45c6e54563e8efa71d5ff136f57a2a27

>[!note] 缺点
>很显然，javaweb没有任何框架就是他的缺点，你在看完javaweb后已经可以做出一个web项目了，但它很多功能并不能很好的实现，同样现在也很少使用这种。

但要明白一点，框架就是对基础的封装。框架的底层其实就是使用的javaweb，只不过对其中一个servlet就要配置很多的步骤改为一个注解就可以自动完成的程序。学习javaweb对学习ssm理论有很好的帮助

如果想要进行前后端分开设计，那可能需要花费更多的时间了

## 前端

### 小程序
学会Vue其实就相当于学会了小程序，学会了小程序也就理解了vue。

想要简单的话我更推荐小程序，因为小程序的文档更加丰富，微信和支付宝对api的封装也很好使用
可以看这个示例教程了解。

【尚硅谷微信小程序开发（零基础小程序开发入门到精通）】 https://www.bilibili.com/video/BV12K411A7A2/?share_source=copy_web&vd_source=45c6e54563e8efa71d5ff136f57a2a27

### vue

如果想要用vue做一个在浏览器上显示的web项目，就需要花费更多时间了，vue想要熟练使用，就需要了解基本原理，这需要花费比小程序更多的时间

【尚硅谷Vue2.0+Vue3.0全套教程丨vuejs从入门到精通】 https://www.bilibili.com/video/BV1Zy4y1K7SH/?share_source=copy_web&vd_source=45c6e54563e8efa71d5ff136f57a2a27

基础原理了解完就可以发送基本的请求和进行页面跳转的实现了

## 后端

后端大家可以自行搜索是否有其他更简单的框架。

目前用的很多的是java和java的spring框架，其他语言可能有其他框架，比如现在增增日上的GO语言，但我并不了解QAQ。

spring是基础理论，springmvc是应用理论，springboot是封装的简单框架

可以看一下springboot快速入门教程

【黑马程序员SpringBoot教程，6小时快速入门Java微服务架构Spring Boot】 https://www.bilibili.com/video/BV1Lq4y1J77x/?share_source=copy_web&vd_source=45c6e54563e8efa71d5ff136f57a2a27

（这个我并没有看过，但快速入门了解应该可以进行基本的crud和返回请求）

有时间可以去 按照 spring  、 springmvc 、springboot的顺序一步步了解spring框架。



