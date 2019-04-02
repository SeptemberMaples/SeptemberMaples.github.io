---
title: nginx入门及实践（二）
date: 2019-04-02 11:15:45
categories: 
- 前端
tags: 
- nginx
- 笔记
---
> 本文继续学习nginx常用的模块

## Rewrite模块
---

Rewrite模块允许正则替换URI，返回页面重定向，和按条件选择配置。

<!-- more -->

### 处理顺序
表面看rewrite和location功能有点像，都能实现跳转，主要区别在于rewrite是在同一域名内更改获取资源的路径，而location是对一类路径做控制访问或反向代理，可以proxy_pass到其他机器。很多情况下rewrite也会写在location里，它们的执行顺序是：
- 处理在`server`级别中定义的`rewrite`指令
- 为请求执行`location`
- 处理在选中的location中定义的`rewrite`指令。如果其中某步URI被重写，则重新循环执行1-3，直到找到真实存在的文件；循环超过10次，则返回500 Internal Server Error错误

### break 指令 
停止处理当前这一轮的Rewrite指令集
```js
syntax:  break;
default: -
context: server, location, if
```
### return 指令 
停止处理并返回指定code给客户端
```js
syntax: return code [text];
        return code URL;
        return URL;
default: -
context: server, location, if
```
### set 指令 
为指定变量variable设置变量值value
```js
syntax: set $variable value;
default: -
context: server, location, if
```
### if 指令
条件判断语句，跟通用编程语言一致，为true时被执行
``` js
syntax: if(condition) { ... }
default: -
context: server, location
```
条件可以是下列任意一种：
- 当表达式只是一个变量时，如果值为空或任何以0开头的字符串都会当做false
- 直接比较变量和内容时，使用 `=` 或 `!=`
- `~` 正则表达式匹配，`~*` 不区分大小写的匹配，`!~` 区分大小写的不匹配
- `-f` 和 `!-f`用来判断是否存在文件
- `d` 和 `!-d` 用来判断是否存在目录
- `e` 和 `!-e` 用来判断是否存在文件或目录
- `x` 和 `!-x` 用来判断文件是否可执行

比如：
```js
if ($http_cookie ~* "id=([^;]+)(?:;|$)") {
    set $id $1;
 } //如果cookie匹配正则，设置变量$id等于正则引用部分
```

以下是用于if指令的**全局变量**
- `$args` ： #这个变量等于请求行中的参数，同`$query_string`
- `$content_length` ： 请求头中的Content-length字段。
- `$content_type` ： 请求头中的Content-Type字段。
- `$document_root` ： 当前请求在root指令中指定的值。
- `$host` ： 请求主机头字段，否则为服务器名称。
- `$http_user_agent` ： 客户端agent信息
- `$http_cookie` ： 客户端cookie信息
- `$limit_rate` ： 这个变量可以限制连接速率。
- `$request_method` ： 客户端请求的动作，通常为GET或POST。
- `$remote_addr` ： 客户端的IP地址。
- `$remote_port` ： 客户端的端口。
- `$remote_user` ： 已经经过Auth Basic Module验证的用户名。
- `$request_filename` ： 当前请求的文件路径，由root或alias指令与URI请求生成。
- `$scheme` ： HTTP方法（如http，https）。
- `$server_protocol` ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
- `$server_addr` ： 服务器地址，在完成一次系统调用后可以确定这个值。
- `$server_name` ： 服务器名称。
- `$server_port` ： 请求到达服务器的端口号。
- `$request_uri` ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
- `$uri` ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
- `$document_uri` ： 与$uri相同。
  
比如：`http://localhost:88/test1/test2/test.php`
$host：localhost
$server_port：88
$request_uri：http://localhost:88/test1/test2/test.php
$document_uri：/test1/test2/test.php
$document_root：/var/www/html
$request_filename：/var/www/html/test1/test2/test.php

### rewrite 指令
rewrite功能就是，使用nginx提供的全局变量或自己设置的变量，结合正则表达式和标志位实现url重写以及重定向。rewrite只能放在server{},location{},if{}中，并且只能对域名后边的除去传递的参数外的字符串起作用，例如 `http://a.com/a/we/index.php?id=1&u=str` 只对/a/we/index.php重写。
``` js
syntax: rewrite regex replacement [flag];
        关键字    正则    替代内容    flag标记
default: -
context: server, location, if
```
`flag`参数：
- `last` : 停止执行当前的Rewrite指令集，然后查找匹配改变后URI的新location
- `break` : 停止执行当前这一轮的Rewrite指令集
- `redirect` : 返回302临时重定向，地址栏会显示跳转后的地址
- `permanent` : 返回301永久重定向，地址栏会显示跳转后的地址

比如：
```js
server {
  listen 80;
  rewrite ^/info/(.*)$ /user_info?username=$1;
  location /user_info {
    echo "$uri";
    echo "$args";
  }
}
```
```
~ curl http://localhost:80/info/haha
/user_info
username=haha
```

Rewrite模块综合例子：
``` js
if ($http_user_agent ~ MSIE) {
    rewrite ^(.*)$ /msie/$1 break;
} //如果UA包含"MSIE"，rewrite请求到/msid/目录下

if ($http_cookie ~* "id=([^;]+)(?:;|$)") {
    set $id $1;
 } //如果cookie匹配正则，设置变量$id等于正则引用部分

if ($request_method = POST) {
    return 405;
} //如果提交方法为POST，则返回状态405（Method not allowed）。return不能返回301,302

if ($slow) {
    limit_rate 10k;
} //限速，$slow可以通过 set 指令设置

if (!-f $request_filename){
    break;
    proxy_pass  http://127.0.0.1; 
} //如果请求的文件名不存在，则反向代理到localhost 。这里的break也是停止rewrite检查

if ($args ~ post=140){
    rewrite ^ http://example.com/ permanent;
} //如果query string中包含"post=140"，永久重定向到example.com

location ~* \.(gif|jpg|png|swf|flv)$ {
    valid_referers none blocked www.a.com www.b.com;
    if ($invalid_referer) {
        return 404;
    } //防盗链
}
```
---
## Upstream模块
---

该模块为后端服务器提供简单的负载均衡

### upstream 指令
定义一组服务器。这些服务器可以监听不同的端口。
```js
syntax: upstream name { ... }
default: -
context: http
```
比如：
```js
upstream backend {
  server xxx.a.com weight = 5;
  server 127.0.0.1:8001 max_fails=3 fail_timeout=30s;
  server 127.0.0.1:8002;
}
```
默认情况下，nginx按加权轮转的方式将请求分别发到各服务器。在上面的例子中，每7个请求会通过以下方式分发：
- 5个请求分到 xxx.a.com
- 1个请求分到第二个服务器
- 1个请求分到第三个服务器

与服务器通信 的时候，如果出现错误，请求会被传给下一个服务器，直到所有可用的服务器都被尝试过。如果所有的服务器都返回失败，客户端将会得到最后通信的那个服务器的（失败）响应结果。

### server 指令
定义服务器的地址和其它参数。用在 `upstream` 中。
```js
syntax: server address [params]
default: -
context: upstream
```
可选参数：
- weight=number —— 设定服务器的权重，默认是1
- max_fails=number —— 设定nginx与服务器通信尝试失败的次数
- fail_timeout=time —— 设定统计失败尝试次数的时间段
- backup —— 标记为备用服务器。当主服务器不可用时，请求会传给这些服务器
- down —— 标记服务器永久不可用，可以跟`ip_hash`指令一起使用


### ip_hash 指令
指定服务器组的负载均衡方法，请求基于客户端的IP地址在服务器间进行分发
```js
syntax: ip_hash;
default: -
context: upstream
```

### least_conn 指令
指定服务器组的负载均衡方法，根据权重值，将请求发到活跃连接数最少的那台服务器
```js
syntax: least_conn;
default: -
context: upstream
```

---
Proxy模块
---
Proxy模块：允许传送请求到其它服务器，即 **反向代理**

### proxy_pass 指令
设置后端服务器的协议和地址，也可设置本地location。
```js
syntax: proxy_pass URL;
default: -
context: location, if in location, limit_except
```
比如：
```js
server {
  listen 80;
  location /frontend/ {
    proxy_pass http://www.a.com:8001/backend/;
  }
}
```
如上例子，请求 `http://www.a.com/frontend/`时，实际请求被代理到 `http://www.a.com:8001/backend/`

### proxy_set_header 指令
允许重新定义或者添加发往后端服务器的请求头。
```js
syntax: proxy_set_header field value;
default: proxy_set_header Host $proxy_host;
         proxy_set_header Connection close; 
context: http, server, location
```

### proxy_http_version 指令
设置代理使用的HTTP协议版本。默认使用的版本是1.0，而1.1版本则推荐在使用keepalive连接是一起使用。
```js
syntax: proxy_http_version 1.0 | 1.1;
default: proxy_http_version 1.0; 
context: http, server, location
```

---
## Gzip模块
---
对输出的数据流进行实时压缩。

### gzip
开启或关闭gzip模块
```js
syntax: gzip on | off;
default: gzip off; 
context: http, server, location, if in location
```
### gzip_comp_level
gzip压缩比，1压缩比最小处理速度最快，9压缩比最大但处理速度最慢。
```js
syntax: gzip_comp_level level;
default: gzip_comp_level 1; 
context: http, server, location
```
### gzip_min_length
设置允许压缩的页面最小字节数，页面字节数从header头中的Content-length中获得。
```js
syntax: gzip_min_length length;
default: gzip_min_length 20; 
context: http, server, location
```
### gzip_types
匹配MIME类型进行压缩。
```js
syntax: gzip_tpes mime-type ...;
default: gzip_tpes text/html; 
context: http, server, location
```

示例：
```js
server {
  listen 80;
  location /gzip {
    gzip            on;
    gzip_min_length 10;
    gzip_types      text/plain application/xml;
  }
}
```
