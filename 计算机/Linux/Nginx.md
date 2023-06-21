# 安装部署

## 安装

### 版本区别

常用版本分为四大阵营 

- Nginx开源版 http://nginx.org/ 
- Nginx plus 商业版 https://www.nginx.com 
- openresty http://openresty.org/cn/ 
- Tengine http://tengine.taobao.org/

### 编译安装

将安装包传入，然后进行解压

```shell
tar zxvf   nginx-1.21.3.tar.gz
```

然后cd进入解压出来的文件夹

ls看到有个 configure

使用命令

```shell
./configure --prefix=/usr/local/nginx  第一步
make   第二步
make install  第三步
```

### 对于警报和报错的处理

可以先安装下边的几个库再执行上方的命令

提示

```shell
checking for OS
+ Linux 3.10.0-693.el7.x86_64 x86_64
checking for C compiler ... not found
./configure: error: C compiler cc is not found
```

安装gcc

```shell
yum install -y gcc
```



提示

```
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
```

安装perl库

```shell
yum install -y pcre pcre-devel
```



提示

```
/configure: error: the HTTP gzip module requires the zlib library.
You can either disable the module by using --without-http_gzip_module
option, or install the zlib library into the system, or build the zlib library
statically from the source with nginx by using --with-zlib=<path> option.
```

安装zlib库

```shell
yum install -y zlib zlib-devel
```


如果之前没有安装[openssl](https://so.csdn.net/so/search?q=openssl&spm=1001.2101.3001.7020),需要在执行./configure命令之前安装

```shell
yum -y install openssl openssl-devel make zlib zlib-devel gcc gcc-c++ libtool    pcre pcre-devel
```


## 部署

### 启动

进入安装好的目录 /usr/local/nginx/sbin

```
./nginx 启动
./nginx -s stop 快速停止
./nginx -s quit 优雅关闭，在退出前完成已经接受的连接请求
./nginx -s reload 重新加载配置
```

然后访问服务器地址或者虚拟机ip就可以看到nginx的欢迎页，默认访问80端口，访问失败可能是防火墙拦截

### 关于防火墙

关闭防火墙

`systemctl stop firewalld.service`

禁止防火墙开机启动

`systemctl disable firewalld.service`

放行端口

`firewall-cmd --zone=public --add-port=80/tcp --permanent`

重启防火墙

`firewall-cmd --reload`

### 安装成系统服务

现在我们启动nginx需要去对应目录启动，还不能使用systemctl

创建服务脚本

`vi /usr/lib/systemd/system/nginx.service`

服务脚本内容

先进入插入模式再黏贴

```shell
[Unit]
Description=nginx - web server
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```

>这里想要启动服务，必须先把之前开启的nginx服务关闭，然后再用系统命令开启
>或者直接使用这个命令干掉nginx         `pkill -9 nginx`

重新加载系统服务

`systemctl daemon-reload`

启动服务

`systemctl start nginx.service`

开机启动 

`systemctl enable nginx.service`



# nginx网关和gatway网关的区别

首先这两种网关的定义不一样

nginx用户访问的总入口，也就是前端页面的容器，流量网关

gateway的定义是针对每一个业务微服务来的，属于业务网关

服务网关一般部署在流量网关之后、业务系统之前，比流量网关更靠近业务系统。通常API网指的是业务网关。 有时候我们也会模糊流量网关和业务网关，让一个网关承担所有的工作,所以这两者之间并没有严格的界线。

nginx与gateway的区别：

nginx是用C语言写的，自定义扩展的话，要么写C要么写lua

gateway是java语言的一个框架，可以在框架上进行代码的扩展与控制，例如：安全控制，统一异常处理，XXS,SQL注入等；权限控制，黑白名单，性能监控，日志打印等；可以利用注册中心进行动态配置

gateway的主要功能有，路由，断言，过滤器，利用它的这些特性，可以做流控。

nginx做网关，更多的是做总流量入口，反向代理，负载均衡等，还可以用来做web服务器。

# 目录结构

我们将nginx安装在了 /usr/local/nginx

这里有几个主要目录

conf   html  logs  sbin

在docker 中对应的目录分别为

/etc/nginx      /usr/share/nginx/html    /var/log/nginx


sbin中只有一个nginx的主程序文件


>conf文件中主要放的就是nginx的配置文件，主配置文件就是nginx.conf
>我们可以将其他的配置分割为这个目录中的其他配置文件，然后让nginx.conf去引用他

>html这个目录放的就是默认情况下显示出来的nginx的页面

>logs这个目录是用来记录日志的，这个目录回越来越大的，我们可以在配置文件中配置logs的大小
>access.log会记录用户访问的进程
> error.log会记录用访问出错的进程
> nginx.pid主要是记录用户访问的id号

# 执行流程
![[Pasted image 20221004173902.png]]


# Nginx基础配置

## 最小配置文件
```shell

worker_processes  1;

events {
    worker_connections  1024;
}


http {
    include       mime.types;
	
    default_type  application/octet-stream;
	
    sendfile        on;
   
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
		
        location = /50x.html {
            root   html;
        }
    }


}

```

删除配置文件中所有注释后，剩下的就是上方的最小配置文件

### worker_processes

worker_processes 1; 默认为1，表示开启一个业务进程

### worker_connections

worker_connections 1024; 单个业务进程可接受连接数

### include mime.types

 include       mime.types; 引入http mime；类型

浏览器通过后缀名并不知道文件类型，需要一个请求头才知道

mime就是这样一个将后缀名和请求头的对应文件，告诉浏览器这个后缀名是什么类型的文件

### default_type  application/octet-stream;
就是默认的文件类型

 ### sendfile        on;
数据0拷贝，就是我的电脑传到你的电脑，需要一个U盘，先拷到U盘，再拷到你电脑上，这就是经过了拷贝

直接从我的电脑传到你的电脑，就是直接数据0拷贝

我们收到用户端需要某个文件时，如果关闭，就是先把这个文件加载到nginx的内存中，然后再传出去，如果开启数据0拷贝
我们就直接给linux一个信号，让linux直接将文件传出去

### keepalive_timeout  65;

保持链接 ——超时

# Nginx虚拟主机配置

## 域名解析

我们购买的域名 比如 harukanine.top

我们可以为其 或者其的子域名  nine.haruka.top添加 域名解析

就是你访问这个域名时转发到哪个ip地址

并且你的访问域名会作为请求头的host携带过去

## Nginx配置

```shell
server {
        listen       80;
        server_name  www.harukanine.top;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /www/www;
            index  index.html index.htm;
        }
```


nginx要保证每一个server的 server_name加上listen 这个值是唯一的

一个请求过来，nginx会根据访问的host+端口和每一个server的server_name+listen进行匹配

匹配到第一个成功的就停止匹配，使用这个server

如果没有匹配到或者host为空（直接使用ip访问），就直接使用第一个server（*前提是端口一致*）
端口不一致，直接失败

## servername 匹配规则

- 完整匹配
	- vod.harukanine.top，完全一样进行匹配，可以匹配多个，以空格隔开
- 通配符匹配
	- ``*.mmban.*``, * 就是任意都可以，也就是 mm.mmban.top就会被上方的进行匹配
- 正则匹配
	- servername可以使用正则表达式进行匹配


# 反向代理配置

```shell
server {
        listen       80;
        server_name  www.harukanine.top;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
	        proxy_pass http://www.bilibili.com; 
        #    root   /www/www;
        #    index  index.html index.htm;
        }
```

使用proxy_pass,我们发送的请求会被代理发送到www.bilibili.com

几个问题

> 我们访问 www.haruaknine.top,但跳转后地址栏自动改变了地址变成了www.bilibili.com
> 因为我们访问地址请求302(重新请求代码)，并且返回给我们一个地址，让我们进行跳转

>代理服务器不能使用https,因为https需要证书

# 负载均衡

## 基本配置

```shell
server {
        listen       80;
        server_name  www.harukanine.top;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        upstream httpds{
        server 192.168.44.102:80;
        server 192.168.44.103:80;
        }
        
        location / {
	        proxy_pass http://httpds; 
        #    root   /www/www;
        #    index  index.html index.htm;
        }
```

使用upstream 定义一个ip或者域名组

在proxy_pass中使用这个组

在访问时，默认从这个组中进行轮询访问

## 负载均衡策略

- 权重：我们不同服务器的带宽不一样，我们一般不能给其分配一样的权重进行轮询，比如一个服务器的为千兆，一个为百兆我们肯定要为千兆分配更多的权重

```shell
server {
        listen       80;
        server_name  www.harukanine.top;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        upstream httpds{
        server 192.168.44.102:80 weight=8;
        server 192.168.44.103:80 weight=2;
        }
        
        location / {
	        proxy_pass http://httpds; 
        #    root   /www/www;
        #    index  index.html index.htm;
        }
```

我们可以在upstream分配的服务器后边加入weight，nginx会根据weight作为权重的比例

我们如果想让一台服务器下线，可以直接清除配置，也可以在后边写在down

```shell
server 192.168.44.102:80 weight=8 down;
```

我们还可以设置备用服务器，正常并不会使用，但如果正常的服务器都寄了，备用服务器就会上线,使用backup

```shell
server 192.168.44.102:80 weight=8 backup;
```


# 动静分离

适用于中小型网站，中小型网站静态资源可能较少，我们可以将静态资源放到nginx服务器
大型网站静态资源太多一般不用
动静分离可以达到一种加载加速的效果

```shell
    server {
        listen       80;
        server_name  haruka.harukanine.top;

        location / {
            root   /www/vod;
            index  index.html index.htm;
        }
		
		location /image/ {
			root html;
			index  index.html index.htm;
		}

        error_page   500 502 503 504  /50x.html;
		
        location = /50x.html {
            root   html;
        }
    }
```

原本放在后端服务器的static的静态资源可以放在nginx的html文件夹（相对路径）
如果想放到nginx安装文件夹以外的文件夹，用 / 表示根目录 如 `/root/static`

这样匹配到 http://haruka.harukanine.top/image/999.jpg  ,就会匹配到image,然后去html找image/999.jpg

这个是**可以使用正则表达式**匹配的

# UrlRewrite

nginx可以对url进行rewrite,对匹配上的url进行重写

rewrite可以对我们的请求进行重写，用来**它能隐藏我们真实的后端服务器的这个物理的地址**，或者不让用户知道我们的参数

**语法：**
```
rewrite          <regex>         <replacement>         [flag];  
关键字          正则                 替代内容            flag标记
```
关键字：其中关键字不能改变  
正则：perl兼容正则表达式语句进行规则匹配  
替代内容：将正则匹配的内容替换成replacement  
flag标记：rewrite支持的flag标记

rewrite  ^  正则 $  书写转变的地址，真实的地址   转发的形式 （break ，rewrite，last，redirect ，percent ）

例子：rewrite ^ /2.html $   /index.jsppageNum=2 break;
URLRewrite 的关键字  rewrite ，后面跟正则表达式，这正则表达式以 ^ 开头，然后以这个$ 结尾， 里边写正则， 后边写上这个我们想要转变的这个地址，这就是原来的这个真实地址

``` shell
flag标记说明：
last         #本条规则匹配完成后，继续向下匹配新的location URI规则
break     #本条规则匹配完成即终止，不再匹配后面的任何规则
redirect   #返回302临时重定向，浏览器地址会显示跳转后的URL地址
permanent #返回301永久重定向，浏览器地址栏会显示跳转后的URL地址

redirect 和 perement 的区别
redirect 302  会返回的临时重定向 
perement  301  返回 永久冲定向。
这个 301 和 302 的区别，在我们实际给真实的用户，我们用户去适用的时候其实是没有任何区别的，它都会跳转，然后这个 URL 都会发生变化。这个临时重定向和永久重定向，这是给我们的这个网络爬虫给它来看的。
```


# 防盗链

当我们请求到一个页面后，这个页面一般会再去请求其中的静态资源，这时候请求头中，会有一个refer字段，表示当前这个请求的来源，我们可以限制指定来源的请求才返回，否则就不返回，这样可以节省资源
![[Pasted image 20221007114708.png]]

对于图片来说，A网站，如果想使用B网站的图片，可以直接写上B网站图片的链接地址，或者将B网站的图片通过右键另存为的方式下载到本地，然后在页面上使用。如果B网站不想A网站这么干了，那么B网站可以采取防盗链的措施来干这个工作，结果就是，A网站想请求所需要的资源，通过url的方式，获取的可能不是原来的图片了，出现404或者别的图片替代了。如果通过浏览器直接打开图片url，那么仍然有可能显示404，这就是防盗链。

![[Pasted image 20221007114825.png]]

none就是referer不存在，也就是直接输入图片地址直接访问，而不是网页中使用img标签进行引用


上图就是发现盗链直接返回错误码

我们还可以返回设定的盗链图片

```shell
location /img{
	valid_referers http:192.168.174/133;
	if ($invalid_referer){#无效的
		 rewrite ^/  /img/x.png break;
	}
	root html;
	index  index.html index.htm;
}
```


# 高可用
![[Pasted image 20221007125058.png]]

如果我们的一台nginx挂了，我们不就直接寄？
我们需要多台nginx，但如果多个ip集群，就需要一个分发nginx，这不有回到了起点，无限循环

所以我们可以使用虚拟ip

用户访问时，访问的是一个虚拟IP，keepalived会选定一个主服务器使用这个虚拟IP

每台机器上的keepalived会相互通信，根据其他机器上的keepalived进程是否存在，判断服务器状态，如果默认的Master停止了，就会在剩下的Backup机器中，竞选出一台Nginx服务器作为Master

**安装keepalived**

```shell
yum install -y keepalived
```

**修改keepalived配置**

-   配置文件在/etc/keepalived/keepalived.conf
-   `vrrp_instance`、`authentication`、`virtual_router_id`、`virtual_ipaddress`这几个一样的机器，才算是同一个组里。这个组才会选出一个作为Master机器

第一台
```shell
! Configuration File for keepalived

global_defs {
   router_id lb1 # 名字与其他配置了keepalive的机器不重复就行
}

vrrp_instance heyingjie {#vrrp实例名可以随意取
    state MASTER #只能有一个默认的Master，其他写BACKUP
    interface ens33 # ip addr查看下网卡名，默认时ens33
    virtual_router_id 51
    priority 100 # 多台安装了keepalived的机器竞争成为Master的优先级
    advert_int 1 #通信时间
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.200.16 #虚拟IP
    }
}
```

第二台

```shell
! Configuration File for keepalived

global_defs {
   router_id lb2 
}

vrrp_instance heyingjie {
    state BACKUP #只能有一个默认的Master，其他写BACKUP
    interface ens33 
    virtual_router_id 51
    priority 50
    advert_int 1 
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.200.16 #虚拟IP
    }
}
```

之后我们访问虚拟IP就会访问 作为master的nginx


# https证书

购买服务器——>购买域名，并解析到这个主机——>购买证书，绑定到域名上，并且把证书文件安装到服务器，并在Nginx上配置好

将两个文件上传到Nginx目录中，记得放置的位置。我这里直接放在nginx.conf配置文件所在的目录（`/user/local/nginx/conf`），所以写的都是相对路径

```shell
server {
	listen 443 ss1;
	
	ss1_certificate  xxx.pem; #这里是证书路径
	ss1_certificate_key  xxx.key;  #这里是私钥路径
}
```

也可以看nginx的默认文件，将部分注释给解开