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
	- 建议配置密码时也配置`masterauth "wang200301"`
	- 这样后边主从配置可以更简单


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
- 工作目录，默认是当前目录，也就是运行redis-server的命令，日志，**持久化**等文件会保存在这个目录
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

> [!info] 提示 
> 注：发布的消息没有持久化，如果在之后订阅的客户端收不到hello，只能收到订阅后发布的消息


---


# 新数据类型

## Bitmaps
>现代计算机用二进制（位） 作为信息的基础单位， 1个字节等于8位， 例如“abc”字符串是由3个字节组成， 但实际在计算机存储时将其用二进制表示， “abc”分别对应的ASCII码分别是97、 98、 99， 对应的二进制分别是01100001、 01100010和01100011，如下图
>![[Pasted image 20221008222253.png]]
>合理地使用操作位能够有效地提高内存使用率和开发效率。

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：
1. Bitmaps本身不是一种数据类型， 实际上它就是字符串（key-value） ， 但是它可以对字符串的位进行操作。
2. Bitmaps单独提供了一套命令，所以在中使用和使用字符串的方法不太相同。可以把想象成一个以位为单位的数组，数组的每个单元只能存储0和1，数组的下标在中叫做偏移量
![[Pasted image 20221008222358.png]]


### 命令
我们可以从命令来理解这个数据类型

- setbit
`setbit<key><offset><value>设置Bitmaps中某个偏移量的值（0或1）# offset:偏移量从0开始`

setbit user 2 1
就是吧一个二进制数的偏移2的位置设置为1，也就是说第三个位置为1
![[Pasted image 20221008224705.png]]

如果我们设置第52位，这个就会以字节（8bit）为单位加长吗，直到满足这个偏移量

我们就可以用用这个来为用户id做今天是否访问的设置

id为53333333，就是第53333333位bit的值为1

>[!info] 提示
>很多应用的用户id以一个指定数字（例如10000） 开头， 直接将用户id和Bitmaps的偏移量对应势必会造成一定的浪费， 通常的做法是每次做setbit操作时将用户id减去这个指定数字。
>在第一次初始化Bitmaps时， 假如偏移量非常大， 那么整个初始化过程执行会比较慢， 可能会造成Redis的阻塞。


- getbit
`getbit<key><offset> # 获取Bitmaps中某个偏移量的值`

- bitcount
	- 统计字符串被设置为1的bit数。一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。start 和 end 参数的设置，都可以使用负数值：比如 -1 表示最后一个位，而 -2 表示倒数第二个位，start、end 是指bit组的字节的下标数，二者皆包含。

- bittop
```shell
bitop  and(or/not/xor) <destkey> [key…]
#bitop是一个复合操作， 它可以做多个Bitmaps的and（交集） 、 or（并集） 、 not（非） 、 xor（异或） 操作并将结果保存在destkey中。
```

`bitop and user user1 user2`

就是将user1 和user2取并集，然后保存到user中


### Bitmaps与set对比

![[Pasted image 20221008225555.png]]
![[Pasted image 20221008225613.png]]

很显然，Bitmaps在多用户，多活跃的场景因为一个角色只占用一个1位而具有大量优势，但在低活跃的场景，因为用户群体存在，我们就需要维护那么多，而set是按需分配，所以就不如set



## HyperLogLog
在工作当中，我们经常会遇到与统计相关的功能需求，比如统计网站PV（PageView页面访问量）,可以使用Redis的incr、incrby轻松实现。但像UV（UniqueVisitor，独立访客）、独立IP数、搜索记录数等需要去重和计数的问题如何解决？这种求集合中不重复元素个数的问题称为基数问题。

解决基数问题有很多种方案：

（1）数据存储在MySQL表中，使用distinct count计算不重复个数

（2）使用Redis提供的hash、set、bitmaps等数据结构来处理

以上的方案结果精确，但随着数据不断增加，导致占用空间越来越大，对于非常大的数据集是不切实际的。

能否能够降低一定的精度来平衡存储空间？Redis推出了HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

**但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以HyperLogLog 不能像集合那样，返回输入的各个元素。**


**什么是基数?**

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(**不重复元素个**数)为5。 基数估计就是在误差可接受的范围内，快速计算基数。

### 命令
-   **pfadd**
-   **pfcount**

```shell
pfadd <key>< element> [element ...]   #添加指定元素到 HyperLogLog 中
pfcount<key> [key ...] # 计算HLL的近似基数，可以计算多个HLL，比如用HLL存储每天的UV，计算一周的UV可以使用7天的UV合并计算即可

```


-   **pfmerge**

```shell
pfmerge<destkey><sourcekey> [sourcekey ...]  #将一个或多个HLL合并后的结果存储在另一个HLL中，比如每月活跃用户可以使用每天的活跃用户来合并计算可得

```


![[Pasted image 20221009074347.png]]
就是将hll1和hll2的基数合并，然后存储到hll3中

>[!note] 注：
>他这个和set区别是他不存元素，专门用于计算基数的，而且是通过数学概率统计的，有一定的误差但误差在允许范围类，因为不存储所以不怎么耗费内存，但是因为不存元素，你加了什么也取不出来，只能知道他们的基数
## Geospatial

Redis 3.2 中增加了对GEO类型的支持。GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

### 命令
-   **geoadd**
```shell
geoadd<key>< longitude><latitude><member> [longitude latitude member...]  # 添加地理位置（经度，纬度，名称）
```

1.  两极无法直接添加，一般会下载城市数据，直接通过 Java 程序一次性导入。
2.  有效的经度从 -180 度到 180 度。有效的纬度从 -85.05112878 度到 85.05112878 度。
3.  当坐标位置超出指定范围时，该命令将会返回一个错误。
4.  已经添加的数据，是无法再次往里面添加的。

-   **geopos**

```shell
geopos  <key><member> [member...]  #获得指定地区的坐标值
```

-   **geodist**

```shell
geodist<key><member1><member2>  [m|km|ft|mi ]  #获取两个位置之间的直线距离
```

单位：

m 表示单位为米[默认值]。

km 表示单位为千米。

mi 表示单位为英里。

ft 表示单位为英尺。

如果用户没有显式地指定单位参数， GEODIST

-   **georadius**

```shell
georadius<key>< longitude><latitude>radius  m|km|ft|mi   # 以给定的经纬度为中心，找出某一半径内的元素 四个参数 -》经度 纬度 距离 单位
```

![[Pasted image 20221009080012.png]]


# Jedis

是Redis官方推荐的java连接开发工具!使用Java操作Redis 中间件!

当然，我们不只有jedis一种操纵的方法

## 导入依赖
```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
</dependency>
```

使用高版本依赖的话会连带引入高版本slf4j，这个有可能报错

如果高版本报slf4j错误，加入依赖

```xml
<dependency>  
    <groupId>org.slf4j</groupId>  
    <artifactId>slf4j-simple</artifactId>  
    <version>1.7.25</version>  
    <scope>compile</scope>  
</dependency>
```


## 使用

没有什么特殊用法，创建一个对象使用即可

```java
@Test  
public void demol(){  
    //创建Jedis对象  
    Jedis jedis = new Jedis("192.168.10.10",6379);  
    jedis.auth("wang200301");  
    Set<String> keys = jedis.keys("*");  
    for (String key : keys) {  
        System.out.println(key);  
    }  
}
```

各种命令和我们cli命令基本一样，可以一一对应

有密码的话，创建完对象需要添加密码
jedis.auth("wang200301");


>[!note] lettcus和jedis
>jedis连接Redis服务器是直连模式，当多线程模式下使用jedis会**存在线程安全问题**，我们每个线程配置一个jedis对象就可以解决，通过配置连接池使每个连接专用，这样整体性能就大受影响
>
>lettcus基于Netty框架进行与Redis服务器连接，底层设计中采用StatefulRedisConnection.StatefulRedisConnection自身是**线程安全**的，可以保障并发访问安全问题，所以一个连接可以被多线程复用。当然lettcus也支持多实例一起工作。springbootmo默认使用lettcus,可以单实例管理


# springboot整合redis

从2.x以后，springboot使用的jedis被lettuce替换，配置文件中使用lettuce

lettuce因为本身是线程安全的，并且一个连接可以被多线程复用，所以默认并不会使用数据连接池

**数据连接池是否启用是自动的，按照是否导入 commons-pool2 的依赖来决定是否启用**

## 依赖导入

```xml
<!--redis连接的依赖-->
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>  
<!--连接池配置，导入就启用连接池，不导入就不启用--> 
<dependency>  
    <groupId>org.apache.commons</groupId>  
    <artifactId>commons-pool2</artifactId>  
</dependency>
```

## 配置文件
```properties
#redis服务器地址  
spring.redis.host=192.168.10.10  
#redis服务器端口  
spring.redis.port=6379  
#密码  
spring.redis.password=wang200301  
#redis数据库索引  
spring.redis.database=0  
# 连接超时时间（毫秒）  
spring.redis.timeout=1800000  
  
# 连接池配置  
#连接池最大连接数（使用负值表示没有限制）  
spring.redis.lettuce.pool.max-active=20  
#最大阻塞等待时间（使用负值表示没有限制）
spring.redis.lettuce.pool.max-wait=-1  
  
#连接池中最大空闲连接  
spring.redis.lettuce.pool.max-idle=5  
#连接池中最小空闲连接  
spring.redis.lettuce.pool.min-idle=0
```


## 使用
```java
@RestController  
@RequestMapping("/redisTest")  
public class RedisTestController {  
  
    @Autowired  
    private StringRedisTemplate redisTemplate;  
  
    @GetMapping  
    public String testRedis(){  
        User user = new User("cv战士", 12);  
        redisTemplate.opsForValue().set("user222", String.valueOf(user));  
        return redisTemplate.opsForValue().get("user222");  
    }  
}
```

>[!info] redisTemplate
>默认的redisTemplate的序列化是JDK序列化，这就导致插入数据库的key和value会乱码，我们使用String序列化
>两种方法
>我们写一个自定义的redisTemplate类，放入bean中，更改其的序列化设置
>直接使用StringRedisTemplate，这个就是使用String序列化的redisTemplate

>[!note] 连接池生效的前提下, 最小空闲连接是 5 ,redis 客户端的连接数为什么还是 1 呢?
>经测试发现，基于 Lettuce 连接 redis， 开始会先创建一个连接实例，当一个实例不够用了才会去创建新的连接实例直到数量达到 max-active，请求高峰过去后，会先维持最大空闲连接一段时间，之后逐渐减少至最小空闲连接


# 事务和锁机制

Redis事务是一个单独的隔离操作，事务中的所有命令斗湖序列化、按顺序地执行。事务在执行地过程中，不会被其他客户端发送来的命令请求所打断。

>[!note]
Redis事务地主要作用就是**串联多个命令防止别的命令插队**


## Multi、Exec、discard

从输入Multi命令开始，输入地命令都会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前地命令队列中地命令依次执行

组队过程中可以通过discard来放弃组队

![[Pasted image 20221009131739.png]]
示例：![[Pasted image 20221009132144.png]]

## 事务的错误处理

- 组队过程中某个命令出现了报告错误，执行时整个的所有队列都会被取消
![[Pasted image 20221009132417.png]]
- 执行阶段某个命令出现了错误，则只有报错的命令不会被执行，而其他的命令都会执行，***不会回滚***
![[Pasted image 20221009132530.png]]
组队错误![[Pasted image 20221009132725.png]]
执行错误![[Pasted image 20221009132941.png]]

## 事务冲突

现在有10000快钱，有3个事务一起去请求
一个人想要8000
一个人想要5000
一个人想要3000

三个人分别对比，发现10000比自己的金额大，都进行执行，然后最后的金额剩下 -6000，这很明显是不对的

我们使用锁去解决这个问题

>[!note] 悲观锁
>悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

![[Pasted image 20221009134049.png]]

很显然，每次只有其他人解锁自己才能进行操作，效率很低。


>[!note] 乐观锁
>乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制（写入后会改变版本号，而只有读写的版本号一致才会更新）。乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种check-and-set机制实现事务的。

![[Pasted image 20221009134418.png]]



- watch key [key...]

在执行multi 之前，先执行watch key1 [key2],可以监视一个（或多个）key，如果在事务执行之前这个key（或这些key被其他命令所改动），那么事务将被打断 

- unwatch

取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后，EXEC 命令或DISCARD 命令先被执行了的话，那么就不需要再执行UNWATCH了.

## Redis事务三特性

- 单独的隔离操作
	- 事务中所有命令都会被序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
- 没有隔离级别的概念
	- 队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
- 不保证原子性
	- 事务中如果有一条命令执行失败，其后的命令依然会被执行，没有回滚。


# 秒杀和相关问题

正常写一个购买商品的服务，在高并发的秒杀下肯定会出问题

1. 超卖：没有锁机制的话，并发情况下，只剩一件商品，但被很多线程读取剩一件，都去执行减一就会导致超卖。
2. 库存遗留：如果使用乐观锁解决超卖问题，就会一个线程打断几百上千个并发线程，导致秒杀一段时间了，并且大部分人收到的都是秒杀结束，但还有库存

>[!note] 正常sql
>正常的sql数据库比如mysql，在面对不大的并发量时，面对事务冲突，使用mybatis-plus的乐观锁插件，失败后会自动重试，可以解决问题

>[!note] redis
>面对高并发，我们使用redis数据库，我们并不能直接使用watch乐观锁,这会造成库存遗留问题，因为redis的乐观锁并不会进行重试,并且数据库的压力会很大。如果我们通过脚本进行类悲观锁，或者使用分布式锁，确实解决了超卖和库存遗留问题，并且减轻了数据库压力，但处理很慢用户体验差。

>[!note] 秒杀方案
>redis + mq + mysql 保证库存安全，满足高并发处理，但相对复杂。
>提前将商品放入redis缓存 ，如果缓存不存在，视为没有该商品
>通过对redis的扣除库存后是否超售来决定是否真正的操作数据库，如果没有超售再进行操作数据库，超售了就把库存加回去

```java
/**
 * 扣库存操作,秒杀的处理方案
 * @param orderCode
 * @param skuCode
 * @param num
 * @return
 */
public boolean subtractStock(String orderCode,String skuCode, Integer num) {
	String key = "shop-product-stock" + skuCode;
	Object value = redis.get(key);
	if (value == null) {
		//前提 提前将商品库存放入缓存 ,如果缓存不存在，视为没有该商品
		return false;
	}
	//先检查 库存是否充足
	Integer stock = (Integer) value;
	if (stock < num) {
		LogUtil.info("库存不足");
		return false;
	}
	//不可在这里直接操作数据库减库存，否则导致数据不安全
	//因为此时可能有其他线程已经将redis的key修改了
	//redis 减少库存，然后才能操作数据库
	Long newStock = redis.increment(key, -num.longValue());
	//库存充足
	if (newStock >= 0) {
		LogUtil.info("成功抢购");
		//TODO 真正扣库存操作 可用MQ 进行 redis 和 mysql 的数据同步，减少响应时间
	} else {
		//库存不足，需要增加刚刚减去的库存
		redis.increment(key, num.longValue());
		LogUtil.info("库存不足,并发");
		return false;
	}
	return true;
}
```


# 持久化操作

>[!note] Redis持久化
>redis是保存在内存中的，一旦关机内存中的数据就会消失，所以我们需要对redis的数据进行持久化操作
>redis提供了两种不同的持久化方式
>RDB（Redis DataBase）
>AOF (Append Of File)

---

## RDB

>[!note] RDB
>在指定的时间间隔内将[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)中的数据集快照写入磁盘， 也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

### 持久化执行流程

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。 整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是**最后一次持久化后的数据可能丢失。**


### Fork
Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程
在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了“写时复制技术”
一般情况父进程和子进程会共用同一段物理内存，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。
![[Pasted image 20221009160831.png]]
先写到临时区域，然后写入dump.rdb进行持久化

### 相关配置

- defilename
	- 持久化文件的名称，默认为dump.rdb
- dir ./
	- 持久化文件的位置,也是aof日志文件的位置，默认为启动位置
- save time change
	- 配置文件中默认的快照设置，配置快照触发条件
	- 默认为![[Pasted image 20221009161216.png]]
	- ![[Pasted image 20221009161237.png]]
- stop-writes-on-bgsave-error yes
	- 当Redis无法写入磁盘的话，直接关掉Redis的写操作。推荐yes
- rdbcompression yes
	- 压缩文件
	- 对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。推荐yes.
- rdbchecksum yes
	- 检查完整性
	- 在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能->推荐yes


### 恢复与停止

如果想要使用快照对数据库进行会恢复，只需要把快照放到对应的路径，并使用相应的名称，redis启动时会自动读取

动态停止RDB：`redis-cli config set save “” `,就是禁用自动save

### 优点和劣势

>[!note] 优点
>适合大规模的数据恢复
>对数据完整性和一致性要求不高更适合使用
>节省磁盘空间
   恢复速度快

>[!note] 劣势
> Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
> 虽然Redis在fork时使用了**写时拷贝技术**,但是如果数据庞大时还是比较消耗性能。
> 在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改

![[Pasted image 20221009162102.png]]

## AOF

>[!note] AOF
>以**日志**的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来(**读操作不记录**)， **只许追加文件但不可以改写文件**，redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

### 持久化执行流程

（1）客户端的请求写命令会被append追加到AOF缓冲区内；

（2）AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中；

（3）AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

（4）Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；
![[Pasted image 20221009162654.png]]

### 相关配置

>[!note] 开启和冲突
> AOF默认是不开启的，需要在配置文件中开启
> AOF和RDB同时开启的时，redis听谁的？
> 系统使用AOF的数据，因为**数据不会丢失**

![[Pasted image 20221009162919.png]]

AOF的恢复和RDB一样，就是开启AOF，然后把文件放到对应位置，改为对应名字，redis启动的时候就会对应读取

>[!note] 异常恢复
>如果遇到**AOF文件损坏**，我们可以通过命令   /usr/local/redis/bin/redis-check-aof-fix  appendonly.aof对aof文件进行修复，然后再启动redis

- appendonly no
	- yes就是开启AOF
- appendfilename "appendonly.aof"
	- 文件名
- appendfsync everysec
	- AOF同步频率设置
	- appendfsync always：始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好
	- appendfsync everysec：每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。
	- appendfsync no：redis不主动进行同步，把同步时机交给操作系统。


### Rewrite压缩

AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制, 当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集.可以使用命令bgrewriteaof.


**原理**
AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，redis4.0版本后的重写，是指上就是把rdb 的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。

**no-appendfsync-on-rewrite：**

如果 no-appendfsync-on-rewrite=yes ,不写入aof文件只写入缓存，用户请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能）

如果 no-appendfsync-on-rewrite=no, 还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低）

触发机制，何时重写

Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

**重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。**

auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）

auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写。

例如：文件达到70MB开始重写，降到50MB，下次什么时候开始重写？100MB

系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size,

如果Redis的AOF当前大小>= base_size +base_size100% (默认)且当前大小>=64mb(默认)的情况下，Redis会对AOF进行重写。

### 优势和劣势

>[!note] 优势
>备份机制更稳健，丢失数据概率更低。
>可读的日志文本，通过操作AOF

>[!note] 劣势
>比起RDB占用更多的磁盘空间。
>恢复备份速度要慢。
>每次读写都同步的话，有一定的性能压力。
>存在个别Bug，造成恢复不能

## 总结

官方推荐两个都启用。

如果对数据不敏感，可以选单独用RDB。

不建议单独用 AOF，因为可能会出现Bug。

- RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储

- AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾.

- Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大

- 只做缓存：如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式.

- 同时开启两种持久化方式

- 在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据, 因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.

- RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？

- 建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)， 快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。

>[!note] 建议
>两者都开启
>因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够了，只保留save 900 1这条规则。
>如果使用AOF，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。
>代价,一是带来了持续的IO，二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。
>只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。默认超过原大小100%大小时重写可以改到适当的数值。

# 主从复制

>[!note] 主从复制
>主机数据更新后根据配置和策略， 自动同步到备机的master/slaver机制，**Master**以写为主，**Slave以读为主**
>读写分离：主服务器主要进行写，从服务器主要进行读，减缓压力
>容灾快速恢复：一台从服务器挂掉，还能读取其他从服务器
![[Pasted image 20221009214629.png]]

## 配置主从复制

我们可以开启多台机器每台机器启动一台redis，也可以在一台机器上启动多个实例

这里写出一台机器上启动多个redis实例的配置

首先确定一个公共的配置文件/myredis/redis.conf（或者将配置文件移到这里）

然后创建三个文件redis6379.conf、redis6381.conf、redis6380.conf内容如下
```shell
######################redis6379.conf#######################
include /myredis/redis.conf
pidfile /var/run/redis_6379.pid
port 6379
dbfilename dump6379.rdb

######################redis6380.conf#######################
include /etc/redis.conf
pidfile /var/run/redis_6380.pid
port 6380
dbfilename dump6380.rdb
slaveof  127.0.0.1 6379
masterauth wang200301

######################redis6381.conf#######################
include /etc/redis.conf
pidfile /var/run/redis_6380.pid
port 6380
dbfilename dump6380.rdb
slaveof  127.0.0.1 6379
masterauth wang200301
```

**如果开启了AOF记得更换AOF的名字，当然如果在多台机器上随便**

>[!note] 配从不配主
>`slaveof <ip><port>`
>在从机上配置主机的ip和端口 在6380和6381上执行: slaveof 127.0.0.1 6379
>有密码记得配置masterauth（如果源文件配置了就不用管）

分别启动这三个redis实例

使用命令查看三个端口的redis
ps -ef | grep redis
![[Pasted image 20221010070505.png]]


我们进入三台机器，运行命令`info replication`,可以看到他们的主从复制相关信息
![[Pasted image 20221010070626.png]]


>[!note] 注
>主机上写入的操作，从机上会同步，可以从机同步读取数据
>但在从机上写数据报错


## 问题和原理

### 一主二仆

主机挂掉后，从机原地待命，等待主机重新启动，此时仍然可以读取到宕机之前的数据，但依旧不能写


### 薪火相传
![[Pasted image 20221010074005.png]]
-   上一个Slave可以是下一个slave的Master，Slave同样可以接收其他 slaves的连接和同步请求，那么该slave作为了链条中下一个的master, 可以有效减轻master的写压力,去中心化降低风险。
-   用 slaveof
    
    中途变更转向:会清除之前的数据，重新建立拷贝最新的
    
    风险是一旦某个slave宕机，后面的slave都没法备份
    
    主机挂了，从机还是从机，无法写数据了
    
### 反客为主

为从机中执行 slaveof no one，会断开和主机的联系，并自动升级为从机


### 复制原理

Slave启动成功连接到master后会发送一个sync命令

Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令， 在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步

全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步

但是只要是重新连接master,一次完全同步（全量复制)将被自动执行

![[Pasted image 20221010072306.png]]


## 哨兵模式

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。
![[Pasted image 20221010074604.png]]

先配置一主二从的主从复制服务

然后写一个哨兵配置文件sentinel.conf。

```shell
sentinel monitor mymaster 127.0.0.1 6379 1

#这个其实是重写所有redis的配置文件，设置redis的密码
sentinel auth-pass mymaster wang200301  
#设置sentinel的密码
requirepass wang200301
```

如果redis有密码一定记得配置密码
其中mymaster为监控对象起的服务器名称， 1 为至少有多少个哨兵同意迁移的数量。


然后启动哨兵 
/usr/local/redis/bin/redis-sentinel /myredis/sentinel.conf


>[!note]  运行
>启动烧饼后，如果主机挂掉，哨兵会从从机中选举产生新的主机
>重新启动原主机，原主机会自动变为从机
>**sentinel的更改其实是通过更改配置文件的方式来更改主从的**

>[!note] 从服务器选择策略
>我们可以在从服务器配置文件中设置从服务器的优先级
>slave-priority 100 ，值越小优先级越高
>选择条件依次为
>优先级靠前的
>偏移量最大的：偏移量指获得原主机数据最全的
>runid最小的从服务：每个redis实例启动后都会随机生成一个40位的runid


>[!info] 代码操作
>代码上如何判断使用主从服务器呢？
>我们连接直接连接哨兵，也就是端口写哨兵的端口 
```shell
##redis服务器地址  
#spring.redis.host=192.168.10.10  
##redis服务器端口  
#spring.redis.port=6379  
#密码  
spring.redis.password=wang200301  
  
spring.redis.sentinel.master=mymaster  
spring.redis.sentinel.nodes=192.168.10.10:26379  
spring.redis.sentinel.password=wang200301
```
redis和sentinel的密码都要写

![[Pasted image 20221010091048.png]]

这里看到主服务器下线后，程序连接不到服务器了，等待哨兵反应过来后，已经切换到从服务器



# Redis集群

问题：
容量不够，redis如何进行扩容？
并发写操作，redis如何分摊？
另外，主从模式，薪火相传模式，主机宕机，导致ip地址发生变化，应用程序中配置需要修改对应的主机地址、端口等信息

之前我们是通过**代理主机**来解决，但是redis3.0中提供了解决方案。就是**无中心化集群**配置。

>[!note] 集群
>Redis集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布在这N个节点中，每个节点存储总数据的1/N.Redis集群通过分区来提供一定程序的可用性：即使集群中有一部分节点失效或无法进行通讯，集群也可以继续处理命令请求


## 配置部署

和主从配置相似
引入配置好的模式
```shell
include /myredis/redis.conf
pidfile "/var/run/redis_6391.pid"
port 6391
dbfilename "dump6391.rdb"
cluster-enabled yes
cluster-config-file nodes-6391.conf
cluster-node-timeout 15000
```

cluster-enabled yes 打开集群模式
cluster-config-file nodes-6379.conf 设定节点配置文件名
cluster-node-timeout 15000 设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换。

一台机器上启动多个节点我们需要 更换其中6391到其他节点，如果配置AOF,记得更改appendfilename

如果多台机器那么随便，都写成一样的也行

开启服务

我们要去对应的目录下查看，确保nodes-xxxx.conf生成
![[Pasted image 20221010130237.png]]


然后使用redis解压出来的安装包的redis-cli的命令
```shell
redis-cli --cluster create --cluster-replicas 1 192.168.242.110:6379 192.168.242.110:6380 192.168.242.110:6381 192.168.242.110:6389 192.168.242.110:6390 192.168.242.110:6391

```
后边分别为集群中的ip和端口号

--replicas 1 采用最简单的方式配置集群，一台主机，一台从机，正好三组。



一个集群至少要有三个主节点。

选项 --cluster-replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。

分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个IP地址上。

## 运行
>[!note] slot
>在运行集成集群命令后，会出现“[OK] All 16384 slots covered ”
说明：一个 Redis 集群包含 16384 个插槽（hash slot）， 数据库中的每个键都属于这 16384 个插槽的其中一个， 集群使用公式CRC16(key) %16384 来计算键key属于哪个槽.其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和 。
集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：
节点 A 负责处理 0 号至 5460 号插槽。
节点 B 负责处理 5461 号至 10922 号插槽。
节点 C 负责处理 10923 号至 16383 号插槽。


在集群中设定

>[!note] 故障恢复
>如果主节点下线？从节点能否自动升为主节点？注意：**15秒超时**
>主节点恢复后，主从关系会如何？主节点回来变成从机。
>如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为yes ，那么 ，整个集群都挂掉
>如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为no ，那么，该插槽数据全都不能使用，也无法存储。
>redis.conf中的参数 cluster-require-full-coverage


## 开发

spring 配置集群配置即可

```xml
spring.redis.cluster.nodes=192.168.10.10:3499 192.168.10.10:5555  
spring.redis.password=wang200301
```


## 好处与不足

好处：
-   实现扩容
-   分摊压力
-   无中心配置相对简单

不足
-   多键操作是不被支持的
-   多键的Redis事务是不被支持的。lua脚本不被支持
-   由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，需要整体迁移而不是逐步过渡，复杂度较大。


# Redis应用问题

## 缓存穿透
![[Pasted image 20221010132808.png]]
现象：
1.应用服务器压力变大了
2.redis命中率降低
3.一直查询数据库

出现问题的原因：
1. redis查询不到数据库
2. 出现很多非正常url访问

就是有很多数据比如查询，缓存中没有，就去查询数据库，数据库也没有，然后很多这样的请求一直来，就会导致一直访问数据库，压力过大服务器就会崩溃，一般是黑客攻击。


处理方式

- 对空值缓存：如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟

- 设置可访问的名单（白名单）：使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。、

- 采用布隆过滤器：(布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被 这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

- 进行实时监控：当发现的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务

## 缓存击穿

key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题。

现象:
1.数据库访问压力损失增加
2.redis里面并没有出现大量key过期
3.redis正常运行，但数据库崩溃

原因：
1.redis某个key过期了，恰好这个key当时被大量访问


处理方案：

- 预先设置热门数据：在redis高峰访问之前，把一些热门数据提前存入到redis里面，加大这些热门数据key的时长

- 实时调整：现场监控哪些数据热门，实时调整key的过期时长

- 使用锁：

（1） 就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

（2） 先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key

（3） 当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；

（4） 当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

## 缓存雪崩

![[Pasted image 20221010142734.png]]
现象：
1.数据库压力变大

原因：
在极少时间段，查询大量key的集中过期情况

解决方案
（1） 构建多级缓存架构：nginx缓存 + redis缓存 +其他缓存（ehcache等）

（2） 使用锁或队列：用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况

（3） 设置过期标志更新缓存：记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存。

（4） 将缓存失效时间分散开：比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5

## 分布式锁

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题。

分布式锁主流的实现方案：

-   基于数据库实现分布式锁
-   基于缓存（Redis等）
-   基于Zookeeper

每一种分布式锁解决方案都有各自的优缺点：

-   性能：redis最高
-   可靠性：zookeeper最高

###  基于redis实现分布式锁

redis最简单的分布式锁,SETNX

setnx设置的值，其他人不能更改,其实就是一种锁，解锁就是直接删除数据

```shell
SETNX KEY VALUE # 设置锁 
del key # 删除锁
```

我们还可以设置key的过期时间，使其自动释放。

`expire users 30`

但这样设置的问题：如果设置时间和上锁分开进行的话，可能存在上完锁，服务器down了，就没有设置过期时间。

优化：一起设置,上锁和设置过期时间同时

`set key value nx ex time`

### java代码实现
```java
@GetMapping("testLock")
public void testLock(){
    //1获取锁，setne ,顺便设置过期时间
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111",3,TimeUnit.SECONDS);
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            return;
        }
        //2.2有值就转成成int
        int num = Integer.parseInt(value+"");
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4释放锁，del
        redisTemplate.delete("lock");

    }else{
        //3获取锁失败、每隔0.1秒再获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

但以上代码会产生误删问题
![[Pasted image 20221011080938.png]]
我们可以使用uuid防止误删，类似乐观锁，自己只能删除自己上的锁，不能删除别人上的锁
```java
@GetMapping("testLock")
public void testLock(){
	String uuid = UUID.randomUUID().toString();
    //1获取锁，setne ,顺便设置过期时间
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", uuid,3,TimeUnit.SECONDS);
    //2获取锁成功、查询num的值
    if(lock){
       ...
        String lockUuid = (String)redisTemplate.opsForValue().get("lock");
        if(uuid.equals(lockUuid)){
             //2.4释放锁，del
        	redisTemplate.delete("lock");
        }
    }else{
       ...
    }
}
```

**优化之LUA脚本保证删除的原子性**

![[Pasted image 20221011081554.png]]
解决方法，lua脚本

```java
@GetMapping("testLockLua")
public void testLockLua() {
    //1 声明一个uuid ,将做为一个value 放入我们的key所对应的值中
    String uuid = UUID.randomUUID().toString();
    //2 定义一个锁：lua 脚本可以使用同一把锁，来实现删除！
    String skuId = "25"; // 访问skuId 为25号的商品 100008348542
    String locKey = "lock:" + skuId; // 锁住的是每个商品的数据

    // 3 获取锁
    Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid, 3, TimeUnit.SECONDS);

    // 第一种： lock 与过期时间中间不写任何的代码。
    // redisTemplate.expire("lock",10, TimeUnit.SECONDS);//设置过期时间
    // 如果true
    if (lock) {
        // 执行的业务逻辑开始
        // 获取缓存中的num 数据
        Object value = redisTemplate.opsForValue().get("num");
        // 如果是空直接返回
        if (StringUtils.isEmpty(value)) {
            return;
        }
        // 不是空 如果说在这出现了异常！ 那么delete 就删除失败！ 也就是说锁永远存在！
        int num = Integer.parseInt(value + "");
        // 使num 每次+1 放入缓存
        redisTemplate.opsForValue().set("num", String.valueOf(++num));
        /*使用lua脚本来锁*/
        // 定义lua 脚本
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        // 使用redis执行lua执行
        DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
        redisScript.setScriptText(script);
        // 设置一下返回值类型 为Long
        // 因为删除判断的时候，返回的0,给其封装为数据类型。如果不封装那么默认返回String 类型，
        // 那么返回字符串与0 会有发生错误。
        redisScript.setResultType(Long.class);
        // 第一个要是script 脚本 ，第二个需要判断的key，第三个就是key所对应的值。
        redisTemplate.execute(redisScript, Arrays.asList(locKey), uuid);
    } else {
        // 其他线程等待
        try {
            // 睡眠
            Thread.sleep(1000);
            // 睡醒了之后，调用方法。
            testLockLua();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下四个条件：

- 互斥性。在任意时刻，只有一个客户端能持有锁。

- 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。

- 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

- 加锁和解锁必须具有原子性。
