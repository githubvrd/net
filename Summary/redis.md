## Redis

 Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。 

Redis 是一个key-value存储系统，支持多种存储类型，包括字符串(String)、哈希(Hash)、列表(list)、集合(sets)和有序集合(sorted sets)等类型。数据存储在内存中，会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现master-slave(主从)同步。 

Redis优势：

- 性能极高：Redis能读的速度是110000次/s，写的速度是81000次/s 
-  丰富的数据类型：Redis支持二进制案例的 String，List， Hash，Set 及 Ordered Set数据类型操作
-  原子：所有操作都是原子性的，要么全部执行成功，要么全部执行失败。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来
-  丰富的特性：支持 publish/subscribe，通知，key 过期等特性

### 1、Redis连接

```shell
1、 auth "密码"        # 验证密码是否正确
2、 echo "内容"        # 打印字符串
3、 ping              # 返回PONG，表示正在运行
4、 quit              # 退出，关闭连接
5、 select index      # 切换数据库，默认一共16个，可在配置文件中设置
```

### 2、Redis数据类型

|         类型         |                          简介                          |                             特性                             |                             场景                             |
| :------------------: | :----------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|    String(字符串)    |                       二进制安全                       | 可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M |                             ---                              |
|      Hash(字典)      |            键值对集合,即编程语言中的Map类型            | 适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去) |                   存储、读取、修改用户属性                   |
|      List(列表)      |                     链表(双向链表)                     |               增删快,提供了操作某一段元素的API               |     1,最新消息排行等功能(比如朋友圈的时间线)  2,消息队列     |
|      Set(集合)       |                 哈希表实现,元素不重复                  | 1、添加、删除,查找的复杂度都是O(1) 2、为集合提供了求交集、并集、差集等操作 | 1、共同好友  2、利用唯一性,统计访问网站的所有独立ip 3、好友推荐时,根据tag求交集,大于某个阈值就可以推荐 |
| Sorted Set(有序集合) | 将Set中的元素增加一个权重参数score,元素按score有序排列 |               数据插入集合时,已经进行天然排序                |                1、排行榜 2、带权重的消息队列                 |

### 3、Redis命令

#### 3.1、key（键）

|          命令           |                             描述                             |
| :---------------------: | :----------------------------------------------------------: |
|         del key         |                           删除key                            |
|        dump key         |                          序列化key                           |
|       exists key        |                         是否存在key                          |
|   expire key seconds    |               已存在的key设置过期时间，以秒计                |
| expireat key timestamp  | EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间，EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp) |
|         pexpire         |              已存在的key设置过期时间，以毫秒计               |
| pexpireat key timestamp |          设置 key 过期时间的时间戳(unix timestamp)           |
|      keys pattern       |                  查找所有符合给定模式的 key                  |
|       move key db       |          将当前数据库的 key 移动到给定的数据库db中           |
|       persist key       |                 移除key的过期时间，永久保持                  |
|        pttl key         |                以毫秒返回 key 的剩余过期时间                 |
|         ttl key         |                 以秒返回 key 的剩余过期时间                  |
|        randomkey        |                 从当前数据库随机返回一个key                  |
|    rename key newkey    |                        修改key的名称                         |
|   renamenx key newkey   |            仅当newkey不存在时，将key改名为newkey             |
|        type key         |                    返回key存储的值得类型                     |

#### 3.2、String（字符串）

|              命令              |                            描述                            |
| :----------------------------: | :--------------------------------------------------------: |
|         set key value          |                     设置指定 key 的值                      |
|            get key             |                     获取指定 key 的值                      |
|     getrange key start end     |   下标截取key字符串长度，start：开始位置，end：结束位置    |
|        getset key value        |                 key赋新值value，并返回旧值                 |
|     mget key1 [ key2... ]      |                   获取一个或多个key 的值                   |
|    setex key seconds value     |    value 关联到 key，设置 key 的过期时间（以秒为单位）     |
|        setex key value         |                    在key不存在时设置值                     |
|           strlen key           |                     返回 key 的值长度                      |
|  mset key1 value1 key2 value2  |              同时设置一个或多个 key-value 对               |
| msetnx key1 value1 key2 value2 | 同时设置一个或多个 key-value 对，仅当所有给定 key 都不存在 |
|            incr key            |                   将 key 储存的数字值加1                   |
|      incrby key increment      |              将 key 储存的值加上给定的增量值               |
|   incrbyfloat key increment    |            将 key 储存的值加上给定的浮点增量值             |
|            decr key            |                   将 key 储存的数字值减1                   |
|      decrby key increment      |              将 key 储存的值加上给定的减量值               |
|        append key value        |                   追加到字符串key的末尾                    |

#### 3.3、Hash（哈希）

| 命令 | 描述 |
| :--: | :--: |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |

#### 3.9、发布订阅

Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息

Redis 客户端可以订阅任意数量的频道

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub1.png)![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub2.png)

实例：

- 开启两个 redis-cli 客户端
- 第一个客户端输入 **subscribe chat**，订阅 ` chat` 频道
- 第二个客户端输入 **publish chat "hi"**， 往 ` chat` 频道发送消息，这时第一个客户端就会看到由第二个 redis-cli 客户端发送的测试消息

![img](https://www.runoob.com/wp-content/uploads/2014/11/redis-pub-sub.gif) 

#### 3.10、事务

Redis 事务可以一次执行多个命令，并且带有以下三个重要的保证：

- 批量操作在执行 EXEC 命令前放入队列缓存
- 收到 EXEC 命令进入事务执行，事务中任意命令执行失败，其余的命令依然被执行
- 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中

一个事务从开始到执行会经历以下三个阶段：

- 开始事务
- 命令入队
- 执行事务

实例：

```shell
127.0.0.1:6379[1]> multi                    # 开始事务
OK
127.0.0.1:6379[1]> set book ".net"          # 命令入队
QUEUED
127.0.0.1:6379[1]> get book
QUEUED
127.0.0.1:6379[1]> sadd tag "net"
QUEUED
127.0.0.1:6379[1]> smembers tag
QUEUED
127.0.0.1:6379[1]> exec                     # 执行事务
1) OK                                       # 返回结果
2) ".net"
3) (integer) 1
4) 1) "net"
```

单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加维持原子性的机制，所以 Redis 事务执行并不是原子性的

事务可以理解为一个批量执行脚本，但批量指令并非原子性操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会停止后续的指令

### 4、Redis常见问题

#### 4.1、Redis内存淘汰机制

redis 内存淘汰机制（MySQL里有2000w数据，Redis中只存20w的数据，如何保证Redis中的数据都是热点数据？）

redis 提供 6种数据淘汰策略：

1. volatile-lru：从已设置过期时间的数据集中挑选最近最少使用的数据淘汰
2. volatile-ttl：从已设置过期时间的数据集中挑选将要过期的数据淘汰
3. volatile-random：从已设置过期时间的数据集中挑选任意数据淘汰
4. allkeys-lru：移除最近最少使用的key（最常用）
5. allkeys-random：随机淘汰任意数据
6. no-enviction： 返回错误内存限制达到 

#### 4.2、Redis持久化

持久化数据将内存中的数据写入硬盘，是为了之后重用数据（比如重启机器、机器故障之后回复数据），或者是为了防止系统故障而将数据备份到一个远程位置

Redis两种持久化方式

1. 快照（snapshotting，RDB）

   ​	通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。Redis创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis主从结构，主要用来提高Redis性能），还可以将快照留在原地以便重启服务器的时候使用。

   触发RDB快照方式：

   1）在指定的时间间隔内，执行指定次数的写操作

   ```shell
   save 900 1  # 在900秒之后，至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照 
   save 300 10  # 在300秒之后，至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照
   save 60 10000   # 在60秒之后，至少有10000个key发生变化
   ```

   2）执行save（阻塞， 只管保存快照，其他的等待） 或者是bgsave （异步）命令

   3） 执行flushall 命令，清空数据库所有数据，意义不大

   4） 执行shutdown 命令，保证服务器正常关闭且不丢失任何数据，意义...也不大

   RBD优缺点：

   **优点：**

   1 适合大规模的数据恢复

   2 如果业务对数据完整性和一致性要求不高，RDB是很好的选择

   **缺点：**

   1 数据的完整性和一致性不高，因为RDB可能在最后一次备份时宕机了

   2 备份时占用内存，因为Redis 在备份时会独立创建一个子进程，将数据写入到一个临时文件（此时内存中的数据是原来的两倍哦），最后再将临时文件替换之前的备份文件

2. 追加文件(append-only file，AOF）

   ​	AOF持久化 的实时性更好，因此已成为主流的持久化方案。默认情况下Redis没有开启AOF（append only file）方式的持久化，可以通过appendonly参数开启：

   ```shell
   appendonly yes
   ```

   ​	开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件。AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的，默认的文件名是appendonly.aof。

   在Redis的配置文件中存在三种不同的 AOF 持久化方式，它们分别是：

   ```
   appendfsync always     # 每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度 
   appendfsync everysec   # 每秒钟同步一次，显示地将多个写命令同步到硬盘 
   appendfsync no         # 让操作系统决定何时进行同步
   ```

   ### 4.3、缓存相关问题及解决方案

   1、**缓存穿透**

   查询一个一定不存在的数据，由于缓存没有命中查询数据库。若流量大时，数据库可能会宕机。

   解决方案：

   布隆过滤器：将所有可能存在的key放到一个足够大bitmap中。一定不存在的数据直接被bitmap拦截掉。将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。另外也有一个更为简单粗暴的方法（我们采用的就是这种），如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。

   短时缓存：若数据库查询的数据为空，扔把key缓存起来，设置一个短时间的过期时间。数据插入后清理

   2、**缓存击穿**：某个热点key失效后，大并发访问，导致数据库宕机。

   解决方案：

   加锁排队：互斥锁对某个key只允许一个线程查数据写缓存，其他线程等待。

   3、**缓存雪崩**：缓存时集中在某一时段同时失效，请求全部转发到数据库，数据库瞬时压力过重导致雪崩效应。
   解决方案：

   缓存时间增加随机值：每个缓存时间不一样，避免集体失效

   4、**缓存预热**：系统上线前把热点数据加入redis缓存中

