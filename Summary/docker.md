## 一. docker架构

Docker 包括三个基本概念:

**镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。

**容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

**仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。

Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。

Docker 容器通过 Docker 镜像来创建。

![img](https://www.runoob.com/wp-content/uploads/2016/04/576507-docker1.png)

|      概念       | 说明                                                         |
| :-------------: | :----------------------------------------------------------- |
|  镜像(Images)   | Docker 镜像是用于创建 Docker 容器的模板                      |
| 容器(Container) | 容器是独立运行的一个或一组应用，是镜像运行时的实体           |
| 客户端(Client)  | Docker 客户端通过命令行或者其他工具使用Docker SDK与Docker的守护进程通信 |
|   主机(Host)    | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器         |
|    Registry     | Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 **<仓库名>:<标签>** 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 **latest** 作为默认标签。 |
|     Machine     | Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker |

## 二. docker镜像

### 1. docker镜像命令

```shell
## docker镜像
docker version                    		 # 查看docker版本

docker search redis               		 # 搜索镜像
	
docker pull redis                 		 # 拉取镜像

docker images                     		 # 查看所有镜像

docker inspect redis[镜像名|镜像ID]  	  # 查看镜像详细信息     

# 导出镜像，打包文件绝对路径+文件名称
docker save redis[镜像ID|镜像名称] > f:\Doc\docker\redis.tar 

# 导入镜像，导入文件绝对路径+文件名称
docker load < f:\Doc\docker\redis.tar        			   

# 删除镜像
docker rmi redis[镜像名|镜像ID]   		  # 删除单个镜像
docker rmi $(docker images -q)     		 # 删除所有镜像，Windows操作失败

docker tag redis[镜像名|镜像ID] redis:1.0  # 镜像重命名，生成新的镜像文件
```

### 2. Dockerfile构建镜像

 Dockerfile是一个包含用于组合映像的命令的文本文档，可以使用在命令行中调用任何命令。 

#### **2.1 Dockerfile文件说明**

 ![img](https://images2017.cnblogs.com/blog/911490/201712/911490-20171208222222062-849020400.png) 

#### **2.2 Dockerfile常用指令**

```shell
1).FROM：指定基础镜像，必须为第一个命令
    FROM <image>
    FROM <image>:<tag>
示例：
    FROM mysql:5.6
# tag是可选的，如果不使用这个值时，会使用latest版本的基础镜像

2).MAINTAINER: 创建者信息
    MAINTAINER <name>
示例：
    MAINTAINER zhsh@163.com
    
3).RUN：构建镜像执行的命令
	RUN用于在镜像容器中执行命令，其有以下两种命令执行方式：
	shell执行
	格式：
    	RUN <command>
	exec执行
	格式：
    	RUN ["executable", "param1", "param2"]
	示例：
    	RUN ["executable", "param1", "param2"]
    	RUN apk update
    	RUN ["/etc/execfile", "arg1", "arg1"]
# RUN指令创建的中间镜像会被缓存，并会在下次构建中使用。如果不想使用这些缓存镜像，可以在构建时指定--no-cache参数，如：docker build --no-cache

4).ADD：将本地文件添加到容器中，tar类型文件会自动解压(网络压缩资源不会被解压)，可以访问网络资源
	ADD <src>... <dest>
    ADD ["<src>",... "<dest>"] 用于支持包含空格的路径
示例：
    ADD hom* /mydir/          # 添加所有以"hom"开头的文件
    ADD hom?.txt /mydir/      # ? 替代一个单字符,例如："home.txt"
    ADD test relativeDir/     # 添加 "test" 到 `WORKDIR`/relativeDir/
    ADD test /absoluteDir/    # 添加 "test" 到 /absoluteDir/

5).COPY：类似ADD，但是是不会自动解压文件，也不能访问网络资源

6).CMD：构建容器后调用，也就是在容器启动时才进行调用
    CMD ["executable","param1","param2"] (执行可执行文件，优先)
    CMD ["param1","param2"] (设置了ENTRYPOINT，则直接调用ENTRYPOINT添加参数)
    CMD command param1 param2 (执行shell内部命令)
示例：
    CMD echo "This is a test." | wc -
    CMD ["/usr/bin/wc","--help"]
# CMD不同于RUN，CMD用于指定在容器启动时所要执行的命令，而RUN用于指定镜像构建时所要执行的命令

7).ENTRYPOINT：配置容器，可执行化。配合CMD可省去"application"，只使用参数
	ENTRYPOINT ["executable", "param1", "param2"] (可执行文件, 优先)
    ENTRYPOINT command param1 param2 (shell内部命令)
示例：
    FROM ubuntu
    ENTRYPOINT ["top", "-b"]
    CMD ["-c"]
# ENTRYPOINT与CMD非常类似，不同的是通过docker run执行的命令不会覆盖ENTRYPOINT，而docker run命令中指定的任何参数，都会被当做参数再次传递给ENTRYPOINT。Dockerfile中只允许有一个ENTRYPOINT命令，多指定时会覆盖前面的设置，而只执行最后的ENTRYPOINT指令。

8).LABEL：用于为镜像添加元数据
	LABEL <key>=<value> <key>=<value> <key>=<value> ...
示例：
　　LABEL version="1.0" description="这是一个Web服务器" by="IT笔录"
# 使用LABEL指定元数据时，一条LABEL指定可以指定一或多条元数据，指定多条元数据时不同元数据之间通过空格分隔。推荐将所有的元数据通过一条LABEL指令指定，以免生成过多的中间镜像。

9).ENV：设置环境变量
	ENV <key> <value>  #<key>之后的所有内容均会被视为其<value>的组成部分，因此，一次只能设置一个变量
    ENV <key>=<value> ...  #可以设置多个变量，每个变量为一个"<key>=<value>"的键值对，如果<key>中包含空格，可以使用\来进行转义，也可以通过""来进行标示；另外，反斜线也可以用于续行
示例：
    ENV myName John Doe
    ENV myDog Rex The Dog
    ENV myCat=fluffy

10).EXPOSE：对外交互端口
	EXPOSE <port> [<port>...]
示例：
    EXPOSE 80 443
    EXPOSE 8080
    EXPOSE 11211/tcp 11211/udp
# EXPOSE并不会让容器的端口访问到主机。要使其可访问，需要在docker run运行容器时通过-p来发布这些端口，或通过-P参数来发布EXPOSE导出的所有端口

11).VOLUME：指定持久化目录
	VOLUME ["/path/to/dir"]
示例：
    VOLUME ["/data"]
    VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"
# 一个卷可以存在于一个或多个容器的指定目录，该目录可以绕过联合文件系统，并具有以下功能：
  1 卷可以容器间共享和重用
  2 容器并不一定要和其它容器共享卷
  3 修改卷后会立即生效
  4 对卷的修改不会对镜像产生影响
  5 卷会一直存在，直到没有任何容器在使用它
         
12).WORKDIR：工作目录，类似于cd命令
	WORKDIR /path/to/workdir
示例：
    WORKDIR /a  (这时工作目录为/a)
    WORKDIR b  (这时工作目录为/a/b)
    WORKDIR c  (这时工作目录为/a/b/c)
# 通过WORKDIR设置工作目录后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT、ADD、COPY等命令都会在该目录下执行。在使用docker run运行容器时，可以通过-w参数覆盖构建时所设置的工作目录

13).USER:指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户
	USER user
	USER user:group
	USER uid
	USER uid:gid
	USER user:gid
	USER uid:group
示例：
　　USER www
# 使用USER指定用户后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT都将使用该用户。镜像构建完成后，通过docker run运行容器时，可以通过-u参数来覆盖所指定的用户

......
```

#### **2.3 Dockerfile构建nginx**

```shell
## 以nginx构建镜像：nginx Dockerfile

FROM centos                                     # 基础镜像                   

MAINTAINER zhsh                                 # 创建者信息

ENV PATH /usr/local/nginx/sbin:$PATH            # 设置环境变量

ADD nginx-1.8.0.tar.gz /usr/local/              # 文件放在当前目录下，拷过去会自动解压
ADD epel-release-latest-7.noarch.rpm /usr/local/  

# 执行命令 
RUN rpm -ivh /usr/local/epel-release-latest-7.noarch.rpm
RUN yum install -y wget lftp gcc gcc-c++ make openssl-devel pcre-devel pcre && yum clean all
RUN useradd -s /sbin/nologin -M www

WORKDIR /usr/local/nginx-1.8.0                  # 相当于cd，工作目录

RUN ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module --with-pcre && make && make install

RUN echo "daemon off;" >> /etc/nginx.conf

EXPOSE 80                                       # 映射端口

CMD ["nginx"]                                   # 运行命令
```

```shell
## docker build命令从Dockerfile构建映像

docker build -t -f /F:/Doc/Dockerfile .  # 在docker build命令中使用-f指向系统中Dockerfile位置
docker build -t Dockerfile .             # 在Dockerfile文件目录构建
```

## 三. docker容器

```shell
## docker容器命令
docker ps -a       				         # 查看所有容器

docker ps -l       				         # 查看最后一次创建的容器

docker logs [-f] redis[容器名|容器ID]      # 查看容器日志

docker inspect redis | grep IPAddress    # docker查看容器IP地址
	"IPAddress": "172.17.0.2"

docker port redis[容器名|容器ID]           # docker查看容器端口

docker top redis[容器名|容器ID]            # 查看容器进程

docker inspect redis[容器名|容器ID]        # 查看容器详细信息

docker start redis[容器名|容器ID]          # 启动容器

docker restart redis[容器名|容器ID]        # 重启容器

# 停止容器
docker stop redis[容器名|容器ID]  		  # 停止单个容器
docker stop $(docker ps -aq)    		 # 停止所有容器，Windows操作失败

# 删除容器
docker rm redis[容器名|容器ID]    		  # 删除单个容器
docker rm $(docker ps -aq)     			 # 删除所有容器，Windows操作失败

# 导出容器快照
docker export redis[容器ID|容器名称] > f:\Doc\docker\redis.tar 

# 导入容器快照
docker import - f:\Doc\docker\redis.tar 

exit 									 # 退出容器
```

```shell
## docker容器连接方式
# 本地连接
docker exec -it redis redis-cli -a 123456                         # 默认 127.0.0.1
docker exec -it redis redis-cli -h localhost -p 6379 -a 123456    # localhost
docker exec -it redis redis-cli -h 127.0.0.1 -p 6379 -a 123456    # 127.0.0.1
docker exec -it redis redis-cli -h 172.17.0.2 -p 6379 -a 123456   # 容器内IP地址
# 远程连接
docker exec -it redis redis-cli -h 192.168.1.195 -p 6379 -a 123456
## 命令描述
-h 本地IP（localhost/127.0.0.1/172.17.0.2）、远程IP(192.168.1.195)   # 连接IP地址
-p 6379                                                           # 指定端口号
-a 123456                                                         # 连接密码
```

## 四. docker运行实例

```shell
## docker运行redis
docker run 
-d
-it
--name redis
-p 6379:6379 
-v /usr/redis/redis.conf:/etc/redis/redis.conf 
-v /d/volume/data/:/data 
redis 
--appendonly yes 
--requirepass "123456"  

## 命令描述
-d 			       			 # 容器是前台还是后台运行，默认为后台

-it                			 # -i：交互模式运行，-t：为容器分配一个伪输入终端

--name redis        		 # 容器名称redis

-p 6379:6379        		 # 容器暴露指定端口6379，把容器内的6379端口映射到宿主机6379端口

-P                  		 # 容器暴露随机端口

-v /usr/redis/redis.conf:/etc/redis/redis.conf  
# 容器挂载目录，把宿主机配置好的redis.conf放到容器内etc/redis/redis.conf

-v /d/volume/data/:/data  
# 容器挂载数据卷，进行数据备份，把redis持久化的数据放在宿主机的 d/volume/data 文件中

redis[镜像名|镜像ID] 		  # 指向redis镜像文件

--appendonly yes   			 # 执行redis启动命令，打开redis持久化配置，使用aof持久化方式
```

```shell
## docker运行nginx镜像
# docker进入容器方式

# docker run 
docker run   
-d                           # 容器后台运行
--name nginx                 # 容器名称
-p 8050:80        			 # 容器暴露指定端口8050，把容器内的80端口映射到宿主机8050端口 
nginx[镜像名|镜像ID]           # 指向nginx镜像文件

# docker exec
docker exec 
-it 			             # 终端交互模式
nginx[容器名|容器ID]           # 指向nginx镜像文件
/bin/bash                    # 进入容器内

# docker attach
docker attach nginx[容器名|容器ID]
# 本机再打开一个终端，输入以下命令，观察上面终端的输出
curl localhost:8050          # 输出正常记录
curl localhost:8050/index    # 输出错误记录
```

```shell
## dokcer运行mysql
docker run 
-d                                 # 表示在后台运行容器

-p 3308:3308                       # 端口映射，用于暴露给外界访问

--name mysql                       # 容器名称

-v d/mysqldocker:/var/lib/mysql    
# 挂载数据卷，将mysql容器的/var/lib/mysql目录映射到宿主机的d:mysqldocker目录

-e MYSQL_ROOT_PASSWORD=admin       # 初始化root用户的密码

--restar=always                    # 自动重启，重启服务器之后不需要手动启动

mysql
```

## 五. 其他内容

```shell
## docker安装vim
apt-get update
apt-get install vim

## vim 退出
A: 按键盘左上角的“ESC”，接着输入“冒号（:）”，在输入命令“wq”（# w：write，写入  q：quit，退出）
再回车，保存退出

B：在最后输入命令，直接输入“x”，即：x=wq

C：按ESC后，直接按 shift+zz 或者切换到大写模式按 ZZ，就可以保存退出了，即是按两下大写的Z

1）正常退出
正常退出有个前提条件是：打开的文本文件在内容上没有被改动过。按了ESC后再输入“冒号”，在输入命令时，直接输入“q”

2）不保存退出
很多时候打开了文件或者修改了一些地方，才发现错了，需要不保存退出。先按ESC，再输入“冒号”，在输入命令时，直接输入“q!”

3）强制退出
不太常用的操作，先按ESC，再按“冒号”，在输入命令时，直接输入“!”，退出后，会有提示
```

```shell
## cmd访问网站地址
curl localhost:8050          
```

