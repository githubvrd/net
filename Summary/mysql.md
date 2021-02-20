## MySQL

- MySQL是一个**关系型数据库管理系统**，由瑞典MySQL AB公司开发，目前属于 **Oracle** 旗下产品。MySQL 是最流行的**关系型数据库管理系统**之一，在 WEB 应用方面，MySQL是最好的 **RDBMS** (Relational Database Management System，关系数据库管理系统) 应用软件。
- MySQL是一种关系数据库管理系统，关系数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。
- MySQL所使用的 SQL 语言是用于访问数据库的最常用标准化语言。MySQL 软件采用了双授权政策，分为社区版和商业版，由于其体积小、速度快、总体拥有成本低，尤其是开放源码这一特点，一般中小型网站的开发都选择 MySQL 作为网站数据库。
- 由于其社区版的性能卓越，搭配 **PHP** 和 **Apache** 可组成良好的开发环境。

### MySQL下载

- 官网提供的安装MySQL的方式有三种：
  - 在线安装版：mysql-installer-web-community.msi
  - 离线安装版：mysql-installer-community.msi
  - 解压缩版：mysql-8.0.13-winx64.zip
- 但实质上只有两种，一种是用安装包安装，一种是用压缩包安装。
  - 官网下载：https://dev.mysql.com/downloads/mysql/
- 当然如果你想方便快捷，可以下载我提供的安装包
  - 离线安装版：[mysql-installer-community.msi](http://coderyyn.cn/DataBase/mysql-installer-community-8.0.13.0.msi)
  - 解压缩版：[mysql-8.0.13-winx64.zip](http://coderyyn.cn/DataBase/mysql-8.0.13-winx64.zip)

### MySQL安装

#### 一、配置文件

1、新建文件，命名为 `     my.ini` 

2、配置文件添加内容

```shell
[mysqld] 
basedir=C:/mysql-8.0.13-winx64                   # 设置mysql的安装目录，也就是刚才我们解压的目录
# datadir=C:/mysql-8.0.13-winx64/data            # 设置mysql数据库的数据的存放目录
port=3306                                        # 设置默认使用的端口
max_connections=200                              # 允许最大连接数
max_connect_errors=10                            # 允许连接失败的次数。为了防止有人攻击数据库
character-set-server=utf8mb4                     # 服务端使用的字符集
collation-server=utf8mb4_general_ci              # 数据库字符集对应一些排序等规则使用的字符集
default-storage-engine=INNODB                    # 创建新表时将使用的默认存储引擎
default_authentication_plugin=mysql_native_password   
# 默认使用“mysql_native_password”插件作为认证加密方式
# MySQL8.0默认认证加密方式为caching_sha2_password
 
[mysql]
default-character-set=utf8mb4                    # 设置mysql客户端默认字符集
 
[client]
default-character-set=utf8mb4
port=3306
```

#### 二、MySQL安装

- 解压下载压缩包放到相应的目录
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217010621353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 用  **管理员**   打开CMD，切换到MySql的解压目录下的`bin`目录
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217010832280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 输入命令 `mysqld --initialize --console` 来初始化数据库，记录红色标注的字符，这是随机生成的密码
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217012540246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
   这时有可能会出现error，原因是已经有了data目录，然后删除即可， 
  
- 输入 `mysqld -install MySQL` 将mysql安装为Windows的服务，服务名为 `MySQL` (可修改)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217011523666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 输入命令 `net start mysql` 或 `sc start mysql` 启动mysql服务
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217012004839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 输入 `mysql -u root -p` 来登陆数据库，并输入前面记录的临时密码：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217012813991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 登陆成功后输入命令 `alter user 'root'@'localhost' identified by '想要设置的密码';` 将原来复杂的密码修改为自己的密码，并输入`commit;`提交：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217012957673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 输入`quit`退出数据库，再次登陆数据库，这时我们用的是我们修改后的密码登陆的：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217013306574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)
  
- 最后我们将Mysql的bin目录配置到环境变量中，方便我们下次启动，而不用切换路径：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181217013447127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ljaGd5eW4=,size_16,color_FFFFFF,t_70)

- **停止MySQL服务：**`net stop mysqld`或`sc stop mysqld`

- **删除MySQL服务：**`sc delete mysqld`或`mysqld -remove`（需先停止服务）

### MySQL主从复制

#### 一、准备数据库

　　数据库1（主服务器）：localhost:3306

　　数据库2（从服务器）：localhost:3307

#### 二、 配置数据库

##### 1、配置主库

（1）在安装目录的 `my.ini` 文件中添加配置

``` shell
[mysqld]
port=3306
basedir=D:\mysql
datadir=D:\mysql\data
max_connections=200
max_connect_errors=10
character-set-server=utf8mb4
default-storage-engine=INNODB
default_authentication_plugin=mysql_native_password

# 在[mysqld]中添加配置
server-id=3306                 #服务器 id 
log-bin=mysql-bin              #二进制文件存放路径
binlog-do-db=testdb            #待同步的数据库
binlog-ignore-db=mysql         #不同步的数据库

[mysql]
default-character-set=utf8mb4

[client]
port=3306
default-character-set=utf8mb4
```

（2）登录mysql 

```shell
mysql -u root -p密码
```

（3）用户授权

  ```shell
# 创建用户
CREATE USER 'rootslave'@'localhost' IDENTIFIED WITH mysql_native_password BY 'admin'; 
# rootslave： 用户名称
# localhost： 数据库地址
# admin：     主从复制连接密码

# 授权
grant replication slave on *.* to 'rootslave'@'localhost';
# 'rootslave'@'localhost'： 对应创建用户的信息
  ```

（4）重启mysql

```shell
net stop mysql    # 停止mysql服务
net start mysql   # 开启mysql服务
```

（5）查看master状态  `show master status;` ，记住File和Position的内容，下面会用到

![1610963103126](C:\Users\ZS\AppData\Roaming\Typora\typora-user-images\1610963103126.png)

 

##### 2、配置从库

（1）在安装目录的 `my.ini` 文件中添加配置

   ```shell
[mysqld]
port=3307
basedir=D:\mysqlsalve
datadir=D:\mysqlsalve\data
max_connections=200
max_connect_errors=10
character-set-server=utf8mb4
default-storage-engine=INNODB
default_authentication_plugin=mysql_native_password

# 在[mysqld]中添加配置
server-id=3307                     # 服务器 id 
log-bin=mysql-bin                  # 二进制文件存放路径
replicate-do-db=testdb             # 待同步的数据库
replicate-ignore-db=mysql          # 不同步的数据

[mysql]
default-character-set=utf8mb4

[client]
port=3307
default-character-set=utf8mb4
   ```

（2）重启mysql

```shell
net stop mysql    # 停止mysql服务
net start mysql   # 开启mysql服务
```

 （3）实现主从复制

```shell
1. mysql -u root -p密码                          # 登录mysql

2. mysql> stop slave;                           # 关闭从库

3. mysql> 
change master to master_host='localhost', master_user='rootslave', master_password='admin', master_log_file='mysql-bin.000010', master_log_pos=156;
# localhost：            对应主数据库的地址
# rootslave：            对应主数据库的用户名称
# admin：                对应主数据库的连接密码
# mysql-bin.000010       对应主数据库的“master status”的File内容
# 156                    对应主数据库的“master status”的Position内容

4. mysql> start slave;                          # 开启从库

5. mysql> show slave status \G;                 # 检查服务器状态

```

![1611019785662](C:\Users\ZS\AppData\Roaming\Typora\typora-user-images\1611019785662.png)

​     **如图无错误，说明主从配置成功**

##### 3、实现主从复制

​	直接进入主库创建 `testdb` 数据库，从库也自动创建，在主库添加数据，从库会自动添加相同的数据，说明已经配置成功

![1611020841541](C:\Users\ZS\AppData\Roaming\Typora\typora-user-images\1611020841541.png)

<center>主库创建“testdb”数据库，创建table1表</center>
![1611020506923](C:\Users\ZS\AppData\Roaming\Typora\typora-user-images\1611020506923.png)

<center>从库自动创建“testdb”数据库，创建table1表</center>
到此，数据库主从复制已搭建完成。

### MySQL知识点

#### 1、MyISAM 和 InnoDB 的区别

**区别内容：**

1. InnoDB支持事务，InnoDB每条SQL都会默认封装成事务，进行自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务；

   MyISAM不支持事务

2. InnoDB支持外键，MyISAM不支持，对一个包含外键的InnoDB表转为MYISAM会失败

3. InnoDB是聚集索引，数据文件是和索引绑在一起的，必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。

   MyISAM是非聚集索引，数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的

4. InnoDB不保存表的具体行数，执行select count(*) from table时需要全表扫描；

   MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快

5. Innodb不支持全文索引，而MyISAM支持全文索引，查询效率上MyISAM要高

**如何选择：**

1. 是否要支持事务，如果要请选择innodb，如果不需要可以考虑MyISAM；
2. 如果表中绝大多数都只是读查询，可以考虑MyISAM，如果既有读写也挺频繁，请使用InnoDB
3. 系统奔溃后，MyISAM恢复起来更困难，能否接受；
4. MySQL5.5版本开始Innodb已经成为Mysql的默认引擎(之前是MyISAM)，说明其优势是有目共睹的，如果你不知道用什么，那就用InnoDB，至少不会差。

#### 2、数据库事务

**2.1、ACID事务特性**

**A**：Atomicity原子性

一个事务中的所有操作，要么全部完成，要么全部不完成，不会在中间某个环节结束。事务在执行过程中发生错误，会被回滚到事务开始前的状态，就像这个事务从来没有执行过一样。

**C**：Consistency一致性

在事务开始之前和事务结束以后，数据库数据的一致性约束没有被破坏。

**I**：Isolation隔离性

数据库允许多个并发事务同时对数据进行读写和修改的能力。隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。

**D**：Durability持久性

事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

**2.2、事务并发问题**

多事务并发进行会造成以下几个问题:

（1）脏读：A事务读取到了B事务未提交的内容，而B事务后面进行了回滚。

（2）不可重复读：当A事务读取B事务提交的结果数据，造成在A事务内的两次查询结果不一样，因为在此期间B事务进行了提交操作。

（3）幻读：A事务读取了一个范围的内容，而同时B事务在此期间插入了一条数据，造成"幻觉"。

 **2.3、解决事务并发问题，事务隔离**

MySQL的四种隔离级别：

（1）读未提交（READ UNCOMMITTED）RU

​	其他事务可以看到本事务没有提交的部分修改，因此造成脏读的问题（读取到了其他事务未提交的部分，而之后该事务进行了回滚）

（2）读已提交（READ COMMITTED）RC

​	其他事务只读取到本事务已经提交的部分，这个隔离级别有不可重复读的问题，在同一个事务内的两次读取，拿到的结果竟然不一样，因为另外一个事务对数据进行了修改。

（3）可重复读（REPEATABLE READ）RR

​	可重复读解决了不可重复读的问题，但是仍然有一个新问题，就是幻读，当读取 id>10 的数据行时，对涉及到的所有行加上了读锁，此时另外一个事务新插入了一条id=11的数据，因为是新插入的，所以不会触发上面的锁的排斥，那么进行本事务进行下一次的查询时会发现有一条id=11的数据，而上次的查询操作并没有获取到，再进行插入就会有主键冲突的问题。

（4）可串行化（SERIALIZABLE）

​	最高的隔离级别，可以解决上面提到的所有问题，因为他强制将所以的操作串行执行，这会导致并发性能极速下降，因此也不是很常用。

**2.4、Innodb隔离级别**

Innodb默认使用的是可重复读隔离级别

**2.5、数据库锁**

1、数据库并发事务时，可能会产生数据的不一致，这时需要一些机制来保证访问的次序，锁机制就是这样的一个机制

举例：就像酒店的房间，如果大家随意进出，就会出现多人抢夺同一个房间的情况，而在房间上装上锁，申请到钥匙的人才可以入住并且将房间锁起来，其他人只有等他使用完毕才可以再次使用

2、锁类别，共享锁和排他锁：

共享锁：又叫做读锁，当用户要进行数据的读取时，对数据加上共享锁，共享锁可以同时加上多个。

排他锁：又叫做写锁，当用户要进行数据的写入时，对数据加上排他锁，排他锁只可以加一个，他和其他的排他锁，共享锁都相斥。

锁的粒度取决于具体的存储引擎，InnoDB实现了行级锁，页级锁，表级锁。加锁开销从大到小，并发能力也是从大到小。

举例：用上面的例子来说就是用户的行为有两种，一种是来看房，多个用户一起看房是可以接受的， 一种是真正的入住一晚,在这期间，无论是想入住的还是想看房的都不可以。

#### 3、索引

**3.1、什么是索引**

索引就是一种数据结构，用于快速查找数据。

索引的数据结构和具体存储引擎有关，在MySQL中使用较多的索引有Hash索引，B+树索引。

InnoDB存储引擎默认索引方式为：B+树索引。

**3.2、Hash索引，B+树索引区别** 

Hash索引和B+树索引底层实现原理 ：

Hash索引底层就是hash表，进行查找时，调用一次hash函数就可以获取到相应的键值，之后进行回表查询获得实际数据

B+树底层实现是多路平衡查找树，对于每一次的查询都是从根节点出发，查找到叶子节点方可以获得所查键值，然后根据查询判断是否需要回表查询数据.

- hash索引进行等值查询更快(一般情况下)，但是却无法进行范围查询，因为在hash索引中经过hash函数建立索引之后，索引的顺序与原顺序无法保持一致，不能支持范围查询。而B+树的的所有节点皆遵循左节点小于父节点，右节点大于父节点，多叉树也类似，天然支持范围

- hash索引不支持使用索引进行排序，原理同上
- hash索引不支持模糊查询以及多列索引的最左前缀匹配。原理也是因为hash函数的不可预测。**AAAA**和**AAAAB**的索引没有相关性
- hash索引任何时候都避免不了回表查询数据，而B+树在符合某些条件（聚簇索引、覆盖索引等）的时候可以只通过索引完成查询
- hash索引虽然在等值查询上较快，但是不稳定。性能不可预测，当某个键值存在大量重复的时候，发生hash碰撞，此时效率可能极差。而B+树的查询效率比较稳定，对于所有的查询都是从根节点到叶子节点，且树的高度较低

因此，在大多数情况下，直接选择B+树索引可以获得稳定且较好的查询速度，而不需要使用hash索引

#### 4、三大范式

4.1、第一范式，确保每列保持原子性，不可在拆分

4.2、第二范式，确保表中的每列都和主键相关，而不能与主键一部分相关

4.3、第三范式， 确保每列都和主键列直接相关，而不是间接相关