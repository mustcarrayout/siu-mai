
# nginx
## 什么是nginx？
1. 事件驱动架构（master-worker）；
2. 主要支持http，另外支持邮件服务器(SMTP,IMAP和POP3)的反向代理服务器；
3. 人气高，口碑好的，代理服务器；
### 什么是代理服务器？
* 正向代理
* 反向代理
### 为什么是nginx？
* 高并发（默认1000）
* 低消耗（10000个非活跃连接，nginx中仅消耗2.5M）
* 热部署（0停机部署，提供7*24h全天候服务）
* 高可用（一个主进程、多个worker进程）
* 高扩展（C语言扩展模块，与LUA脚本扩展模块） OpenResty
* 文档成熟度（曝光率、社区活跃度）

## 如何使用nginx？
### 安装
1. 源码编译install
2. 下载安装包install
```shell
yum research nginx
yum install -y nginx
# 查看nginx是否安装成功，显示列表则表示安装成功，默认的安装路径是/usr/sbin/nginx /etc/nginx /usr/share/nginx
rpm -qa | grep nginx
# 自定义软件源目录，在其目录下添加描述信息 /etc/yum.repos.d/
```
```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
```

### 运行nginx
```shell
# 如何配置开机启动
systemctl start nginx
systemctl status nginx
# 关闭nginx
systemctl stop nginx
# 强杀进程终止nginx
nginx -s stop
# 正常关闭nginx
nginx -s quit
# 启动nginx
nginx
# 查看版本
nginx -v
# 查看端口是否被占用
lsof -i:80
# 启动nginx并且加载指定的配置文件（路径是绝对路径）
nginx -s -c /etc/nginx/conf.d/demo.conf
# 测试配置文件语法是否正确
nginx -t -c /etc/nginx/conf.d/demo.conf
# 卸载nginx，不能直接使用yum remove nginx，因为这个会把nginx依赖的包也删除，而这些依赖可以是别的软件的依赖
rpm -e --nodeps nginx
```

### 配置nginx
配置文件/etc/nginx/nginx.conf
三大块
1. 全局配置
2. http配置
3. server配置
```nginx
# 全局配置
user username;
worker_processes 4;
pid /run/nginx.pid;


events{
    worker_connections 768;
}

# http 配置（http协议配置的相关操作）
http {
    sendfile on;
    tcp_nopush on;
	tcp_nodelay on;
    keepalive_timeout 65;

    include /etc/nginx/mime.types;
	default_type application/octet-stream;
    # 日志配置
    access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;
    # 压缩配置
    gzip on;
	gzip_disable "msie6";

    # 加载其他配置
    include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;

    # server
    server {
        listen 80;
        
        root /usr/share/html;
        index index index.html;

        location / {
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   Host      $http_host;
            proxy_pass         http://0.0.0.0:8000;
        }
    }

    server {
        listen 443;
        server_name _;
        ssl on;
        ssl_certificate       certificate-bundle.crt;
        ssl_certificate_key   private-key.pem;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

        location / {
            proxy_pass http://127.0.0.1:8080;

        }
    }
}
# mail协议
#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
#
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}

```

proxy_pass中有/的问题=> “/”这个表示结束符
```nginx
# request http://192.168.10.1/proxy/test.html
location /proxy/ {
    proxy_pass http://127.0.0.1:81/; #==> http://127.0.0.1:81/test.html
}
location /proxy/ {
    proxy_pass http://127.0.0.1:81; #==> http://127.0.0.1:81/proxy/test.html
}
location /proxy/ {
    proxy_pass http://127.0.0.1:81/abc/; #==> http://127.0.0.1:81/abc/test.html
}
location /proxy/ {
    proxy_pass http://127.0.0.1:81/abc; #==> http://127.0.0.1:81/abctest.html
}
```
## 零拷贝原理（依赖操作系统）
一个存储区到另一个存储区，没有CPU的参与.
1. 没有CPU运算
2. 通常用于网络传输，以减少CPU和内存的消耗
3. 减少内核空间和用户空间拷贝过程，减少用户上下文和CPU内核上下文的切换

零拷贝指的是CPU没有参与拷贝。

![传统操作](https://img-blog.csdnimg.cn/20190808103727858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aHNvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)
![MMAP：Memory Mapping](https://img-blog.csdnimg.cn/20190808103738760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aHNvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)
![SENDFILE](https://img-blog.csdnimg.cn/20190808103747474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aHNvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)
![DMA 辅助的 SENDFILE](https://img-blog.csdnimg.cn/20190808103756934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aHNvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)

```c
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <poll.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int main()
{
	int p[2];
	int fd, down_fd, up_fd, ret;
	struct pollfd set[2];
	struct sockaddr_in addr, server;
	struct in_addr in;
	
	fd = socket(AF_INET, SOCK_STREAM, 0);
	
	addr.sin_family = AF_INET;
	addr.sin_port = htons(80);
	addr.sin_addr.s_addr = inet_addr("192.168.106.177");
	
	bind(fd,(struct sockaddr *)(&addr), sizeof(struct sockaddr));
	listen(fd, 10);
	
	socklen_t len = sizeof(addr);
	down_fd = accept(fd, (struct sockaddr*)(&addr), &len);
	
	inet_aton("192.168.106.176", &in);
	server.sin_family = AF_INET;
	server.sin_addr = in;
	server.sin_port = htons(8888);
	up_fd = socket(AF_INET, SOCK_STREAM, 0);
	
	connect(up_fd, (struct sockaddr*)&server, sizeof(server));
	
	set[0].fd = down_fd;
	set[0].events = POLLOUT | POLLIN;
	set[1].fd = up_fd;
	set[1].events = POLLOUT | POLLIN;
	
	pipe(p);
	
	while (1) {
		if ((ret = poll(set, 2, -1)) < 0) {
			return -1;
		}
		if(set[0].revents & POLLIN) {
			splice(set[0].fd, NULL, p[1], NULL, 65535, SPLICE_F_MORE);
			splice(p[0], NULL, set[1].fd, NULL, 65535, SPLICE_F_MORE);
		}
	}
}
```

## Zero-Copy的三种实现方式
* 减少甚至*避免用户空间和内核空间之间的数据拷贝(减少CPU的参与)*：在一些场景下，用户进程在数据传输过程中并不需要对数据进行访问和处理，那么数据在Linux的Page Cache和用户进程的缓冲区之间的传输就完全可以避免，让数据拷贝完全在内核里进行，甚至可以通过更巧妙的方式避免在内核里的数据拷贝。这一类实现一般是通过增加新的系统调用来完成的，比如 Linux 中的mmap()，sendfile()以及splice()等。
* 绕过内核的直接I/O：允许在*用户态进程*绕过内核直接和*硬件进行数据传输(直接加硬件跳过CPU参与，事后告诉CPU)*，内核在传输过程中只负责一些管理和辅助的工作。这种方式其实和第一种有点类似，也是试图避免用户空间和内核空间之间的数据传输，只是第一种方式是把数据传输过程放在内核态完成，而这种方式则是直接绕过内核和硬件通信，效果类似但原理完全不同。=>(**目前只适用于诸如 MPI 高性能通信、丛集计算系统中的远程共享内存等有限的场景。(不合适)**)
* 内核缓冲区和用户缓冲区之间的传输优化*\(性能优化\)*：这种方式侧重于在用户进程的缓冲区和操作系统的页缓存之间的CPU拷贝的优化。这种方法延续了以往那种传统的通信方式，但更灵活。

![三种方式](https://pic4.zhimg.com/80/v2-84f2ddaac27ddd998d59e0cc5fcf7613_720w.jpg)

1. mmp(相当于赋值)
![mmp方式](https://pic2.zhimg.com/80/v2-c8107b01ab7354c17f31ba1ac19c66f9_720w.jpg)
mmap() 之后的拷贝次数是2次DMA拷贝，1次CPU拷贝，加起来一共3次拷贝操作，比传统的 I/O 方式节省了一次CPU拷贝以及一半的内存，不过因为mmap() 也是一个系统调用，因此用户态和内核态的切换还是4次。
2. sendfile(相比较上面方案1，2是push，1是pull的方式)
![](https://pic1.zhimg.com/80/v2-b946f48c5d325f985c9cd99fa214f5ac_720w.jpg)
基于sendfile()， 整个数据传输过程中共发生2次DMA拷贝和1次 CPU 拷贝，这个和 mmap() + write() 相同，但是因为sendfile()只是一次系统调用，因此比前者少了一次用户态和内核态的上下文切换开销。
3. splice()=sendfile()+DMA(需要新的硬件支持)
![实现CPU的0参与](https://pic1.zhimg.com/80/v2-fd3470464e0a6f76e9cc0f8e1d992e90_720w.jpg)
![splice流程示意图](https://pic2.zhimg.com/80/v2-977e08cec51566d9141ca3736d1b542d_720w.jpg)

## 多路复用性
1. epoll
![epoll](https://img-blog.csdnimg.cn/20190628174625978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMwMzIwOTc=,size_16,color_FFFFFF,t_70)


## 模块应用

### 限速(自带的简便功能)
```nginx

limit_conn_zone $server_name zone=servers:10m;

server {
    location /download/ {
        limit_conn servers 1000;
        limit_rate_after 500k;
        limit_rate 50k;
    }
}
# 通过 limit_conn 限制了并发连接数，于是也就限制了总带宽。可惜这个解决方式并不完美，大家可以设想如下的例子：1000 个用户能够同时以 50k 的速度下载。那么在总带宽不变的情况下，2000 个用户是否能够同时以 25k 的速度下载？
```

### 限流(控制请求量平稳)
```nginx
#以用户二进制IP地址，定义三个漏桶，滴落速率1-3req/sec，桶空间1m，1M能保持大约16000个(IP)状态
limit_req_zone $binary_remote_addr zone=qps1:1m  rate=1r/s;
limit_req_zone $binary_remote_addr zone=qps2:1m  rate=2r/s;
limit_req_zone $binary_remote_addr zone=qps3:1m  rate=3r/s;
 
server {
 
#速率qps=1，峰值burst=5，延迟请求
#严格按照漏桶速率qps=1处理每秒请求
#在峰值burst=5以内的并发请求，会被挂起，延迟处理
#超出请求数限制则直接返回503
#客户端只要控制并发在峰值[burst]内，就不会触发limit_req_error_log
# 例1：发起一个并发请求=6，拒绝1个，处理1个，进入延迟队列4个：
#time  request  refuse  sucess  delay
#00:01    6    1    1      4
#00:02    0    0    1      3
#00:03    0    0    1      2
#00:04    0    0    1      1
#00:05    0    0    1      0
location /delay {
  limit_req  zone=qps1 burst=5;
}
 
#速率qps=1，峰值burst=5，不延迟请求
#加了nodelay之后，漏桶控制一段时长内的平均qps = 漏桶速率，允许瞬时的峰值qps > 漏桶qps
#所以峰值时的最高qps=(brust+qps-1)=5
#请求不会被delay，要么处理，要么直接返回503
#客户端需要控制qps每秒请求数，才不会触发limit_req_error_log
# 例2：每隔5秒发起一次达到峰值的并发请求，由于时间段内平均qps=1 所以仍然符合漏桶速率：
#time  request   refuse  sucess
#00:01     5     0     5
#00:05     5     0     5
#00:10     5     0     5
# 例3：连续每秒发起并发请求=5，由于时间段内平均qps>>1，超出的请求被拒绝：
#time  request   refuse   sucess
#00:01     5     0      5
#00:02     5     4      1
#00:03     5     4      1
 
location /nodelay {
  limit_req  zone=qps1 burst=5 nodelay;
}
 

```


# 参考资料
* [请看官网文档入门级](http://nginx.org/en/docs/beginners_guide.html)
* [请看官网文档详细](http://nginx.org/en/docs/)
* [nginx的原理](https://blog.csdn.net/ifeves/article/details/102607011?utm_medium=distribute.pc_relevant.none-task-blog-title-2&spm=1001.2101.3001.4242)
* [Zero-Copy历史脉络](https://zhuanlan.zhihu.com/p/296207162)
* [nginx限速模块](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html)
* [漏桶原理（leaky bucket）](https://en.wikipedia.org/wiki/Leaky_bucket)
* [令牌桶算法（Token bucket）](https://en.wikipedia.org/wiki/Token_bucket)
* [leaky_bucket解析](https://www.cnblogs.com/CarpenterLee/p/8084533.html)