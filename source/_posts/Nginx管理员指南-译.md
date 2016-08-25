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

#### 配置nginx提供静态内容访问

##### 根目录和索引文件

配置文件中的`root`配置项指定了搜索文件的根目录。Nginx会将请求中的URI附加到制定的根目录路径的后面来返回对应的资源。这个配置项可以出现在http，server和location配置块中。下面例子中的server配置块中的root配置项会应用到本server中的所有没有明确指定根目录的location中：
```bash
server {
    root /www/data;

    location / {
    }

    location /images/ {
    }

    location ~ \.(mp3|mp4) {
        root /www/media;
    }
}
```

上面的配置中，如果URI以`/images`开始则从`/www/data/images/`目录中查找请求的资源，而以`mp3/mp4`结尾的请求则从`/www/media/`目录中查找资源

如果请求以斜线结尾，则nginx会认为要请求这个目录中的索引文件。index配置项则指定了索引的文件名（默认是index.html）。在上面的例子中如果用户请求的URI是`/images/some/path/`则nginx会将返回`/www/data/images/some/path/index.html`文件返回。如果这个文件不存在则返回404状态码。可以使用`autoindex`配置项来将用户请求的目录中的所有文件列出，而不是返回目录中的index.html文件:
```bash
location /images/ {
    autoindex on;
}
```

index配置项可以指定多个值，nginx会返回第一个找到的文件:
```bash
location / {
    index index.$geo.html index.htm index.html;
}
```

上面配置中的`$geo`变量是一个使用geo配置项定义的变量，这个变量的值依赖于客户端的IP地址

Nginx根据`index`配置项指定的文件进行搜索，将第一个存在的文件附加到URI路径后面，然后还会依次进行location的匹配，而最后处理这个请求的location可能是另外一个匹配到完整URI的location：
```bash
location / {
    root /data;
    index index.html index.php;
}

location ~ \.php {
    fastcgi_pass localhost:8000;
    ...
}
```

如果用户请求的URI是`/path/`，而`/data/path/index.html`文件不存在，但是`/data/path/index.php`文件存在，则新的请求URI会是`/path/index.php`，所以第二个location会处理这个请求

##### Trying Serval Options

`try_files`配置项可以用来测试特定的文件或者目录是否存在，如果不存在则进行内部重定向或者返回一个错误码。比如要检查URI对应的文件是否存在可以使用`try_files`配置项和`$uri`变量：
```bash
server {
    root /www/data;

    location /images/ {
        try_files $uri /images/default.gif;
    }
}
```

文件以URI的形式指定，最终要测试的文件是应用了root配置项或者alias配置项的结果。本例中nginx会检查URI对应的文件是否存在，如果不存在则返回`/www/data/images/default.gif`文件

最后一个参数可以是一个状态码（需要前置一个等号）或者是一个命名的`location`:
```bash
location / {
    try_files $uri $uri/ $uri.html =404;
}
location / {
    try_files $uri $uri/ @backend;
}

location @backend {
    proxy_pass http://backend.example.com;
}
```

##### 优化静态内容访问速度

加载的速度对nginx静态内容的响应有重要的影响。下面的一些简单的优化方法可能会带来性能的提升

**`启用sendfile`**  
默认情况下，nginx发送文件之前会将文件拷贝至buffer中。启动sendfile配置项，则让nginx直接将内容从一个文件描述符拷贝至另外一个文件描述符，不经过中间的缓存。另外为了避免一个快速的连接整个的占据一个worker进程，我们可以使用`sendfie_max_chunk`配置项限制一个`sendfile()`系统调用可以传输的数据总量：
```bash
location /mp3 {
    sendfile           on;
    sendfile_max_chunk 1m;
    ...
}
```

**`启用tcp_nopush`**  
在开启`sendfile`的情况下启用`tcp_nopush`配置项可以让nginx在sendfile系统调用获取到数据后立即发送http响应头:
```bash
location /mp3 {
    sendfile   on;
    tcp_nopush on;
    ...
}
```

**`启用tcp_nodelay`**  
启用`tcp_nodelay`配置项可以替换Nagle's算法，这种算法被设计用来解决慢速网络中的小数据包问题；这种算法会将多个小数据包合成一个大的数据包发送，所以在发送前等待200ms。默认情况下`tcp_nodelay`已经被开启。所以这个选项仅仅用来保持连接：
```bash
location /mp3  {
    tcp_nodelay       on;
    keepalive_timeout 65;
    ...
}
```

**`优化backlog队列`**  
默认情况下linux待处理连接队列最大值是128:
```bash
sudo sysctl -a | grep somax
可以使用下面的命令更改这个值
sudo sysctl -w net.core.somaxconn=4096
```

或者在/etc/sysctl.conf文件中添加:

    net.core.somaxconn = 4096
    
然后使用`sysctl -p`命令使其生效

nginx中可以在`listen`配置项后面添加`backlog`值:
```bash
server {
    listen 80 backlog 4096;
    # The rest of server configuration
}
```

#### Nginx作为反向代理

Nginx作为反向代理时，可以使用多种协议将请求转发给后端服务器，也可以更改请求头中的信息，或者缓存被代理服务器的响应信息。反向代理的典型应用是，将负载分配到后端的若干服务器，或者是将http请求转换为另外一种请求协议发给后端服务器

##### 将请求转发给后端服务器

将请求转发给后端服务器可以在`location`配置块中使用`proxy_pas`配置项：
```bash
location /some/path/ {
    proxy_pass http://www.example.com/link/;
}
```

配置值也可以是IP地址加端口的形式：
```bash
location ~ \.php {
    proxy_pass http://127.0.0.1:8000;
}
```

> 注意：在第一个例子中配置值指定的地址后面有一个URI，这样的话匹配到的`/some/path/`会被这个URI代替，比如`/some/path/page.html`的请求转发到后端的服务器时会变成`http://www.example.com/link/page.html`；如果配置值中没有URI则原始的请求URI被转发到后端

Nginx支持下面几种形式的非http转发：
*   **fastcgi_pass** passes a request to a FastCGI server
*   **uwsgi_pass** passes a request to a uwsgi server
*   **scgi_pass** passes a request to an SCGI server
*   **memcached_pass** passes a request to a memcached server

> 注意：不同的请求方法可能还需要指定另外的一些参数

配置项`proxy_pass`的值可以是一个命名的主机组，这样的话请求会被分发到组中的主机，详细的信息可以参考后面的负载均衡章节

##### 控制请求头

默认情况下，经过代理的请求头信息会有两处被修改或者添加：`Host`被改为`$proxy_host`变量，`Connection`改为`close`

要更改请求中的头信息可以使用`proxy_set_header`配置项。这个配置项一般用在location中，但也可以用在server和http配置块:
```bash
location /some/path/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_pass http://localhost:8000;
}
```

本例中Host被设置为`$host`变量；如果要阻止某个请求头信息转递给后端主机可以设置为空：
```bash
location /some/path/ {
    proxy_set_header Accept-Encoding "";
    proxy_pass http://localhost:8000;
}
```

##### 配置buffer

默认情况下nginx会缓存后端服务器发送过来的响应信息，直到它收到这个完整的响应信息；这样在客户端较慢的情况下不用浪费服务器的时间

配置项`proxy_buffering`指定了这个设置，默认为on开启的状态

`proxy_buffers`配置项指定了分配给一个请求的buffer的大小和数量。后端服务器响应信息的第一部分（http响应头信息）存放在一个单独的buffer中，这个buffer的大小由`proxy_buffer_size`配置项指定，这个值通常小于其他的buffer的值（但有时候也可能需要大的头信息）：
```bash
location /some/path/ {
    proxy_buffers 16 4k;
    proxy_buffer_size 2k;
    proxy_pass http://localhost:8000;
}
```

如果大部分客户端需要尽快的到相应的信息，我们可以使用`proxy_buffering off`来关闭这个功能。这样就只缓存http响应头，所以只有`proxy_buffer_size`配置项有效

##### 将连接代理的流量绑定到特定的IP

配置项`proxy_bind`可以将发送给后端服务器的数据绑定在某一个接口：
```bash
location /app1/ {
    proxy_bind 127.0.0.1;
    proxy_pass http://example.com/app1/;
}
location /app3/ {
    proxy_bind $server_addr;
    proxy_pass http://example.com/app3/;
}

```

#### 响应内容压缩

压缩相应通常会显著的减少传输的数据量。但同时他也会增加运行时负载，这可能会给服务器性能带来负面影响。Nginx会在将相应内容发送给客户端之前进行压缩，但是不会压缩已经压缩过的响应内容（比如作为一个代理服务器时）

##### 启用压缩

要启用压缩功能，只需要将gzip配置项的值指定为on即可：

    gzip on;
    
默认情况下，Nginx仅仅压缩MIME类型为`text/html`的相应。要压缩其他类型，需要使用`gzip_types`配置项来指定其他的类型：

    gzip_types text/plain application/xml;
    
要指定启用压缩的最小响应体大小，可以使用`gzip_min_length`配置项。默认是20字节（这里增加到1000字节）：
    
    gzip_min_length 1000;
    
默认情况下，Nginx对代理服务发送的请求的响应不会进行压缩。而判断一个请求是否是代理服务器发送过来的依据是请求头中是否包含via头信息。要让Nginx也压缩此类请求的相应，则需要使用`gzip_proxied`配置项，这个配置项有几个配置参数来指定哪种被代理的请求会进行压缩。比如，我们通常只会压缩那些不会被缓存服务器缓存的响应内容。`gzip_proxied`配置项有些参数可以让Nginx检查相应内容的`Cache-Control`头字段，如果字段值是`no-cache`，`no-store`或者`private`时才启用压缩。另外也需要包含`expired`参数来让Nginx检查`Expired`头字段的值。这些参数按照下面例子中的配置方法，后面跟着auth参数可以检查响应头中是否包含`Authorization`头字段（包含这个字段的响应信息不会被缓存）

    gzip_proxied no-cache no-store private expired auth;
    
和其他大多数配置项一样，这些启用压缩的配置项可以在http，server和location配置块中进行配置

整个启用内容压缩的配置项如下：
```bash
server {
    gzip on;
    gzip_types      text/plain application/xml;
    gzip_proxied    no-cache no-store private expired auth;
    gzip_min_length 1000;
    ...
}
```

##### 启用解压缩

一些客户端不能读取使用gzip编码方法压缩的相应内容。有时候我们想存储被压缩的数据（解压缩存储）或者运行时解压缩响应数据然后将它存入缓存。为了同时满足支持和不支持压缩内容的客户端，Nginx运行时解压缩数据然后将其发送给客户端

要启用解压缩，可以使用`gunzip`配置项：
```bash
location /storage/ {
    gunzip on;
    ...
}
```

`gunzip`配置项可以和`gzip`配置项在同一个配置块中使用
```bash
server {
    gzip on;
    gzip_min_length 1000;
    gunzip on;
    ...
}
```

> 注意：本配置项在一个分开的模块中定义，默认的编译参数不能启用这个配置项

##### 发送压缩文件

要发送一个压缩版本的文件到客户端，需要在合适的配置块中设置`gzip_static`配置项的值为on：

    location / {
        gzip_static on;
    }
    
这种情况下，如果客户端发来一个/path/to/file文件的请求，则Nginx会尝试查找并发送/path/to/file.gz文件。如果文件不存在，或者客户端不支持gzip，则发送未压缩的文件

`gzip_static`不会在运行时对文件进行压缩。它仅仅指示nginx使用已经压缩的文件响应请求

> 注意：本配置项在一个分开的模块中定义，默认的编译参数不能启用这个配置项

#### 内容缓存

当启用内容缓存时，如果同样的请求再次到来nginx直接将缓存的内容发给客户端，而不用向后端被代理的服务器去请求资源

##### 启用响应缓存

要启用缓存，可以在http配置块中包含`proxy_cache_path`配置项。第一个强制的参数指定了缓存内容存放的路径，而强制的参数`keys_zone`则定义了缓存zone名称和这个zone对应缓存条目元数据可使用的共享内存的大小；然后就可以在想要缓存的server或者location中使用`proxy_cache`配置项指定这个zone名称：
```bash
http {
    ...
    proxy_cache_path /data/nginx/cache keys_zone=one:10m;

    server {
        proxy_cache one;
        location / {
            proxy_pass http://localhost:8000;
        }
    }
}
```

注意上面定义的内存限制并不是限制总的缓存的数据；被缓存的内容和元数据会另外存储在磁盘特定的文件里，这里只是限制了可存储在内存中的数据的总量。要限制总的缓存的大小需要在`proxy_cache_path`配置项中指定`max_size`参数

##### 缓存需要调用的进程

有两个额外的进程需要在缓存中使用到

缓存管理器：它会周期性的被激活来检查缓存的状态。如果缓存的大小超过了上面配置的限制值，则会删除那些最近最不经常访问的数据；所以在管理器两次被激活期间，缓存数据可能会超出限制值

缓存导入器：只会在nginx刚刚启动时运行一次。会导入之前缓存数据的元数据到共享内存区域。导入整个缓存的元数据可能会消耗大量资源，可能在nginx刚刚运行的短暂时间内降低nginx的性能。要避免这个问题可以使用下面的配置项来分段导入：

*   **loader_threshold** – Duration of an iteration, in milliseconds (by default, 200)
*   **loader_files** – Maximum number of items loaded during one iteration (by default, 100)
*   **loader_sleeps** – Delay between iterations, in milliseconds (by default, 50)
*   
下面的例子中每次迭代时间为300毫秒，每次导入200个条目：

    proxy_cache_path /data/nginx/cache keys_zone=one:10m loader_threshold=300 loader_files=200;
    
##### 指定缓存哪些请求

默认情况下nginx会缓存所有的HTTP GET和HEAD请求的相应数据，并且会在第一次请求时就缓存响应数据。Nginx默认使用请求字符串作为缓存内容的key。如果一个请求的key可以在缓存中找到则直接使用缓存的内容响应客户端。你可以在http、server和location配置块中使用多种配置项来控制缓存

要改变缓存key的计算方法，可以使用`proxy_cache_key`配置项：

    proxy_cache_key "$host$request_uri$cookie_user";
    
可以使用`proxy_cache_min_uses`配置项定义同样的key被请求最少多少次时才会缓存：

    proxy_cache_min_uses 5;
    
要缓存除GET HEAD请求方法之外的其他请求方法可以使用`proxy_cache_method`配置项：

    proxy_cache_methods GET HEAD POST;
    
##### 限制或者绕过缓存

默认情况下，缓存中的内容会被无限期保存直到超出配置的最大限制，然后会删除最不经常被访问的缓存内容。你可以在http server和location配置块中来设置缓存过期条件

针对响应码来设置过期时间可以使用`proxy_cache_valid`配置项：

    proxy_cache_valid 200 302 10m;
    proxy_cache_valid 404      1m;
    
上例中响应码是200和302过期时间为10分钟，而响应码是404的则一分钟过期，要指定所有的响应码可以使用

要设置在什么条件下会将缓存内容发送给客户端可以使用`proxy_cache_bypass`配置项，配置项的每一个参数指定了一个条件，参数中可以包含多个变量。如果有一个参数非空或者不等于`0`，则nginx不会返回缓存中的内容，而是将请求转发给后端服务器：

    proxy_cache_bypass $cookie_nocache $arg_nocache$arg_comment;
    
使用`proxy_no_cache`配置项可以定义在什么条件下nginx不缓存，对条件的检查规则和上面一样：

    proxy_no_cache $http_pragma $http_authorization;
    
##### 混合配置示例

```bash
http {
    ...
    proxy_cache_path /data/nginx/cache keys_zone=one:10m loader_threshold=300 
                     loader_files=200 max_size=200m;

    server {
        listen 8080;
        proxy_cache one;

        location / {
            proxy_pass http://backend1;
        }

        location /some/path {
            proxy_pass http://backend2;
            proxy_cache_valid any 1m;
            proxy_cache_min_uses 3;
            proxy_cache_bypass $cookie_nocache $arg_nocache$arg_comment;
        }
    }
}
```
#### 管理SSL连接

##### 设置一个HTTPS服务器

要设置https服务，只需要在配置文件的server配置块中listen配置项后面加上ssl选项值，然后指定服务器证书和私钥文件的路径即可：
```bash
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ...
}
```

服务器证书就是公钥，会发送给所有连接到本服务器的客户端。私钥应该放置在一个访问受限的安全的位置，只允许服务器master进程读取私钥文件。服务器证书和私钥也可以存储与同一个文件中：
    
    ssl_certificate www.example.com.cert;
    ssl_certificate_key www.example.com.cert;
    
这种情况下这个文件的访问要受到限制。尽管服务器证书和私钥放置在同一个文件中，但是客户端连接时Nginx只会把公钥发送给客户端

`ssl_protocols` `ssl_ciphers`配置项可以用来限制只能使用特定版本的安全协议和安全套件

1.0.5版本的Nginx默认使用`ssl_protocols SSLv3 TLSv1 和 ssl_ciphers HIGH:!aNULL:!MD5`；从版本1.1.13和1.0.12，默认使用`ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2`

旧的安全套件设计中常常有一些缺陷，明智的做法是在现在的Nginx配置中禁用他们（但是可能会有一些需要向后兼容的需求让你不太那么容易使用最新的安全套件）。请注意CBC-mode安全套件可能遭受到一些安全攻击，目前使用SSLv3是避免这些攻击的最好办法

##### HTTPS服务器优化

SSL加密服务会额外消耗一些CPU资源。最耗资源的是SSL协议的握手阶段。下面两种方法可以用来减少这些操作：
*   启用长连接来在同一个连接中发送多个资源
*   重用SSL会话的参数可以在并发和后续连接建立时减少SSL握手的操作

可以使用`ssl_session_cache`配置项启用ssl会话缓存功能，Sessions被存储在SSL会话缓存中并且被所有worker进程共享。1M的缓存大概可以存储4000个Sessions。默认的缓存超时时间是5分钟，这个时间可以使用`ssl_session_timeout`配置项来更改。下面是一个优化配置的示例：
```bash
worker_processes auto;

http {
    ssl_session_cache   shared:SSL:10m;
    ssl_session_timeout 10m;

    server {
        listen              443 ssl;
        server_name         www.example.com;
        keepalive_timeout   70;

        ssl_certificate     www.example.com.crt;
        ssl_certificate_key www.example.com.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;
        ...
    }
}
```

##### SSL证书链

虽然我们的证书已经被权威证书机构签名，但是还有有一些浏览器提示不能验证证书的签名。这可能是因为给我们证书签名的权威证书机构使用的是他们签过名的中间证书，而这些中间证书可能并没有内置到浏览器中。这种情况下我们应该将权威证书机构提供的一系列中间证书连接到我们的服务器证书文件中。我们的服务器证书必须出现在中间证书之前：
```bash
$ cat www.example.com.crt bundle.crt > www.example.com.chained.crt
```    

然后使用这个证书来替代之前的服务器证书：
```bash
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.chained.crt;
    ssl_certificate_key www.example.com.key;
    ...
}
```

浏览器通常会缓存这些接收到并且已经被内置权威证书验证过的中间证书。然后我们的服务器证书可以被这些中间证书验证，这样浏览器就不会报警告了。要确保服务器会发送完整的证书链到客户端我们可以使用openssl客户端工具进行验证：
```bash
$ openssl s_client -connect www.godaddy.com:443
...
Certificate chain
 0 s:/C=US/ST=Arizona/L=Scottsdale/1.3.6.1.4.1.311.60.2.1.3=US
     /1.3.6.1.4.1.311.60.2.1.2=AZ/O=GoDaddy.com, Inc
     /OU=MIS Department/CN=www.GoDaddy.com
     /serialNumber=0796928-7/2.5.4.15=V1.0, Clause 5.(b)
   i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc.
     /OU=http://certificates.godaddy.com/repository
     /CN=Go Daddy Secure Certification Authority
     /serialNumber=07969287
 1 s:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc.
     /OU=http://certificates.godaddy.com/repository
     /CN=Go Daddy Secure Certification Authority
     /serialNumber=07969287
   i:/C=US/O=The Go Daddy Group, Inc.
     /OU=Go Daddy Class 2 Certification Authority
 2 s:/C=US/O=The Go Daddy Group, Inc.
     /OU=Go Daddy Class 2 Certification Authority
   i:/L=ValiCert Validation Network/O=ValiCert, Inc.
     /OU=ValiCert Class 2 Policy Validation Authority
     /CN=http://www.valicert.com//emailAddress=info@valicert.com
...
```

本例中主体为`s`的`www.godaddy.com`的服务器证书`#0`是由证书发行者`i`签名的，而`i`又是`#1`证书的主体。证书`#1`的签发者又是证书`#2`的主体。而证书`#2`是由权威证书发行商`ValliCert Inc`签名的，后者的证书是内置于我们的浏览器中

如果没有将中间证书添加到服务器证书中，则只有服务证书`#0`被显示

##### A Single HTTP/HTTPS Server

我们可以将含有`ssl`配置项值的`listen`配置项加入到一个`server`配置块中来让这个server可以同时处理HTTP和HTTPS的请求：
```bash
server {
    listen              80;
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ...
}
```
在0.7.13版本之前的nginx需要使用`ssl`配置项来启用server的HTTPS处理能力，所以被配置`ssl`配置项的server配置块就不能处理非HTTPS的请求；现在可以用在`listen`配置项后面加上`ssl`配置值的方法来启用HTTPS所以可以使用上面的配置方法

##### 基于主机名的HTTPS服务器

如果有多个使用HTTPS的servers需要在同一个IP地址接收请求的话就会有问题：
```bash
server {
    listen          443 ssl;
    server_name     www.example.com;
    ssl_certificate www.example.com.crt;
    ...
}

server {
    listen          443 ssl;
    server_name     www.example.org;
    ssl_certificate www.example.org.crt;
    ...
}
```

如果使用上面的配置方法，则客户端会收到默认的服务器证书，在本例中是`www.example.com`，即使你请求的是第二个server的主机名。这是由于SSL协议特定导致的，因为在nginx收到客户端的http请求之前（能拿到客户都请求中的HOST字段值），首先要建立SSL连接，这时候就需要服务器端证书了。所以nginx只能提供默认的服务器证书

解决这个问题最好的方法是给每一个启用HTTPS的server使用不同的IP地址：
```bash
server {
    listen          192.168.1.1:443 ssl;
    server_name     www.example.com;
    ssl_certificate www.example.com.crt;
    ...
}

server {
    listen          192.168.1.2:443 ssl;
    server_name     www.example.org;
    ssl_certificate www.example.org.crt;
    ...
}
```

注意还有一些关于HTTPS upstreams的特殊的设置（`proxy_ssl_ciphers /proxy_ssl_protocols` `proxy_ssl_session_reuse`）可以用来调优Nginx和上游服务器之间的SSl传输

###### 多个主机名使用同一个证书

有另外一些方法来让多个HTTPS servers使用同一个IP地址，但他们都有一些不足的地方。一种方法是在证书的`SubjectAltName`字段使用多个主机名，比如`www.example.org` `www.example.com`。但是要注意的是`SubjectAltName`字段的长度是有限制的

另外一种方式是使用通配型的证书，比如`*.example.org`。这样这个证书就可以验证所有`example.org`的子域名（主机名）；但是这里的通配符`*`只能指代一级，即不能用这个证书验证`www.subdomain.example.org`主机名。证书的`SubjectAltName`字段值可以混合精确的主机名和通配的主机名

这样我们可以将指定证书的配置项放在`http`配置块中：
```bash
ssl_certificate     common.crt;
ssl_certificate_key common.key;

server {
    listen          443 ssl;
    server_name     www.example.com;
    ...
}

server {
    listen          443 ssl;
    server_name     www.example.org;
    ...
}
```

###### Server Name Indication

另外一在同一个IP地址上支持多个HTTPS servers的方法是使用TLS的SNI扩展，但是目前支持这个扩展的浏览器比较少

##### 兼容性提示

*   The SNI support status has been shown by the “-V” switch since versions 0.8.21 and 0.7.62.
*   The ssl parameter of the listen directive has been supported since version 0.7.14. Prior to version 0.8.21 it could only be specified along with the default parameter.
*   SNI has been supported since version 0.5.32.

*   The shared SSL session cache has been supported since version 0.5.6.

*   From version 0.7.65, 0.8.19 and later the default SSL protocols are SSLv3, TLSv1, TLSv1.1, and TLSv1.2 (if supported by the OpenSSL library).

*   From version 0.7.64, 0.8.18 and earlier the default SSL protocols are SSLv2, SSLv3, and TLSv1.

*   From version 1.0.5 and later the default SSL ciphers are HIGH:!aNULL:!MD5
*   From version 0.7.65, 0.8.20 and later the default SSL ciphers are HIGH:!ADH:!MD5
*   From version 0.8.19 the default SSL ciphers are ALL:!ADH:RC4+RSA:+HIGH:+MEDIUM
*   From version 0.7.64, 0.8.18 and earlier the default SSL ciphers are ALL:!ADH:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP

原文地址：
[**`Nginx admin guide and tutorial`**](https://www.nginx.com/resources/admin-guide/?_ga=1.220252177.848255972.1471312696)
