1.1.1 http简介

http是一种用于计算机之间交流通信的规范，其传输的是文字、图片、音频、视频等超文本数据。client是http协议里的请求方，即User
Agent；server是 HTTP 协议里的应答方，如:Apache和Nginx;
CDN位于浏览器和服务器之间，主要起到缓存加速的作用；

https相当于\"HTTP+SSL/TLS+TCP/IP\"; http分层对应TCP/IP四层模型

http特点:
HTTP灵活可扩展，可以任意添加头字段实现任意功能；基于TCP/IP可靠传输协议;
是一种应用层协议，能够传输任意数据；使用了请求 - 应答模式；HTTP
本质上是无状态的，每个请求都是互相独立、毫无关联的，协议不要求客户端或服务器记录请求相关的信息。但是HTTP是明文传输，是不安全的，无法验证通信双方的身份，也不能判断报文是否被窜改;

1.1.2 http报文及请求

1\) http报文组成

起始行(start line): 描述请求或响应的基本信息；

头部字段集合(header): 使用 key-value
形式更详细地说明报文，头部和正文有空行；

消息正文(entity/body): 实际传输的数据，如:
纯文本、可以是图片、视频等二进制数据

2\) http报文内容

HTTP报文可以没有body，但必须要有header，而且header后也必须要有空行；

请求头由\"请求行+头部字段\"构成，响应头由\"状态行+头部字段\"构成；请求行有三部分:
请求方法，请求目标和版本号; 状态行也有三部分: 版本号,
状态码和原因字符串;

头部字段是key-value的形式，用\":\"分隔，不区分大小写，顺序任意，除了规定的标准头，也可以任意添加自定义字段，实现功能扩展；HTTP/1.1
里唯一要求必须提供的头字段是Host，它必须出现在请求头里，标记虚拟主机名。

3\) http请求方式:

  ----------------------------------- -----------------------------------
  GET 获取资源                        HEAD 获取资源的元信息

  POST 向资源提交数据, 写入或上传数据 PUT 类似 POST

  DELETE 删除资源                     CONNECT 建立特殊的连接隧道

  OPTIONS 列出可对资源实行的方法      TRACE 追踪请求 - 响应的传输路径
  ----------------------------------- -----------------------------------

4\) url: 是用来唯一标记服务器上资源的一个字符串，其结构如下:

scheme://user:passwd@host:port/path?query(附加要求)#fragment

注意: 在URI里对\"@&/\"等特殊字符和汉字必须要做编码

5\) 相应状态码

状态码在响应报文里表示了服务器对请求的处理结果，状态码后的原因短语是简单的文字描述，可以自定义，状态码是十进制的三位数，分为五类，从100到599：

  ----------------------------------- -----------------------------------
  2xx表示成功: 200、204、206等        3xx表示重定向: 301、302、304；

  4xx表示客户端错误: 400、403、404    5xx示服务器错误: 500、501、502、503
  ----------------------------------- -----------------------------------

1.1.3 http数据传输及编码

1\) http数据类型

  ----------------------------------- -----------------------------------
  text: html, plain(纯文本), css      image: gif, jpeg, png

  video: mpeg, mp4                    application(不固定格式): json, pdf,
                                      js

  Encoding type: gzip, deflate(zlib), 
  br                                  
  ----------------------------------- -----------------------------------

HTTP定义了两个头字段Accept和Content，客户端用Accept头告诉服务器希望接收什么样的数据,
服务器用Content头告诉客户端实际发送了什么样的数据。同时,
用\"q\"表示权重, 默认最大为1, 0表示拒绝。使用服务器lety表示接受结果。如:

  ------------------------------------------------------------------------------- -----------------------------------
  Accept: text/html,application/xml,image/png; q=0.9,\*/\*;q=0.8                  Content-Type: text/html

  Accept-Encoding: gzip, deflate, br                                              Content-Encoding: gzip

  Accept-Language: zh-CN, zh, en                                                  Content-Language: zh-CN

  Accept-Charset: gbk, utf-8                                                      Content-Type: text/html;
                                                                                  charset=utf-8

  Lety:                                                                           
  Accept-Encoding,User-Agent,Accept(表示服务器依据了Accept-Encoding、User-Agent   
  和 Accept 这三个头字段，然后决定了发回的响应报文。)                             
  ------------------------------------------------------------------------------- -----------------------------------

2\) 传输大文件

常用方法: 数据压缩(nginx中gzip on), 分块传输, 范围请求(只获取文件一部分,
服务器响应头Accept-Ranges: bytes表示支持)，多段数据

1.1.4 http连接管理

1\) http连接

短连接:
每次发送请求前需要先与服务器建立连接，收到响应报文后会立即关闭连接。

长连接: 连接后不断开,
一直到最后一个响应结束再断开连接。响应报文里有\"Connection:
keep-alive\"字段

注意: 长连接也需要在恰当的时间关闭，不能永远保持与服务器的连接,
客户端在请求头里加上\"Connection: close\"字段,
后nginx里设置keepalive_timeout和keepalive_requests

对首阻塞(Head-of-line blocking):
http报文是一个先进先出的\"串行队列\"，如果队首的请求处理的太慢将会造成堵塞。

2\) http连接行能优化

并发连接(concurrent connections), 域名分片(domain sharding)

3\) http重定向

\"重定向\"就是让浏览器跳转到新的url上，实际上发送了两次 HTTP
请求，第一个请求返回了302，然后第二个请求就被重定向到了\"/index.html\"。浏览器通过响应报文里的\"location\"字段来提取url，发出新的http请求。在\"Location\"里的
URI形式分为\"绝对
URI\"(包含scheme、host:port、path、query等)和\"相对URI\"(省略了scheme和host:port)。

4\) 重定向状态码

301称\"永久重定向\"(Moved
Permanently)，意思是原URI已经\"永久\"性地不存在了，今后的所有请求都必须改用新的URI。

302称\"临时重定向\"(Moved
Temporarily)，意思是原URI处于\"临时维护\"状态，新的URI是起\"顶包\"作用的\"临时工\"。其它状态码:
303, 307, 308

5\) 重定向应用场景

2 HTTP优化

2.1.1 cookie机制

1\) cookie工作过程

当用户通过浏览器第一次访问服务器的时候，服务器会创建一个独特的身份标识数据(\"key=value\")，然后放进Set-Cookie字段里,
随着响应报文一同发给浏览器。浏览器收到响应报文，就将保存Set-Cookie，在下次再请求的时候就自动把这个值放进Cookie字段里发给服务器。服务器通过cookie识别用户身份，并提供响应的服务。

2\) cookie属性

cookie属性就是防止cookie外泄或窃取的手段，如设置Cookie
的生存周期(Expires (过期时间)或 Max-Age(相对时间))，Cookie
的作用域(\"Domain\"和\"Path\")，Cookie 的安全性

3\) cookie应用

Cookie
最基本的一个用途就是身份识别，保存用户的登录信息，实现会话事务；广告跟踪

4\) 服务器缓存控制

缓存运行机制:
浏览器发现缓存无数据，于是发送请求，向服务器获取资源；服务器响应请求，返回资源，同时标记资源的有效期；浏览器缓存资源，等待下次重用。

缓存有效期通过头字段\"Cache-Control\"控制，如max-age=30(只保存30秒),
no_store(不允许缓存), no_cache(使用前验证是否过期),
must-revalidate(如果缓存不过期就可以继续使用，但过期了如果还想用就必须去服务器验证)。

5\) 客户端缓存控制

浏览器也可以发\"Cache-Control\"，如: max-age=0。

6\) 条件请求

HTTP 协议就定义了一系列\"If\"开头的\"条件请求\"(如:
if-Modified-Since和If-None-Match)字段，专门用来检查验证资源是否过期(如果没有过期，服务器就回应一个\"304
Not Modified\")，把两个请求才能完成的工作合并在一个请求里做。

注意:
需要第一次的响应报文预先提供\"Last-modified\"和\"ETag(实体标签，用来解决修改时间无法准确区分文件变化的问题)\"，然后第二次请求时就可以带上缓存里的原值，验证资源是否是最新的。

2.1.2 http代理服务

1\) 代理服务器

代理服务器(proxy
server)位于客户端和真实服务其之间，实现负载均衡、健康检查、安全防护、加密卸载、数据过滤、内容缓存等。

代理服务器通过\"Via\"字段标明代理身份。每当报文经过一个代理节点，代理服务器就会把自身的信息追加到字段的末尾，形成一个链表。

服务端为获取客户端ip一般使用X-Forwarded-For(记录客户端和代理服务器ip或域名)或X-Real-IP(只记录客户端ip)字段

2\) 缓存代理

HTTP
的服务器缓存功能主要由代理服务器来实现，目的是为了加快对变更较小热点资源访问。

源服务器的缓存控制:
一般通过\"private\"和\"public\"这两个属性来区分。如\"private,
max-age=5(客户端缓存时间),
must-revalidate(只要过期就必须回源服务器验证)\"，\"public, max-age=5,
s-maxage=10(代理服务器缓存时间),
proxy-revalidate(只要求代理的缓存过期后必须验证，客户端不必回源),
no-transform(不允许代理对缓存优化)\"

2.1.3 http 加密

1\) 加密方式

对称加密: 指加密和解密时使用同一个密钥, TLS对称加密算法AES(Advanced
Encryption Standard)、ChaCha20等。

加密分组模式: 让算法用固定长度的密钥加密任意长度的明文,
最新的分组模式被称为 AEAD(Authenticated Encryption with Associated
Data)，在加密的同时增加了认证的功能，常用: GCM、CCM 和 Poly1305。

非对称加密: 使用公钥(public key)和私钥(private key)进行单向加密和解密,
其算法有: RSA(整数分解)、ECC(椭圆曲线离散对数, 其子算法 ECDHE
用于密钥交换，ECDSA 用于数字签名,
目前比较常用的曲线是P-256和x25519)、DH、DSA

混合加密: ECDHE-ECDSA-CHACHA20-POLY1305 , ECDHE-ECDSA-AES128-GCM-SHA256
,
其中前两字段为密钥交换和签名使用，后面的为正式传输加密方法和分组，SHA256为摘要算法。

2\) 数字签名与证书

摘要算法(Digest Algorithm):
它加密后的数据无法解密，不能从摘要逆推出原文，可以验证密文的完整性。TLS用来生成伪随机数(PRF)的常用算法有SHA256、SHA384等。

完整性:
为了保证消息的完整性，需要在混合加密系统里用会话密钥加密消息和摘要，也叫哈希消息认证码(HMAC)。

数字签名:
用于确认客户端和服务端身份，由于两端都交换了对方的公钥，因此使用私钥加密摘要实现数字签名。

3\) 数字证书和CA(Certificate Authority，证书认证机构):

知名的 CA 有 DigiCert、VeriSign、Entrust、Let's Encrypt
等，它们签发的证书分 DV、OV、EV(可信度最高)三种。

4\) TLS协议

记录协议(Record Protocol)规定了TLS收发数据的基本单位:
记录(record)，所有的其他子协议都需要通过记录协议发出，多个记录数据可以在一个
TCP 包里一次性发出。

警报协议(Alert
Protocol)是向对方发出警报信息。收到警报后另一方可以选择继续，也可以立即终止连接。

握手协议(Handshake
Protocol)浏览器和服务器会在握手过程中协商TLS版本号、随机数、密码套件等信息，然后交换证书和密钥参数，最终双方协商得到会话密钥，用于后续的混合加密系统。

变更密码规范协议(Change Cipher Spec
Protocol)是一个\"通知\"，告诉对方后续的数据都将使用加密保护。在它之前，数据都是明文的。

5\) TS1.2 连接过程

ECDHE握手过程

RSA握手过程

双向认证

6\) TLS1.3 特性

最大化兼容性: 扩展协议(Extension
Protocol)通过在记录末尾添加一系列的\"扩展字段\"来增加新的功能，老版本的
TLS 不认识它可以直接忽略，这就实现了\"后向兼容\"。

强化安全: TLS1.3 里只有 5 个套件

提升行能: TLS1.3 压缩了\"Hello\"协商过程，删除了\"Key
Exchange\"消息，把握手时间减少到了\"1-RTT\"，效率提高了一倍。

注意: nginx要支持tls1.3，openssl版本必须是1.1.1以上(openssl version查看)

2.1.4 https优化

1\) 硬件优化

https连接计算是计算密集型，更快的CPU或用ssl加速服务器

2\) 软件优化

升级linux内核4.x, nginx1.16，openssl1.1.1

3\) 协议优化

尽量用TLS1.3，nginx选择加密算法：

ssl_protocols TLSv1.2 TLSv1.3;

ssl_session_timeout 5m;

ssl_session_tickets on; 打开\"Session Ticket\"会话复用

ssl_session_ticket_key ticket.key;

ssl_ciphers
\"ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-CHACHA20-POLY1305:ECDHE+AES128:!MD5:!SHA1\"；

ssl_prefer_server_ciphers on; (该参数在tlsv1.3中失效)

4\) 证书优化

服务器使用椭圆曲线(ECDSA)证书，证书验证使用\"OCSP
Stapling\"让服务器预先访问 CA 获取 OCSP 响应。

5\) 会话复用

其中一种方式是\"Session Ticket\"，服务器加密会话信息，用\"New Session
Ticket\"消息发给客户端，让客户端保存，重连的时候，客户端使用扩展\"session_ticket\"发送\"Ticket\"，服务器解密后验证有效期，就可以恢复会话，开始加密通信。

6\) 预共享密钥

原理和\"Session Ticket\"差不多，但在发送 Ticket
的同时会带上应用数据(Early
Data)，免去了1.2里的服务器确认步骤，这种方式叫\"Pre-shared
Key(PSK)\"。但可能会受到\"重放攻\"。

7\) 其它配置

配置http/2: listen 443 ssl http2;

配置推送服务(提前推给浏览器): http2_push /path/xxx.css;
http2_push_preload on;

配置压缩指令: gzip on;

8\) 传输链路优化: CDN(Content Delivery Network)

为解决\"长距离\"上网络访问速度慢而诞生的一种网络应用服务，将源站资源加载到CDN节点上，用户可以访问最近的CDN节点(edge
node)，从而实现加速。

全局负载均衡(Global Sever Load Balance,
GSLB)，主要的职责是当用户接入网络的时候在CDN专网中挑选出一个\"最佳\"节点提供服务，解决的是用户如何找到\"最近的\"边缘节点，对整个CDN网络进行\"负载均衡\"。

CDN概念:
\"命中\"是指用户访问的资源恰好在缓存系统里，可以直接返回给用户；\"回源\"则是缓存里没有，必须用代理的方式回源站取。因此，CDN应该是命中率越高越好，回源率越低越好。

9\) 服务器性能优化

衡量服务器性能的主要指标有三个: 吞吐量(requests per second,
RPS)、并发数(concurrency)和响应时间(time per request)。

服务器网站性能测试工具ab(apache bench): ab -c -n 1000
\'http://www.xxx.com\'

系统资源监控: top, vmstat 2, sar -n DEV 2(每两秒查看一次网卡流量)

10\) 客户端 HTTP 性能优化的关键就是：降低延迟。

11\) nginx配置优化

nginx实现动静分离，启用长连接TCP

server {

listen 80 deferred reuseport backlog=4096 fastopen=1024;

keepalive_timeout 60;

keepalive_requests 10000;

location \~\* \\.(png)\$ {

root /let/images/png/;

}

location \~\* \\.(php)\$ {

proxy_pass http://php_back_end;

}}

打开nginx中压缩功能如gzip或br。对于不能压缩的图片可以用JPEG(有损)和Webp(无损)格式，去除一些无用信息提高压缩率。

除非必要尽量不要使用外部重定向，尽量使用内部重定向。

开启客户端缓存功能，服务端可以使用redis等。

注意: 在HTTP/2里\"资源合并\"并不一定是最优选项,
一个TCP尽量使用一个域名。

二 HTML

2.1 结构元素

2.1.1 html简介

1\) html简介

html(HyperText Markup Language,
超文本标记语言)一种用于创建网页的标准标记语言。

2\) html文件结构

\<!DOCTYPE html\> //声明为html5文档

\<html lang=\"en\"\> // html根元素

\<head\> ... \</head\> //头部元素

\<body\> ... \</body\> //网页类容元素

\</html\>

注意: a) 标签推荐小写, 大部分标签都包含开始标签和结束标签

b\) 注释: \<!\-- content \--\>

2.1.2 头部元素

1\) 基本元素

\<!DOCTYPE html\> //声明文档类型

\<head\> //头部标签

\<title\> //标题标签, 由导航栏显示

2\) 元数据

\<meta charset=\"utf-8\"\> //设置字符集

\<meta name=\"description\" content=\"xx\"\> //元数据, 引擎检索, 其它:
author, http-equiv

3\) 其它元素

\<link rel=\"stylesheet\" href=\"xx/xx.css\" \[type=text/css\] /\>
//资源引用

\<script src=\"xx.js\" defer\>\</script\> //导入外部脚本

\<base href=\"http://xx.com/\" /\> //设置默认属性

\<style\>xx\</style\> //内部样式

2.1.3 文本元素

1\) 基本元素

  ----------------------------------- -----------------------------------
  \<body\> //文档主体                 \<h1\> to \<h6\> //标题, 大到小

  \<p\> //段落文本, 换行              \<hr /\>, \<br /\> //下划线 \| 换行
  ----------------------------------- -----------------------------------

2\) 文本样式

  ----------------------------------- -----------------------------------
  \<b\>, \<strong\> //粗体文字        \<i\>, \<em\> //斜体字

  \<u\>, \<del\>, \<ins\>             \<sub\>, \<sup\> //下标 \| 上标
  //下划\|删除\|插入线                

  \<abbr title=\"xx\"\> //缩写及提示  \<address\> //地址(斜体)

  \<blockquote cite=\"http://xx\"\>   \<q cite=\"http://xx.com\"\>
  块引用                              //内联引用

  特殊字符: \< - &lt; \> - &gt; \" -  
  &quot; \' - &apos; & - &amp; \"     
  \" - &nbsp                          
  ----------------------------------- -----------------------------------

3\) 计算机文本

  ----------------------------------- -----------------------------------
  \<pre\> //原文本, 计算机文本父元素  \<code\>, \<samp\> //代码,
                                      代码输出样本

  \<var\>, \<kbd\> //标记变量 \|      
  键盘输入                            
  ----------------------------------- -----------------------------------

注意: a) 在html段落中所有连续的空格或空行都会被算作一个空格。

2.1.4 文档元素

1\) 文档结构元素

\<header\>, \<main\>, \<footer\> //文档头部 \| 主体 \| 脚注

\<nav\>, \<aside\> //文档导航栏 \| 侧边栏

2\) 文章元素

\<article\>, \<section\> //文章声明 \| 文章内容组合

2.1.4 列表表格

1\) 列表

普通列表: \<ol\>, \<ul\>, \<li\> //列表表头, 有序表头 \| 无序表头 \|
列元素

描述列表: \<dl\>, \<dt\>, \<dd\> //表头(包装器) \| 列表元素 \|
每个元素描述

2\) 表格

\<table\>, \<tr\>, \<th\>, \<td\> //表格块 \| 表头 \| 表格行 \| 表格单元

\<colgroup\> \<col style=\"x\" /\> ... \</colgroup\> //表格分组 \|
分组的列属性

\<caption\>, \<thead\>, \<tbody\>, \<tfoot\> //标题行 \| 表头 \|
主体表格 \| 表位行(总结行)

2.1.5 链接元素

1\) 超链接

\<a href=\"https://xx.com/\" title=\"xx\"\> xx \</a\> //超链接

其它属性:

href=\"/path/#my_id\" \| \"mailto:x@qq.com\" \| \"tel:+123\"
//跳转到id处 \| 发邮件xx \| 拨号123

download=\"x.x\" \| rel=\"external\|next\" //下载文件名 \| 链接类型,
额外资源, 下一个资源

target=\"\_self\|\_blank\|\_parent\|\_top\" //如何展示该url, 当前 \|
新标签页 \| 父\|最顶层浏览器

2\) 位置跳转

\<a name=\"test\"\>xx\</a\>; \<a href=\"/xx#test\"\>xx\</a\>
//跳转到锚位置

\<a id=\"test\"\>xx\</a\>; \<a href=\"/x#test\"\>xx\</a\>
//跳转到指定id位置

3\) 引用

\<link rel=\"xx\" href=\"xx\" /\> //引用资源, 见2.1.2

2.2 嵌入元素

2.2.1 布局元素

1\) 基本元素

\<div\>, \<span\> //区块 \| 内联块, 属于非语义包装器, (p,
h1等是语义包装器)

\<iframe\> //内嵌块, 其具有单独的语义, 注意一定要有sandbox属性

\<embed\>, \<object\> //嵌入外部资源

2.2.2 图像画布

1\) 光栅图像

\<img src=\"/x/xx.jpg\" alt=\"text xx\" title=\"xx\" width=\"10\"
height=\"20\" /\> //引用外部图片

\<figure\>, \<figcaption\> //图片容器(img父) \| 容器标题(img同级)

\<map\> \<area shape=\"x\" coords=\"2, 2\" alt=\"x\" href=\"x\"
/\>\</map\> //图像可点击区域

2\) 矢量图像

\<svg xmlns=\"http://x/svg\"\> \<rect width=\"2\" height=\"1\"
fill=\"blue\" /\> \</svg\> //画矢量图

3\) 响应式图片

\<img srcset=\"x-480w.jpg 480w, x-800w.jpg 800w\" sizes=\"(max-width:
600px) 480px, 800px\" src=\"x-800w.jpg\" alt=\"xx\"\>
//根据屏幕宽度加载不同尺寸图片

\<picture\> \<source media=\"(min-width: 80px)\" srcset=\"x-400w.jpg\"\>
\<img src=\"x-800w.jpg\" alt=\"xx\"\> \</picture\>
//根据屏幕尺寸调整加载图片, 推荐

4\) canvas使用

\<canvas width=\"1\" height=\"2\"\> //创建一个画布

通过js作图

2.2.3 视频音频

1\) 基本元素

\<video controls\> \<source src=\"xx\" type=\"video/mp4\" /\> ...
\</video\> //引用视频资源

\<audio controls\> \<source src=\"xx\" type=\"audio/ogg\" /\> ...
\</audio\> //音频播放

\<track kind=\"subtitles\" src=\"xx.vtt\" srclang=\"es\"
label=\"Spanish\" /\> //视频字幕

2.3 表单元素

2.3.1 表单结构

1\) 基本表单元素

\<form action=\"/xx\" method=\"post\" \>...\</form\> //表单元素,
表单空间父元素

\<lable for=\"my_id\"\> //标签, 注意每个表单控件都应该有一个标签,
或父级或同级

\<input id=\"my_id\" type=\"text\|email\|...\" name=\"my_name\" /\>
//字符串输入控件

\<textarea if=\"my_id\" name=\"my_name\"\> //文本输入控件

\<button type=\"submit\|reset\" \[alt=\"x\" src=\"/x/x.png\"\]\>
//按钮控件, 用于提交或重置表单

\<fieldset\>, \<legend\> //表单框 \| 框标题

2.3.2 input属性

1\) type属性

text \| number \| password \| email \| tel \| url \| file \| image
//输入类型

checkbox(多选) \| radio(单选) \| range \| color //选择类型, 可配合列表

date \| datetime-local \| month \| week \| time //时间相关类型

hidden \| button \| reset \| search \| submit //其它相关类型

2\) 其它属性

id, name, class, title, autofocus //标识属性

list=\"text \| email \| tel \| number \| time \| ...\" //列表属性

maxlength \| minlength \| max \| min \| size //范围属性

multiple, accept //多列属性, 如文件等 \| 接收文件类型

placeholder \| value \| readonly \| required \| disabled //提示 \|
默认值 \| 只读 \| 必须 \| 无操作

pattern=\"xx\" //匹配模式, 用于输入验证

2.3.3 选择控件

1\) 下拉控件

\<select id=\"x\" name=\"x\"\>, \<optgroup\>, \<option\> //下拉控件 \|
下拉分组 \| 下拉元素

select属性: multiple(多选) \| autofocus \| disabled \| required

2\) 输入选择控件

\<datalist id=\"my_id\"\>, \<option\> //输入列表 \| 选项, 在\<input ...
list=\"my_id\" /\>

3\) 其它控件

\<progress\>, \<meter\> //进度条控件 \| 仪表盘控件

\<output\> //结果计算控件

2.3.4 发送数据

1\) form属性

name, id, class, rel //标识属性

action=\"/xx\" //数据提交路径

enctype=\"multipart/form-data\" //发送数据编码格式

method=\"get\|post\|dialog\" //发送方式

target=\"\_self\|\_blank\|\_parent\|\_top\" //如何显示响应

2.3.5 废除特性

\<acronym\>; \<applet\>; \<basefont\>; \<bgsound\>; \<big\>; \<blink\>;
\<center\>; \<content\>;

\<dir\>; \<font\>; \<frame\>; \<frameset\>; \<hgroup\>; \<image\>;
\<isindex\>; \<keygen\>; \<listing\>;

\<marquee\>; \<menuitem\>; \<multicol\>; \<nextid\>; \<nobr\>;
\<noembed\>; \<noframes\>;

\<plaintext\>; \<rb\>; \<rtc\>; \<shadow\>; \<spacer\>; \<strike\>;
\<tt\>; \<xmp\>

三 CSS

3.1 css基础

3.1.1 css简介

css(Cascading Style Sheets)指层叠样式表, 样式定义如何显示HTML元素,
样式通常存储在样式表中, 外部样式表通常存储在CSS文件中,
多个样式定义可层叠为一个。

1\) 语法

css语法由选择器和属性键值对组成, 如: selector { property:
property_value; ... }

css注释: /\* ...... \*/

2\) html中插入样式表

外部样式表(External style sheet):

\<head\>\<link rel=\"stylesheet\" href=\"mystyle.css\"
\[type=\"text/css\"\]\>\</head\>

内部样式表(Internal style sheet):

\<head\>\<style\>....\</style\>\</head\>

内联样式(Inline style):

\<p style=\"....\"\>sytly\</p\>

多重样式表优先级: 内联样式 \> 内部样式 \> 外部样式 \> 浏览器默认样式

注意: a) css设置时属性顺序很重要

3.1.2 选择器

要在html元素中使用css需要设置选择器, 元素的属性是从最近的规则中获取。

1\) 元素选择器

\* { ... } //所有元素

p { ... } //html中的\<p\>元素

h1, h2, h3 { ... } //html中的h1, h2, h3元素

2\) id选择器

#my_id { .... } //在html用使用\<p id=\"my_id\"\>, 全局唯一

3\) class选择器

.my_class { ... } //使用\<h2 class=\"my_class\"\>, 可重复使用

4\) 嵌套选择器

p.my_class { ... } //只能在\<p class=\" my_class \"\>使用

.my_class p{ ... } //在class=\" my_class \"内的p元素

#my_id h1, h2 { ... } //在id=\"my_id\"内的h1, h2元素

5\) 组合选择器

div p { ... } //所有子元素

div\>p { ... } //直接子元素, 只降一级

div+p { ... } //相邻的同级元素

div\~p { ... } //所有同级元素

6\) 属性选择器

a\[class\] { ... } //所有具有class属性的a元素

li\[class=\|\~=\"a\"\] { ... } //class属性等于\|包含值\"a\"的li元素

li\[class\^=\|\$=\|\*=\"a\"\] { ... }
//class属性以\"a\"开头\|结尾\|匹配的li元素

7\) 属性继承

.my_id a { color: inherit(继承父属性) \| initial(设置为初始值) \|
unset(设置为自然值) }

.my_id { all: unset } //重置所有属性

8\) 导入属性

\@import \'/path/xx.css\'; //导入css文件

3.1.3 伪类

1\) 伪类 pseudo-classes

根据元素所处状态而触发的属性。

语法: selector.class:pseudo-classe { ... } 或 :pseudo-classe { ... }

2\) 文本伪类

:dir(ltr \| rtl) { ... } //根据文本方向选择的元素

:lang(en \| zh \| dr \| \...) { ... } //根据语言来选择元素

3\) 链接伪类

:link \| :visited \| :any-link { ... } //链接未访问 \| 已访问 \|
两者都匹配的元素

:target \| :target-within { ... } //匹配链接目标元素 \| 链接目标内元素

:local-link \| :scope { ... } //同一页面链接 \| 选择器匹配的参考点的元素

4\) 用户操作伪类

:hover \| :active { ... } //鼠标指向该元素 \| 点击该元素

:focus \| :focus-visible \| :focus-within { ... } //焦点元素 \|
鼠标和键盘焦点 \| 焦点内元素

5\) input伪类 //并不仅限于\<input\>

:autofill \| :enabled \| :disabled { ... } //自动填充 \| 允许输入 \|
禁止输入

:read-only \| :read-write { ... } //只读 \| 读写

:placeholder-shown \| :default { ... } //有占位(提示) \| 有默认值的元素

:checked \| :indeterminate { ... } //选择框被选中 \| 不确定时的元素

:valid \| :invalid \| :blank { ... } //输入有效 \| 无效 \| 为空的元素

:in-range \| :out-of-range { ... } //输入在指定范围 \| 超出范围的元素

:required \| :optional { ... } //必须输入 \| 可选输入的元素

6\) 结构伪类

:root \| :empty { ... } //文档根 \| 除空格外无子元素

:first-child \| :last-child { ... } //同级相同的第一个 \| 最后一个元素,
注意要有多个元素

:noly-child { ... } //在某块内唯一的元素

:nth-child(2n+1\|2n\|...) {...} //选择同级相同元素, 2n+1为奇数号元素,
2n为偶数号元素

:nth-last-child(2n+1\|2n\|...) { ... } //同级同规则同元素的最后一个,
其它同级也计数

:first\|last\|only-of-type { ... } //同级同类型第一个 \| 最后一个 \|
唯一类型的元素

:nth-\[last\]-of-type(2n+1\|2n\|...) {...} //同级同类型同规则元素,
其它同级不计数

n\) 其它伪类

:current(p, span) \| :past(p, span) \| :future(p, span) { ... } //当前
\| 过去 \| 未来

:playing \| :paused { ... } //媒体播放 \| 暂停

3.1.4 伪元素

1\) 伪元素pseudo-elements

对选择元素特定部分进行操作, 一般配合伪类使用

语法: selector::pseudo-element { ... } //实例: p::first-line { color:
blue; }

2\) 伪元素的元素

::before \| ::after { ... } //当前元素的第一个子元素前 \|
最后一个子元素后, 操作默认内联

::first-letter \| ::first-line { ... } //当前元素子元素首字母 \|
子元素第一行

::marker { ... } \| ::placeholder //列表标签 \| 占位符格式

::selection { ... } //鼠标选中元素

::file-selector-button { ... } //文件上传按钮

3.2 css样式

3.2.1 盒模型

1\) box类型

display: block(块) \| flex(柔性) \| inline-block(内联块) \| grid(网格)
//类型

2\) box尺寸

固定尺寸:

margin, margin-top\|right\|bottom\|left //外边界宽度

padding, padding-top\|right\|bottom\|left //内边界宽度

width, height: 120px //context宽和高

逻辑尺寸: 解决文本溢出, 推荐

inline-size, block-size: 120px //文本框, 对应width, height

margin-block-start\|end, margin-inline-start\|end //外边界,
对应margin-top\|bottom\|left\|right

padding-block-start\|end, padding-inline-start\|end //内边界,
对应padding-top\|bottom\|left\|right

3\) box边框

border, border-top\|right\|bottom\|left: 1px solid black //边框属性

border-width: 1px; border-style: solid\|dashed; border-color: black
//边框线宽\|格式\|颜色

border-radius: 10px; border-top\|bottom-right\|left: 1em 5em //边框圆角

border-block-start\|end, border-inline-start\|end //逻辑边框,
对应border-top\|bottom\|left\|right

border-block-start-color\|style\|width //逻辑边框属性, 其边类似

4\) box背景

background-color: #567895 \| rgba(255, 255, 255, .2) \| black //背景颜色

background-image: url(/path/xx.png) \| //背景图片

background-repeat: no-repeat\|repeat-x\|repeat-y\|repeat
//背景图平铺行为

background-size: cover\|contain\|2px 1em //背景图大小

background-position: 20px 10% \| top center \| 20px top //背景图位置

background-attachment: scroll\|fixed\|local
//背景图片滚动\|固定\|局部固定

background: linear-gradient(...) \| radial-gradient(...) \| ...
//渐变背景

5\) box文本方向及溢出

writing-mode: horizontal-tb \| vertical-rl \| vertical-lr; //排列方向

overflow: hidden \| scroll \| auto; //隐藏 \| 滚动 \| 自动调节

注意: a) 每个元素都有自己的盒属性

b\) 长度单位: px (1/96in); 字体单位: rem \| em; 百分比: %

3.2.2 文本

1\) 字体样式

color: red; //字体颜色

font-family: arial; //字体

font-style: normal \| italic \| oblique; \\\\斜体格式, 正常\|斜体

font-weight: normal \| lighter, bolder \| 100-900; \\\\粗体格式

text-transform: none \| uppercase \| lowercase \| capitalize \|
full-width; \\\\大小写转换

text-decoration: none \| underline \|overline \| line-through;
//划线格式

text-decoration-line\|style\|color: xx; //划线具体格式

text-shadow: 4px 4px 5px red; //字体阴影

2\) 字体布局

text-align: left\|right\|center\|justify; //对齐方式, 左\|右\|中\|分散

line-height: 1.6; //行高, 推荐1.5-2

letter-spacing \| word-spacing: 4px; //字母间距 \| 词间距

text-indent: 10%; //第一行文本缩进

其它属性见mdn

3.2.3 表格列表

1\) 基本属性

同3.2.1盒模型, 3.2.2文本

2\) 列表样式

list-style-type: upper-roman \| upper-alpha \| circle \| ...; //列表标记

list-style-position: inside \| outside; //列表标记位置

list-style-image: url(\"/path/ss.svg\"); //列表标记图

3\) 列表标记值

start=\"4\" \[reversed\] ; //数字标记起始位置 \[倒序\]

value=\"2\"; //设置每行具体值

4\) 表格布局

table-layout: auto \| fixed; //表格布局, 自动调节尺寸 \| 固定尺寸

border-collapse: collaps \| separate; //相邻单元格边框共享 \| 不共享

3.2.4 动态转换

1\) 基本元素

transform-origin: center \| top left \| 50px 50px; //指定原点位置

transform: rotate(0.5turn) \| scale(2, 0.5) \| ...; //转换方式, 总特性

transition-property: all \| margin-righy, color \| ...; //转换过渡效果

transition-duration \| delay: 0.5s; //转换持续时间 \| 延时时间

transition-timing-function: ease \| steps(3, jump-start); //转换路径函数

perspective: none \| 800px \| 23rem \| ...; //3D透视深度

2\) 动画

\@keyframes expName { //动画样式

from { key: val; ... }; to { key: val; ... } //模式1, 设置开头结尾

0% { ... }; 25% { ... }; 50% { ... }; //模式2, 设置各阶段

}

animation: expName 5s linear 2s infinite alternate; //动画运行简写属性

animation-name: expName; //引用动画样式

animation-duration \| delay: 4s; //动画持续时间 \| 延迟时间

animation-iteration-count: 3 \| infinite; //动画运行3次 \| 持续运行

animation-direction: normal \| reverse \| alternate \|
alternate-reverse; //运行方向

animation-timing-function: ease \| linear ...; //运行曲线

animation-fill-mode: none \| forwards \| backwards \| both;
//动画不运行样式

3.2.5 其它

1\) 图片

object-fit: cover \| contain \| fill //图片尺寸, 覆盖 \| 填充 \|
调整尺寸填充

2\) 链接

cursor: pointer \| wait \| not-allowed \| grab \| ... //光标显示属性

3.3 css布局

3.3.1 柔性盒子

display: flex; //声明为柔性盒子

flex-direction: row \| row-reverse \| column \| column-reverse;
//项目排列, 行 \| 列

flex-wrap: nowrap \| wrap \| wrap-reverse; //项目强制放一行 \| 放多行 \|
多行反转

flex: 1 \| 1 200px \| auto; //盒子尺寸比例, 平分 \|
每一个盒子至少200px剩下平分

align-items: center \| start \| end \| ...; //项目水平排列方式

justify-content: center \| start \| end \| ...; //项目垂直排列方式

order: -1 \| 2 \| ...; //列表中项目放置位置

3.3.2 网格布局

display: grid; //使用网格布局

grid-template-columns: 2fr 1fr 1fr \| repeat(3, 1fr); //网格模板尺寸,
fr比例单位

grid-auto-rows\|colums: auto \| 1 fr \| minmax(10px, auto); //隐式网格,
超出部分

grid-gap \| row-gap \| column-gap: 10px; //网格间隙

grid-column \| rows: auto \| 1 / 3 \| span 2; //基于线位置的布局,
span跨列或行

grid-column-start\|end, grid-row-start\|end: auto \| 3 \| ...;
//具体线位置布局

grid-template-areas: \"a b\" \"a b c\" \"a b c\";
//根据网格区域名直接排列, 注意这里是换行写

grid-area: a \| b \| c; //网格区域名

3.3.3 浮动布局

float: left \| right \| inline-start\|end; //背景浮动, 类似环绕

clear: left \| right \| both \| inline-start\|end; //清理浮动,
类似不允许环绕

overflow: visible \| hidden \| scroll \| auto; //浮动框内容溢出处理

display: flow-root; //元素生成一个块元素框解决溢出

3.3.4 位置布局

position: static \| relative \| absolute \| fixed \| sticky; //位置属性,
静态位置 \| 相对于自身正常位置定位 \| 相对于父元素位置定位 \| 固定位置
\| 粘性位置(跟着滑轮移动)

z-index: auto \| 3; //当元素位置重叠时, z轴方向的位置

3.3.5 多列布局

column-count: auto \| 3; //列数量

column-width: 200px; //列宽度

column-gap: 20px; //列间隙

column-rule \| column-rule-width\|style\|color: 4px dotted rgb(79, 185,
227); //列间隙规则

column-span: none \| all; //跨列属性

break-inside: auto \| avoid \| avoid-page \| column ; //页面,
列或区域分隔符生成框如何展现

3.3.6 其它布局

1\) 媒体查询

\@media screen and (min-width: 1200px) { //当屏幕宽度小于1200px的布局

h1 { font-size: 4rem; } //其它属性: max-width \| width

}

\@media print { ... } //打印页面的布局方式

\@media (orientation: landscape) { ... } //根据方向布局

\@media (hover: hover) { ... } //鼠标悬停布局

\@media not all and (orientation: landscape) { ... } //逻辑判断

四 JavaScript

4.1 js基础

4.1.1 js简介

1\) js简介

javascript是一种轻量级的脚本语言，主要用于web编程,
其官方标准名称是ECMA-262。

4.1.2 js语法

1\) js用法

在html中使用: \<script \> ... \</script\> 或 \<script
src=\"xx.js\"\>\</script\>

通常会将js内容放在html的\<head\>或底部或单独的外部文件,
避免干扰html内容。使用外部js需要通过src属性指明位置。

2\) js语法

js注释: // 单行注释

/\* 多行注释 \*/

3\) js输出数据方式:

window.alert(); //弹出警告框

document.write(); //内容写入html文档中, 若在文档加载后执行,
输出会覆盖html页面。

document.getElementById(\'name\').innerHTML;
//写入到属性id为\'name\'的HTML元素中

console.log(); //写入到浏览器的控制台, 用于调试

4.2 变量及数据类型

4.2.1 变量, 常量

1\) 数据类型(8种)

值类型: string, number, bigint, boolean, null, undefined, symbol

引用类型: object

2\) js变量声明

js变量名由字母、数字和_组成, 且只能以字母开头

let x, y; //声明变量, 推荐,

let x = \'yes\', y = \'no\'; //声明变量并给定值

var x, y; var x = 4, name = \"xx\"; //声明变量, 老式

let x = new String(\'x\'); //x已经不是原始类型, 而是object, 不推荐

注意: a) let变量只能声明一次, let比var更严谨, var没有块级作用域,
let没有变量提升。

3\) js常量声明

const pi = 3.14 //const是块级作用域, 不能重复定义, 也不能修改, 推荐大写

4\) 类型转换

typeof x \| typeof(x) //返回变量或值的数据类型

x.constructor //返回变量的构造函数

String(x) \| x.toString() //将变量x转换为string类型

Number(x) \| +x; //将x转换为number类型, 转换失败结果为NaN

Boolean(x) //将x转换为布尔值

5\) 无值类型

null, undefined //可用来清空对象, undefined是所有没有赋值变量的默认值,
null主动释放引用变量(用来释放内存), 这两个值相等类型不相等

4.2.2 运算符

算术运算: +, -, \*, /, %, ++, \--, \*\* //注意浮点运算的精度问题

赋值运算: =, +=, -=, \*=, /=, %=, x++(返回当前值), ++x(返回计算后值),
x\--, \--x

比较运算: ==(值相等), ===(值和类型都相等), !=(值不等),
!==(值或类型至少一个不等)，

\>, \>=, \<, \<= //注意在进行比较时先检查是否为null或undefined

逻辑运算: !, &&, \|\| //注意优先级

&, \|, \^, \~, \<\<, \>\>

条件运算: let name = (condition)?value1:value2 //实例: let txt = (n \>
2)?\"a\":\"b\"; n\>2为a

空值合并运算: let name = a ?? b //如果a不是null或undefined, 则返回a,
否则返回b。

4.2.3 string

1\) string定义

let x = \'chi\', y = new String(\"chi\"); //定义字符串 \|
定义字符串对象, 注意: x !== y

let z = \`result \${x}\`; //可将\${...}结果嵌入到字符串中,
也用于定义多行字符串

let first = x\[2\], test = x + \"yeye\"; //访问单个字符 \| 拼接字符串

转义字符: \\\', \\\", \\\\, \\n, \\r, \\t, \\b(退格), \\f(换页)

2\) string属性

x.constructor \| x.prototype //字符串创建函数 \| 添加属性和方法

x.length //字符串长度

3\) string方法

x.toString() \| x.valueOf(); //字符串化 \| 返回原始值

x.search(rgx) \| x.match(rgx) \| x.matchAll(rgx); //匹配下标 \| 结构集
\| 结构集对象

x.indexOf(str\[, pos\]) \| x.lastIndexOf(str); //str第一次 \|
最后一次出现位置, 无为-1

x.includes(str) \| x.startsWith(str)\|endsWith(str); //str是否在x中 \|
x是否已str开头或结尾

x.slice(n, m) \| x.substring(n, m) \| x.charAt(n) \| x.substr(n, m);
//切片 \| 获取n处\|从n起m个

x.splie(rgx) \| x.trim(); //分割x \| 去除x两端空格

x.concat(str, ...) \| x.repeat(n); //拼接str到x \| 重复n次x

x.replace(old, new) \| x.toLowerCase()\|toUpperCase(); //替换 \|
转换大小写

x.charCodeAr(n) \| x.fromCharCode(); //字符转换

x.localeCompare(str); //比较x于str, 小于\<0, 大于\>0, 等于0

4\) html包装方法

x.anchor() \| x.link(); //创建锚 \| 显示为链接

x.big() \| x.small() \| x.bold() \| x.italics() \| x.fixed(); //大号 \|
小号 \| 粗体 \| 斜体 \| 打印文本

x.fontcolor() \| x.fontsize() \| x.sup() \| x.sub(); //指定颜色 \| 大小
\| 上标 \| 下标

x.strike() \| x.blink(); //删除线 \| 闪动

4.2.4 number

1\) number定义:

let num = 3.14; //js数字均为64位, 无不同类型的数字(浮点数, 整数等)

let big = 124n; // BigInt类型, 用于存储大于2^53^-1的数, 转换: BigInt(a)

2\) 正无穷: Infinity, 负无穷: -Infinity // number整数精度为15位,
小数最大位数为17位

3\) NaN表示计算错误, 用于指示某个值不是数字。它不等于任何东西,
包括它自己;

4\) 数字方法

num.toString(base); //将num转换为对应进制字符串, base=2, 8, 10, 16, 32

num.toFixed(n); //将num转换为精度为n的字符串

isNaN(val) \| isFinite(val); //val转换为数字后是否为NaN \| 有效数字

parseInt(val) \| parseFloat(val); //解析val中的整数 \| 浮点数部分

parseInt(str, base); //解析base进制的str为整数

4.2.5 array

1\) 定义数组: let a = \[\'a\', { name: \'John\' }, function() { ... }
\]; //数组是特殊的对象

let a = new Array(); let a = new Array{\'a\', \'b\', \'c\'}; //不推荐,
没了内部优化

注意: a) 不要使用\'==\'比较数组, 而是用循环比较; 对复制数组修改的影响。

b\) 删除元素: delete a\[n\]; //删除第n个元素

2\) array属性: a.constructor; //返回创建数组对象的原型函数

a.length; //数组长度, a.length = 0用于清空数组

3\) array方法

a.push(\"x\") \| a.pop(); //数组尾部添加元素(返回长度) \|
移除一个尾部元素, LIFO

a.unshift(\"x\") \| a.shift(); //数组头部添加元素(返回长度) \|
移除一个头部元素

a.splice(n \[, m, e1, e2, ...); //从n开始删除m个元素, 并插入e1, e2, ...

a.slice(n, m) \| a.concat(b, ...); //输出切片 \| 拼接数组

a.join(\"x\") \| str.split(\'x\'); //用x符连接a元素为string \|
将str以x分割为数组

a.indexOf(v, m) \| a.lastIndexOf(v, m) \| a.includes(v, m);
//从m开始检索v值

a.find(function(item, idx, array)) \| arr.findIndex(function(i, idx, a))
\| a.filter(function(i, idx, a)); //使用指定方法查找, 返回元素 \| 索引
\| 匹配数组, item元素本身, idx索引, array数组本身

a.map((i, idx, a) =\> {...}) \| a.forEach((i, idx, a) =\> {\...});
//遍历处理并返回数据 \| 更改a

a.reduce(function(pre, item, idx, array){...}, \[init\]) \|
a.reduceRight(...); //类似forEach, 同时关联上一个处理值, 左至右处理 \|
右至左处理

a.sort((x, y) =\> {...}) \| a.reverse(); //指定方法排序 \| 反转数组

a.isArray(); //是否为数组

4\) 解构赋值

let \[name, age = \"8\", , ...rest\] = \[\"chi\", \"18\", \"x\", \"y\",
\"z\"\]; //空为忽略, ...rest分配剩余数组

let { var1=\'z\', key2: var2 } = { key1: \"x\", key2: \"y\" };
//对象解构, 将key2赋给var2

\[a, b\] = \[b, a\]; //交换变量

使用: for ( let \[key, val\] of Object.entries(myobj)) { ... }

5\) 类数组对象

let a = { 0: \'test\', 1: \'test\', length: 2 };
//具有数字索引和length属性为类数组对象

let arr = Array.from(a, \[mapFn, thisArg\]); //类数组对象转换为数组

4.2.6 Map, Set

1\) Map

map定义: let m = new Map(); //是一个带键的数据集合对象,
允许任何类型的key

let m = new Map(\[\[key1, val1\], \[key2, val2\]\]);
//存储的是有序数组集合

let m = new Map(Object.entries(obj)); //将一个对象转换为map

let obj = Object.fromEntries(m); //将一个map转换为一个对象

map方法:

m.set(key, value) \| m.delete(key) \| m.clear(); //存储 \| 删除 \| 清空

m.get(key) \| m.has(key); //获取 \| 是否有指定键

m.keys() \| m.values() \| m.entries() \| m.size; //键 \| 值 \| 所有键值
\| 元素个数

m.forEach((v, k, m) =\> {...}); //遍历处理

2\) Set

set定义: let s = new Set(); //值的集合(没有键), 没有重复值

let s = new Set(\[\"val1\", \"val2\"\]);

set方法:

s.add(v) \| a.delete(v) \| a.clear(); //添加 \| 删除 \| 清空

s.has(v) \| s.forEach(v, v, s); //是否有v值 \| 遍历处理s

s.values() \| s.keys() \| s.entries() \| s.size; //所有v, 三个类似 \|
元素个数

3\) 其它map, set

WeakMap是类似于Map的集合, 它仅允许对象作为键,
并且一旦通过其他方式无法访问它们, 便会将它们与其关联值一同删除。创建:
let m = new WeakMap()

WeakSet是类似于Set的集合, 它仅存储对象,
并且一旦通过其他方式无法访问它们, 便会将其删除。创建: let s = new
WeakSet()

4.2.7 date, math

1\) date

a\) 创建时间: let d = new Date(); //创建一个当前时间对象

let d = new Date(dateStr); //解析时间字符串或时间戳(1970至今)

let d = new Date(Y, M \[, D, h, m, s, millis\]); //解析时间, Date(2021,
5, 24)

let s = Date.now() \| Date.parse(str) //获取 \| 解析时间串为时间戳

b\) date方法

d.getTime()\|getFullYear()\|getMonth()\|getDate();
//获取时间戳(1970至今) \|年 \|月 \|日 \|天

d.getDay()\|getHours()\|getMinutes()\|getSeconds()\|getMilliseconds();
//获取星期\|时\|分\|秒\|毫

d.setTime()\|setFullYear()\|setMonth()\|setDate();
//设置时间(1970起)\|年\|月\|日

d.setHours()\|setMinutes()\|setSeconds()\|setMilliseconds();
//设置小时\|分钟\|秒\|毫秒

d.getTimezoneOffset() \| toUTCString(); //与utc时间差\|转utc时间

2\) math

Math对象用于执行数学任务，注意没有构造函数Math()

方法:

Math.flool(n) \| cell(n) \| round(n) \| trunc(n); //舍人向下 \| 向上 \|
向最近 \| 移除小数点后

Math.random() \| max\|min(n, m, ...) \| pow(n, m); //随机数\[0,1) \|
最大\|最小 \| m的n次幂

4.2.8 正则表达式

1\) 定义正则

let reg = new RegExp(\"pattern\", \"mode\"); //pattern匹配模式,
mode修饰符

let reg = /pattern/mode; //短语法

2\) mode修饰符

i忽略大小写, g全局匹配, s严格匹配(转义失效)

m多行匹配, y粘滞模式, u完整unicode

3\) 匹配模式

见shell

4\) 方法: reg.test(str); //str中存在为true

reg.exec(str); //str中存在输出匹配结果, 不存在输出null

string方法: str.search(reg); 和str.match\|matchAll(reg)

4.3 逻辑处理

4.3.1 流程控制

1\) 条件语句

+----------------------------------+-----------------------------------+
| if语句: if (condition1) {        | switch语句: switch (n) {          |
|                                  |                                   |
| ......                           | case n1: //严格相等===            |
|                                  |                                   |
| } else if (condition2) {         | ......; break;                    |
|                                  |                                   |
| ......                           | case n2: //无break会穿透          |
|                                  |                                   |
| } else {                         | case n3: //n2, n3组合             |
|                                  |                                   |
| ......                           | ......; break;                    |
|                                  |                                   |
| }                                | default:                          |
|                                  |                                   |
| if短语句: if (condition)         | ......                            |
| command;                         |                                   |
|                                  | }                                 |
+----------------------------------+-----------------------------------+

2\) for循环语句

+----------------------------------+-----------------------------------+
| for (let i = 0, j = 1; i \< 5;   | for (let k in obj) { ... }        |
| i++) {                           | //遍历对象                        |
|                                  |                                   |
| ......                           | for (let v of str) { ... }        |
|                                  | //遍历array\|string k索引, v是值  |
| }                                |                                   |
+----------------------------------+-----------------------------------+

3\) while循环

+----------------------------------+-----------------------------------+
| while (condition) {              | do {                              |
|                                  |                                   |
| ...... //先判断条件再执行        | ......                            |
|                                  |                                   |
| }                                | } while (condition)               |
|                                  | //先执行再判断条件                |
+----------------------------------+-----------------------------------+

4\) 循环控制

break \[label\] //跳出循环或标签处, 标签需要提前声明( label: )

continue \[label\] //跳出本次迭代, 进入下一个迭代

4.3.2 函数

1\) 函数

函数定义: function test(arg1, arg2 = 10) { //js函数参数没有类型检查

.... //函数具体逻辑

return x, y //退出函数, 并返回值。空值或无return返回undefined

}

调用函数: test(val1, val2)

注意: a) js变量解析是由内到外的, 因此在函数内重新声明全局变量,
可避免影响到函数外

b\) 函数传参是值复制过程, 且函数可接收任意数量参数,
通过arguments获取传入参数值, 如: arguments\[3\],
arguments.length获取传入参数的个数

变长参数: function test(arg1, ...args) { ... }; 传入数组 \| 对象:
test(...val) \| test({...val})

c\) 箭头函数: let sum = (a, b) =\> a + b; //箭头函数没有this, arguments,
super

d\) 在js中函数是对象, 其属性: test.name返回函数名,
test.length返回形参个数。方法: call(), apply(), bind(), 示例:

let res = test.call(obj \[, args\]); //将test中的this替换为obj

let res = test.apply(obj, args); //类似call, 用于传递多个参数, 如数组等

let res = test.bind(obj\[, arg, ...\]); //用于绑定this,
处理对象方法中this丢失的情况

e\) 命名函数表达式(NFE，Named Function Expression)

let say = function name(n) { ... ; name(\"ha\") }; //name仅函数内部可见

f\) javascript:void(func()); //计算一个表达式, 但不返回值

2\) 闭包

let add = function test(arg) { ...; return function() { return ... }; };

调用: let test = add(\'val\'); test()

3\) 严格模式(现代模式)

\'use strict\'; //声明使用严格模式, 在脚本开头或函数内开始时声明,
不然不生效

严格模式限制:

a\) 不能使用未声明变量, 同一个块级下不能重复声明变量

b\) 不能删除不允许删除变量、对象、函数和属性

c\) 不能对只读属性赋值, 不能对getter方法读取的属性进行赋值

d\) 禁止this关键字指向全局对象。

e\) 在代码块内声明的变量、函数等, 在代码块外不可见。

4\) 内建函数

eval(\'alert(\"hello\")\'); //允许执行一个代码字符串, 不推荐

4.3.3 异步编程

1\) 异步调用

对于需要长时间运行的任务可以调用子线程处理, 避免主线程阻塞。

异步语法: setTimeout(test, t, arg1, ...) //等待t毫秒后执行test函数,
arg是向test传参

setInterval(test, t, arg1, ...) //每隔t毫秒执行一次test函数, 不推荐

clearTimeout(timerId) \| clearInterval(timerId) //取消异步执行

示例: // function print() { alert(\'test\') }; let timerId =
setInterval(print, 2000) //每2秒执行

let timerId = setTimeout(function print() {

alert(\'test\'); timerId = setTimout(print, 2000)

}, 2000) //每隔2秒执行一次, 推荐, 时间精度更准确

setTimeout(() =\> { clearTimeout(timerId); alert(\'stop\') }, 5000)
//5秒后执行停止

注意: 子线程是与主线程没有通信的, 无法确定其结束时间。与go不同,
js主线程结束并不会影响子线程运行

2\) promise链

//处理多次调用异步函数的情况

new Promise(function (resolve, reject){ //resolve正常时函数,
reject错误时函数

setTimeout(function () { ... }, time); //也可以是其它事务, 如:

if (condition) { reject(new Error(\'val\')) //抛出错误,
只能在起始函数块用

} else { resolve(\'val\') }; //向下传递参数, 只能在起始函数块用

}).then(function (arg) { //resolve正常时函数, 可以有多个then

setTimeout(function()) { ... }, time);

return \'val\' 或 throw \'val\'; //结束then,
并向下个then传递参数或抛出错误

}).catch(function(err) { //reject错误和throw时处理函数

console.log(err);

}).finally(function() { //注意promise是从上到下依次运行的

......;

})

注意: a) promise构造器和promise链区别

let test = new Promise(function(resolve, reject) { ... }); //向下传递

test.then((result) =\> {...}, (error) =\> {...}); //不会向下传递,
result, error都是来自test

类似于发布订阅模式, test发布者, then为接收者

3\) Promise API

并行执行: Promise.all(\[ //并行执行多个promise

new Promise(...), new Promise(...), ... //执行多个Promise

\]).then(...).catch(...) //处理结果, 任意一个出错, 所有Promise都会reject

并行执行: Promise.allSettled(\[ //出错的Promise不影响其它Promise的运行

new Promise(...), new Promise(...), ... //返回: {status: \'fulfilled\',
value: result}

\]).then(...).catch(...) //错误返回: {status: \'rejected\', reason:
error}

4\) 异步函数(async function)

语法: async function sayncFunc() { //总是隐式返回一个promise

await new Promise(function(resolve, reject) {setTimeout(resolve,
3000)});

await new Promise(...);

let result = await new Thenable(val); //支持then的对象

}

调用: asycnFunc()

注意: a) await不能用在顶层代码和普通函数中, 只能在async中使用

b\) await是按顺序依次运行的, 有一个在运行时,
其它值包装在一个resolved的promise

c\) 支持.then的对象都可以使用await, 如: class Thenable { ...;
then(resolve, reject) {...}}

d\) class中的async, 如: class Waiter { async wait() { return await
Promise(...) }}

5\) 异步可迭代对象:

语法: let range = {

from: 1, to: 5,

\[Symbol.asyncIterator\]() { //实现可迭代方法

return {

current: this.from, last: this.to,

async next() {

await new Promise(resolve =\> setTimeout(resolve, 1000)); // (3)

if (this.current \<= this.last) return { done: false, value:
this.current++ };

else return { done: true };

}}};}};

调用: (async () =\> { for await (let value of range) { alert(value);
}})()\\

6\) Generator函数

语法: function\* genSeq() { //用来生成数据流

yield 1; yield 2; yield\* genSeq2(n, m) //字段值

let ask = yield \"2 \* 2 = ?\" //接收一个外部值

return 3; //可以没有return

}

调用: let gen = genSeq(); //返回一个generator object对象

使用: let res = gen.next(); //返回一个对象{value: 1, done: false\|true},
return使done为true。

let question = gen.next().value; gen.next(4); //将结果传递到generator中

迭代: for (let val of gen) { ... } //迭代不会显示return的值

7\) 异步generator

语法: async function\* generateSequence(start, end) {

for (let i = start; i \<= end; i++) {

await new Promise(resolve =\> setTimeout(resolve, 1000));

yield i;

}}

调用: (async () =\> {

let generator = generateSequence(1, 5);

for await (let value of generator) { alert(value); }

})();

4.3.4 对象

1\) 对象

定义对象: let user = {

key: value, //对象属性

\[variable\]: value2, //属性名从variable变量中得到, 计算属性

method: function(var1, var2 ) { ...; return this.key, y } //对象方法

method(vars) { ... } //简写对象方法

};

构造器: function Person(name, age) {

this.name = name; ...; this.test = function(arg1, arg2) { ... ; return
x, this.name };

} // let user = new Person(\'chi\', 18); 构造器函数只能由new执行

调用: user.key \| user\[key\]; //如果key不存在, 返回undefined

user.method(val1, val2);

获取: Object.keys(user) \| Object.values(user) \| Object.entries(user);
//键 \| 值 \| 键值对

方法: \"key\" in user; \| delete user.key; //判断 \| 删除

Object.fromEntries(user); //遍历处理对象

合并对象: let clone = Object.assign({}, user) //复制对象,
复杂对象需要通过循环复制

Object.assign(dst, src1\[, src2, ...\]) //合并多个对象到dst,
相同属性会被覆盖

可选链: ?. //判断未声明字段

user?.Name?.First; //防止user.Name.First中有一个不存在就报错问题

user.test?.(); user.?\[variable\]?.Last; delete user?.Name; //应用

注意: a) 对象是引用类型, 如: let a = user, 修改a也会改变user值

b\) 创建空对象: let user = new Object(); 或let user = {};

c\) 不能用可选链写入数据: user?.name = \"test\";

d\) 访问器属性: let obj = { //获取或设置对象函数是就像常规属性一样,
示例:

key: \'val1\',

get test() { ... }, //访问obj.test时, getters起作用

set test(arg) { ... } //执行obj.test = \'val2\'时, setters起作用

};

e\) 属性标志: Object.getOwnPropertyDescriptor(user, \'key\');
//获取属性标志

Object.defineProperty(user, \'key\', {\"value\": \"John\", \"writable\":
true, \"enumerable\": true, \"configurable\": true}); //设置属性标志:
值, 可写, 可被循环, 可删除

对象限制: Object.preventExtensions(obj); //禁止向对象添加新属性。

Object.seal(obj); //禁止添加/删除属性, configurable: false

Object.freeze(obj); //禁止添加/删除/更改属性, configurable, writable:
false

Object.isExtensible(obj); //允许添加属性, 返回true

Object.isSealed(obj) \| Object.isFrozen(obj); //满足seal \| freeze,
返回true

f\) js对象都会从prototype(原型对象)中继承属性和方法,
可以使用prototype属性给对象的构造函数添加新的属性, 如:

person.prototype.sex = \'man\'

person.prototype.say = function() { ... }

g\) 使用person.prototype = { ... } 定义新属性会重写原型, 谨慎使用

3\) this

this表示对当前对象的引用, 其指向谁取决于执行时最后一次调用它的对象。

a\) 方法中, this表示该方法所属的对象, 如: method: function() { return
this.key }

b\) 单独使用, this指向全局对象, 如: let x = this

c\) 函数的严格模式下, 在没有对象的情况下调用: this == undefined,
看\'.\'前有没有。

d\) 在事件中, this表示接收事件的元素

e\) this绑定的对象, 用apply和call切换函数执行的上下文环境(context), 如:

let person1 = { fullName: function() { return this.first + \" \" +
this.last }}

let person2 = { first:\"John\", last: \"Doe\" }

调用: person1.fullName.call(person2);

f\) 链式调用

let ladder = { step:0, up() { ...; return this; }, down() {...; return
this; }};

ladder.up().up().down().up()

3\) Symbol

\"Symbol\"值表示唯一的标识符, 对象的属性key只能是string类型或Symbol类型

对象Symbol:

let id = Symbol(\"num\"); //id是symbol的实例化对象, \"num\"是Symbol名,
也可省略。

id.description; //获取symbol名

let user = {

name: \"test\",

\[id\]: 1, //user.id 属性会被隐藏, 访问数据: user\[id\]

};

全局Symbol: //全局Symbol注册表中注册了所有全局Symbol

let id = Symbol.for(\"num\") //通过Symbol名获取Symbol, 不存在就创建

Symbol.keyFor(id) //获取全局Symbol名

注意: a) 非全局Symbol即使名字相同它们也不相等, 全局Symbol名字相同就相等

b\) js中大多数值都支持字符串隐式转换, 但Symbol不会自动转换

c\) 对象中隐藏的属性不会被第三方访问到或修改, 提升安全性

d\) Symbol属性在for..in循环中会被跳过

e\) Object.keys(user) 也会忽略Symbol属性

let clone = Object.assign({}, user) //Symbol是会被复制到clone中的

4\) 对象与原始值转换

js对象转换方法: obj\[Symbol.toPrimitive\](hint), obj.toString(),
obj.valueOf()

a\) obj\[Symbol.toPrimitive\](hint)

let user = {

name: \"tom\", // hint: 当一个对象被用在需要原始值的上下文中时(计算,
alter()等),

age: 18, //对象会被转换为原始值。其有三种类型: string, number,
default(不确定)

\[Symbol.toPrimitive\](hint) { return hint == \"string\" ? \`name:
\"\${this.name}\"\` : this.age; },

};

b\) toString(), valueOf()

let user = {

name: \"tom\",

age: 18,

toString() { return \`name: \"\${this.name}\"\` }, //对于hint =
\"string\"

valueOf() { return this.age }, //对于hint = \"number\"或\"default\"

};

5\) 可迭代对象

let range = { //可用for (let n of range) { ... }进行循环处理

from: 1, to: 5, //迭代范围

\*\[Symbol.iterator\]() { for(let v = this.from; v \<= this.to; v++) {
yield v; }} //构造迭代对象

\*\[Symbol.asyncIterator\]() { //异步可迭代对象

for(let v = this.from; v \<= this.to; v++) {

await new Promis(resolve =\> setTimeout(resolve, 1000)); yield v

}}} //使用异步迭代: (async () =\> {for await (let v of range) { ...
}})();

4.3.5 继承, 类, 模块

1\) 原型继承(Prototypal inheritance)

普通继承: let a = { x: true };

let b = { y: true, ­\_\_proto\_\_ = a }; //a就是b的原型, b具有了x属性:
b.x

let b = Object.create(a, { y: true }); //以a为原型创建对象, 推荐

new继承: function b(y) { this.y = y } ; b.prototype = a; //构造器属性

let c = new b(true) //继承只在new时生效

注意: a) Object.getPrototypeOf(b) 获取对象b的原型

Object.setPrototypeOf(b, a) 将对象b的原型修改为a

b\) 所有对象都有一个隐藏属性\[\[Prototype\]\],
继承至原生原型Object.prototype

c\) 所有写/删操作都是在对象上进行的, 不会影响到原型,
且this始终指向对象。

d\) 继承中相同的属性, 会以自身属性为准。

e\) 每个构造器都有一个隐藏的constructor属性, 其指向构造器自己

f\) 其它方法:

Object.keys(obj)/Object.values(obj)/Object.entries(obj) 返回自身的键,
值, 键值对

Object.getOwnPropertySymbols(obj) 返回一个由自身symbol类型的键组成的数组

Object.getOwnPropertyNames(obj) 返回一个由自身所有键组成的数组

Reflect.ownKeys(obj) 返回一个由自身所有键组成的数组

obj.hasOwnProperty(key) 若obj拥有名为key的自身的属性(非继承), 则返回true

2\) 类

class语法: class A { //是一个语法糖, 类似于构造器, 类总是使用\'use
strict\',

constructor(arg1) { this.key1 = arg1; ... } //构造器, 初始化对象属性

method1() { this.key1 = val; ... } //创建对象方法, 可以有多个method

name = \"test\"; //类字段, 通常将箭头函数方法绑定到对象, 解决this丢失

} // let a = new A(\'vals\'); 创建对象

class构造器: function A(args) { //用于动态创建不同类

... ; return class { constructor(arg) { ... }; method() { ... }; ... };

} //let a = new A(\'vals\'); let b = new a(\'val\'); //类表达式创建对象

类检查: obj instanceof Class //检查obj是属于Class, 如a instanceof A
返回true

class继承: class B extends A { //extends后可以是任意表达式, 如构造器等

constructor(arg1, arg2) {

super(arg1); //给父类属性传递参数

this.key2 = arg2; //添加自己的属性

}

method2() { ... } //定义自己的方法, 也可以重写方法

method1() { super.method1(); ... } //super.method调用父类同名方法

name = \'aa\'; //在子类中重写父类的类字段是不生效的, 类字段以父类为准

}

静态语法: class A { //只属于类构造器, 而不属于任何特定对象(类实例)

static method() { alert(this === A); } //静态属性

static akey = \"test\"; //静态属性, 静态属性和方法是可继承的

}

私有属性: class A { //只能在类构造器的内部使用

\_prikey = \'val\'; //这不是真的私有, 只是一个约定, 外部依然可以访问

#limitKey = \'val\'; //真正的私有属性, 外部使用需通过setters和getters

get limitKey() { return this.#limitKey; } //外部访问

set limitKey(arg) { this.#limitKey = arg; } //外部更改

#limitMethod(val) { ... } //私有方法, 且子类不能访问父类的私有属性

}

注意: a) 类包含prototype, getters, setters属性, constructor指向其自身

b\) 方法内部的HomeObject属性记住了它们的类/对象,
将一个带有super的方法从一个对象复制到另一个对象是不安全的。箭头函数没有super,
可解决意外super错误。

c\) js不支持多重继承, 但可用方法拷贝来实现: Object.assign(A.prototype,
mixin)

let mixin = { a() { ... }, b(arg) { ... } };

let eventMixin = { //事件处理的mixin

on(eventName, handler) { ... } //订阅事件

off(eventName, handler) { ... } //取消订阅

trigger(eventName, \...args) { ... } //生成具有给定名称和数据的事件

}

3\) 模块

需要在package.json中加入字段: "type": "module",

命名导出: export function Test(args) { ... } //导出内容可以是变量, 函数,
类

export {A, B} from \'./path/xx.js\'; //重新导出, 用于统一管理模块

导入模块: import {A as a, Test} from \'./auth/index.js\';
//明确导入内容, 推荐

import \* as mol from \'./path/xx.js\'; //所有内容导入mol对象中,
mol.Test(val)

默认导出: export default class User { ... }; export default function(x)
{ xx };

export { A as default }; //每个文件只有一个默认导出, 可用于导出无名内容

import User from \'/user.js\'; //导入不需要{}

注意: a) 模块导入html中要声明\<script type=\"module\"\>,
不同文件的相同导入只执行一次。

b\) js模块在html加载完后执行, 使用内联脚本: \<script async
type=\"module\"\>提前执行

c\) 模块属性import.meta包含当前模块的信息, 如url等

d\) 动态导入: let {A, B} = await import(\'./xx.js\'); //调用A(), B(),
动态导入在{...}可生效

let mol = await import(\'./xx.js\'); //所有模块导入mol中: mol.A(),
mol.B()

e\) 生产环境通常使用webpack等工具将模块打包发布

4.3.6 错误处理及调试

1\) 错误处理语法: try { ... 运行代码 } catch(err) { //捕获错误

... //err.name(错误名), err.message(错误描述), err.stack(当前栈)

} finally { ... } //最后处理

2\) 抛出错误: throw new Error(\'xx\'); //抛出内建错误, 其它:
SyntaxError, ReferenceError

3\) 自定义错误: class myError extends Error { //继承至内建错误

constructor(mess) { super(mess); this.name = \"myError\"; ...}
//构造内容

} //throw new myError(\'xxx\'); 抛出自定义错误

4\) 调试: console.log(xx); //打印运行结果

设置断点: a) 浏览器 \-- sources \-- Breakpoints \-- 点击行数字, 设置断点

b\) 在代码中添加关键词: debugger; //推荐

4.4 文档和事件

4.4.1 文档

1\) DOM(Document Object Model, 文档对象模型)

document对象是页面的主要\"入口点\", js通过它来创建动态的HTML

节点导航: //重量级

顶层节点: document.documentElement \| body \| head; //对应\<html\> \|
\<body\> \| \<head\>

子节点: document.body.childNodes; //所有子节点, 是一个集合

document.body.firstChild \| lastChild; //第一个和最后一个子节点

父节点: document.body.parentNode; //父节点

兄弟节点: document.head.nextSibling \| previousSibling; //下\|上一个节点

元素导航: //顶层元素同上

子元素: document.documentElement.children; //所有子元素

document.documentElement.firstElementChild \| lastElementChild;

父元素: document.documentElement.parentElement;

兄弟元素: document.documentElement.previousElementSibling \|
nextElementSibling;

2\) 查找html元素

id查找: let elem = document.getElementById(\'name\'); //若有多个相同id,
只返回一个

css选择: let elems = document.querySelectorAll(css); //返回匹配集合,
使用css选择器

let elem = document.querySelector(css); //匹配第一个元素, css = \'.name
\> li\'

let elem = document.closest(css); //返回最近的祖先元素

let ok = document.matches(css); //是否有匹配的元素, 返回bool

3\) js操作html

修改: elem.innerHTML = \"\<p\>xx\</p\>\"; //将elem内所有元素替换为指定值

elem.outerHTML = \"\<p\>xx\</p\>\"; //将elem替换为指定值

elem.textContent = \"\<p\>xx\</p\>\"; //将elem内元素用指定文本格式替换

创建: let node = document.createElement(\'div\');
//创建一个元素节点\<div\>

let text = document.createTextNode(\'xxxx\'); //创建文本节点

node.className = \"aa\" \| node.innerHTML = \"xx\"; //设置类名 \|
填充内容

插入: elem.append \| prepend \| before \| after(node); //在elem的末尾
\|开头 \|前面 \|后面

elem.replaceWith(node); //用node替换

elem.insertAdjacentHTML/Text/Element(\'afterbegin(开头) \|
beforeend(末尾) \| beforebegin(前) \| afterend(后)\', node);
//另一中插入node或html方法

其它: let node = elem.cloneNode(true); //克隆节点

elem.remove(); //删除elem

elem.type \| id \| value \| href \| hiden \| ...; //修改元素属性,
另一种属性操作方式如下:

elem.hasAttribute(name) \| getAttribute(n) \| removeAttribute(n) \|
setAttribute(name, val);

4\) js操作样式和类

样式: elem.style.width \| top \| display \| ... = \'new\';
//更改样式属性

elem.style.cssText = \`color: red; ...\`; //重写所有样式

getComputedStyle(elem, \[pseudo\]).color \| margin \| ...;
//读取样式属性值

类: elem.className; //获取类名

elem.classList.add \| remove(\'name\'); //添加 \| 移除类名

elem.classList.contains \| toggle(\'name\'); //类是否存在 \|
类不存在添加, 存在移除

元素大小: elem.offsetParent \| offsetWidth/Height \| clientTop/Left \|
clientWidth/Height;

4.4.2 事件

1\) js事件

事件是某事发生的信号, 可用于处理表单验证、用户输入、用户行为及浏览器动作

html中添加事件属性: \<html-elemnt event=\"test()\"\>

2\) 监听事件

语法: x.addEventListener(event, handler\[, options\]); //添加事件

x.removeEventListener(event, handler\[, options\]); //移除处理程序

注意: a) event 事件名; handler 处理程序; options 可选对象, 值有{once:
true\|alse} 触发后是否删除监听, {capture: false\|true} 冒泡或捕获,
{passive: true\|false} 是否调用preventDefault()

b\) capture: 冒泡(false)是内部元素事件先触发, 默认;
捕获(true)是外部元素事件先触发

c\) 当事件发生时,
详细信息被放入event对象中，并将其作为参数传递给handler,
handler中可直接访问event属性: event.target 引发事件的层级最深的元素,
event.currentTarget (=this) 处理事件的当前元素, event.eventPhase
当前阶段(capturing=1, target=2, bubbling=3)

d\) 事件委托:
在处理程序中获取event.target以查看事件实际发生的位置并进行处理

e\) 阻止浏览器行为: \<html-elemnt event=\" event.preventDefault()\"\>

3\) 自定义事件

语法: let event = new Event(type\[, options\]); //其它构造器: UIEvent,
MouseEvent等

调用: x.dispatchEvent(event); //在对应元素上运用, 要先添加监听事件

注意: a) type事件类型, 如\"click\"; options值: {bubbles:
false\|true}不会\|会冒泡, {cancelable: false\|true} 不会\|会阻止默认行为

b\) 要使event.preventDefault()生效, 需要设置{cancelable: true}

4.4.3 UI事件

1\) 鼠标事件

  ------------------------------------- ---------------------------------
  onclick 用户点击html元素              click 鼠标左键触发事件

  mousemove 鼠标在元素上移动触发        contextmenu 鼠标右键触发

  dblclick 双击时触发                   oncopy 复制时触发

  mouseover/mouseout                    
  鼠标从一个元素上移入/移出,            
  两元素相互影响                        

  mouseenter/mouseleave                 
  鼠标从一个元素上移入/移出,            
  两元素不相互影响                      

  mousedown/mouseup                     
  元素上点击/释放鼠标按钮               
  ------------------------------------- ---------------------------------

鼠标事件属性: event.button = 0(左)\|1(中)\|2(右)\|3(X1后退)\|4(X2前进)
//左右键

event.shiftKey\|altKey\|ctrlKey\|metaKey\|cmdKey(Mac) //组合键

event.clientX\|Y //相对于窗口坐标; event.pageX\|Y //相对于文档坐标

2\) 指针事件(触摸屏)

  ------------------------------------- ---------------------------------
  pointerdown\|pointerup 点击\|释放     pointermove 移动

  pointerover\|pointerout 移进\|移出    pointerenter\|pointerleave
                                        移进\|移出

  pointercancel 活跃的指针终止时触发    gotpointercapture 指针捕获时触发

  lostpointercapture                    
  指针捕获被释放时触发                  
  ------------------------------------- ---------------------------------

指针事件属性: event.pointerId 标识符, event.pointerType 设备类型

event.isPrimary 多点触控第一根手指为true

event.height\|width\|pressure 接触区域长\|宽\|压力

event.tiltX\|tiltY\|twist 触摸笔位置属性

event.clientX\|Y 相对于窗口坐标, event.target等

指针捕获: x.setPointerCapture(pointerId);
//将pointerId的后续事件重新定位到x

3\) 键盘事件

事件: keydown\|keyup //键盘按下\|释放时触发

属性: event.code //获取物理按键代码

event.key //获取键盘字符

4\) 滚动事件

scroll //允许对页面或元素滚动作出反应

4.4.4 表单

1\) 表单属性

表单属性集合: document.forms

获取属性: let x = document.forms.test; //获取\<xx name=\"test\"\>属性

let y = document.forms\[0\]; //获取第一个属性

let z = z.elements.test; //获取\<xx name=\"test\"\>属性的集合,
elements可省略

反向引用: let a = test.form; //获取对应表单

表单元素: input.value = \"value\"; //设置输入值

textarea.value = \" text\";

input.checked = true; //选择复选框

select.options\[2\].selected = true //\<option\>的子元素的集合

select.value = \'value\' //当前所选择的\<option\>的value

select.selectedIndex = 2 //当前所选择的\<option\>的编号

let option = new Option(text, value, defaultSelected, selected);
//自定义option

2\) 聚焦属性和方法

聚焦: 准备在此处接受数据; 失去焦点: 数据输入完成

x.focus\|blur 元素聚焦\|失去焦点触发事件, 支持: \<button\>, \<input\>,
\<select\>, \<a\>

x.focus()\|blur() 设置\|移除元素上的焦点

tabindex=\"1\" 支持\<div\>, \<span\>, \<table\>等的聚焦, 值有: -1, 0, 1,
2, ...

3\) 相关事件

change事件: x.onchange= handler(); //元素更改完成后触发

input事件: x.oninput = handler(); //对输入值进行修改时触发

剪切\|拷贝\|粘贴: x.oncut = x.oncopy = x.onpaste = handler(event);

4\) 提交

提交方式: \<input type=\"submit\"\> 或input字段中按enter键

属性: x.onsubmit = handler(); //提交时触发

x.onclick = handler(); //点击时触发

方法: x.submit(); //手动将表单提交到服务器

4.4.5 资源加载

1\) 文档加载

DOMContentLoaded //已完全加载HTML, 并构建了DOM, 外部资源还未加载

window.onload = handler(); //外部资源已加载完成, 如图片、样式等

window.onunload = handler(); //当访问者离开页面时触发

window.onbeforeunload = handler(); //用户正在离开时触发

document.readyState == \" loading\|interactive\|complete\";
//文档加载状态

2\) 脚本延时

defer //延时加载脚本, 文档顺序执行, \<script defer
src=\"xx.js\"\>\</script\>

async //延时加载脚本, 加载完成的先执行, \<script async
src=\"xx.js\"\>\</script\>

3\) 外部资源加载

x.onload = handler(); //脚本加载并执行完成时触发

x.onerror = handler(); //脚本加载期间error会触发

4.4.6 范围和选择

1\) 范围

语法: let r = new Range(); //创建一个范围

属性: r.startContainer\|startOffset; //起始节点和偏移量

r.endContainer\|endOffset; //结束节点和偏移量

r.collapsed; //在同一点上开始和结束为true

r.commonAncestorContainer; //在范围内所有节点最近的共同祖先节点

方法: r.setStart(node, offset); //将起点设置在node中的位置offset

r.setStartBefore(node)\|setStartAfter(node); //将起点设置在node前\|后

r.setEnd(node, offset); //将终点设置为node中的位置offset

r.setEndBefore(node)\|setEndAfter(node); //将终点设置在node前\|后

r.selectNode(node)\|selectNodeContents(node);
//设置选择整个node\|node内容

r.deleteContents()\|extractContents();
//从文档中删除范围内容\|并返回内容

2\) 选择

语法: let s = window.getSelection() \| document.getSelection();
//获取文档选择对象Selection

属性: s.anchorNode\|focusNode; //选择的起始\|结束节点

s.anchorOffset\|focusOffset; //选择的偏移量

s.isCollapsed; //未选或不存在为true

s.rangeCount; //选择中的范围数, 最大为1

方法: s.getRangeAt(i); //获取从0开始的第i个范围

s.addRange(range); //将range添加到选择中

s.removeRange(range)\|removeAllRanges(); //删除选择的range\|所有range

事件: s.onselectstart = handler(); //开始选择时触发

s.onselectionchange = handler(); //选择变动时触发

3\) 表单控件中的选择

属性: input.selectionStart\|selectionEnd; //选择的起始\|结束位置

input.selectionDirection; //选择方向, 值\"forward\", \"backward\",
\"none\"等

事件: input.onselect; //当某个东西被选择时触发

方法: input.select(); //选择文本控件中的所有内容

input.setSelectionRange(start, end, \[direction\]); //在给定方向上,
从start选到end

input.setRangeText(replace, \[start\], \[end\], \[mode\]);
//用新文本替换范围中的文本

4.5 浏览器和数据

4.5.1 浏览器对象

1\) 浏览器对象模型(BOM, Browser Object Model)

window对象为浏览器窗口, 所有js全局对象、函数以及变量均为window对象的成员

属性: window.innerHeight\|innerWidth; //浏览器窗口的内部高度\|宽度

window.screen.availWidth\|availHeight; //可用的屏幕宽度\|高度

window.navigator.xx; //浏览器的信息(xxx: userAgent, platform)

window.location.hostname\|pathname\|port\|protocol\|href\|pathname;
//返回web相关信息

方法: window.location.assign(); //加载新的文档

window.alert \| confirm(\"sometext\"); //提示窗口 \| 确认提示窗口

window.prompt(\"sometext\",\"defaultvalue\"); //输入提示窗口

window.history.back()\|forward(); //加载历史列表中的前一个\|下一个url

window.open(url, name, params); //打开独立新窗口(第三方), params窗口参数

window.close()\|moveTo(x,y)\|resizeTo(w,h); //关闭\|移动\|调整当前窗口

2\) 跨窗口通信

\<iframe\>承载了一个单独的嵌入的窗口, 它具有自己的document和window

获取: window.frames\[0\]; //索引获取,
window.frames是一个索引\<iframe\>集合

window.frames.iframeName; 名称获取

属性: iframe.contentWindow; //获取\<iframe\>中的window

iframe.contentDocument; //获取\<iframe\>中的document

sandbox特性: \<iframe sandbox=\"options\" src=\"\...\"\>\</iframe\>
//允许在\<iframe\>中禁止的某些特定行, options选项有: allow-same-origin,
allow-top-navigation, allow-forms, allow-scripts,
allow-popups多个以空格隔开。

跨窗口通信: window.frames.ifName.postMessage(data, targetOrigin)
//发送方发送

window.addEventListener(\"message\", function(event) { //接收方处理

event.data 数据; event.origin源; event.source对发送方窗口的引用

}

4.5.2 数据处理

1\) json数据转换

let obj = JSON.parse(text\[, reviver\]) //解析json字符串,
reviver对值进行处理的函数

let text = JSON.stringify(obj\[, replacer\[, space\]\])
//string格式化为json, replacer设置转换的函数或数组, space添加文本缩进

2\) 二进制数据

3\) 文件

4.5.3 存储数据

1\) Cookie

读取: document.cookie; //由多个name=value组成

写入: document.cookie = \"name=value\";

2\) 键值对存储

localStorage和sessionStorage允许我们在浏览器上保存键/值对

方法: s.setItem(key, value); //存储键/值对

s.getItem(key)\|removeItem(key); //按照键获取\|删除值

s.clear(); //删除所有数据

s.key(index); //获取该索引下的键名

属性: s.length; //存储的内容的长度

事件: window.onstorage = handler(event); //数据更新后触发

3\) IndexedDB

a\) IndexedDB是一个浏览器内置的数据库

打开: let r = indexedDB.open(name, version); //name数据库名, version版本

　　 let d = indexedDB.deleteDatabase(name); //删除数据库

事件: r.onsuccess = hander(); //数据库准备就绪, r.result进一步调用

r.onerror = handler(); 打开失败

b\) 对象库: 存数据时构建

建库: db.createObjectStore(name\[, options\]); //options: {keypath:
\'id\'}或{autoincrement: true}

删库: db.deleteObjectStore(name);

启动事务: db.transaction(name, \[type\]); //type类型:
readonly(只读)\|readwrite(读写)

方法: name.put(value, \[key\]); //存储value, 没有id是要有key

name.add(value, \[key\]); //存储value,
如果该值存在则返回\"ConstraInterror\"

事件: db.oncomplete = handler(); //事务成功触发

name.add(value).onerror = handler(); //失败触发

中止事务: db.abort();

c\) 搜索

创建索引: let index = name.createIndex(idxName, keyPath, \[options\]);

键搜索: name.id \| field; //id搜索 \| 字段收索

范围: IDBKeyRange.lowerBound\|upperBound (lower, \[open\]);
//大于或小于范围的值

IDBKeyRange.bound(lower, upper, \[lowerOpen\], \[upperOpen\]);
//之间的值

参数: name.get(query)\|getAll(\[query\], \[count\]);
//搜索满足条件的第一个\|所有

name.getKey(query)\|getAllKeys(\[query\], \[count\]); //搜索key

name.count(\[query\]); //统计满足的键总数

d\) 删除

删除值: name.delete(query);

删除所有值: name.clear()

4.5.4 网络请求

1\) Fetch

语法: let r = fetch(url, \[options\]); //请求, options包含method,
header等

属性: r.status\|ok; //http状态码 \| 状态码为200-299为true

方法: r.text()\|json()\|formData()\|blob()\|arrayBuffer();
//以对应形式返回response

r.headers.get(\'xx\'); 获取指定请求头

中止: let c = new AbortController(); //创建一个控制器

c.singal; //监听属性, 加入到options中signal: c.signal

c.abort(); //调用该方法终止Fetch

2\) Fetch api

fetch所有参数:

let promise = fetch(url, {

method: \"GET\", // POST, PUT, DELETE等

headers: { //内容类型header值通常是自动设置的

\"Content-Type\": \"text/plain;charset=UTF-8\"

},

body: undefined, //string, FormData, Blob, BufferSource, URLSearchParams

referrer: \"about:client\", //或\"\"或是当前源的url

referrerPolicy: \"no-referrer-when-downgrade\", //no-referrer, origin,
same-origin\...

mode: \"cors\", //same-origin, no-cors

credentials: \"same-origin\", //omit, include

cache: \"default\", //no-store, reload, no-cache, force-cache,
only-if-cached

redirect: \"follow\", //manual, error

integrity: \"\", //一个hash, 如\"sha256-abcdef1234567890\"

keepalive: false, //长连接true

signal: undefined, //中止请求AbortController

window: window //null

});

3\) 常用对象

a\) FormData

语法: let f = new FormData(\[formId\]); //自动获取表单数据

方法: f.append(name, value)\|append(name, blob, fileName); //添加name:
value字段\|文件

f.delete(name)\|get(name)\|has(name); //移除\|获取\|是否有name字段

f.set(name, value)\|set(name, blob, fileName); //删除原有的设置新字段

b\) URL对象

语法: let u = URL(url, \[base\]);

参数: u.searchParams.append(name, value)\|set(name, value);
//添加参数\|set/replace参数

u.searchParams.delete(name)\|get(name)\|getAll(name)\|has(name);
//删除\|获取\|判断

4\) WebSocket

语法: let s = new WebSocket(\"wss://xx.info\", \[options\]);

事件: s.onopen = handler(); //已经建立连接

s.onmessage = handler(event); //接收到数据

s.onclose = handler(event); //连接已关闭

s.onerror = handler(error); //WebSocket错误

方法: s.send(data); //发送数据

s.close(\[code\], \[reason\]); //code是关闭码, reason是关闭原因

属性: s.readyState = \"0\|1\|2\|3\";
//连接还未建立\|通信中\|关闭中\|已关闭

五 web应用

5.1 项目管理

5.1.1 yarn管理包

1\) 初始化项目

yarn init

2\) 依赖管理(dependencies(必须包), devDependencies(开发包))

添加: yarn add \[\--dev(开发) \| \--exact(排除)\] pkg\[@version\]
//添加到package.json

升级: yarn upgrade \[package@version\]

移除: yarn remove package

安装: yarn install \[\--production(只装dep包) \--force(重装)\]
//默认安装所有

显示: yarn info \[package\]

3\) 运行打包

yarn run \[name\] //执行package.json中scripts定义的脚本

4\) 其它

yarn global add pkg //全局安装

yarn cache \[list\|dir\|clean\]
//列出已缓存的每个包\|返回全局缓存位置\|清除缓存

yarn autoclean \[\--force\] //清理依赖中无用文件或文件夹

yarn bin \[name\] //打印可执行文件路径

yarn config list //显示所有配置

yarn config get\|delete key 删除配置

yarn config set \[\--global\] key value 设置配置

注意: 安装的可执行软件在./node_modules/.bin

5.1.2 npm包管理

1\) 初始化项目

npm init

2\) 管理依赖

npm search package //搜索包

npm install \[-g(全局) \| \--save (生产) \| \--save-dev(开发)\] package
//添加package.json,

npm update pkg \[\--save-dev\] //更新包

npm remove package //删除包

npm uninstall \[-g \| \--save \| \--save-dev\] package //卸载包

npm run \[name\] //运行打包

n\) 其它

npm config list //查看npm配置

npm cache clean //删除缓存目录下的所有数据

5.2 react

5.2.1 react基础

1\) react简介

react是用于构建用户界面的js库, 其特点是声明式、组件化。

创建应用: yarn global add create-react-app && create-react-app myApp

构建: yarn run start \| build //测试 \| 生产

2\) jsx语法

变量声明: const name = \"xx\";

引用声明: const elm = \<h1 frontSize=\"xx\"\>xx, {name}\</h1\>;
//引用变量

const elm = \<h1\>xx, {myFunc(xx)}\</h1\>; //js表达式都可以, 如: u.Name

jsx表达式: function test(a) { ...; return \<p\>xx, {expression}\</p\> }

子元素: const elm = (\<div\>\<h1\>xx\</h1\>\</div\>); //换行写

jsx对象: const elm = React.createElement(\'h1\', {className: \"xx\"},
\"xx\");

注意: a) 在jsx中dom的属性名使用camelCase命名, 如: class - className,
font-size - fontSize

b\) 内联样式: {{key: value}}

3\) 组件

组件: 将ui拆分后可复用的代码片段

渲染: ReactDOM.render(elem, document.getElementById(\'myId\'));
//渲染元素到根DOM

函数组件: function Test(props) { return \<p\>xx, {props.val}\</p\>}
//props由父组件传入

class组件: class Test extends React.Component {render() {return
\<p\>x,{this.props.val}\</p\>}}

使用组件: const elm = \<Test val=\"xx\" /\>; //然后进行渲染,
val=\"xx\"是传给props的

注意: a) 组件名必须以大写字母开头, 组件之间可以组合, 如:

function App() { return (\<div\>\<Test val=\"xx\" /\>\<Test val=\"yy\"
/\>\</div\>) };

b\) React不能更改props值, props属于react数据流

4 stage生命周期

class Test extends React.Component {

constructor(props) { super(props); this.state = {xx}; } //初始化state,
由组件本身管理

componentDidMount() { ... } //第一次渲染组件时挂载

componentWillUnmount() { ... } //组件被删除时卸载

change() { this.setState({ xx }); } //更新组件,
多个setStage()可能会被合并为一个

render() { return xx; }

}

5\) 事件处理

class Test extends React.Component {

constructor(props) { super(props); this.state = {xx}; this.A =
this.A.bind(this, xx); }

A(xx) { this.setState(state =\> ({ ... })); } //该函数中的this需要绑定

B = () =\> { this.state; ... } //该处理函数中的this不需要绑定

render() { return (xx) }

}

注意: a) 事件以camelCase命名, 且需要传入函数作为事件处理函数。

6\) 列表和组合

列表: const content = list.map((item) =\> \<ListItem key={xx} value={xx}
/\>); //必须指定key

组合: function A(props) {return (\<div xx\>{props.child}\</div\>)}
//接收PropTypes.node

function B() {return (\<A xx\>\<h1 xx\>xx\</h1\>\<p xx\>xx\</p\>\</A\>)}
//传递子组件

class C() extends React.Component { ...; render() { return \<A
xx\>...\</A\>}} //使用类

7\) 其它

状态提升: 如果不同组件需要同一数据源, 可将其提升至共同父组件

严格模式: function Test() { //严格模式只在开发模式下运行

return (\<div\> \<React.StrictMode\> \<A /\> ... \</React.StrictMode\>
\</div\>)

}

5.2.2 react进阶

1\) 类型检查(import PropTypes from \'prop-types\')

类组件类型检查:

class Test extends React.Component {

constructor(props) { ... }; ...; render() { return (...) };

static propTypes = { //设置props类型验证

key: PropTypes.string \| bool \| number \| object \| array \| func \|
symbol, //验证类型

key: PropTypes.node, //为任何可被渲染的元素

key: element \| elementType, //为单个react元素 \| 类型

key: PropTypes.instanceOf(MyClass), //为自定义类型

key: PropTypes.oneOf(\[\'News\', \'Photos\'\]), //自定义枚举类型之一

key: PropTypes.oneOfType (\[PropTypes.string, PropTypes.func, ...\]),
//指定类型之一

key: PropTypes.arrayOf(PropTypes.string), //数组值类型

key: PropTypes.objectOf(PropTypes.string), //对象值类型

key: PropTypes.shape({ k1: PropTypes.string, k2: PropTypes.func }),
//对象值类型

key: PropTypes.any.isRequired, //任意类型的必须数据,
其它检查也可用isRequired

customProp: function(props, key, componentName) { ...}, //自定义验证器

objProp: PropTypes.objectOf(val, key, componentName, location,
propFullName) { ... }

} //自定义验证器, 失败时要返回Error

static defaultProps = { key: \'xx\' } //设置props的默认值

}

函数式组件检查:

function Test(props) { return \<p\>xx, {props.val}\</p\>}

Test.propTyepes = { val: PropTypes.string.isRequired, ... } //类型检测

Test.defaultProps = { val: \"xx\", ... } //添加默认值

2\) refs

refs用于修改子组件, 使用场景: 焦点, 选择, 媒体, 触发动画,
集成第三方DOM库。

class Test extends React.Component { ...

myRef = () =\> React.creatRef() //返回一个容器,
将标识节点存储在current中

myRef = (current) =\> { ... } //回调式ref, 通过current之间修改,
如current.value

render() { return (\<input ref={this.myRef} ...\>)} //调用ref

}

3\) 受控和非受控组件

受控组件:

class Test extends React.Component { //值通过事件传递

constructor(props) { super(props); this.state = {user: \' \', pwd: \'
\'}}

change = (event) =\> { this.setState({user: event.target.value})}; ...

render() { return (\<input type=\'x\' onChange={this.change}
name=\"name\" /\>);}

}

非受控组件:

class Test extends React.Component { //值不是通过事件传递的

myRef = () =\> React.creatRef(); show = () =\> {
this.myRef.current.value = xx };

render() { return ( \<form onSubmit={this.show}\>\<input
ref={this.myRef} .../\>\</form\>)}

}

5.2.3 react api

5.3 vue

5.2.1 vue基础

1\)

5.n) 学习项目

5.n.1 初级项目

学习react: 俄罗斯方块: https://github.com/chvin/react-tetris

学习vue: vue-sui-demo: https://github.com/eteplus/vue-sui-demo

学习node: N-blog: https://github.com/nswbmw/N-blog

小程序: weapp: https://github.com/imageslr/weapp-library

游戏: keyboard: https://github.com/viclm/numeric-keyboard

pacman: https://github.com/mumuy/pacman

sweeper: https://github.com/propjockey/css-sweeper

emoji: https://github.com/muan/emoji-minesweeper

battle: https://github.com/shinima/battle-city

star: https://github.com/gd4Ark/star-battle

huejumper: https://github.com/KilledByAPixel/HueJumper2k

5.n.2 中级项目

react进阶: Cnode: https://github.com/Juliiii/React-Cnode

trello: https://github.com/rcdexta/react-trello

vue进阶: 商城: https://github.com/bailicangdu/vue2-elm

电商: https://github.com/newbee-ltd/newbee-mall-vue3-app

electron: gridea: https://github.com/getgridea/gridea

分享微信, 微博: share.js : https://github.com/overtrue/share.js

css图标: Font-Awesome: https://github.com/FortAwesome/Font-Awesome

js实例: wtfjs: https://github.com/denysdovhan/wtfjs

小程序: wxapp: https://github.com/o2team/wxapp-market

加载动画: Spinkit: https://github.com/tobiasahlin/SpinKit

5.n.3 高级项目

游戏: BrowserQuest: https://github.com/mozilla/BrowserQuest

hearthstone: https://github.com/hbt-org/hearthstone-battlegrounds-tools

大项目: Rental: https://github.com/answershuto/Rental

cdfang: https://github.com/mengsixing/cdfang-spider

folio: https://github.com/brunosimon/folio-2019

视频弹幕: DPlayer: https://github.com/DIYgod/DPlayer

高级vue: weweChat: https://github.com/trazyn/weweChat

chrome: simpread: https://github.com/Kenshin/simpread

图表: echats: https://github.com/apache/echarts
