### NoSQL能干嘛、是什么、怎么用？

* NOSQL能干嘛:

  * 多样灵活的数据模型NoSQL无需事先为要存储的数据建立字段，随时可以存储定义的数据格式。而在关系数据库里，增删字段是一件非常麻烦的事请。若果是非常大数据量的表，增加字段简直就是一个噩梦
  * 易扩展
  * 大数据量高性能
  * 传统RDBMS VS NOSQL
* RDBMS
  *  高度组织化结构化数据
  *  结构化查询语言（SQL）
  *  数据和关系都存储在单独的表中
  *  数据操纵语言，数据定义语言
  *  严格的一致性
  *  基础事务
* NoSQL
  * 代表着不仅仅是SQL
  * 没有声明性查询语言
  * 没有预定的模式  
  * 键-值对存储，列存储，文档存储，图形数据库
  * 最终一致性，而非ACID属性
  * 非结构化和不可预知的数据
  * CAP定理
  * 高性能，高可用性和伸缩性
* NoSQL这盘棋去哪里下：
  * Redis
  * Memcache（强悍--缓存）
  * Mongdb（最像关系型数据库的一种非关系型数据库）淘宝存商品信息评论的文档数据库 
* 怎么玩：
  * KV
  * Cache
  * Persistence 
* 3V+3 高
  * 大数据时代的3V
    * 海量 Volume
    * 多样 Variety
    * 实时 Velocity
  * 互联网需求的3高
    * 高并发
    * 高可扩 （一定是指横向扩展）
    * 高性能  
 * NoSQL怎么设计
   * BSON
     * 是一种json的一种二进制形式的存储格式，简称Binary JSON
     * 和json一样，支持内嵌的文档对象和数组对象
   * 用BSON  建立数据模型
   * ![a1dee381fca73097baab18c4656cbf1c.png](en-resource://database/672:1)
* NoSQL模型简介
  *   KV键值
  *   Bson
  *   列族
  *   图形
* NoSQL数据库的四大分类
  *   KV键值：景点介绍
      *   新浪：BrekeyDB+redis
      *   美团：redis+tair
      *   阿里、百度：memcache+redis
  *   文档型数据库（bson格式比较多）：典型介绍
      *   CouchDB
      *   MongoDB 
          *   基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案
          *   介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的
  *   列存储数据库
      *   Cassandra， Hbase（哈杜泼的兄弟）
      *   分布式文件系统
  *   图关系数据库
      *   它不是放图形的，放的是关系 比如：朋友圈社交网络、广告推荐系统
      *   社交网络，推荐系统等。专注于构建关系图谱
      *   Neo4J,InfoGrid
  *   四者对比
      *   

| 分类              | Examples举例                                     | 典型应用场景                                                 | 数据类型                                      | 优点                                                         | 缺点                                                         |
| ----------------- | ------------------------------------------------ | ------------------------------------------------------------ | --------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 键值（Key-Value） | Tokyo、Cabinet/Tyrant\Redis\Vodemort、Oracle BDB | 内容缓存，主要用于出里大量数据的高访问量负载，也用于一些日志系统等 | key只想Value的键值对，通常用hash table 来实现 | 查找速度块                                                   | 数据五结构化，通常只被当作字符串或者二进制数据               |
| 列存储数据库      | Cassandra、HBase、Riak                           | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存在一起            | 查找速度快、可扩展性强，更易于进行分布式扩展                 | 功能相对局限                                                 |
| 文档型数据库      | CouchDB、MongDB                                  | web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解Value的内容） | Key-Value对应的键值对，Value为结构化数据      | 数据结构要求不严格，表结构可变，不需要像关系数据库一样需要预先定义表结构 | 查询性能不高，而且缺乏统一的查询语句                         |
| 图关系数据库      | Neo4J,InfoGrid、Infinite Graph                   | 社交网络，推荐系统等。专注于构建关系图谱                     | 图结构                                        | 利用图结构相关算法。比如：最短路径寻址，N度关系查找等        | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的额集群方案 |

* 在分布式数据库中CAP原理 CAP+BSASE
  * 传统的关系型数据库：ACID
    * A（Atomicity）原子性
    * C（Consistency）一致性
    * I（Isolation）独立性
    * D（Durability）持久性
  * NoSQL：CAP
    * C:Consistency（强一致性）
    * A:Availability（可用性）
    * P: Partition tolerance（分区容错性）
  * CAP 不可能全部满足 需要三选二：
  * CAP核心：最多只能同时较好的满足两个
    * CA：单点集群，满足一致性，可用性的系统，通常在可扩展上不太强大
    * CP：满足一致性，分区容忍性的系统，通常性能不是特别高
    * AP: 满足可用性，分区容忍性的系统，通常可能对一致性的要求低一些 （一半用这个）
  * BASE：为了解决关系数据库强一致性引起的问题而引起的可用性降低而题除的解决方案
    * 基本可用 （Basically Available）
    * 软状态 （Soft state）
    * 最终一致 （Eventually consistent）
  * 分布式+集群简介 
    * 分布式：不同的多台服务器上面部署不同的服务模块(工程)，他们之间通过Rpc/Rmi之间通.信和调用，对外提供服务和组内协作。
    * 集群:不同的多台服务器上面部署相同的服务模块，通过分布式调度软件进行统- -的调度，对外提供服务和访问。

### Redis入门介绍

* Redis是什么: Redis:Remote Dictionary Server(远程字典服务器)
  * 完全开源免费的，用C语言编写的，遵守BSD协议
  * 是一个高性能（Key/Value）分布式内存数据库，基于内存运行并支持持久化的NoSQL数据库，是当下最热门的NoSQL数据库之一，也被人们称为数据结构服务器
  * Redis与其他kry-value 缓存产品有一下三个特点
    * Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
    * Redis不仅仅支持简单的key-value类型的数据，同时嗨提供list，set，zset，hash等数据结构的存储
    * Redis支持数据的备份，即master-slave模式的数据备份
* Redis能干嘛：
  * 内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务
  * 取最新N个数据的操作
  * 模拟类似于HttpSession这种需要设定过期时间的功能
  * 定时器、计数器
* Redis下载：英文：Http//redis.io/ 中文：http：//www.redis.cn/
* Redis怎么玩：
  * 数据类型、基本操作和配置
  * 持久化和复制，RDB/AOF
  * 事务的控制
  * 复制
  * ......
 * 企业里边做开发，99%都是Linux班的运用和安装，几乎不会涉及到WIndows版。 

### Redis 安装

- 前提：Redis是c语言编写的所以需要 Gcc环境 预先装好gcc
- 直接 干上下载地址就完事了：wget http://download.redis.io/releases/redis-5.0.4.tar.gz
- 下载好解压：tar -zxvf redis-5.0.4.tar.gz
- 解压后修改一下配置文件
- 然后运行就完事了 
- 查看进程：ps -ef|grep redis

### Redis启动后杂项基础知识

- 单进程
  - 单进程模型来处理客户端的请求，对读写等事件的响应 通过对epoll函数的包装来做到的。Redis的实际处理速度完全依靠主进程的执行效率
  - Epoll是Linux内核为处理大批量文件描述符而做了改进的epoll，是Linux下多路复用IO接口select/poll的增强版本，他能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率
- 默认16个数据库，类似数组下表从零开始，初始默认使用零号库
- Select命令切换数据库 ：select 7 对应八号库
- Dbsize查看当前数据库的key的数量
  - keys * 查看当前库所有key
- Flushdb：清空当前库
- Flushall：通杀全部库
- 同意密码管理，16个库都是同样密码，要么OK要么一个也连不上
- Redis索引都是从零开始
- 为什么默认端口是6379

### Redis的五大数据类型



[指令参考（API文档）]: http://redisdoc.com/

#### key关键字

- keys*  查看所有key
- exist key 的名字，判断某个key是否存在（1存在 0不存在）
- move key db -->当前库就没有了，被移除了
- expire key秒钟：给定的key设置过期时间
- ttl key 查看还有多少秒过期（-1永不过期，-2已过期） **过期死亡（意思为过期即删除该数据）**
- type key 查看你的key是什么类型

| 常用                    | 备注                                              |
| ----------------------- | ------------------------------------------------- |
| DEL key                 | 在key存在时删除key                                |
| DUMP key                | 序列化给定key，并返回被序列化的值                 |
| EXISTS key              | 检查给定的key是否存在                             |
| EXPIRE key              | 给key设置过期时间，设置的时间单位为秒             |
| EXIPIREAT key timestamp | 和 EXPIRE的作用一样，只不过设置的时间单位时时间戳 |
| KETYS pattern           | 查找所有给定模式的key                             |
| MOVE key db             | 将key移到几号库中                                 |
| PERSIST key             | 移除key的过期时间，key将持久保存                  |
| PTTL  key               | 以毫秒为单位返回key的生于的过期时间               |
| TTL key                 | 以秒为单位返回key的生于的过期时间                 |
| RANDOMKEY               | 从当前数据库中随机返回一个key                     |
| RENAME key newkey       | 修改key的名称                                     |
| RENAMENX key newkey     | 当newkey不存在是将key改名为newkey                 |
| TYPE key                | 返回key所存储值的类型                             |



#### 字符串（String）

- String是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value
- String类型是二进制安全的，意思时redis的string可以包含任何数据。比如jpg图片或者序列化的对象
- String类型是Redis最基本的数据类型，一个redis中字符串value最多可以是**512M**

**常用操作**

| 常用                                               | 备注                                                         |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Incr/decr/incrby/decrby                            | 前提时数字的情况下才能用（incr/decr自增一  incrby+数字 增加多少 decrby+数字 减少多少） |
| getrange/setrange                                  | 相当于截取 getrange k3 0 -1 意为从下标0开始读取到-1截止      |
| setex（set with expire）/setnx（set if not exist） | set k1 10 v1 设置k1 v1键值对 时间是10秒 setnx  k1 v1 会失败因为k1存在 |
| mset/mget/msetnx                                   | 设置、获取多个kv   msetnx ：**key友谊的小船说翻就全翻了**    |
| getset                                             | 先get再set                                                   |



#### 哈希（Hash类似java里的Map）

- Redis hash是一个键值对的集合
- Redis hash是一个String类型的field和value的映射表，hash特别适合用于存储对象
- 类似于Java里面的Map<String, Object>
- KV模式不变，但V是一个键值对

* 

| 常用                               | 备注                                        |
| ---------------------------------- | ------------------------------------------- |
| hset/hget/hmset/hmget/hgetall/hdel | 添加/获取/添加多个/获取多个/全部获取/删除   |
| hlen                               | 获取hash长度                                |
| hexists key key里边的某个key       | 查看是否有某个key                           |
| hkeys/hvals                        | 获取key里边所有的key/获取key里边所有的value |
| hincrby/hincrbyfloat               | 增加/浮点增加                               |
| hsetnx                             | 不存在往里边灌                              |

#### 列表（List）

- Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边或者尾部（右边），
- 它的底层实际就是个链表）

操作

* 单值多value

| 常用                           | 备注                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| ipush/rpush/lrange             | ipush相当于队列 rpush相当于栈  **lrange list01 0 -1**        |
| lpop/rpop                      | 取栈顶、栈尾数据 **取出去就没了**                            |
| lindex                         | **lindex list01 0**  索引下标 拿值                           |
| llen                           | 获取集合长度  **llen list01**                                |
| lrem key                       | 删N个**value  lrem list01 2 3：**删除集合中两个值为3的数     |
| ltrim key 开始 index 结束index | 截取指定范围的值后再赋值给key **LTRIM list01 0 2** 意为节去list01 2之后的数据给截断 不要了 |
| linsert key befor/after值1 值2 | 在key中值1前后插入值2 linsert list02  befor 8 java：在list02中值为8的前边 |

性能总结：

* 它是一个字符串链表、left、right都可以插入添加
* 如果键不存在，创建新的链表
* 如果键已存在，新增内容
* 如果值全部移除，对应的键也就消失啦
* 链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了

#### 集合（Set）

- Redis的Set是String类型的无序集合。ta是通过HashTable实现的

操作

* 单值多value
* 添加重复元素会自动消除 但是不会报错

| 常用                               | 备注                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| sadd/smembers/sismember            | isadd增加set smembers set01查看 set sismember k 判断k是否在set中 |
| scard                              | 获取集合元素个数                                             |
| srem key value                     | 删除集合中值为value的元素                                    |
| srandmember key                    | 在某个key中随机出几个                                        |
| spop key                           | 随机出栈                                                     |
| smove key1 key2 在key1里边的某个值 | 将key1里边的某个值赋值给key2                                 |
| 差：sdiff 交：sinter  并：sunion   | 数学集合类（交并差）                                         |

性能总结：

#### 有序集合Zset（sorted set）

- Redis zsrt和set一样是String类型元素的集合，且不允许重复的成员
- 不同的是每个元素都会关联一个double类型的分数
- redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的，但分数（score）却可以重复
- 在set基础上，加一个score值
  - 之前set是 k1 v1 v2 v3
  - 现在zset是k1 score1 v1 score2 v2

| 常用                               | 备注                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| sadd/smembers/sismember            | isadd增加set smembers set01查看 set sismember k 判断k是否在set中 |
| scard                              | 获取集合元素个数                                             |
| srem key value                     | 删除集合中值为value的元素                                    |
| srandmember key                    | 在某个key中随机出几个                                        |
| spop key                           | 随机出栈                                                     |
| smove key1 key2 在key1里边的某个值 | 将key1里边的某个值赋值给key2                                 |
| 差：sdiff 交：sinter  并：sunion   | 数学集合类（交并差）                                         |

### Redis配置文件 redis.conf

* 在linux上做开发 配置>开发 永远要把原始的配置文件备份一份

* untis单位

* ```
  # 1k => 1000 bytes
  
  # 1kb => 1024 bytes
  
  # 1m => 1000000 bytes
  
  # 1mb => 1024*1024 bytes
  
  # 1g => 1000000000 bytes
  
  # 1gb => 1024*1024*1024 bytes
  ```

  缓存写满处理：

  * volatile-lru:使用LRU算法移除key，只对设置了过期时间的键
  * allkeys-lru：使用LRU算法一处key
  * volatile-random：在集合中移除随机的key，只对设置了过期时间的键
  * allkeys-random：移除随机的key
  * volatile-ttl：移除那些TTL最小的key，即那些最近要过期的key
  * noeviction：不进行移除。针对写操作，只是返回错误信息

* redis.conf配置项说明如下:

  1 Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程

  daemonize no

  2.当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件， 可以通过pidfile指定
  pidfile /var/run/redis.pid]

  3.指定Redis监听端口，默认端口为6379,作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
  port 6379

  4.绑定的主机地址
  bind 127.0.0.1

  5.当客户端闲置多长时间后关闭连接，如果指定为0,表示关闭该功能
  timeout 300

  6.指定日志记录级别，Redis.总共 支持四个级别: debug、 verbose、 notice、 warning, 默认为verbose
  loglevel verbose

  7.日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
  logfile stdout

  8.设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
  databases 16

  9.指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
  save <seconds> <changes>
  Redis默认配置文件中提供了三个条件:
  save 900 1
  save 300 10
  save 60 10000
  分别表示900秒(15分钟)内有1个更改，300秒(5分钟)内有10个更改以及60秒内有10000个更改。

  10.指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩， 如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
  rdbcompression yes

  11.指定本地数据库文件名，默认值为dump.rdb

  12.指定本地数据库存放目录
  dir :I|1

  13.设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
  slaveof <masterip> <masterport>

  14.当master服务设置了密码保护时，slav服务连接master的密码
  masterauth <master-password>

  15.设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password> 命令提供密码，默认关闭
  requirepass foobared

  16.设置同一时间最大客户端连接数，默认无限制，Redis可 以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置maxclients 0,表示不作限

  制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
  maxclients 128

  17.指定Redis最大内存限制，Redis在 启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
  maxmemory <bytes>

  18.指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致-段时间内的数据丢失。因为redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。 默认为no

  19.指定更新日志文件名，默认为appendonly .aof
  appendfilename appendonly.aof

  20.指定更新日志条件，共有3个可选值:
  no: .表示等操作系统进行数据缓存同步到磁盘(快)
  always:表示每次更新操作后手动调用fsync()将数据写到磁盘(慢，安全)
  everysec:表示每秒同步一次(折衷，默认值)
  appendfsync everysec

### Redis 持久化

* RDB（Redis DateBase）
  * 是什么：Redis会单独创建(fork) 一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。
  * Fork：作用是复制-一个与当前进程一样的进程。新进程的所有数据(变量、环境变量、程序计数器等)数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程
  * SNAPSHOTTING快照：
    * Save 
  * 动态停止RDB保存规则的方法：redis-cll config set save " "

* AOF（Append Only one）
  * appendonly ：yes 开启 no关闭
  * appendfilename：名字 默认 qppendonly.aof
  * Appendfsync：
    * Always:同步持久化每次发生数据变更会被立即记录到磁盘性能较差但数据完整性比差
    * Everysec:出厂默认推荐，异步操作，每秒记录如果一秒内宕机，有数据丢失
    * No
  * No-appendfsync-on-rewrite:重写时是否可以运用Appendfsync，用默认no即可，保证数据安全性。
  * Auto-aof-rewrite-min-size:设置重写的基准值
  * Auto-aof-rewrite-percentage:设置重写的基准值
  * 检查修复：Redis-check-aof --fix进行修复
  * AOF重写原理：文件持续增长而过大时，会fok出一条新进程来将文件重写，遍历新进程的内存中数据，每条记录有一条set语句。重写aof文件操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似
  * 重写触发机制：Redis会记录上次重写时的AOF大小，默认配置时当AOF文件大小是上次rewrit后大小的一倍且文件大于64M时触发
  * 小结：
    * AOF文件时一个只进行追加的日志文件
    * Redis可以在AOF文件体积变得过大时，自动地在后台对AOF进行重写
    * AOF文件有序地保存了对数据库执行的所有写入操作，这些写入操作以Redis协议的格式保存，因此AOF文件的内容非常容易被人读懂，对文件进行分析也很轻松
    * 对于相同的数据集来说，AOF文件的体积通常要大于RDB文件的体积.
    * 根据所使用的fsync策略，AOF的速度可能会慢于RDB

* 两者可以和平共存  加载的时候先加载appendonly.aof然后zhao dump.rdb

* RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储
* AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末*尾.Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大

* 只做缓存:如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式.|

* 同时开启两种持久化方式
* 性能建议：
  * 因为RDB文件只用作后备用途，建议只在Slave.上持久化RDB文件，而且只要15分钟备份- -次就够了，只保留save 900 1这条规则。
  * 如果Enalbe AOF,好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。代价一是带来了持续的IO，二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite的频率， AOF重写的基础大小默认值64M太小了，可以设到5G以上。默认超过原大小100%大小时重写可以改到适当的数值。
  * 如果不Enable AOF，仅靠Master-Slave Replication实现高可用性也可以。能省掉- -大笔IO也减少了rewrite时带来的系统波
    动。代价是如果Master/Slave同时倒掉， 会丢失十几分钟的数据，启动脚本也要比较两个Master/Slave中的RDB文件，载入较新
    的那个。新浪微博就选用了这种架构

### Redis的事务（只能说对事务部分支持）

- 是什么：可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会被序列化，按顺序地串行执行而不会被其他命令插入，不许加塞

- 能干嘛：一个队列中，一次性、顺序性、排他性的执行一系列命令

- 怎么用：

  - 常用命令

  | 序号 | 命令及描述                                                   |
  | ---- | ------------------------------------------------------------ |
  | 1    | DISCARD：取消事务，放弃事务块内的所有命令                    |
  | 2    | EXEC：执行所有事务块内的命令                                 |
  | 3    | MULTI：标记一个事务块的开始                                  |
  | 4    | UNWATCH：取消WATCH命令对所有key的监控                        |
  | 5    | WATCH key [key...] 监视一个（或多个）key，如果在执行之前这个（或这些）key被其他命令所改动，那么事务将被打断 |

  * Case1：正常执行

  - Case2：放弃事务
  - Case3：全体连坐 直接报错
  - Case4：冤头债主 正常加入队列不报错
  - **Case5：watch监控**： 先监控后开启事务
    - 乐观锁/悲观锁/CAS（Check And Set）
      - 表锁（悲观锁）：有人修改时锁整张表。一致性极好 但是并发性不行
        - 悲观锁(Pessimistic Lock),顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁
      - 行锁（乐观锁）：会多个Version版本号，如果修改和拿到的版本号不一样 则需要重新修改 有人改 锁定改的行
        - 乐观锁(Optimistic Lock),顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断- -下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量I
        - 乐观锁策略:提交版本必须大于记录当前版本才能执行更新
    - 初始化信用卡可用余额和欠额
    - 无加赛篡改，先监控在开启multi，保证两笔金额变动在一个事物内
    - 有加塞篡改
    - unwatch
    - 一旦执行了exec之前加的监控锁都会被取消掉了
    - 小结
      - WATCH指令，类似乐观锁，事务提交时，如果key的值都已被别的客户端改变，比如某个list已被别的客户端push/pop过了，整个事务队列都不会被执行
      - 通过WATCh 命令在事务执行之前监控了多个keys，倘若在WATCH之后有任何key值发生了变化，EXEC命令执行的事务都将被放弃，同时返回NUllmulti-bulk应答以通知调用者事务执行失败

- 阶段

  - 开启：以MULTI开始一个事务
  - 入队：将多个命令入队到事务中，街道这些命令并不会立即执行，而是放到等待执行的事务的队列里边
  - 执行：由EXEC命令触发事务

- 特性

  - 单独的隔离操作:事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断.
  - 没有隔离级别的概念:队列中的命令没有提交之前都不会实际的被执行，因为事务提交前任何指令都不会被实际执行，也就不存在”事务内的查询要看到事务里的更新，在事务外查询不能看到”这个让人万分头痛的问题
  - 不保证原子性: redis同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚

### Redis的发布订阅

ps:（很少有人拿redis去订阅发布消息）

* 是什么
  * 进程间的一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息
  * 订阅/发布
* 命令
* 案例:
  * 先订阅后发布后才能收到消息，
    1、可以一次性订阅多个，SUBSCRIBE c1 c2 e3
    2、消息发布，PUBLISH c2 hello-redis
    3、订阅多个，通配符*PSUBSCRIBE new*
    4、收取消息，PUBL ISH new1 redis2015

### Redis 主从复制

* 是什么
  * 也就是我们所说的主从复制，主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主
* 能干嘛
  * 读写分离
  * 容灾恢复
* 怎么玩
  * 配从（库）不配主（库）
  * 从库配置：salveof 主库 IP 主库端口 eg:SALAVEOF 127.0.0.1 6379
    * 每次与master断开之后，都需要重新连接，除非你配置进redis.conf文件(从机死了 需要重新连接)
    * Info replication
  * 修改配置文件细节操作
    * 拷贝多个redis.conf文件
    * 开启daemonize yes
    * Pid文件名字
    * 指定端口
    * Log文件名字
    * Dump.rdb名字
  * 常用
    * 一主二仆：一个主机 两个仆机
    * 薪火相传
      * 上一个的Slave可以使下一个Slave的Master
    * 反客为主：SLAVEOF  no one 使当前数据库停止与其他数据库的同步，转成主数据库
* 复制原理：
  * Slave启动成功连接到msater后会发送一个sync命令
  * Master接到命令启动后台的存盘进程，同时手机所有接收到的用于修改数据集的命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，已完成一次完全同步
  * 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内容中
  * 增量复制：Master继续将新的所有收集到的修改命令个一次传给slave，完成同步
  * 但是只要是重新连接 master，一次完全同步（全量复制）将被自动执行
* 哨兵模式（sentinel）
  * 是什么：反客为主的自动版
  * 怎么玩：
    * 自定义的/myredis目录下新建sentinel.conf文件，名字绝对不能错：touch sentinel.conf
    * 配置哨兵，填写内容
      * sentinel monitor 被监控主机名字（自己起名字） 127.0.0.1 6379 1
      * 上面最后一个数字1，表示主机挂掉后salve投票看让谁接管成为新的主机
    * 启动哨兵 
    * 正常主从演示
    * 原有的master挂了
    * 投票新选
    * 重新主从继续开工，info replication查查看
    * 问题：如果之前的master回来 不会双master 会成为slave 跟这新的Msater干
* 复制的缺点：
  * 由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有-定的延迟，当系统很繁
    忙的时候，延迟问题会更加严重，Slave机器 数量的增加也会使这个问题更加严重。