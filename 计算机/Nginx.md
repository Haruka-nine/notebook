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

重新加载系统服务

`systemctl daemon-reload`

启动服务

`systemctl start nginx.service`

开机启动 

`systemctl enable nginx.service`