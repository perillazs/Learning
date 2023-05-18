# Nginx 学习手册
## 一、概述
what/how/why
Nginx 是一个高性能的 HTTP 和反向代理 Web 服务器, 也是一个 IMAP/POP3/SMTP 代理服务器  

特点是占用内存少，并发能力强  

## 二、作用

### 1. 正向代理
用于客户端，转发一些无法直接请求到的 web 资源，如 VPN

### 2. 反向代理
用于服务端，将请求分发给其他服务器

### 3. 负载均衡
负载均衡有两种策略：内置策略和扩展策略  
内置策略为轮询、加权轮询、iphash  
iphash指的是将同一IP的请求转发到固定的服务器，用来保证能获取session之类的功能  

动静分离：有些请求需要后台处理，有些则是静态资源如 js/css/img 文件。根据一定的规则把两者区别开，之后根据静态资源的特点做缓存操作，提高资源响应速度  

## 三、安装

### windows 环境  
进入 `http://nginx.org/en/download.html` 官网下载页直接下载，建议下载稳定版本 Stable Version  

### Linux 环境
**yum安装**  
> yum install nginx

#### 安装 gcc 环境
> yum install gcc-c++
#### 第三方的开发包  
1. PCRE  
PCRE是一个Perl库，正则表达式库
> yum install -y pcre pcre-devel

2. zlib  
zlib 提供了多种压缩和解压缩的方式  
> yum install -y zlib zlib-devel
  
3. OpenSSL  
OpenSSL是一个强大的安全套接字层密码库，囊括主要的密码、算法常用的密钥和证书封装管理哦功能及SSL协议
> yum -y install openssl openssl-devel

#### 安装过程
1. 解压到 /usr/local 下
> tar -xvf nginx-1.14.0.tar.gz -C /usr/local

2. 在解压目录下使用 configure创建一个makeFile文件
```
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi \
--with-http_stub_status_module \
--with-http_ssl_module \
--with-file-aio \
--with-http_realip_module
```
3. 然后执行编译
> make
4. 执行 make install 进行安装
> make install

## 四、常用指令
> $ /usr/sbin/nginx           启动  
> $ /usr/sbin/nginx -s stop   停止  
> $ /usr/sbin/nginx -s quit   安全退出  
> $ /usr/sbin/nginx -t        测试配置文件是否合法  
> $ /usr/sbin/nginx -s reload 重启nginx  
> $ ps -ef | grep nginx       查看nginx进程  

## 五、配置及使用

``` config
# 全局配置
events {
  worker_connections 1024;
}

http {
  # http 配置

  upstream hello {
    # 负载均衡配置
    server 127.0.0.1:8080 weight=1;
    server 127.0.0.1:8081 weight=2;
  }

  server {
    listen        80;
    server_name   localhost;

    localtion / {
      root  html
      index index.html index.htm;
      proxy_pass http://hello
    }
  }
}
```

### 配置文件
```
main
events { ... }
http {
  server {
    location { ... }
  }
}
```
Nginx 默认配置文件主要有 main、events、http、server、location五块组成，其中 http、server、location属于嵌套关系
* main: 主要控制 Nginx 子进程所属的用户和用户组、派生子进程书、错误日志位置与级别、pid位置、子进程优先级、进程对应CPU、进程能够打开的文件描述符数目等
* events: 控制 Nginx 处理连接的方式
* http: Nginx 处理 http 请求的主要配置块
* server: Nginx 中主机配置块，可用于配置多个虚拟主机
* location: server 中对应目录级别的控制块，可配置多个

location 根据不同前缀的使用方式，大致分为普通 location 和正则 location  
多个正则 location 之间会按照先后顺序进行匹配，匹配成功后就不会继续匹配后面定义的 location  
多个普通 location 之间遵循“最大匹配原则”，匹配度最高的location将会被执行，同时也可以利用 =精准匹配和 ^~非正则匹配可以在正则匹配之前优先匹配。  
location 匹配优先级，只要优先的 location 匹配成功就不会执行其他的 location： 
  * =匹配 
  * ^~匹配
  * 正则匹配
  * 普通的最大前缀匹配

### 反向代理
在 Nginx 服务器中，反向代理配置非常简单，只要指令就是 `proxy_pass` 用于设置后端服务器的地址
```
server {
  listen 80;
  server_name localhost;
  location / {
    proxy_pass http://localhost:9000
  }
}
```
如上配置所有 http://localhost:80 的请求都会被转发到 http://localhost:9000

### 负载均衡
负载均衡就是将负载分摊到多个操作单元上执行，从而提高服务的可用性和响应速度
Nginx 默认支持轮询、权重、IP Hash 等多种负载均衡策略

* 轮询：默认设置方式，每个请求按时间顺序逐一分配到不同的后端服务器进行处理，可自动剔除宕机服务器
```
server {
  listen 80;
  server_name localhost;
  location / {
    proxy_pass http://web_server
  }
}
upstream web_server {
  server 192.168.78.128
  server 192.168.78.129
}
```
* 权重方式：利用 weight指定轮询的权重比率，用于后端服务器性能不均的情况
* ip_hash 方式：每个请求按照 IP 的 hash 结果分配，让每个方可每次请求都访问同一个后端服务器，可解决 session 共享的问题
* 第三方模块： fair 或 url_hash 等策略

### location的使用
#### 语法规则
|符号|含义|
|:-:|:-|
|=| =开头表示精准匹配|
|^~| ^~开头表示url以某个常规字符串开头，可理解为匹配url路径(禁止正则匹配)|
|~| ~开头表示区分大小写的正则匹配|
|*~| *~开头表示不区分大小写的正则匹配|
|!和!* | !和!*分别为区分大小写不匹配及不区分大小写不匹配的正则|
|/| 通用匹配，任何请求都会匹配到|
