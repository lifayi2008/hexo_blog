---
title: Curl文档(译)
date: 2016-09-24 14:19:22
tags: [bash, tools]
categories: Tools
---
**语法**

    curl [options] [URL...]
    
**简介**
<!-- more -->
**`curl`** 是一个向服务器发送/接收数据的工具，可以支持下列协议`DICT`, `FILE`, `FTP`, `FTPS`, `GOPHER`, `HTTP`, `HTTPS`, `IMAP`,
       `IMAPS`, `LDAP`, `LDAPS`, `POP3`, `POP3S`, `RTMP`, `RTSP`, `SCP`, `SFTP`, `SMTP`, `SMTPS`, `TELNET`, `TFTP`。这个工具被设置为一次性请求，而不是交互式的
       
`curl`提供了很多有用的功能比如：代理支持、用户认证、FTP上传、HTTP post、SSL连接、cookies支持、文件断点传输、Metalink等等。下面将会介绍

**URL**

`URL`是协议独立的。详细信息可以参考`RFC 3986`

你可以使用`{}`指定多个`URL`或者`URL`部分：

    http://site.{one,two,three}.com
    
也可以使用`[]`制定一个字母或者数字序列：

    ftp://ftp.numericals.com/file[1-100].txt
    ftp://ftp.numericals.com/file[001-100].txt    ##with leading zeros
    ftp://ftp.letters.com/file[a-z].txt
    
不能嵌套使用这些标记，但是可以在一个`URL`中同时使用多个：

    http://any.org/archive[1996-1999]/vol[1-4]/part{a,b,c}.html
    
也可以指定步进值：

    http://www.numericals.com/file[1-100:10].txt
    http://www.letters.com/file[a-z:2].txt
    
可以在命令行中指定任意数量的`URL`。curl会按照给出的顺序一个一个的取数据

如果你指定的`URL`没有协议前缀，curl会默认使用`HTTP`协议进行取数据，并且会根据你的主机前缀进行猜测，比如：`ftp.test.com` `curl`会尝试使用`FTP`协议进行连接；curl不会进行`URL`的合法性检查

在一个命令中向同一个服务器请求多个资源时，curl会尝试重用`TCP`连接，这样可以加快速度

**处理进度** 

curl在处理过程中默认会显示一个处理进度，表示已经传输的数据、传输速度、和消耗的时间信息，curl默认将这些信息打印到终端；如果你的curl请求会返回大量的数据则curl不会显示这些信息

> 大部分情况下你可以使用重定向操作符`>`或者`-o`选项将返回数据写入到文件，这样可以显示出处理进度信息；使用`ftp`上传时不可以，因为它不会返回任何信息

可以使用`-#`来用一个进度条代替常规的进度信息

**选项**

选项使用一个或者两个连字符开始，很多选项需要指定一个参数；使用一个连字符时，比如：`-d`，它和后面的选项参数之间可以有空格也可以没有空格，而使用两个连字符的选项和后面的参数之间必须有至少一个空格；不需要参数的选项可以连在一起写，比如：`-OLv`

通常布尔类型的参数，比如使用`--option`启用的都可以使用`--no-option`禁用，下面的列表中仅列出了`--option`的使用

这些选项中有的连续使用多次是有意义的，比如`-d`；但是另外一些可能无意义，这样有的是第一个起作用，有的是最后一个起作用，详细信息参考`man page`

**`-#`**  
使用`#`作为进度条来代替默认的处理进度信息

**`-0/--http1.0 --http1.1 --http2.0`**  
使用`http1.0 http1.1 http2.0`协议来发送请求，默认使用`http1.1`，`http2.0`需要底层的`libcurl`编译支持

**`-1/--tlsv1 -2/--sslv2 -3/--sslv3`** 
在向远程的`TLS/SSL`服务器发送请求时使用的协议

**`-4/--ipv4 -6/--ipv6`** 
将请求的主机名仅仅解析为`ipv4`地址/`ipv6`地址

**`-a/--append`**  
在使用`FTP/SFTP`协议进行上传数据时，告诉服务器将内容添加到目标文件，而不是覆盖。如果文件不存在则创建这个文件。`SSH`服务器会忽略这个选项

**`-A/--user-agent <agent string>`** 
在`HTTP`协议中指定`User-Agent`头信息，如果`agent string`中有空格则使用单引号引用；这个选项可以使用`-H --header`选项代替

**`--anyauth`**  
在`HTTP`协议中，告诉curl发送请求并选择一个服务器返回的可支持的最安全的认证方法。具体的做法是先发送一个请求，并检查服务器的响应头信息拿到所有可支持的认证方法，这需要多一次的`HTTP`请求。这用来代替直接指定认证方法：`--basic` `--digest` `--ntlm` `--negotiate`

> 注意在有提交/上传请求时不要使用这个参数，这可能会导致两次提交


**`-b/--cookie <name=data>`**  
在`HTTP`协议中，将`name=data`作为cookie数据传送给http服务器。通常是上一次请求中从服务器收到的`Set-Cookie`头信息的内容。数据格式为`NAME1=VALUE1; NAME2=VALUE2`

如果参数中不包含`=`则被当作是一个文件名，会根据请求地址从文件中查找匹配的cookies信息；使用这个方法可以启用curl的`cookie parser`，这样可以保存本次请求收到的cookies信息，也可以制定`-L --location`选项来手动处理本次请求收到的cookies；文件的格式应该是文本格式包含HTTP头信息，或者是`Netscape/Mozilla`的cookie文件格式

**`-B/--use-ascii`**  
在`FTP/LDAP`协议中启用ASCII传输。在FTP协议中可以使用在URL末尾加上`;type=A`的方法达成同样的效果

**`--basic`**  
在`HTTP`协议中使用`--basic`认证方法，这是默认使用的方法。一般不同指定这个选项，除非你之前的请求使用了别的认证方法，然后在这次请求中要改变它

**`-c/--cookie-jar <file name>`**  
用在`HTTP`协议中，指定curl将所有cookies写入的文件。curl将之前从文件中读取的cookis和从服务器收到的cookies都写入到这个文件。如果没有使用cookies则不会写文件。文件使用`Netscape`cookie文件格式；如果使用`-`代替文件名则cookie被写到标准输出

使用这个选项可以启用curl的cookie处理系统来记录和使用cookie；如果因为其他原因导致文件不能写入，则curl不会执行失败或者报错，使用`-V`选项可以得到一个警告信息

**`-C, --continue-at <offset>`**  
根据特定的`offset`继续/重启一个之前进行的文件传输。给定的`offset`表示会被跳过的精确的字节数，从之前准备传输的文件的开始进行计数。如果用在ftp上传文件中，curl不会使用FTP服务器的`SIZE`命令来获取上传的字节数

使用`-C -`表示让curl自动计算出从哪里开始继续传输。然后在文件中找到相应的位置

**`--ciphers <list of ciphers>`**  
在`SSL`连接中指定使用的加密套件。`list of ciphers`必须是合法的加密套件。关于SSl加密套件列表详情可以查看[这里](http://www.openssl.org/docs/apps/ciphers.html)

**`--compressed`**  
在`HTTP`协议中请求返回一个curl支持的压缩方法的压缩响应内容，然后curl进行解压缩并返回数据。如果使用这个选项并且服务器返回一个不支持的压缩格式，则curl报错

**`--connect-timeout <seconds>`**  
指定在连接阶段的超时时间（秒）；如果连接已经建立则这个选项不起作用

**`--create-dirs`**  
和`-o`选项连用时，curl会根据需求在本地创建`-o`选项指定的目录结构。如果`-o`选择没有指定目录或者目录已经存在则不会创建

使用`FTP` `SFTP`协议时，如果要在远程创建目录可以使用`--ftp-create-dirs`选项

**`--crlf`**  

**`--crlfile <file>`**  
在`HTTPS` `FTPS`协议中，提供一个pem格式的吊销证书列表来指定对端的证书已经准备被吊销

**`-d, --data <data>`**  
**`--data-ascii <data>`**  
**`--data-binary <data>`**  
在`HTTP`协议中使用POST请求发送指定的数据到服务器，就像在浏览器中填写表单然后点击提交按钮一样。使用这参数curl会在http头中将`Content-type`设置为`application/x-
              www-form-urlencoded`，表示由表单提交过来的数据。可以和`-F --form`对比一下
              
这个选项等同于`--data-ascii`选项。要使用二进制方式提交数据，需要添加`--data-binary`选项；如果已经对提交的数据进行过`URL`编码，请使用`--data-urlencode`选项

如果使用多个`-d`选项，则效果等同于使用`&`将他们连接起来，合并提交

如果`data`以`@`开始，则其他部分被认为是一个文件名，然后从文件中读取数据；`data`也可以是`-`表示从标准输入中读取。也可以指定多个文件，文件中的空行被忽略

`--data-binary`表示数据/文件不经任何处理直接提交
              
**`-D, --dump-header <file>`**  
在`HTTP`协议中，用来保存服务器发送过来的响应头信息到`file`中，然后可以在请求中使用`-b`选项读取文件中的`cookies`信息；`-c`选项可以以更合理的方式存储`cookies`

也可以用在`FTP`协议中，ftp响应行被当作头信息存储

**`--data-urlencode <data>`**  
在`HTTP`协议中，表示将数据进行`URL-encoding`然后再POST提交，`<data>`可以是如下格式：

*   `content` : 如果指定的内容中不包括`@` `=`字符，则curl直接进行`URL-encoding`然后提交，否则按下面规则
*   `=content` : 将`content`进行编码和提交，内容不包括`=`
*   `name=content` : 将`content`进行编码和提交，`name`应该是已经编码过的内容
*   `@filename` : 从文件中读取内容(包括换行)，然后编码提交
*   `name@filename` :  This  will  make curl load data from the given file (including any newlines), URL-encode that data and pass it on in the POST.
                     The name part gets an equal sign appended, resulting in name=urlencoded-file-content. Note that the name  is  expected  to  be
                     URL-encoded already

**`--delegation LEVEL`**  

**`--digest`**  
在`HTTP`协议中启用HTTP Digest认证方法，这用来避免密码的明文传输；可以和`-u`选项合用来指定用户名和密码。相关的选项还有`--ntlm` `--negotiate` `--anyauth`

**`--disable-epsv`**  
在`FTP`协议中，表示在被动FTP传输时禁用`EPSV`命令。通常curl会首先使用`EPSV`，然后才是`PASV`

这个选项只在被动传输模式下有效；`--epsv`表示显式的使用`EPSV`，`--no-epsv`等同于`--disable-epsv`

**`--disable-eprt`**  
在`FTP`协议中，表示在主动FTP传输时禁用`EPRT`和`LPRT`命令。curl总是会首先尝试`EPRT`，然后是`LPRT`，最后是`PORT`，但使用这个选项后curl直接使用`PORT`命令。`EPRT` `LPRT`是FTP协议的扩展，可能有些ftp服务器并未实现这两个命令，但是他们提供了比`PORT`命令更丰富的功能

这个选项只在主动传输模式下有效；`--eprt`表示显式启用`EPRT`命令，`--no-eprt`等同于`--disable-eprt`

**`--dns-interface <interface>`**  
让curl在进行DNS请求时使用指定的接口发送请求，这个选项需要和`--interface`合用

**`--dns-ipv4-addr <ip-address>`**  
**`--dns-ipv6-addr <ip-address>`**  
在进行ipv4/ipv6的DNS请求时使用的服务器的地址

**`--dns-servers <ip-address,ip-address>`**  
在发送请求时使用制定的DNS服务器列表代替系统配置的。地址列表使用逗号分隔。可选的的可以在地址的后面加上端口：`:port`

> 上面四个选项都需要在编译libcurl时开启相关支持

**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
**``**  
