6.1.1 Linux集群简介

1\) 集群概述

集群是一组协同工作的服务器，各有分工，对外表现为一个整体。

2\) 集群分类

负载均衡集群LBC: 分担服务的总体压力

高可用集群HAC: 尽可能的保障服务状态的可用性

高性能运算集群HPC: 提供单台服务器提供不了的计算能力

6.1.2 负载均衡集群LBC

1\) LBC简介

用户访问→负载调度器→真实服务器池

负载调度器: 硬件: F5 Big-IP, Citrix Netscaler

软件: LVS(工作在传输层), Nginx(工作在应用层), HAProxy

2\) nginx简介

nginx是工作在网络的7层之上, 主要针对http分发, 可实现上万并发。

3\) LVS简介

LVS(linux virtual server)是基于4层的网络协议的,
抗负载能力强的负载均衡技术,
其架构从逻辑上可分为调度层、Server集群层和共享存储

4\) LVS三种负载负载均衡技术

NAT (Network Address Translation): 通过网络地址转换,
调度器重写请求报文的目标地址, 根据预设的调度算法,
将请求分派给后端的真实服务器; 真实服务器的响应报文通过调度器时,
报文的源地址被重写, 再返回给客户, 完成整个负载调度过程, 支持10\~20节点。

TUN (IP Tunneling): 调度器把请求报文通过IP隧道转发至真实服务器,
而真实服务器将响应直接返回给客户, 所以调度器只处理请求报文,
支持约100节点。

DR (Direct Routing): 通过改写请求报文的MAC地址, 将请求发送到真实服务器,
真实服务器将响应直接返回给客户, 支持约100节点,
调度器与真实服务器要在同一物理网段上。

5\) NAT模式实现

注意所有实验都要开路由转发, 及配置合理的防火墙规则

6.1.3 高可用集群HAC

1\) keepalived简介

keepalived是可提供vrrp(Virtual Router Redundancy
Protocol)以及health-check功能的高可用软件。

keepalived主要有三个模块构成:

core模块: 为核心组件, 负责主进程的启动, 维护以及全局配置文件的加载和解析

check模块: 负责健康检查

VRRP模块: 实现VRRP协议

2\) keepalived配置(keepalived.conf):

全局配置: global_defs { ... }

VRRPD配置: vrrp_sync_group vgName { ... }或vrrp_instance vName { ... }

LVS配置: virtual_server ipaddr { ... }

3\) keepalived + nginx配置

修改: vi .../etc/keepalived/keepalived.conf文件

global_defs { #全局配置, 块要有，参数可以不用配置

notification_email { #在发生切换发送的email

xxx@qq.com #一行一个

}

notification_email_from xxx@qq.com #指定发件人

smtp_server smtp.qq.com #指定smtp服务器地址

smtp_connect_timeout 30 #指定smtp连接超时时间

router_id centos801 #运行keepalived机器的标识, 每台机子唯一

#vrrp_strict #如果使用单播模式, 需要关闭严格模式, 不然会失败

}

vrrp_script chk_nginx { #配置服务的健康检查

script \"path/chk_nginx.sh\" #检查时使用的脚本

interval 2 #监控间隔, s

weight -2 #每检测失败一次，则当前节点的priority会加该配置的值

fall 1 #执行几次才会认为是失败

rise 3 #执行多少次才会认为是成功

}

vrrp_sync_group VG_n { #同时监控多个实例时配置

group {

VI_1 #具体实例, 一行一个, 具体配置在下面

}

notify_master path/nginx.sh #指定当切换到master时, 执行的脚本

notify_backup path /nginx.sh #指定当切换到backup时, 执行的脚本

notify path /nginx.sh #发生任何切换, 均执行的脚本

smtp_alert #使用global_defs中提供的邮件地址和smtp服务器发送邮件通知

}

vrrp_instance VI_1 { #配置真实主机监听状态

state MASTER\|BACKUP #指定当前节点的初始状态, 指定为master或backup

nopreempt #非抢占模式, (一般master设置, 既恢复后马上由master提供服务)

interface ens160 #对外绑定的网卡接口, 用于发送vrrp包

lvs_sync_daemon_inteface ens33 #负载均衡器之间监控接口,
其它服务器不一样设置

track_interface { #设置额外的监控，网卡出现问题都会切换

ens160 #一行一个

}

garp_master_delay #在切换到master状态后, 延迟进行gratuitous ARP请求

virtual_router_id 50 #指定vrrp实例的ID 范围是0-255 主备节点必须一致

priority 101 #指定当前节点的优先级 优先级高的为MASTER

advert_int 5 #指定发送vrrp时间间隔(检查, s), 主备必须一致

authentication { #主备必须一致

auth_type PASS #指定认证方式 这里使用简单密码认证

auth_pass 1111 #指定认证使用的密码 最大为8位

}

unicast_src_ip 192.168.99.103 #单播的源地址, 写本机上的ip即可

unicast_peer { #主备交换, 单播模式比较安全

192.168.99.104 #如果有多个主机组成集群, 把其它主机ip都写上

}

track_script { #监控脚本执行的状态

chk_nginx #上文设置内容

}

virtual_ipaddress { #配置虚拟ip地址

192.168.100.13 #指定VIP地址

}}

nginx检查脚本: check_nginx.sh

#!/bin/bash

count=\$(ps -ef \| grep nginx \| egrep -cv \"grep\|\$\$\")

if \[ \"\$count\" -eq 0 \] ; then

exit 1

else

exit 0

fi

注意: 如果开着防火墙，需要配置vrrp防火墙规则: firewall-cmd
\--add-rich-rule=\'rule protocol value=\"vrrp\" accept\' \--permanent ;
firewall-cmd \--reload (也可以指定特定端口)

4\) lvs+keepalived

6.2 Linux存储集群

6.2.1 Squid服务

1\) Squid简介

一个缓存服务器的守护进程，支持FTP，HTTP，SSL协议。

磁盘 分区 目录 object

Hash tables目录 每个digest 的索引信息；

Digest tables索引 不同分区对应的object 大概说明

(其它软件Varnish服务器)

2\) 工作模式

传统代理(标准代理):
必须在每个内部主机的浏览器上指明代理服务器的ip地址和端口号

透明代理:
不需要指明代理服务器的ip和端口号，该服务器阻断网络通信，并且过滤出外网的HTTP流量，对linux是使用iptables或ipchains实现的，其余和传统代理一样，(给局域网访问加速)。

反向代理:
位于本地web服务器和internet之间，处理所有对web服务器的请求，组织web服务器和internet的直接通信，这种方式通过降低向web服务器的请求降低了web服务器的负载，(公网用户加速)。

3\) Squid服务器介绍

Squid服务端软件包:squid-xxx；系统服务:squid；主程序:/usr/sbin/squid

主配置文件:etc/squid/squid.conf；默认监听端口:TCP 3128；

默认访问日志:/var/log/squid/access.log

常见配置参数:

http_port 3128

access_log /var/log/squid/access.log squid

cache_mem 64 MB

cache_dirufs/var/spool/squid 100 16 256

visible_hostname proxy.benet.com

dns_testnameswww.google.com [www.163.com](http://www.163.com)

maximum_object_size 4096 KB

reply_body_max_size 10 MB

4\) 搭建Squid服务器: 传统模式，透明模式，反向代理模式

5\) ACL(Access Control List，访问控制列表):

根据源地址、目标URL、文件类型等定义列表: acl 列表名称 列表类型 列表内容
\...

针对已定义的acl列表进行限制: http_access allow或deny 列表名称\...

src源地址；dst目标地址；port 目标端口；dstdomain目标域；time 访问时间；

maxconn最大并发连接；url_regex目标URL地址；Urlpath_regex整个目标URL路径

6.2.2 集群存储

1\) 存储分类

a\) 网络拓扑

DAS Direct-Attached Storage:
直接附加存储；优点是技术简单，传输速率最高；缺点:
存储设备与磁盘相互绑定。

NAS Network Attached Storage:
网络附加存储；优点是技术相对简单，不要求存储设备直连本机，只需在同局域网下即可缺点；缺点是存储速率较慢。

SAN Storage Area Network:
存储区域网络；优点是存储安全性较高，存储速率较高；缺点是造价昂贵，技术难度相对较高。

b\) 存储技术

块存储: 优点是可以通过Raid/LVM 等提供保护，可独立使用；缺点是不利于共享

文件存储/网络存储:
优点是构建资金较低，可在不同主机之间共享存储；缺点是读写速率低，传输速率慢

对象存储/分布式存储/存储桶:
优点是读写效率高，可在不同主机之间共享存储；缺点是造价昂贵，技术实现难度高。

c\) 网络拓扑+存储技术

块存储+DAS，块存储+NAS，块存储+SAN

文件存储+NAS，对象存储+SAN

2\) 文件存储之NFS

a\) NFS(Network File System)网络文件系统

NFS是可以透过网络，让不同的主机、不同的操作系统可以共享存储

NFS在文件传送或信息传送过程中依赖于RPC协议。远程过程调用(Remote
Procedure Call)是能使客户端执行其他系统中程序的一种机制。

其优点是节省本地存储空间，将常用的数据存放在一台NFS服务器上且可以通过网络访问，那么本地终端将可以减少自身存储空间的使用。用户不需要在网络中的每个机器上都建有Home目录，Home目录可以放在NFS服务器上且可以在网络上被访问使用。一些存储设备CDROM和Zip等都可以在网络上被别的机器使用。这可以减少整个网络上可移动介质设备的数量

b\) NFS构建

nfs-utils: 这个是NFS 服务主程序（包含rpc.nfsd、rpc.mountd、daemons）

rpcbind: 这个是CentOS6.X 的RPC主程序（CentOS5.X的为portmap）

Server端安装并开启服务: yum -y install rpcbind nfs-utils ;
启动/etc/init.d/rpcbind status

/etc/exports是NFS程序的配置文件，格式为:

NFS共享目录 NFS客户端地址1(参数1,参数2,参数3\...\...)
客户端地址2(参数1,参数2,参数3\...\...)

NFS共享目录 NFS客户端地址(参数1,参数2,参数3\...\...)

参数有: {

rw:(read-write)可读写；

ro:(read-only)只读；

sync:文件同时写入硬盘和内存；

async: 文件暂存于内存，而不是直接写入内存；

no_root_squash:
NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，也拥有root权限。显然开启这项是不安全的；

root_squash: NFS 客户端连接服务端时如果使用的是root
的话，那么对服务端分享的目录来说，拥有匿名用户权限，通常他将使用nobody
或nfsnobody身份；

all_squash: 不论NFS
客户端连接服务端时使用什么用户，对服务端分享的目录来说都是拥有匿名用户权限；

anonuid: 匿名用户的UID 值，通常是nobody
或nfsnobody，可以在此处自行设定；

anongid: 匿名用户的GID值

}

查看挂载情况showmount -e ip ;

挂载: mount -t nfs ip:/file /file1

3\) 分布式存储之MFS

a\) MFS(MooseFS)简介

MFS是一个具备冗余容错功能的分布式网络文件系统，它将数据分别存放在多个物理服务器或单独磁盘或分区上，确保一份数据有多个备份副本，然而对于访问MFS的客户端或者用户来说，整个分布式网络文件系统集群看起来就像一个资源一样，从其对文件系统的情况看MooseFS就相当于UNIX
的文件系统

MFS特点: 高可靠性；高可扩展性；高可容错性；高数据一致性

缺点:
Master是单点，虽然会把数据信息同步到备份服务器，但是恢复需要时间；Master服务器对主机的内存要求略高；默认Metalogger
复制元数据时间较长(可调整)；

应用场景:
大规模高并发的线上数据存储及访问(小文件，大文件都适合)；大规模的数据处理，如日志分析。（小文件强调性能不如HDFS）。

b\) MFS组件

管理服务器(managing
server)又称master，是管理整个mfs文件系统的主服务器，除了分发用户请求外，还用来存储整个文件系统中每个数据文件的metadata信息。

元数据备份服务器(metadata backup
servers)简称metalogger，是备份管理服务器master的变化的metadata信息日志文件，文件类型为changelog_ml.\*.mfs。

数据存储服务器组(data servers(chunk
servers))简称data，是真正存放数据文件实体的服务器了，这个角色可以有多台不同的物理服务器或不同的磁盘及分区来充当，当配置数据的副本多于一份时，据写入到一个数据服务器后，会根据算法在其他数据服务器上进行同步备份。

客户机服务器组(client servers)简称client:
是挂载并使用mfs文件系统的客户端，当读写文件时，客户端首先会连接主管理服务器获取数据的metadata信息，然后根据得到的metadata信息，访问数据服务器读取或写入文件实体，mfs客户端通过fuse
mechanism实现挂载mfs文件系统的，因此，只有系统支持fuse，就可以作为客户端访问mfs整个文件系统

c\) 增、删、改、读、遍历

d\) MFS补充描述

e\) 构建MFS集群

f\) MFS维护操作

4\) ISCSI存储

a\) 存储区域网络(Storage Area Network):
多采用高速光纤通道，对速率、冗余性要求高，使用iSCSI存储协议，块级传输。(大环境)

网络附加存储(Network Attachment Storage):
采用普通以太网，对速率、冗余无特别要求，使用NFS、CIFS共享协议，文件级传输。(小环境)

b\) ISCSI的工作方式: 服务端(设备端):target。客户端(应用端):initiator

LUN(logical unit):逻辑单元，逻辑磁盘空间

c\) ISCSI构建

6.3 Linux集群管理

6.3.1 集群监控

1\) 常见监控软件

Cacti流量与性能监测为主。平台B/S，收集数据: SNMP(Simple Network
Management Protocol)

Nagios服务与性能监测为主，平台:B/S，收集数据:C/S(脚本)

Zabbix新型全功能监控软件，平台:B/S，收集数据: C/S

2\) Cacti监控

a\) Cacti原理

b\) Cacti工作模式:
C/S模式，采集检测数据，由SNMP完成；B/S模式，管理检测平台，由RRDtool完成

c\) Cacti监控构建

d\) Cacti插件

3\) Nagios监控

a\) Nagios原理

偏重主机、服务的状态；

监控对象:主机、主机组；服务/资源，服务组；联系人，联系人组；时段；命令

b\) Nagios构建

c\) 添加监控主机

4\) Zabbix监控

a\) Zabbix组件说明

zabbix是一个基于WEB界面的提供分布式系统监视以及网络监视功能的企业级的开源解决方案(基于GPL
V2); zabbix由2部分构成，zabbix server与可选组件zabbix agent

Zabbix工作架构; zabbix-server及整个数据收集模块负责数据收集,
zabbix-database负责存储采集到的数据, zabbix-web-GUI负责为用户提供报告

zabbix 安装完成后会产生5 个程序:

zabbix_agentd:客户端守护进程,
此进程收集客户端数据，例如cpu负载、内存、硬盘使用情况等;

zabbix_get:单独使用的命令，通常用于排错;

zabbix_sender:用于发送数据给server或者proxy，在脚本完成之后使用sender主动将数据提交;

zabbix_server:服务端守护进程，所有的数据都是被提交或主动提交到zabbix_server
端;

zabbix_proxy:代理守护进程。功能类似server，唯一不同的是它只是一个中转站，它需要把收集到的数据提交/被提交到server里;

zabbix_java_gateway:在zabbix2.0之后引入的一个功能。只用于Java方面，它只能主动去获取数据。

b\) 构建Zabbix

c\) 监控主机/自动发现

d\) Nginx并发监控

e\) web场景、组合图

f\) 报警设置

6.1.8 安全防护

1\) 常见的共计手段

拒绝服务, 口令破解, 欺骗用户, 已知漏洞

2\) 常见的防护工具

基础类防火墙; IDS类; IPS类; 主动安全类

3\) 基础类防火墙

a\)
工作在主机边缘处或者网络边缘处对数据报文进行检测，并且能够根据事先定义好的规则，对数据报文进行相应处理的模块

防火墙分类:

构造: 硬件: 深信服，网御，华为；软件: windows防火强，iptables，firewall

工作机制: 包过滤: SIPDIPSPORTDPORT；应用层: URL, HOSTNAME

模块:应用态:ipvsadm(iptables, firewalld)，内核态:ipvs(netfilter)

b\) iptables原理: 四表五链

五链: INPUT处理入站数据包; OUTPUT处理出站数据包; FORWARD处理转发数据包;
POSTROUTING在进行路由选择后处理数据包;
PREROUTING在进行路由选择前处理数据包。

四表:
raw表:确定是否对该数据包进行状态跟踪；mangle表:为数据包设置标记；nat表:修改数据包中的源、目标IP地址或端口；filter表:确定是否放行该数据包（过滤）

4\) 防火墙顺序

表顺序: raw - mangle - nat - filter

链顺序:入站PREROUTING, INPUT;出站OUTPUT, POSTROUTING; 转发PREROUTING,
FORWARD, POSTROUTING

规则顺序:
按顺序依次检查，匹配即停止(LOG策略例外)；若找不到相匹配规则，则按该链的默认策略处理

5\) iptables语法规则

iptables \[-t 表名\] 选项 \[链名\] \[条件\] \[-j 控制类型\]

几个注意事项:
不指定表名时，默认指filter表；不指定链名时，默认指表内的所有链；除非设置链的默认策略，否则必须指定匹配条件；选项、链名、控制类型使用大写字母，其余均为小写书写规则

a\) 控制类型:

  -----------------------------------------------------------------------------------------
  控制类型                                              控制类型    
  ---------- ------------------------------------------ ----------- -----------------------
  ACCEPT     允许通过                                   SNAT        修改数据包源地址

  DROP       直接丢弃，不给出任何回应                   DNAT        修改数据包目的地址

  REJECT     拒绝通过，必要时会给出提示                 REDIRECT    重定向

  LOG        记录日志信息，然后传给下一条规则继续匹配               
  -----------------------------------------------------------------------------------------

b\) 选项:

  ---------------------------------------------------------------------------
  类别             选项              用途
  ---------------- ----------------- ----------------------------------------
  添加新规则       -A                在链的末尾追加一条规则

                   -I                在链的开头(或指定序号)插入一条规则

  查看规则列表     -L                列出所有的规则条目

                   -n                以数字形式显示地址、端口等信息

                   -v                以更详细的方式显示规则信息

                   \--line-numbers   查看规则时，显示规则的序号

  删除、清空规则   -D                删除链内指定序号(或内容)的一条规则

                   -F                清空所有的规则

  设置默认策略     -P                为指定的链设置默认规则
  ---------------------------------------------------------------------------

注意: 将匹配类多的规则放到最前面，可以减小服务器压力

c\) 匹配类型

通用匹配:
可直接使用，不依赖于其他条件或扩展包括网络协议、IP地址、网络接口等；

隐含匹配: 要求以特定的协议匹配作为前提包括端口、TCP标记、ICMP类型等

条件显式匹配: 要求以"-m
扩展模块"的形式明确指出类型包括多端口、MAC地址、IP范围、数据包状态等条件

  --------------------------------------------------------------------------------
  匹配类型       option         说明                  option         说明
  -------------- -------------- --------------------- -------------- -------------
  通用匹配       -p             协议名                -i             入站网卡

                 -s             源地址                -o             出站网卡

                 -d             目标地址                             

  隐含匹配       \--sport       源端口                \--dport       目的端口

                 \--icmp-type                         ICMP类型       

  条件显式匹配   -m multiport                         -m multiport   
                 \--sport                             \--dport       
                 源端口列表                           目的端口列表   

                 -m iprange                           -m mac         
                 \--src-range                         -macl-source   
                 IP范围                               MAC地址        

                 -m state                                            
                 \--state                                            
                 连接状态                                            
  --------------------------------------------------------------------------------

iptables -I INPUT -p tcp -m state \--state NEW,ESTABLISHED,RELATED -j
ACCEPT

d\) SNAT策略的典型应用环境: 局域网主机共享单个公网IP地址接入Internet;

SNAT策略的原理: 源地址转换(Source Network Address
Translation)，修改数据包的源地址。

编写SNAT转换规则: iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o
eth0 -j SNAT \--to-source 218.29.30.31

MASQUERADE \--地址伪装: iptables -t nat -A POSTROUTING -s 192.168.1.0/24
-o eth0 -j MASQUERADE

e\) DNAT策略的典型应用环境: 在Internet中发布位于企业局域网内的服务器；

DNAT策略的原理: 目标地址转换(Destination Network Address
Translation)，修改数据包的目标地址

编写DNAT转换规则: iptables -t nat -A PREROUTING -ieth0 -d 218.29.30.31
-p tcp \--dport80 -j DNAT \--to-destination 192.168.1.6

发布时修改目标端口: iptables -t nat -A PREROUTING -ieth0 -d 218.29.30.31
-p tcp\--dport2346 -j DNAT \--to-destination 192.168.1.6:22

6\) 防火墙脚本

导出(备份)规则: iptables-save工具，可结合重定向输出保存到指定文件

导入(还原)规则: iptables-restore工具，可结合重定向输入指定规则来源

iptables服务

脚本位置: /etc/init.d/iptables

规则文件位置: /etc/sysconfig/iptables

7\) SELinux

1\) 安全级别: linux和windows都是C2级别

2\) 安全上下文

所有操作系统访问控制都是以关联的客体和主体的某种类型的访问控制属性为基础的。在SELinux
中，访问控制属性叫做安全上下文。所有客体（文件、进程间通讯通道、套接字、网络主机等）和主体（进程）都有与其关联的安全上下文，一个安全上下文由三部分组成:
用户、角色和类型标识符。常常用下面的格式指定或显示安全上下文:

用户:角色:类型 可以通过ls -lZ test 查看相关文件的安全上下文

3\) 更改角色类型

chcon\[-R\] \[-t type\] \[-u user\] \[-r role\] 文件

选项与参数:
-R:连同该目录下的次目录也同时修改；-t:后面接安全性本文的类型字段！-u:后面接身份识别，例如system_u；-r:后面接角色，例如system_r

4\) restorecon还原成原有的SELinux type格式

restorecon\[-Rv\] 档案或目录

选项与参数: -R:连同次目录一起修改；-v:将过程显示到屏幕上

5\) Selinux布尔值

Managing Boole(管理SElinux布尔值):
Seliux布尔值就相当于一个开关，精确控制Selinux对某个服务的某个选项的保护，比如samba服务

查看: getsebool -a \| grep 应用名

setsebool命令用来改变SELinux布尔值: setsebool -p
samba_enable_home_dirs=1开启家目录是否能访问的控制

6.1.9 无人值守装机

1\) PXE原理

PXE(Pre-boot Execution
Environment)是由Intel和Microsoft设计的协议，它可以使计算机通过网络启动。协议分为client和server两端，PXE
client在网卡的ROM中，当计算机引导时，BIOS把PXE
client调入内存执行，并显示出命令菜单，经用户选择后，PXE
client将放置在远端的操作系统通过网络下载到本地运行

2\) PXE服务构建

3\) Cobbler服务构建

搭建更为简单, 管理更为便捷; 能个选择系统版本进行安装
