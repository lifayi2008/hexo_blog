---
title: Nginx管理员指南-译
date: 2016-08-22 15:21:06
tags: [Nginx, Web]
categories: 基础服务
---
#### Nginx进程及进程管理

##### Master和worker进程

正常启动的nginx有一个master进程和多个worker进程。如果启动caching，则也会启动一个cache leader进程和一个cache manager进程
<!-- more -->
Master进程主要负责读入并应用配置文件，同时也管理worker进程

Worker进程真正的来处理请求。Nginx使用一个不依赖于操作系统的机制高效的将请求分发给worker进程。Worker进程的数量可以在配置文件中设置为一个固定的值或由nginx根据当前系统可用核心数来决定

##### Nginx控制

可以给运行着的nginx进程发送不同的信号来，执行不同的操作，形式为：
```bash
nginx -s signal
```

signal可以是下面的值：
*   quit – Shut down gracefully
*   reload – Reload the configuration file
*   reopen – Reopen log files
*   stop – Shut down immediately (fast shutdown)

也可以使用`kill`直接给主进程的ID发送信号（可发送的信号可以参考nginx.org），主进程id保存在`nginx.pid`文件中，这个文件一般在`/usr/local/nginx/logs/`或者`/var/run/`目录中

#### Nginx作为一个web服务器

Web服务器中可以配置多个virtual server，每个虚拟主机可以定义多个不同的配置实例---location，每一个location都有很多选项来控制映射到本location的一系列URI应该如何处理。每一个location都可以将请求转发或者返回一个文件（页面），而且还可以更改请求的URI，然后这些请求被重定向到另外一些location或者virtual server。另外可以返回特定的错误代码，你也可以设置这些错误代码应该返回的页面

##### 设置虚拟主机

可以在nginx配置文件中配置一个或者多个virutal server：
```bash
http {
    server {
        # Server configuration
    }
}
```
`server`配置块中通常包含一个listen配置项表示这个virtual server监听的地址和端口（也可以是一个unix domain socket的地址），ipv4地址和ipv6地址都是可以的，ipv6地址需要放置在一个方括号中
```bash
server {
    listen 127.0.0.1:8080;
    # The rest of server configuration
}
```

如果没有冒号和地址，则表示监听在所有地址；如果没有冒号和端口，则表示监听在80端口；如果没有`listen`，则在特权用户下是80端口，非特权用户下是8000端口

如果根据地址和端口匹配到的`server`有多个，则nginx会根据请求头中的`Host`信息，来测试匹配的`server`。`server_name`配置项可以是一个完整的主机名，通配符或者是正则表达式，如果是正则表达式则需要以`~`开始
```bash
server {
    listen      80;
    server_name example.org www.example.org;
    ...
}
```

如果有多个`server_name`被匹配到，则nginx按照下面的顺序来决定哪一个`server`会处理这个请求：
1.  Exact name
2.  Longest wildcard starting with an asterisk, such as *.example.org
3.  Longest wildcard ending with an asterisk, such as mail.*
4.  First matching regular expression (in order of appearance in the configuration file)

如果主机名没有被任何`server_name`匹配到，则nginx会使用第一个server来处理这个请求，除非你显式的使用`default_server`配置项来指定一个默认的主机
```bash
server {
    listen      80 default_server;
    ...
}
```

##### 配置Location

一个virtual server里可以有多个`location`，nginx会根据`location`配置项的值来决定哪一个`location`块中的配置项会应用到这个请求的处理中。每一个`location`中还可以嵌套另外一个或者多个`location`来分别处理这些请求

`location`配置项的值可以有两种形式：表示URI前缀路径名的字符串，或者正则表达式

下面的例子中的配置项会匹配`/some/path` `/some/path/document.html`但是不会匹配`/my/some/path`，因为配置项的值会从URI的开始来进行匹配
```bash
location /some/path/ {
    ...
}
```

正则表达式使用`~`来表示大小写敏感的匹配，`*~`来表示大小写不敏感的匹配，下面的例子中会匹配任何以`.html`结尾的请求
```bash
location ~ \.html? {
    ...
}
```

对于每一个`location`配置项，nginx会首先进行URI前缀路径匹配，如果没有找到匹配项才会进行正则表达式匹配。准确的逻辑规则如下：
1.  Test the URI against all prefix strings
2.  The = (equals sign) modifier defines an exact match of the URI and a prefix string. If the exact match is found, the search stops
3.  If the ^~ (caret-tilde) modifier prepends the longest matching prefix string, the regular expressions are not checked.
4.  Store the longest matching prefix string.
5.  Test the URI against regular expressions.
6.  Break on the first matching regular expression and use the corresponding location.
7.  If no regular expression matches, use the location corresponding to the stored prefix string

`=`的一个典型应用是对`/`的请求，下面的配置放在`server`中的第一个会在匹配到`/`后立即停止检查，这样可以最快的处理
```java
location = / {
    ...
}
```
`location`配置项的内容决定了如何处理这个请求，直接返回一个页面或者将请求转发到其他的服务器都是可以的。下面的例子中，URI以`/images/`开始的请求会直接从`/data`目录中获取，其他的请求会转发给另外一个URL来处理
```bash
server {
    location /images/ {
        root /data;
    }

    location / {
        proxy_pass http://www.example.com;
    }
}
```

`root`配置项告诉nginx获取静态文件的根目录，nginx返回`根目录+URI` 对应的资源，如果URI是`/images/test.png`，nginx会返回`/data/images/test.png`
`porxy_pass`配置项告诉nginx将请求转发给另外一个URL来处理，将这个URL指定的主机的返回结果返回给客户端

##### 使用变量

可以在nginx的配置文件中使用变量，变量的值到运行时才会进行计算，变量以`$`开始。有些变量值的定义依赖于客户端请求的信息；也有一些预定义的变量可以参考nginx.org文档中的描述；当然你也可以使用`set` `map` `geo` 配置项自定义变量。大部分的变量都在运行时才知道实际的值，包含的信息会依赖于实际请求信息。比如`$remote_addr`表示客户端的IP地址，而`$uri`则表示请求中的URI的值

##### 返回指定的状态码

有些情况下，客户端请求的网页不存在或者已经被暂时或者永久的移动到另外一个位置。这样的话就需要直接返回给客户端一个状态码来通知客户端。可以在配置文件中使用 `return`配置项来实现：
```bash
location /wrong/url {
    return 404;
}
```

配置项的第一个参数是返回的状态码，可选的第一个参数可以指定错误的页面或者应该重定向的页面：
```bash
location /permanently/moved/url {
    return 301 http://www.example.com/moved/here;
}
```

>`return`配置项可以在`server`配置块或者`location`配置块中使用

##### 重写请求中的URI

一个请求中的URI可以在请求处理过程中使用`rewrite`配置项多次进行重写，这个配置项需要两个参数，和一个可选的选项；第一个参数是一个正则表达式用来匹配请求中的URI，第二个参数指定了匹配到的URI应该被替换的内容，第三个选项则用来控制是否就此停止`rewrite`的处理（并返回内容），或者发送一个`301` `302`状态码表示重定向
```bash
location /users/ {
    rewrite ^/users/(.*)$ /show?user=$1 break;
}
```

本例中正则表达式`()`捕获的信息会代替第二个参数中的`$1`

可以在`server`和`location`配置块中使用多个`rewrite`。`server`配置块中的一系列`rewrite`指令只会被执行一遍，如果有匹配到的URI则按这条规则进行URI修改，使用修改过的URI进行下一条`rewrite`规则的测试，直到最后一条。`server`中的`rewirte`执行完毕后，nginx会根据新的URI来决定哪一个`location`会处理这个请求，如果`location`中也有`rewrite`规则，则继续对URI进行匹配和修改，修改后的URI要再次进行`location`的匹配，这样一直循环下去，直到匹配到的`location`中的`rewite`规则都不匹配这个新的URI，然后则由这个`location`来处理这个最终的URI请求
```bash
server {
    ...
    rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 last;
    rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  last;
    return  403;
    ...
}
```

上面的例子中混合了`rewrite`和`return`指令，`/download/some/media/file`的请求会被重写为`/download/some/mp3/file.mp3`，因为使用了`last`标志选项，第二条rewrite规则会被直接跳过；如果URI没有被两条rewrite规则匹配到则返回`403`状态码

有两种类型的中断`rewrite`处理的指令选项:
*   **`last`** – Stops execution of the rewrite directives in the current server or location context, but NGINX Plus searches for locations that match the rewritten URI, and any rewrite directives in the new location are applied (meaning the URI can be changed again)

*   **`break`** – Like the break directive, stops processing of rewrite directives in the current context and cancels the search for locations that match the new URI. The rewrite directives in the new location are not executed.

`last`会停止本server或者location中的剩余的rewrite的处理，而`break`则停止所有rewrite处理

##### 重写HTTP相应

使用`sub_filter`配置项可以重写http响应中的某些内容。这个配置项也支持变量和链式替换，可以组合出复杂的替换规则
```bash
location / {
    sub_filter      /blog/ /blog-staging/;
    sub_filter_once off;
}
```
下面的配置中将响应内容中链接的`http://`替换为`http://`并且将本地地址替换为请求头中的服务器地址。而`sub_filter_once`配置项则表示连续的应用本location中的所有`sub_filter`：
```bash
location / {
    sub_filter     'href="http://127.0.0.1:8080/'    'href="http://$host/';
    sub_filter     'img src="http://127.0.0.1:8080/' 'img src="http://$host/';
    sub_filter_once on;
}
```

>注意：已经被替换的内容不会再进行第二次匹配替换

##### 处理错误

使用`error_page`配置项可以让nginx返回一个自定义的错误页面，或者替换为另外一个错误码，或者重定向到另外一个URI地址。下面的例子中如果有404错误时返回`404.html`页面：
```bash
error_page 404 /404.html;
```

上面的配置的意思并不是返回一个404状态码，而是有404状态码出现时会返回对应的页面。响应码可以来自于被代理的服务器或者是nginx本身

下面的例子表示如果有404出现时，替换为301状态码并且重定向到后面的URL地址。这在用户访问一个旧的URL时有用，301状态码表示这个地址已经被永久重定向到后面的URL地址：
```bash
location /old/path.html {
    error_page 404 =301 http:/example.com/new/path.html;
}
```

下面的例子表示当一个请求未找到时，重定向到另外一个URI（被代理的后端服务器）。因为这里没有指定状态码，所以返回给客户端的状态码是被代理的服务器返回的值：
```bash
server {
    ...
    location /images/ {
        # Set the root directory to search for the file
        root /data/www;

        # Disable logging of errors related to file existence
        open_file_cache_errors off;

        # Make an internal redirect if the file is not found
        error_page 404 = /fetch$uri;
    }

    location /fetch/ {
        proxy_pass http://backend/;
    }
}
```

`open_file_cache_errors`配置项表示当请求未找到时阻止写入一条错误消息，因为这里的错误已经被妥善处理，所以关闭这个选项

原文地址：
[**`Nginx admin guide and tutorial`**](https://www.nginx.com/resources/admin-guide/?_ga=1.220252177.848255972.1471312696)