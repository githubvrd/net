## Nginx

Nginx功能丰富，可作为HTTP服务器、反向代理服务器、邮件服务器。其特点是占有内存少，并发能力强，支持FastCGI、SSL、Virtual Host、URL Rewrite、Gzip等功能和第三方的模块扩展。Nginx的稳定性、功能集、示例配置文件和低系统资源的消耗让他在全球的使用比率很高。

### 一、Nginx常用功能

1、Http代理，反向代理：作为web服务器最常用的功能之一，尤其是反向代理。

![img](https://images2015.cnblogs.com/blog/398358/201602/398358-20160202133724350-1807373891.jpg)

（1）正向代理：

访问外网，先找到一个可以访问国外网站的代理服务器，我们将请求发送给代理服务器，代理服务器去访问国外的网站，然后将访问到的数据传递给我们，这样的代理模式称为正向代理。

正向代理最大的特点：

​		客户端非常明确要访问的服务器地址

​		服务器只清楚请求来自哪个代理服务器，而不清楚来自哪个具体的客户端

​		正向代理模式屏蔽或者隐藏了真实客户端信息

（2）反向代理：

客户端给服务器发送的请求，nginx服务器接收到之后，按照一定的规则分发给了后端的业务处理服务器进行处理了。此时请求的来源也就是客户端是明确的，但是请求具体由哪台服务器处理的并不明确了，nginx扮演的就是一个反向代理角色，反向代理隐藏了服务器的信息。

Nginx在做反向代理时，提供性能稳定，并且能够提供配置灵活的转发功能。Nginx可以根据不同的正则匹配，采取不同的转发策略，比如图片文件结尾的走文件服务器，动态页面走web服务器，并且Nginx对返回结果进行错误页跳转，异常判断等。如果被分发的服务器存在异常，他可以将请求重新转发给另外一台服务器，然后自动去除异常服务器。

2、负载均衡

当一台服务器的单位时间内的访问量越大时，服务器压力就越大，大到超过自身承受能力时，服务器就会崩溃。为了避免服务器崩溃，让用户有更好的体验，我们通过负载均衡的方式来分担服务器压力。

通过建立很多服务器，组成一个服务器集群，当用户访问网站时，先访问一个中间服务器，在让这个中间服务器在服务器集群中选择一个压力较小的服务器，然后将该访问请求引入该服务器。如此以来，用户的每次访问，都会保证服务器集群中的每个服务器压力趋于平衡，分担了服务器压力，避免了服务器崩溃的情况。

**负载均衡是用反向代理的原理实现的。**

Nginx提供的负载均衡策略有2种：内置策略和扩展策略

​		内置策略：轮询，加权轮询，Ip hash

​		扩展策略：可逐个实现负载均衡算法

（1）轮询（默认方式），加权轮询

 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

```shell
upstream backserver {
    server 192.168.0.14;
    server 192.168.0.15;
}
```

![img](https://images2015.cnblogs.com/blog/398358/201602/398358-20160202133753382-1863657242.jpg)

（2）ip_hash

Ip hash算法，对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题

```shell
upstream backserver {
    ip_hash;
    server 192.168.0.14:88;
    server 192.168.0.15:80;
}
```

![img](https://images2015.cnblogs.com/blog/398358/201602/398358-20160201162405944-676557632.jpg)

（3） fair ( 第三方 扩展策略)

 按后端服务器的响应时间来分配请求，响应时间短的优先分配

```shell
upstream backserver {
    server server1;
    server server2;
    fair;
}
```

每个设备的状态设置为：

​		down：表示单前的server暂时不参与负载

​		weight：默认为1，weight越大，负载的权重就越大

​		max_fails：请求失败的次数默认为1，当超过最大次数时，返回proxy_next_upstream模块定义的错误

​		fail_timeout:max_fails：一次失败后，暂停的时间

​		backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻

```shell
upstream backserver {
    ip_hash;
    server 192.168.1.100:8000;
    server 192.168.1.100:8001 down;
    server 192.168.1.100:8002 max_fails=3;
    server 192.168.1.100:8003 fail_timeout=20s;
    server 192.168.1.100:8004 max_fails=3 fail_timeout=20s;
}
```

3、web缓存

Nginx可以对不同的文件做不同的缓存处理，配置灵活，并且支持FastCGI_Cache，主要用于对FastCGI的动态程序进行缓存。配合着第三方的ngx_cache_purge，对制定的URL缓存内容可以的进行增删管理。

### 二、Nginx配置文件

1、 nginx.conf 默认配置

```shell
## 1.全局块：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等

#user  nobody;
worker_processes  1;
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#pid        logs/nginx.pid;

## 2.events块：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等

events {
    worker_connections  1024;
}

## 3.http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等

http {                                      ## http配置
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {                                ## server全局配置
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {                        ## location：配置请求的路由，以及各种页面的处理情况
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

nginx配置实例

```shell
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;                 #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;           #指定nginx进程运行文件存放地址
error_log log/error.log debug;       #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;       #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  	   #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;            #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;        #最大连接数，默认为512
}
http {
    include       mime.types;                #文件扩展名与文件类型映射表
    default_type  application/octet-stream;  #默认文件类型，默认为text/plain
    #access_log off;                         #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;       #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;    #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;       #连接超时时间，默认为65s，可以在http，server，location块。

    upstream mysvr {             # 负载均衡
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    
    error_page 404 https://www.baidu.com; #错误页
    
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #匹配访问根目录，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录，访问虚拟主机的web目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
} 
```

2、负载均衡实例

```shell
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
	
	upstream myservers {          # 负载均衡服务
		server localhost:8081;    # 请求8081端口
		server localhost:8082;    # 请求8082端口
	}
	
    server {
        listen       8050;        # nginx默认端口8050
        server_name  localhost;   # 服务器
        charset utf-8;
        
        location / {
            root   html;
            index  index.html index.htm;
			proxy_pass http://myservers;   # 负载均衡请求转向
        }
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

3、反向代理实例

```shell
server{
	    listen       9099;
        server_name  localhost;
        #charset utf-8;
        #站点访问
        location / {
		    root   E:/D2Admin;
            index  index.html;
		}
		#访问路径拼接 访问本地绝对路径下的静态资源：css,js,images
        location /D2Admin/ {
            alias E:/D2Admin/;
            autoindex on;
        }
		#api 接口访问
		location /api {
            add_header 'Access-Control-Allow-Origin' '*';
			add_header 'Access-Control-Allow-Credentials' 'true';
			add_header 'Access-Control-Allow-Methods' '*';
			add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
			proxy_pass_header Server;
		    proxy_set_header Host $http_host;
		    proxy_redirect off;
		    proxy_set_header X-Real-IP $remote_addr;
		    proxy_set_header X-Scheme $scheme;
		    proxy_http_version  1.1;
		    proxy_set_header    Upgrade    "websocket";
		    proxy_set_header    Connection "Upgrade";
		    proxy_read_timeout 36000s; #nginx服务器想被代理服务器组发出read请求后，等待响应的超时间
            proxy_pass  http://192.168.1.211:9279;
    }
}
	
	
proxy_connect_timeout 1;   #nginx服务器与被代理的服务器建立连接的超时时间，默认60秒

proxy_read_timeout 1; #nginx服务器想被代理服务器组发出read请求后，等待响应的超时间，默认为60秒

proxy_send_timeout 1; #nginx服务器想被代理服务器组发出write请求后，等待响应的超时间，默认为60秒

proxy_http_version 1.0 ; #Nginx服务器提供代理服务的http协议版本1.0，1.1，默认设置为1.0版本

#proxy_method get;    #支持客户端的请求方法。post/get

proxy_ignore_client_abort on;  #客户端断网时，nginx服务器是否终端对被代理服务器的请求。默认为off

proxy_ignore_headers "Expires" "Set-Cookie";  #Nginx服务器不处理设置的http相应投中的头域，这里空格隔开可以设置多个

proxy_intercept_errors on;    #如果被代理服务器返回的状态码为400或者大于400，设置的error_page配置起作用。默认为off

proxy_headers_hash_max_size 1024; #存放http报文头的哈希表容量上限，默认为512个字符

proxy_headers_hash_bucket_size 128; #nginx服务器申请存放http报文头的哈希表容量大小。默认为64个字符

proxy_next_upstream timeout;  #反向代理upstream中设置的服务器组，出现故障时，被代理服务器返回的状态值。error|timeout|invalid_header|http_500|http_502|http_503|http_504|http_404|off

#proxy_ssl_session_reuse on; 默认为on，如果我们在错误日志中发现“SSL3_GET_FINSHED:digest check failed”的情况时，可以将该指令设置为off。
```

