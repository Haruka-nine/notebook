# Docker简介
## Docker是什么？

docker是解决了运行环境和配置问题的软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术

## Docker与虚拟机对比

虚拟机（virtual machine）就是带环境安装的一种解决方案。

它可以在一种操作系统里面运行另一种操作系统，比如在Windows10系统里面运行Linux系统CentOS7。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变。

虚拟机缺点 ：1    资源占用多               2    冗余步骤多                 3    启动慢

由于前面虚拟机存在某些缺点，Linux发展出了另一种虚拟化技术：

Linux容器(Linux Containers，缩写为 LXC)

Linux容器是与系统其他部分隔离开的一系列进程，从另一个镜像运行，并由该镜像提供支持进程所需的全部文件。容器提供的镜像包含了应用的所有依赖项，因而在从开发到测试再到生产的整个过程中，它都具有可移植性和一致性。

Linux 容器不是模拟一个完整的操作系统而是对进程进行隔离。有了容器，就可以将软件运行所需的所有资源打包到一个隔离的容器中。容器与虚拟机不同，不需要捆绑一整套操作系统，只需要软件工作所需的库资源和设置。系统因此而变得高效轻量并保证部署在任何环境中的软件都能始终如一地运行。

Docker容器是在操作系统层面上实现虚拟化，直接复用本地主机的操作系统，而传统虚拟机则是在硬件层面实现虚拟化
与传统虚拟机相比，Docker优势体现为启动速度块、占用体积小。

比较了 Docker 和传统虚拟化方式的不同之处：

- 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；

- 容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

* 每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源。

## Docker官网

官网网址 ：https://www.docker.com

docker hub 仓库 ：https://hub.docker.com

# Docker安装
Docker必须部署在Linux内核的系统上，因此其他系统想部署Docker就必须安装一个虚拟机

**前提条件**

目前，CentOS 仅发行版本中的内核支持 Docker。Docker 运行在CentOS 7 (64-bit)上，

要求系统为64位、Linux系统内核版本为 3.8以上，这里选用Centos7.x

**查看自己的内核**

uname命令用于打印当前系统相关信息（内核版本号、硬件架构、主机名称和操作系统类型等）。
`` cat /etc/redhat-release``

## Docker三要素

### 镜像(image)
Docker 镜像（Image）就是一个**只读**的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。

它也相当于是一个root文件系统。比如官方镜像 centos:7 就包含了完整的一套 centos:7 最小系统的 root 文件系统。

相当于容器的“源代码”，docker镜像文件类似于Java的类模板，而docker容器实例类似于java中new出来的实例对象。

### 容器(container)
Docker 利用容器（Container）独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环境，容器是用镜像创建的运行实例。就像是Java中的类和实例对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器为镜像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台

**_可以把容器看做是一个简易版的 Linux 环境_**（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

### 仓库(repository)
仓库（Repository）是集中存放镜像文件的场所。

类似于

Maven仓库，存放各种jar包的地方；

github仓库，存放各种git项目的地方；

Docker公司提供的官方registry被称为Docker Hub，存放各种镜像模板的地方。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

最大的公开仓库是 Docker Hub(https://hub.docker.com/)，

存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云 、网易云等

### 总结
需要正确的理解仓库/镜像/容器这几个概念:

Docker 本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就是image镜像文件。只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个对象)。

image文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

镜像文件

*  image 文件生成的容器实例，本身也是一个文件，称为镜像文件。

容器实例

*  一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器

仓库

* 就是放一堆镜像的地方，我们可以把镜像发布到仓库中，需要的时候再从仓库中拉下来就可以了。

## 安装步骤
https://docs.docker.com/engine/install/centos/
**建议参照官网和笔记一起看，官网有更新以官网为主**

### 确定自己的linux版本
您需要 CentOS 7、CentOS 8（流）或 CentOS 9（流）的维护版本。

### 卸载旧版本

```
 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

### yum安装gcc相关

首先我们需要确保我们的linux能够上网

运行命令

```shell
yum -y install gcc

yum -y install gcc-c++
```

### 安装需要的软件包
您可以根据需要以不同的方式安装 Docker Engine：

-   大多数用户 [设置 Docker 的存储库](https://docs.docker.com/engine/install/centos/#install-using-the-repository)并从中安装，以便于安装和升级任务。这是推荐的方法。
    
-   一些用户下载 RPM 包并 [手动安装它](https://docs.docker.com/engine/install/centos/#install-from-a-package)并完全手动管理升级。这在诸如在无法访问 Internet 的气隙系统上安装 Docker 等情况下很有用。
    
-   在测试和开发环境中，一些用户选择使用自动化 [便利脚本](https://docs.docker.com/engine/install/centos/#install-using-the-convenience-script)来安装 Docker。

我们跟随大多数用户，选择第一种

*官网提示：*
在新主机上首次安装 Docker Engine 之前，您需要设置 Docker 存储库。之后，您可以从存储库安装和更新 Docker
我们先执行第一条命令，进行软件包的安装

```shell
sudo yum install -y yum-utils
```

### 设置stable镜像仓库

这里有一个坑，官网的命令设置的镜像仓库是国外的，也是就说经常命令超时
所以我们使用国内的镜像源

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```


### 更新yum软件包的索引

后续需要使用yum软件下载，这里重建一下索引

```shell
yum makecache fast
```

### 安装DOCKER CE

```SHELL
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### 启动docker
```shell
 sudo systemctl start docker
```

### 测试docker
```shell
docker version

docker run hello-world
```
### 卸载docker
先停止docker
```shell
systemctl stop docker
```

卸载 Docker Engine、CLI、Containerd 和 Docker Compose 软件包：
```shell
sudo yum remove docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
主机上的映像、容器、卷或自定义配置文件不会自动删除。要删除所有映像、容器和卷：
```shell
 sudo rm -rf /var/lib/docker
 sudo rm -rf /var/lib/containerd
```

## 阿里云镜像加速
找到阿里云的容器镜像服务
镜像工具 - 镜像加速器

可以看到自己的加速器地址，看文档可以看到命令
```shell
sudo mkdir -p /etc/docker 
sudo tee /etc/docker/daemon.json <<-'EOF' 
{ 
"registry-mirrors": ["https://rxmcfmag.mirror.aliyuncs.com"] 
} 
EOF 
sudo systemctl daemon-reload 
sudo systemctl restart docker
```

# Docker常用命令
## 帮助启动类命令
- **启动docker**： systemctl start docker

- **停止docker**： systemctl stop docker

- **重启docker**： systemctl restart docker

- **查看docker状态**： systemctl status docker

- **开机启动**： systemctl enable docker

- **查看docker概要信息**： docker info

- **查看docker总体帮助文档**： docker --help

- **查看docker命令帮助文档**： docker 具体命令 --help
## 镜像命令
- **docker images** 
	- 列出本地主机上的镜像
	- PEPOSITORY:表示镜像的仓库源
	- TAG：镜像的标签
	- IMAGEID ：镜像ID
	- CREATED:镜像创建时间
	- SIZE：镜像大小
	- OPTIONS说明：
		-  -a:列出本地所有镜像(含历史映像层)
		- -q:只显示镜像id
同一仓库源可以有多个 TAG版本，代表这个仓库源的不同个版本，我们使用 REPOSITORY:TAG 来定义不同的镜像。
如果你不指定一个镜像的版本标签，例如你只使用 ubuntu，docker 将默认使用 ubuntu:latest 镜像

- **docker search 某个XXX镜像名字**
	- 搜索镜像 : docker search redis
	- NAME:镜像名称
	- DESCRIPTION:镜像说明
	- STARS:点赞数量
	- OFFICIAL:是否是官方的
	- AUTOMATED:是否是自动构建的
	- OPTIONS说明：
		-  --limit : 只列出N个镜像，默认25个 ： docker search --limit 5 redis

-  **docker pull 某个XXX镜像名字**
	- 下载镜像 :docker pull ubuntu
	- docker pull 镜像名字[:TAG]
		- 没有TAG就是最新版，等同于 docker pull 镜像名字 ：latest

- **docker system df**
	- 查看镜像/容器/数据卷所占的空间

- **docker rmi 某个XXX镜像名字ID**
	- 删除镜像(默认-根据名字删除)  docker rmi hello-world
	- 删除单个（根据id删除）          docker rmi  -f 镜像ID      //这里使用 -f是强制删除，不加有的因为容器依赖不能删除
	- 删除多个 (就是直接写多个id或名字)        docker rmi -f 镜像名1:TAG 镜像名2:TAG  
	- 删除全部（docker 支持参数续传，组合命令）docker rmi -f $(docker images -qa)

docker虚悬镜像是什么？
- 仓库名、标签都为none的，工作中需要删掉

## 容器命令

有了镜像才能创建容器，这里使用ubuntu进行演示

- ``docker run [OPTIONS]IMAGE[COMMAND] [ARG...]``
	- **新建+启动命令**
	- OPTIONS说明
		-   --name="容器的新名字"    为容器分配一个名称，不指定会使用默认
		-  -d     后台运行容器并返回容器ID,也即守护式容器（后台运行）
		- 
		-  -i      以交互模式运行容器，通常与 -t 同时使用
		-  -t     为容器从新分配一个伪输入终端，通常与 -i 同时使用，也即启动交互式容器    ``docker run -it``
		- 
		-  -P    随机端口映射，大写P
		-  -p    指定端口映射，小写p
	- for example : docker run -it --name=myu1 ubuntu  /bin/bash 
	    - /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。要退出终端，直接输入 exit:

- **docker ps**
	- 列出当前所有正在运行的容器
	- OPTIONS说明（常用）：
		-a :列出当前所有正在运行的容器+历史上运行过的

		-l :显示最近创建的容器。

		-n：显示最近n个创建的容器。 docker ps  -n  2 显示最近两个创建的容器

		-q :静默模式，只显示容器编号。
	
- **退出容器**
	- exit : run 进去容器，exit退出，容器停止
	- ctrl+p+q :run 进去容器，ctrl+p+q退出，容器不停止

- **启动已停止运行的容器**
	- docker start 容器ID获取容器名

- **重启容器**
	- docker restart 容器ID获取容器名

- **停止容器**
	- docker stop 容器ID获取容器名

- **强制停止容器**
	- docker kill 容器ID获取容器名

- **删除已停止的容器**
	- docker rm 容器ID或容器名（*不能删除正在运行的容器*）
	- docker rm -f  容器ID或容器名 (可以加入-f使其强制执行)


**重要**

我们上边还没有研究-d后台运行

这里使用命令进行尝试

**docker  run  -d ubuntu**

然后我们到docker ps  -a进行查看，*发现容器已经退出*

这里有很重要的一点：***Docker 容器后台运行，就必须有一个前台进程***。
如果不是哪些***一直挂起的命令***（比如运行top，tail），就是会自动退出的。

以nginx为例，正常情况下，我们配置启动服务只需要启动响应的service即可。例如service nginx start
但是，这样做，nginx为后台进程模式，运行，就导致docker前台没有运行的应用
这样的容器后台启动后，会立即自杀，因为他觉得自己没事可做了
最佳的解决方案是，将你要运行的程序以前台进程的形式运行，常见的就是命令行模式，表示我还有交互操作，别中断

所以说，有的镜像是不可以用 -d的，比如ubuntu

- **docker logs 容器ID或者容器名**
	- 查看容器日志

- **docker top 容器ID或者容器名**
	- 查看容器内运行的进程

- **docker inspect 容器ID或者容器名**
	- 查看容器内部细节


***进入正在运行容器并以命令行交互***

- **docker exec -it 容器id或者容器名  /bin/bash
	- 进入容器并以命令行进行交互
	- ***是在容器中打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的终止***

- **docker attach -it 容器id或者容器名**
	- 重新进入docker容器
	- 直接进入容器启动命令的终端，不会启动新的进程，用exit退出，*会导致容器的停止*

**推荐使用docker exec命令**

- **从容器拷贝文件到主机上**
	- docker cp  容器ID:容器内路径 目的主机路径

- **导入和导出容器**
	- export 导出容器中的内容留作为一个tar归档文件[对应import命令]
	- import 从tar包中的内容创建一个新的文件系统再导入为镜像[对应export]
	- 案例
		- docker export 容器id>文件名.tar
		- cat 文件名.tar | docker import -镜像用户/镜像名:镜像版本号

# Docker镜像

## 镜像是什么？
是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。

只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个对象)。

以我们的pull为例，在下载的过程中我们可以看到docker的镜像好像是在一层一层的在下载

UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union 文件系统是 Docker 镜像的基础。**镜像可以通过分层来进行继承**，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。


特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了复用。

比如说有多个镜像都从相同的 base 镜像构建而来，那么 Docker Host 只需在磁盘上保存一份 base 镜像；

同时内存中也只需加载一份 base 镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。

## 镜像commit操作实例

- **docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]**
	- 提交容器副本使之成为一个新的镜像

	先下载一个官方的ubuntu镜像，然后创建容器
	进入容器
	vim a.txt 发现vim是不能用的，因为默认的镜像是没有vim的

我们安装vim

```shell
#更新包管理工具
apt-get update
#安装我们需要的vim
apt-get -y install vim
```

测试一下就可以看到，这个容器已经支持vim了

我们可以commit我们的新镜像,之后可以在docker images 中看我们commit的镜像

# 将本地镜像推送到阿里云
![[Pasted image 20220928191712.png]]
阿里云镜像服务网站：https://cr.console.aliyun.com/cn-shanghai/instances

进入容器镜像服务

选择个人实例，如果进入企业，可以使用企业实例

创建命名空间和镜像仓库

镜像仓库的管理界面会自动生成响应的脚本
![[Pasted image 20220928194339.png]]
首先应该登陆，push执行3，拉取执行2

**注意：一个仓库放的应该是一种镜像的不同版本**

不应该在一个仓库种存放多种不同的镜像，因为一个仓库内之只区分版本不同

# 本地镜像推送到私有库
其实和推送到阿里云相差不大

1 官方Docker Hub地址：https://hub.docker.com/，中国大陆访问太慢了且准备被阿里云取代的趋势，不太主流。


2 Dockerhub、阿里云这样的公共镜像仓库可能不太方便，涉及机密的公司不可能提供镜像给公网，所以需要创建一个本地私人仓库供给团队使用，基于公司内部项目构建镜像。

 **Docker Registry是官方提供的工具，可以用于构建私有镜像仓库**

1.下载镜像 registry
 docker pull registry
2.运行我们的私有库，相当于本地有个Docker hub
docker run -d -p 5000:5000 -v /zzyyuse/myregistry/:/tmp/registry --privileged=true registry
默认情况，仓库被创建在容器的/var/lib/registry目录下，建议自行用容器卷映射，方便于宿主机联调

接下来创建我们要提交的镜像

我们创建携带ifconfig的ubuntu

先创建ubuntu容器

```shell
#更新包管理工具
apt-get update
#安装ifconfig命令
apt-get install net-tools
```

可以运行命令查看私服库上有什么镜像
curl -XGET http://192.168.10.10:5000/v2/_catalog

那个id就写本机的id，下方的id也和上方同理

将新镜像更改为符合私服规范的Tag
公式： docker   tag   镜像:Tag   Host:Port/Repository:Tag

示例 : docker tag  zzyyubuntu:1.2  192.168.10.10:5000/zzyyubuntu:1.2

*docker默认不允许http方式推送镜像，通过配置选项来取消这个限制。*

vim命令将配置文件更改：vim /etc/docker/daemon.json

```
{

  "registry-mirrors": ["https://aa25jngu.mirror.aliyuncs.com"],

  "insecure-registries": ["192.168.10.10:5000"]

}
```

push推送到私服库
docker push 192.168.10.10:5000/zzyyubuntu:1.2

push后可以再看一下有什么库

curl -XGET http://192.168.10.10:5000/v2/_catalog

从私服库上pull到本地进行运行
docker pull 192.168.10.10:5000/zzyyubuntu:1.2

# Docker容器数据卷

## 坑
Docker挂载主机目录访问如果出现cannot open directory .: Permission denied

解决办法：在挂载目录后多加一个--privileged=true参数即可

如果是CentOS7安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为，

在SELinux里面挂载目录被禁止掉了额，如果要开启，我们一般使用--privileged=true命令，扩大容器的权限解决挂载目录没有权限的问题，也即

使用该参数，container内的root拥有真正的root权限，否则，container内的root只是外部的一个普通用户权限。

## 是什么

有点类似我们Redis里面的rdb和aof文件
将docker容器内的数据保存进宿主机的磁盘中，达到数据持久化的目的

运行一个带有容器卷存储功能的容器实例
**docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录      镜像名**

## 能干吗
将运用与运行的环境打包镜像，run后形成容器实例运行 ，但是我们对数据的要求希望是持久化的

Docker容器产生的数据，如果不备份，那么当容器实例删除后，容器内的数据自然也就没有了。

为了能保存数据在docker中我们使用卷。

特点：

1：数据卷可在容器之间共享或重用数据

2：卷中的更改可以直接实时生效，爽

3：数据卷中的更改不会包含在镜像的更新中

4：数据卷的生命周期一直持续到没有容器使用它为止

**docker run -it --name myu3 --privileged=true -v /tmp/myHostData:/tmp/myDockerData ubuntu /bin/bash**

执行这个命令后，主机的tmp/myHostData就和容器的/tmp/myDockerData进行了绑定，**容器和宿主机数据共享**
1  docker修改，主机同步获得 

2 主机修改，docker同步获得

3 docker容器stop，主机修改，docker容器重启看数据是否同步。*经过测试依旧是同步的*

- docker inspect 容器ID
	- 其中的mount项，可以看到容器挂载的数据卷

- **读写映射规则**
	- 上方的命令使用的是默认读写映射规则，就是rw,可读，可写
	- 但我们有时候需要容器只读，只能读取不能写
		- docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro      镜像名
		- ro=read only 就是只读，容器内进行写时会禁止操作

- 卷的继承与共享
	- 首先容器1完成与宿主机的映射
	- 容器2也想用这个映射，可以不写那么一长串，可以直接继承
		- docker run -it  --privileged=true **--volumes-from** 父类  --name u2 ubuntu


# Docker常规软件安装

## 总体步骤

- 搜索镜像
- 拉取镜像
- 查看镜像
- 启动镜像- ---服务端口映射
- 停止镜像
- 移除镜像

## tomcat

- 首先现在docker hub 上搜索镜像  , docker search tomcat
- 将镜像拉取到本地，docker pull tomcat
- docker images 查看是否有拉取到tomcat
- 运行tomcat镜像实例（也叫运行实例） docker run -d -p 8080:8080 --name tomcat10 tomcat  (这里是后台运行并起名tomcat10)
- 访问8080端口
	- 出现问题---无法访问
	- 解决
		- 可能没有映射端口和关闭防火墙（这个一般操作正常都不会出现）
		- 新版的bug，我们访问的是webapps，但新版这个为空，我们需要**rm -r webapps** 删除webapps，然后**mv webapps.dist webapps**将webapps.dist 重命名为webapps，就可以访问了
- 或者下载免修改版
	- docker pull billygoo/tomcat8-jdk8
	- docker run -d -p 8080:8080 --name mytomcat8 billygoo/tomcat8-jdk8

## mysql

### 简单安装
- 搜索镜像
- 拉取镜像
- 运行实例 docker run -p 3306:3306 --name some-mysql -e MYSQL_ROOT_PASSWORD=wang200301 -d mysql
	- 这里-e代表环境变量 MYSQL_ROOT_PASSWORD是设置root登陆密码
- 进入实例  **docker exec -it mysql /bin/bash**
- 管理员账户登陆 **mysql -uroot -p**
- 然后就可以进行sql语句操作了
- 问题：
	- 中文字符问题
	- 删除容器后，里面的sql数据丢失，删库跑路

### 实际安装
```shell
docker run -d -p 3306:3306 --privileged=true -v /zzyyuse/mysql/log:/var/log/mysql -v /zzyyuse/mysql/data:/var/lib/mysql -v /zzyyuse/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456  --name mysql mysql
```

通过容器数据卷将数据放在主机上而不是容器内

我们可以看到第三个 -v将配置文件也设置了数据卷，我们可以更改数据库字符集配置解决中文乱码问题
进入配置文件目录  我们上边绑定的是 /zzyyuse/mysql/conf
然后 vim mysql.cnf,将下方文件写入
```
[mysql] 
default-character-set = utf8

[mysql.server] 
default-character-set = utf8

[mysqld_safe] 
default-character-set = utf8

[client] 
default-character-set = utf8

[mysqld] 
character_set_server=utf8 
init_connect='SET NAMES utf8'
```

重启数据库就可以看到字符集已更改

更详细可以看这篇博客，配置十分齐全
https://blog.csdn.net/m0_53151031/article/details/123118920

## redis

同样，redis我们同样不能简单的run，所以简单的写了，基本不会那么安装
直接展示实际使用
- 首先，现在宿主机下新建目录/app/redis       **mkdir -p /app/redis**
	- 将配置文件放好，并修改好（这里因为没学过redis，暂时搁置）
- **docker run  -p 6379:6379 --name myr3 --privileged=true -v /app/redis/redis.conf:/etc/redis/redis.conf -v /app/redis/data:/data -d redis redis-server /etc/redis/redis.conf**

# DockerFile

## 是什么
Dockerfile是用来构建docker镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本文件

官网：https://docs.docker.com/engine/reference/builder/

构建步骤
1. 编写Dockerfile文件
2. docker build 命令构建镜像
3. docker run依镜像运行容器

## Dockerfile 构建过程
### 基础知识
1. 每条保留字指令都必须为**大写字母**且后面跟随至少一个参数
2. 指令按照从上到小，顺序执行
3. # 表示注解
4. 每条镜像都会创建一个新的镜像层并对镜像进行提交

### docker执行dockerfile大致流程
1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似 docker commit的操作提交一个新的镜像层
4. docker再基于刚刚提交的镜像运行一个新容器
5. 执行dockerfile中的下一条指令直到所有指令执行完成

### 总结

从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段，

*  Dockerfile是软件的原材料

*  Docker镜像是软件的交付品

*  Docker容器则可以认为是软件镜像的运行态，也即依照镜像运行的容器实例

Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基石
![[Pasted image 20221001101323.png]]
1 Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等;

2 Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行 Docker镜像时会真正开始提供服务;

3 Docker容器，容器是直接提供服务的。

## Dockerfile保留字
https://github.com/docker-library/tomcat
这是tomcat的docker的github，可以在其中可以看到他的Dockerfile

- FROM
	- 基础镜像，当前的新镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板，第一条必须是from
- MAINTAINER
	- 镜像维护者的姓名和邮箱地址
- RUN
	- 容器构建时需要运行的命令
	- 两种格式
		- shell格式 ：RUN yum -y install vim ![[Pasted image 20221001102524.png]]
		- exec格式：![[Pasted image 20221001102551.png]]
	- RUN 是在 docker build时运行
- EXPOSE
	- 当前容器对外暴露的端口
- WORKDIR
	- 指定在创建容器后，终端默认登陆的进来工作目录，一个落脚点
- USER
	- 指定该镜像以什么样的用户去执行，如果都不指定，默认时root
- ENV
	- 用来在构建镜像过程中设置环境变量
		- ENV MY_PATH /usr/mytest
		- 这个环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样；
		- 也可以在其它指令中直接使用这些环境变量，
		- 比如：WORKDIR $MY_PATH
- VOLUME
	- 容器数据卷，用于数据保存和持久化工作
- ADD
	- 将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包
- COPY
	- 类似ADD,拷贝文件和目录到镜像中。将从构建上下文目录中<源路径>的文件/目录复制带新的一层的镜像内的<目标位置>
	- ![[Pasted image 20221001105035.png]]
- CMD
	- 指定容器启动后要干的事情
		- ![[Pasted image 20221001105216.png]]
	- 注意
		- Dockerfile中可以有多个cmd指令，**但只有最后一个生效（意思是后边冲突会覆盖前边的），CMD会被docker run 之后的参数替换
		- 参考tomcat进行理解
			- 官网最后一行命令![[Pasted image 20221001105401.png]]
			- 我们演示自己覆盖![[Pasted image 20221001105431.png]]
	- 和前面RUN的区别
		- CMD是在docker run 时运行的
		- RUN是在docker build时运行的
- ENTRYPOINT
	- 也是用来指定一个容器启动时运行的命令
	- 类似于CMD指令，**但是ENTRYPOINT*不会*被docker run后面的命令覆盖，而且这些命令行参数会被当做参数送给ENTRYPOINT指令指定的程序**
	- 案例说明![[Pasted image 20221001110237.png]]
	- 优点
		- 在执行docker run 时可以指定ENTRYPOINT运行所需的参数
	- 注意
		- 如果Dockerfile中存在多个ENTRYPOINT，**仅最后一个生效（意思是后边的相同参数会覆盖前边的）**
## 案例

### 自定义镜像mycentosjava8

#### 要求
- Centos7镜像具备vim+ifconfig+jdk8
- JDK下载镜像地址
	- 官网https://www.oracle.com/java/technologies/downloads/#java8
	- ![[Pasted image 20221001123712.png]]
#### 编写
准备编写Dockerfile文件----***D要大写***

同时将jdk放到与Dockerfile一个文件夹
这里注意from使用centos时要使用7，不能直接写centos，因为8的源已经不支持，所以不能使用yum命令
```Dockerfile
FROM centos:7

MAINTAINER zzyy<zzyybs@126.com>

ENV MYPATH /usr/local

WORKDIR $MYPATH

#安装vim编辑器

RUN yum -y install vim

#安装ifconfig命令查看网络IP

RUN yum -y install net-tools

#安装java8及lib库

RUN yum -y install glibc.i686

RUN mkdir /usr/local/java

#ADD 是相对路径jar,把jdk-8u171-linux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置

ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/

#配置java环境变量

ENV JAVA_HOME /usr/local/java/jdk1.8.0_171

ENV JRE_HOME $JAVA_HOME/jre

ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH

ENV PATH $JAVA_HOME/bin:$PATH

EXPOSE 80

CMD echo $MYPATH

CMD echo "success--------------ok"

CMD /bin/bash
```

#### 构建

- dockers build -t 新镜像名字:TAG .
	- docker build -t centosjava8:1.5 .
	- ***注意TAG后边有一个空格和一个点***

#### 运行
之后我们就会发现image里有新的镜像
可以docker run 看一下


### 虚悬镜像

#### 是什么
仓库名、标签都是`<none>`的镜像，俗称dangling image

docker build .  这种不写名称和tag的build就会出现

#### 查看
docker image ls -f dangling=true
![[Pasted image 20221001132007.png]]


#### 删除
docker image prune
虚悬镜像已经失去存在价值，可以删除
![[Pasted image 20221001132029.png]]
# Docker微服务实战

创建一个微服务模块，注意：不能是继承空父类的模块，父类需要是springboot的starter，不然打包时不会打包依赖

对微服务进行打包，然后传入docker服务器

编写Dockerfile
```Dockerfile
# 基础镜像使用java

FROM java:8

# 作者

MAINTAINER zzyy

# VOLUME 指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp

VOLUME /tmp

# 将jar包添加到容器中并更名为zzyy_docker.jar

ADD docker_boot-0.0.1-SNAPSHOT.jar zzyy_docker.jar

# 运行jar包

RUN bash -c 'touch /zzyy_docker.jar'

ENTRYPOINT ["java","-jar","/zzyy_docker.jar"]

#暴露6001端口作为微服务

EXPOSE 6001
```

将jar包和Dockerfile放在一个目录下/mydocker

构建镜像：docker build -t zzyy_docker:1.6 .

运行容器： docker run -d -p 6001:6001 zzyy_docker:1.6

可以访问一下模块中的controller方法

一些注意点：模块的java版本应该和容器的版本相同

# Docker网络

## 是什么
- docker不启动，默认网络情况，三种![[Pasted image 20221001162703.png]]
- docker启动后![[Pasted image 20221001192622.png]]
## 常用命令
- 所有的命令![[Pasted image 20221001192816.png]]
- 查看网络
	- docker network ls

- 查看网络源数据
	- docker network inspect  XX网络名字

- 删除网络
	- docker network rm XX网络名字
- 案例
	- ![[Pasted image 20221001193110.png]]
## 能干吗

容器间的互联通信以及端口映射

容器IP变动时候可以通过服务名直接网络通信而不受到影响


## 网络模式

### 总体介绍

![[Pasted image 20221001193617.png]]
bridge模式：使用--network bridge指定，**默认使用docker0**
host模式：使用--network host指定
none模式：使用--network none指定
container模式：使用--network container：NAME或者容器ID指定

### 容器实例内默认网络IP生产规则
结论：**docker容器内部的ip是会变动的**
![[Pasted image 20221001195229.png]]
![[Pasted image 20221001195237.png]]
![[Pasted image 20221001195243.png]]

### 案例说明

#### bridge
Docker 服务默认会创建一个 docker0 网桥（其上有一个 docker0 内部接口），该桥接网络的名称为docker0，它在内核层连通了其他的物理或虚拟网卡，这就将所有容器和本地主机都放到同一个物理网络。Docker 默认指定了 docker0 接口 的 IP 地址和子网掩码，让主机和容器之间可以通过网桥相互通信。

1 Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址，称为Container-IP，同时Docker网桥是每个容器的默认网关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够通过容器的Container-IP直接通信。

2 docker run 的时候，没有指定network的话默认使用的网桥模式就是bridge，使用的就是docker0。在宿主机ifconfig,就可以看到docker0和自己create的network(后面讲)eth0，eth1，eth2……代表网卡一，网卡二，网卡三……，lo代表127.0.0.1，即localhost，inet addr用来表示网卡的IP地址

3 网桥docker0创建一对对等虚拟设备接口一个叫veth，另一个叫eth0，成对匹配。

   3.1 整个宿主机的网桥模式都是docker0，类似一个交换机有一堆接口，每个接口叫veth，在本地主机和容器内分别创建一个虚拟接口，并让他们彼此联通（这样一对接口叫veth pair）；

   3.2 每个容器实例内部也有一块网卡，每个接口叫eth0；

   3.3 docker0上面的每个veth匹配某个容器实例内部的eth0，两两配对，一一匹配。

 通过上述，将宿主机上的所有容器都连接到这个内部网络上，两个容器在同一个网络下,会从这个网关下各自拿到分配的ip，此时两个容器的网络是互通的。
 ![[Pasted image 20221001201256.png]]
 
#### host
直接使用宿主机的 IP 地址与外界进行通信，不再需要额外进行NAT 转换。

容器将不会获得一个独立的Network Namespace， 而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡而是使用宿主机的IP和端口。
![[Pasted image 20221001201935.png]]
**代码**
警告：docker run -d -p 8083:8080 --network host --name tomacat83 billygoo/tomcat8/jdk8
![[Pasted image 20221001202137.png]]
   问题：

     docke启动时总是遇见标题中的警告

原因：

    docker启动时指定--network=host或-net=host，如果还指定了-p映射端口，那这个时候就会有此警告，

并且通过-p设置的参数将不会起到任何作用，端口号会以主机端口号为主，重复时则递增。

解决:

    解决的办法就是使用docker的其他网络模式，例如--network=bridge，这样就可以解决问题，或者直接无视。。。。O(∩_∩)O哈哈~

也就是说host模式是不能使用端口映射的

---
正确：
docker run -d --network host --name tomacat83 billygoo/tomcat8/jdk8
#### none
在none模式下，并不为Docker容器进行任何网络配置。 

也就是说，这个Docker容器没有网卡、IP、路由等信息，只有一个lo

需要我们自己为Docker容器添加网卡、配置IP等。

案例：
docker run -d -p 8084:8080 --network none --name tomacat83 billygoo/tomcat8/jdk8



#### container
container⽹络模式 

新建的容器和已经存在的一个容器共享一个网络ip配置而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP、端口范围等。同样，两个容器除了网络方面，其他的如文件系统、进程列表等还是隔离的。
![[Pasted image 20221001203305.png]]

如果使用一个tomcat和另一个tomcat共享，一个 -p 8086：8080 一个-p 8087：8080，这回导致同一个ip用一个端口两个占用，导致错误
可以使用alpine这种不需要暴露端口的进行验证查看

如果alpine2使用alpine1的网络，那么停用alpine1会导致alpine2网卡消失
#### 自定义网络

docker run -d -p 8081:8080 --name tomcat81 billygoo/tomcat8-jdk8
docker run -d -p 8081:8080 --name tomcat81 billygoo/tomcat8-jdk8

创建上述两个容器，并进入容器内部

按照ip地址是可以ping通的
![[Pasted image 20221001210218.png]]
但按照服务名无法ping通
![[Pasted image 20221001210235.png]]
**默认的bridge网络并不维护服务名**

我们创建自定义的网络
![[Pasted image 20221001210325.png]]

然后使用这个网络创建容器

docker run -d -p 8081:8080 --network haruka --name tomcat81 billygoo/tomcat8-jdk8
docker run -d -p 8082:8080 --network haruka --name tomcat82 billygoo/tomcat8-jdk8

我们发现可以使用服务名进行ping通

***自定义网络本身就维护好了主机名和ip的对应关系（ip和域名都能通）***

# Docker-compose容器编排
## 是什么
Compose 是 Docker 公司推出的一个工具软件，可以管理多个 Docker 容器组成一个应用。你需要定义一个 YAML 格式的配置文件docker-compose.yml，写好多个容器之间的调用关系。然后，只要一个命令，就能同时启动/关闭这些容器
## 能干嘛
 docker建议我们每一个容器中只运行一个服务,因为docker容器本身占用资源极少,所以最好是将每个服务单独的分割开来但是这样我们又面临了一个问题？

如果我需要同时部署好多个服务,难道要每个服务单独写Dockerfile然后在构建镜像,构建容器,这样累都累死了,所以docker官方给我们提供了docker-compose多服务部署的工具

例如要实现一个Web微服务项目，除了Web服务容器本身，往往还需要再加上后端的数据库mysql服务容器，redis服务器，注册中心eureka，甚至还包括负载均衡容器等等。。。。。。

Compose允许用户通过一个单独的docker-compose.yml模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

可以很容易地用一个配置文件定义一个多容器的应用，然后使用一条指令安装这个应用的所有依赖，完成构建。Docker-Compose 解决了容器与容器之间如何管理编排的问题。

## 下载

官网：https://docs.docker.com/compose/compose-file/compose-file-v3/

官网下载：https://docs.docker.com/compose/install

新版本docker自带compose(准确来说：是按照开头的安装步骤我们已经安装了)

## 核心概念

- 一文件--docker-compose.yml
- 两要素
	- 服务(service)--一个个应用容器实例，比如订单微服务、库存微服务、mysql容器、nginx容器、或者redis容器
	- 工程(project)--由一组关联的应用容器组成的一个完整业务单元，在docker-compose.yml中定义

## 使用步骤
1. 编写Dockerfile定义各个微服务应用并构建出对应的镜像文件
2. 使用docker-compose.yml定义一个完整业务单元，安排好整体应用中的各个容器服务。
3. 最后，执行docker-compose up命令来启动并运行整个应用程序，完成一键部署上线

## Compose常用命令
```shell
docker-compose -h                           # 查看帮助

docker-compose up                           # 启动所有docker-compose服务

docker-compose up -d                        # 启动所有docker-compose服务并后台运行

docker-compose down                         # 停止并删除容器、网络、卷、镜像。

docker-compose exec  yml里面的服务id                 # 进入容器实例内部  docker-compose exec docker-compose.yml文件中写的服务id /bin/bash

docker-compose ps                      # 展示当前docker-compose编排过的运行的所有容器

docker-compose top                     # 展示当前docker-compose编排过的容器进程

docker-compose logs  yml里面的服务id     # 查看容器输出日志

docker-compose config     # 检查配置

docker-compose config -q  # 检查配置，有问题才有输出

docker-compose restart   # 重启服务

docker-compose start     # 启动服务

docker-compose stop      # 停止服务
```
***找不到命令就去掉横线***
## 微服务编排

首先我们准备一个微服务工程docker_boot,一个mysql容器实例，一个redis容器实例

微服务工程会调用mysql和redis实例

如果 不使用compose，我们需要手动一个个run，按顺序依次启动
并且我们微服务工程的数据库配置需要设置固定ip，如果ip变动就会寄

那么我们使用compose

编写docker-compose.yml文件

```yml
version: "3"

services:

  microService:

    image: zzyy_docker:1.6

    container_name: ms01

    ports:

      - "6001:6001"

    volumes:

      - /app/microService:/data

    networks: 

      - atguigu_net 

    depends_on: 

      - redis

      - mysql

  redis:

    image: redis:6.0.8

    ports:

      - "6379:6379"

    volumes:

      - /app/redis/redis.conf:/etc/redis/redis.conf

      - /app/redis/data:/data

    networks: 

      - atguigu_net

    command: redis-server /etc/redis/redis.conf

  mysql:

    image: mysql:5.7

    environment:

      MYSQL_ROOT_PASSWORD: '123456'

      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'

      MYSQL_DATABASE: 'db2021'

      MYSQL_USER: 'zzyy'

      MYSQL_PASSWORD: 'zzyy123'

    ports:

       - "3306:3306"

    volumes:

       - /app/mysql/db:/var/lib/mysql

       - /app/mysql/conf/my.cnf:/etc/my.cnf

       - /app/mysql/init:/docker-entrypoint-initdb.d

    networks:

      - atguigu_net

    command: --default-authentication-plugin=mysql_native_password #解决外部无法访问

networks: 

   atguigu_net:
```

对上述文件的一些解释：
我们默认使用version 3
然后每个服务services的配置项其实都和我们run的时候一一对应，应该很好理解
networks不需要提前创建
先后顺序是看是否由depends_on

然后执行启动命令就可以启动

启动后，执行关闭和开启这几个容器就是同步的

# Portainer--轻量级可视工具

## 是什么
Portainer 是一款轻量级的应用，它提供了图形化界面，用于方便地管理Docker环境，包括单机环境和集群环境。

## 安装

官网：https://www.portainer.io/

下载地址：https://docs.portainer.io/start/install/server/docker/linux

下载方式
首先，创建 Portainer Server 将用于存储其数据库的卷：
docker volume create portainer_data

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
![[Pasted image 20221002093503.png]]
安装后访问https://ip:9443,登陆。
自己设置的密码是 haruka
wang200301


不傻的应该看界面都知道怎么用


# GIG容器重量级监控系统介绍

原生命令：docker stats
![[Pasted image 20221002094628.png]]
可以看到容器的各个情况

通过docker stats命令可以很方便的看到当前宿主机上所有容器的CPU,内存以及网络流量等数据，一般小公司够用了。。。。

但是，
docker stats统计结果只能是当前宿主机的全部容器，数据资料是实时的，没有地方存储、没有健康指标过线预警等功能


CAdvisor监控收集+InfluxDB存储数据+Granfana展示图表
![[Pasted image 20221002094948.png]]
![[Pasted image 20221002094956.png]]

![[Pasted image 20221002095002.png]]
![[Pasted image 20221002095017.png]]

```yml
version: '3.1'

volumes:

  grafana_data: {}

services:

 influxdb:

  image: tutum/influxdb:0.9

  restart: always

  environment:

    - PRE_CREATE_DB=cadvisor

  ports:

    - "8083:8083"

    - "8086:8086"

  volumes:

    - ./data/influxdb:/data

 cadvisor:

  image: google/cadvisor

  links:

    - influxdb:influxsrv

  command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxsrv:8086

  restart: always

  ports:

    - "8080:8080"

  volumes:

    - /:/rootfs:ro

    - /var/run:/var/run:rw

    - /sys:/sys:ro

    - /var/lib/docker/:/var/lib/docker:ro

 grafana:

  user: "104"

  image: grafana/grafana

  user: "104"

  restart: always

  links:

    - influxdb:influxsrv

  ports:

    - "3000:3000"

  volumes:

    - grafana_data:/var/lib/grafana

  environment:

    - HTTP_USER=admin

    - HTTP_PASS=admin

    - INFLUXDB_HOST=influxsrv

    - INFLUXDB_PORT=8086

    - INFLUXDB_NAME=cadvisor

    - INFLUXDB_USER=root

    - INFLUXDB_PASS=root
```

启动 docker compose up

会分别在8080，8083，3000这三个端口运行，都有对应的图形化界面，可以访问查看

前两个没什么，3000是grafana的展示界面，需要配置

首先访问，默认的账户密码是 admin/admin

首先，我们要其配置数据源
![[Pasted image 20221002103159.png]]

选择influxdb数据源
![[Pasted image 20221002103216.png]]

![[Pasted image 20221002103225.png]]
url使用服务名访问，因为我们配置了自定义网络
![[Pasted image 20221002103300.png]]
数据库使用我们cadvisor收集数据存储到的数据库


然后创建显示面板panel
![[Pasted image 20221002103443.png]]
![[Pasted image 20221002103450.png]]
![[Pasted image 20221002103636.png]]

![[Pasted image 20221002104533.png]]
配置想展示的数据库


![[Pasted image 20221002104753.png]]

配置线条

保存并应用即可

