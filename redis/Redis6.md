# Redis6

[TOC]

## NoSQL 数据库

### NoSQL数据库概述

NoSQL ( NoSQL = **Not Only SQL** )，意即 “不仅仅是SQL”，泛指**非关系型的数据库**。 

NoSQL 不依赖业务逻辑方式存储，而以简单的key-value模式存储。因此大大的增加了数据库的扩展能力。

-   不遵循SQL标准。
-   不支持ACID。
-   远超于SQL的性能。



### NoSQL适用场景 

-   对数据高并发的读写
-   海量数据的读写
-   对数据高可扩展性的



### NoSQL不适用场景

-   需要事务支持
-   基于sql的结构化查询存储，处理复杂的关系,需要即席查询。

<span style='color: red;'>**（用不着sql的和用了sql也不行的情况，请考虑用NoSql）**</span>



### NoSQL 产品概述

**Memcache**

| ![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml12228\wps1.png) | ü 很早出现的NoSql数据库ü 数据都在内存中，一般不持久化ü 支持简单的key-value模式，支持类型单一ü 一般是作为缓存数据库辅助持久化的数据库 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

**Redis**

| ![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml12228\wps2.png) | 几乎覆盖了Memcached的绝大部分功能数据都在内存中，支持持久化，主要用作备份恢复除了支持简单的key-value模式，还支持多种数据结构的存储，比如 list、set、hash、zset等。一般是作为缓存数据库辅助持久化的数据库 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

**MongoDB**

| ![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml12228\wps3.png) | ü 高性能、开源、模式自由(schema  free)的***\*文档型数据库\****ü 数据都在内存中， 如果内存不足，把不常用的数据保存到硬盘ü 虽然是key-value模式，但是对value（尤其是***\*json\****）提供了丰富的查询功能ü 支持二进制数据及大型对象ü 可以根据数据的特点***\*替代RDBMS\**** ，成为独立的数据库。或者配合RDBMS，存储特定的数据。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |





## Redis6 概述安装

-   Redis是一个开源的key-value存储系统。
-    和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。
-   这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。
-   在此基础上，Redis支持各种不同方式的排序。
-   与memcached一样，为了保证效率，数据都是缓存在内存中。
-   区别的是Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件。
-   并且在此基础上实现了master-slave(主从)同步。

### Redis6 安装

笔记使用的是 `CentOS7` 版本的 `Linux`，`Redis 6.2.3`。

1.  官网下载 `redis`，官网只提供 `Linux` 版本的压缩包，学习 `redis` 前需要会基本的 `Linux` 操作。

2.  使用 `Xfpt` 传输压缩包到 `Linux` 系统中

3.  安装 C 语言编译环境
    1.  `gcc --version` 查看 `gcc` 版本，有信息输出就代表有 C 语言编译环境
    2.  按顺序输入下面的指令安装 `gcc`
    3.  `yum install centos-release-scl scl-utils-build`
    4.  `yum install -y devtoolset-8-toolchain`
    5.  `scl enable devtoolset-8 bash`
    6.  如果安装过程中有提示都输入 `y` 
    7.  `gcc --version` 查看 `gcc` 版本，检查是否安装成功

4.  `tar -zxvf redis-6.2.3.tar.gz` 解压

5.  `cd redis-6.2.3` 进入目录

6.  在 redis-6.2.3 目录下再次执行 `make` 命令（只是编译好）

    1.  如果没有准备好C语言编译环境，make 会报错—` Jemalloc/jemalloc.h`：没有那个文件
    2.  解决方案：运行 `make distclean` 清除编译文件
    3.  在 redis-6.2.3 目录下再次执行 `make` 命令（只是编译好）

7.  `make install` 安装

8.  `cd /usr/local/bin`，如果这个目录中有文件就表示安装成功。`Redis` 默认安装在这个目录

    ```shell
    # /usr/local/bin中文件的作用
    redis-benchmark:性能测试工具，可以在自己本身运行，看看自己本身性能如何
    redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
    redis-check-dump：修复有问题的dump.rdb文件
    redis-sentinel：Redis集群使用
    redis-server：Redis服务器启动命令
    redis-cli：客户端，操作入口
    ```



### Redis6 启动

#### 前台启动 ( 不推荐 )

执行 `/usr/local/bin` 目录下的 `redis-server` 文件即可。`/usr/local/bin/redis-server` 命令启动。

<span style='color: red;'>**前台启动，命令行窗口不能关闭，否则服务器停止**</span>



#### 后台启动 ( 推荐 )

1.  `cd /opt/redis/redis-6.2.3/` 进入目录
2.  `cp redis.conf /etc/redis.conf` 拷贝一份 `redis.conf` 到其他目录
3.  `cd /etc` 进入拷贝的 `redis.conf` 的存放目录
4.  `vim redis.conf` 编辑文件
5.  `/daemonize` 搜索，将 `daemonize no` 的值改为 `daemonize yes`
6.  **<span style='color: red;'>`cd /usr/local/bin` 进入目录</span>**
8.  **<span style='color: red;'>`redis-server /etc/redis.conf` 启动 Redis</span>**
9.  **<span style='color: red;'>`/usr/local/bin/redis-cli` 访问 Redis，进入 Redis 终端</span>**
10.  再输入 `ping` 如果显示 `PONG` 表示正常
11.  单实例关闭：`redis-cli shutdown`
12.  多实例关闭，指定端口关闭：`redis-cli -p 6379 shutdown`
13.  也可以进入终端后 `shutdown` 进行关闭



#### 密码设置

`config set requirepass 密码` 学习阶段可以不设置

`auth 密码` 认证后才能操作



### Redis介绍相关知识

-   <span style='color: red;'>**Redis 默认16个数据库，类似数组下标从0开始，初始默认使用 0 号库**</span>
-   使用命令 `select  dbid` 来切换数据库。如: `select 8 `
-   统一密码管理，所有库同样密码。
-   <span style='color: red;'>`dbsize`</span> 查看当前数据库的 key 的数量
-   <span style='color: red;'>`flushdb` 清空当前库</span>
-   <span style='color: red;'>`flushall` 通杀全部库</span>

**Redis 是单线程 + 多路IO复用技术**

多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如使用线程池）

串行  vs  多线程+锁（memcached） vs  单线程+多路IO复用(Redis)

( 与Memcache三点不同: 支持多数据类型，支持持久化，单线程+多路IO复用 )





## 常用五大数据类型

http://www.redis.cn/commands.html 获得 redis 常见数据类型操作命令

>   PS：第一个 Key 不是 Redis 的数据类型

### Redis 键 ( key )

`redis-server /etc/redis.conf` 启动 Redis，`/usr/local/bin/redis-cli` 进入到 Redis 终端。演示 Redis 针对 Key 的基本命令。

1.  `keys *`：查看当前库所有 key ( 匹配：keys * 1 )

    <img src="images\image-20210521224552778.png" alt="image-20210521224552778" style="zoom:80%;" align="left"/>

2.  `exists key`：判断某个 key 是否存在。( 0：不存在，1：存在)

3.  `type key`：查看你的 key 是什么类型

    | 返回值 | 描述   |
    | ------ | ------ |
    | none   | 不存在 |
    | string | 字符串 |
    | list   | 列表   |
    | set    | 集合   |
    | zset   | 有序集 |
    | hash   | 哈希集 |

    

4.  `del key`：删除指定的 key 数据，立刻删除

5.  `unlink key`：删除指定的 key 数据，根据 value 选择非阻塞删除。仅将 keys 从 keyspace 元数据中删除，真正的删除会在后续异步操作。

6.  `expire key 10`：为给定的 key 设置过期时间，单位秒

7.  `ttl key`：查看还有多少秒过期，-1表示永不过期，-2表示已过期



1.  `select index`：命令切换数据库
2.  `dbsize`：查看当前数据库的key的数量
3.  `flushdb`：清空当前库
4.  `flushall`：通杀全部库



### Redis字符串(String)

#### 简介

String 是 Redis 最基本的类型，你可以理解成与 Memcached 一模一样的类型，一个key对应一个value。

String 类型是<span style='color: red;'>二进制安全的</span>。意味着 Redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。

String 类型是 Redis 最基本的数据类型，一个 Redis 中字符串value最多可以是<span style='color: red;'>512M</span>



#### 常用命令

`set <key> <value>`：添加键值对

![image-20210524113225094](images\image-20210524113225094.png)

使用 `set` 命令时最多可以携带三个参数。分别是中括号里面的参数，每个中括号只能选择一个参数

```
EX: key的超时秒数
PX: key的超时毫秒数，与EX互斥

NX: 当数据库中key不存在时，可以将key-value添加数据库
XX: 当数据库中key存在时，可以将key-value添加数据库，与NX参数互斥

GET: 添加到数据库后自动运行一次get命令
```



`get <key>`：查询对应键值

`append <key> <value>`：将给定的 `<value>` 追加到原值的末尾

`strlen <key>`：获得值的长度

`setnx <key> <value>`：只有在 key 不存在时   设置 key 的值

`incr <key>`：将 key 中储存的数字值增1。只能对数字值操作，如果为空，新增值为1

`decr <key>`：将 key 中储存的数字值减1。只能对数字值操作，如果为空，新增值为-1

`incrby / decrby <key> <步长>`：将 key 中储存的数字值增减。自定义步长



`mset <key1> <value1> <key2> <value2>  ..... `：同时设置一个或多个 key-value对  

`mget <key1 ><key2> <key3> .....`：同时获取一个或多个 value  

`msetnx <key1> <value1> <key2> <value2>  ..... `：同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。**<span style='color: red;'>原子性，有一个失败则都失败</span>**



`getrange  <key> <起始位置> <结束位置>`：获得值的范围，取值后进行字符串截取，类似 java 中的 `substring`，**前包，后包**

`setrange  <key> <起始位置> <value>`：用 `<value>` 覆写 `<key>` 所储存的字符串值，从 `<起始位置>` 开始 ( 索引从0开始 )。



`setex <key> <过期时间> <value>`：设置键值的同时，设置过期时间，单位秒。

`getset <key> <value>`：以新换旧，设置了新值同时获得旧值。



#### 数据结构

String 的数据结构为简单动态字符串 ( Simple Dynamic String,缩写SDS )。**是可以修改的字符串，内部结构实现上类似于 Java 的 ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配 ( 扩容 )。**

<img src="images\image-20210524120500462.png" alt="image-20210524120500462" style="zoom:80%;" />

如图中所示，内部为当前字符串实际分配的空间 capacity 一般要高于实际字符串长度len。当字符串长度小于1M时，**扩容**都是加倍现有的空间，**如果超过1M，扩容时一次只会多扩1M的空间**。需要注意的是**字符串最大长度为512M**。





### Redis 列表 ( List ) 

#### 简介

单键多值

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。元素是可以重复的。

它的底层实际是个**双向链表**，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。



#### 常用命令

`lpush / rpush <key> <value1> <value2> <value3> .... `：从左	2/ 右边插入一个或多个值。

`lpop/rpop  <key>`：从左边 / 右边吐出一个值。**<span style='color: red;'>值在键在，值光键亡</span>**。



`rpoplpush  <key1> <key2>`：从 `<key1>` 列表右边吐出一个值，插到 `<key2>` 列表左边。



`lrange <key> <start> <stop>`：按照索引下标获得元素 ( 从左到右 )。

```
lrange mylist 0 -1: 0左边第一个，-1右边第一个 (0,-1表示获取所有)
```

`lindex <key> <index>`：按照索引下标获得元素 ( 从左到右 )

`llen <key>`：获得列表长度 



`linsert <key> before / after <value> <newvalue>`：在` <value>` 的 ( 前 / 后 ) 插入` <newvalue>` 插入值

`lrem <key> <n> <value>`：从左边删除 n 个 value ( 从左到右 )

`lset <key> <index> <value>`：将列表 key 下标为 index 的值替换成 value



#### 数据结构

List 的数据结构为快速链表 quickList。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是 ziplist，也即是压缩列表。

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成 quicklist。

因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针 prev 和next。

<img src="images\image-20210524145102396.png" alt="image-20210524145102396" style="zoom:80%;" /> 

Redis 将链表和 ziplist 结合起来组成了 quicklist。也就是将多个 ziplist 使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。





### Redis 集合 ( Set )

#### 简介

Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以**<span style='color: red;'>自动排重</span>**的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。

Redis的Set是string类型的<span style='color: red;'>无序集合。它底层其实是一个value为null的hash表</span>，所以添加，删除，查找的**<span style='color: red;'>复杂度都是O(1)</span>**。

一个算法，随着数据的增加，执行时间的长短，如果是O(1)，数据增加，查找数据的时间不变



#### 常用命令

`sadd <key> <value1> <value2> ..... `：将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略

`smembers <key>`：取出该集合的所有值，不会从集合中删除

`sismember <key> <value>`：判断集合 `<key>` 是否为含有该 `<value> `值；有1、没有0

`scard <key>`：返回该集合的元素个数。



`srem <key> <value1> <value2> ....`：删除集合中的某个元素。



`spop <key>`：**随机从该集合中吐出一个值。**

`srandmember <key> <n>`：随机从该集合中取出n个值。不会从集合中删除 。



`smove <source> <destination> <value>`：将 `<source>` 集合中的 `<value>` 移动到 `<destination>` 集合中

`sinter <key1> <key2>`：返回两个集合的**交集**元素。

`sunion <key1> <key2>`：返回两个集合的**并集**元素。

`sdiff <key1> <key2>`：返回两个集合的**差集**元素 ( key1中的，不包含key2中的 )



#### 数据结构

Set 数据结构是 dict 字典，字典是用哈希表实现的。

Java 中 HashSet 的内部实现使用的是 HashMap，只不过所有的 value 都指向同一个对象。Redis 的 set 结构也是一样，它的内部也使用 hash 结构，所有的 value 都指向同一个内部值。





### Redis 哈希 ( Hash )

#### 简介

Redis hash 是一个键值对集合。

Redis hash 是一个 string 类型的 **<span style='color: red;'>field 和 value 的映射表</span>**，hash 特别适合用于存储对象。

类似 Java 里面的 Map<String,Object>

用户 ID 为查找的 key，存储的 value 用户对象包含姓名，年龄，生日等信息，如果用普通的 key/value 结构来存储。

主要有以下2种存储方式：

<table>
    <tr>
    	<td>
        	<img src="images/image-20210524152341001.png" style="zoom: 70%"/>
            <br/>
			每次修改用户的某个属性需要，先反序列化改好后再序列化回去。开销较大。
        </td>
        <td>
        	<img src="images/image-20210524152400499.png" style="zoom: 70%"/>
            <br/>
			用户ID数据冗余
        </td>
    </tr>
    <tr>
        <td colspan="2">
        	<img src="images/image-20210524152423720.png" style="zoom: 70%"/>
            <br/>
			通过key(用户ID) + field(属性标签)就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题
        </td>
	</tr>
</table>



#### 常用命令

`hset <key ><field> <value>`：给 `<key>` 集合中的 `<field>` 键赋值 `<value>`，也可以批量设置

`hmset <key> <field1> <value1> <field2> <value2>... `：批量设置hash的值

`hsetnx <key> <field> <value>`：将哈希表 key 中的域 field 的值设置为 value ，当且仅当域. field 不存在是生效



`hget <key> <field>`：从 `<key>` 集合 `<field>` 取出 value，不会删除 field

`hdel <key> <field>`：从 `<key>` 集合中删除 `<field>`



`hexists<key1> <field>`：查看哈希表 key 中，给定域 field 是否存在。( 0：不存在，1：存在 )

`hkeys <key>`：列出该hash集合的所有field

`hvals <key>`：列出该hash集合的所有value



`hincrby <key> <field> <increment>`：为哈希表 `<key>` 中的域 `<field>` 的值加上 `<increment>` ( `<increment>` 可以是负数)



#### 数据结构

Hash 类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当 field-value 长度较短且个数较少时，使用 ziplist，否则使用hashtable。





### Redis 有序集合 Zset ( sorted set )

#### 简介

Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。

不同之处是有序集合的每个成员都关联了一个**<span style='color: red;'>评分 ( score )</span> **,这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。<span style='color: red;'>集合的成员是唯一的，但是评分可以是重复了 。</span>

因为元素是有序的, 所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。

访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表。



#### 常用命令

`zadd <key> <score1> <value1> <score2> <value2>…`：将一个或多个 member 元素及其 score 值加入到有序集 key 当中。



**<span style='color: red;'>`zrange <key> <start> <stop>  [WITHSCORES]`</span>**：返回有序集 key 中，下标在 `<start> <stop>` 之间的元素；带 WITHSCORES，可以让分数一起和值返回到结果集。

`zrangebyscore <key> <min> <max> [withscores] [limit offset count]`：返回有序集 key 中，所有 score 值介于 min 和 max 之间 ( 包括等于 min 或 max ) 的成员。有序集成员按 score 值递增 ( 从小到大 ) 次序排列。 

`zrevrangebyscore <key> <min> <max> [withscores] [limit offset count]`：同上，改为从大到小排列。 



`zincrby <key> <increment> <value>`：为元素的score加上增量

`zrem  <key> <value>`：删除该集合下，指定值的元素 

`zcount <key> <min> <max>`：统计该集合，分数区间内的元素个数 

`zrank <key> <value>`：返回该值在集合中的排名，从0开始。



#### 数据结构

SortedSet ( zset ) 是 Redis 提供的一个非常特别的数据结构，一方面它等价于 Java 的数据结构 Map<String, Double>，可以给每一个元素 value 赋予一个权重 score，另一方面它又类似于 TreeSet，内部的元素会按照权重 score 进行排序，可以得到每个元素的名次，还可以通过 score 的范围来获取元素的列表。

zset 底层使用了两个数据结构

1.  hash，hash的作用就是关联元素 value 和权重 score，保障元素 value 的唯一性，可以通过元素 value 找到相应的 score 值。
2.  跳跃表，跳跃表的目的在于给元素 value 排序，根据 score 的范围获取元素列表。





## Redis 的发布和订阅

### 什么是发布和订阅

Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。

Redis 客户端可以订阅任意数量的频道。



### Redis的发布和订阅

1、客户端可以订阅频道如下图

 <img src="images\image-20210524171419108.png" alt="image-20210524171419108" style="zoom:80%;" />

2、当给这个频道发布消息后，消息就会发送给订阅的客户端

<img src="images\image-20210524171434725.png" alt="image-20210524171434725" style="zoom:80%;" align="left"/>



### 发布订阅命令行实现

1、 打开一个客户端订阅 channel1

`SUBSCRIBE channel1`

 <img src="images\image-20210524172320311.png" alt="image-20210524172320311" style="zoom:80%;" />

2、打开另一个客户端，给 channel1 发布消息 hello

`publish channel1 hello`

<img src="images\image-20210524172427946.png" alt="image-20210524172427946" style="zoom:80%;" /> 

返回的1是订阅者数量

3、打开第一个客户端可以看到发送的消息

 <img src="images\image-20210524172553520.png" alt="image-20210524172553520" style="zoom:80%;" />

注：发布的消息没有持久化，如果在订阅的客户端收不到 hello，只能收到订阅后发布的消息





## Redis 新数据类型

### Bitmaps

#### 简介

现代计算机用二进制（位） 作为信息的基础单位， 1个字节等于8位， 例如“abc”字符串是由3个字节组成， 但实际在计算机存储时将其用二进制表示， “abc”分别对应的ASCII码分别是97、 98、 99， 对应的二进制分别是01100001、 01100010和01100011，如下图

<img src="images\image-20210524173429404.png" alt="image-20210524173429404" style="zoom:80%;" />

合理地使用操作位能够有效地提高内存使用率和开发效率。

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：

1.  Bitmaps本身不是一种数据类型， 实际上它就是字符串（key-value）， 但是它可以对字符串的位进行操作。
2.  Bitmaps单独提供了一套命令， 所以在Redis中使用Bitmaps和使用字符串的方法不太相同。 可以把Bitmaps想象成一个以位为单位的数组， 数组的每个单元只能存储0和1， **数组的下标在 Bitmaps 中叫做偏移量**。

<img src="images\image-20210524174446655.png" alt="image-20210524174446655" style="zoom:80%;" />



#### 命令

##### setbit

格式：`setbit <key> <offset> <value>`：设置 Bitmaps 中某个偏移量的值 ( 0或1 )。**offset：偏移量从0开始**

**应用场景：**

公司员工今日是否签到存放到 Bitmaps 中， 将签到的员工记做1， 没有签到的员工记做0， 用偏移量作为员工的id。

设置键的第 offset 个位的值 ( 从0算起 )， 假设现在有20个员工，userid=1、6、11、15、19 的员工进行了签到，那么当前 Bitmaps 初始化结果如图

<img src="images\image-20210524175709215.png" alt="image-20210524175709215" style="zoom:80%;" />

`users:20200524` 代表 2020-05-24 这天的员工签到的 Bitmaps

<img src="images\image-20210524180026098.png" alt="image-20210524180026098" style="zoom:80%;" align="left"/>

**<span style='color: red;'>注：</span>**很多应用的用户id以一个指定数字（例如10000） 开头， 直接将用户id和Bitmaps的偏移量对应势必会造成一定的浪费， 通常的做法是每次做setbit操作时将用户id减去这个指定数字。

在第一次初始化Bitmaps时， 假如偏移量非常大， 那么整个初始化过程执行会比较慢， 可能会造成Redis的阻塞。



##### getbit

格式：`getbit<key> <offset>`：获取Bitmaps中某个偏移量的值。获取键的第offset位的值 ( 从0开始算 )

**应用场景：**

获取 id=8 的员工是否在 2020-05-24 这天是否签到， 返回0说明没有签到

<img src="images\image-20210524183347822.png" alt="image-20210524183347822" style="zoom:80%;" align="left"/>

**<span style='color: red;'>注：</span>**因为100根本不存在，所以也是返回0



##### bitcount

统计**字符串**被设置为1的bit数。一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。start 和 end 参数的设置，都可以使用负数值：比如 -1 表示最后一个位，而 -2 表示倒数第二个位，start、end 是指bit组的字节的下标数，二者皆包含。

格式：`bitcount <key> [start end]`：统计字符串从start字节到end字节比特值为1的数量

**应用场景：**

统计 2020-05-24 这天签到员工的数量

<img src="images\image-20210524183503117.png" alt="image-20210524183503117" style="zoom:80%;" align="left"/>

start 和 end 代表起始和结束字节数， 下面操作计算用户id在第1个字节到第3个字节之间的独立访问用户数， 对应的用户id是11， 15， 19。

<img src="images\image-20210524183618567.png" alt="image-20210524183618567" style="zoom:80%;" align="left"/>

**<span style='color: red;'>举例： K1 [ 01000001 01000000  00000000 00100001 ]</span>**

`bitcount K1 1 2`：统计下标1、2字节组中bit=1的个数，即 `01000000  00000000`。结果：`1`

`bitcount K1 1 3`：统计下标1、2字节组中bit=1的个数，即`01000000  00000000 00100001`。结果：`3`

`bitcount K1 0 -2`：统计下标0到下标倒数第2，字节组中bit=1的个数，即`01000001  01000000  00000000`。结果：`3`

**<span style='color: red;'>注意：</span>**redis 的 `setbit` 设置或清除的是bit位置，而bitcount计算的是byte位置。( 1byte = 8bit )



##### bitop

格式：`bitop and(or/not/xor) <destkey> [key…]`

bitop是一个复合操作， 它可以做多个Bitmaps的 `and(交集)、or(并集)、not(非)、xor(异或)`操作并将结果保存在destkey中。

<span style='color: red;'>**举例：**`user:20200524`：`10001001 00000010`( 1 5 8 15 )、`user:20200525`：`00101000 01000010` ( 3 5 10 15 ) </span>

`bitop and destkey users:20200524 users:20200525`：`00001000 00000010` ( 5 15 )

`bitop or destkey users:20200524 users:20200525`：`10100001 00000010` ( 1 3 8 10 )

`bitop not destkey users:20200524`：`01110110 11111101`。`not(非)` 只能接收一个 `key`，将0设为1，1设为0

`bitop xor destkey users:20200524 users:20200525`：`10100001 01000000`

```shell
# 异或规则
真 + 假 = 真
假 + 真 = 真
假 + 假 = 假
真 + 真 = 假
```



#### Bitmaps 与set 对比

假设网站有1亿用户， 每天独立访问的用户有5千万， 如果每天用集合类型和Bitmaps分别存储活跃用户可以得到表

| set和Bitmaps存储一天活跃用户对比 |                    |                  |                        |
| -------------------------------- | ------------------ | ---------------- | ---------------------- |
| 数据类型                         | 每个用户id占用空间 | 需要存储的用户量 | 全部内存量             |
| 集合类型                         | 64位               | 50000000         | 64位*50000000 = 400MB  |
| Bitmaps                          | 1位                | 100000000        | 1位*100000000 = 12.5MB |

 

很明显， 这种情况下使用Bitmaps能节省很多的内存空间， 尤其是随着时间推移节省的内存还是非常可观的

| set和Bitmaps存储独立用户空间对比 |        |        |       |
| -------------------------------- | ------ | ------ | ----- |
| 数据类型                         | 一天   | 一个月 | 一年  |
| 集合类型                         | 400MB  | 12GB   | 144GB |
| Bitmaps                          | 12.5MB | 375MB  | 4.5GB |

 

但Bitmaps并不是万金油， 假如该网站每天的独立访问用户很少， 例如只有10万（大量的僵尸用户） ， 那么两者的对比如下表所示， 很显然， 这时候使用Bitmaps就不太合适了， 因为基本上大部分位都是0。

| set和Bitmaps存储一天活跃用户对比（独立用户比较少） |                    |                  |                        |
| -------------------------------------------------- | ------------------ | ---------------- | ---------------------- |
| 数据类型                                           | 每个userid占用空间 | 需要存储的用户量 | 全部内存量             |
| 集合类型                                           | 64位               | 100000           | 64位*100000 = 800KB    |
| Bitmaps                                            | 1位                | 100000000        | 1位*100000000 = 12.5MB |

 



### HyperLogLog

#### 简介

在工作当中，我们经常会遇到与统计相关的功能需求，比如统计网站PV（PageView页面访问量）,可以使用Redis的incr、incrby轻松实现。

但像UV（UniqueVisitor，独立访客）、独立IP数、搜索记录数等需要去重和计数的问题如何解决？这种求集合中不重复元素个数的问题称为基数问题。

**解决基数问题有很多种方案：**

（1）数据存储在MySQL表中，使用distinct count计算不重复个数

（2）使用Redis提供的hash、set、bitmaps等数据结构来处理

以上的方案结果精确，但随着数据不断增加，导致占用空间越来越大，对于非常大的数据集是不切实际的。

能否能够降低一定的精度来平衡存储空间？Redis推出了HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

**什么是基数?**

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}，基数 ( 基数集元素的个数 ) 为5。 基数估计：就是在误差可接受的范围内，快速计算基数。

 

#### 命令

##### pfadd

格式：`pfadd <key> < element> [element ...]`：添加指定元素到 HyperLogLog 中，可以是多个。

如果执行命令后HLL估计的近似基数发生变化，则返回1，否则返回0。



##### pfcount

格式：`pfcount <key> [key ...]`：计算HLL的近似基数，可以计算多个HLL。

比如用HLL存储每天的UV，计算一周的UV可以使用7天的UV合并计算即可



##### pfmerge

格式：`pfmerge <destkey> <sourcekey>  [sourcekey ...]`：将一个或多个HLL合并后的结果存储在另一个HLL中。

比如每月活跃用户可以使用每天的活跃用户来合并计算可得





### Geospatial

#### 简介

Redis 3.2 中增加了对GEO类型的支持。GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。



#### 命令

##### geoadd

格式：`geoadd <key> <longitude> <latitude> <member> [longitude latitude member...]`：添加地理位置（经度，纬度，名称）

**实例**

`geoadd china:city 121.47 31.23 shanghai`

`geoadd china:city 106.50 29.53 chongqing 114.05 22.52 shenzhen 116.38 39.90 beijing`

<img src="images\image-20210525120532283.png" alt="image-20210525120532283" style="zoom:80%;" />

两极无法直接添加，一般会下载城市数据，直接通过 Java 程序一次性导入。

有效的经度从 -180 度到 180 度。有效的纬度从 -85.05112878 度到 85.05112878 度。

当坐标位置超出指定范围时，该命令将会返回一个错误。

已经添加的数据，是无法再次往里面添加的。



##### geopos 

格式：`geopos <key> <member> [member...]`：获得指定地区的坐标值



##### geodist

格式：`geodist <key> <member1> <member2> [m|km|ft|mi ]`  获取两个位置之间的直线距离

**单位：**

m 表示单位为米[默认值]。

km 表示单位为千米。

mi 表示单位为英里。

ft 表示单位为英尺。

如果用户没有显式地指定单位参数， 那么 GEODIST 默认使用米作为单位



##### georadius

格式：`georadius <key> < longitude> <latitude> <radius> <m|km|ft|mi>`  以给定的经纬度为中心，找出某一半径内的元素

经度 纬度 距离 单位





## Redis Jedis 测试

创建一个基本的 Maven 工程。

### Maven

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.2.0</version>
</dependency>
```



### Linux 打开6379端口 

```shell
# 打开6379端口
firewall-cmd --permanent --add-port=6379/tcp
# 重新载入使之生效
firewall-cmd --reload
```



### 创建测试程序

```java
package org.hong.jedis;

import redis.clients.jedis.Jedis;

public class JedisDemo1 {
    public static void main(String[] args) {
        // 1.创建Jedis对象
        Jedis jedis = new Jedis("192.168.200.130", 6379);

        // 2.测试
        String ping = jedis.ping();
        System.out.println(ping);

        // 3.关闭连接
        jedis.close();
    }
}
```

**控制台打野**

```shell
## 控制台输出 PONG 代表连接成功
PONG
```



### 测试相关数据

`Jedis` 方法与 `Redis` 命令几乎一样，根据 `Redis` 命令可以找到对应的 `Jedis` 方法。

#### 搭建 Test 环境

##### Maven

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13</version>
    <scope>test</scope>
</dependency>
```

##### 测试类

```java
package org.hong.jedis;

import org.junit.Before;
import org.junit.Test;
import redis.clients.jedis.Jedis;

import java.util.Set;

public class JedisDemo1 {

    private Jedis jedis;
    
	/**
     * 在单元测试方法之前执行
     */
    @Before
    public void testBefore(){
        jedis = new Jedis("192.168.200.130", 6379);
    }
    
    /**
     * 在单元测试方法之后执行
     */
    @After
    public void testAfter(){
        jedis.close();
    }
}
```



#### Key

```java
@Test
public void testKey(){
    // keys *
    Set<String> keys = jedis.keys("*");
    keys.forEach(System.out :: println);

    // del key1
    Long key1 = jedis.del("key1");
    // 返回删除的数量
    System.out.println(key1);
}
```



#### String

```java
@Test
public void testString(){
    // 添加
    String set = jedis.set("name", "hong");
    System.out.println(set);

    // 获取
    String name = jedis.get("name");
    System.out.println(name);

    // 设置多个值
    String mset = jedis.mset("k1", "v1", "k2", "v2");
    System.out.println(mset);

    // 获取多个值
    List<String> mget = jedis.mget("k1", "k2");
    mget.forEach(System.out :: println);
}
```

**控制台打印**

```shell
# 添加成功返回OK
OK
# 获取指定key的value值
hong
OK
v1
v2
```



#### List

```java
@Test
public void testList(){
    // 添加列表
    Long rpush = jedis.rpush("key1", "tom", "jerry", "hong");
    System.out.println(rpush);

    // 获取列表
    List<String> key1 = jedis.lrange("key1", 0, -1);
    key1.forEach(System.out :: println);
}
```

**控制台打印**

```shell
# 添加数量
3
# 返回的列表元素
tom
jerry
hong
```



#### Set

```java
@Test
public void testSet(){
    // 添加
    Long sadd = jedis.sadd("program", "java", "c++", "mysql", "java");
    System.out.println(sadd);

    // 获取
    Set<String> program = jedis.smembers("program");
    program.forEach(System.out :: println);
}
```

**控制台打印**

```shell
# 添加的数量
3
# 获取到的Set中的元素
java
c++
mysql
```



#### Hash

```java
@Test
public void testHash(){
    // 添加单个
    Long hset = jedis.hset("tom", "age", "18");
    System.out.println(hset);

    // 批量添加
    HashMap<String, String> map = new HashMap<>();
    map.put("sex", "男");
    map.put("birth", "2021-5-25");
    Long hset1 = jedis.hset("tom", map);
    System.out.println(hset1);

    // 获取单个field
    String age = jedis.hget("tom", "age");
    System.out.println(age);

    // 获取全部field
    Map<String, String> tom = jedis.hgetAll("tom");
    tom.forEach((k, v) -> System.out.println(k + "=" + v));
}
```

**控制台打印**

```shell
# 添加数量
1
2
# 获取单个filed
18
# 获取全部field
birth=2021-5-25
age=18
sex=男
```



#### Zset

```java
@Test
public void testZset(){
    // 添加单个
    Long zadd = jedis.zadd("China", 200, "上海");
    System.out.println(zadd);

    // 批量添加
    HashMap<String, Double> map = new HashMap<>();
    map.put("北京", 100D);
    map.put("长沙", 300D);
    map.put("长沙", 400D);
    Long china = jedis.zadd("China", map);
    System.out.println(china);

    // 获取
    Set<Tuple> zrangeWithScores = jedis.zrangeWithScores("China", 0, -1);
    zrangeWithScores.forEach(System.out :: println);
}
```

**控制台打印**

```shell
# 添加数量
1
2
# Zset中的元素
[北京,100.0]
[上海,200.0]
[长沙,400.0]
```





## Redis Jedis 示例

### 完成一个手机验证码功能

```
要求:
1.输入手机号，点击发送后随机生成6位数字码，2分钟有效
2.输入验证码，点击验证，返回成功或失败
3.每个手机号每天只能获取3次验证码
```

### 简单分析

```
要求1: 
	1.使用Java的Random类生产验证码
	2.存入Redis中并设置过期时间为120秒
要求2:
	1.从Redis中取出验证码, 于用户输入继续判断
要求3:
	1.incr每次获取验证码之后+1
	2.大于等于3的时候不能获取验证码
```

### Java 代码模拟

```java
package org.hong.jedis;

import redis.clients.jedis.Jedis;

import java.util.Random;
import java.util.Scanner;

public class PhoneCode {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请出入手机号");
        String phone = scanner.next();

        while (true) {
            System.out.println("1:输入验证码; 2:获取验证码; 3:退出系统");
            int actionCode = scanner.nextInt();

            if (actionCode == 1) {
                System.out.println("请输入验证码");
                String code = scanner.next();
                checkCode(phone, code);
            } else if(actionCode == 2) {
                verifyCode(phone);
            } else if(actionCode == 3) {
                break;
            }
        }
    }

    /**
     * 获取6位数字的验证码
     *
     * @return
     */
    public static String getCode() {
        String code = "";
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            int number = random.nextInt(10);
            code += number;
        }
        System.out.println("验证码:" + code);
        return code;
    }

    /**
     * 获取验证码逻辑
     * 每个手机每天只能发送三次, 验证码发到Redis中, 设置过期时间120秒
     *
     * @param phone
     */
    public static void verifyCode(String phone) {
        // 连接Redis
        Jedis jedis = new Jedis("192.168.200.130", 6379);

        // 拼接Key
        // 手机发送次数Key
        String countKey = "VerifyCode" + phone + ":count";
        // 验证码Key
        String codeKey = "VerifyCode" + phone + ":code";

        // 每个手机每天只能发送3次
        String count = jedis.get(countKey);
        if (count == null) {
            // 如果是null, 代表是第一次发送
            // 设置发送次数为1, 同时设置过期时间为1天(当然也可以设置当前时间到第二天的秒数, 更加准确)
            jedis.setex(countKey, 24 * 60 * 60, "1");
        } else if (Integer.parseInt(count) <= 2) {
            jedis.incr(countKey);
        } else {
            System.out.println("今天发送次数已经超过三次了");
            jedis.close();
            return;
        }

        // 将验证码放到Redis中
        jedis.setex(codeKey, 120, getCode());

        // 关闭连接
        jedis.close();
    }

    /**
     * 校验验证码
     *
     * @param phone
     * @param code
     * @return
     */
    public static void checkCode(String phone, String code) {
        Jedis jedis = new Jedis("192.168.200.130", 6379);

        // 验证码Key
        String codeKey = "VerifyCode" + phone + ":code";
        // 获取Redis中存放的验证码
        String redisCode = jedis.get(codeKey);
        // 判断
        if (code.equals(redisCode)) {
            System.out.println("验证成功");
        } else {
            System.out.println("验证失败");
        }

        jedis.close();
    }
}
```





## SpringBoot 整合 Redis

创建一个基本的 SpringBoot 项目

### Maven

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

<!-- redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- spring2.X集成redis所需common-pool2-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```



### application.properties

```properties
#Redis服务器地址
spring.redis.host=192.168.200.130
#Redis服务器连接端口
spring.redis.port=6379
#Redis数据库索引（默认为0）
spring.redis.database= 0
#连接超时时间（毫秒）
spring.redis.timeout=1800000
#连接池最大连接数（使用负值表示没有限制）
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间(负数表示没限制)
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```



### RedisConfig 配置类

```java
package org.hong.redis.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;

@EnableCaching
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
        //key序列化方式
        template.setKeySerializer(redisSerializer);
        //value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
        //value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        //解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        // 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}
```



### 测试用例

```java
package org.hong.redis;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;

@SpringBootTest
class RedisSpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        Object name = redisTemplate.opsForValue().get("name");
        System.out.println(name);
    }

}
```





## Redis 事务 锁机制

### Redis 的事务定义

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的主要作用就是**<span style='color: red;'>串联多个命令</span>**防止别的命令插队。



### Multi、Exec、discard

从输入 **<span style='color: red;'>`Multi` 命令开始</span>**，输入的命令都会依次进入命令队列中，但不会执行，直到**<span style='color: red;'>输入 `Exec` 后，Redis 会将之前的命令队列中的命令依次执行。</span>**

组队的过程中可以通过 **<span style='color: red;'>`discard` 来放弃组队</span>**。  

<img src="images\image-20210525204258049.png" alt="image-20210525204258049" style="zoom:80%;" />

<img src="images\image-20210525204741474.png" alt="image-20210525204741474" style="zoom:80%;" align="left"/>

组队成功，提交成功

<img src="images\image-20210525204930186.png" alt="image-20210525204930186" style="zoom:80%;" align="left" />

组队阶段报错，提交失败

<img src="images\image-20210525205104855.png" alt="image-20210525205104855" style="zoom:80%;" align="left" />

组队成功，提交有成功有失败情况



### 事务的错误处理

组队中某个**命令出现了报告错误，执行时整个的所有队列都会被取消**。

<img src="images\image-20210525204413490.png" alt="image-20210525204413490" style="zoom:80%;" />

如果执行阶段某个命令报出了错误，则**只有报错的命令不会被执行，而其他的命令都会执行**，不会回滚。

<img src="images\image-20210525204506161.png" alt="image-20210525204506161" style="zoom:80%;" />



### 事务冲突的问题

#### 例子

一个请求想给金额减8000

一个请求想给金额减5000

一个请求想给金额减1000

<img src="images\image-20210525220944309.png" alt="image-20210525220944309" style="zoom:80%;" align="left"/>



#### 悲观锁

<img src="images\image-20210525221023627.png" alt="image-20210525221023627" style="zoom:80%;" align="left" />

**<span style='color: red;'>悲观锁(Pessimistic Lock)</span>**, 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。**<span style='color: red;'>传统的关系型数据库里边就用到了很多这种锁机制</span>**，比如**<span style='color: red;'>行锁，表锁</span>**等，**<span style='color: red;'>读锁，写锁</span>**等，都是在做操作之前先上锁。

#### 乐观锁

<img src="images\image-20210525221220078.png" alt="image-20210525221220078" style="zoom:80%;" align="left"/>

**<span style='color: red;'>乐观锁(Optimistic Lock)</span>**, 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。**<span style='color: red;'>乐观锁适用于多读的应用类型，这样可以提高吞吐量</span>**。**Redis就是利用这种check-and-set机制实现事务的。**



#### WATCH key [key ...]

在执行multi之前，先执行watch key1 [key2],可以监视一个(或多个) key ，如果在**<span style='color: #42B992;'>事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。</span>**



#### unwatch

取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后，EXEC 命令或DISCARD 命令先被执行了的话，那么就不需要再执行UNWATCH 了。

http://doc.redisfans.com/transaction/exec.html



### Redis 事务三特性

-   **单独的隔离操作** 
    -   事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。 
-   **没有隔离级别的概念** 
    -   队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
-   **不保证原子性** 
    -   事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚 





## Redis 事务 秒杀案例

秒杀案例数据存储

<img src="images\image-20210525234448628.png" alt="image-20210525234448628" style="zoom:80%;" align="left" />

### 代码实现

创建一个简单的 SpringBoot 项目，添加 web 场景启动器。

#### Maven

```xml
<!-- 添加jedis依赖 -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.6.0</version>
</dependency>
```



#### Controller

```java
package org.hong.seckill.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import redis.clients.jedis.Jedis;

import java.util.Random;

/**
 * 秒杀Controller
 * 本次案例不分层
 */
@RestController
public class SeckillController {
    @GetMapping("/seckill")
    public Boolean seckill(@RequestParam("productId") String productId){
        String userId = getUserId();
        return doSeckill(productId, userId);
    }

    /**
     * 随机获得用户ID
     * @return
     */
    public String getUserId(){
        Random random = new Random();
        String userId = "";
        for (int i = 0; i < 4; i++) {
            int number = random.nextInt(10);
            userId += number;
        }
        return userId;
    }

    /**
     * 秒杀方法, 暂时不考虑高并发场景
     * @param productId
     * @param userId
     * @return
     */
    public boolean doSeckill(String productId, String userId){
        Jedis jedis = new Jedis("192.168.200.130", 6379);

        // 拼接Key
        // 商品库存Key
        String countKey = "seckill" + productId + ":count";
        // 秒杀成功用户Key
        String usersKey = "seckill" + productId + ":users";

        // 1.获取当前商品的秒杀库存, 如果为null, 表示秒杀还未开始
        String count = jedis.get(countKey);
        if(count == null){
            System.out.println("秒杀还未开始");
            jedis.close();
            return false;
        }

        // 2.判断商品库存, 小于1代表秒杀结束
        if(Integer.parseInt(count) < 1){
            System.out.println("秒杀已结束");
            jedis.close();
            return false;
        }

        // 3.判断用户是否重复秒杀
        if(jedis.sismember(usersKey, userId)){
            System.out.println("不能重复秒杀");
            jedis.close();
            return false;
        }

        // 4.秒杀过程
        // 4.1 库存减1
        jedis.decr(countKey);
        // 4.2 将秒杀成功用户添加到列表中
        jedis.sadd(usersKey, userId);
        System.out.println("秒杀成功");

        jedis.close();
        return true;
    }
}
```



#### index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <!-- 导入jquery -->
  <script type="text/javascript" src="jquery.js"></script>
  <script type="text/javascript">
    function seckill() {
      $.get("/seckill", {productId: "0105"}, function (data) {
        console.log(data)
      }, "json")
    }
  </script>
</head>
<body>
    <h1>HUAWEI P30 1元 限时秒杀</h1>
    <button onclick="seckill()">秒杀</button>
  </body>
</html>
```



### 秒杀并发模拟

使用工具ab模拟测试。CentOS6 默认安装、CentOS7需要手动安装 `yum install httpd-tools`

`ab -n 2000 -c 200 http://192.168.200.1:8080/seckill?productId=0105` 进行并发测试

```
-n: 后面写发送次数
-c: 后面写并发量
```



#### 超卖

<table>
    <tr>
    	<td>
        	<img src="images\image-20210526115320279.png" alt="image-20210526115320279" style="zoom:80%;" align="left" />
        </td>
        <td>
        	<img src="images\image-20210526115625447.png" alt="image-20210526115625447" style="zoom:80%;" />
        	<br/>
            秒杀结束后又出现秒杀成功，并且可以很明显的看到出现了超卖的问题。
        </td>
	</tr>
</table>



### 利用乐观锁淘汰用户，解决超卖问题

#### 增加乐观锁

```java
package org.hong.seckill.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;

import java.util.List;
import java.util.Random;

/**
 * 秒杀Controller
 * 本次案例不分层
 */
@RestController
public class SeckillController {
    @GetMapping("/seckill")
    public Boolean seckill(@RequestParam("productId") String productId){
        String userId = getUserId();
        return doSeckill(productId, userId);
    }

    /**
     * 随机获得用户ID
     * @return
     */
    public String getUserId(){
        Random random = new Random();
        String userId = "";
        for (int i = 0; i < 4; i++) {
            int number = random.nextInt(10);
            userId += number;
        }
        return userId;
    }

    /**
     * 秒杀方法
     * @param productId
     * @param userId
     * @return
     */
    public boolean doSeckill(String productId, String userId){
        Jedis jedis = new Jedis("192.168.200.130", 6379);

        // 拼接Key
        // 商品库存Key
        String countKey = "seckill" + productId + ":count";
        // 秒杀成功用户Key
        String usersKey = "seckill" + productId + ":users";

        // ###########################################
        // 监听商品库存, 增加乐观锁
        jedis.watch(countKey);

        // 1.获取当前商品的秒杀库存, 如果未null, 表示秒杀还未开始
        String count = jedis.get(countKey);
        if(count == null){
            System.out.println("秒杀还未开始");
            jedis.close();
            return false;
        }

        // 2.判断商品库存, 小于1代表秒杀结束
        if(Integer.parseInt(count) < 1){
            System.out.println("秒杀已结束");
            jedis.close();
            return false;
        }

        // 3.判断用户是否重复秒杀
        if(jedis.sismember(usersKey, userId)){
            System.out.println("不能重复秒杀");
            jedis.close();
            return false;
        }

        // ###########################################
        // 4.秒杀过程
        // 4.1 库存减1
        // 增加事务
        Transaction multi = jedis.multi();
        // 加入队列
        multi.decr(countKey);
        // 4.2 将秒杀成功用户添加到列表中
        multi.sadd(usersKey, userId);
        
        // 执行
        List<Object> exec = multi.exec();
        if(exec == null || exec.size() != 2){
            System.out.println("秒杀失败");
            jedis.close();
            return false;
        }

        System.out.println("秒杀成功");
        jedis.close();
        return true;
    }
}
```

<table>
    <tr>
    	<td>
        	<img src="images\image-20210526145135441.png" alt="image-20210526145135441" style="zoom:80%;" />
        </td>
        <td>
            <img src="images\image-20210526145354874.png" alt="image-20210526145354874" style="zoom:80%;" />
        </td>
    </tr>
    <tr>
    	<td colspan="2">
        	商品全部卖完, 没有出现超卖情况
        </td>
    </tr>
</table>



#### 增加商品库存再次测试

`set seckill0105:count 500`：设置库存数量为500

` ab -n 2000 -c 200 http://192.168.200.1:8080/seckill?productId=0105`：测试并发环境

**测试结果**

<img src="images\image-20210526145655621.png" alt="image-20210526145655621" style="zoom:80%;" align="left" />

使用 `ab` 工具发送 2000 个请求是可以卖完 500 个库存的，可是查看库存时还剩下 475 个。这是因为乐观锁造成的。

当 200 个请求同时秒杀商品时，如果一个用户秒杀了一件库存，因为乐观锁的存在会修改版本号，其他 199 个请求在进行修改的时候就会因为版本号不一致而全部导致秒杀失败。



### 解决库存遗留问题

<img src="images\image-20210526155728156.png" alt="image-20210526155728156" style="zoom:80%;" align="left" />

Lua 是一个小巧的[脚本语言](http://baike.baidu.com/item/脚本语言)，Lua脚本可以很容易的被C/C++ 代码调用，也可以反过来调用C/C++的函数，Lua并没有提供强大的库，一个完整的Lua解释器不过200k，所以Lua不适合作为开发独立应用程序的语言，而是作为嵌入式脚本语言。

很多应用程序、游戏使用LUA作为自己的嵌入式脚本语言，以此来实现可配置性、可扩展性。

这其中包括魔兽争霸地图、魔兽世界、博德之门、愤怒的小鸟等众多游戏插件或外挂。

https://www.w3cschool.cn/lua/



#### LUA脚本在Redis中的优势

将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。

<span style='color: #42B992;'>LUA脚本是类似redis事务</span>，有一定的原子性，**<span style='color: red;'>当lua脚本在执行的时候，不会有其他脚本和命令同时执行，这种语义类似于 MULTI/EXEC。从别的客户端的视角来看，一个lua脚本要么不可见，要么已经执行完</span>**。<span style='color: #42B992;'>不会被其他命令插队，可以完成一些redis事务性的操作</span>。

<span style='color: #42B992;'>但是注意redis的lua脚本功能，只有在Redis 2.6以上的版本才可以使用。</span>

<span style='color: #42B992;'>利用lua脚本淘汰用户，解决超卖问题。</span>

redis 2.6版本以后，通过lua脚本**<span style='color: red;'>解决争抢问题</span>**，实际上是 **<span style='color: red;'>redis 利用其单线程的特性，用任务队列的方式解决多任务并发问题</span>**。



#### LUA 脚本

>   效果：将所有操作 Redis 的指令写到 Lua 脚本中，Lua 脚本具有原子性，因此每次都只会有一个用户运行 Lua 脚本完成秒杀。

```lua
local userid=KEYS[1]; 
local prodid=KEYS[2];
local qtkey="seckill"..prodid..":count";
local usersKey="seckill"..prodid.":users'; 
local userExists=redis.call("sismember",usersKey,userid);
if tonumber(userExists)==1 then 
  return 2;
end
local num= redis.call("get" ,qtkey);
if tonumber(num)<=0 then 
  return 0; 
else 
  redis.call("decr",qtkey);
  redis.call("sadd",usersKey,userid);
end
return 1;
```

#### 代码实现

##### Controller

```java
@RestController
public class SeckillController {
    @GetMapping("/seckill")
    public boolean seckill(@RequestParam("productId") String productId) throws IOException {
        String userId = getUserId();
        return SecKillRedisByScript.doSeckill(productId, userId);
    }
}
```

##### SecKillRedisByScript

```java
package org.hong.seckill.script;

import java.io.IOException;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

import org.apache.commons.pool2.impl.GenericObjectPoolConfig;
import org.hong.seckill.util.JedisPoolUtil;
import org.slf4j.LoggerFactory;

import ch.qos.logback.core.joran.conditional.ElseAction;
import redis.clients.jedis.HostAndPort;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisCluster;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;
import redis.clients.jedis.ShardedJedisPool;
import redis.clients.jedis.Transaction;

public class SecKillRedisByScript {

   private static final  org.slf4j.Logger logger =LoggerFactory.getLogger(SecKillRedisByScript.class) ;

   static String secKillScript ="local userid=KEYS[1];\r\n" +
         "local prodid=KEYS[2];\r\n" +
         "local qtkey='seckill'..prodid..\":count\";\r\n" +
         "local usersKey='seckill'..prodid..\":users\";\r\n" +
         "local userExists=redis.call(\"sismember\",usersKey,userid);\r\n" +
         "if tonumber(userExists)==1 then \r\n" +
         "   return 2;\r\n" +
         "end\r\n" +
         "local num= redis.call(\"get\" ,qtkey);\r\n" +
         "if tonumber(num)<=0 then \r\n" +
         "   return 0;\r\n" +
         "else \r\n" +
         "   redis.call(\"decr\",qtkey);\r\n" +
         "   redis.call(\"sadd\",usersKey,userid);\r\n" +
         "end\r\n" +
         "return 1" ;

   public static boolean doSeckill(String prodid, String uid) throws IOException {

      JedisPool jedispool =  JedisPoolUtil.getJedisPoolInstance();
      Jedis jedis=jedispool.getResource();

       //String sha1=  .secKillScript;
      String sha1=  jedis.scriptLoad(secKillScript);
      Object result= jedis.evalsha(sha1, 2, uid,prodid);

      String reString=String.valueOf(result);
      if ("0".equals( reString )  ) {
         System.err.println("已抢空！！");
      }else if("1".equals( reString )  )  {
         System.out.println("抢购成功！！！！");
      }else if("2".equals( reString )  )  {
         System.err.println("该用户已抢过！！");
      }else{
         System.err.println("抢购异常！！");
      }
      jedis.close();
      return true;
   }
}
```

##### JedisPoolUtil

```java
package org.hong.seckill.util;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisPoolUtil {
   private static volatile JedisPool jedisPool = null;

   private JedisPoolUtil() {
   }

   public static JedisPool getJedisPoolInstance() {
      if (null == jedisPool) {
         synchronized (JedisPoolUtil.class) {
            if (null == jedisPool) {
               JedisPoolConfig poolConfig = new JedisPoolConfig();
               poolConfig.setMaxTotal(200);
               poolConfig.setMaxIdle(32);
               poolConfig.setMaxWaitMillis(100*1000);
               poolConfig.setBlockWhenExhausted(true);
               poolConfig.setTestOnBorrow(true);  // ping  PONG

               jedisPool = new JedisPool(poolConfig, "192.168.200.130", 6379, 60000 );
            }
         }
      }
      return jedisPool;
   }

   public static void release(JedisPool jedisPool, Jedis jedis) {
      if (null != jedis) {
         jedisPool.returnResource(jedis);
      }
   }

}
```



### 执行结果

<img src="images\image-20210526160715630.png" alt="image-20210526160715630" style="zoom:80%;" align="left" />

库存商品没有出现问题。





## Redis 持久化之 RDB（Redis DataBase）

### RDB 概述

在指定的**时间间隔**内将内存中的数据集**快照**写入磁盘， 也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里



### 备份是如何执行的

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。 整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。**RDB的缺点是<span style='color: red;'>最后一次持久化后的数据可能丢失</span> ( 持久化过程中 Redis 出现问题退出会造成数据丢失 )**



### dump.rdb文件

在redis.conf中配置文件名称，默认为dump.rdb

<img src="images\image-20210526172236547.png" alt="image-20210526172236547" style="zoom:80%;" align="left" />



### 配置位置

rdb文件的保存路径，也可以修改。默认为 Redis 启动时命令行所在的目录下创建 dump.rdb 文件

<img src="images\image-20210526172427398.png" alt="image-20210526172236547" style="zoom:80%;" align="left" />



### RDB快照触发时机 保存策略

#### 自动触发时机

<img src="images\image-20210526172906882.png" alt="image-20210526172906882" style="zoom:80%;" align="left"/>

```shell
默认是被注释掉的, 即不会自动触发
save 3600 1: 1个小时之内一个key发生变化
save 300 100: 5分钟之内100个key发生变化
save 60 10000: 1分钟之内10000个key发生变化
```



#### 命令 save VS bgsave 手动触发

save：save 是只管保存，其它不管，全部阻塞 ( 保存的时候无法处理请求 )。手动保存。不建议。

**<span style='color: red;'>bgsave：Redis会在后台异步进行快照操作， 快照同时还可以响应客户端请求。</span>**

可以通过 `lastsave` 命令获取最后一次成功执行快照的时间



#### flushall 命令

执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义



#### save 命令

格式：`save 秒钟 写操作次数`

RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件，

**<span style='color: red;'>默认是1分钟内改了1万次，或5分钟内改了10次，或15分钟内改了1次。</span>**

禁用

不设置save指令，或者给save传入空字符串



#### stop-writes-on-bgsave-error

<img src="images\image-20210526180615907.png" alt="image-20210526180615907" style="zoom:80%;" align="left"/>

当Redis无法写入磁盘的话，直接关掉Redis的写操作。推荐yes.



#### rdbcompression 压缩文件

<img src="images\image-20210526180733974.png" alt="image-20210526180615907" style="zoom:80%;" align="left"/>

对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用**LZF算法**进行压缩。

如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。推荐yes.



#### rdbchecksum 检查完整性

<img src="images\image-20210526180854612.png" alt="image-20210526180615907" style="zoom:80%;" align="left"/>

在存储快照后，还可以让redis使用CRC64算法来进行数据校验，

但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

推荐yes.



#### rdb 的备份

先通过 `config get dir`  查询rdb文件的目录 

将 `*.rdb` 的文件拷贝到别的地方

rdb的恢复

-   关闭Redis
-   先把备份的文件拷贝到工作目录下 `cp dump.rdb dump.rdb.bak`
-   启动Redis, 备份数据会直接加载，记得先修改文件名为 `dump.rdb`



### 优势

-   适合大规模的数据恢复
-   对数据完整性和一致性要求不高更适合使用
-   节省磁盘空间
-   恢复速度快



### 劣势

-   Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
-   虽然Redis在fork时使用了**<span style='color: red;'>写时拷贝技术</span>**，但是如果数据庞大时还是比较消耗性能。
-   在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改。





## Redis 持久化之 AOF（Append Only File）

### AOF 概述

<span style='color: red;'>以**日志**的形式来记录每个写操作（增量保存）</span>，将Redis执行过的所有写指令记录下来 **<span style='color: red;'>( 读操作不记录 )</span>**， **<span style='color: red;'>只许追加文件但不可以改写文件</span>**，redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作



### AOF持久化流程

-   客户端的请求写命令会被append追加到AOF缓冲区内；
-   AOF缓冲区根据AOF持久化策略 [always,everysec,no] 将操作sync同步到磁盘的AOF文件中；
-   AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；
-   Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；



### AOF默认不开启

可以在redis.conf中配置文件名称，默认为 **<span style='color: red;'>appendonly.aof</span>**

AOF文件的保存路径，同RDB的路径一致。

<img src="images\image-20210526185106882.png" alt="image-20210526185106882" style="zoom:80%;" align="left" />



### AOF和RDB同时开启，redis听谁的？

AOF和RDB同时开启，**系统默认取AOF的数据**（数据不会存在丢失）



### AOF 启动/修复/恢复

-   AOF的备份机制和性能虽然和RDB不同, 但是备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，**启动系统即加载。**

-   正常恢复

    -   修改默认的 `appendonly no`，改为 `yes`
    -   将有数据的 aof 文件复制一份保存到对应目录  (查看目录：`config get dir` )
    -   恢复：重启 redis 然后重新加载

    

-   异常恢复

    -   修改默认的 `appendonly no`，改为 `yes`
    -   如遇到**<span style='color: red;'>AOF文件损坏</span>**，通过 **<span style='color: red;'>`/usr/local/bin/redis-check-aof--fix appendonly.aof`</span>** 进行恢复
    -   备份 aof 文件
    -   恢复：重启 redis，然后重新加载





### AOF 同步频率设置

<img src="images\image-20210526191137873.png" alt="image-20210526191137873" style="zoom:80%;" align="left" />

-   `appendfsync always`
    -   **始终同步**，每次 Redis 的写入都会立刻记入日志；性能较差但数据完整性比较好
-   `appendfsync everysec`
    -   **每秒同步**，每秒记入日志一次，如果宕机，本秒的数据可能丢失。
-   `appendfsync no`
    -   Redis 不主动进行同步，把**同步时机交给操作系统**。



### Rewrite 压缩

#### 概述

AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制, 当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集。可以使用命令 `bgrewriteaof` 指示Redis开始追加唯一的文件重写过程。

```
set k1 v1
set k2 v2 
set k3 v3
一共三条指令, 经过Redis的重写机制会变成如下指令
mset k1 v1 k2 v2 k3 v3
```



#### 重写原理

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写 ( 也是先写临时文件最后再rename )。



#### 触发机制

Redis 会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次 rewrite 后大小的一倍且文件大于64M时触发

<span style='color: #42B992;'>重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。 </span>

`auto-aof-rewrite-percentage`：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）

`auto-aof-rewrite-min-size`：设置重写的基准值，最小文件64MB。达到这个值开始重写。

**系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size，如果 <span style='color: red;'>`Redis的AOF当前大小` >= `base_size + base_size * 100% (默认)` AND `当前大小 >= 64mb(默认)` </span>的情况下，Redis 会对 AOF 进行重写。**

```
工作原理: Redis记住上次重写时AOF日志的大小（或者重启后没有写操作的话，那就直接用此时的AOF文件）， 
         基准尺寸和当前尺寸做比较。如果当前尺寸超过指定比例，就会触发重写操作。
```



### 优势

-   备份机制更稳健，丢失数据概率更低。
-   可读的日志文本，通过操作AOF稳健，可以处理误操作。



### 劣势

-   比起RDB占用更多的磁盘空间。
-   恢复备份速度要慢。
-   每次读写都同步的话，有一定的性能压力。
-   存在个别Bug，造成恢复不能。



### 推荐

官方推荐两个都启用。

如果对数据不敏感，可以选单独用RDB。

不建议单独用 AOF，因为可能会出现Bug。

如果只是做纯内存缓存，可以都不用。

```
因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够了，只保留save 900 1这条规则。
 
如果使用AOF，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。
代价,一是带来了持续的IO，二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。
只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。
默认超过原大小100%大小时重写可以改到适当的数值。
```





## Redis 主从复制

### 简介

主机数据更新后根据配置和策略， 自动同步到备机的<span style='color: red;'> master/slaver机制</span>，**<span style='color: red;'>Master以写为主，Slave以读为主</span>**

-   读写分离，性能扩展
-   容灾快速恢复

<img src="images\image-20210527104717403.png" alt="image-20210527104717403" style="zoom:80%;" align="left" />



### 环境搭建

1.  创建 `/myredis` 文件夹存放主从环境需要的文件

    -   `mkdir /myredis`
    -   `cd /myredis`

2.  复制 `redis.conf` 配置文件到 `/myredis` 文件夹中

    -   `cp /etc/redis.conf /myredis/redis.conf`

3.  配置一主两从，创建三个配置文件 `redis6379.conf`、`redis6380.conf`、`redis6381.conf`

    -   创建配置文件 `vim redis6379.conf`

    -   写入内容。其他两个配置文件内容类似，只需要把所有的 `6379` 改为对应的端口号就行

        ```shell
        # 引入公共部分
        include /myredis/redis.conf
        pidfile /var/run/redis_6379.pid
        # 修改端口号
        port 6379
        # 修改RDB文件名称
        dbfilename dump6379.rdb
        ```

4.  启动服务 Redis

    ```
    redis-server redis6379.conf
    redis-server redis6380.conf
    redis-server redis6381.conf
    ```

5.  查看三个 `Redis` 的运行情况

    -   使用 `Xshell` 打开3个连接，使用 `redis-cli -p 端口号` 连接指定端口的 Redis

    -   进入终端后运行 `info replication` 打印主从复制的相关信息

        <img src="images\image-20210527115639953.png" alt="image-20210527115639953" style="zoom:80%;" align="left"/>

6.  配置从机

    -   在从机上执行 `slaveof 主机ip 主机端口号`，将当前 Redis 加入到指定的主机之下

    -   在次运行 `info replication` 命令

        <table>
            <tr>
            	<th>主机</th>
                <th>从机</th>
            </tr>
            <tr>
            	<td>
                    <img src="images\image-20210527120334317.png" alt="image-20210527120334317" style="zoom:80%;" />
                </td>
            	<td>
                	<img src="images\image-20210527120513701.png" alt="image-20210527120513701" style="zoom:80%;" />
                </td>
            </tr>
        </table>

7.  测试

    -   主机中 `set k1 v1`，然后在从机中 `get k1`，如果从机能取到值代表搭建成功

        <table>
            <tr>
            	<th>主机</th>
                <th>从机</th>
            </tr>
            <tr>
            	<td>
                    <img src="images\image-20210527121214399.png" alt="image-20210527120334317" style="zoom:80%;" />
                </td>
            	<td>
                	<img src="images\image-20210527121240542.png" alt="image-20210527120513701" style="zoom:80%;" />
                </td>
            </tr>
        </table>

`materauth password` ( 主机配置密码情况下从机配置文件添加 )



### 常用三招

#### 一主二从

1.  主机可以读写，从机只能读不能写
2.  **从机 `shutdown` 或 `GG` 后再次启动从机，从机的 `role` 将会变为 `master`**，我们需要再次运行 `slaveof 主机ip 主机端口号` 指令
3.  从机 `Slave` 初始化后，从机会主动将 `Master` 上的所有数据都复制一份
4.  主机 `shutdown` 或 `GG` 后，从机的 `role` 不会变为 `master`，而是等待主机直到主机上线。



#### 薪火相传

上一个Slave可以是下一个slave的Master，Slave同样可以接收其他 slaves的连接和同步请求，那么该slave作为了链条中下一个的master, 可以有效减轻master的写压力,去中心化降低风险。

-   中途变更转向：会清除之前的数据，重新建立拷贝最新的
-   风险是一旦某个slave宕机，后面的slave都没法备份
-   主机挂了，从机还是从机，无法写数据了



#### 反客为主

当一个 master 宕机后，后面的slave可以立刻升为master，其后面的slave不用做任何修改。

用 `slaveof no one`  将从机变为主机。



### 复制原理

-   Slave启动成功连接到master后会发送一个sync命令
-   Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令， 在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步
-   全量复制：slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
-   增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步
-   但是只要是重新连接master,一次完全同步 ( 全量复制 ) 将被自动执行



### 复制延时

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。



### 哨兵模式

#### 概述

**<span style='color: red;'>反客为主的自动版</span>**，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库



#### 配置哨兵

1.  搭建 `一主多从` 环境
2.  在 `/myredis` 文件夹下创建 `sentinel.conf` 文件，名字不能错
    -   内容：`sentinel monitor mymaster 127.0.0.1 6379 1`
    -   其中 `mymaster` 为监控对象起的服务器名称， 1 为至少有多少个哨兵同意迁移的数量。 



#### 启动哨兵

`redis-sentinel  /myredis/sentinel.conf`

![image-20210527155651250](images\image-20210527155651250.png)



#### 测试主机宕机

主机宕机后，哨兵会从主机中选择一个作为主机。( 哨兵检测主机宕机需要时间 )

![image-20210527160104438](images\image-20210527160104438.png)



#### 故障恢复

<img src="images\image-20210527161243651.png" alt="image-20210527161243651" style="zoom:80%;" align="left" />

-   优先级在redis.conf中默认：
    -   Redis6：`replica-priority 100`，值越小优先级越高
    -   Redis6之前：`slave-priority 100`，值越小优先级越高
    -   `salve` 单词有奴隶的意思，可能是觉得这个单词不友好就换了。
-   偏移量是指获得原主机数据最全的
-   每个redis实例启动后都会随机生成一个40位的runid



#### Java 获取主机连接

```java
private static JedisSentinelPool jedisSentinelPool=null;

public static  Jedis getJedisFromSentinel(){
   if(jedisSentinelPool==null){
      Set<String> sentinelSet=new HashSet<>();
      sentinelSet.add("192.168.200.130:26379");

      JedisPoolConfig jedisPoolConfig =new JedisPoolConfig();
      jedisPoolConfig.setMaxTotal(10); //最大可用连接数
      jedisPoolConfig.setMaxIdle(5); //最大闲置连接数
      jedisPoolConfig.setMinIdle(5); //最小闲置连接数
      jedisPoolConfig.setBlockWhenExhausted(true); //连接耗尽是否等待
      jedisPoolConfig.setMaxWaitMillis(2000); //等待时间
      jedisPoolConfig.setTestOnBorrow(true); //取连接的时候进行一下测试 ping pong

      // 通过哨兵来获取主机连接
      jedisSentinelPool=new JedisSentinelPool("mymaster",sentinelSet,jedisPoolConfig);
      return jedisSentinelPool.getResource();
   }else{
      return jedisSentinelPool.getResource();
   }
}
```





## Redis 集群

### 概述

Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

Redis 集群通过分区（partition）来提供一定程度的可用性（availability）： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

之前通过代理主机来解决，但是 redis3.0 中提供了解决方案。就是**<span style='color: red;'>无中心化集群配置</span>**。**即使连接的不是主机，集群会自动切换主机存储。主机写，从机读。无中心化主从集群。无论从哪台主机写的数据，其他主机上都能读到数据。**



### 环境搭建

1.  创建6个 `Redis` 实例，`6379,6380,6381,6382,6383,6384`

    <img src="images\image-20210527170607063.png" alt="image-20210527170607063" style="zoom:80%;" align="left" />

2.  编辑 `redis*.conf` 文件内容

    ```shell
    # 引入公共部分
    include /myredis/redis.conf
    pidfile /var/run/redis_6379.pid
    # 修改端口号
    port 6379
    # 修改RDB文件名称
    dbfilename dump6379.rdb
    #打开集群模式
    cluster-enabled yes    
    #设定节点配置文件名
    cluster-config-file nodes-6379.conf 
    #设定节点失联时间，超过该时间（毫秒），集群自动进自动进行主从切换。
    cluster-node-timeout 15000   
    ```

3.  使用查找替换修改另外5个文件 `:%s/6379/6380`

4.  启动6个 Redis 服务

    <img src="images\image-20210527171948882.png" alt="image-20210527171948882" style="zoom:80%;" align="left"/>

5.  将六个节点合成一个集群。**组合之前，请确保所有redis实例启动后，`nodes-xxxx.conf` 文件都生成正常**。

    -   `cd /opt/redis/redis-6.2.3/src`

    -   `redis-cli --cluster create --cluster-replicas 1 192.168.200.130:6379 192.168.200.130:6380 192.168.200.130:6381 192.168.200.130:6382 192.168.200.130:6383 192.168.200.130:6384`

        **此处不要用 `127.0.0.1`， 请用真实IP地址**

        `--replicas 1`：采用最简单的方式配置集群，一台主机，一台从机，正好三组。

        <img src="images\image-20210527194432634.png" alt="image-20210527172528199" style="zoom:80%;" align="left"/>

        <img src="images\image-20210527194246681.png" alt="image-20210527172647040" style="zoom:80%;" align="left"/>

6.  **<span style='color: red;'>`-c` 采用集群策略连接，写入数据会自动切换相对应的写主机。`redis-cli -c -p 端口号`</span>**

    ```shell
    # [9423]就是Redis计算的key的插槽值, 根据对应的插槽值重定向到对应端口的Redis主节点进行写入操作
    Redirected to slot [9423] located at 192.200.130:6380
    ```

    <img src="images\image-20210527194949170.png" alt="image-20210527194949170" style="zoom:80%;" align="left"/>

7.  通过 `cluster nodes` 命令查看集群信息

    <img src="images\image-20210527200518653.png" alt="image-20210527200518653" style="zoom:80%;" />



**redis cluster 如何分配这六个节点**

-   一个集群至少要有**<span style='color: red;'>三个主节点</span>**。
-   选项 `--cluster-replicas 1` 表示我们希望为集群中的每个主节点创建一个从节点。
-   分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个IP地址上。



### 插槽

节点合成集群的时候，最后输出了这么一段内容，`All 16384 slots voverd.`，表示当前集群有 16384 个插槽。

<img src="images\image-20210527201752017.png" alt="image-20210527201752017" style="zoom:80%;" align="left"/>



#### 概述

一个 Redis 集群包含 16384 个插槽（hash slot）， <span style='color: red;'>**数据库中的每个键都属于这 16384 个插槽的其中一个**</span>， 

集群使用公式` CRC16(key) % 16384` 来计算键 key 属于哪个槽， 其中 `CRC16(key)` 语句用于计算键 key 的 CRC16 校验和 。

集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：

节点 A 负责处理 0 号至 5460 号插槽。

节点 B 负责处理 5461 号至 10922 号插槽。

节点 C 负责处理 10923 号至 16383 号插槽。

**使用 `cluster nodes` 命令也可以到看到每个主节点负责处理的插槽范围**

![image-20210527202142427](images\image-20210527202142427.png)



#### 集群中录入多个值

在 redis-cli 每次录入、查询键值，redis 都会计算出该 key 应该送往的插槽，如果不是该客户端对应服务器的插槽，redis 会报错，并告知应前往的 redis 实例地址和端口。

redis-cli 客户端提供了 `–c` 参数实现自动重定向。

如 `redis-cli  -c –p 6379` 登入后，再录入、查询键值对可以自动重定向。

**<span style='color: red;'>不在一个 slot 下的键值，是不能使用 `mget,mset` 等多键操作。</span>**

<img src="images\image-20210527204717991.png" alt="image-20210527202905527" style="zoom:80%;" align="left"/>

**<span style='color: red;'>可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去。</span>**

<img src="images\image-20210527205242462.png" alt="image-20210527203035418" style="zoom:80%;" align="left"/>

```shell
# 使用mset可以给所有的key设置一个相同的组名, 再进行添加
# Redis会使用这个组名来计算插槽值并放入对应的插槽中
# 也就是说一个插槽中会存放多个数据
mset key1{组名} value1 key2{组名} value2
```

添加数据时使用组，那么数据对应的 `key` 也会发生变化

<img src="images\image-20210527205359109.png" alt="image-20210527205359109" style="zoom:80%;" align="left"/>



#### 插槽命令

`cluster keyslot <key>`：查看指定 `key` 的插槽值

`cluster countkeysinslot <slot>`：查询指定 `slot` ( 插槽 ) 里面的数据数量

`cluster getkeysinslot <slot> <count>`：查询指定 `slot` ( 插槽 ) 里面的 `count` 个数据



### 故障恢复

-   如果主节点下线，从节点将自动升为主节点

-   主节点恢复后，主节点将变为从节点

-   如果某一段插槽的主从都挂掉，而 `cluster-require-full-coverage 为 yes` ，那么 ，**整个集群都挂掉**

    如果某一段插槽的主从都挂掉，而 `cluster-require-full-coverage 为 no` ，那么，该插槽数据全都不能使用，也无法存储。其他节点依旧能够使用

    `redis.conf` 中的参数  `cluster-require-full-coverage`



### 集群的 Jedis 开发

首先打开 Redis 集群所有节点的端口号

<img src="images\image-20210528155053898.png" alt="image-20210528155053898" style="zoom:80%;" align="left"/>

```java
package org.hong.jedis;

import org.junit.Test;
import redis.clients.jedis.HostAndPort;
import redis.clients.jedis.JedisCluster;

public class RedisClusterDemo {
    @Test
    public void testCluster() {
        HostAndPort hostAndPort = new HostAndPort("192.168.200.130", 6379);
        JedisCluster jedisCluster = new JedisCluster(hostAndPort);
        jedisCluster.set("k1", "v1");
        System.out.println(jedisCluster.get("k1"));
        jedisCluster.close();
    }
}
```



### SpringBoot 集成 Redis 集群

#### application.properties

```properties
#Redis数据库索引（默认为0）
spring.redis.database=0
#连接超时时间（毫秒）
spring.redis.timeout=1800000
#连接池最大连接数（使用负值表示没有限制）
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间(负数表示没限制)
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
#Redis节点
spring.redis.cluster.nodes=192.168.200.130:6379,192.168.200.130:6380,192.168.200.130:6381,192.168.200.130:6382,192.168.200.130:6383,192.168.200.130:6384
```





## Redis 应用问题解决

### 缓存穿透

#### 概述

`key` 对应的数据在数据源并不存在，每次针对此 `key` 的请求从缓存获取不到，请求都会到数据源，从而可能压垮数据源。**比如用一个不存在的用户 `id` 获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。**



#### 解决方案

一个一定不存在缓存及查询不到的数据，由于**缓存是不命中时被动写的**，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

-    **<span style='color: red;'>对空值缓存：</span>**如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟
-   **<span style='color: red;'>设置可访问的名单（白名单）：</span>**
    -   使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。
-   **<span style='color: red;'>采用布隆过滤器：</span>**
    -   (布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。
    -   布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。)
    -   将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。
-   **<span style='color: red;'>进行实时监控：</span>**当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务



### 缓存击穿

#### 概述

**`key` 对应的数据存在，但在 `redis` 中过期**，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端 `DB` 加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端 `DB` 压垮。



#### 解决方案

-   **<span style='color: red;'>使用互斥锁(mutex key)：</span>**业界比较常用的做法，是使用 `mutex`。

    -   就是在缓存失效的时候（判断拿出来的值为空），不是立即去 `load db`，
    -   而是先使用缓存工具的某些带成功操作返回值的操作 ( 比如 Redis 的 `SETNX` 或者 Memcache 的 `ADD` ) 去 set 一个 mutex key
    -   当操作返回成功时，再进行`load db`的操作并回设缓存；并回设缓存,最后删除 mutex key
    -    当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

    <img src="images\image-20210528170045416.png" alt="image-20210528170045416" style="zoom:80%;" align="left"/>



### 缓存雪崩

#### 概述

**当缓存服务器重启或者大量缓存集中在某一个时间段失效**，这样在失效的时候，也会给后端系统 ( 比如DB ) 带来很大压力。



#### 解决方案

-   **<span style='color: red;'>构建多级缓存架构：</span>**nginx缓存 + redis缓存 +其他缓存（ehcache等）
-   **<span style='color: red;'>使用锁或队列：</span>**用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。**不适用高并发情况**
-   **<span style='color: red;'>设置过期标志更新缓存：</span>**记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际 key 的缓存。
-   **<span style='color: red;'>将缓存失效时间分散开：</span>**比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。**这个挺不错的**



### 分布式锁

#### 概述

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

**分布式锁主流的实现方案：**

1.  基于数据库实现分布式锁

2.  基于缓存（Redis等）

3.  基于Zookeeper

**每一种分布式锁解决方案都有各自的优缺点：**

1.  性能：redis最高

2.  可靠性：zookeeper最高

**这里，我们就基于redis实现分布式锁。**



#### 解决方案

<img src="images\image-20210528171304943.png" alt="image-20210528171304943" style="zoom:80%;" align="left"/>

1.  多个客户端同时获取锁（setnx）

2.  获取成功，执行业务逻辑，执行完成释放锁（del）

3.  其他客户端等待重试

##### 代码

```java
@GetMapping("testLock")
public void testLock(){
    //1获取锁，setnx
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            redisTemplate.delete("lock");
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

使用 `ab` 工具进行压力测试 `ab -n 1000 -c 100 http://192.168.140.1:8080/test/testLock`。

| 压力测试                                                     | 结果                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img src="images\image-20210528171908405.png" alt="image-20210528171908405" style="zoom:80%;" /> | <img src="images\image-20210528172016115.png" alt="image-20210528172016115" style="zoom:80%;" /> |

基本实现。

##### 问题

​	`setnx` 刚好获取到锁，业务逻辑出现异常，导致锁无法释放

##### 解决

​	设置过期时间，自动释放锁。



#### 设置锁的过期时间

```java
@GetMapping("testLock")
public void testLock(){
    //1获取锁，setnx
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111", 3, TimeUnit.SECONDS); // 设置3秒后过期, TimeUnit.SECONDS: 单位
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            redisTemplate.delete("lock");
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

压力测试肯定也没有问题。自行测试

##### 问题

​	可能会释放其他服务器的锁。

##### 场景

如果业务逻辑的执行时间是7s。执行流程如下

1.  index1业务逻辑没执行完，3秒后锁被自动释放。

2.  index2获取到锁，执行业务逻辑，3秒后锁被自动释放。

3.  index3获取到锁，执行业务逻辑

4.  index1业务逻辑执行完成，开始调用del释放锁，这时释放的是index3的锁，导致index3的业务只执行1s就被别人释放。最终等于没锁的情况。

##### 解决

​	setnx 获取锁时，**设置一个指定的唯一值（例如：uuid）**；释放前获取这个值，判断是否自己的锁



#### UUID 防误删

```java
@GetMapping("testLock")
public void testLock(){
    String uuid = UUID.randomUUID().toString();
    //1获取锁，setne
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", uuid, 3, TimeUnit.SECONDS);

    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            redisTemplate.delete("lock");
            return;
        }
        //2.2有值就转成int
        int num = Integer.parseInt(value+"");
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4释放锁，del
        //判断当前Redis中锁的UUID是否是自己的, 如果是自己的锁才进行释放
        if(uuid.equals(redisTemplate.opsForValue().get("lock"))){
            redisTemplate.delete("lock");
        }

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

##### 问题

​	查询锁和删除锁操作缺乏原子性。

##### 场景

1.  假设锁的过期时间为 10 秒
2.  index1 执行业务代码使用了 9.5 秒，然后查询锁是否是自己的
3.  指令发送到 Redis 使用了 0.3 秒，Redis 查询数据，此时花了 9.8 秒，锁还没过期，查询到的依然是 index1 的锁
4.  Redis 将数据返回给我们花了 0.5 秒，此时一共花了 10.3 秒，锁过期，但是之前查询到的数据已经返回给了 index1
5.  index1 执行删除前被打断，index2 获取到了 cpu 资源并获得了新的锁
6.  index2 线程执行过程中被打断还没释放 lock，index1 线程获取到了 cpu 资源
7.  由于 index1 被打断之前就已经获取到了 Redis 返回的数据，可以通过判断，并执行删锁操作，导致 index2 的锁被删除



#### LUA 脚本保证删除的原子性

>   效果：在查询锁和删除锁的时候无法被打断，保证在删除锁的时候时不会有新的锁被创建，不会造成误删

```java
@GetMapping("testLockLua")
public void testLockLua() {
    //1 声明一个uuid ,将做为一个value 放入我们的key所对应的值中
    String uuid = UUID.randomUUID().toString();
    //2 定义一个锁：lua 脚本可以使用同一把锁，来实现删除！
    String skuId = "25"; // 访问skuId 为25号的商品 100008348542
    String lockKey = "lock:" + skuId; // 锁住的是每个商品的数据

    // 3 获取锁
    Boolean lock = redisTemplate.opsForValue().setIfAbsent(lockKey, uuid, 3, TimeUnit.SECONDS);

    // 如果true
    if (lock) {
        // 不管业务是否执行成功, 都必须删除锁
        try{
            // 执行的业务逻辑开始
            // 获取缓存中的num 数据
            Object value = redisTemplate.opsForValue().get("num");
            // 如果是空直接返回
            if (StringUtils.isEmpty(value)) {
                return;
            }
            int num = Integer.parseInt(value + "");
            // 使num 每次+1 放入缓存
            redisTemplate.opsForValue().set("num", String.valueOf(++num));
        }finally{
            /* 使用lua脚本来释放锁 */
            // 定义lua 脚本
            String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
            // 使用redis执行lua执行
            DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>(); // 简写: DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>(script, Long.class);
            // 设置执行的脚本
            redisScript.setScriptText(script);
            // 设置脚本执行后的返回值类型
            redisScript.setResultType(Long.class);
            // 第一个是script 脚本 ，第二个需要判断的key，第三个是key所对应的值。
            Long result = redisTemplate.execute(redisScript, Arrays.asList(lockKey), uuid);
        }
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





## Redisson 分布式锁

### 简介

Redisson在基于NIO的Netty框架上，充分的利用了Redis键值数据库提供的一系列优势，在Java实用[工具包](https://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=57704855&ss_c=ssc.citiao.link)中常用接口的基础上，为使用者提供了一系列具有分布式特性的常用工具类。使得原本作为协调单机[多线程](https://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=452880&ss_c=ssc.citiao.link)并发程序的工具包获得了协调分布式多机多线程并发系统的能力，大大降低了设计和研发大规模[分布式系统](https://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=5697460&ss_c=ssc.citiao.link)的难度。同时结合各富特色的分布式服务，更进一步简化了分布式环境中程序相互之间的协作。



### 入门案例

#### 新建一个 SpringBoot 工程

#### pom.xml

```xml
<dependency>
   <groupId>org.redisson</groupId>
   <artifactId>redisson</artifactId>
   <version>3.13.0</version>
</dependency>
```

#### application.yaml

```yaml
server:
  port: 11000
```

#### RedissonConfig

```java
package org.hong.gulimall.product.config;

import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.redisson.config.Config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.io.IOException;

/**
 * redisson作为分布式锁, 分布式对象等功能
 * 1、导入依赖
 * <dependency>
 *     <groupId>org.redisson</groupId>
 *     <artifactId>redisson</artifactId>
 *     <version>3.13.0</version>
 * </dependency>
 * 2、编写配置
 */
@Configuration
public class RedissonConfig {
    @Bean(destroyMethod="shutdown")
    public RedissonClient redisson() throws IOException {
        Config config = new Config();
        config.useSingleServer() // 启用单节点模式
                .setAddress("redis://192.168.200.130:6379"); // 设置主机地址, 记得加上前缀 redis:// 或者 rediss://
        return Redisson.create(config); // 创建实例
    }
}
```

#### RedissonController

```java
package org.hong.gulimall.product.web;

import org.redisson.api.RLock;
import org.redisson.api.RedissonClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class RedissonController {
    @Autowired
    private RedissonClient redisson;

    @RequestMapping("/hello")
    public String hello(){
        /*
         * 1.获取锁, 并指定锁名称
         * 2.存入Redis后会以锁名称作为key, 存入Hash类型的数据
         *  2.1、其中field为(UUID:线程ID), 防止误删锁
         *  2.2、value暂时不知道什么含义
         * 3.需要注意的是getLock方法不会真正的加锁, 只是定义了锁的信息
         */
        RLock lock = redisson.getLock("hello-lock");
        /* 
         * 调用lock()方法进行真正的加锁
         * 	1.将锁存入Redis中
         * 	2.默认过期时间为30s, 防止死锁
         *  3.阻塞式等待
         *  4.如果业务运行时间超长, 会自动给锁续上新的30秒
         */
        lock.lock();
        try{
            // 模拟超长业务
            Thread.sleep(40000);
        }catch (Exception e){

        }finally{
            // 保险起见解锁代码放到finally块中, 保证解锁
            lock.unlock();
        }
        return "hello";
    }
    
    @RequestMapping("/hello2")
    public String hello2(){
        /*
         * 1.获取锁, 并指定锁名称
         * 2.存入Redis后会以锁名称作为key, 存入Hash类型的数据
         *  2.1、其中field为(UUID:线程ID), 防止误删锁
         *  2.2、value暂时不知道什么含义
         * 3.需要注意的是getLock方法不会真正的加锁, 只是定义了锁的信息
         */
        RLock lock = redisson.getLock("hello-lock");
        /*
         * 调用lock方法进行真正的加锁
         * 	1.将锁存入Redis中
         * 	2.指定过期时间为10, 单位为SECONDS(秒), 防止死锁
         *  3.阻塞式等待
         *  4.指定时长的lock是不会为锁续期的
         */
        lock.lock(10, TimeUnit.SECONDS);
        try{
            // 模拟超长业务
            Thread.sleep(40000);
        }catch (Exception e){

        }finally{
            // 保险起见解锁代码放到finally块中, 保证解锁
            lock.unlock();
        }
        return "hello";
    }
}
```

#### Redis 查看 hello-lock 锁的存储信息

![image-20210731224335143](images\image-20210731224335143.png)

#### Redis 查看 hello-lock 锁的过期时间

>   Redisson 默认会给锁设置 30 秒的过期时间，但是我们模拟40秒的超长业务，30秒的锁很明显是不够用的，当程序很长一段时间没有释放锁，Redisson 就会自动给锁续期，大概在锁的过期时间还有20秒的时候就会重新将锁的过期时间设置为30秒。
>
>   如果在程序运行期间，服务器断电，会不会造成死锁呢 ( 即锁一直无法被释放 )？                                                                                                   由于锁的续期是 Redisson 进行的，当我们的服务宕机后，Redisson 自然也无法运行，也就无法给锁续期，锁到时间就会自动释放，不会造成死锁。

![image-20210731224806778](images\image-20210731224806778.png)

#### lock() 空参方法

锁的过期时间：30秒

是否会自动续期：true



#### lock() 带参方法

锁的过期时间：指定的过期时间

是否会自动续期：false





### 读写锁

保证一定能督导最新数据，修改期间，写锁是一个排他锁 ( 互斥锁 )。读锁是一个共享锁。

#### 代码

```java
@Autowired
private StringRedisTemplate redisTemplate;

@RequestMapping("/read")
public String read (){
    RReadWriteLock readWriteLock = redisson.getReadWriteLock("rw-lock");
    String value = "";
    RLock rLock = readWriteLock.readLock();
    rLock.lock();
    System.out.println("读锁加锁成功" + Thread.currentThread().getId() + "\t" + new Date());
    try {
        value = redisTemplate.opsForValue().get("rw-key");
    } finally {
        rLock.unlock();
        System.out.println("读锁解锁成功" + Thread.currentThread().getId() + "\t" + new Date());
    }
    return value;
}

@RequestMapping("/writh")
public String writh (){
    RReadWriteLock readWriteLock = redisson.getReadWriteLock("rw-lock");
    String uuid = UUID.randomUUID().toString();
    RLock rLock = readWriteLock.writeLock();
    rLock.lock();
    System.out.println("写锁加锁成功" + Thread.currentThread().getId() + "\t" + new Date());
    try {
        redisTemplate.opsForValue().set("rw-key", uuid);
        Thread.sleep(10000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        rLock.unlock();
        System.out.println("写锁解锁成功" + Thread.currentThread().getId() + "\t" + new Date());
    }
    return uuid;
}
```



#### 读写锁在 Redis 中的结构

```properties
mode: 锁的类型
48fcddc7-....: 锁的唯一标识
```

![image-20210804155752151](images\image-20210804155752151.png)



#### 测试

##### 先写后读

在读锁未释放的时候，读取操作必须等待读锁释放才能继续进行。

![image-20210804152736411](images\image-20210804152736411.png)

>   控制台打印

```shell
写锁加锁成功118	Wed Aug 04 15:44:14 CST 2021
写锁解锁成功118	Wed Aug 04 15:44:24 CST 2021
读锁加锁成功119	Wed Aug 04 15:44:24 CST 2021
读锁解锁成功119	Wed Aug 04 15:44:24 CST 2021
```



##### 多次写

拿到写锁的线程进行运行，其他线程等待写锁释放后获取到写锁才能运行，阻塞式等待。

```shell
写锁加锁成功120	Wed Aug 04 15:45:12 CST 2021
写锁解锁成功120	Wed Aug 04 15:45:22 CST 2021
写锁加锁成功121	Wed Aug 04 15:45:22 CST 2021
写锁解锁成功121	Wed Aug 04 15:45:32 CST 2021
```



##### 先读后写

>   在读的方法中添加 sleep

```java
@RequestMapping("/read")
public String read (){
    RReadWriteLock readWriteLock = redisson.getReadWriteLock("rw-lock");
    String value = "";
    RLock rLock = readWriteLock.readLock();
    rLock.lock();
    System.out.println("读锁加锁成功" + Thread.currentThread().getId() + "\t" + new Date());
    try {
        value = redisTemplate.opsForValue().get("rw-key");
        Thread.sleep(10000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        rLock.unlock();
        System.out.println("读锁解锁成功" + Thread.currentThread().getId() + "\t" + new Date());
    }
    return value;
}
```

读锁在未解锁前无法获取到写锁，保证读锁线程获取到的数据还没有被更改

```shell
读锁加锁成功233	Wed Aug 04 15:48:07 CST 2021
读锁解锁成功233	Wed Aug 04 15:48:17 CST 2021
写锁加锁成功234	Wed Aug 04 15:48:17 CST 2021
写锁解锁成功234	Wed Aug 04 15:48:27 CST 2021
```



##### 多次读

读锁是被共享的，多次读等于没有锁

```shell
读锁加锁成功113	Wed Aug 04 16:20:48 CST 2021
读锁加锁成功114	Wed Aug 04 16:20:48 CST 2021
读锁解锁成功112	Wed Aug 04 16:20:49 CST 2021
```



### 信号量

在生活中有这样的问题，当你开车进入车库时，发现没车位怎么办，只有等待别人开走留下空车位，当然如果有空车位，我们就可直接停进去，此时车位数就会减少，Semaphore信号量就是实现这种现象的一个功能。

#### 代码

```java
@GetMapping("/park")
public String park() throws InterruptedException {
    //获得信号量
    RSemaphore park = redisson.getSemaphore("park");
    //占用车位, 阻塞式等待, 获取不到就一直卡在这里
    park.acquire();
    return "获得一个车位...";
}

@GetMapping("/leave")
public String leave() {
    //获得信号量
    RSemaphore park = redisson.getSemaphore("park");
    //释放一个车位
    park.release();
    return "释放一个车位...";
}
```



#### 测试

先在 Redis 中创建 key 为 park 的数据 `set park 3`

浏览器发起 http://localhost:8080/park 请求，此时 park 会减少一，直到 park 为0时，请求就会一直进行，直到发起 http://localhost:8080/leave请求使得 park 数增加才会终止



#### 信号量在 Redis 中的结构

key-value 键值对

![image-20210804165758585](images\image-20210804165758585.png)



#### tryAcquire() 方法

尝试获取信号量，如果获取成功返回 true，反之 false

```java
@GetMapping("/park")
public String park() throws InterruptedException {
    //获得信号量
    RSemaphore park = redisson.getSemaphore("park");
    //占用车位
    boolean result = park.tryAcquire();
    if(result){
        return "获得一个车位...";
    }else{
        return "车位已满";
    }
}
```



### 闭锁

在要完成某些运算时，只有其它线程的运算全部运行完毕，当前运算才继续下去。

场景：学校放假关门，只有所有班级的人都走玩了才能锁门。

#### 代码

```java
@GetMapping("/lockDoor")
public String loclDoor() {
    RCountDownLatch door = redisson.getCountDownLatch("door");
    door.trySetCount(5); // 设置起始值
    try {
        door.await(); //等待闭锁都完成
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return "关门了";
}

@GetMapping("/gogogo/{id}")
public String gogogo(@PathVariable("id") Integer id) {
    RCountDownLatch door = redisson.getCountDownLatch("door");
    door.countDown(); // 计数器减一
    return id + "班的人都走了";
}
```



#### 测试

先访问 http://localhost:8080/lockDoor，这个请求会一直执行，只有当我们访问了 5 次 http://localhost:8080/gogogo/1，lockDoor 请求才能运行完毕



#### 闭锁在 Redis 中的结构

key-value 键值对

![image-20210804170013429](images\image-20210804170013429.png)

