---
title: nginx入门及实践（一）
date: 2019-03-23 20:02:24
categories: 
- 前端
tags: 
- Ngix
- 笔记
---

> 一直以来关于nginx的使用和了解，也只限于在开发中启动server服务或反向代理转发请求api。通常是查阅文档简单配置。本文是自己学习nginx的笔记和理解，也便于查阅及配置。

## 什么是nginx

nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好。

<!-- more -->

Apache与nginx对比
![Apache与nginx对比](/images/2019-03-23/why-nginx.png "为什么是nginx")


## nginx 入门

### 安装

  安装包 VS 编译
  nginx可以使用各平台的默认包和使用源码编译安装，更推荐使用源码编译的方式，原因在于：
  - 按需定制、优化编译参数，提高性能
  - 管理方便，执行文件、配置文件、库文件都可以安装到指定目录
  - 批量部署，配置好后可打包快速分发到其他机器

nginx建议安装在 `/usr/local/`、`/etc/`或者`/usr/local/etc/`文件夹中。

源码下载到本地后，解压进入源码目录
```
  tar -zxvf nginx-1.15.9.tar.gz
  cd nginx-1.15.9
```
然后使用`configure`命令并设置相应参数安装所需服务模块，执行结束时会生成`Makefile`文件。
configure命令参数示例：（为展示需要写在多行，执行时内容需要在同一行）
```
./configure
    --sbin-path=/usr/local/etc/nginx
    --conf-path=/usr/local/etc/nginx/nginx.conf
    --pid-path=/usr/local/etc/nginx/nginx.pid
    --with-http_ssl_module
    --with-pcre=../pcre-8.42
    --with-zlib=../zlib-1.2.11
```
> `--sbin-path`指定nginx安装目录。`--conf-path`指定conf文件目录。`with-*`需要安装的服务模块，`without-*`去掉不需要的模块。
再运行：
```
make
make install
```
![nginx源码编译](/images/2019-03-23/nginx-1.png "nginx源码编译")

**详情见-->[官网文档](http://nginx.org/en/docs/configure.html)**

### 常用命令

启动：
``` bash
 nginx
```
查看80端口*lsof -i:80*

启动后，可以使用以下命令控制
```
nginx -s 
```
`-s`意思是向主进程发送信号，可为以下：
- `stop`——快速关闭
- `quit`——优雅关闭
- `reload`——重新加载配置文件
- `reopen`——重新打开日志文件

### 配置文件
nginx及其模块的工作方式由配置文件确定，默认情况下，配置文件名为**`nginx.conf`**

常规站点配置示例：
![配置示例](/images/2019-03-23/nginx-2.png "配置示例")

默认`nginx.conf`配置内容：
```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
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

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
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

#### 注释
```
# 以#开头的行为注释
```
#### 简单指令
由空白分隔的指令名、参数以及结尾的`;`组成，如：
```
worker_processes  1;
```
#### 指令块
以`{}`组成的区块，包含简单指令，支持嵌套(main,event,http,server,location):
```
http {
  server {

  }
}
```

#### 区块结构
- http —— 为HTTP服务器提供配置上下文
- server —— 表示开始设置虚拟的配置
- location —— 为某个请求URI（路径）建立配置

##### server区块常用指令
- listen : 设置web服务监听的IP地址/端口号，默认 `80`
  ```
  listen 8000;
  listen localhost; 
  listen localhost:8080;    
  listen 127.0.0.1;
  ```
- server_name: 设置虚拟主机的名称
  ```
  server_name localhost;
  server_name example.com www.example.com;
  server_name *.example.com;
  ```
  ###### 匹配规则
  nginx检查请求的Host头来决定处理此请求的虚拟主机，如果Host头没有匹配任何一个虚拟主机，或者没有包含Host头，则将请求分发到此端口的默认虚拟主机。如果没有显示使用`default_server`选项，则第一个列出的虚拟主机被认为是虚拟主机。
  ###### 优先级
  1. 确切的名字；
  2. 最长的以星号起始的通配符名字，如"*.expample.com";
  3. 最长的以星号结束的通配符名字，如"mail.*";
  4. 第一个匹配的正则表达式名字（按在配置文件中出现的顺序）
  ![Demo](/images/2019-03-23/nginx-3.png "Demo")

- location: 为某个请求URI建立配置(路由) 修饰符： `=`、`~`、`~*`、`^~`
  - `=`修饰符： URI的定位必须与指定的模式精确匹配（不能使用正则表达式） 
    ```
    server {
      listen 80;
      location = /foo {
        # echo "$uri";
        {
          root   html;
          index  index.html index.htm;
        }
      }
    }
  ```
    - `http://localhost:80/foo` 可用（严格匹配）
    - `http://localhost:80/Foo` 取决于操作系统是否区分大小写
    - `http://localhost:80/foo?x=1&y=2` 可用（忽略参数）
    - `http://localhost:80/foo/` 不可用（结尾有斜杠）
    - `http://localhost:80/foobar` 不可用（指定模式后有额外字符）
  
  - 无修饰符：URI的必须心指定模式开始（不能使用正则表达式）
    ```
    server {
      listen 80;
      location /foo {
        echo "$uri";
      }
    }
  ```
    - `http://localhost:80/foo` 可用（严格匹配）
    - `http://localhost:80/Foo` 取决于操作系统是否区分大小写
    - `http://localhost:80/foo?x=1&y=2` 可用（忽略参数）
    - `http://localhost:80/foo/` 可用
    - `http://localhost:80/foobar` 可用

  - `~` 修饰符： 客户端请求的URI与指定的*正则表达式*匹配，**必须区分大小写**
    ```
    server {
      listen 80;
      location ~ ^/foo$ {
        echo "$uri";
      }
    }
    ```
    - `http://localhost:80/foo` 可用（严格匹配）
    - `http://localhost:80/Foo` 不可用（区分大小写）
    - `http://localhost:80/foo?x=1&y=2` 可用（忽略参数）
    - `http://localhost:80/foo/` 不可用（结尾有斜杠）
    - `http://localhost:80/foobar` 不可用

  - `~*` 修饰符：客户端请求的URI与指定的*正则表达式*匹配，**不区分大小写**
    ```
    server {
      listen 80;
      location ~* ^/foo$ {
        echo "$uri";
      }
    }
    ```
    - `http://localhost:80/foo` 可用（严格匹配）
    - `http://localhost:80/Foo` 可用（不区分大小写）
    - `http://localhost:80/foo?x=1&y=2` 可用（忽略参数）
    - `http://localhost:80/foo/` 不可用（结尾有斜杠）
    - `http://localhost:80/foobar` 不可用

- `^~` 修饰符：如果最大前缀匹配的路径以`^~`开始，那么nginx不再检查正则表达式；

- `@` 修饰符：定义命名`location`区段，这些区段客户端不能访问，只由内部产生的请求访问。

  ###### 优先级
  1. 带有`=`修饰符的`location`区段
  2. 无修饰符的`location`区段（满足精确匹配）
  3. 带有`^~`修饰符的`location`区段
  4. 带有`~`或`~*`修饰符的`location`区段
  5. 无修饰符的`location`区段（满足以指定模式开始的匹配）

  ![Demo](/images/2019-03-23/nginx-4.png "Demo")
  ![Demo](/images/2019-03-23/nginx-5.png "Demo")
  ![Demo](/images/2019-03-23/nginx-6.png "Demo")
  ![Demo](/images/2019-03-23/nginx-7.png "Demo")


---

本文主要介绍了nginx安装及其基本配置。接下来会写几个常用模块及注意事项，以及实践案例。

未完，待续~

[##### Rewrite模块]:TODO
<!-- ##### Upstream模块  
##### Proxy模块 ： 反向代理、跨域
- proxy_pass
- proxy_set
- proxy_set_header
##### RealIP模块
##### GZip模块
- gzip_types
- gzip_http_version

## 实践 -->


