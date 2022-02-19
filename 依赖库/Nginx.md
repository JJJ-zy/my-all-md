# Nginx

## 简介

```tex
https://lnmp.org/nginx.html
Nginx是俄罗斯人编写的十分轻量级的HTTP服务器,Nginx，它的发音为“engine X”，是一个高性能的HTTP和反向代理服务器，同时也是一个IMAP/POP3/SMTP 代理服务器。

目前有很多国内网站采用Nginx作为Web服务器，如国内知名的新浪、163、腾讯、Discuz、豆瓣等
非常高效的反向代理、负载平衡
Nginx具有很高的稳定性。其它HTTP服务器，当遇到访问的峰值，或者有人恶意发起慢速连接时，也很可能会导致服务器物理内存耗尽频繁交换，失去响应，只能重启服务器。例如当前apache一旦上到200个以上进程，web响应速度就明显非常缓慢了。而Nginx采取了分阶段资源分配技术，使得它的CPU与内存占用率非常低。nginx官方表示保持10,000个没有活动的连接，它只占2.5M内存，所以类似DOS这样的攻击对nginx来说基本上是毫无用处的。就稳定性而言,nginx比lighthttpd更胜一筹。

Nginx支持热部署。它的启动特别容易, 并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够在不间断服务的情况下，对软件版本进行进行升级。
```

## 相关概念

### 反向代理

> 正向代理

```tex
代理客户端
一个位于客户端和原始服务器(origin server)之间的服务器
为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端才能使用正向代理。

在客户端(浏览器)配置代理服务器，通过代理服务器进行互联网访问
```

![image-20220111160335616](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111160335616.png)

> 反向代理

```tex
代理服务端
反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源。同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。反向代理服务器通常可用来作为Web加速，即使用反向代理作为Web服务器的前置机来降低网络和服务器的负载，提高访问效率
隐藏服务端，暴露代理服务器
```

![image-20220111161353719](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111161353719.png)

### 负载均衡

```tex
负载均衡（Load Balance）其意思就是分摊到多个操作单元上进行执行，例如Web服务器、FTP服务器、企业关键应用服务器和其它关键任务服务器等，从而共同完成工作任务。
```

![image-20220111162501481](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111162501481.png)

### 动静分离

```tex
将动态页面和静态页面使用不同的服务器进行解析
```

![image-20220111163111671](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111163111671.png)

## 安装

```tex
依赖素材
	pcre-8.37.tar.gz
	openssl-1.0.1t.tar.gz
	zlib-1.2.8.tar.gz
	nginx-1.12.1.tar.gz
```

```tex
yum一键下载安装
	yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
	
或者wget 联网下载资源包
	make 安装
	
或者本机下载，linux解压
tar -xvf pcre-8.37.tar.gz 
进入解压之后的目录，执行 ./configure编译
使用make && make install 编译并安装
pcre-config --version 查看版本号
```

```tex
安装nginx
tar -xvf nginx-1.12.2.tar.gz 
进入解压之后的目录，执行 ./configure编译
使用make && make install 编译并安装

在nginx目录下执行
如果服务器版本高
vim /usr/src/nginx-1.12.2/src/os/unix/ngx_user.c
注释/*cd.current_salt[0] = ~salt[0];*/

去掉-Werror 错误
vim /usr/src/nginx-1.12.2/objs/Makefile
CFLAGS =  -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g   去掉-Werror

检查是否安装好
cd /usr/local
ls 
出现nginx文件夹
在nginx中有sbin启动脚本
在sbin文件夹中  ./nginx启动

nginx.conf配置文件中配置了80端口
```

> 防火墙开放端口

```bash
#查看开放端口
firewall-cmd --list-all
#设置开放的端口号
firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-port=80/tcp --permanent
firewall-cmd --reload

ip:80访问
```

## nginx操作的常用命令

*注意：必须进入到usr/local/nginx/sbin中使用命令

```bash
#查看nginx版本号
./nginx -v
#启动nginx
./nginx
#关闭nginx
./nginx -s stop
#重新加载nginx
./nginx -s reload
```

## nginx配置文件

```bash
#位置
/usr/local/nginx/conf/nginx.conf
#组成
全局块(events之前所有)、events块、http块
#全局块
文件开始到events之前的内容，主要设置一些影响nginx服务器整体运行的配置指令，主要包括配置运行nginx服务器的用户(组)，允许生成的worker process数、进程PID存放路径，日志存放路径和类型以及配置文件的引入等
如
worker_processes 1;
这是nginx服务器并发处理服务的关键配置，worker_processes值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约
#events块
events块涉及的指令主要影响nginx服务器与用户的网络连接
如
worker_connections 1024;
支持的最大连接数
#http
nginx服务器配置中最频繁的部分，代理，缓存和日志定义等绝大多数功能和第三方模块都在这里，需要注意的是http块也可以包括http全局块和server块
#http全局块
http全局块配置的指令包括文件引入，MIME-TYPE定义，日志自定义，连接超时时间，单链接请求数上限等

```

## 配置实例

### 反向代理

#### 示例一

> 实现效果

```tex
1.打开浏览器，在浏览器地址栏中输入地址www.123.com,跳转linux系统tomcat主页面
2.准备工作
	1.linux中安装tomcat,使用默认端口号
	2.防火墙开启端口
```

![image-20220111181308489](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111181308489.png)

> 具体配置

```bash
#在windows系统中的hosts文件中配置域名,先访问hosts文件，没有才去网上找dns协议
C:\Windows\System32\drivers\etc\hosts
#在nginx进行请求转发的配置(反向代理)
server_name  192.168.229.128;
location / {
            root   html;
            proxy_pass http://127.0.0.1:8080
            index  index.html index.htm;
}
```

#### 示例二

> 实现效果

```bash
使用nginx反向代理，根据访问的路径跳转到不同端口的服务中
nginx监听端口为9001
访问http://127.0.0.1:9001/edu 直接跳转到127.0.0.1：8080
访问http://127.0.0.1:9001/vod 直接跳转到127.0.0.1：8081
```

> 准备工作

```tex
准备两个tomcat服务器
usr/src
设置不同的端口启动
分别创建edu和vod文件夹 放置两个不同的页面
```

> nginx配置反向代理

```bash
 server {
        listen       9001;
        server_name  192.168.229.128;

        location ~ /edu/ {
            proxy_pass  http://127.0.0.1:8080;
            #只会替换路径的ip地址，所以不需要带路径，也不允许带路径
        }
        location ~ /vod/ {
            proxy_pass  http://127.0.0.1:8081;
        }
    }

```

#### location属性

```bash
# =
用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功就进入处理该请求
# ~
用于uri包含正则表达式前，并且区分大小写
# ~*
用于uri包含正则表达式前，不区分大小写
# ^~
用于不含正则表达式的uri前，要求Nginx服务器找到表示uri和请求字符串匹配度最高的location后，立即使用此location处理请求，而不再使用location块中的正则uri和请求字符串做匹配

注意：如果uri包含正则表达式，则必须要有~或者~*标识
```

### 负载均衡

> 实现效果

```tex
浏览器地址栏输入地址 http://192.168.229.128/edu/a.html,负载均衡效果，平均8080和8081端口中
```

> 准备工作

```tex
两台tomcat服务器8080，8081
在两台tomcat中，创建edu文件夹，创建a.html
```

> 配置nginx负载均衡

```bash
    #负载均衡
    upstream myserver{
        server 192.168.229.128:8080;
        server 192.168.229.128:8081;
    }   

    server {
        listen       80;

        server_name  192.168.229.128;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            proxy_pass http://myserver;
            proxy_connext_timeout 10; #连接超时时间
            index  index.html index.htm;
       	}
   }
```

#### 分配策略

```bash
# 轮询 默认
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除
# weight
weight代表权重，默认为1，权重越高，被分配的客户端越多
upstream myserver{
	server ip:port weight=10;
	server ip:port weight=1;
}
# ip_hash
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session问题
upstream myserver{
	ip_hash
	server ip:port;
	server ip:port;
}
#fair (第三方)
根据服务器响应时间进行分配，哪个服务器响应时间短，哪个服务器处理请求
upstream myserver{
	server ip:port;
	server ip:port;
	fair;
}
```

### 动静分离

> 什么是动静分离

```tex
动静分离是动态请求和静态请求分离，不能理解成只是单纯的把动态页面和静态页面分离，严格意义上说应该是动态请求跟静态请求分开，可以理解成使用Nginx处理静态页面，tomcat处理动态页面。
动静分离从目前实现角度分为两种
一种是纯粹把静态文件独立成单独的域名，放在独立的服务器上，目前主流
另一种是动静态文件混合在一起，通过nginx分开

通过location指定不同后缀名实现 不同的请求转发。通过expires参数设置，可以设置浏览器缓存过期时间，减少与服务器之间的请求和流量。
具体expires定义：是给一个资源设置过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量，此种方法非常适合不经常变动的资源。(如果经常更新的文件，不建议使用Expires来缓存)，我这里设置3d,表示在这3天之内访问这个url,发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304，如果有修改，则直接从服务器重新下载，返回状态码200
```

> 准备工作

```tex
1.在linux系统中准备静态资源，用于进行访问
	/data/image    /data/www
```

> 具体配置

 server_name  192.168.229.128;

```bash
    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location /www/ {
        root   /data/;
        index  index.html index.htm;
    }

    location /image/ {
         root  /data/;
         autoindex  on;   # 不指定资源，使用/image/访问会列出目录
    }
```
![image-20220111203228951](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111203228951.png)

### 高可用的集群

![image-20220111204041452](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111204041452.png)

```tex
两台nginx服务器
需要keepalived
需要虚拟ip
```

> 准备工作

```tex
需要两台服务器
在两台服务器上安装nginx
在两台服务其中安装keepalived			yum install keepalived -y   成功后在etc里面生成目录keepalived
```

> 完成主备配置(主从)

```bash
# 找到keepalived的配置文件
/etc/keepalived/keepalived.conf
```

## nginx原理分析

![image-20220111204942926](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111204942926.png)

> master和worker

```bash
# 好处
1.可以使用nginx -s reload热部署,正在处理工作的worker不会参与重新加载，完成后再进行加载
2.每个worker是独立的进程，如果有一个worker出现问题，其他worker继续争抢，不会造成服务中断

# 设置多少个worker合适
	worker的个数和服务器的cpu相等是最合适的
	
# worker_connection 连接数
这个值是表示每个worker进程所能建立连接的最大值，所以，一个nginx能建立的最大连接数，应该是worker_connection * worker_processes
```

![image-20220111205131779](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111205131779.png)

![image-20220111210901087](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220111210901087.png)