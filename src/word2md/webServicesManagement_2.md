4.1.1 TCP/IP模型层次结构

1\) ISO/OSI七层模型

应用层: 为用户直接提供各种网络服务。网络服务协议有: HTTP, HTTPS, FTP,
SMTP等

表示层: 表示层提供各种用于应用层数据的编码和转换功能

会话层: 会话层就是负责建立、管理和终止表示层实体之间的通信会话

传输层: 对报文进行分组(发送)、组装(接收)。协议有: TCP(传输控制协议,
在收发数据前, 必须和对方建立可靠的连接, 面向有连接的传输),
UDP(用户数据报协议, 面向无连接的传输协议,
传输数据之前源端和终端可不建立连接, 对系统资源的要求较少, 传输效率高)

网络层: 由IP寻址来建立两个节点之间的连接。协议: IP, ARP(地址解析),
RARP(反向)

数据链路层: MAC地址编址、寻址。协议: PPP, PPPOE

物理层: 数据实际传输

2\) TCP/IP五层协议

应用层、传输层、网络层、数据链路层、物理层。

4.1.2 TCP三次握手、四次挥手

tcp报头:

<https://blog.csdn.net/qq_38950316/article/details/81087809>

https://www.cnblogs.com/jainszhang/p/10641728.html

4.1.3 TCP拥塞控制机制

网络拥塞: 当网络中某一资源的需求超过了该资源所能提供的可用部分,
网络性能变坏。在计算机网络中数位链路容量(即带宽)、交换结点中的缓存和处理机等,
都是网络的资源。若出现拥塞而不进行控制,
整个网络的吞吐量将随输入负荷的增大而下降。

TCP的四种拥塞控制算法: 慢开始, 拥塞控制, 快重传, 快恢复

新方法: BBR拥塞控制算法

4.1.4 计算机网络: IP地址, 子网掩码, 网段表示法, 默认网关, DNS服务器详解

https://www.cnblogs.com/iceJava/p/5372033.html

局域网ip地址: 10.0.0.0-10.255.255.255; 172.16.0.0-172.131.255.255;
192.168.0.0-192.168.255.255

默认DNS地址国内114.114.114.114(备用114.114.115.115),
国外是8.8.8.8(备用8.8.4.4)

其它知识:

二进制, 八进制, 十六进制转十进制: 从低位开始, 将每个位上的数提取出来,
乘以进制(2,8,16)的位数减一次方, 然后求和。

1011 = 1\*2\^0 + 1\*2\^1 + 0\*2\^2 + 1\*2\^3 = 11

0x1A = 10\*16\^0 + 1\*16\^1 = 17

十进制转其它进制: 将十进制数除以要转的进制(2,8,16), 直到不能被整除,
然后将每步得到余数倒过来排列就是对应的进制数, 如果所有余数都为零,
则增加一个值为1的高位。

4 = 4%2=0 2%2=0 = 100

156 = 156%8=4 19%8=3 2/8=0 = o234

4.1.5 VPN类型, 常见路由协议。

http://www.dttc-icp.com/newsarticle/9800.html

https://blog.csdn.net/istrangeboy/article/details/103247314

虚拟专用网络(Virtual Private Network,
简称VPN)指的是在公用网络上建立专用网络的技术。

按VPN的协议分类: VPN的隧道协议主要有三种, PPTP, L2TP和IPSec

按VPN的应用分类: Access VPN(远程接入VPN), Intranet VPN(内联网VPN),
Extranet VPN(外联网VPN)

按所用的设备类型进行分类: 路由器式VPN, 交换机式VPN, 防火墙式VPN

VPN按应用来分类: 可分为VLL、VPLS、VPDN、VPRN。

4.1.6 常见网络端口

  ----------------------------------- -----------------------------------
  ftp服务20/21 tcp/udp文件共享        ssh服务 22
                                      tcp/udp安全远程管理记得改

  smtp(ssl) 465发信                   imap4(ssl) 993收信

  www服务(https://) 443加密网页访问   http/nginx 默认端口80

  tomcat/jenkins 默认端口8080         DNS端口 53域名解析端口

  dhcp 发送67/udp 接收68/udp          Mycat默认端口8066

  mysql端口3306数据库连接端口         redis 默认端口6379
  ----------------------------------- -----------------------------------

所有端口可以在/etc/service中查看, 需要了解CCNA

4.2 网络管理

4.2.1 ip

ip address 查看网卡信息

ip route显示路由信息

ip neigh 查看ARP(将ip解析为mac地址)缓存列表

ip -s link 查看网络统计信息, 如传输的字节和数据包,
错误或丢失的数据包等。

4.1.2 ss

1\) ss \[options\] 查看网络状态, 及端口占用

  ------------------------------------- ---------------------------------
  -a 列出所有网络状态                   -p 显示PID和程序名

  -n 显示ip和端口                       -t 显示tcp协议端口连接状态

  -u 显示UDP协议端口连接状态            -l 显示正在监听的端口

  -C num 每隔num秒刷新一次网络状态      -r 显示路由表

  -s 统计每种状态tcp连接数              -o state对应连接状态进行统计

  src \| dst 按源地址ip或目标地址ip统计 dport \| sport
                                        按源端口或目标端口统计
  ------------------------------------- ---------------------------------

ss -tunlp \| grep service 显示端口占用情况

ss -at \'( dport = :ssh or sport = :ssh )\' 或ss -at \'( dport = :80 or
sport = :80 )\'进行过滤

ss -o state established \'( dport = :http or sport = :http )\'
已建立的http连接

ss -ta \| -ua \| -wa \| -xa \| -tl查看tcp sockets \| udp sockets \| RAW
sockets \| unix sockets \| tcp listen

注意: LISTEN状态的Recv-Q(当前全连接队列的大小, 表示已完成三次握手),
Send-Q(为listen backlog值, 也就是accept队列最大值); Recv-Q不能超过Send-Q

ESTAB状态的 Recv-Q(内核中的数据还有多少(bytes)没有被应用程序读取,
发生了一定程度的阻塞, 应为0),
Send-Q(代表内核中发送队列里还有多少(bytes)数据没有收到ack,
对端的接收处理能力不强, 应为0)。

ss dst 10.66.224.130\[:http \| 443 \| smtp\]
目标端口ip的所有连接(dst目标端口, Peer Address)

ss sport eq :22 查看源端口为22的所有连接(Local Address)

ss -a \| awk \'/\^tcp/{++S\[\$2\]} END {for(a in S) print a, S\[a\]}\'
统计各种连接状态数

注意源端口或ip(Local Address)与目的端口或ip(Peer Address)的区别

4.1.3 nmcli

查看所有网卡信息(属于NetWorkManager命令, 可用于脚本中批量修改ip)

nmcli device status 查看网卡设备状态

nmcli device disconnect\|connect ens33 断开\|连接ens33

nmcli connection show 查看连接服务设备

nmcli connection up\|down ens33 启动\|关闭ens33

nmcli connection delete ens33删除网络连接

nmcli connection reload \[ens33\] ; systemctl restart
network重启网络服务

4.1.4 curl和wget

1\) curl

curl \[option\] url #向url发出GET请求(不加参数)

curl -O\|\[-o test.txt\] url #保存服务器响应, 用url最后一部分作文件名 \|
指定文件名

curl -i \| -I \| -k http://xx.com #输出服务器响应头及网页源码 \| 响应头
\| 跳过https验证

curl -X POST http://xx.com #向url发送POST请求, -X指定请求方式

curl -X POST -d \'login=nam\' -d \@file.txt http://xx.com/login
#发送POST数据体

curl -X POST -F raw=@file.txt -F name=xx https://xx.com #发送表单数据

curl -H \'Content-Type: application/json\' https://xx.com
#添加http请求头

curl -L http://xx.com #让http请求跟随服务器重定向, 默认不跟随

curl -u \'name:passwd\' https://google.com/login #发送用户名和密码

curl ip.sb \| curl ifconfig.me #查看公网ip

2\) wget

wget \[-c -P path -O name\] url 下载, -c 断点续传, -P下载到指定目录,
-O指定文件名

4.1.5 ping

ping \[-i\|c\|s\|p\] ip 其中-i指定时间间隔; -c指定次数;
-s指定数据包大小; -p查看在局域网段ip占用情况。

监测某个范围ip是否在线:

#!/bin/bash

for((a=1;a\<255;a++)) ; do

ping -c 1 10.10.10.\$a \> /dev/null && echo \"10.10.10.\$a is up\" \|\|
echo \"10.10.10.\$a is down\"

done

4.1.6 其它命令

1\) 地址解析协议

arp \[-a\] 将ip地址解析成MAC地址; -a查看所有;

2\) 网络探测扫描

nmap \[-sP\|sT\] ip ; 需要安装, -sP探测某个网段内有哪些主机存活;
-sT某主机上开启了那些TCP端口

3\) nslookup域名\|ip 进行域名解析\|反向解析

4\) dig [www.baidu.com](http://www.baidu.com) \[+short\] 域名解析监测

5\) netstat

netstat -antp \| awk \'{print \$4}\' \| cut -d \':\' -f1 \| sort \| uniq
-c \| sort -n -k1 -r \| head -n 10 统计连接本地做多的10个ip

netstat -na \| awk \'/\^tcp/ {++S\[\$NF\]} END {for(a in S) print a,
S\[a\]}\' 统计各种连接状态数

6\) tcpdump抓包(安装yum -y install tcpdump)

tcpdump not port 22 排除22端口; tcpdump port 443 仅监听443端口

tcpdump -i eth0 -nn -s0 -v port 80 其中: -i 网卡; -n不解析域名;
-nn表示端口也是数字, 否则解析成服务名; -s设置抓包长度, 0不限制;
-v显示输出, -vv, -vvv更详细。

-A选项将打印ascii ; -X打印hex码; host ip 指定ip ; -w
test.txt写入test.txt文件;

7\) iperf3 测试网速

服务端: iperf3 -s(服务模式) -D(后台运行)

客户端: iperf3 -c 192.168.8.80 -b 100M(发送带宽大小) -p 80(端口)

4.1.7 开启路由转发

临时生效: sysctl -w net.ipv4.ip_forward=1

永久生效: echo \" net.ipv4.ip_forward = 1 \" \>\> /etc/sysctl.conf ;
然后刷新内核: sysctl -p

4.1.8 tcp优化

修改: /etc/sysctl.conf或/proc/sys/net/ipv4/...

1\) TCP三次握手优化:

\(1\) SYN_SENT 状态的优化(客户端)

net.ipv4.tcp_syn_retries = 4 #对于一个新建连接, 内核要发送多少个 SYN
连接请求才决定放弃。默认值是5, 对应于63s=1+2+4+8+16+32。

\(2\) SYN_RCVD 状态的优化(服务端)

查看半连接接队列溢出: netstat -s \| grep \"SYNs to LISTEN\"
如果值在增大就有问题

net.ipv4.tcp_max_syn_backlog = 8192 #表示SYN队列的长度, 默认为1024,
加大队列长度可以容纳更多等待连接的网络连接数。

net.core.somaxconn = 4096
#定义了系统中每一个端口最大的监听队列的长度,这是个全局的参数,默认值为128

对于应用程序: nginx增大backlog: listen 80 default backlog=4096;

net.ipv4.tcp_syncookies = 1
#表示开启SYNCookies。当出现SYN等待队列溢出时, 启用cookies来处理,
可防范少量SYN攻击, 默认为0表示关闭, 2表示无条件开启。

net.ipv4.tcp_synack_retries = 4 #控制SYN+ACK包的重传数量, 既二次握手,
默认值是5, 对应于63s。

查看全连接接队列溢出: netstat -s \| grep overflowed

net.ipv4.tcp_abort_on_overflow = 0 #全队列溢出处理方式,
0表示丢弃client发来的ack,
有利于突发流量。1表示直接发RST(重连)发给client, 处理长期溢出的情况。

accept 队列的长度取决于somaxconn 和 backlog
之间的最小值。Tomcat、Nginx、Apache 常见的 Web 服务的 backlog 默认值都是
511

绕过三次握手(握手两次): net.ipv4.tcp_fastopn = 0\|1\|2\|3 #0关闭,
1作为客户端使用fast open, 2作为服务器使用fast open, 3都使用fast open

2\) TCP四次挥手优化

\(a\) 主动方优化

关闭连接方式: 程序异常退出, 内核发送RST不走四次挥手; 正常关闭发送FIN。

\(1\) FIN_WAIT1 状态的优化

net.ipv4.tcp_orphan_retries = 5 #FIN报文重传次数, 默认0特指8。

net.ipv4.tcp_max_orphans = 16384 #系统中最大的孤儿连接数量,
防恶意攻击发不出FIN, 使新增的孤儿连接不走四次挥手, 直接RST强制关闭。

\(2\) FIN_WAIT2 状态的优化

net.ipv4.tcp_fin_timeout = 60 #孤儿连接在FIN_WAIT_2状态的时间, 默认60s

\(3\) TIME_WAIT 状态的优化

TIME_WAIT作用: 防止数据丢失, 保证被动方收到ack正常关闭。

net.ipv4.tcp_max_tw_buckets = 60000 #表示系统最大保持TIME_WAIT的数量,
如果超过,
新关闭的连接不走TIME_WAIT而立刻被清除并打印警告信息。默认为180000。

net.ipv4.tcp_tw_reuse = 1 #表示开启重用。允许将TIME_WAIT
sockets重新用于新的TCP连接, 默认为0, 表示关闭,
注意必须打开时间戳才能用。

net.ipv4.tcp_timestamps = 1 #打开tcp时间戳功能。

\# net.ipv4.tcp_tw_recycle = 0 #表示开启TCP连接中TIME_WAIT
sockets的快速回收, 0关闭, 1开启, 正常情况不建议开启, 开启可能导致丢包,
Linux 4.12以后取消了该参数。

\(b\) 被动方的优化

\(1\) CLOSE_WAIT 状态优化

net.ipv4.tcp_keepalive_time = 600 #控制 TCP/IP
尝试验证空闲连接是否完好的频率, 默认7200s。

net.ipv4.tcp_keepalive_probes = 5
#TCP发送keepalive探测以确定该连接已经断开的次数, 默认9。

net.ipv4.tcp_keepalive_intvl = 15 #当探测没有确认时, 重新发送探测频率,
默认75。该值乘以tcp_keepalive_probes就是从开始探测以来没有响应的连接杀除的时间。如:
15\*5=75s。

\(2\) LAST_ACK 状态优化

net.ipv4.tcp_orphan_retries = 4 \# FIN报文重传次数, 同上。

3\) tcp传输数据优化

net.ipv4.tcp_window_scaling = 1 #启用窗口扩大因子功能, 默认打开。

net.ipv4.tcp_wmem = 4096 87380 4194304 #tcp发送缓冲区自行调节范围,
高于第三个值, TCP拒绝分配socket(内存单位是页), 4096 byte = 4K。

net.ipv4.tcp_rmem = 4096 87380 6291456 #tcp接收缓冲区自行调节范围,
第3个为动态调整最大值。

net.ipv4.tcp_moderate_rcvbuf = 1 #开启tcp接收缓冲区自动调节功能。

net.ipv4.tcp_mem = 88560 118080 177120 #调整tcp内存范围,
该单位是页面大小, 当tcp内存大于第3个值时内核不再未tcp分配内存,
此时新连接无法建立。

net.ipv4.ip_local_port_range = 10001 65000 对外端口范围,
默认是32768-61000。

4.1.9 常见问题

1\) TIME_WAIT

TIME_WAIT是主动关闭连接的一方保持的状态, 像 nginx、爬虫服务器,
经常发生大量处于time_wait状态的连接。TCP一般在主动关闭连接后, 会等待2MS,
然后彻底关闭连接。由于HTTP使用了TCP协议,
所以在这些频繁开关连接的服务器上, 就积压了非常多的 TIME_WAIT 状态连接。

2\) CLOSE_WAIT

CLOSE_WAIT一般是由于对端主动关闭,
而我方没有正确处理的原因引起的。说白了, 就是程序写的有问题,
属于危害比较大的一种。如果CLOSE_WAIT持续增长要注意。

治标解决办法: 是修改内核参数, 见上节。

可用ss \--tcp state CLOSE-WAIT \--kill 关闭所有close-wait状态,
不建议使用

4.3 安全远程连接SSH

4.3.1 ssh安全远程管理协议

1\) 密码连接

2\) 免密连接

Client将自己的公钥存放在Server上, 追加在文件authorized_keys中。

Server端接收到Client的连接请求后,
会在authorized_keys中匹配到Client的公钥pubKey, 并生成随机数R,
用Client的公钥对该随机数进行加密得到pubKey(R),
然后将加密后信息发送给Client。

Client端通过私钥进行解密得到随机数R,
然后对随机数R和本次会话的SessionKey利用MD5生成摘要Digest1,
发送给Server端。

Server端会也会对R和SessionKey利用同样摘要算法生成Digest2。

Server端会最后比较Digest1和Digest2是否相同, 完成认证过程

4.3.2 ssh连接

ssh \[-i key.pem\] -p port user@ip; 第一次连接免yes加-o
StrictHostKeyChecking=no;
或修改/etc/ssh/ssh_config文件StrictHostKeyChecking no

免密码登录:

客户端生成密钥文件: ssh-keygen -t rsa(加密类型rsa\|dsa) -b
2048(加密长度); 免交互使用: ssh-keygen -t rsa -b 2048 -P \"\" -f
\~/.ssh/id_rsa

将公钥传输到服务器: ssh-copy-id -p port user@ip
公钥在服务器的authorized_keys中;免交互需要安装sshpass;
或者用expect免交互。

密钥文件权限: id_rsa 600 , id_rsa.pub 644, authorized_keys 600, .ssh 700

4.3.3 ssh配置

配置文件: /etc/ssh/sshd_config(做服务端配置文件)
/etc/ssh/ssh_config(做客户端配置文件),
修改配置文件要重启ssh服务(systemctl restart sshd)

服务器禁止客户端密码登录: sshd_config中改为PasswordAuthentication no

允许密钥验证: RSAAuthentication yes ; PubkeyAuthentication yes (默认,
老版本要设置)

禁止远程root登录: PermitRootLogin no, 可以通过普通用户内部切换到root。

默认端口: Port num(\>10000), 改完端口后免密钥需要重配

无操作掉线: ClientAliveInterval 300 (5分钟); ClientAliveCountMax 3
(提醒次数)

限制ssh监听IP: ListenAddress ip, 可以限制外网登录, 只有局域网可以登录,
这样可以只设置一台服务器可以远程连接, 其它主机通过这台机子远程连接。

使用脚本收集/var/log/secure里面的信息, 若是某个IP链接次数超过一定次数,
则把此ip记录到/etc/hosts.deny里面。

4.3.4 ssh相关服务

ssh执行远程命令: ssh -p port \[-t\] user@ip \"command\"
其中-t保持远程登陆

ssh远程执行本地脚本: ssh -p port user@ip \< script.sh

scp远程文件复制: scp -P port user@ip:/path /path; scp -P port \[-r\]
/path user@ip:/path

sftp文件传输协议: sftp -P port user@ip, 进入交互模式

Xshell与本机上传下载工具lrzsz, 上传rz, 下载sz

4.4 DHCP服务

4.4.1 DHCP简介

DHCP动态主机配置协议, 是一个工作在应用层的局域网网络协议,
数据传输时使用UDP, 通常被用在大型局域网络环境中,
主要作用是集中管理、分配网络资源,
使网络环境中的主机能动态获得IP地址、Gateway地址、DNS服务地址等信息,
并能提升地址使用率。后期进行无人值守安装操作系统会用。

4.4.2 DHCP租约和续租

1\) DHCP租约过程

客户端发送ip请求, 服务端发送ip, 客户端回复服务器确认,
服务器和客户端建立租约(ACK)或不建立(NAK)。

DHCP客户端进行IP请求: 客户机启动时,
会将自己的IP地址配置为0.0.0.0作为源地址, UDP 68为源端口,
使用255.255.255.255作为目标地址, UDP
67端口作为目的端口来广播请求IP地址信息。广播信息DHCP
discover包含DHCP客户机的MAC地址和计算机名。

DHCP服务器响应请求: DHCP服务器使用自己的ip地址作为源地址, UDP
68为源端口, 使用255.255.255.255作为目标地址, UDP 67为目的端口来广播DHCP
OFFER信息。该信息包括: DHCP客户机MAC地址, DHCP服务器提供的合法IP地址,
子网掩码, 默认网关(路由), 租约的期限, DHCP服务器的IP地址-MAC

DHCP客户机选择IP: DHCP客户机保存接受的第一个DHCP
OFFER中的IP。客户机将DHCP REQUEST消息(客户端MAC, 接受的IP,
服务器的MAC)广播到所有DHCP服务器, 表明接受。由于还没有得到最后确认,
客户机仍然使用0.0.0.0为源地址, 255.255.255.255作为目标地址, 使用UDP
68端口作为目的端口来广播DHCP REQUEST消息。

DHCP服务器租约确认: DHCP服务器收到DHCP REQUEST后, 以DHCP
ACK消息(IP地址有效租约和其它配置信息)向客户机广播成功确认,
在收到之前一直播。客户机收到后就配置IP, 完成TCP/IP初始化。

服务器拒绝租约(DHCP NAK): 若DHCP REQUEST不成功, 如IP不可用或无效,
DHCP服务器将广播否定确认消息DHCP NAK。客户机收到DCHP NAK时从新DHCP过程。

注意: 如果客户机无法找到DHCP服务器,
它将从TCP/IP的B类网段169.254.0.0/16中挑选IP地址,
每5分钟尝试DHCP服务器通讯; 客户机收到服务器回应的ACK后,
进行地址冲突检测(arp), 如果不能用, 会发送DECLINE报文, 通知地址不能用。

2\) DHCP续租过程

当客户机在租期到50%时, 直接向DHCP服务器发送DHCP REQUEST消息,
如果客户机收到回复的DHCP ACK消息后,
客户机会根据新的租期及其它进行更新TCP/IP参数。没收到继续用原来的租约的最大值。

如果没更新, 当租期到87.5%时会再次请求, 如果还不成功,
到100%时DHCP客户机会放弃IP重新申请。如果没有可用DHCP服务器,
会从169.254.0.0/16中挑选IP地址每隔5分钟试一次。

4.4.3 DHCP服务搭建

a\) 模拟环境:

DHCP软件: dhcp, dhcp-common

服务名: dhcpd(DHCP服务名), dhcrelay(DHCP中继服务名)

端口号: udp 67(接收端口) ; udp 68(发送端口)

配置文件: /etc/dhcp/dhcpd.conf默认是空的;
/usr/share/doc/dhcp-4.\*/dhcpd.conf.sample配置文件模板;
/etc/sysconfig/dhcrelay中继配置文件(dhcrelay)

b\) 配置文件:(注意先修改配置文件, 再启动dhcp)

前面是全局配置。后面subnet就是局部配置,
资源池。host部分是给某一个主机设置一个固定的ip地址。

注意subnet配置中, 至少有一个配置的网段是服务器正在使用的网段,
自己配置时把多余的subnet删掉。配置完后启动服务。

subnet 192.168.88.0 netmask 255.255.255.0 { 声明要分配的网段和子网掩码

range 192.168.88.3 192.168.88.254; 声明IP地址池

option domain-name \"haha.com\" ; 设置DNS域, 实验可不要

option domain-name-servers 8.8.8.8 ; 设置DNS服务器地址, 实验可不要

option routers 192.168.88.2 ; 默认网关地址, 实验可不要

option broadcast-address 192.168.88.255 ; 广播地址, 可不写

default-lease-time 600 ; 默认租约(s)

max-lease-time 7200 ; 最大租约(s)

}

注意在生产环境中, 重启网卡不建议使用systemctl restart network。

如果systemctl start dhcpd失败, 可能是你在修改配置文件之前就启动过一次,
需要先stop, 再start。

d\) 保留地址(固定地址分配): 为某一个服务器配置一个固定ip地址

首先在客户端获取mac地址: arp -a 或ifconfig 查看客户机的mac地址

修改/etc/dhcp/dhcpd.conf文件:

host fantasia {

hardware ethernet mac地址 ; 客户机地址, 通过ip adder 查看,
link/ether后的值

fixed-address IP地址 ; 固定ip, 可以是地址池以外的ip

}

重启dhcpd服务, 可以在/var/log/messages中看相关过程

4.4.4 超级作用域

DHCP服务器可为单个物理网络上的客户端提供多个作用域租约地址。就是当一个网段的ip不够分的时候,
需要加一个网段。

实验环境准备: 三台虚拟机同一网络模式, 一个DHCP服务器, 两个客户机。

设置DHCP服务器的单臂路由所需网卡: cp -a ifcfg-ens33 ifcfg-ens33:0
编辑此文件, 修改NAME和ip地址。(就是一个网卡两个ip, 如果有两个网卡会出错)

开启路由转发: 将/etc/sysctl.conf 中改net.ipv4.ip_forward = 1 ; sysctl
-p刷新内核参数。实际生产环境中是直接修改路由器。

修改/etc/dhcp/dhcpd.conf文件: 前面的subnet注释掉

shared-network 88-99 { 和下面ip对比

subnet 192.168.88.0 netmask 255.255.255.0 {
subnet至少有一个是服务器使用的网段

option routers 192.168.88.10 ; 网关

range 192.168.88.100 192.168.88.100 ; }

subnet 192.168.99.0 netmask 255.255.255.0 {

option routers 192.168.99.10 ;

rang 192.168.99.100 192.168.99.110}

} 将里面的pool删掉

重启dhcpd服务。再启动另外两个客户端的网卡,
注意这两个客户端要先设置成dhcp模式。

4.4.5 DHCP中继

DHCP Relay(DHCPR) DHCP中继是一个小程序,
可以实现不同子网和物理网段之间处理和转发dhcp信息的功能。就是中间隔着路由器的情况

1\) 实验环境: 三台实验机: DHCP服务器(eth0(192.168.101.10) vmnet10);
DHCP中继(eth0(192.168.10.20) vmnet10 ; eth1(100.100.100.20) vmnet11) ;
外网客户机(eth0(ip自动获取) vmnet11) ; 将VM的DHCP服务关闭,
关闭防火墙和SELinux

2\) 配置DHCP服务器: 修改/etc/dhcp/dhcpd.conf

subnet 192.168.10.0 netmask 255.255.255.0 { 必须有一个和本机在同一网段的

range 192.168.10.100 192.168.10.110 ;

option routers 192.168.10.20 ;

}

subnet 100.100.100.0 netmask 255.255.255.0 {

range 100.100.100.100 100.100.100.110 ;

option routers 100.100.100.20 ;

}, 重启dhcpd服务,

3\) 配置DHCP中继

网卡配置: 第一块网卡ip=192.168.10.20; 第二块ip=100.100.100.20

安装dhcp, 修改中继配置文件/etc/sysconfig/dhcrelay文件中的:
INITERFACES=\"eth0 eth1\" ; DHCPSERVERS=\"192.168.10.10\"

开启路由转发: 改/etc/sysctl.conf文件: netipv4.ip_foeward = 1 ;
然后使用命令sysctl -p

重启中继服务: systemctl start dhcrelay

测试外网主机: 重启网络

4.5 DNS域名服务

4.5.1 DNS基础

1\) DNS简介

域名(Domain Name),
是由一串用\'.\'分隔的名字组成的Internet上某一台计算机或计算机组的名称,
用于在数据传输时标识计算机的电子方位。具有独一无二, 不可重复的特性。

域名系统(Domain Name
System)是互联网的一项服务。域名解析是把域名指向网站空间ip,
让人们通过注册的域名可以方便访问到网站的一种服务。域名解析就是把域名解析到ip地址的过程。域名解析工作由DNS服务器完成。分为正向解(域名到ip),
反向解析(ip到域名)

格式: [www.google.com](http://www.google.com). (最后一个\'.\'是根域,
可不写; com是顶级域, 由ICANN组织制定和管理; google二级域, 注册域;
www三级域, 服务网站名; 主机名S1.www.google.com)

2\) 域名解析过程

[www.google.com](http://www.google.com)浏览器自动补齐:80(web端口),
客户端首先查询自己的hosts文件,
看看文件内是否有解析记录。如果没有客户端继续查询DNS解析缓存,
直接返回给浏览器。如果没有查看网卡上设置的DNS服务器地址, 访问DNS服务器,
查看域名解析结果, 将ip返给浏览器。

3\) 分布式DNS服务器

同一个任务, 由不同步骤共同完成的过程叫分布式。负载均衡:
将用户的请求分配到多个功能相同的服务器上。

4.5.2 DNS软件

DNS软件bind; 服务名称named; 端口: UDP 53数据通信(域名解析), TCP
53数据同步(主从同步);

主配置文件: /etc/named.conf(服务器运行参数)

options {

listen-on port 53 { any; }; 设置服务器监听网卡, 可以写本机ip,
也可以写any

listen-on-v6 port 53 { ::1;}; ipv6监听网卡,

directory \"/var/named\" ;

dump-file \"/var/named/data/cache_dump.db\" ;

statistics-file \"/var/named/data/named_stats.txt\" ;

memstatistics-file \"/var/named/data/named_mem_stats.txt\" ;
数据文件位置

allow-query { any; } ; 设置可以访问的服务器的客户端ip, 可用any

区域配置文件: /etc/named.rfc1912.zones(服务器解析区域配置,
正反向区域定义信息, 可以将多余的正反向模板删除)

zone \"dev.com\" IN { 正向区域配置标签, 修改为要解析的域, 不是域名

type master ; DNS服务器类型(master/slave)

file \"named.dev.com.zone\" ;
正向数据配置文件名称(默认保存在/var/name/...)

allow-update { none; } ; 是否允许从服务器数据更新的列表(允许填写ip地址,
主填从, 从填主)

};

zone \"10.168.192.in-addr.arpa\" IN { 反向区域配置标签, 将IP反写,
表示10网段

type master ;

file \"named.10.168.192.zone\" ; 反向解析配置文件

allow-update { none; } ;

};

数据配置文件:

正向解析/var/named/named.dev.com.zone

\$TTL 1D #域名有效解析生存周期(一般指缓存时间, 1D指一天)

@ IN SOA dev.com. rname.invalid. ( #SOA标记(起始授权机构资源记录)

0 ; serial #配置文件修改版本

1D ; refresh #更新频率

1H ; retry #更新失败重试周期

1W ; exptre #无法更新失效周期

3H) ; minimum #缓存服务器无法更新失效时间

NS @ #域名的解析

A 127.0.0.1 #ipv4解析

AAAA ::1 #ipv6解析

NS ns.dev.zd. #设置DNS服务器的域名

ns IN A 192.168.100.12 #指向dns服务器

www IN A 192.168.100.11 #为web服务器ip记录

反向解析named.10.168.192.zone

NS @ ; A 127.0.0.1 ; AAAA ::1 #前面同上(这里是三行)

PTR localhost.

NS ns.dev.zd.

ns A 192.168.10.12

11 PTR www.dev.com.

记录类型:

  ----------- -----------------------------------------------------------
  A           地址记录, 指定IPv4地址的记录

  CNAME       将域名指向另一个域名, 再由另一个域名提供ip

  TXT         可以任何东西, 限制长度255, 大多用于做SPF反垃圾邮件

  NS          域名服务器记录, 把子域名交给其它DNS服务商解析

  AAAA        地址记录, 指定IPv6的记录

  MX          邮件交换记录
  ----------- -----------------------------------------------------------

启动服务: systemctl start named

修改客户端网卡配置文件, 将DNS3=192.168.10.11(DNS服务器ip)

注意: a) 区域文件属组为named

b\) 解析内网dns: windows系统: 1. 修改首选dns服务器为192.168.10.198; 2.
重启网卡; 3. ipconfig /renew #重新配置ip; 4. ipconfig /flushdns
#刷新dns缓存; 5. nslookup.exe www.dev.zd #查看是否能继续内网域名; 6.
ping www.dev.zd #测试连接

linux系统: 1. 修改dns1为192.168.10.198; 2. 重启network; 3. nslookup
www.dev.zd

c\) 或者修改dhcp 的dns服务分配

4.5.3 主从DNS服务器

主要是为了减轻主服务器的压力及数据备份: 一台主从服务器, 一台测试机。

搭建过程:

1\) 主服务器: 安装bind, bind-chroot, bind-utils;

修改/etc/named.conf监听本机ip就行;
修改/etc/named.rfc1912.zones将allow-update写为lslave的ip,
修改named.localhost, 启动服务

2)从服务器: 安装bind软件;

修改/etc/named.conf监听本机ip就行; 修改/etc/named.rfc1912.zones改为:

zone \"dev.com\" IN { type slave ; \\n masters { 192.168.100.12; 主ip }
; \\n file \"slaves/google.localhost ; }\"

重启服务

3\) 在测试机上填写从服务器的IP, 并使用(nslookup域名)进行测试。

4.5.4 DNS缓存服务器

加快解析速度, 提高工作效率。

1\) 主DNS服务器同上

2\) 缓存DNS服务器

实验软件: dnsmasq

配置文件: 将从named服务停了, 安装dnsmasq软件, 修改配置文件:
/etc/dnsmasq.conf;

domain=域 (google.com);

server=ip(主DNS服务器IP);

cache-size=15000(声明缓存条数); 重启服务dnsmasq。测试

4.5.5 智能DNS(分离解析)

原理:
DNS分离解析即将相同域名解析为不同的IP地址。实现网络中一些网站为了让用户有更好的体验效果解析速度更快,
就把来自不同运营商的用户解析到相对应的服务器这样就大大提升了访问速度。

实验环境: 一台内网测试机(单网卡); 一台网关+DNS(双网卡);
一台外网测试机(单网卡); 一台web服务器(双网卡)

4.6 VSFTP服务

4.6.1 VSFTP简介

FTP(File Transfer Protocol)文件传输协议,
用于internet上的文件+的双向传输。使用FTP来传输时,
是具有一定的程度的危险性, 因为数据是完全没有受到保护的明文传输。

VSFTP(VerySecure FTP)是基于GPL发布的类Unix系统上使用FTP服务软件,
为了解决ftp传输的安全问题。

1\) 连接类型

控制连接(持续连接)---TCP21(命令信道)---(用户收发FTP命令)

数据连接(按需连接)---TCP20(数据信道)---(用于上传下载数据)

2\) 工作模式: 主动模式和被动模式,
注意由于被动模式是随机端口进行数据传输, 所以在设置防火墙时需要可以开放。

3\) VSFTP传输模式

Binary模式: 不对数据进行任何处理,
适合进行可执行文件、压缩文件、图片等传输;

ASCII模式: 进行文本传输时, 自动适应目标操作系统的结束符, 如回车等。

切换方式: 在ftp下输入ascii或bin切换

4\) 相关信息: 服务端软件名vsftpd; 客户端软件名ftp; 端口: 20, 21,
指定范围端口; 配置文件: /etc/vsftpd/vsftpd.conf

4.6.2 登录验证方式ftp ip

匿名用户登录验证: 用户账户名: ftp或anonymous; 无密码; 工作目录:/var/ftp;
默认权限: 默认可下载不可上传, 上传权限由两部分组成(主配置文件和文件系统)

本地用户验证: 用户账号:本地用户(/etc/passwd);
用户密码:用户密码(/etc/shadow); 工作目录:登录用户的宿主目录;
权限:最大权限(drex\-\-\-\--)

虚拟(virtual)用户验证:创建虚拟用户来代替本地用户, 减少本地用户曝光率;
使用本地用户作为虚拟用户的映射用户, 为虚拟用户提供工作目录和权限控制;
能够设置严格的权限(为每一个用户生成单独的配置文件)

4.6.3 VSFTP实验部署

1\) 匿名用户实验

配置文件/etc/vsftpd/vsftpd.conf

anonymous_enable=YES 启用匿名用户访问

anon_umask=022 匿名用户上传文件权限掩码

anon_root=/var/ftp 匿名用户的FTP根目录

anon_upload_enable=YES 允许上传

anon_mkdir_write_enable=YES 允许创建目录

anon_other_write_enable=YES 开放其它写入权(删除, 覆盖, 重命名)

anon_max_rate=0 传输速率(0不限速)

2\) 本地用户实验, 基本和匿名一样, 将anon换为local

chroot_local_user=YES 将用户禁锢在自己的目录, 不然有r权限的都可以下载

userlist_enable=YES & userlist_deny=YES
禁止/etc/vsftpd/user_list中的用户登录

userlist_enable=YES & userlist_deny=NO
仅允许/etc/vsftpd/user_list中的用户登录

pasv_enable=YES 被动模式开关

pasv_min_port=30000 数据传输使用端口

pasv_max_port=35000

配置文件: /etc/vsftpd/ftpusers文件中的用户禁止, 权限比user_list高,
即时生效

创建用户: useradd -r -s /sbin/nologin username 创建用户, -r是系统用户

3\) 虚拟用户实验

建立FTP的虚拟用户的用户数据文件(/etc/vsftpd/vsftpd.user):

user1 奇数行为用户名

123456 偶数行为密码

对vsftpd.user文件进行加密

db_load -T -t hash -f vsftpd.user vsftpd.db 对文件进行加密

chmod 600 vsftpd.db 修改权限, 保证其安全

创建映射用户: useradd -d /home/virtual(目录) -s /sbin/nologin virtual

建立支持虚拟用户的PAM认证文件, 添加虚拟用户支持

cp -a /etc/pam.d/vsftpd /etc/pam.d/vsftpd.pam (生成自己认证配置文件)

添加以下两行(其它全部删除):

auth required pam_userdb.so db=/etc/vsftpd/vsftpd

account required pam_userdb.so db=/etc/vsftpd/vsftpd

在/etc/vsftpd/vsftpd.conf文件中添加支持配置

修改: pam_service_name=vsftpd.pam

添加: guest_enable=YES 匿名开启

guest_username=virtual

user_config_dir=/etc/vsftpd/dir 这个dir目录需要提前创建,
这里面放配置文件

建立虚拟用户独立的配置文件, 启动服务。注意做虚拟用户配置文件时,
将主配置文件中自定义的匿名用户相关设置注释掉(就是自己添加的anon\_);

直接添加配置文件到/etc/vsftpd/dir目录下, 文件名对应虚拟用户名(user1)

anon_upload_enable=YES(允许上传); anon_mkdir_write_enable=YES(创建目录);
anon_other_write_enable=YES(允许删除,覆盖)

注意需要给映射用户的家目录/home/virtual设置o+r权限, 让虚拟用户有读权限

4.6.4 openssl+vsftpd加密传输

使用tcpdump指定工具抓包, 抓取ftp登录过程中的数据包

tcpdump -i eth0 -nn -X -vv tcp port 21 and ip host192.168.88.1(来源ip)
(-i指定监听网卡, -n对地址以数字显示, -nn把端口显示为数字,
-X输出包的头部数据, -vv详细输出)

openssl+vsftpd加密传输过程: 首先是服务器向CA证书服务器注册,
然后CA证书服务器产生密钥\*.key, 证书\*.csr, 签字后的证书\*.crt,
然后发给服务器。当客户端访问时, 客户端会先下载签字证书\*.crt,
客户端用证书加密后的密码发给服务器。

1\) 实验流程

安装openssl, 查看vsftpd是否支持openssl(ldd /usr/sbin/vsftpd \| grep
libssl)

生成加密信息的密钥和证书文件: /etc/ssl/certs/

openssl genrsa -out vsftpd.key 1024(建立服务器私钥, 生成RSA密钥)

openssl req -new -key vsftpd.key -out vsftpd.csr 生成证书,
需要依次输入国家, 地区, 城市, 组织, 单位, Email等。最重要的是common
name, 可以写名字或域名。如果为了https申请, 必须为域名吻合,
否则会引发服务器报警。生成csr交给CA签名后形成服务端自己的证书。

openssl x509 -req -days 365(时效) -sha256(加密长度) -in vsftpd.csr
-signkey vsftpd.key -out vsftpd.crt 使用CA服务器签发证书,
设置证书的有效期等信息

chmod 500 certs

注意生成完密钥和证书文件后,
必须本目录/etc/ssl/certs/的权限修改为500。在实验中可以用命令生成测试,
在生产环境中必须要在https证书厂商注册(否则浏览器不识别)

修改主配置文件/etc/vsftpd/vsftpd.conf

ssl_enable=YES 开启ssl认证;

ssl_tlsv1=YES ; ssl_sslv2=YES ; ssl_sslv3=YES 开启tlsv1, 2, 3都支持

allow_anon_ssl=YES 允许匿名用户(虚拟用户)

force_anon_logins_ssl=YES ; force_anon_data_ssl=YES
匿名登录和传输强制使用ssl

force_local_logins_ssl=YES ; force_local_data_ssl=YES
本地登录和传输强制使用ssl

rsa_cert_file=/etc/ssl/crets/vsftpd.crt rsa格式的证书

rsa_private_key_file=/etc/ssl/certs/vsftpd.key rsa格式的密钥

注意密钥文件要在配置文件中单独声明(写入配置文件时, 注释要单独一行,
否则会报错)

重启vsftpd服务; 测试

4.7 Samba服务

4.7.1 Samba服务

SMB(server Messages block,
信息服务块)是一种在局域网上共享文件和打印机的一种通信协议,
为局域网内不同操作系统的计算机之间提供文件及打印机等资源的共享服务。SMB协议是客户机/服务器型协议,
客户机通过该协议可以访问服务器上的共享文件系统、打印机及其它资源。

Samba是使用smb/cifs协议、可跨平台、可实现文件系统挂载、可实现服务端修改文件。

1\) Samba软件相关信息

软件名:samba; 协议:SMB/CIFS; 服务名:smb实现资源共享、权限验证 TCP 139
445

配置文件(/etc/samba/): smb.conf主配置文件, smbusers别名配置文件

2\) 登录验证模式(安全级别)

a\) share匿名验证(不需要用户和密码)

b\) user本地用户验证(Samba服务器默认的安全级别, 用户名和密码);
验证方法之一,
tbdsam是使用一个数据库文件来验证。数据库文件叫passdb.tbd。使用pdbedit管理:

pdbedit -a username: 新建Samba账户(将系统用户转化为Samba用户,
并设置独立密码)

pdbedit -x username: 删除Samba账户

pdbedit -L 列出Samba用户列表, 读取passdb.tdb数据库文件。

c\) 别名用户(虚拟用户)

4.7.2 Samba配置

1\) Samnba简单应用

Windows需要开启Samba服务, 在控制面板, 程序功能, 开启服务中Samba服务,
开启后重启。

在Linux上建立Samba用户, useradd -s /sbin/nologin username ; pdbedit -a
username

Windows连接直接用\\\\ip\\直接连接

Windos挂载文件: 右击此电脑, 映射网络驱动器,
文件夹[\\\\ip\\username](file:///\\\\ip\\username)

2\) 配置文件(/etc/samba/)

global全局配置: (主配置文件/etc/samba/smb.conf)

workgroup = WORKGROUP 设置samba server所要加入的工作组或者域

server string = Samba Server Version %v 设定注释, 宏%v表示samba的版本号

interfaces = eth33\|192.168.12.2/24 设置监听那些网卡, 可以是网卡名,
或是网卡ip

hosts allow = 192.168.100. 192.168.200. 表示允许连接到samba
server的客户端, 多个参数以空格隔开, 可以是一个ip, 也可一是一个网段表示,
但host deny于host allow相反（二选一）如: host allow = 172.17.2.
EXCEPT172.17.2.5允许172.17.2.\*.\*连接, 排除172.17.2.5; host allow =
172.17.2.0/255.255.0.0表示允许172.17.2.0/255.255.0.0子网中的所有主机连接

log file = /var/log/samba/log.%设置日志文件保存和名, 宏%m主机名,
表示对每台访问Samba server的机器都单独记录一个日志文件

max log size = 50 设置日志文件的最大容量, 单位为kB, 0代表不限制

security = user 设置安全级别

passdb backend = tdbsam 设置用户访问的验证方式

load printers = yes/no 设置是否在启动Samba时就共享打印机

homes用于设置用户宿主目录的共享属性(特殊共享, 泛指每个用户对应的家目录)

\[homes\] 共享名, 更具具体情况更改\[minj\]

comment = Home Directories 共享描述

browseable = no 共享是否可以被查看, yes能看到所有的, 要求是no

writable = yes 共享是否可写

valid users = %S 允许访问该共享的用户, 如果允许其它用户:valid users =
bob, \@bob多个用户或组用逗号隔开, \@组名

\[printers\]用于设置打印机共享资源的属性(但是现在基本不用)

\[自定义\] 只定义共享区

\[自定义\] 共享名, 使用url连接时需要

comment = the share is xbz 共享描述

path = /share/zdy 共享路径, 需要提前建立

public = yes 是否可以匿名访问, 类似guest ok

browseable = yes 共享是否可以被查看

writable = yes 是否可写(同时设置目录的w权限)

create mask 上传文件默认权限, 注意和umask不一样

directory mask 上传目录默认权限

3\) 配置文件检测

testparm: 若显示\"Loaded services file OK\"表示配置文件语法正确;
-v显示samba所支持的所有有选项。

4\) 访问控制: 写入权限的控制方式

a\) 配置文件开启, 文件系统严格控制(尽量采用)

writable = yes ; setfacl 或chmod 777 目录 (推荐使用setfacl权限)

b\) 文件系统开启, 配置文件严格控制

chmod 777 /dir

read only = yes

write list = 用户, \@组

5\) 服务启动管理

systemctl start\|stop\|restart\|reload smb

systemctl enable samb

6\) 客户端登录方式

Linux端: smbclient -U username -L //服务器ip 查看服务器共享

smbclient -U username //服务器ip/共享名 登录服务器共享

window端: [\\\\服务器ip\\共享名](file:///\\\\服务器ip\\共享名) 登录

net use \* /del 清空登录缓存

4.7.3 Samba部署与实验

自定义实验:

1\) 验证安装samba, samba-client, samba-common

2\) 创建共享区域, 备份配置文件, 创建独立的共享区间(有模板)

3\) 启动smb服务并查看默认共享区域

虚拟用户实验(别名用户):

1\) 添加别名(/etc/samba/smbusers): zhangsan = zs

2\) 启用别名(修改主配置文件/etc/samba/smb.conf): username map =
/etc/samba/smbusers

3\) 测试: smbclient -U 别名 //服务器ip/共享名

映射网络驱动器(挂载):

Linux下: 临时挂载: mount -t cifs -o username=xxx,password=xxx
//服务器ip/服务器共享 /本地挂载目录

永久挂载: /etc/fstab添加: //服务器ip/服务器共享 /本地挂载目录 cifs
defaults, username=xxx,password=xxx 0 0 ; 挂载后使用mount
-a扫描/etc/fstab中未挂载的自动挂载。

Windows下: Windos挂载文件: 右击此电脑, 映射网络驱动器,
文件夹[\\\\ip\\username](file:///\\\\ip\\username)

图形化web管理界面:

1\) 安装相关软件: libsmbcilent, samba, samba-client, samba-common,
samba-swat, samba-winbind, samba-winbind-clients, xinetd(集中管理式服务)

2\) 修改/etc/xinetd.d/swat; 添加only_from = 登录来源ip ; disable = no ;
重启xinetd服务。

3\) 测试: 浏览器: IP: 901注意登录时的端口

4.8 NFS

4.8.1 NFS简介

NFS(network file
system)是网络文件系统。一种使用分散式文件系统的协定。功能是通过网络让不同的机器、不同操作系统能够彼此分享个别的数据,
让应用程序在客户端通过网络访问位于服务器磁盘中的数据,
实现类Unix系统间实现磁盘文件共享的一种方法。

主要功能是通过网络让不同的机器之间可以批次共享文件和目录。允许客户端将远程的NFS服务器的共享目录挂载到本地的NFS客户端中。一般用于储存共享视频,
图片等静态数据。

1\) NFS挂载原理

NFS服务器端是随机选择选择端口来传输数据。NFS服务器通过远程调用(remote
procedure call,
RPC)协议/服务来实现客户端获取服务端NFS端口。也就是说RPC服务会统一管理NFS的端口,
客户端和服务端通过RPC来先沟通NFS使用那些端口,
之后利用这些端口(小于1024)来进行数据传输。

首先NFS启动后, 就会随机的使用一些端口,
然后NFS就会向RPC注册这些端口。RPC就会记录下这些端口。并且RPC会开启111端口,
等待客户端RPC的请求, 如果客户端有请求,
那么服务端的RPC就会将记录的NFS端口信息告知客户端。启动时,
要先启动RPC服务, 然后再启动NFS SERVER。每次修改NFS配置文件后,
直接使用命令/etc/init.d/nfs reload。

2\) NFS相关协议及软件

协议: RPC(Remote Procedure Call Protocol) 远程过程调用协议

软件: nfs-utils-\*: 包含NFS命令与监控程序; rpcbind-\*: 支持安全NFS
RPC服务的连接。

3\) NFS服务器的启动与停止

启动NFS服务器: 服务名rpcbind和nfs; 查询NFS服务器状态; 停止NFS服务器;
rpcinfo -p localhost查看RPC服务器开启了那些端口。

4.8.2 NFS相关实验

1\) 将NFS服务器的/home/zhangsan共享给192.168.115.0网段, rw权限;
编辑/etc/exports;

输入/home/zhangsan 192.168.115.0 (rw)

重启rpcbind和nfs服务;

查看服务端nfs共享状态: showmount -e 本机ip ; 查看客户端nfs共享状态:
showmount -e NFS服务器ip

2\) 客户端挂载nfs服务器共享目录

mount NFS服务器IP:共享目录 本地挂载点目录

mount 192.168.115.10:/home/zhangsan/ /media/zhangsan ; mount \| grep nfs

mount -o vers=3 共享目录 本地目录 指定挂载使用nfs V3版本(避免同步延迟)

4.8.3 相关命令

1\) exportfs \[-aruv\] 启动NFS之后修改/etc/exports,
该命令使得改动立即生效

options: -a全部挂载或卸载export中的内容; -r重新读取export中的信息,
并同步更新/etc/exports、/var/lib/nfs/xtab;
-u卸载单一目录(和-a一起使用为卸载所有/export文件中的目录);
-v再export的时候, 将详细信息输出到屏幕上。

exportfs -au 卸载所有共享目录 ; exportfs -ra
重新共享所有目录并输出详细信息

2\) rpcinfo -p查看RPC开启的端口所提供的程序有那些; 其中nfs开启的是2049,
portmapper(rpcbind)开启的是111, 其余则是rpc开启的

注意rpc服务不止是为NFS提供服务, 所以不能随意停止rpc服务。

4.9 web平台搭建-LAMP

4.9.1搭建环境准备

关闭SELinux和防火墙

1\) 安装编译工具gcc、gcc-c++、make等(注意解决依赖关系, 推荐使用yum安装)

2\) 建议将LAMP环境安装源码包统一放在一个目录下, 可以使用脚本解压;
为了保证软件正常安装, 检测磁盘容量。

3\) 源码软件安装报错确认与解决方案:

echo \$? 每个步骤结束后执行命令

./configure 注意出错关键词

make 一般是数字书写出错, 不存在, 漏写, ./configure --help检查上一步

4.9.2 LAMP源码安装

Apache的依赖软件apr, apr-util, pcre; MySQL的依赖软件ncurses, bison,
cmake; PHP的依赖软件openssl memcache, freetype, zlib, libpng, jpg/jpeg,
mcrypt(依赖libmcrypt, ltdl, mhash)

libxml2是一种xml c语言版的解析器, 支持c, c++, PHP, Pascal, Ruby,
Tcl等语言, 需要提前安装。其依赖软件需要提前安装libxm12-devel
python-devel, 然后再安装libxml2

libmcrypt是加密算法扩展。支持DES, 3DES, RIJNDAEL, Twofish, IDEA, GOST,
GAST-256, ARCFOUR, SERPENT,
SAFER+等算法。安装libltdl也在libmcrypt源码目录中, cd
/lamp/libmcrypt-2.5.8/libltdl, ./configure \--enable-ltdl-install, make,
make install

安装mhash, mcrypt加密扩展库,
在安装mcrypt时需要指定libmcrypt和mhash的库位置, export
LD_LIBRARY_PATH=/usr/local/libmorypt/lib:/usr/local/mhash/lib,
./configure \--with-libmcrypt-prefix=/usr/local/libmcrypt , make , make
install

安装zlib数据压缩用的函数库, ./configure后修改Makefile中的CFLAGS=-03
-DUSE_MMAP -fPIC , 然后make && make install

安装图片库libpng, jpeg6(需要手动创建系列安装目录,
此外还要先安装libtool\*工具, 需要覆盖两个文件)

安装freetype字体引擎

安装Appache, 需要将apr和apr-util复制到指定位置, 手工安装依赖pcre软件,
以及加密模块mod_ssl (openssl-devel), 最后安装httpd时需要指定相关参数

安装MySQL, 先安装Ncuress提供字符终端处理库, 包括面板和菜单,
这里先用yum安装ncurses-devel,
然后再编译安装ncurses-5.9。然后用yum安装cmake和bison。最后安装MySQL,
要先创建mysql运行用户(useradd -r -s /sbin/nologin mysql), 编译安装cmake
指定参数, make, make install, 安装完后修改目录归属, 复制配置文件到/etc,
创建数据库授权表, 初始化数据库(相当于引导设置)

4.9.3 Apache服务

1\) HTML超文本标记语言, 使用html语言编写的文本,
就是指定页面内可以包含图片、链接, 甚至音乐、程序等非文字元素。

HTTP协议: 超文本传输协议,
使用统一资源标识符(URL)来建立连接和传输数据。是一个基于TCP/IP通信协议来传递数据的协议,
属于应用层协议。

URL: 统一资源定位符,
是对可以从互联网上得到的资源的位置和访问方法的一种简洁的表示,
是互联网标准资源的地址:
[http://www.google:80/image/a.jpg。注意URL](http://www.google:80/image/a.jpg。注意URL)则是URI的一个子集。

2\) Apache工作模式

Apache一共有3种稳定的MPM模式(MPM: 多进程处理模块),
分别为prefork、worker、event。

prefork工作模式, 在apache启动之前, 就预先fork一些子进程,
然后等待请求进来,
可以减少频繁创建和销毁进程的开销。每个子进程只有一个线程,
在一个时间点内, 只能处理一个请求。优点是成熟稳定,
不需要担心线程安全问题。缺点是一个进程相对占用更多的系统资源,
并不擅长处理高并发请求。

worker工作模式, 使用多进程和多线程的混合模式,
也预先fork几个子进程(数量较少), 然后每个子进程创建一些线程,
同时包含一个监听线程。每个请求过来, 会被分分配到1个线程来服务,
线程比进程会更轻量, 线程通常会共享进程的内存空间,
因此内存占用减少些。在高并发下比prefork有更多的可用线程。但是必须考虑线程安全问题。

event工作模式(默认): 与worker模式很像, 最大区别在于,
它解决keep-alive场景下, 长期被占用的资源浪费问题。event
MPM种会有一个专门的线程来管理这些keep-alive类型的线程,
当有真实请求过来时, 将请求传递给服务线程, 执行完毕后,
允许它释放。增强了高并发场景下的处理能力。

httpd -V \| grep -i \"server mpm\" 查看apache工作模式;
在编译安装时使用\--with-mpm=xxx指定工作模式

3\) Apache目录结构

配置文件: 源码包安装: dir/etc/hpptd.conf(主配置文件);
dir/etc/extra/\*.conf(子配置文件)

rpm安装: /etc/httpd/conf/httpd.conf

网页位置: 源码包安装: /dir/htdocs/ ; rpm安装: /var/www/html

日志文件位置: 源码包安装: /dir/logs; rpm包安装:/var/log/httpd

4\) apache实验

别名实验, 用户认证实验, 虚拟主机(重点), 域名跳转(用301永久跳转)

/usr/local/httpd/bin/apachectl -t检测配置文件修改的语法错误

4.9.4 Apache+openssl实现https

1\) HTTPS (Hypertext Transfer Protocol Secure, 超文本传输安全协议)

在HTTP下加入SSL层, 用于安全的HTTP数据传输。这个系统被内置于浏览器中,
提供身份验证与加密通讯方法。被广泛用于万维网上安全敏感的通讯,
例如交易等。

准备Apache是否支持SSL, 检查应用模块是否安装, 并启用模块。

模块存放目录: /usr/local/httpd/modules; 检查模块是否启用: apachectl -M

将httpd.conf文件中开启LoadModules ssl_module modules/mod_ssl.so和Include
etc/extra/httpd-ssl.conf

创建/usr/local/httpd/cert目录, 进入进行密钥生成

CA证书申请: openssl gensa -out ca.key 1024(生产RSA密钥); openssl req
-new -key ca.key -out
google.csr生成csr文件交给CA签名后形成服务端自己的证书, 注意common
name后面一定要写域名域名\"www.google.com\"; openssl x509 -req -days 365
-sha256 -in google.csr -signkey ca.key -out
google.crt设置证书有效期。注意生成密钥和证书文件后,
将文件存放在Apache的安装目录下的cert目录下,
在生产环境中必须要在https证书厂商注册(否则浏览器不识别)

修改conf/extra/httpd-ssl.conf配置文件, 调用证书等文件。开启SSLProtocol
all-SSLv2-SSLv3, SSLCipherSuite ....., SSLHonorCipherOrder on,
SSLCertificateFile cert/google.crt和SSLCertificateKeyFile cert/ca.key

修改主配置文件, 添加虚拟主机(在80端口下后面添加,
就是指定目录htdocs段后面)

\<VirtualHost_default\_:443\>

DocumentRoot \"/usr/local/apache2/htdocs\"
注意DocumentRoot目录位置要和httpd.conf一致

ServerName localhost:443

SSLCertificateFile cert/google.crt

SSLCertificateKeyFile cert/ca.kry

SSLCertificateChainFile cert/google.crt

\</VirtualHost\>

Apache语法验证apachectl -t, 如果不报错则没有问题

强制跳转https, 在http部分的目录权限标签中添加以下内容(在80端口内):

\<Directory \"/usr/local/httpd/htdocs\"\>

......

RewriteEngine on 开启转发规则

RewriteCond %{SERVER_PORT} !\^443\$ 检查端口只要不是目标443的

RewriteRule \^(.\*)?\$ [https://%{SERVER_NAME}/\$1]() \[R=301,L\]
全部使用https重新访问

\</Directory\>

2\) apache日志切割

a\) 设置日志的路径名称: 在httpd.conf文件中添加:

Errorlog \"logs/error.log\" 错误日志

CustomLog \"logs/access.log\" combined(或common) 访问日志

而combined/common为日志显示的格式, 如下:

LogFormat \"%h %l %u %t \\\"%r\\\" %\>s %b \\\"%{Referer}i\\\"
\\\"%{User-Agent}i\\\"\" combind

LogFormat \"%h %l %u %t \\\"%r\\\" %\>s %b\" common 简化版

b\) 设置日志分割

ErrorLog \"\|/usr/local/apache2/bin/rotatelogs -l
/usr/local/apache2/logs/error\_%Y%m%d.log 86400\"

CustomLog \"\|/usr/local/apache2/bin/rotatelogs -l

/usr/local/apache2/logs/access\_%Y%m%d.log 86400\" combined 上下是一行

如果开启了https, 则需要修改http-ssl.conf配置文件中日志记录条目

\|管道符号, 将日志交给rotatelog工具, -l是校准时区为UTC, 86400正好一天,
combined同上

c\) 不记录指定文件类型的日志

如果网站访问量特别大, 我们可以忽略一些访问日志, 比如图片, js,
css等静态对象。修改httpd.conf, 相关配置为:

SetEnvIf Request_URL \".\*\\.gif\$\" image-request 忽略掉gif图片日志,
其他可以忽略的有\".\*\\.jpg\$\", \".\*\\.bng\$\", \".\*\\bmp\$\",
\".\*\\swf\$\", \".\*\\js\$\", \".\*\\.css\$\"每个一行

CustomLog \"\|/usr/local ... \_%Y%m%d.log 86400\" combined
env=!image-request 前面和上面一样

3\) Apache配置静态缓存

静态文件指的是图片、js、css等文件(访问量较大),
这些静态文件是会缓存在客户端的浏览器,
目的就是为了下次请求时不再到服务器上加载,
这样提高访问效率。但是这些静态文件不能一直缓存, 所以需要设置一个有效期。

配置静态缓存: 修改/httpf.conf文件, 先开启expires_module
modules/mod_expires.so模块, 然后添加:

\<IfModule mod_expires.c\>

ExpiresActive on

ExpiresByType image/gif \"access plus 1 days\" 其他还有image/jpeg,
image/png, test/css, application/x-javascript, application/javascript,
application/x-shockwave-flash

ExpiresDefault \"now plus 0 min\"

\</IfModule\>

或者使用mod_headers模块, 该模块默认开启了

\<IfModule mod_headers.c\>

\<filesmatch \"\\.(html\|htm\|txt)\$\"\> 表示htm, html,
txt类日志缓存一小时

Header set cache-control \"max-age=3600\"

\</filesmatch\>

\</IfModule\>

4\) 禁止解析PHP, 防止恶意解析

\< Directory /dir/htdocs/data \> 该目录需要创建

php_admin_flag engine off

\<filesmatch \"(.\*)php\"\>

Order deny,allow

Deny form all

\</filesmatch\>

\</Directory \>

4.10 Nginx

4.10.1 Nginx简介

1\) Nginx(engine x)

nginx是一种高性能的HTTP和反向代理web服务器,
同时也提供了IMAP/POP3/SMTP服务。其特点是模块化设计、高可靠性、内存消耗低、支持热部署、并发能力强(最高5万并发)、功能丰富。

2\) 工作模式

Nginx采用一主多从的主从架构。master是使用root身份启动,
主要是负责启动worker, 加载配置文件,
以及系统的平滑升级。其它的工作是交给worker, woker只负责简单web工作,
大部分工作由worker调用的模块来实现。模块之间是以流水线(用户请求)的方式实现功能的。nginx的worker调度采用异步非堵塞方式,
通信模型采用I/O复用机制。nginx同时支持支持sedfile机制,
通过直接操作内核层数据, 减少应用与内核层数据传递。

4.10.2 Nginx基础

1\) nignx相关命令

nginx -t 检测配置文件语法

nginx -s start/stop/quit/reload

nginx -V 查看nginx版本及参数

2\) nginx配置文件结构

配置文件目录: /etc/nginx/ 或/usr/local/nginx/conf/

nginx.conf文件结构:

#comment statement 注释模块

key value; #main模块配置

events { \# 工作模式设置块 }

http { \# web服务全局设置块

server { #虚拟主机块, 具体服务

location / { \# 更具规则匹配服务或设置

}}}

4.10.3 nginx核心参数详解

1\) 全局main块参数

daemon on\|off; #nginx是否为守护进程, 主要用于开发

user www www; #运行用户, 一般为nginx或www, 就是运行worker进程用户

worker_processes auto; #nginx启动主进程数, 也可设置为cpu核心数如2, 4等;

env TZ\|var\[=value\]; #指定环境变量

pid /var/run/nginx.pid; 主进程PID保存文件

events { #指定影响连接处理指令的配置文件上下文

use epoll; #使用epoll模型(多路复用), 对于2.6以上内核,
建议使用epoll模型。其它事件处理方式有: select \| poll \| kqueue \| epull
\| /dev/poll \| eventport, 默认使用最有效的。

worker_connections 1024; #工作进程最大并发连接数, 不能超过最大打开文件数

}

worker_rlimit_nofile 51200; #打开文件描述符最大数量

error_log /path/error.log error; #(main, http, server,
location)错误日志,
error是日志记录格式和级别(该参数需要开启日志调试功能), 可自定义。

load_module modules/xxx.so #加载一个动态模块, 一般是自己安装的模块要加载

3\) http块设置

http{ #网站优化参数, 如识别文件类型, 日志保存格式, 长连接, 压缩策略...

include /etc/nginx/conf.d/\*.conf; #导入其它服务配置,
注意不同文件的变量冲突问题(如: server块外面的配置都属于http,
不同文件有时候一样的话会冲突)

default_type application/octet-stream; #(http, server, location)
#默认响应的mime类型

ignore_invalid_headers on\|off; #(http, server)是否忽略无效名称的头字段

keepalive_requests 100; #(http, server,
location)设置长连接(keep-alive)最大请求数

keepalive_timeout 65s; #(http, server,
location)设置长连接(keep-alive)时间限制

log_format name sting; #(http)指定日志格式, 可以时json等格式

access_log /path/xx.log name \| off \[buffer=64k gzip flush=1m\];
#(http, server, locaion, location-if)设置日志记录路径与格式(name见下),
还可通过if只记录4xx和5xx的日志。注意:
自己安装的nginx要设置日志轮转功能。为减少i/o开销可以加上后面的。

map \$var \$name { #(http)创建一个新变量(name),
其值取决于第一个参数(var)中指定

default test1; #的一个或多个源变量的值, 原值可以使用\~, \~\*正则匹配等

\~\*var1 test2;

} }

4\) server块设置

server { #可设置一具体规则, 如根目录, 安全规则...

listen 443 \[ ssl http2 default_server\]; #监听端口, 或ip:port,
default_server设置默认服务

server_name www.goweb.com; #监听服务名,
可以使用通配符(只能在两端使用)或正则(正则必须以\~开头),
可以通过\$domain调用该域名。

root /home/www; #资源根目录

index index.html; #定义将用作索引的文件,
文件名可以包含变量。当指定多个时,
列表的最后一个元素可以是一个具有绝对路径的文件

etag on\|off; #(http, server, location) #静态资源ETag响应头字段,
集群时关闭。

server_tokens on\|off\|build\|string;
#响应头字段中启用或禁用发送nginx版本, 建议off。

}

5\) location块设置

location /test/ \| /test { #路劲匹配, 对于root这两效果一致,
但alisa必须要和这里一致

root /data; #指定根目录, 访问xx.com/test/x.txt映射到/data/test/x.txt

alias /data; #定义location的替换, 访问xx.com/test/x.txt映射到/data/x.txt

internal; #指定给定的 location只能用于内部请求。

limit_except GET { #限制给定location内允许的http方法

allow 192.168.1.0/32; #(http, server, location, limit_except)
这里只指定了GET(包含HEAD), 其它方法都会限制访问

deny all; #(http, server, location, limit_except) 可以指定ip

}}

location \~ \\.(gif\|jpg\|png)\$ { #正则匹配规则, 匹配修正符:

= 精确匹配(完全匹配), 对于频繁请求可用location = /login加速,
它会停止向下匹配

\^\~ 前缀位置匹配, \^\~ /static/匹配任何以/static/开头的地址,
符合就停止向下匹配

\~ 匹配区分大小写, 会匹配完所有, 相反 !\~

\~\* 匹配不区分大小写, 会匹配完所有, 相反 !\~\*

其中\'\\\'转义符, \'.\'或\'..\'指相对路径。

root /home/images #访问xx.com/test/x.jpg访问的是/home/images/test/x.jpg

}

location \@name { ... } #变量匹配, 用于请求重定向。

location / { 代理设置

proxy_pass http://192.168.100.12:8080; 反向代理

或: fastcgi_pass localhost:9000; #代理到php构建的应用程序的FastCGI服务器

fastcgi_param SCRIPT_FILENAME
\$document_root\$fastcgi_script_name;#确定脚本名

fastcgi_param QUERY_STRING \$query_string; #传递请求参数

}

5\) 其它常用参数

a\) return (server, location, if)

作用: 自定义访问返回资源

return 301 https://\$host\$request_uri; 永久重定向,

return 500 502 503 504 /error.html; 当前访问返回服务端自定义错误信息

b\) try_files (server, location)

作用: 按顺序检查文件是否存在,
返回第一个找到的文件或文件夹(结尾加斜线表示为文件夹),
如果所有的文件或文件夹都找不到, 会进行一个内部重定向到最后一个参数。

try_files \$uri \$uri/ /index.html; #最后一个参数也可以是=404

location / {... try_files /data/xx.html \$uri \$uri/index.html
\$uri.html \@mongrel; }

location \@mongrel { proxy_pass http://mongrel }; #注意:
反向代理中不能用。

c\) error_page (http, server, location, location if)

作用: 指定针对错误显示的url(内部重定向), url可以是变量。

error_page 500 502 503 504 /50x.html; #配合location /50x.html { ... }

error_page 405 =200 https://\$host\$request_uri; #修改响应代码

error_page 404 = \@fallback; location \@fallback { ... };
#将错误传给命名的location.

n\) limit_rate (http, server, location, location if)

作用: 限制客户端的响应传输速率;

if (\$slow) { set \$limit_rate 4k; } #更具特殊条件限速。

4.10.4 nginx重要模块配置

nginx处理TCP/UDP会话模块调用顺序:

Post-accept: 接收客户端请求后的第一个阶段,
调用ngx_stream_realip_module模块

Pre-access: 初步检查访问, 调用ngx_stream_limit_conn_module模块

Access: 实际处理之前的客户端访问限制, 调用ngx_stream_access_module模块

SSL: TLS/SSL 终止, 调用ngx_stream_ssl_module 模块

Preread: 将数据的初始字节读入预读缓冲区中,
用如ngx_stream_ssl_preread_module之类的模块在处理前分析数据。

Content: 实际处理数据的强制阶段, 通常代理到upstream服务器,
或者返回一个特定的值给客户端。

Log: 最后阶段, 客户端会话处理结果将被记录, 调用ngx_stream_log_module。

1\) ngx_http_core_module

http核心模块, 其内嵌变量:

  ------------------------------------- -------------------------------------
  \$arg_name 请求行中name参数;          \$args, \$query_string请求行中参数

  \$request 完整原始请求行              \$request_uri 原始请求uri路径

  \$request_method 请求方法             \$request_body 请求正文

  \$request_length请求长度              \$status 响应状态

  \$remote_addr                         \$remote_port 客户端端口
  客户端地址(不在代理后端)              

  \$server_addr 接收请求服务器地址      \$server_port接收请求服务器地址端口

  \$host_name 主机名                    \$scheme 请求模式, http或https

  \$time_local 本地时间                 \$limit_rate 设置现实响应速率

  \$document_root                       
  根目录或别名指令的当前请求值          

  \$uri, \$document_uri 映射后的uri     
  (本地文件路径或代理)                  

  \$host主机名, Host请求头字段的主机名, 
  或与请求匹配的服务器名(www.xx.com)    

  \$server_protocol 请求协议HTTP/1.1,   
  HTTP/2.0等                            
  ------------------------------------- -------------------------------------

2\) ngx_http_proxy_module

作用: 模块允许将请求传递给另一台服务器

参数: (http, server, location)

proxy_pass url; #设置代理服务器的协议、地址以及应映射位置的可选 URI

注意: location /name/ { #请求/name/page.html

proxy_pass http://192.x.x.x/remote/; #发送到后端/remote/page.html

proxy_pass http://192.x.x.x/ ; #发送至后端/page.html

proxy_pass http://192.x.x.x或 http://192.x.x.x\$request_uri;
#发送至后端/name/page.html

}

proxy_hide_header field; #隐藏向客户端响应传递头(header)字段

proxy_pass_header field; #在向客户端响应传递头(header)中添加字段

proxy_http_version 1.0 \| 1.1; #设置代理http协议版本,
建议1.1配合keepalive

proxy_redirect https://xx.com/; #改写代理服务器响应url

proxy_set_header filed value; #重新定义或附加请求header传给代理服务器

proxy_pass_request_body on\|off; #是否向上游服务器发送请求体

内嵌变量:

\$proxy_host #proxy_pass指令中指定的代理服务器的名称和端口

\$proxy_port #proxy_pass指令中指定的代理服务器的端口或协议的默认端口

\$proxy_add_x\_forwarded_for #X-Forwarded-For客户端请求头字段,
附加了\$remote_addr 变量。若客户端请求头中不存在 X-Forwarded-For,
其值等于\$remote_addr变量。

3\) ngx_http_headers_module

作用: 允许将 Expires和Cache-Control头字段以及任意字段添加到响应头中

参数:(http, server, location, location-if)

add_header name value; #将指定的字段添加到响应报头中

add_trailer number size; #将指定的字段添加到响应的末尾

expires time\|max\|off;
#则启用或禁用添加或修改Expires和Cache-Control响应头字段

4\) ngx_http_realip_module

作用: 当nginx处于一个反向代理的后端时获取到真实的用户IP,
用于日志记录等。

参数(http, server, location):

set_real_ip_from 10.10.10.10; #受信任地址, 代理服务器的内外网ip都要指定

real_ip_header X-Forwarded-For;
#真实ip位于http扩展头部的X-Forwarded-For中。

real_ip_recursive on; #递归收索真实ip,
on用户ip为XFF中最后一个不受信任的ip, off表示用户ip为XFF中最后一个ip

内置变量:

模块将\$remote_addr和\$remote_port从负载平衡器IP和端口重写为原始客户端IP地址和端口。\$realip_remote_addr和\$realip_remote_port端口变量将保留负载均衡器的地址和端口

注意: 需要添加listen 80\|443 ssl proxy_protocol;
表示可以接受PROXY协议头,
此时\$proxy_protocol_addr和\$proxy_protocol_port也表示客户端 IP
地址和端口

5\) ngx_http_v2_module

作用: 提供对 HTTP/2 的支持

参数(http, server):

listen 443 ssl http2; #server 开启http2

http2_idle_timeout 3m; #设置连接关闭后不活动时间。

http2_max_concurrent_pushes 10; #限制一个连接的最大并发推送请求数。

http2_max_requests 1000; #设置可以通过一个 HTTP/2
连接提供服务的最大请求数量

http2_push uri(绝对路径) \| off; 抢先向指定的 uri
发送(推送)请求以及对原始请求响应。

内置变量: \$http2 协商的协议标识符。

6\) ngx_http_upstream_module

作用: 定义可被proxy_pass、fastcgi_pass等指令引用的服务器组

参数: (upstream)

upstream name { \... } #(http)定义一组服务器, 见4.10.5案例

server address \[params\]; #定义服务器及参数,

zone name \[size\]; #定义共享区域名称和大小

ip_hash; #保证同一客户端始终传递到同一服务器

hash \$requset_uri; #基于hash映射服务器, 相同url到同一服务器

keepalive 32; #在每个worker缓存中保留的upstream服务器最大空闲 keepalive
连接数。

keepalive_requests 100; #可通过一个 keepalive 连接提供的最大请求数

keepalive_timeout 60s; #设置超时时间

least_conn; #指定组应使用将请求传递到活动连接数最少的服务器

zone test 10M; #设置共享内存空间名和大小

queue number \[timeout=60\]; #设置请求队列最大长度及保留时间

内部变量:

\$upstream_addr #保存IP地址和端口

\$upstream_connect_time #与upstream服务器建立连接所花费的时间

\$upstream_cache_status #保存访问响应缓存的状态

\$upstream_status #从upstream服务器获得的响应的状态代码

7\) ngx_http_ssl_module

作用: 提供https过滤

参数(http,server):

listen 443 \[ssl\]; #server,
监听ssl的443端口(ssl参数在0.7.14前的版本要加)

ssl_buffer_size 16k #对于大请求保持默认, 小请求可设置为4k, 减小cpu开销。

ssl_certificate file; #ssl证书文件, 会被发送到客户端, .crt,
server-cert.pem

ssl_certificate_key file; #服务器私钥文件, .pem, server-key.pem

ssl_dhparam file; #为DHE密码指定DH参数文件。

ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:...; #加密的密码套件,
常用的在前。

ssl_protocols TLSv1.2 TLSv1.3; #指定协议版本

ssl_prefer_server_ciphers on \| off; #使用SSLv3和TLS协议时,
优先使用服务器密码

ssl_client_certificate file; #如果启用了ssl_stapling,
则指定一个有可信CA的PEM证书文件,
该证书用于验证客户端证书和OCSP响应。证书列表将被发送到客户端

ssl_trusted_sertificate file; #同上, 但这些证书的列表不会发送给客户端

ssl_stapling on \| off; #启用或禁用服务器OCSP stapling; 要配合resolver
ip(OCSP响应服务器)使用, 还要知道服务器证书颁发者的证书。

ssl_stapling_file file;
#通过指定文件响应OCSP而不是到resolver服务器中查询。

ssl_stapling_verify on \| off; #启用或禁用服务器对 OCSP 响应的验证。

ssl_ocsp on; #客户端证书验证, 1.19版, 配合上一个参数和resolver
192.0.2.1;

ssl_verify_client on \| off \| optional \| optional_no_ca;
#启用客户端证书验证

ssl_verify_depth number; #设置客户端证书链验证深度。

ssl_early_data off \| on; #禁用或启用tls1.3 early
data。on时为了防止带有Early data
的请求将受到重放攻击可以设置proxy_set_header Early-Data
\$ssl_early_data;

ssl_session_cache off \| none \| \[builtin\[:size\]\]
\[shared:name:size\]; #设置存储会话参数缓存类型和大小。off关闭,
none轻度使用, builtin仅有一个worker使用, shared所有worker共享,
后面两个可以同时使用, 如: ssl_session_cache builtin:1000 shared:SSL:10m;

ssl_session_timeout 5m; #指定客户端可以重用会话参数的时间。

ssl_session_tickets on \| off;
#通过tls会话ticket启用或禁用会话重用。将会话缓存保存在客户端,
是ssl_session_cache的一种替代方式。

ssl_session_ticket_key file;
#设置一个用于加密和解密TLS会话ticket的密钥文件。如果在多个服务器之间共享相同密钥时必须指定。默认使用随机生成密钥。密钥文件生成openssl
rand 80 \> ticket.key (80对应AES256, 40对应AES128)

错误处理: 495 客户端证书验证期间发生错误; 496 客户未提交所需证书; 497
已将常规请求发送到 HTTPS 端口

内嵌变量:

8\) ngx_http_rewrite_module

作用: 使用PCRE正则表达式更改请求 URI, 返回重定向和有条件地选择配置。

参数:(server, location, if)

break; #停止处理当前的ngx_http_rewrite_module指令集

if (condition) { \... }; #(server, location)
为否的条件是0或false。比较运算符=, !=; 匹配\~, \~\*, !\~, !\~\*,
文件是否存在-f, !-f; 目录是否存在-d, !-d; 是否存在文件或目录-e, !-e;
文件是否为可执行文件-x, !-x。

return \[code\] \[URL\|text\]; #停止处理并将指定的code返回给客户端

rewrite regex replacement \[flag\] #指定的正则表达式与请求URI匹配,
url根据replacement中指定进行更改, flag值有: last(停止处理当前,
并根据更改搜索location), break(停止当前处理),
redirect(返回302临时重定向), permanent(返回301永久重定向)

rewrite_log off\|on; #启用或禁用将notice级别日志记录到error_log

set \$variable value; #为指定的变量设置一个value

9\) ngx_http_gzip_module

作用: 使用gzip压缩响应

参数(http, server, location):

gzip on \| off; #开启或禁用gzip

gzip_buffers 32 4k; #用于压缩响应的缓冲区数量和大小, 也可用16 8k。

gzip_comp_level level; #gzip压缩级别(1-9), 级别越低压缩比越小。

gzip_disable regex \...;
#禁用对与正则匹配的User-Agent头字段的请求响应做gzip处理

gzip_min_length 2k; #设置被压缩响应的最小长度, 0表示所有都压缩。

gzip_http_version 1.1; #设置用gzip压缩的http最小版本

gzip_proxied off \| expired \| no-cache \| no-store \| private \|
no_last_modified \| no_etag \| auth \| any;
#启用或禁用针对代理请求的gzip(nginx做反向代理时的参数,
响应头包含设置参数则启用压缩)

gzip_types text/html; #设置需要压缩的MIME类型, \*表示所有类型都进行压缩

gzip_vary on \| off; #是否增加响应头\"Vary: Accept-Encoding\"

gzip_static off \|on;
#是否启用将预压缩文件传输给客户端(\--with-http_gzip_static_module)

10\) ngx_http_fastcgi_module

作用: 允许将请求传递给 FastCGI 服务器

参数: (http, server, location)

fastcgi_pass address; #设置FastCGI服务器的地址(ip:port)

fastcgi_param parameter value; #设置传递给FastCGI服务器的参数,
必须指定。

fastcgi_keep_conn off\|on; #FastCGI
服务器响应后是否保持连接处于打开状态, 对keepalive极为重要

fastcgi_pass_header field;
#允许从FastCGI服务器向客户端传递隐藏禁用的头字段

fastcgi_limit_rate 0; #限制读取 FastCGI 服务器响应的速度, s.

fastcgi_cache_bypass string; #定义不从缓存中获取响应的条件

fastcgi_no_cache string; #定义响应不会保存到缓存中的条件

fastcgi_index index.php;
#在\$fastcgi_script_name变量的值中设置一个文件名, 该文件名追加到 URL
后面并以一个斜杠结尾。

fastcgi_hide_header field; #隐藏响应头字段特定字段。

fastcgi_ignore_headers field \...;
#禁止处理来自FastCGI服务器的某些响应头字段。

内嵌变量:

\$fastcgi_script_name #用于设置SCRIPT_FILENAME和PATH_TRANSLATED参数,
以确定PHP中的脚本名称

\$fastcgi_path_info #由fastcgi_split_path_info指令设置的第二个捕获值,
设置PATH_INFO参数

11\) ngx_http_grpc_module 模块

作用: 允许将请求传递给 gRPC 服务器

参数:(http, server, location)

grpc_pass address; #(location, location-if), 设置 gRPC 服务器地址

grpc_ssl_verify off\|on; #禁用或启用gRPC ssl证书

12\) ngx_http_addition_module

作用: 在站点响应之前或者之后追加文本内容

参数(http, server, location):

add_before_body /path/xx.html; #响应前添加, 也可以是css, js等

add_after_body uri; #响应后追加

addition_types text/html \| \*; #允许添加的文件类型, \*表示所有。

13\) ngx_http_uwsgi_module

作用: 允许将请求传递到uwsgi服务器(python服务器)。

参数: 类似proxy_pass

n\) nginScript

nginScript 是JS语言的一个子集,
其可在http和stream中实现位置(location)和变量处理器。其需要单独下载ngx_http_js_module和ngx_stream_js_module两个模块安装

4.10.5 Nginx案例

1\) Nginx的状态统计及目录保护

location /nginx_status{

stub_status on;

access_log off; 状态统计的访问关闭日志记录

auth_basic \"Welcome to nginx\"; 登陆提示符, 提示要用户和密码

auth_basic_user_file /path/htpasswd.nginx; 密码文件(格式: user:passwd)

allow 192.168.88.1; 设置允许访问地址

deny all; 拒绝访问地址

}

注意: 密码文件需要手动创建, htpasswd -c /path/htpasswd.nginx userNmae

2\) nginx的反向代理

a\) server {

listen 80;

server_name www.test.com ;

location / {

proxy_pass http://127.0.0.1:8085;
将[www.test.com](http://www.test.com)跳转到本机8085端口

proxy_set_header Host \$host; #(含下)使后端能够获得client端http请求信息

proxy_set_header X-Real-IP \$remote_addr;

proxy_set_header X-Forwarded-For \$proxy_add_x\_forwarded_for;

proxy_set_header X-Forwarded-Proto \$scheme;

proxy_buffers 16 4k; #请求缓存区设置, 有大页面需要调大

proxy_busy_buffers_size 8k;
#nginx在没有读完后端响应就开始向client发数据,
该参数就是设置这部分数据缓冲区大小, 一般为proxy_buffer单个的2倍。

proxy_buffer_size 2k; #后端响应头大小

}}

注意: 如果nginx本身就在代理中, 则需要使用另外的proxy_set_header配置。

b\) server {

server_name www.test.com;

if (\$host = www.test.com) {

return 301 <https://test.com$request_uri>; #重写实现反向代理(永久重定向)

\# rewrite \^ https://bbs.google.com\$request_uri permanent; \# (同上,
不推荐使用)

}}

3\) 负载调度(负载均衡Load Balance)

upstream bbs { 服务器池bbs (可以是其它名字)

server 192.168.88.100:80 weight=2; #weight表示加权轮询,
权重越大调用次数越多

server 192.168.88.200:80 max_fails=1 fail_timeout=10s(默认); #健康检测,
10s有2两次请求失败, 则判定节点不可用, 10s后重新检测节点健康状况。

注意: upstream默认使用rr轮询, 其它轮询配置(只能用一个):

least_conn; #最少连接, 可以和加权轮询配合使用

ip_hash; #会话持久连接, 可以和加权轮询配合使用

keepalive 32; #每个worker保持最大空闲连接数, 注意server需要有配合参数

}

server {

.....

location / {

proxy_pass http://bbs; #添加反向代理, 地址写upstream声明名字

proxy_set_header Host \$host; #重写请求头部, 保证网站页面可用

health_check \[parameters\]; #定义健康检测

}}

4\) WebSockter代理

将http/1.1转换为websockter需要使用协议切换机制。

http {

map \$http_upgrade \$connection_upgrade {
#connection头字段取决于upgrade字段

default upgrade;

\'\' close;

}

server {

......

location /chat/ {

proxy_pass http://backend; #对于反向代理的情况

proxy_http_version 1.1;

proxy_set_header Upgrade \$http_upgrade;
#客户端通过upgrade头来请求协议交换

proxy_set_header Connection \$connection_upgrade; #是否建立连接

proxy_read_timeout 60s; #代理服务器60s内没有发送数据, 连接将会关闭。

}

}

5\) nginx安全配置

server {

listen 443 ssl; ......

keepalive_timeout 70; #设置长连接, 最大75s(必须)

ssl_session_cache shared:SSL:1m; #配置共享会话缓存(按需设置, 1m \~=
4000个会话)

ssl_session_timeout 10m; #会话超时

ssl_certificate .../xxx.crt; #证书文件

ssl_certificate_key .../xxx.key; #私钥文件

ssl_protocols TLSv1.2 TLSv1.3; 版本控制协议

ssl_prefer_server_ciphers on; #优先采取服务器算法

ssl_dhparam .../dhparam.pem; #指定dh文件

ssl_ciphers HIGH:!aNULL:!MD5; #定义加密算法

add_header Strict-Transport-Security
\"max-age=31536000;includeSubDomains;preload\" always;
#HSTS强制使用https访问连接, max-age:强制使用https时间段;
includeSubDomain:所有子域名都生效; preload使用hsts预加载;
always保证所有响应都发送此响应头。

add_header X-Frame-Options DENY\|SAMEORIGIN; #frame嵌入(减少劫持),
禁止\|允许相同域名

add_header X-Content-Type-Options nosniff; #禁止服务器自动解析资源类型

add_header X-Xss-Protection 1; #防XSS攻击

}

4.10.6 常见问题

1\) server_name错误

could not build the server_names_hash, you should increase
server_names_hash_bucket_size:

服务器名称过多或过长, 在http块修改缓存: server_names_hash_bucket_size
64; (值为提式2倍), server_names_hash_max_size 512;
(设置为接近server_name数量的值)

4.11 Openssl

4.11.1 加密简介

1\) 加密分类

对称加密: 采用共同密钥, 算法有DES, 3DES, AES等

非对称加密: 采用私钥和公钥, 算法有RSA, DSA, ELGama等

单向加密: 只能加密不能解密, 常见算法MD5, SHA1, SHA256等。

2\) https加密验证过程

服务端(server)生成私钥, 然后要求生成证书(csr), 提交给CA(Certificate
Authority), 获得一张证书(包括了服务端的公钥,
CA使用其私钥对服务端的公钥进行加密后得到的签名)。

此前CA需要先给自己颁发一个根证书, 用来给其他主体的证书请求颁发证书。

最后是客户端(client)在访问HTTPS网站时, 收到了服务端发来的证书,
此时客户端要向CA查询这张证书是否合法(用CA自己的证书验证)。(CA的根证书会保存在浏览器中)。合法则从服务端证书中导出公钥进行加密传输。

4.11.2 openssl管理ssl证书

1\) 生成CA根证书(CA机构的事)

创建ca私钥: (无密码)

openssl genrsa -out ca-key.pem 2048 #(推荐.pem(ASCII),
-des3用.key(二进制))

创建ca csr证书请求:

openssl req -new -key ca-key.pem \\ #提交给ca

-sha256 -out ca-req.csr \\ #.csr是向CA发起认证请求的中间文件,
有公钥和名字信息

-subj \"/C=CN/ST=BJ/L=BJ/O=BD/OU=BD/CN=CS\[/emailAddress=xx@g.com\]\"
#见下

生成根证书(用自己(CA)的私钥来签名):

openssl x509 -req -days 3650 \\ #x509签名参数

-in ca-req.csr -signkey ca-key.pem -sha256 \\ #-sha256指定摘要算法

-out ca-cert.pem #ca颁发的证书文件

2\) 生成服务端证书

创建服务端私钥: openssl genrsa -out server-key.pem 2048

创建csr证书请求:

openssl req -new -key server-key.pem \\

-sha256 -out server-req.csr \\

-subj \"/C=CN/ST=SH/L=SH/O=TM/OU=TM/CN=CS\"

生成证书(用根证书和CA的私钥来签名):

openssl x509 -req -days 3650 \\

-in server-req.csr -signkey server-key.pem -sha256 \\

-CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial \\

-out server-cert.pem

3\) 确认证书

openssl verify -CAfile ca-cert.pem server-cert.pem

#可以在浏览器中导入ca-cert.pem取消警告

#windos中安装ca证书，直接双击安装，证书存储选择 "受信任的根证书颁发机构"

4\) 生成dh文件

openssl dhparam \[-rand rand.seed\] -out dh.pem 2048 #使用rand种子会更快

\# dh(Diffie-Hellman)是密钥交换协议, 用于client和server协商加密算法,
保护密钥交换的过程

5\) openssl生成密码: openssl rand -base64 10

4.11.3 cfssl管理ssl证书

1\) 导入配置文件模板

cfssl print-defaults config \> config.json

cfssl print-defaults csr \> csr.json

一般用于集群内部创建ssl验证

2\) 生成CA根证书

创建配置文件a), 证书请求配置文件b)

cfssl gencert -initca ca-csr.json \| cfssljson -bare ca

将会生成私钥ca-key.pem, 证书文件ca.pem

3\) 生成服务端证书

c)为服务器证书请求配置文件

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem \\

-config=config.json -profile=www \\

server-csr.json \| cfssljson -bare etcd

配置表

+-----------------------+---------------------+-----------------------+
| a)cat \> config.json  | b\) cat \>          | c\) cat \>            |
| \<\< EOF              | ca-csr.json \<\<    | server-csr.json \<\<  |
|                       | EOF                 | EOF                   |
| {                     |                     |                       |
|                       | {                   | {                     |
| \"signing\": {        |                     |                       |
|                       | \"CN\": \"CA\",     | \"CN\": \"etcd\",     |
| \"default\": {        |                     |                       |
|                       | \"key\": {          | \"hosts\": \[         |
| \"expiry\":           |                     |                       |
| \"43800h\"            | \"algo\": \"rsa\",  | \"192.168.10.0\",     |
|                       |                     |                       |
| },                    | \"size\": 2048      | ......                |
|                       |                     |                       |
| \"profiles\": {       | },                  | \],                   |
|                       |                     |                       |
| \"www\": {            | \"names\": \[       | \"key\": {            |
|                       |                     |                       |
| \"expiry\":           | {                   | \"algo\": \"rsa\",    |
| \"43800h\",           |                     |                       |
|                       | \"C\": \"CN\",      | \"size\": 2048        |
| \"usages\": \[        | #国家               |                       |
|                       |                     | },                    |
| \"signing\",          | \"ST\": \"BJ\",     |                       |
|                       | #省份               | \"names\": \[         |
| \"key encipherment\", |                     |                       |
|                       | \"L\": \"BJ\",      | {                     |
| \"server auth\",      | #城市               |                       |
|                       |                     | \"C\": \"CN\",        |
| \"client auth\"       | \"O\": \"CA\",      | \"ST\": \"BJ\",       |
|                       | #公司               |                       |
| \]}                   |                     | \"L\": \"BJ\", \"O\": |
|                       | \"OU\": \"system\"  | \"etcd\",             |
| \"server\": {...}     | #部门               |                       |
|                       |                     | \"OU\": \"system\"    |
| }}}                   | }\]}                |                       |
|                       |                     | }\]}                  |
| EOF                   | EOF                 |                       |
|                       |                     | EOF                   |
+-----------------------+---------------------+-----------------------+

参数:

a\) ca-config.json 用于创建证书的配置文件

\"signing\" #表示该证书可用于签名其它证书

\"expiry\": \"43800h\" #证书有效期, \"43800h\"是5年, \"87600h\"是10年

\"www\" #标识符, 标识不同场景证书配置, 可指定多个

\"key encipherment\" #密钥加密

\"server auth\" #表示client可用该CA对server提供的证书进行验证

\"client auth\" #表示serve可以用该CA对client提供的证书进行验证

b\) xxx-csr.json 自建CA的证书请求文件

\"CN\": \"CA\", #common name, 浏览器使用该字段验证网站是否合法,
一般为xxx.com

\"hosts\" #哪些主机名(域名)或IP可以使用此csr申请的证书, 不指定或\"
\"表示所有的都可以用

4.11.4 certbot管理ssl证书

1\) 使用certbot自动安装ssl证书

需要先配置DNS解析, 不然会报错。

填好xxx.conf文件中server和location基本内容, 验证语法错误nginx -t。

certbot \--nginx, 填写基本内容, 自动生成证书, 完后重启nginx

2\) cerbot基本命令

certbot certificates 列出本机管理的所有ssl证书信息, 包括过期信息。

certbot \[-q\] renew 更新所有证书, -q静默输出

certbot run\|certonly\|renew\|delete\|certificates \--cert-name
example.com \[-d example.org\] 其中: \--cert-name指定证书名,
-d指定域名example.org,www.example.org (证书名优先级高)

certbot revoke \--cert-path dir/.../cert.pem \--reason keycompromise
吊销证书, 注意要先吊销证书, 然后再delete证书,
\--reason有unspecified(未指定), keycompromise(关键妥协),
affiliationchanged(关系更改), superseded(被取代), and
cessationofoperation(停止操作)。

老版: certbot register \--update-registration \--email 108329460@qq.com
更改通知邮箱

新版: certbot update_account -m 108329460@qq.com

删除证书中的无效域名, 只需要更新证书即可, 将不需要的域名排除: certbot
\--cert-name xx.com -d aa.com -d bb.com

注意: certbot申请的证书有效期90天, 需要自动续期

4.12 日志分析

4.12.1 ELK简介

为了解决日志量太大的归档和日志搜索问题, 需要对日志进行集中化管理,
一个完整的集中式日志系统, 需要有以下几个主要特点:
收集-能够采集多种来源的日志数据;
传输-能够稳定的把日志数据传输到中央系统; 存储-如何存储日志数据;
分析-可以支持UI分析;
警告-能够提供错误报告。监控机制ELK提供了一整套解决方案,
并且都是开源软件, 之间相互配合使用, 完美衔接, 高效满足多种应用场景。

ELK是Elasticsearch, Logstash, Kibana的缩写, 现在新增了FileBeat。

Elasticsearch是一个开源分布式搜索引擎,
提供搜索、分析、存储数据三大功能。其特点是分布式, 自动发现,
索引自动分片, 索引副本机制, restful风格接口, 多数据源, 自动搜索负载等。

Logstash用于日志的搜索、分析、过滤日志的工具,
支持大量数据获取方式。一般工作方式c/s架构, client端安装在收集日志主机,
server端负责将收到的各节点日志进行过滤、修改等操作并发到Elasticsearch

Kilbana可以为Logstash和Elasticsearch提供的日志分析有好的web界面,
可以帮助汇总, 分析和搜索重要数据日志。

FileBeat是一个轻量级的日志收集处理工具(Agent), 占用资源少,
适用于各个服务器上搜集日志后传输给Logstash。录属于Beats有四种:
Packetbeat(搜集网络流量数据),
Topbeat(搜集系统、进程和文件系统级别的CPU和内存使用情况等数据),
Filebeat(搜集文件数据), Winlogbeat(搜集Windows事件日志数据)

4.12.2 ELK实验部署

本次部署的是filebeats(客户端),
logstash+elasticserach+kibana(服务端)组成架构。当业务请求到达nginx;
nginx响应请求的日志记录在access.log文件中增加访问记录;
FileBeat收集新增日志, 通过LogStash的5044端口上传日志;
LogStash将日志信息通过9200端口传入ElasticSerach;
搜索日志的用户通过浏览器访问Kibana, 服务器端口是5601;
Kibana通过9200端口访问ElasticSerach。

下载ELK工具, 安装jdk环境。

a\) 配置elasticsearch:

useradd elasticsearch

chown -R elasticsearch.elasticsearch /usr/local/ elasticsearch-6.2.3

su - elasticsearch ; cd /usr/local/ elasticsearch-6.2.3 ;
./bin/elasticsearch -d

查看是否启动成功(需要等一会): netstat -antp;
若是出现错误可以查看日志/usr/local/elasticsearch-6.2.3/logs/elasticsearch.log;
测试是否可以正常访问: curl localhost:9200

b\) 配置logstash

收集nginx日志之使用grok过滤插件解析日志, 支持根据模式解析文本日志行,
拆成字段。logstash中grok的正则匹配, 修改配置文件(更具日志文件格式设置)

vendor/bundle/jruby/2.3.0/gems/logstash-patterns-core-4.1.2/patterns/grok-patterns如下:

WZ(\[\^\]\*)

NGINXACCESS %{IP:remote_ip} \\- \\-
\\\[%{HTTPDA:timestamp}\\\]\"%{WORD:method} %

{WZ:request} HTTP/%{NUMBER:httpversion}\" %{NUMBERR:status}
%{NUMBER:bytes} %

{QS:referer} %{QS:agent} %{QS:xforward} 以上是一行数据

创建logstash配置文件: /usr/local/logstash-6.2.3/default.conf

input {

beats {

port =\> \"5044\"

}

}

filter { 数据过滤

grok {

match =\> { \"message\" =\> \"%{NGINXACCESS}\" }

}

geoip { nginx客户端ip

source =\> \"192.168.88.110\"

}

}

output { 输出配置为本机9200端口, 这是ElasticSerach服务器的监听端口

elasticsearch {

hosts =\> \[\"127.0.0.1:9200\"\]

}

}

进入到/usr/local/logstash-6.2.3目录下, 执行下列命令启动logstash:

nohup bin/logstash -f default.conf &

查看启动日志: tailf nohup.out ; 查看端口是否启动: netstat -napt \| grep
5044

c\) 配置kibana

打开/usr/local/kibana-6.2.3-linux-x86_64/config/kibana.yml, 并修改

server.host: \"localhost\"修改为server.host:
\"192.168.88.100\"本地ip地址

进入Kibana的目录: /usr/local/kibana-6.2.3-linux-x86_64; 执行启动命令:

nohup bin/kibana &

查看启动日志: tail -f nohup.out ; 查看端口是否启动: nestat -napt \| grep
5601

d\) 测试, 访问192.168.88.100:5601, 查看ELK部署

e\) Nginx客户端配置

安装nginx, 下载filebeat并解压到/usr/local,
打开文件/usr/local/filebeat-6.2.3-linux-x86_64/filebeat.yml,
修改以下三处:

enable: false 修改为true

path: /var/log/\*.log 修改为/var/log/nginx/\*.log

#output.elasticsearch: ; #hosts: \[\"localhost:9200\"\] ; 注释掉这两行

output.logstash ; host: \[\"ip:5044\"\] 取消注释,
最后一个ip是ELK服务器地址

切换到/usr/local/filebeat-6.2.3-linux-x86_64 ;

启动filebeat命令: nohup ./filebeat -e -c filebeat.yml &

查看日志: tail -f nohup.out

f\) 通过浏览器访问nginx服务器, 制造访问日志。访问Kibana,
点击Discover查看收集到的日志。然后完成以下设置:

输入logstash-\*, 点击Next step; 选择Time Filter, 再点击Create index
pattern; 然后自行创建日志内容规则。

4.12.3 Sentry

sentry是一个错误监控和收集工具, 分为客户端与服务端, 支持多种语言和框架。

4.13 其它服务

4.13.1 企业邮件服务

1\) 邮件概述

电子邮件服务器是处理邮件交换的软硬件设置的总称,
电子邮件程序、电子邮箱等。为用户提供基于E-mail服务的电子邮件系统,
通过访问服务器实现邮件交换。

服务器端: Sendmail(安全欠佳), Postfix(模块化设计,
效率、稳定性、性能及安全性优异), Qmail(模块化设计, 速度快、执行效率高,
配置稍微复杂)

客户端: Outlook, foxmail, 浏览器等,

2\) 邮件传输协议

SMTP 简单传输协议, 发邮件TCP 25端口, 加密使用TCP 465端口。

POP3 第三版邮局协议, 收邮件TCP 110, 加密时使用TCP 995。

IMAP4 第四版互联网邮件访问协议, 收邮件TCP 143, 加密使用TCP 993。

3\) 邮件相关软件

软件名: Postfix; 主目录: /etc/postfix; 主配置文件: main.cf

myhostname邮件服务器主机名, mydomain邮件域,
myorigin设置允许发信的用户邮件域;
mydestination设置允许收信的用户的邮件域

4\) 邮件服务器的发件方

首先搭建DNS服务器, 安装服务软件bind,
修改配置文件named.conf中的监听地址呵访问控制为any,
修改named.rfc1912.zones中使用extamil.org作为解析域,
只保留正向解析即可。修改named/extmail.localhost文件如下

NS dns.extmail.org

MX3 mail.extmail.org

dns A 192.168.88.10

mail A 192.168.88.10

在另一台上使用nslookup测试dns是否能解析

安装gcc\*, mysql-server, mysql, httpd,
mailx等依赖软件。设置mysql和httpd开机自启动。将web页面放到Apache网页目录下。将成品数据库文件导入mysql中(没设置密码,
空密码登录)

将邮件模板拷贝到邮件服务器的主目录下; 创建映射用户&修改配置文件;

重启服务, 并测试。

5\) 邮件服务器的收件放配置

先安装dovecot, dovecot-devel, dovecot-mysql软件

配置dovecot能够去数据库读取数据, 修改/etc/dovecot/conf.d/10-mail.conf,
加上:

mail_location = maildir:/home/vmail/%d/%n/Maildir
设置dovecot查询邮件的位置(顶头)

first_vaild_uid = 600

修改/etc/dovecot/conf.d/10-auth.conf, 取消掉!include auth-sql.conf.ext
取消调用数据库记录注释。

修改数据库连接配置文件(需要拷贝模板生成):

cp -a /usr/share/doc/dovecot-2.0.9/example-config/dovecot-sql.conf.ext
/etc/dovecot/

编辑dovecot-sql.conf.ext, 添加:

driver = mysql 驱动类型

connect = host=localhost dbname=extmail user=extmail password=extmail

default_pass_scheme = MD5

.......

重启dovecot验证是否连接

6\) mail+web页面

修改/etc/httpd/conf/httpd.conf配置文件, 能加载邮件web页面

extmial目录中更改cgi的属组和属主, 让vmail有权限执行

extman中更改cgi的属组和属主, 让vmail有权限执行

安装unix-syslog软件, 在浏览器上访问, windos上需要手动指向dns服务器。

4.13.2 rsync数据同步

1\) rsync简述

rsync是unix系统下的数据镜像备份工具。支持快速完全备份和增量备份工具,
支持本地复制, 远程同步等, 类似scp命令;
rsync命令在同步文件之前要先登录目标主机进行用户身份认证,
认证过后进行数据同步, 身份认证方式取决于所用的协议类型,
rsync一般使用两种协议进行数据同步: ssh协议和rsync协议。

rsync能更新整个目录树和文件系统;
能有选择性的保留符号链接、硬链接、文件属性等; 对于多个文件来说,
文件传输效率高; 能使用ssh或自定义的端口传输

2\) rsync工作原理

源地址(文件), 目标地址(文件),
以及以哪一方为基准。rsync在进行数据同步之前需要先进行用户身份验证,
验证方式取决于使用的连接方式: ssh登录验证模式,
rsync登录验证模式(使用rsync用户非系统用户)

数据同步方式: 推送(上传)、拉取(下载)

3\) rsync实验

ssh协议数据同步:

rsync -avz user@ip:/dir/\* /dir 下行同步(将远程同步到本地), -a归档模式,
递归并保留对象属性; -v显示同步过程; -z在传输文件时进行压缩

rsync -avz /dir/\* user@ip:/dir 上行同步(将本地上传到服务器),
注意尽量使用普通用户同步,
并给同步目录设置ACL权限。免密登录使用ssh密钥对。

Rsync协议数据同步: (将NFS数据同步到rsync服务器)

搭建rsync服务(仅需要在NFS服务器上搭建即可)

创建主配置文件(/etc/rsyncd.conf):

address = 192.165.88.10 rsync服务绑定IP

port 873 默认服务器端口873

log file = /var/log/rsyncd.log 日志文件位置

pid file = /var/run/rsyncd.pid 进程号文件位置

\[web\] 共享名: 用来连接是写在url上的

comment = web directory backup 共享描述

path = /dir 实际共享目录

read only = no 是否允许读取

dont compress = \*.gz \*.bz2 哪些文件类型不进行压缩

auth users = user1 登录用户名(非系统用户, 自己创建)

secrets file = /etc/rsyncd_users.db 认证所需要的密码文件(自己创建)

创建认证所需账户密码文件/etc/rsyncd_users.db中添加: user1:123456

chmod 600 /etc/rsyncd_users.db 必须修改权限, 否则出错。

启动服务: rsync \--daemon ; netstat -antp \| grep :873

设置映射用户对共享目录有读权限: setfacl -m u:nobody:rwx /dir

下行同步(下载): rsync -avz \--delete rsync://user1@ip/web /dir
(delete删除本地服务器多出来的文件)

上行同步(上传): rsync -avz /dir rsync://user1@ip/web(配置文件中\[web\])

免密码可以借助环境变量: export RSYNC_PASSWORD=虚拟用户密码(客户端生成)

4\) rsync+inotify实时同步

a\) inotify是一个文件监控系统,
并且及时向专门的应用程序发出相关事件警告。系统内核要在2.6.13版本以上。inotify两个监控命令:
inotifywait:用于持续监控, 实时输出监控结果(常用),
inotifywatch:用于短期监控, 任务完成后再输出结果。

先安装gcc\*, 再安装inotify-tools。

inotifywait -mrq -e event1,event2 /path &

options: -m始终保持事件监控, -r递归查询目录, -q只打印监控事件信息

监控动作: modify(内容修改), create(创建), attrib(权限), move(移动),
delete(删除)

b\) 利用脚本事项单向实时同步

#!/usr/bin/bash

a=\"inotifywait -mrq -e create,delete\" /dir

b=\"rsync -avx /dir/\* user@ip:/dir\"

\$a \| while read directory event file ; do 判断是否接收到监控记录

\$b

done

5\) unison + inotify实现双向同步

rsync在单向同步上很好, 效率高, 但双向同步支持较差;
unison则是双向同步工具, 但是同步效率较差。

先安装inotify, 再安装ocaml, 最后安装unison(再unison中已经有Makefile文件,
不需要./configure, 直接使用make UISTYLE=text THREADS=true STATIC=true,
然后将生成脚本拷贝cp unsion /usr/local/bin/), 同样的操作再服务端做一遍

配置脚本: 注意双向自动同步, 监控目录和数据同步时,
源目录不能使用\*通配符传输, 否则会变成死循环。

服务端:

#!/bin/bash

a=\"inotifywait -mrq -e create,delete\" /dir

b=\"/usr/local/bin/unison -batch /dir/\* ssh://ip//dir/\"
其中-batch批处理

\$a \| while read directory event file ; do

\$b

done

客户端脚本一样, 只需要改一下ip地址就好了; 运行脚本, 进行测试。

4.13.3 Java-web环境搭建(Tomcat)

1\) Tomcat简介

Tomcat服务器是一个免费开源的web应用服务器, 属于轻量级应用服务器,
在中小型系统和并发访问用户不是很多的场合下被普遍使用,
是开发和调试TSP程序的首选。Tomcat是Apache服务器的扩展,
但运行时它是独立运行的。

2\) java web环境: Nginx+JDK+Tomcat+MySQL

可以部署在一台主机上, 也可以部署在不同主机上。Nginx默认端口是80,
用来接受web请求。Tomcat默认开启的是8080端口,
用来接受Nginx转发过来的web请求

3\) 环境部署流程

a\) 首先安装gcc, 源码安装jdk, 将java解压到/usr/local目录下,
配置JDK的环境变量(在/etc/profile中添加以下内容):

export JAVA_HOME=/usr/local/jdk1.7

export JAVA_BIN=/usr/local/jdk1.7/bin

export PATH=\$PATH:\$JAVA_HOME/bin

export CLASSPATH=.:\$JAVA_HOME/lib/dt.jar:\$JAVA_HOME/lib/tools.jar

然后source /etc/profile, 查看java是否安装成功: java -version

b\) 安装tomcat

同样将tomcat解压到/usr/local下, 配置Tomcat的环境变量(/etc/profile)

export TOMCAT_HOME=/usr/local/tomcat

export PATH=\$PATH:\$TOMCAT_HOME/bin

source /etc/profile; 然后将tomcat启动脚本赋予执行权限: chmod +x
/usr/local/tomcat/bin/\*

开启tomcat, /usr/local/tomcat/bin/catalina.sh start ; 查看netstat -antp,
并访问测试

c\) 安装MySQL数据库

安装依赖ncurses-devel, gcc和之前的类似, 安装mysql, 生成配置文件,
初始化数据库生成授权表, 生成启动管理脚本, 启动mysql并设置开机自启动。

d\) 安装Nginx

解压Nginx, 安装依赖pcre-devel, zlib-devel, gcc; 添加用户useradd -r -s
/sbin/nologin nginx; 编译并安装;
修改nginx配置文件(dir/nginx/conf/nginx.conf):

user nginx;

upstream tomcat { 添加负载调度

server 192.168.10.20:8080

}

location / {

proxy_pass <http://tomcat>;

proxy_set_header Host \$host;

}

重启服务, pkill -HUP nginx

在客户端进行测试, 输入nginx地址, 打开的为tomcat的网址。

也可以部署多个tomcat, 注意修改端口和启动脚本, 将新的加到Nginx上。

4\) Tomcat目录结构

a\) 一级目录

bin: 存放Tomcat脚本

conf: 存放Tomcat配置文件

lib: 存放Tomcat服务器和所有web应用程序需要访问的jar文件

logs: 存放日志文件

temp: 存放临时文件

webapp: 为web应用程序及文件存放目录, 具体的网页程序等

work: 将jsp生成的servlet源文件和字节码文件存放在此目录

b\) webapp二级目录
