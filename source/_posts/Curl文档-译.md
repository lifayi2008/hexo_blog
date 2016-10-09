---
title: Curl文档(译)
date: 2016-09-24 14:19:22
tags: [bash, tools]
categories: Tools
---
**语法**

    curl [options] [URL...]
<!--more-->
**简介**

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
`(HTTP)`指定`User-Agent`头信息，如果`agent string`中有空格则使用单引号引用；这个选项可以使用`-H --header`选项代替

**`--anyauth`**  
`(HTTP)`告诉curl发送请求并选择一个服务器返回的可支持的最安全的认证方法。具体的做法是先发送一个请求，并检查服务器的响应头信息拿到所有可支持的认证方法，这需要多一次的`HTTP`请求。这用来代替直接指定认证方法：`--basic` `--digest` `--ntlm` `--negotiate`

> 注意在有提交/上传请求时不要使用这个参数，这可能会导致两次提交


**`-b/--cookie <name=data>`**  
`(HTTP)`将`name=data`作为cookie数据传送给http服务器。通常是上一次请求中从服务器收到的`Set-Cookie`头信息的内容。数据格式为`NAME1=VALUE1; NAME2=VALUE2`

如果参数中不包含`=`则被当作是一个文件名，会根据请求地址从文件中查找匹配的cookies信息；使用这个方法可以启用curl的`cookie parser`，这样可以保存本次请求收到的cookies信息，也可以制定`-L --location`选项来手动处理本次请求收到的cookies；文件的格式应该是文本格式包含HTTP头信息，或者是`Netscape/Mozilla`的cookie文件格式

**`-B/--use-ascii`**  
`(FTP/LDAP)`启用ASCII传输。在FTP协议中可以使用在URL末尾加上`;type=A`的方法达成同样的效果

**`--basic`**  
`(HTTP)`使用`--basic`认证方法，这是默认使用的方法。一般不同指定这个选项，除非你之前的请求使用了别的认证方法，然后在这次请求中要改变它

**`-c/--cookie-jar <file name>`**  
`(HTTP)`指定curl将所有cookies写入的文件。curl将之前从文件中读取的cookis和从服务器收到的cookies都写入到这个文件。如果没有使用cookies则不会写文件。文件使用`Netscape`cookie文件格式；如果使用`-`代替文件名则cookie被写到标准输出

使用这个选项可以启用curl的cookie处理系统来记录和使用cookie；如果因为其他原因导致文件不能写入，则curl不会执行失败或者报错，使用`-V`选项可以得到一个警告信息

**`-C, --continue-at <offset>`**  
根据特定的`offset`继续/重启一个之前进行的文件传输。给定的`offset`表示会被跳过的精确的字节数，从之前准备传输的文件的开始进行计数。如果用在ftp上传文件中，curl不会使用FTP服务器的`SIZE`命令来获取上传的字节数

使用`-C -`表示让curl自动计算出从哪里开始继续传输。然后在文件中找到相应的位置

**`--ciphers <list of ciphers>`**  
`(SSL)`指定使用的加密套件。`list of ciphers`必须是合法的加密套件。关于SSl加密套件列表详情可以查看[这里](http://www.openssl.org/docs/apps/ciphers.html)

**`--compressed`**  
`(HTTP)`请求返回一个curl支持的压缩方法的压缩响应内容，然后curl进行解压缩并返回数据。如果使用这个选项并且服务器返回一个不支持的压缩格式，则curl报错

**`--connect-timeout <seconds>`**  
指定在连接阶段的超时时间（秒）；如果连接已经建立则这个选项不起作用

**`--create-dirs`**  
和`-o`选项连用时，curl会根据需求在本地创建`-o`选项指定的目录结构。如果`-o`选择没有指定目录或者目录已经存在则不会创建

使用`FTP` `SFTP`协议时，如果要在远程创建目录可以使用`--ftp-create-dirs`选项

**`--crlf`**  

**`--crlfile <file>`**  
`(HTTPS FTPS)`提供一个pem格式的吊销证书列表来指定对端的证书已经准备被吊销

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
`(HTTP)`用来保存服务器发送过来的响应头信息到`file`中，然后可以在请求中使用`-b`选项读取文件中的`cookies`信息；`-c`选项可以以更合理的方式存储`cookies`

也可以用在`FTP`协议中，ftp响应行被当作头信息存储

**`--data-urlencode <data>`**  
`(HTTP)`表示将数据进行`URL-encoding`然后再POST提交，`<data>`可以是如下格式：

*   `content` : 如果指定的内容中不包括`@` `=`字符，则curl直接进行`URL-encoding`然后提交，否则按下面规则
*   `=content` : 将`content`进行编码和提交，内容不包括`=`
*   `name=content` : 将`content`进行编码和提交，`name`应该是已经编码过的内容
*   `@filename` : 从文件中读取内容(包括换行)，然后编码提交
*   `name@filename` :  This  will  make curl load data from the given file (including any newlines), URL-encode that data and pass it on in the POST.
                     The name part gets an equal sign appended, resulting in name=urlencoded-file-content. Note that the name  is  expected  to  be
                     URL-encoded already

**`--delegation LEVEL`**  

**`--digest`**  
`(HTTP)`启用HTTP Digest认证方法，这用来避免密码的明文传输；可以和`-u`选项合用来指定用户名和密码。相关的选项还有`--ntlm` `--negotiate` `--anyauth`

**`--disable-epsv`**  
`(FTP)`表示在被动FTP传输时禁用`EPSV`命令。通常curl会首先使用`EPSV`，然后才是`PASV`

这个选项只在被动传输模式下有效；`--epsv`表示显式的使用`EPSV`，`--no-epsv`等同于`--disable-epsv`

**`--disable-eprt`**  
`(FTP)`表示在主动FTP传输时禁用`EPRT`和`LPRT`命令。curl总是会首先尝试`EPRT`，然后是`LPRT`，最后是`PORT`，但使用这个选项后curl直接使用`PORT`命令。`EPRT` `LPRT`是FTP协议的扩展，可能有些ftp服务器并未实现这两个命令，但是他们提供了比`PORT`命令更丰富的功能

这个选项只在主动传输模式下有效；`--eprt`表示显式启用`EPRT`命令，`--no-eprt`等同于`--disable-eprt`

**`--dns-interface <interface>`**  
让curl在进行DNS请求时使用指定的接口发送请求，这个选项需要和`--interface`合用

**`--dns-ipv4-addr <ip-address>`**  
**`--dns-ipv6-addr <ip-address>`**  
在进行ipv4/ipv6的DNS请求时使用的服务器的地址

**`--dns-servers <ip-address,ip-address>`**  
在发送请求时使用制定的DNS服务器列表代替系统配置的。地址列表使用逗号分隔。可选的的可以在地址的后面加上端口：`:port`

> 上面四个选项都需要在编译libcurl时开启相关支持

**`-e, --referer <URL>`**  
设置`HTTP`头中的`Referer`信息。也可以使用`-H`选项设置。在和`-L`选项同时使用时，可以在`--referer`的选项参数后面加上`;auto`，这样HTTP在进行跳转时会自动将命令行中的URL设置为跳转时请求的头中的`Referer`字段

**`-E, --cert <certificate[:password]>`**  
`(SSL)`让curl使用指定的客户端证书文件。如果使用`Secure Transport`的话证书必须是`PKCS#12`格式，如果使用其他安全引擎的话证书必须是`PEM`格式。如果可选的`:password`未给出则会在终端中请求输入。注意本选项中的`certificate`应该是私钥文件

If  curl  is  built  against the NSS SSL library then this option can tell curl the nickname of the certificate to use within the NSS
              database defined by the environment variable SSL_DIR (or by default /etc/pki/nssdb). If the NSS PEM PKCS#11 module (libnsspem.so)  is
              available  then PEM files may be loaded. If you want to use a file from the current directory, please precede it with "./" prefix, in
              order to avoid confusion with a nickname.  If the nickname contains ":", it needs to be preceded by "\" so that it is not  recognized
              as  password delimiter.  If the nickname contains "\", it needs to be escaped as "\\" so that it is not recognized as an escape char‐
              acter.

(iOS and Mac OS X only) If curl is built against Secure Transport, then the certificate string can either be the name of  a  certifi‐
              cate/private  key  in the system or user keychain, or the path to a PKCS#12-encoded certificate and private key. If you want to use a
              file from the current directory, please precede it with "./" prefix, in order to avoid confusion with a nickname.

**`--engine <name>`**  
选择加密操作的OpenSSL引擎。使用`--engine list`选项参数可以列出支持的引擎列表。注意：不是所有的引擎在运行时都是可用的

**`--environment`**  

**`--egd-file <file>`**  

**`--cert-type <type>`**  
告诉curl使用的证书的格式。可以支持`PEM` `DER`  `ENG`类型。默认使用`PEM`格式

**`--cacert <CA certificate>`**  
`(SSL)`告诉curl使用指定的证书文件来验证对端。文件中可以包含多个CA证书。证书必须是`PEM`格式。通常curl会使用默认的证书文件，这个选项可以让curl使用另外的证书文件

curl可以识别`CURL_CA_BUNDLE`环境变量，并使用其值来作为证书文件所在的路径；使用这个选项参数可以覆盖这个环境变量的设置

**`--capath <CA certificate directory>`**  
`(SSL)`告诉curl在指定的目录中查找证书文件来验证对端。可以使用多个路径--路径中间使用`:`隔开。证书必须是`PEM`格式，并且如果curl被编译不支持OpenSSL，则使用OPenSSL提供的目录时必须首先使用`c_rehash`工具进行处理。Using --capath can allow OpenSSL-powered curl to make SSL-
              connections much more efficiently than using --cacert if the --cacert file contains many CA certificates.

**`-f, --fail`**  
`(HTTP)`抑制服务器返回错误时的输出信息。这在脚本中进行错误尝试时有用。一般情况下当请求的资源不存在或者服务器内部错误时，服务器会返回一个表示出错原因的页面，使用这个选项可以让curl不显示这个页面，而使用一个22退出状态表示错误

**`-F, --form <name=content>`**  
`(HTTP)`让curl模拟提交一个已经填写的表单。curl会使用POST发送请求并且在请求头中将`Content-Type`设置为`multipart/form-data`，这也允许进行二进制文件上传。在`content`前置一个`@`表示这是一个文件名，而要从文件中获取内容进行提交的话在`content`之前加一个`<`符号；可以使用`-`代替文件名表示从标准输入获取内容

可以在`content`内容之后增加`;type=<type>`来指定提交的数据的类型：

    curl -F "web=@index.html;type=text/html" url.com

**`--ftp-account [data]`**  
**`--ftp-alternative-to-user <command>`**  
**`--ftp-create-dirs`**  
**`--ftp-method [method]`**  
**`--ftp-pasv`**  
**`--ftp-skip-pasv-ip`**  
**`--ftp-pret`**  
**`--ftp-ssl-ccc`**  
**`--ftp-ssl-ccc-mode [active/passive]`**  
**`--ftp-ssl-control`**  

**`--form-string <name=string>`**  
`(HTTP)`类似于`--form`，区别是`string`总是一个字符串；前置的`@` `<`和`;type=`形式都没有特殊意义。如果要提交的字符串值中出现`@`和`<`则应该优先使用这个选项

**`-g, --globoff`**  
这个选项关闭`URL globbing parser`。使用这个选项你可以在URL中包含`{}` `[]`字符，这些字符不会被curl解释。注意这些字符不是正常合法的URL组成部分，所以需要转码

**`-G, --get`**  
使用这个选项，则所有指定提交数据的选项`-d` `--data` `--data-binary` `--data-urlencode`都使用GET请求代替POST请求，如果不指定这个选项则默认使用POST提交。所有提交的数据都会被添加在URL的后面

如果和`-I`合用，则会使用`HEAD`请求并将提交的数据添加到URL后面

**`-H, --header <header>`**  
`(HTTP)`指定请求时使用的额外头信息。你可以指定任意数量的头。注意，如果你添加的头的字段名和curl使用的内置的头字段名重复的话，这个内置的头字段值被替换。所以这允许你更改所有的请求信息，但是除非你很熟悉这些内容，否则最好不要更改默认的头信息。如果要去掉一个内置头信息可以将`<header>`中冒号右边的部分置空，比如 `-H "Host:"`，如果要发送一个自定义的无值的头信息则需要以冒号结尾:`-H "X-Custom-Header;"`。curl会保证你发送的所有头信息都有正确的行结尾标志，所以你不必自行添加

**`--hostpubmd5 <md5>`**  
`(SCP/SFTP)`发送一个包含32为十六进制数值的字符串。字符串的内容是远程主机公钥的128位md5值，除非md5值匹配否则curl拒绝连接（不能理解）

**`--ignore-content-length`**  
`(HTTP)`忽略`Content-Length`头信息。这在Apache1.x版本的服务器中有用，因为如果传输的文件大于2G的服务器会报错

**`-i, --include`**  
`(HTTP)`让curl的响应输出包括`HTTP`头信息

**`-I, --head`**  
`(HTTP)`只获取相应的头信息。在`FTP/FILE`中curl只显示文件大小和最后更改时间

**`--interface <name>`**  
在指定的接口上发出请求，`name`可以是接口名、IP地址和主机名

**`-j, --junk-session-cookies`**  
`(HTTP)`当curl从文件中读取cookies数据时，这个选项可以让curl丢弃所有 session cookies。效果如同开启了一个新的会话。典型的浏览器总是在被关闭时丢弃所有的 session cookies

**`-J, --remote-header-name`**  

**`-k, --insecure`**  
`(SSL)`这个选项显式的允许不安全的`SSL`连接和传输。默认情况下所有的`SSL`连接都使用安装的CA证书进行安全性验证，服务器认为不安全的连接会失败

**`-K, --config <config file>`**  
指定读取命令行参数、选项的文件，这个文件是一个文本文件，文件的内容可以是所有可以在命令行指定的选项

选项和选项对应的参数必须是在同一行，并且使用空格或者冒号分隔；长选项可以不用写出前面的两个连字符`--`，如果这样使用的话，长选项和对应的参数使用空格和分号分隔。如果使用一个或者两个连字符则长选项和对应的参数之间可以没有分隔符

如果参数值中有空格则需要使用引用。在双引号引用中，下面的这些转义字符有特殊意义：`\\` `\"` `\t` `\n` `\r` `\v`，在其他任何字符前加一个反斜线都是无效的。文件中如果第一个字符是`#`则本行其他的内容当作注释

将文件名指定为`-`则curl从标准输入读取文件

注意：如果要在文件中指定URL地址则需要使用`--url`选项，形如：`url = "http://curl.haxx.se/docs/"`

当调用curl命令时，它会总是查找并使用一个默认的配置文件（除非使用`-q`选项），默认从下面制定的位置和顺序进行查找：

*   首先会从`home`目录中进行查找，`home`目录是变量`CURL_HOME`或者`HOME`的值。如果获取`home`目录失败，则调用`getpwuid()`函数，这个函数会返回系统中当前用户的家目录；如果在windows系统中，则会接着解析`APPDATA`变量，or as a last resort the '%USERPROFILE%\Application Data'
*   在windows系统中如果`home`目录中没有`_curlrc`文件，则会查找curl可执行文件所在的目录；在Unix系统中，curl只会从得到的`home`目录中导入`.curlrc`文件

下面是一个文件示例：
```bash
# --- Example file ---
# this is a comment
url = "curl.haxx.se"
output = "curlhere.html"
user-agent = "superagent/1.0"

# and fetch another URL too
url = "curl.haxx.se/docs/manpage.html"
-O
referer = "http://nowhereatall.com/"
# --- End of example file ---
```

> 注意这个选项也可以使用多次，导入多个文件

**`--keepalive-time <seconds>`**  
这个选项指定了`保活`数据包发送的间隔时间，默认使用操作系统提供的`TCP_KEEPIDLE`和`TCP_KEEPINTVL`套接字选项值。如果指定`--no-keepalive`选项则这个选项无效

默认的超时时间是60秒，如果这个选项指定多次则最后一个生效

**`--key <key>`**  
`(SSL/SSH)`私钥文件名。可以让你将私钥放在单独的文件中并单独提供

**`--key-type <type>`**  
`(SSL)`指定私钥类型。支持`DER` `PEM` `ENG`，默认是`PEM`

**`--krb <level>`**  
`(FTP)`启用`Kerberos`认证。`level`必须提供，可以是`clear` `safe` `confidential` `private`，其他值都被认为是`private`

这个选项需要在编译时启用`kerberos4`或者`GSSAPI(GSS-Negotiate)`支持，这个可能在某些系统中并不支持，可以使用`-V, --version`选项来查看是否支持

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