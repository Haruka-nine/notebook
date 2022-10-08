# Redis概述安装

![[Pasted image 20221007215154.png]]

官网：https://redis.io/


- 去官网下载 压缩包 redis-6.2.7.tar.gz，
	- 这里使用redis 6的最新版本6.2.7
- 解压压缩文件
	- `tar -zxvf redis-6.2.7.tar.gz`
- 安装GCC编译器，（安装linux需要c语言环境，查看是否具有GCC命令 `gcc --version`）
	- `yum install -y gcc`
- 进入解压后的redis文件夹,指定安装目录安装
	- `make PREFIX=/usr/local/redis install`
	- 但建议直接`make`然后`make install`
	- 因为redis的全局命令默认在默认的安装位置/usr/local/bin
	- 也可以之后配置文件然后使用系统命令启动

以下的操作都是直接安装在默认目录的操作
# Redis启动

## 前台启动（不推荐）

安装后直接 redis-server，这是前台启动，启动后我们这个终端就不能使用了，而且关闭终端redis就会关闭

## 后台启动

我们可以拷贝我们的redis.conf文件到其他目录进行备份

然后修改配置文件
-   监听地址，默认是127.0.0.1，会导致只能在本地访问，修改为0.0.0.0则可以在任意IP访问，生产环境不要设置为0.0.0.0
	- `bind 0.0.0.0`
- 守护进程，修改为yes后即可后台运行
	- `daemonsize yes`
- 密码，设置后访问redis必须输入密码
	- `requirepass 123321`


然后我们使用修改后的配置文件启动
如果不是默认目录redis-server需要加路径的！
`redis-server /etc/redis.conf` ,后边是配置文件的路径

>如果启动提示权限不够，`chmod 777 操作`
>比如提示你redis-server 权限不够，就 chmod 777  redis-server 

查看redis是否运行：
`ps -ef | grep redis`

如果想停止，直接利用杀死进程即可：

`kill -9 4083898(对应进程号)`

## 其他配置

-   监听的窗口
	- `port 6379`
- 工作目录，默认是当前目录，也就是运行redis-server的命令，日志，持久化等文件会保存在这个目录
	- 建议更改为确定的目录
	- 默认：`dir ./`  更改 `dir /usr/local/redis`
- 数据库数量，设置为1，代表只能使用一个库，默认有16个库，编号0~15
	- `databases 1`
- 设置redis能够使用的最大内存
	- `maxmemory 512mb`

## 开机自启

以下为自定义目录进行开机自启
1.  首先新建一个系统服务文件：

`vi /etc/systemd/system/redis.service`

内容如下：

```shell
[Unit]
Description=redis-server
After=network.target
    
[Service]
Type=forking
ExecStart=/usr/local/redis/bin/redis-server /etc/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

使其生效（重新加载服务）：

`systemctl daemon-reload`

重新启动redis：

`systemctl start redis`

查看redis状态：

`systemctl status redis`

来停止redis：

`systemctl stop redis`

重启redis：

`systemctl restart redis`

开机自启redis:

`systemctl enable redis`


# Redis简介

端口为6379

默认16个数据库，类似数组下标从0开始，初始默认使用0号库

使用命令 `select <dbid>` 来切换数据库。如 select 8

```shell
#使用redis-cli进入数据库

/usr/local/redis/bin/redis-cli

#登陆

auth wang200301

#切换数据库
select 8
```

![[Pasted image 20221008093252.png]]

统一密码管理，所有的库同样的密码

dbsize 查看当前数据库的key的数量

flushdb清空当前库

flushall 通杀全部库


>redis是多线程+多路IO复用


# 五大数据类型

命令文档：https://redis.io/commands/

## Redis 键 (key)

- `keys *` 查看当前库所有key （匹配：`keys *1`）
- exists key 判断某个key是否存在
- type key 查看你的key是什么类型
- del key 删除指定的key数据
- unlink key 根据value选择非阻塞删除
	- 仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作
- expire key 10 
	- 10秒钟：为给定的key设置过期时间
- ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已经过期

## 字符串 (String)

String 是Redis最基本的类型，你可以理解成与 Memcached 一摸一样的的类型，一个key对应一个value

String 类型是**二进制安全的**。意味着Redis的string 可以包含任何数据，比如jpg图片或者序列化的对象

意味着只要能用字符串表示，都可以存到String类型

String 类型是Redis中最基本的数据类型，一个Redis中字符串value最多可以是512M

### 常用命令

- set key value
	- 增加键值对
	- set 相同的key会进行覆盖
- get key
	- 得到key对应的键值
- append  key value
	- 将value接在 key所对应的值的后边
- strlen key
	- 获取值的长度
- setnx key value
	- 只有key不存在时，才能设置成功，如果key存在就失败
- incr key
	- 将key中储存的数字值增1
	- 只能对数字值操作，如果为空。新值增加1
- decr key
	- 将key中储存的数字值减少1 
- deceby/incrby key 步长
	- deceby k5 20
	- k5减去20 ，根据步长增加或者减少

### 原子性
所谓原子操作是指不会被线程调度机制打断的操作

这种操作一旦开始，就一直运行到结束，中间不会有任何context switch（切换到另一个线程）

1. 在单线程中，能够在单条指令中完成的操作都可以认为是“原子操作”，因为中断只能发生于指令之间
2. 在多线程中，不能被其他线程打断的操作就叫原子操作

Redis单命令的原子性主要得益于Redis的单线程

![[Pasted image 20221008110552.png]]

### 其他命令
- mset key1 value1 key2 value2
	- 同时设置一个或者多个key-value对
- mget key1 key2
	- 同时获取多个key的value值
- msetnx key1 value1 key2 value2
	- 同时设置一个或者多个key-value对，并且仅当**所有的key都不存在才会成功** （**原子性**，一个失败就都失败）
- getrange key 起始位置 结束位置
	- 获得值的范围，类似java的substring，前包，后包
- setrange key 起始位置 value
	- 用value覆写key的内容，从起始位置开始
- setex key 过期时间 value
	- 设置键值的时候设置过期时间，单位秒
- getset key value
	- 以新换旧，设置了新值同时获得旧值

### 数据结构
String的数据结构为简单动态字符串。是可以修改的字符串，内部结构实现上类似于java的ArrayList,采用预分配冗余空间的方式来减少内存的频繁分配

![[Pasted image 20221008112241.png]]
如图所示，内部为字符串实际分配的空间capacity一般要高于实际字符串长度len,当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩1M的空间，需要注意的是字符串最大长度为512M

## 列表(List)
单键多值
Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）

它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会比较差
![[Pasted image 20221008122213.png]]

### 常用命令

- lpush/rpush key value1 value2 value3
	- 从左边/右边插入一个或多个值
- lpop/rpop key  
	- 从左边/右边吐出一个值，值在键在，值亡键亡
- rpoplpush key1 key2
	- 从key1列表右边吐出一个值，插入到key2的左边
- lrange key start stop
	- 按照索引下标获得元素(从左到右)
	- lrange key 0 -1是获取全部元素
- lindex key index 
	- 按照索引下标获取元素（从左到右）
- llen key 
	- 获取列表长度
- linsert key before/after value new value 
	- 在value 的前边/后边插入newvalue
- lrem key n value
	- 从左边删除n个value（从左到右）
- lset key index value
	- 将列表key下标为index的值替换为value

### 数据结构
List的数据结构为快速链表quickList
首先在列表中元素比较少的情况下会使用一个块连续的内存存储，这个结构是ziplist，也即是压缩列表
他将所有的元素紧挨着一起存储，分配的是一块连续的内存
当数据量比较多的时候才会改成quicklist
因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next
![[Pasted image 20221008150612.png]]
Redis将链表和ziplist结合起来组成了quicklist,也就是将多个和ziplist使用双向指针串起来使用。这样既满足了快速的插入和删除性能，又不会出现太大的空间冗余

## 集合（Set）
Redis set对外提供的功能与list类似，是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。
Redis的set是string类型的无需集合。它底层起始是一个value为空的hash表，所以添加，删除，查找的复杂度都是O(1)

### 常用命令
- sadd key value1 value2
	- 将一个或多个member元素加入到集合key中，已经存在的member元素将被忽略
- smembers key
	- 取出该集合的所有值
- sismember key value
	- 判断集合key中是否含有该value值，有1，没有0
- scard key
	- 返回该集合的元素个数
- srem key value1 value2
	- 删除该集合中的某个元素
- spop key
	- 随机从集合中**吐出一个值**
- srandmember key n
	- 随机从集合中取出n个值，**不会从集合中删除**
- smove source destination value 
	- 把集合中一个值从一个集合移动到另一个集合
- sinter key1 key2 
	- 返回两个集合的交集
- sunion key1 key2
	- 返回两个集合的并集元素
- sdiff key1 key2
	- 返回两个集合的差集元素（key1中的，不包含key2中的）

### 数据结构

set的数据结构是dict字典，字典是用哈希表实现的

## 哈希（Hash）

Redis hash 是一个键值对集合。
Redis hash 是一个string类型的field 和value 的映射表，hash特别适用于存储对象

类似java里面的Map<String,Objecy>

用户ID 为查找的key，储存的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储

主要有下面的2种存储方法
![[Pasted image 20221008160159.png]]

如果使用hash存储

![[Pasted image 20221008161742.png]]
### 常用命令
![[Pasted image 20221008161802.png]]

HMSET已经弃用，从4.0版本开始，hset也可以添加多个值

### 数据结构

Hash类型对应的数据结构是两种：ziplist(压缩列表)，hashtable(哈希表)。当field-value长度较短且个数较少时，使用ziplist,否则使用hashtable

## 有序集合Zset(sorted set)

Redis 有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。
不同之处时有序集合的每一个成员都关联了一个评分（score）,这个评分被用来按照从最低分到最高分的方式排序集合中的成员，集合的成员是唯一的，但是评分可以是重复了。
因为元素是有序的，所以你也可以很快的根据评分或者次序来获取一个范围的元素。
访问有序集合的中间元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表。

### 常用命令
![[Pasted image 20221008163333.png]]


![[Pasted image 20221008163941.png]]
### 数据结构
zset是Redis提供的一个非常特殊的数据结构，一方面它等价于java的数据结构Map<String,Double>,可以给每个元素value赋予一个权重score,另外一方面它又类似于TreeSet,内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表。
zset底层使用了两个数据结构
1. hash,hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到对应的score值。
2. 跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表

### 跳跃表

示例分析

有序列表查询出51 
![[Pasted image 20221008164909.png]]

要查找值为51的元素，需要从第一个元素开始依次查找、比较才能找到。共需要6次比较

跳跃表

![[Pasted image 20221008165031.png]]

![[Pasted image 20221008165255.png]]

# 配置文件

-   **Units单位**
>配置大小单位,开头定义了一些基本的度量单位，只支持bytes，不支持bit
>大小写不敏感

![[Pasted image 20221008171748.png]]

---
-   **INCLUDES包含**

>类似jsp中的include，多实例的情况可以把公用的配置文件提取出来

![[Pasted image 20221008171820.png]]

---

- **网络相关配置**

>**bind**
>默认情况bind=127.0.0.1只能接受本机的访问请求
>不写的情况下，无限制接受任何ip,相当于写bind 0.0.0.0
>生产环境肯定要写你应用服务器的地址；服务器是需要远程访问的，所以需要将其注释掉
>如果开启了protected-mode，那么在没有设定bind ip且没有设密码的情况下，Redis只允许接受本机的响应

![[Pasted image 20221008172129.png]]

>  **protected-mode**
>  本机的保护模式，开启后，如果bind没有绑定到一组地址或者没有设置密码的话，就会禁止其他机器访问，只能由本机访问

![[Pasted image 20221008172352.png]]

>  **PORT**
>  端口号

![[Pasted image 20221008172440.png]]

>  **tcp-backlog**
>设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。
>在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。
>注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增
>大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

![[Pasted image 20221008172614.png]]

>**timeout**
>一个空闲的客户端维持多少秒会关闭，0表示关闭该功能。即永不关闭

![[Pasted image 20221008172638.png]]

>**tcp-keepalive**
>对访问客户端的一种心跳检测，每个n秒检测一次。
>单位为秒，如果设置为0，则不会进行Keepalive检测，建议设置成60

![[Pasted image 20221008172714.png]]


---
- **GENERAL通用**

>**daemonize**
>是否为后台进程，设置为yes
>守护进程，后台启动

![[Pasted image 20221008172833.png]]
>**pidfile**
>存放pid文件的位置，每个实例会产生一个不同的pid文件

![[Pasted image 20221008172904.png]]
>**loglevel**
>指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为**notice**
>四个级别根据使用阶段来选择，生产环境选择notice 或者warning

![[Pasted image 20221008172944.png]]

>**logfile**
>日志文件名称

![[Pasted image 20221008173016.png]]

>**databases**
>设定库的数量 默认16，默认数据库为0，可以使用SELECT `<dbid>`命令在连接上指定数据库id

![[Pasted image 20221008173048.png]]

---

- **SECURITY安全**

>**requirpass**
>设置密码

![[Pasted image 20221008173228.png]]


---
- **LIMITS限制**

> **maxclients**
> 设置redis同时可以与多少个客户端进行连接。
> 默认情况下为10000个客户端。
> 如果达到了此限制，redismax number of clients reached

![[Pasted image 20221008173412.png]]

> **maxmemory**
> 建议必须设置，否则，将内存占满，造成服务器宕机
>设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。
>如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。
>但是对于无内存申请的指令，仍然会正常响应，比如GET等。如果你的redis是主redis（说明你的redis有从redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。

![[Pasted image 20221008173514.png]]

>**maxmemory-policy**
>volatile-lru：使用LRU算法移除key，只对设置了过期时间的键；（最近最少使用）
>allkeys-lru：在所有集合key中，使用LRU算法移除key
>volatile-random：在过期集合中移除随机的key，只对设置了过期时间的键
>allkeys-random：在所有集合key中，移除随机的key
>volatile-ttl：移除那些TTL值最小的key，即那些最近要过期的key
>noeviction：不进行移除。针对写操作，只是返回错误信息

![[Pasted image 20221008173627.png]]

>**maxmemory-samples**
>1.  设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个。
>2.  一般设置3到7的数字，数值越小样本越不准确，但性能消耗越小。

![[Pasted image 20221008173732.png]]


# 发布和订阅

## 什么是发布和订阅
-   Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。
-   Redis 客户端可以订阅任意数量的频道。

![[Pasted image 20221008183644.png]]

客户端可以订阅频道

当频道发布消息后，消息就会发送给订阅的客户端

## 演示

>打开一个客户端订阅channel1
>SUBSCRIBE channel1

![[Pasted image 20221008184401.png]]

>打开另一个客户端，给channel1发布消息hello
>publish channel1 hello

![[Pasted image 20221008184424.png]]
返回的1是订阅者数量


> 打开第一个客户端可以看到发送的消息

![[Pasted image 20221008184450.png]]

注：发布的消息没有持久化，如果在之后订阅的客户端收不到hello，只能收到订阅后发布的消息

---


# 新数据类型

