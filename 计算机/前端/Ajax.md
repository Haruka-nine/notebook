# AJAX



## AJAX说明

Ajax就是异步的JS和XML

Ajax可以在浏览器中向服务器发送请求:即无刷新获取数据

## XML

1. 可扩展的标记语言
2. 被设计用来传输和存储数据



## AJAX优缺点

1. 优点
   1. 可以无刷新页面而和服务器端进行通信
   2. 允许你根据用户事件来更新部分页面内容

2. 缺点
   1. 没有浏览历史，不能回退
   2. 存在跨域问题
   3. SEO不友好

## HTTP协议请求和响应

### HTTP

超文本传输协议，协议详细规定了浏览器和万维网服务器之间相互通信的规则。



### 请求报文

1. 行
   1. 请求类型  get /post
   2. 请求路径url
   3. HTTP协议   HTTP/1.1

2. 头
   1. Host: atguigu.com
   2. Cookie: name = guigu
   3. Content-type: application/x-www-form-urlencoded
   4. User-Agent: chrome 83

3.  一个固定的空行

4. 体

   username = admin&password=admin


### 响应报文

1. 行 
   1. HTTP/1.1 x	协议版本
   2. 200    响应状态码
   3. ok       响应状态值

2. 头
   1. Content-Type: text/html;charset = utf-8
   2. Content-length:2048
   3. Content-encoding:gzip

3. 一个固定的空行

4. 体 

   ``` html
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
   
   </body>
   </html>
   ```

   

   ## express的使用

   在express框架下学习

   创建一个空项目

   1. 设置npm包管理工具初始化

      ```shell
      npm init --yes
      ```

   2. 在当前的包下安装express

      ```shell
      npm i express
      ```

      

   3. 引入express

      ```js
      const express = require('express')
      ```
   
   2. 创建应用对象

      ```js
      const app = express
      ```
   
   3. 创建路由规则

      ```js
      //request是对请求报文的封装
      //response是对响应报文的封装
      app.get('/',(request,response)=>{
      	//设置响应头 设置允许跨域
      	response.setHeader('Access-Control-Allow-Origin','*')
      	//设置响应
      	response.sed
      });
      ```
   
   4. 监听端口启动服务

      ```js
      app.listen(8000,()=>{
      console.log("服务已经启动，8000端口监听中");
      })
      ```
   
      这样可以设置一个简单的服务器，端口为8000，

## AJAX的基本操作

为一个页面创建事件，为这个事件设置AJAX操作

```js
           //1.创建对象
            const xhr = new XMLHttpRequest()
            //2.初始化 设置请求方法和url
            xhr.open('get','http://localhost:8000/server')
            //3.发送
            xhr.send();
            //4.事件绑定 处理服务端返回的结果
            //on  when 当...时候
            //readystate 是xhr对象中的属性，表示状态0 1 2 3 4
            //change 改变
            xhr.onreadystatechange = function () {
                //判断
                if (xhr.readyState===4){
                    //判断响应的状态码 200 404 403 401 500
                    //状态码为2xx就为成功
                    if (xhr.status >=200 && xhr.status <300){
                        //处理结果 行 头 空行 体
                        //1.响应行
                        console.log(xhr.status)//状态码
                        console.log(xhr.statusText)//状态字符串
                        console.log(xhr.getAllResponseHeaders())//所有响应头
                        console.log(xhr.response)//响应体
                    }
                    else {

                    }
                }
            }
```

在得到响应体可以将响应体（xhr.response）在html中作为文本显示



## AJAX的url设置请求参数

在请求url后加?然后写参数，多个参数用&分割

```js
http://localhost:8000/server?a=100&b=200&c=300
```



## AJAXPOST请求

### 发送

只需要设置请求头的请求方法为POST就可以发送POST请求

需要服务器设置对应的post响应方法



### 设置参数

在xhr.send()中设置参数

```js
xhr.send('a=100&b=200&c=300'); 
或者
xhr.send('a:100&b:200&c:300');
或者其他服务端可以处理的格式
```



## 设置请求头信息

在open方法后使用

```js
xhr.setRequestHeader()设置请求头信息
例如：
xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded')
```

当然也可以不使用预定义的请求头，使用自定义的请求头（但需要服务器接收你自定义的请求头）

当头信息存在自定义请求头时，会发送一个OPTION看当前的请求头是否可用



## 服务器响应Json数据

需要在服务器将响应回的json对象数据转化为字符串

```js
let str = JSON.stringify(data)
response.send(str)
```

浏览器接收到json字符串数据我们需要将其转化为json数据

1. ```js
//手动转化
   let data = JSON.parse(xhr.response)
   data就是传回来的json数据  
  2.  ```js
   //自动转化
   //在xhr.open（）前添加响应数据类型
   xhr.responseType='json'
   这样返回的 xhr.response会自动转为json对象数据




## nodemon 自动重启工具

全局安装：

```js
npm install -g nodemon
```

webstrom设置启动不用node xxx.js 而是用 nodemon xxx.js

设置Node parameters为

C:\Users\YYM\AppData\Roaming\npm\node_modules\nodemon\bin\nodemon.js

不使用可以设置为空



## IE缓存 问题

ie浏览器会对浏览器请求的数据进行缓存，就导致服务器的数据改变后，再一次发送相同请求时，ie会读取缓存，导致数据错误

解决方法：在请求的url后添加时间戳，使每次请求都不一样

```js
xhr.open('GET','http://localhost:8000/ie?t='+Date.now())
```

但实际使用时我们并不需要自己操作，各种工具会自动添加，子需要了解



## AJAX请求超时和网络异常处理

可以在xhr设置超时设置

```
xhr.timeout = 2000; 就是2s没有收到就取消
```

也可以设置超时回调函数

```
xhr.ontimeout = function () {
                alert("网络异常,请稍后再试")
            }
如果超时就调用超时回调函数
这里使用alert，实际可以使用页面或者div显示
```

设置网络异常回调函数

```
xhr.onerror=function () {
                alert("你的网络似乎出了一些问题")
            }
```



## AJAX的取消请求

ajax可以在请求还没有返回响应前取消掉请求

使用这个需要将 xhr放在点击方法外边

`let  xhr = null`

然后发送请求的方法使用这个xhr获取请求api

取消请求只需要在另一个方法中调用

```
xhr.abort()就可以需要掉这个请求
```



## AJAX重复发送请求

当我们的服务比较慢时，用户多次点击，服务器收到多次请求，压力较大

我们在多次请求时可以判断前边是否有相同请求，如果有就把前面的请求取消掉

```js
let xhr = null
        //标识变量
        let isSending = false //是否正在发送AJAX请求
        btn0.onclick = function () {
            //判断标识变量
            if(isSending){
                xhr.abort(); //如果正在发送，就取消该请求，创建一个新请求
            }
             xhr = new XMLHttpRequest()
            //修改标识变量的值
            isSending = true
            xhr.open('GET','http://localhost:8000/delay')
            xhr.send()
            xhr.onreadystatechange = function () {
                if(xhr.readyState ===4)
                {
                    //修改标识变量
                    isSending = false
                }
            }
        }
```

这里主要是将请求的变量放在函数外，所以下次的函数可以获取上次的请求变量



## JQuery发送AJAX请求

### get

```
$('button').eq(0).click(function () {
            /*第一个参数是url，第二个参数是对象，第三个参数是回调函数,第四个参数可以写json,表明返回的类型自动转换为json*/
            $.get('http://localhost:8000/jquery-server',{a:100,b:200},function (data) {
                console.log(data);
            },'json')
            console.log('test')
        })
```

### post

```
$('button').eq(1).click(function () {
            /*第一个参数是url，第二个参数是对象，第三个参数是回调函数*/
            $.post('http://localhost:8000/jquery-server',{a:100,b:200},function (data) {
                console.log(data);
            })
            console.log('test')
        })
       //post同样也可以设置返回类型自动转换为json
```

### 通用型方法ajax

```js
        $('button').eq(2).click(function () {
          $.ajax({
              //url
              url:'http://localhost:8000/delay',
              //参数
              data:{a:100,b:200},
              //请求类型
              type:'GET',
              //响应体结果
              dataType:'json',
              //成功的回调
              success:function (data) {
                  console.log(data);
              },
              //超时时间
              timeout:2000,
              //失败的回调
              error:function () {
                  console.log('出错了!!')
              }
              //头信息
              headers:{
                  c:300,
                  d:400
              }
          })

        })
```



## axios

首先axios可以配置自己的baseURL

```js
axios.defaults.baseURL ='http://localhost:8000'
```

axios默认发送的请求体是json类型，收到json的字符串类型也会默认转换为json类型

这里参数都会转成query类型的url拼接的

### get

```js
btns[0].onclick=function () {
            //GET请求
            axios.get('/axios-server',{
                //url参数
                params:{
                    id:100,
                    vip:7
                },
                //请求头信息
                headers:{
                    name:'atttt',
                    age:10
                }
            }).then(value => {
                console.log(value)
            })
        }
```

### post

```js
btns[1].onclick=function () {
            //POST请求 //第二个参数是请求体 ，第三个参数才是配置
            axios.post('/axios-server',{
                    username:'admin',
                    password:'123456'
                },{
                //url参数
                params:{
                    id:100,
                    vip:7
                },
                //请求头信息
                headers:{
                    name:'atttt',
                    age:10
                }

                }
            ).then(value => {
                console.log(value)
            })
        }
```

### AJAX

```js
btns[2].onclick=function () {
            axios({
                method:'POST',

                //url参数
                url:'/axios-server',

                //params
                params:{
                    vip:10,
                    level:30
                },
                headers:{
                    a:100,
                    b:200
                },
                data:{
                    username: 'admin',
                    password: 'admin'
                }
            }).then(response=>{
                console.log(response.data)
            })
        }
```

## fetch函数发送ajax请求

注意fetch在兼容性较差，在ie上无法使用

fetch获取数据需要调用两次then

```js
btn.onclick=function () {
            //fetch接收两个参数 第一个是url，第二个为配置项
            fetch('http://localhost:8000/fetch-server',{
                //请求方法
                method:'POST',
                //请求头
                headers:{
                    name:'atttt'
                },
                //请求体
                body:'username=admin&password=admin'

            }).then(response=>{
                return response.text()
            }).then(response=>{
                console.log(response)
            })

        }
```

## 跨域

### 同源策略

同源：协议、域名、端口号、必须完全相同

违背同源策略就是跨域

AJAX在请求时是默认遵循同源策略的

### JSONP

使用<script>标签通过src对目标发起请求可以跨域

但返回的必须是标准的js语句

### JQuery完成jsonp请求

```js
$.getJSON('http://localhost:8000/jquery-jsonp-server?callback=?',function (data) {
                $('#result').html(`
                名称:${data.name}
                校区:${data.city}
                `)
            })
```

调用getJSON方法发起请求，其中url最后要加上 ?callback=?  ，服务器需要使用  let cb = request.query.callback 接收callback

在返回结果时使用 

```js
response.end(`${cb}(${str})`)
```

其中str就是回调函数的data参数

### 设置CORS响应头实现跨域

服务器设置允许跨域响应头

```js
response.setHeader('Access-Control-Allow-Origin','*')//这是允许所有网页
//如果对某个网页就将*换为网页的url
```

```js
response.setHeader('Access-Control-Allow-Methods','*')//允许其他请求方法
```

```js
response.setHeader('Access-Control-Allow-Headers','*')//允许自定义头信息
```

还有很多其他的响应头，详情可看CORS的文档