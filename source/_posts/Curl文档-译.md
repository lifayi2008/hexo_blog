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

**`-l, --list-only`**  

**`-L, --location`**  
`(HTTP/HTTPS)`如果服务器报告请求的页面已经被移动到另外一个位置（使用HTTP头`Location:`和3xx响应码），使用这个选项可以让curl继续请求新的地址；如果和`-i, --include`或者`-I,  --head`选项一起使用，则会显示所有被请求页面的头信息。如果启用认证则curl只会将认证信息发送到初始的请求地址，可以使用`--location-trusted`选项改变这种行为。可以紧接着使用`--max-redirs`选项限制重定向的最大次数

如果curl最初发送的不是普通的`GET`请求（比如`POST`和`PUT`）则如果响应码是`301` `302` `303`，curl会使用`GET`方法发送接下来的请求；如果响应码是其他的3xx则接下来的请求仍旧使用原来的请求方法

**`--libcurl <file>`**  

**`--limit-rate <speed>`**  
指定curl可以使用的传输速度。这在你想限制curl使用的带宽时有用；`speed`默认是字节/秒，你可以增加一个后缀比如：`k K` `m M` `g G`来表示一个单位；给定的速度是整个传输过程中的平均速度，有可能在瞬时会超过这个速度

如果同时使用`-Y, --speed-limit`选项，that option will take precedence and might cripple the rate-limiting slightly, to help
              keeping the speed-limit logic working.

**`--local-port <num>[-num]`**  
设置发起连接所优先使用的端口号或者端口号范围

**`--location-trusted`**  
`(HTTP/HTTPS)`就像`-L, --location`选项一样，但是允许将认证信息发送给重定向后的服务器；注意这可能有安全的问题，在基本认证方法下用户名和密码都是明文传输

**`-m, --max-time <seconds>`**  
允许整个操作运行的最大秒数。这有助于防止批量的请求占用大量的带宽和CPU时间。从7.32.0版本开始可以支持小数值

**`--mail-auth <address>`**  
`(SMTP)`指定单个的地址。这个地址被用在请求转发邮件到其他服务器时的验证地址

**`--mail-from <address>`**  
`(SMTP)`指定邮件信封中的发件人地址

**`--mail-rcpt <address>`**  
`(SMTP)`指定单个地址，用户名或者邮件列表名。要发送邮件时，`recipient`应该是一个合法的地址。如果发送邮件时使用`VRFY`命令，则`recipient`使用用户名或者用户名+域名相同。如果使用邮件列表扩展（`EXPN`命令），则`recipient`应该指定一个邮件列表名

**`--max-filesize <bytes>`**  
指定允许下载的文件的最大字节数，如果请求的文件大小大于这个值，则curl不进行文件传输并返回63

> 注意：文件大小不总是在文件传输之前可以获得，所以在这种情况下这个选项不起作用；这个选项主要用于`FTP` `HTTP`

**`--max-redirs <num>`**  
用于限制`-L, --location`选项允许重定向时，最大的重定向次数。默认是50，设置为-1表示无限制

**`--metalink`**  

**`-n, --netrc`**  
让curl在用户家目录寻找并且读取`.netrc`文件中的登陆用户名和密码。典型的使用是在Unix系统的FTP服务。如果在HTTP中使用则curl会启用验证。文件的格式参考netrc(4)和ftp(1)。环境变量`HOME`的值被当作是家目录

文件的格式：

    machine host.domain.com login myself password secret
    # machine、login、password是固定的

**`--netrc-file`**  
指定`.netrc`文件的位置

**`--netrc-optional`**  
`-n, --netrc`为强制使用`.netrc`文件，而这个选项表示可选的使用

**`-N, --no-buffer`**  
禁用输出的缓存

**`--negotiate`**  
`(HTTP)`启用`GSS-Negotiate`认证。`GSS-Negotiate`认证方法是微软设计并且在其web框架中使用。主要是为了支持`Kerberos5`认证，但是也可以用于其他的认证方法

If you want to enable Negotiate for your proxy authentication, then use --proxy-negotiate.

这个选项需要libcurl库编译时启用GSSAPI支持，并且可能在很多系统中不被支持，可以使用`-V`选项来检查是否支持这个选项

要使用这个选项，必须提供一个假的user来恰当的激活认证过程，通常使用`-u :`

**`--no-keepalive`**  
禁用HTTP长连接，curl默认是启用的

**`--no-sessionid`**  
`(SSL)`禁用SSL session—ID缓存。By default all transfers are done using the cache. Note that while nothing
              should ever get hurt by attempting to reuse SSL session-IDs, there seem to be broken SSL implementations in the wild that may require
              you to disable this in order for you to succeed. (Added in 7.16.0)
              
**`--noproxy <no-proxy-list>`**  
指定的逗号分隔的主机列表不使用代理。`*`表示任意主机。注意，列表中指定的域名匹配这个域名本身，及这个域名所有的子域名和主机名

**`--ntlm`**  
`(HTTP)`启用NTLM认证。NTLM认证是由微软设计并且在IIS web服务器中使用。

**`-o, --output <file>`**  
将输出写入到文件而不是标准输出。如果你使用`{}` `[]`一次性获取多个文档数据，则可以在`file`中加上一个`#`并且后跟一个数字，这个数字指代`url`中的第`n`个`[]或者{}`，并且会被替换为实际的`{} []`中的字符串：

    curl http://{one,two}.site.com -o "file_#1.txt"
    # 1会被替换为 one 和 two
    
    curl http://{site,host}.host[1-5].com -o "#1_#2"
    # 1会被替换为 site host 而2会被替换为1 2 3 4 5
    
`file`同样可以使用`-`表示标准输出

**`-O, --remote-name`**  
同样将输出写入到文件，但是不需要指定文件名，而直接使用获取的资源名作为文件名。资源名通常是URL的一部分。文件会被写入到当前文件夹。如果URL中有URLEncode编码则curl不会进行解码

**`--oauth2-bearer`**  
`(IMAP,POP3,SMTP)`为OAUTH2.0验证服务指定Bearer Token

**`-p, --proxytunnel`**  
在使用HTTP proxy时`(-x, --proxy)`，这个选项让非HTTP协议通过这个HTTP代理通道发送其他协议请求。这个通道是利用HTTP代理的`CONNECT`请求，并且需要代理服务器允许重定向连接请求到远程的服务端口

**`-P, --ftp-port <address>`**  
`(FTP)`使用FTP的主动模式。`address`可以是下面的三种值：`interface` `IP address` `hostname`

禁用`PORT`命令可以使用`--ftp-pasv`。禁用`EPRT`命令可以使用`--disable-eprt`

从`7.19.5`版本开始可以在`IP address`后面加上`:[start]-[end]`来指定一个curl可以使用的端口范围，也可以指定一个单独的端口号，但是要注意这个端口没有被其他应用占用

**`--pass <phrase>`**  
`(SSL/SSH)`指定私钥的Passphrase（密码）

**`--post301 --post302 --post303`**  
`(HTTP)`按照RFC 2616/10.3.2的规定在301/302/303重定向后不将POST请求转换为GET请求。因为很多浏览器会做这样的转换，所以curl默认也做转换，使用这个选项可以阻止这种转换

这个选项只在使用`-L, --location`选项时才有意义

**`--proto <protocols>`**  
`protocols`是逗号分隔的协议列表（也可以是`all`）来指定curl依次尝试请求所使用的协议类型，可以使用下面的一个或者多个前缀：
```bash
+   除了协议列表允许的协议外，还包括这个协议（这是默认的前缀）
-   从允许的协议列表中禁用某个协议
=   只允许这个指定的协议（忽略已经允许的协议列表）
```

**`--proto-redir <protocols>`**  
让curl在重定向之后使用指定的协议列表。`protocols`同上面的参数

**`--proxy-anyauth`**
让curl在连接代理时自动选择一个合适的认证方式，这可能会导致curl发送多次请求

**`--proxy-basic --proxy-digest --proxy-negotiate --proxy-ntlm`**  
让curl在连接代理时使用`basic/digest/negotiage/NTLM`认证方式。使用`--basic --digest --negotiate --ntlm`选项可以让curl连接远程服务器时使用相应的认证方法

**`--proxy1.0 <proxyhost[:port]>`**  
使用指定的`HTTP 1.0`代理协议。如果未指定`port`则默认是1080

这个选项和`-x, --proxy`选项的区别是后者默认是用`HTTP 1.1`协议的`CONNECT`请求连接代理服务器

**`--pubkey <key>`**  
`(SSH)`指定公钥文件的位置

**`-q`**  
如果作为第一个选项。则`curlrc`不会被使用。`-K, --config`提供了查找默认配置文件的路径

**`-Q, --quote <command>`**  
`(FTP/SFTP)`发送一个命令到远程的`FTP` `SFTP`服务器。Quote命令会在数据传输之前发送（并且在连接到FTP服务器发送`PWD`命令之后）。要让命令在成功传输数据之后发送则需要在`command`之前加一个`-`字符。要让命令在curl改变当前工作目录之后发送可以在`command`之前加上`+`（只支持FTP）。你可以指定任意数量的`command`，如果这些command的执行有任何错误发生则停止执行其他的command。这些`command`必须是语义正确的（RFC959）或者是`SFTP`服务器支持的。这个选项可以使用多次，如果在前一个选项的`command`之前加上一个`*`则即使这个命令执行错误，下一个选项的`command`依旧会执行（默认会停止执行）

`SFTP`是一个二进制协议。不像`FTP`协议，curl会在发送命令到服务器之前解释这个命令。文件名可以使用shell风格的引用这样允许中间包含空格或者特殊字符。下面是`SFTP`支持的所有quote命令：
```bash
chgrp group file
chmod mode file
chown user file
ln source_file target_file
mkdir directory_name
pwd
rename source target
rm file
rmdir directory
symlink source_file target_file
```

**`-r, --range <range>`**  
`(HTTP/FTP/SFTP/FILE)`从`HTTP1.1` `FTP` `SFTP` `FILE`服务器获取一个字节范围的数据。`range`可以使用下面的表示方法
```bash
0-499     前500字节
500-999   指定第二个500字节
-500      指定最后500字节
9500-     从9500偏移量开始的字节
0-0,-1    制定第一个和最后一个字节 (*)(H)
500-700,600-799  指定从500偏移量开始的300个字节 (H)
100-199,500-599  制定两个范围的字节 (*)(H)

(*)表示服务器会返回多个响应
```
`range`中只能使用数字，如果发送非数字的范围。则根据服务器端的设置，返回不确定的结果；另外很多服务器可能未启用这个功能，所以当你想获取一部分数据时服务器给你发送了整个文档；`FTP/SFTP`只支持`start-stop`的表示方法

**`-R, --remote-time`**  
如果使用这个选项，则curl会尝试获取远程文件的时间，并且使用那个时间作为下载到本地文件的时间

**`--random-file <file>`**  
`(SSL)`指定一个包含随机数的文件。这个随机数会作为ssl引擎的随机数种子

**`--raw`**  
`(HTTP)`禁用curl的内容编码和传输编码

**`--remote-name-all`**  

**`--resolve <host:port:address>`**  
Provide a custom address for a specific host and port pair. Using this, you can make the curl requests(s) use a specified address and
              prevent  the  otherwise  normally  resolved  address to be used. Consider it a sort of /etc/hosts alternative provided on the command
              line. The port number should be the number used for the specific protocol the host will be  used  for.  It  means  you  need  several
              entries if you want to provide address for the same host but different ports.


**`--retry <num>`**  
指定错误（超时，FTP4xx相应码，HTTP5xx响应码）传输重试的次数。设置为0表示不重试，这也是默认值。

第一次重试时会等待一秒的时间，以后每次重试都会等待前一次双倍的时间，直到最大值为10分钟，以后每次重试都使用这个间隔时间。下一个选项可以指定固定的等待时间

**`--retry-delay <seconds>`**  
这个选项只有在`--retry <num>`选项使用时才有意义，表示指定一个固定的等待时间

**`--retry-max-time <seconds>`**  
指定`--retry <num>`选项会增加到的最大等待时间，因为是前一个数值的双倍所以实际时间有可能超过这个数值

**`-s, --silent`**  
静默模式。不显示进度信息或者错误信息，只显示用户请求的数据。可以使用重定向将请求数据输出到文本

**`--sasl-ir`**  
Enable initial response in SASL authentication.  (Added in 7.31.0)

**`-S, --show-error`**  
如果请求失败显示错误信息

**`--ssl`**  
`(FTP,  POP3,  IMAP,  SMTP)`尝试使用`SSL/TLS`连接服务器。如果服务器不支持`SSL/TLS`则使用非安全的连接。`--ftp-ssl-control` `--ssl-reqd`可以设置不同级别的加密请求

**`--ssl-reqd`**  
`(FTP,  POP3,  IMAP,  SMTP)`尝试使用`SSL/TLS`连接服务器。如果服务器不支持`SSL/TLS`则终止连接

**`--ssl-allow-beast`**  
(SSL)  This  option  tells  curl  to not work around a security flaw in the SSL3 and TLS1.0 protocols known as BEAST.  If this option
              isn't used, the SSL layer may use work-arounds known to cause interoperability problems with some older SSL implementations. WARNING:
              this option loosens the SSL security, and by using this flag you ask for exactly that.  (Added in 7.25.0)

**`--socks4 <host[:port]> --socks4a <host[:port]>`**  
使用指定的`socks4[a]`代理。如果端口未指定默认是1080。这个选项会覆盖掉`-x --proxy`选项的设置，这两个选项是互斥的

从7.21.7版本开始这个选项是多余的，因为你可以使用`-x --proxy`，并将选项参数指定为`socks4[a]://xxxxxxxx`的形式

**`--socks5-hostname <host[:port]> `**  
使用指定的`socks5`代理，并且让代理服务器解析主机名；其他的和上面的`socks4[a]`相同；使用`-x --proxy`选项时，选项参数应该是`socks5h://xxxxxxxx`的形式

**`--socks5 <host[:port]>`**  
使用指定的`socks5`代理，但是使用本地的服务器解析主机名；其他的和上面的`socks4[a]`相同；使用`-x --proxy`选项时，选项参数应该是`socks5://xxxxxxxx`的形式

这个选项和`--socks4`都不支持`ipv6` `FTPS` `LDAP`协议

**`--socks5-gssapi-service <servicename>`**  
**`--socks5-gssapi-nec`**  

**`--stderr <file>`**  
将所有标准错误输出的信息，输出到指定的文件`<file>`；如果文件名是`-`则表示标准输出

**`-t, --telnet-option <OPT=val>`**  
使用`telnet`协议连接服务器时传递一些参数。支持这些参数：

*   `TTYPE=<term>` 设置终端类型
*   `XDISPLOC=<X display>` 设置`X play`位置
*   `NEW_ENV=<var,val>` 设置环境变量

**`-T, --upload-file <file>`**  
将本地文件传输到指定的URL地址，文件名加引号。如果在URL中没有指定文件部分，则Curl将本地的文件追加在URL后。注意：如果不指定文件名那么URL中最后必须使用`/`结束，否则curl可能认为最后一部分是你要指定的远程文件名。如果用在`HTTP(S)`协议中，将会使用`PUT`请求方式

使用`"-"`（加引号）作为文件名表示从标准输入获取数据。使用`"."`（加引号）作为文件名表示以非阻塞的方式从标准输入中获取数据

可以同时制定多个`-T URL`组合来同时将不同的文件传送到不同的目标。`-T`也支持通配符模式，可以上传多个文件到同一个指定的地址：

    curl -T "{file1, file2}" http://www.uploadthisfiel.com
    curl -T "img[1-1000].png" ftp://ftp.picturemania.com/upload/

**`--tcp-nodelay`**  
启用`TCP_NODELAY`选项

**`--tftp-blksize <value>`**  
`(TFTP)`设置`TFTP BLKSIZE`选项。这是curl从TFPT服务器传输数据时使用的数据块大小，默认是512字节

**`--tlsauthtype <authtype>`**  
设置`TLS`认证类型。目前仅支持`SRP`。如果同时使用`--tlsuser --tlspassword`选项则这个选项默认被设置为`SRP`

**`--tlspassword <password>`**  
设置`--tlsauthtype`所指定的认证类型所需要的密码，这个选项要求`--tlsuser`选项也被指定

**`--tlsuser <user>`**  
设置`--tlsauthtype`所指定的认证类型所需要的用户，这个选项要求`--tlspassword`选项也被指定

**`--tlsv1.0 --tlsv1.1 --tlsv1.2`**  
`(SSL)`当和一个远程的`TLS`服务器连接时强制使用的`TLS`版本

**`--tr-encoding`**  
`(HTTP)`使用curl支持的压缩算法，来发送HTTP请求，获取相应的数据并解压缩文件

**`--trace <file>`**  
跟踪（显示）所有请求和响应的数据，包括描述性信息，将`<file>`指定为`"-"`表示将输出信息打印到标准输出；这个选项会覆盖之前的`-v --verbose --trace-ascii`选项设置

**`--trace-ascii <file>`**  
和`--trace`选项类似，但是只会显示`ASCII`字符，其他的数据不会显示，这样可以减少一些输出让人更容易获取到感兴趣的信息；这个选项会覆盖之前的`-v --verbose --trace-ascii`选项设置

**`--trace-time`**  
在每一条跟踪信息前面显示一个时间戳

**`-u, --user <user:password;options>`**  
指定一个用户名、密码和可选的登录选项用于服务器的验证。指定这个选项会忽略`-n,  --netrc`选项

如果只是指定一个用户名，则curl会提示你输入密码

如果你使用了启用`SSPI`的curl，并且进行`NTLM`验证，你可以使用`-u :`的形式强制curl从环境中选择一个用户名和密码或者指定一个登陆选项，比如：`-u ;auth=NTLM`

可以在选项中使用协议相关的验证选项。目前只有`IMAP` `POP3` `SMTP`支持使用登陆选项作为一部分登陆信息。更多的登陆选项可以查询`RFC 2384` `RFC 5092`

**`-U, --proxy-user <user:password>`**  
指定代理服务器验证时需要的用户名和密码

如果你使用了启用`SSPI`的curl，并且进行`NTLM`验证，你可以使用`-U :`的形式强制curl从环境中选择一个用户名和密码

**`--url <URL>`**  
指定一个URL。如果要在一个文件中读取参数时通常使用这个选项指定URL；这个选项可以被使用多次；To control where this URL is written, use the -o,  --output  or  the  -O,  --remote-name
              options.

**`-v, --verbose`**  
显示更详细的信息通常用于排错；以`>`开始的行，表示curl发送的头数据；以`<`开始的行表示收到的头数据（默认情况下这些数据不显示）；以`*`开始的行表示curl添加的信息

如果你只想显示`HTTP`头信息可以使用`-i, --include`选项

如果这个选项显示的信息仍然不够详细，可以使用`--trace` `--trace-ascii `选项

这个选项会覆盖之前的`--trace` `--trace-ascii `选项

**`-w, --write-out <format>`**  
指定成功完成一个操作后显示到终端的内容；`format`是一个包括普通字符和变量的字符串的混合，指定为`@filename`表示从文件读入格式，指定为`@-`表示从标准输入读入格式

输出格式中的变量会被curl使用实际的值代替，变量使用`${variable_name}`的形式（如果要表示`"%"`字符需要使用`%%`），也可以使用`\n` `\r` `\t`分别表示换行、回车、制表符

可用的变量如下：

```bash
content_type    请求文档的内容类型（Content-Type）

filename_effective  curl写输出的最终文件名；这仅在指定--remote-name --output选项时有意义；在和--remote-header-name选项合用时也很有用

ftp_entry_path      The initial path curl ended up in when logging on to the remote FTP server

http_code       上一次收到的http/ftp传输的状态码；等同于response_code

http_connect    上一次curl发送的CNNECT请求收到的响应码

local_ip        最近一次连接使用的本地IP地址
local_port      最近一次连接使用的本地端口地址
remote_ip       最近一次连接的远程IP地址
remote_port     最近一次连接的远程端口

num_connects    最近一次传输使用的连接数

num_redirects   一个请求被重定向的次数
redirect_url    当一个请求被允许进行重定向时（使用-L选项），实际重定向到的URL

size_download   总共下载的字节数
size_header     下载的头部信息的大小
size_request    http请求的头部字节数
size_upload     上传的总字节数

speed_download      完成下载后计算出的下载速度 字节/秒
speed_upload        完成上传后计算出的下载速度 字节/秒

ssl_verify_result   The  result  of  the  SSL  peer  certificate verification that was requested. 0 means the verification was successful.

time_appconnect     从发出curl命令到SSL/SSH/etc connect/handshake完成耗费的时间（秒）
time_connect        从发出curl命令到完成连接耗费的时间（秒）
time_namelookup     从发出curl命令到域名解析完成耗费的时间（秒）
time_pretransfer    从发出curl命令到准备开始传输文件所消耗的时间（time_appconnect时间+文件传输协商时间）
time_redirect       从发出curl命令到准备重定向，名称解析，文件传输协商，到最后实际准备开始传输消耗的事件
time_starttransfer      从发出curl命令到第一个字节准备开始传输消耗的时间
time_total          整个请求耗费的事件，微秒为单位
url_effective       实际响应请求的URL地址，这在允许curl进行重定向时有用
```

**`-x, --proxy <[protocol://][user:password@]proxyhost[:port]>`**  
使用特定的代理；尖括号中的代理实体可以使用`protocol://`前缀指定一个协议：`socks4://` `socks4a://` `socks5://` `socks5h://`，如果未使用协议前缀则默认是`http://`表示使用HTTP代理

如果端口未指定的话，默认使用1080

这个选项可以覆盖环境变量中配置的相关代理的设置；如果环境变量中有代理相关配置可以使用`""`来表示不使用代理

使用HTTP代理进行其他协议的数据传输都会被转换为HTTP协议，所以其他协议的某些操作可能会不用

**`-X, --request <command>`**  
`(HTTP)`指定一个请求方法（默认为GET）；通用的请求方法包括`PUT` `DELETE`等，基于HTTP的`WebDAV`协议还提供了`PROPFIND` `COPY` `MOVE`和其他的一些方法

通常不需要使用这个选项，因为请求方法会根据特定的选项而设置；这个选项仅仅更改了请求头信息中的字符串，而不会实际更改curl的行为；比如如果你想发送一个`HEAD`请求使用`-X HEAD`是无效的，需要使用`-I, --head`选项

(FTP) Specifies a custom FTP command to use instead of LIST when doing file lists with FTP.  
(POP3) Specifies a custom POP3 command to use instead of LIST or RETR. (Added in 7.26.0)  
(IMAP) Specifies a custom IMAP command to use insead of LIST. (Added in 7.30.0)  
(SMTP) Specifies a custom SMTP command to use instead of HELP or VRFY. (Added in 7.34.0)

**`--xattr`**  
将输出信息保存在文件中时，在扩展文件属性中记录一些数据的元信息

**`-y, --speed-time <time>`**  
**`-Y, --speed-limit <speed>`**  
如果在指定的时间（默认30秒）内，下载速度（默认是1k）小于指定的值则终止传输

**`-z, --time-cond <date expression>|<file>`**  
