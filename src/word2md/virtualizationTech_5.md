1\) 虚拟化技术

虚拟化,
是指通过虚拟化技术将一台计算机虚拟为多台逻辑计算机。在一台计算机上同时运行多个逻辑计算机,
每个逻辑计算机可运行不同的操作系统,
并且应用程序都可以在相互独立的空间内运行而互不影响,
从而显著提高计算机的工作效率

2\) 虚拟化技术分类

a\) 技术分类

全虚拟化技术(Full
Virtualization,也叫硬件辅助虚拟化技术):它在虚拟机(VM)和硬件之间加了一个软件层\--Hypervisor,
或者叫做虚拟机监控器(VMM); hypervisor直接运行在物理硬件之上-KVM;
hypervisor运行在另一个操作系统中-QEMU和WINE

半虚拟化技术(准虚拟化技术):它是在全虚拟化的基础上,
把客户操作系统进行了修改, 增加了一个专门的API,
这个API可以将客户操作系统发出的指令进行最优化,
即不需要Hypervisor耗费一定的资源进行翻译操作,
因此Hypervisor的工作负担变得非常的小, 因此整体的性能也有很大的提高。

b\) 架构分类

寄居架构: 就是在操作系统之上安装和运行虚拟化程序,
依赖于主机操作系统对设备的支持和物理资源的管理

裸金属架构: 就是直接在硬件上面安装虚拟化软件,
再在其上安装操作系统和应用, 依赖虚拟层内核和服务器控制台进行管理。

3\) 升级centos7系统内核4.4

下载内核源rpm -Uvh
http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm

安装最新版本内核yum \--enablerepo=elrepo-kernel install -y kernel-lt

查看可用内核cat /boot/grub2/grub.cfg \| grep menuentry

设置开机从新内核启动grub2-set-default \"CentOS Linux
(4.4.221-1.el7.elrepo.x86_64) 7 (Core)\"

查看内核启动项grub2-editenv list; 重启系统使内核生效reboot;
查看内核版本是否生效uname -r

7.1.2 EXSI虚拟化技术

1\) EXSI安装

VMware
ESXi是一款行业领先、专门构建的裸机hypervisor。ESXi直接安装在物理服务器上,
并将其划分为多个逻辑服务器, 即虚拟机

像安装操作系统一样安装, 内存要大于4GB。

2\) EXSI资源分割

3\) EXSI组网

4\) EXSI其它说明

7.1.3 云计算openstack

1\) 云计算简介

基础是虚拟化、前身是网格计算

云计算是一种按使用量付费的模式,
这种模式提供可用的、便捷的、按需的网络访问,
进入可配置的计算资源共享池(资源包括网络, 服务器, 存储, 应用软件, 服务),
这些资源能够被快速提供, 只需投入很少的管理工作,
或与服务供应商进行很少的交互。

2\) 云计算分类

架构设计: 私有云, 公有云, 混合云

供给方式: IaaS(infrastructure as a service)基础设施即服务; PaaS(platform
as a service)平台即服务; SaaS(software as a service)软件即服务

3\) openstack

OpenStack是一个云平台管理的项目,
项目由几个主要的组件组合起来完成一些工作,
通过一个通过web界面提供资源管理,
通过一个仪表盘管理整个数据中心的计算存储资源等; 有如下特性:
API对接亚马逊云平台; 使用Apache协议进行开源; 最火爆开源项目; 一个框架;
使用Python进行开发; 混合云。

4\) OpenStack框架说明

a\) Openstack组件说明: 使用Juno版

Nova计算服务; Glance镜像服务; Swift对象存储; Cinder块存储;
Neutron网络服务; Horizon仪表盘; Keystone认证服务; ceilometer监控;
Heat编排; ceilometer监控; Trove数据库服务; Sahara数据处理

b\) 安装结构说明: 配置相关环境

八 容器技术-Docker

8.1 Docker基础

8.1.1 Docker简介

Linux Container是一种内核虚拟化技术, 可以提供轻量级的虚拟化,
以便隔离进程和资源;
Docker是PaaS提供商dotCloud开源的一个基于LXC的高级容器引擎,
基于go语言并遵从Apache2.0协议开源;
Docker中用户可以通过标准化手段自由组装运行环境,
同时集装箱的内容可以由用户自定义, 也可以由专业人员制造。

docker组成: 仓库(Repository), 镜像(image), 容器(container)

8.1.2 docker基本命令

1\) 常用命令

  -----------------------------------------------------------------------
  docker info 守护进程的系统资源设置  docker search name 镜像搜索查询
  ----------------------------------- -----------------------------------
  docker pull name:tag 仓库下载       docker images \[-aq\] 镜像查询

  docker rmi name\|id镜像删除         docker image prune \[-f\]
                                      清理遗留镜像

  docker build -t na:tg .             docker push name:tag 上传镜像
  用dockerfile创建镜像                

  docker run \[option\]               docker update \[options\]
  ID\|name创建容器                    container更新配置

  docker pause\|unpause name\|id      docker start\|stop\|restart
  暂停\|恢复容器                      ID启动/停止/重启

  docker exec ID\|name                docker attach ID
  command容器执行命令                 进入到运行容器的stdin

  docker rm \[-fv\] ID删除容器,       docker inspect
  \[强制\|数据卷\]                    ID\|name查看容器基本信息

  docker ps \[-aq\] 容器状态查询      docker stats
                                      ID\|name查看容器占用资源

  docker logs ID\|name查看日志        docker port container查看端口映射

  docker save -o test.tar image       docker load -i test.tar 载入镜像
  存出镜像                            

  docker export -o test.tar name\|ID  docker import test.tar \-- name:tag
  导出容器                            导入容器

  docker cp \.../dir ID\|name:/tmp/   docker tag imageid
  拷入/出文件                         newName:tag镜像改名

  docker rename container newName     
  容器改名                            
  -----------------------------------------------------------------------

2\) docker run option images command

  ---------------------------------------------------------------------------------------
  \--name name 别名                   -p 8080:80 映射端口(宿主机:容器内部)
  ----------------------------------- ---------------------------------------------------
  -d 后台运行                         -it 打开容器终端, 并保持标准输入打开

  -e var=value 指定运行时的环境变量   -v dir:dir 挂载文件到容器

  \--rm 服务停止时自动删除            \--restart=always\|on-failure\|unless-stopped\|no

  \--link=db:mysql 连接关系           \--dns=xx.xx.xx.xx 设置容器使用的DNS
  容器名:别名                         

  \--add-host=xx.com:IP 添加host解析  \--hostname=xxx 容器主机名

  \--cpus 限制cpu                     -m 限制内存

  \--ip\|\--ip6 指定固定ip地址        
  ---------------------------------------------------------------------------------------

3\) docker exec option containerID command

docker exec -it containerID /bin/bash 进入容器

docker exec -it containerID sh -c "exec command" 以exec模式执行命令

docker exec -it containerID sh -c \"command\" 以shell形式执行命令

8.1.2 podman命令

1\) podman基本命令

podman支持docker所有命令, 可以在.bashrc中添加 alias docker=podman

注意: a) podman run 不支持\--link , 一般是使用pod连接两个容器

b\) rootless容器镜像存储在: \$HOME/.local/share/containers/storage/

2\) podman独有命令

podman auto-apdate 根据run时\--label指定的策略更新容器

podman generate kube \[-s\] pod\|container 根据pod创建k8s配置,
-s同时创建一个服务

podman generate systemd \--files \--name container\|pod
配置systemd管理配置文件

podman play kube xx.yaml 根据配置文件部署服务

3\) podman pod 管理

podman pod create \--name=myPod \[ -p port \--ip=ipaddr
\--add-host=host:ip \--dns=ipaddr \--hostname=xx
\--infra-image=image(指定基础镜像) \--label=label(指定标签) \] 创建pod,
其它\--network(创建pod网络)

podman pod list 查看pod列表

podman pod exists\|inspect\|ps\|stats\|top podName 查看pod详情

podman pod start\|restart\|stop\|rm\|kill\|prune\|pause\|unpause 管理pod

示例:

podman pod create \--name=work -p 22022:22 -p 80:80 -p 443:443

podman run -d \--name=develop \--pod=work centos:latest /usr/sbin/init

注意: a) pod相当于一台主机, pod中的container相当于应用程序,
所以port和ip等要在pod创建时指定,
同一个pod中的容器是可以直接互联(localhost), 不需要任何的映射

b\) pod目前只能在root用户下使用

8.2 docker管理及应用

8.2.1 搭建私有仓库

1\) 简易仓库

拉取仓库镜像: registry

启动镜像: docker run -d -p 5000:5000 -v
/opt/docker/registry:/var/lib/registry id\|name

上传镜像: docker tag centos:latest ip:5000/centos:t1 (IP为仓库ip)

docker push ip:5000/centos:t1

下载镜像: docker pull ip:5000/centos:t1

2\) harbor仓库

8.2.2 docker数据管理

卷管理: docker volume create \| inspect \| rm my_vol \| ls //创建 \|
查看 \| 删除 \| 列出卷

绑定: docker run -d \--mount source=my_vol,destination=/opt,readonly
centos

docker run -d -v my_vol:/opt:ro centos

挂载目录: docker run -d \[\--privileged=true\] -v
/path(本地):/opt(容器)\[:ro\] centos

应用: docker run -d \[-v /dbdata\] \--name data1 centos

docker run -d \--volumes-from data1 \--name data2 \[-v /path1:/path2\]
centos //关联容器

注意: 1) 容器关联的是dockerfile中指定的VOLUME

2\) root权限不足用\--privileged=true,
非root用户挂载时容器内外uid和gid必须一致

3\) :ro 只读类型挂载中, 容器内响应目录中已经存在的文件不会显示载本地。

5.2.3 端口映射和容器互联

端口映射: docker run -d -p 8080:80 jenkins

查看端口映射: docker port container 或 docker container port id\|name

容器互联: docker run -d \--name db mysql

docker run -d \--name web \--link db:mysql nginx

注意: \--link 要连接容器名:别名, 容器互联用于防止数据库暴露问题,
可以在web中直接通过ping mysql连接db容器

8.2.4 docker镜像管理

1\) dockers镜像

容器创建时需要指定镜像, 每个镜像都由唯一的标示Image ID。镜像的分层:
Docker的镜像通过联合文件系统(union filesystem)将各层文件系统叠加在一起。

2\) 在线创建

由容器创建镜像: docker commit ID xx:xx 至少有一个工作在前台的守护进程

3\) Dockerfile

一种被Docker程序解释的脚本, Dockerfile由一条一条的指令组成,
每条指令对应Linux下面的一条命令。Dockerfile语法:

a\) FROM centos:8.0 指定基础镜像

b\) MAINTAINER zhangsan "gg@dd.cn" 开发者信息

c\) LABEL xxx 设置标签信息, LABEL version=\"1.0\" description=\"web
server\" by=\"IT\"

d\) USER username 设置登录用户, 如服务不要root登录

e\) ENV var=value 环境变量, build后依然存在, 多组ENV var1=value1
var2=value2

f\) ARG name\[=value\] 设置临时变量,
build后失效。如果在Dockerfile中未指定值, 可以用docker build \--build-arg
name=value指定值

g\) WORKDIR dir 指定后面RUN, CMD等的工作目录

h\) RUN command 构建images运行的shell命令, \[\"command\"\]为exec格式。

i\) COPY src dest 复制本地文件到容器, 不解压, 推荐使用

j\) ADD src dest 将本地文件复制容器中, src可以是url,
也可以是tar将自动解压, 注意文件一定要放在dockerfile同级目录下。

k\) VOLUME dir 创建数据挂载点, 多个\[\"dir1\",\"dir2\",\...\],
为数据卷容器的默认挂载点。注意尽量不要将属主不同、服务需要初始化的路径直接挂载到本地。

l\) EXPOSE port1 port2声明镜像内的暴露端口, 注意这只有显示作用

m\) ENTRYPOINT command 设置container启动运行命令,
只有最后一个生效。运行(run)时用户指定命令, 只能作为该命令的参数;
可被运行时的\--entrypoint后的参数覆盖。格式:

ENTRYPOINT \[\"command\", \"paraml \", \"param2\"\] 命令以exec调用执行,
command param1 param2, 推荐该方式。

ENTRYPOINT command param1 param2 命令在shell中执行, /bin/sh -c "command
\..."。

n\) CMD command, 类似ENTRYPOINT。如果运行(run)时用户指定参数,
则会覆盖此选项, 如: docker run -it centos
/bin/bash会覆盖此命令。其格式也有以上两种。但当CMD只指定参数时,
只为ENTRYPOINT提供参数, 如CMD \[\"paraml \", \"param2\"\]。

o\) ONBUILD command 在子镜像中执行, 构建镜像时不执行,
command是Dockerfile中的命令, 就是别人构建下一个镜像时执行

4\) dockerfile构建镜像

镜像生成命令: docker build -t images:tags dir/Dockerfile

注意Docker镜像最多不能超过128层,
如果需要容器一直在后台运行还要保证PID1进程存在。当无法满足条件时,
使用Supervisor辅助进行前台运行的功能(http://supervisord.org)。

8.2.5 安全防护与配置

安全自动化检查的工具Docker Bench和clair。

8.2.6 Docker网络管理

Docker
服务启动时会首先在主机上自动创建一个docker0虚拟网桥(172.17.0.0/16网段,
掩码为255.255.0.0), 启动的容器内的网口也会自动分配一个该网段的地址。

1\) docker网络通信

容器访问外部网络:(开启路由转发sysctl -w net.ipv4.ip_forward =
1或/etc/sysctl.conf)

iptables -t nat -A POSTROUTING -s 172.17.0.0/16 -o docker0 -j MASQUERADE

外部网络访问容器:

docker run -d -p 80:80 apache

iptables -t nat -A PREROUTING -m addrtype \--dst-type LOCAL -j DOCKER

iptables -t nat -A DOCKER ! -I docker0 -p tcp -m tcp \--dport 80 -j DNAT
\--to-destination 172.17.0.2:80

2\) docker自定义网络配置(启动时:docker run)

\--hostname=hostname 设定主机名

\--add-host hostname:IP 添加ip解析, 修改的是/etc/hosts。

\--link=container_name:alias 创建容器连接关系

\--dns=114.114.114.114 配置容器的DNS服务器

\--ip=10.10.10.10/24 指定容器的ip和掩码, 使用标准CIDR形式。\--ip6=\"\"
指定ipv6

\--mtu=1500B 接口允许接收的最大传输单元

\--net=networkname 连接到自己创建的网络上, 见下3)

3 ) libnetwork 插件化网络功能

工作过程:首先, 驱动注册自己到网络控制器, 网络控制器使用驱动类型,
来创建网络; 然后在创建的网络上创建接口;
最后把容器连接到接口上即可。销毁过程则正好相反, 先把容器从接入口上卸载,
然后删除接入口和网络即可。

a\) 创建网络: docker network create \[OPTIONS\] NETWORK(名字, 如docker0)

-d,\--driver=\"bridge\": 网络驱动类型, 如bridge(不同容器间通信),
overlay(跨主机通信, 只能在swarm下创建), host(直接使用宿主及网络),
none(禁用网络);

\--gateway=172.17.0.1 网关地址; \--ip-range=172.17.1.0/24
指定分配IP地址范围;

\--subnet＝172.17.0.0/16 网络地址段, CIDR格式; \--ipv6 启用ipv6网络。

\--internal 限制外部访问网络; \--aux-address
网络驱动程序使用的辅助IPv4或IPv6地址

b\) 接入网络:docker network connect \[option\] NETWORK CONTAINER

\--ip=\"172.17.1.11\" 指定IPv4地址, 需要注意不能跟已接入的容器地址冲突;

\--ip6=\"\" 指定ipv6 地址; \--link value 添加链接到另外一个容器;

c\) 断开网络: docker network disconnect \[-f\] NETWORK CONTAINER

d\) 查看网络信息: docker network inspect \[-fv\] NETWORK \[NETWORK \...
］

e\) 列出所有网络: docker network ls \[\--filter=bridge\]

f\) 清理无用网络: docker network prune \[-f(强制) \--filter=\"bridge\"\]

g\) 删除网络: docker network rm NETWORK \[NETWORK \... \]

8.2.7 docker资源限制

容器是没有资源限制的, 容器可以耗尽宿主机资源

1\) 内存限制

对于Linux主机, 如果没有足够的内存来执行其它重要的系统任务, 将会抛出OOM
(Out of Memory
Exception,内存溢出、内存泄漏、内存异常),随后系统会开始杀死进程以释放内存。

启动容器时内存限制参数:

-m 256m, \--memory=256m 容器可以使用的最大内存量(单位m,g),
该值必须大于4m。

\--memory-swap=1g 容器可以使用的交换分区大小, 必须要设置-m才有效。

\--memory-reservation 内存软限制,
小于-m的值。当Docker内存不足时会激活该限制。

\--kernel-memory 可使用的最大内核内存量, 最小为4m,
内核内存不足的容器可能会阻塞宿主主机资源,
这会对主机和其他容器或者其他服务进程产生影响, 因此不要设置过小。

\--oom-kill-disable 对某个容器关闭omm机制, 必须要有-m才生效。

2\) cup限制

\--cpus=1.5 指定容器可以使用多少可用CPU资源, 如最多可以访问1.5个的CPU。

\--cpuset-cpus=1,3 用于指定容器运行的CPU编号, 如只能用第2个和第4个。

3\) 压力测试

docker pull lorel/docker-stress-ng 压测镜像

docker run \--name stress \--rm lorel/docker-stress-ng:latest stress
\--help 查看帮助

模板: stress-ng \--cpu 8(测试使用cpu数) \--io 4 \--vm 2(测试启动容器数)
\--vm-bytes 128M(每个容器使用内存) \--fork 4 \--timeout 10s

内存: docker run -it \--rm \--name c2 -m 256m lorel/docker-stress-ng
\--vm 2 \--vm-bytes 256M

cpu测试: docker run -it \--rm \--name c2 \--cpus 2
lorel/docker-stress-ng \--cpu 4 \--vm 4

8.2.8 docker常见问题

1\) systemctl

systemctl不能使用问题, 使用\--privileged和/usr/sbin/init 模式启动容器,
进入容器使用正常/bin/bash

如: docker run -d \--privileged centos:latest /usr/sbin/init 或
/usr/sbin/systemd

注意: podman的rootless不需要加\--privileged

2\) ipv4

WARNING: IPv4 forwarding is disabled. Networking will not work,
因为没有开路由转发

echo \"net.ipv4.ip_forward=1\" \>\> /etc/sysctl.conf; sysctl -p;
重启网络

注意: podman不需要设置

3\) 容器时间与宿主机不同步问题

运行容器时挂载: -v /etc/localtime:/etc/localtime:ro

如果容器已运行: docker cp /etc/localtime nginx:/etc/

或者进入容器: ln -nfs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

4\) pod容器开机启动问题

podman容器开机启动需要通过systemd控制

podman generate systemd \--files \--name container\|pod 生成配置文件

rootless将文件保存在\~/.config/systemd/user/xxx.service或/etc/systemd/user/文件中,
root将文件保存在/etc/systemd/sytem/, 刷新systemd配置

调用: systemctl \[\--user\] daemon-reload

systemctl \[\--user\] start\|status\|reload\|stop\|enable\|disable
container-xxx.service

5\) docker资源占用清理

docker system df #查看docker磁盘使用情况

docker system prune #清理磁盘

docker rmi \$(docker images \| grep \"\^\<none\>\" \| awk \"{print
\$3}\") #删除无tag镜像

docker volume rm \$(docker volume ls -qf dangling=true)
#删除所有无用的volume

6\) docker日志大小限制

cat \> /etc/docker/daemon.json \<\< EOF

{

\"log-driver\":\"json-file\",

\"log-opts\": {\"max-size\":\"500m\", \"max-file\":\"3\"}
#单个容器日志大小和个数限制

}

EOF

8.3 docker集群

8.3.1 Etcd高可用键值库

8.3.2 Docker machine

负责实现对Docker运行环境进行安装和管理, 特别在管理多个Docker环境时。

1\) 创建docker主机

docker-machine create -d generic \--generic-ip-address=192.168.100.80
\[\--generic-ssh-key \~/.ssh/id_rsa\] \--generic-ssh-user=test
name(主机名)

-d,\--driver支持的驱动类型: linux通用generic; 微软hyper-v驱动hyperv;
VirtualBox驱动virtualbox; 亚马逊云amazonec2

\--engine-install-url \"https://get.docker.corn\" 配置Docker
主机时的安装URL;

\--engine-env option 指定环境变量;

\--engine insecure-registry option 以键值对格式指定注册仓库服务;

\--engine-registry-mirror= https://hub-mirror.c.163.com 配置镜像加速

\--swarm 配置Docker 主机加入到Swarm 集群中;

\--swarm-master 配置机器作为Swarm 集群的master 节点;

\--swarm-discovery集群Swarm的服务发现机制参数;

\--swarm-strategy \"spread\" 默认Swarm调度策略;

\--swarm-host \"tcp ://0 .0.0.0:3376\" 指定地址将监昕Swarm
master节点请求;

\--swarm-addr 从指定地址发送广播加入Swarm集群服务。

前提是已安装好操作系统和免密ssh服务, 同时可以提前配置好国内镜像,
安装依赖。

2\) 查看docker主机

docker-machine ls \[option\] 列出所有管理主机, -q减少无关输出信息;
\--filter option(state=stopped) ...只输出符合过洁、条件主机; -t
\"10\"命令执行超时时间s; -f使用所指定的Go模板格式化输出。

docker-machine active查看激活的主机

docker-machine config \[options\] name 查看到激活Docker主机的连接信息

docker-machine env \[options\] name
查看连接某个主机需要的环境变量。\--swarm显示Swarm 集群配置;
\--unset,-u取消对应的环境变量;

docker-machine inspect \[options\] name 输出指定docker主机的详细信息

docker-machine ip name 显示ip

3\) 管理docker主机

docker-machine start\|restart\|stop\|kill\|rm name
启动\|重启\|停止\|杀掉\|删除主机

4\) 远程执行docker命令

docker-machine ssh name command 例如: docker-machine ssh centos702
docker ps -a

8.3.3 Docker compose

定义和运行多个Docker容器的应用

1\) 重要概念

任务(task): 一个容器被称为一个任务。任务拥有独一无二的ID,
在同一个服务中的多个任务序号依次递增。

服务(service): 某个相同应用镜像的容器副本集合,
一个服务可以横向扩展为多个容器实例。

服务栈(stack): 由多个服务组成, 相互配合完成特定业务,
如Web应用服务、数据库。

2\) docker compose文件重要组成:

version: '3.8' compose文件版本, 最新为3.8, 默认用3就行。

services: 具体的docker服务。

webapp:

\...\...

networks: 服务所使用的网络定义。

mynet:

\...\...

3\) Docker-compose.yml模板文件参数(以下大部分是services参数)

https://docs.docker.com/compose/compose-file/

image: 指定服务的镜像名称、镜像ID、url。本地不存在会拉取镜像。

build: 可以指定Dockerfile所在文件夹的路径, 构建镜像, 不支持swarm。

build:

context: path/to/build/dir 可以是路径(推荐相对路径), 也可是git仓库的url

dockerfile: Dockerfile-alternate 为dockerfile文件名

args:

\- \"para1: value1\" 为build时的临时参数, 布尔值需要引号引起来

labels:为容器添加Docker元数据(metadata)信息

container_name: app指定容器名称。不支持swam模式。

environment:
设置环境变量。只给定名称的变量会自动获取运行Compose主机上对应变量的值。变量名称或者值中用到true\|false,
yes\|no等表达布尔含义的词, 最好放到引号里。

environment :

\- \"RACK_ENV: development\"

\- \"SESSION SECRET\"

env_file: 从文件中获取环境变量, env_file: /opt/web.env (多个用数组模式)

entrypoint:
覆盖容器中默认的人口命令。注意:也会取消掉镜像中指定的人口命令和默认启动命令。

entrypoint: python app.py 或 entrypoint: \[\"python\",\"app.py\"\]

command:覆盖容器启动后默认执行的命令(CMD), 格式:字符串或JSON数组

command: echo \"hello world\" 或 command:
\[\"bash\",\"-c\",\"echo\",\"hello world\"\]

expose:暴露端口, 但不映射到宿主机,
只被连接的服务访问。仅指定内部端口为参数。

expose:

\- \"3000\"

\- \"8000\"

ports:用于映射端口的标签, 两种格式。

ports:

\- \"3000\"

\- \"8000:8000\"

\- \"127.0.0.1:8001:8001\"

ports:

\- target: 80 容器端口

published: 8080 宿主机端口

protocol: tcp 协议类型

mode: host\|ingress host在每个节点上发布主机端口,
ingress对于集群模式端口进行负载均衡

volumes:挂载一个目录或者一个已存在的数据卷容器, 两种格式。

volumes:

\- \"/opt/data:/var/lib/mysql\[:ro\]\"

\- \"datavolume:/var/lib/mysql\" (已经存在的命名的数据卷。)

volumes: （顶级域）

\- type: volume 数据挂载类型, volume(共享数据)、 bind(共享配置文件)
、tmpfs(非持久化数据)

source: mydata 宿主机目录

target: /data 容器目录

volume: 配置额外的选项, 其key必须和type的值相同

nocopy: true volume 额外的选项, 在创建卷时禁用从容器复制数据

read_only: true 只读文件系统

volumes:

mydata: /opt/mysql8.0/tmp 推荐swarm模式

volumes_from:从另一个服务或容器挂载其数据卷:

volumes_from:

\- service_name

\- container_name

depends_on:服务依赖关系, 先启动被依赖的服务, 推荐。

depends_on:

\- db (为服务名)

\- redis

links: 链接到其它服务中的容器。SERVICE:ALIAS, 不支持swarm。

links:

\- db

\- db:database

networks: 所加入的网络。需要在顶级的networks
宇段中定义具体的网络信息。网络模式:\"bridge\"、\"none\"、\"host\" ,
(下面是双网卡示例)

services:

web1:

networks:

dev:

ipv4_address: 192.168.100.11

ipv6_address: 2001:3984:3989::10

networks:

dev:

driver: bridge\|overlay 推荐单机bridge, swarm用overlay

ipam: 自定义IPAM配置, 这是一个具有多个属性的对象

driver: default 使用默认驱动模式

config: 配置项

\- subnet: 192.168.100.0/24

其它参数:gateway、ip_range、aux_addresses 版本3不支持

\- subnet: \"2001:3984:3989::/64\" 为ipv6地址断

internal: true 创建外部隔离的覆盖网络, bridge连不上外网

outside: 指定的网络已在Compose之外创建, 也就是直接加入现有网络

external: true 注意其不能和driver, driver_opts, ipam, internal一起使用。

name: myweb 设置网络设置名称

extra_hosts: 指定host名称映射信息, 用于ip解析,
会添加到/etc/hosts文件中。

extra_hosts:

\- www.centos701.com:192.168.100.12

\- otherhost:50.31.209.229

restart : 指定重启策略, 不支持swarm。

restart:
always(总是)\|no(不重启)\|on-failure(失败时)\|unless-stopped(除非停止)

dns:自定义DNS 服务器。可以是一个值, 也可以是一个列表

extends:基于其他模板文件进行扩展

healthcheck:指定检测应用健康状态的机制

isolation:配置容器隔离的机制, 包括default、process和hyperv。

logging:跟日志相关的配置

secrets:配置应用的秘密数据。

sysctls:配置容器内的内核参数, 不支持swarm。

stop_signal:指定停止容器的信号, 默认为SIGTERM, 不支持swarm。

configs: 在Docker Swarm模式下, 管理和访问非敏感的配置信息。

deploy: 指定部署和运行时的容器相关配置。只在Swarm模式下生效:docker stack
deploy。

deploy:

endpoint_mode:
vip\|dnsrr服务暴露方式。vip所有请求都会通过一个对外的虚拟ip到达集群服务内。dnsrr所有请求会自动轮询(dnsrr)获取集群ip列表中的一个ip地址。

labels: 为服务添加元数据(metadata)信息

mode: replicated\|global指定服务提供的模式。replicated复制服务,
复制指定服务到集群的机器上。global全局服务, 服务将部署至集群的每个节点

replicas: 3 当mode为replicated时, 指定容器副本的数量。

placement: 服务限制和约束

max_replicas_per_node: 1 每个节点上的最大副本数, 对应replicated模式。

resources: 资源限制

limits: 设置容器的资源限制

cpus: \"0.5\" 设置该容器最多只能使用50%的CPU

memory: 500M 设置该容器最多只能使用500M的内存空间

reservations: 设置为容器预留的系统资源(随时可用)

cpus: \"0.2\" 为该容器保留20%的CPU

memory: 200M 为该容器保留200M的内存空间

restart_policy: 定义容器重启策略, 用于代替restart参数

condition: none\|on-failure\|any 定义容器重启策略

delay: 10s 尝试重启的间隔时间(默认为 0s)

max_attempts: 3 尝试重启次数(默认一直尝试重启)

window: 5s 检查重启是否成功之前的等待时间(默认 0s)

update_config: 用于配置滚动更新配置

parallelism: 3 \# 一次性更新的容器数量

delay: 3s \# 更新一组容器之间的间隔时间

failure_action: continue\|rollback\|pause 定义更新失败的策略,
继续\|回滚\|暂停

monitor: 30s 每次更新持续时间检测更新是否失败(ns\|us\|ms\|s\|m\|h,
默认为0)

max_failure_ratio: 0 更新期间容忍的失败率(默认值为0)

order: stop-first\|start-frist 更新期间的操作顺序,
stop-first旧任务在启动新任务之前停止(默认)。start-frist首先启动新任务,
并且正在运行的任务暂时重叠

rollback_config: 更新失败的回滚策略, 参数同update_config

parallelism; delay; failure_action; monitor; max_failure_ratio; order

devices:指定设备映射关系, 不支持Swarm 模式。

devices:

\- \"/dev/ttyUSBl:/dev/ttyUSB1\"

5\) docker-compose 命令(所有操作都要在.yml文件目录下)

a\) 基本格式: docker-compose \[-f \<arg\>\...\] \[options\] \[COMMAND\]
\[ARGS\...\]

-f,\--file FILE指定Compose模板文件, 默认为当前路径下的docker-compose.yml

-p,\--project-name NAME 指定项目名称, 默认使用当前所在目录为项目名

\--verbose 输出更多调试信息; -v, -version 打印版本并退出

b\) docker-compose config \[options\] 验证并查看compose文件配置。

\--resolve-image-digests 将镜像标签标记为摘要 ; -q, \--quiet
只输出错误信息

\--services 打印服务名, 一行一个; \--volumes 打印数据卷名, 一行一个

c\) docker-compose build \[options\] \[\--build-arg key=val\...\]
\[SERVICE\...\] 构建(重新构建)项目

\--compress通过gzip压缩构建上下环境; \--force-rm
删除构建过程中的临时容器

\--no-cache构建镜像过程中不用缓存; \--pull始终尝试通过拉取操作来获取镜像

-m,\--memory MEM为容器设置内存大小; \--build-arg
key=val为服务设置临时变量

d\) docker-compose up \[options\] \[\--scale SERVICE=NUM\...\]
\[SERVICE\...(是文件中的服务名)\]

自动完成构建镜像, (重新)创建服务, 启动服务,
并关联服务相关容器的一系列操作。

-d 在后台运行服务容器; \--no-deps 不启动服务所链接的容器

\--force-recreate 强制重新创建容器; \--no-recreate 如果容器已经存在,
则不重新创建。

\--build 在启动容器前构建服务镜像; \--no-build 不自动构建缺失的服务镜像

\--scale web=2 指定服务运行的数目 ; -t, \--timeout 10s(默认)
停止容器时候的超时

\--abort-on-container-exit 停止所有容器, 如果任何一个容器被停止,
不能与-d同时使用

\--remove-orphans 删除服务中没有在compose文件中定义的容器

e\) docker-compose down \[options\] 停止和删除所有容器、网络、卷、镜像。

\--rmi all\|loacal 其中all删除compose文件中所有镜像,
local删除镜像名为空的镜像

-v, \--volumes删除已经在compose文件中定义的和匿名的附在容器上的数据卷

f\) docker-compose exec \[options\] SERVICE COMMAND \[ARGS\...\]
在运行的容器内执行命令。

-d后台运行命令; \--privileged 获取特权; -u,\--user USER 指定运行的用户。

-e key=value 设置环境变量; -T 禁用分配TTY

\--index=index当有多个容器时指定容器索引, 默认第一个; docker-compose
exec \--index=1 web /bin/bash, web服务中包含多个容器

g\) docker-compose run \[options\] SERVICE \[COMMAND\] \[ARGS\...\]
在指定服务上执行命令。

-d 后台运行容器; \--name NAME为容器指定一个名字; -e
key=value设置环境变量值

\--entrypoint commad: 覆盖默认的容器启动指令; -u指定运行容器的用户名;

-p 80:80 映射容器端口到本地主机; \--rm 运行命令后自动删除容器;

\--no-deps不自动启动关联的服务容器;

h\) docker-compose start\|stop\|restart \[-t 20s\]
SERVICE\...启动\|停止\|重启已经存在的服务容器。

i\) docker-compose pause\|uppause \[SERVICE\...\]
暂停\|恢复一个服务容器。

j\) docker-compose scale web=3 db=2 设置指定服务运行的容器个数。

k\) docker-compose rm \[options\] \[SERVICE\...\]
删除所有(停止状态的)服务容器。

options: -f, \--force强制直接删除 ; -v, 删除容器所挂载的数据卷

l\) docker-compose kill \[-s SIGINT\] \[SERVICE\...\]
默认发送SIGKILL信号强制停止服务容器。

m\) docker-compose images \[-q\] \[SERVICE\... \] 列出服务所创建的镜像。

n\) docker-compose ps \[-q\] \[SERVICE\... \] 列出项目中目前的所有容器。

o\) docker-compose port \[\--index=index\] SERVICE
PRIVATE_PORT显示某个容器端口映射

p\) docker-compose logs \[-f\] 查看服务容器的输出, \[跟踪日志输出\]

q\) docker-compose top \[SERVICE \... \]
显示服务栈中正在运行的进程信息。

r\) docker-compose events \[-json\] \[SERVICE \...\]
实时监控容器的事件信息。

z\) docker-compose pull\|push \[options\] \[SERVICE\...\]
拉取\|推送服务依赖的镜像。

\--ignore-pull-failures忽略拉取镜像过程中的错误;
\--parallel多个镜像同时拉取

\--quiet拉取镜像过程中不打印进度信息

5.3.4 Docker swarm

提供Docker容器集群服务。Swarm采用了典型的"主从"结构,
通过Raft协议来在多个管理节点（Manager）中实现共识,
工作节点（Worker）上运行agent接受管理节点的统一管理和任务分配。

1\) docker swarm基本概念

Swarm集群(Cluster)为一组被统一管理起来的Docker主机。

节点(Node)是Swarm集群的最小资源单位, 分为:管理节点和工作节点。

任务是Swarm集群中最小的调度单位, 即一个指定的应用容器。

服务是由若干个任务组成, 分为两种:复制服务(replicated services)模式,
为默认模式, 每个任务在集群中会存在若干副本,
这些副本会被管理节点按照调度策略分发到集群中的工作节点上;
全局服务(global services)模式,
调度器将在每个可用节点都执行一个相同的任务。该模式适合运行节点的检查,
如监控应用, 日志分析应用等。

Swarm集群中的服务要被集群外部访问, 必须要能允许任务的响应端口映射出来。

dockers swarm管理服务端口为2377,
节点端口7946TCP/UDP(集群)、4789UDP(外部)。

2\) docker swarm基本命令

a\) docker swarm init \[options\] 初始化集群

\--advertise-addr x.x.x.x:2377 广播地址和端口, 多个ip时必须指定,
也可ens33:2377;

\--listen-addr 0.0.0.0:2377 监听地址, 也可是ens33:2377

\--cert-expiry duration 根证书的过期时长, 默认为90天;

\--data-path-addr :指定数据流量使用的网络接口或地址;

\--dispatcher-heartbeat 10s :分配组件的心跳时长, 默认为5 秒;

\--force-new-cluster 强制创建新集群;

\--task-history-limit 3 任务历史的保留个数, 默认为5 。

b\) docker swarm join-token manager\|worker
查看管理节点\|工作节点的token

c\) docker swarm join \--token \[init产生的token\] 加入集群中

d\) docker swarm update \[OPTIONS\] 更新集群, options同1)

e\) docker swarm leave \[-f\] 离开集群

3\) docker node options \[nodename\] 命令:

  -----------------------------------------------------------------------
  ls查看所有的节点                    inspect 查看一个或多个节点的详情
  ----------------------------------- -----------------------------------
  promote 工作节点升级为管理节点      demote 管理节点降级为工作节点

  ps 查看一个或多个节点中的task       rm 删除一个或多个节点

  update 新一个节点                   
  -----------------------------------------------------------------------

4\) docker service 命令

a\) docker service create \[options\] image \[command arg ..\] 部署服务

-d 后台模式; \--mode replicated(默认)\|global 服务模式; \--replicas 3
指定实例份数;

b\) docker service inspect \[options\] service \[service \... \]
查看服务详情

\--pretty 合适阅读的格式输出, \--format string 使用go模板输出

c\) docker service logs \[options\] service\|task 产看某个服务日志

\--details输出所有的细节日志信息; -f持续输出; \--tail
20只输出给定行数信息;

d\) docker service ls \[options\] 查看所有服务详情

\--filter filter只输出符合过滤条件的服务; -q, \--quiet只输出服务的ID
信息

e\) docker service ps \[options\] service \[service \... \]
列出服务中的任务信息。option同d)

f\) docker service rm \[-f\] service \[service \... \] 删除某个服务

g\) docker service rollback \[options\] service 回滚服务配置

g\) docker service scale service=replicas\... 设置某个服务个数

h\) docker service update \[options\] service 更新某个服务,
options类似create

5\) docker stack 命令

stack是构成特定环境中的service集合,
它是自动部署多个相互关联的服务的简便方法, 而无需单独定义每个服务。类似于
docker-compose.yml 文件

a\) docker stack deploy \[options\] stack(名) 在swarm集群上用部署stack

\--bundle-file ../dir 分布式应用程序包文件的路径

-c,\--compose-file xxx.yml 指定compose文件,
(默认当前目录的docker-compose.yml)

\--with-registry-auth 向 Swarm 代理发送 Registry 认证详细信息

b\) docker stack ls 列出现有堆栈

c\) docker stack \[options\] ps stack \[\...\] 列出堆栈中的任务

-f,\--filter \"key=value\" 条件过滤; -q,\--quiet只输出task的ID

d\) docker stack rm \[options\] stack \[\...\] 删除堆栈

e\) docker stack services \[options\] stack 列出堆栈中的服务

-f,\--filter \"key=value\" 条件过滤; -q,\--quiet只输出的ID

f\) docker stack down stack \[\...\] 移除某个堆栈（不会删除数据）

8.3.5 yaml语法

yaml是一种标记语言可以简单表达清单、散列表, 标量等数据形态, 文件后缀.yml

1\) 语法基础

大小写敏感

使用缩进表示层级关系, 缩进不允许使用tab, 只允许空格。缩进的空格数不重要,
只要相同层级的元素左对齐即可。

\'#\'表示注释

3\) YAML支持数据类型:

对象: 键值对的集合,
又称为映射（mapping）/哈希（hashes）/字典（dictionary）

数组: 一组按次序排列的值, 又称为序列（sequence）/列表（list）

纯量(scalars): 单个的、不可再分的值

4\) YAML对象

单个键值:key: value

多个键值:key:

key1: value1

key2: value2

类似于字典: key: { key1: value1, key2: value2}

5\) YAML数组

以-开头的行表示构成一个数组: - A 数组A

\- B 数组B

多维数组:companies:

\- id: 1

name: company1

price: 200W

\- id: 2

name: company2

price: 500W

companies是一个数组, 每一个数组元素又是由 id、name、price
三个属性构成。类似于列表:companies: \[{id: 1,name: company1,price:
200W},{id: 2,name: company2,price: 500W}\]

6\) YMAL纯量

字符串、布尔值、整数、浮点数、Null、时间、日期

7\) 引用

&锚点 和 \*别名, 可以用来引用

九 Kubernetes

9.1 kubernetes简介

9.1.1 k8s简介

Kubernetes是用于自动部署,
扩展和管理容器化应用程序的开源系统。其具有轻量级, 消耗资源小, 开源,
弹性伸缩, 负载均衡(IPVS)等优点。

9.1.2 k8s结构

![](media/image1.png){width="2.352777777777778in"
height="0.9736111111111111in"}1) k8s结构

如图所示

2\) Master组件

kube-apiserver: 负责对外暴露k8s api

etcd: 用于存储Kubernetes集群的所有数据

kube-scheduler: 负责为新创建的Pod选择可供其运行的节点

kube-controller-manager: 通过与apiserver交互使相应的资源达到预期状态

3\) Node组件

kubelet: 负责维护和管理节点上Pod的运行状态

kube-proxy: 负责维护主机上的网络规则以及转发

Container Runtime: 如Docker,rkt,runc等提供容器运行时环境

9.1.3 k8s资源

资源对象: Pod, ReplicaSet, ReplicationController, Deployment,
StatefulSet, DaemonSet, Job, CronJob, HorizontalPodAutoscaler

配置对象: Node, Namespace, Service, Secret, ConfigMap, Ingress, Label,
CustomResourceDefinition, ServiceAccount

存储对象: Volume, PersistentVolume, PersistentVolumeClaim, StorageClass

策略对象: SecurityContext, ResourceQuota, LimitRange

9.2 Pod

9.2.1 Pod参数

apiVersion: v1 #版本号

kind: Pod \| Deployment \| Job \| Ingress \| Service #类型

metadata: #元数据信息

name: string #名称

namespace: string #命名空间, 默认为default

labels: #自定义标签

\- name: string

annotations: #自定义注释

\- name: string

spec: #为pod容器详细内容

initContainers: #初始化容器, 用于部署前测试依赖是否就位

\- name: init-db

image: msyql:5.7

command: \[\'sh\', \'-c\', \'until nslookup mysql\'\]

containers: #为pod容器列表

\- name: string #容器名称

image: string 容器镜像名

imagePullPolicy: Always\|Never\|IfNotPresent 镜像拉取策略

command: \[string\] 容器启动命令列表

args: \[string\] 容器启动参数列表

workingDir: string 容器工作目录

volumeMounts: 挂载到容器内的存储卷设置

\- name: string 引用pod定义存储卷名, 需要volumes定义好。

mountPath: string 存储卷在容器内的绝对路劲

readOnly: true\|false 是否为只读, 默认为读写模式

ports: 容器暴露端口列表

\- name: string 暴露端口名

containerPort: int 容器需要监听端口

hostPort: int 主机监听端口, 设置后同一个主机无法启动第二个副本

protocol: TCP \| UDP 端口协议, 默认TCP

env: 环境变量列表

\- name: string 环境变量名

value: string

resources: 资源限制

limits: 上限

cpu: string CPU限制, 单位千分之一个cpu, 250m为cpu使用率25%

memory: string 内存限制, 单位MiB, GiB

requests: 下限

cpu: string

memory: string

lifecycle: #生命周期

postStart: #容器创建后立即执行的hook

exec:

command: \[\"/bin/sh\", \"-c\", \"echo test\"\]

preStop: #容器结束前执行的hook

exec:

command: \[\"/usr/sbin/nginx\", \"-s\", \"quit\"\]

livenessProbe: 存活指针, 判断Pod(中的应用容器)是否健康

exec: 在容器内执行命令, 执行成功(命令退出状态码为0), 则Pod就绪;

command: \[string\]

httpGet: 向容器发送Http Get请求, 通过Http状态码判断确定Pod就绪;

path: string (如/app/healthz)

port: number

host: string

scheme: string

httpHeaders:

\- name: string

value: string

tcpSocket: 打开一个TCP连接到容器的指定端口, 连接成功则确定Pod就绪。

port: number

initialDelaySeconds: 5 在初始化容器多少秒后开始第一次就绪探测;

timeoutSeconds: 10 对容器检查等待超时时间, 默认1s, 超时则表示不健康。

periodSecond: 20 则每隔多少秒周期性的做就绪探测, 默认10s, 最小值1s。

successThreshold: 3之前探测成功, 后续几次失败, 容器不健康, 默认值3 。

failureThreshold: 1 之前探测失败, 后续几次成功, 则容器就绪, 默认值1。

readinessProbe: #可读性探针, 用于探测容器端口等

...

securityContext: 限制不可信容器的行为, 保护系统和其他容器不受其影响。

privileged: false\|true 其中true为特权模式

restartPolicy: Always\|Never\|OnFailure 重启策略

nodeSelector: 指定pod分配到指定node上,
如部署mysql搭配到有sdd磁盘的node。

key: value (通过kubectl describe node name 查看的值)

imagePullSecrets: 拉取镜像是使用的secret名称, 私有镜像的身份凭证

\- name: string

hostNetwork: false 是否使用主机网络模式

volumes: 定义pod上共享存储卷列表

\- name: string 存储卷名

emptyDir: {} 类型, 与pod同生命周期的临时目录

hostPath: 表示挂载pod所在宿主机的目录

path: string

secret: 类型, 挂载集群预定义的secret对象到容器内部

secreName: string

items:

\- key: string

path: string

configMap: 类型, 挂在集群预定义的configMap对象到容器内部

name: string

items:

\- key: string

path: string

应用: kubectl create -f xx.yaml

查看: kubectl get pod \--show-labels

kubectl logs pod_name #查看pod日志

kubectl describe pod pod_name -n namespace #查看详情

进入容器: kubectl exec -it pod -c\|\--container name -n namespace \--
/bin/bash

9.2.2 Pod调度

版本控制器:RC(ReplicationController), RS(Replica Set), Deployment,
StatefulSet, ...

1\) Deployment或RC(全自动调度)

配置文件格式: nginx-deployment.yml

apiVersion: apps/v1

kind: Deployment

metadata:

name: nginx-deployment

spec:

replicas: 3 副本数

selector:

tier: frontend 调度标签, 或者使用下面判断

matchLabels: 筛选条件

tier: frontend

matchExpressions:

\- {key: tier, operator: In\|NotIn, values: \[frontend, ....\]}

minReadySeconds: 5 #休眠时间

strategy: 发布策略

type: Recreate(重建, 先删旧版本) \| RollingUpdate(滚动更新)

...

template: 为pod模板

metadata:

lables:

app: nginx

spec:

containers:

\- name: nginx 同上面的lables

image: nginx:1.7.9

ports:

\- containerPort: 80

应用: kubectl create -f xxx.yaml

查看: kubectl get rc \| rs \| deployment pod_name

2\) NodeSelector 定向调度

首先通过kubectl label命令给目标Node打上一些标签:

kubectl label nodes node-name key=value

使用实例:

spec.template: ...

spec: ...

nodeSelector:

key: value

3\) NodeAffinity:Node亲和性调度

用于替换NodeSelector的全新调度策略。目前有两种节点亲和性表达:硬限制和软限制。

配置文件格式:

apiVersion: v1

kind: pod

metadata:

name: with-node-affinity

spec:

affinity:

nodeAfiinity:

requiredDuringSchedulingIgnoredDuringExecution: 硬限制

nodeSelectorTerms:

\- matchExpressions:

\- key: beta.kubernetes.io/arch

operator: In

values:

\- amd64

preferredDuringSchedulingIgnoredDuringExecution: 软限制

\- weight: 1

preference:

\- matchExpressions:

\- key: disk-type

operator: In

values:

\- ssd

containers:

......

4\) PodAffinity:Pod亲和与互斥调度策略

5\) Taints(污点)和Tolerations(容忍)

Taint需要和Toleration配合使用, 让Pod避开那些不合适的Node。

用kubectl taint命令为Node设置Taint信息:

kubectl taint nodes k8s-node1
key=value:NoSchedule(不使用)\|PreferNoSchedule(尽量不用)\|NoExecute(立即去除无toleration的容器)

配置文件中声明容忍, 就可以调度此节点:

tolerations:

\- key: "key"

operator: "Equal"(要下面的value) \| "Exists"(不要下面的value)

value: "value"

effect: "NoSchedule"\|"PreferNoSchedule"\|"NoExecute"

tolerationSeconds: 3600 当effect为NoExecute时使用此参数,
表示宽限时间(s)。

6\) Pod Priority Preemption:Pod优先级调度

当发生资源不足的情况时, 系统可以选择释放一些不重要的负载(优先级最低的),
保障最重要的负载能够获取足够的资源稳定运行。

首先创建PriorityClasses(不属于任何命令空间):

apiVersion: scheduling.k8s.io/vibeta1

kind: PriorityClass

metadata:

name: high-priority

value: 100000 (数越大, 优先级越高, 大于1亿的系统保留)

globalDefault: false

description: "This priority class should be used for XYZ service pods
only"

在任意pod上使用:

...

spec: ...

priorityClassName: high-priority

注意:优先级抢占调度策略可能会导致一些pod无法成功调度。因此,
一旦发生资源紧张的局面, 首先要考虑的是集群扩容, 如果无法扩容,
则再考虑有监管的优先级调度特性。

8\) Job

9\) Cronjob:定时任务

10\) 自定义调度器

9.2.3 Pod管理

1\) Deployment升级和回滚

实例:nginx-deployment.yml

apiVersion: v1

kind: Deployment

metadata:

name: nginx-deployment (下面所有操作会用到)

spec:

strategy:

type: Recreate(重建) \| RollingUpdate(滚动更新, 默认)

rollingUpdate: 为滚动更新时, 有以下两种滚动方式, 二选一

maxUnavailable: 30% 更新时先将总副本数缩小30%, 然后创建新副本

maxSurge: 30% 更新时先创建新30%的新副本, 然后删旧副本

......

revisionHistoryLimit: 15 #保留历史版本数

spec:

containers:

\- name: nginx

image: nginx:1.7.9

......

升级: kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1
(自动升级)

kubectl edit deployment/nginx-deployment , 将nginx镜像改为1.9.1

kubectl apply -f nginx-deployment-v2.yml 新建一个文件, 修改升级部分。

查看: kubectl rollout status deployments nginx-deployment 滚动状态

kubectl rollout history deployment nginx-deployment 查看部署历史记录

kubectl rollout history deployment nginx-deployment \--revision=3
查看某一个版本

回滚: kubectl rollout undo deployment nginx-deployment

kubectl rollout undo deployment nginx-deployment \--to-revision=2
#指定版本

控制: kubectl rollout pause deployment nginx-deployment #暂停

kubectl rollout resume deploy nginx-deployment #恢复

2\) 其它服务滚动升级

RC: kubectl rolling-update oldName -f xxxNew.yml #升级

kubectl rolling-update oldName -f xxxNew.yml \--rollback #回滚

DaemonSet和StatefulSet更新和回滚类似于Deployment的滚动升级

3\) 扩容和缩容

命令行: kubectl scale deployment nginx-deployment \--replicas 5\|1

4\) 自动扩容和缩容:Horizontal Pod Autoscaler (HPA)

基于autoscaling/v1版本HorizontalPodAutoscaler配置文件, 仅支持CPU设置:

apiVersion: autoscaling/v1

kind: HorizontalPodAutoscaler

metadata:

name: php-apache

spec:

scaleTargetRef: 目标作用对象, 可以是Deployment, RC, ReplicaSet

apiVersion: apps/v1

kind: Deployment

name: php-apache

minReplicas: 1 最少副本数

maxReplicas: 10 最大副本数

targetCPUUtilizationPercentage: 70
期望每个Pod的CPU使用率为70%。该值根据CPU Request值进行设置, 如为200m,
系统将维持pod实际CPU使用值为140m

\-\--

基于autoscaling/v2beta2的HorizontalPodAutoscaler配置, 支持更多选项:

apiVersion: autoscaling/v2beta2

kind: HorizontalPodAutoscaler

metadata:

name: php-apache

spec:

scaleTargetRef: 目标作用对象, 可以是Deployment, RC, ReplicaSet

apiVersion: apps/v1

kind: Deployment

name: php-apache

minReplicas: 1 最少副本数

maxReplicas: 10 最大副本数

metrics: 目标指标值

\- type: Resource (基于资源的指标)

resource:

name: cpu

target:

type: Utilization

averageUtilization: 70

\- type: Pods (基于pod)

pods:

......

\- type: Object (基于某种资源对象, 如资源访问量)

object:

9.2.4 ConfigMap和Secret

1\) configMap和Secret实例

apiVersion: v1

kind: ConfigMap

metadata:

name: mycnf

data:

key1: val1 #保存单个属性

my.cnf: \| #保存一个配置文件

host=val1

port=val2

\-\--

apiVersion: v1

kind: Secret

metadata:

name: mycnf

type: Opaque #编码格式base64

data:

username: xxx #值echo -n \"test\" \| base64

password: xxx #值解码: echo -n \"xx\" \| base64 \--decode

应用: kubectl create -f xx.yaml

2\) 命令行创建

kubectl create configmap mycnf \--from-file=/path/dir
#注册该目录下所有配置

kubectl create configmap mycnf \--from-file=key1=/file1.txt
\--from-file=key2=/file2.txt

kubectl create configmap mycnf \--from-literal=key1=config1
\--from-literal=key2=config2

kubectl create secret docker-registry myregistry \--docker-server=xx
#使用docker认证key

3\) 查看配置

kubectl get configmap \| secret #配置列表

kubectl describe configmap \| secret mycnf -o yaml #查看配置详情

4\) 使用

spec.containers:

\- name: test

command: \[\"/bin/sh\", \"-c\", \"echo \${DB_HOST} \${PASSWORD}\"\]
#引用

env: #填充环境变量

\- name: DB_HOST

valueFrom:

configMapKeyRef:

name: mycnf

key: db.host

\- name: PASSWORD

valueFrom:

secretKeyRef:

name: mycnf

key: password

envFrom: #直接引入环境变量

\- configMapRef:

name: mycnf

volumeMounts: #挂载配置文件

\- name: config

mountPath: /etc/config

spec.volumes: #创建配置文件挂载

\- name: config

configMap:

name: mycnf

\- name: config-test #挂载到指定路径

configMap:

name: mycnf

items:

\- key: redis.conf

path: path/redis.conf #容器中的路径: /etc/config/path/redis.conf

\- name: secrets

secret:

secretName: mycnf

9.2.5 DaemonSet和StatefulSet

1\) DaemonSet

DaemonSet用于在每个k8s节点中将守护进程的副本作为后台进程运行, 如: 监控等

实例:

apiVersion: v1

kind: DaemonSet

metadata: ...

spec: (不需要指定副本数)

updateStrategy: 升级策略

type: RollingUpdate 滚动升级, 默认。

template:

metadata: ...

spec: ...

查看: kubectl get pods -o wide #查看pod分布

2\) StatefulSet

StatefulSet用来搭建有状态的应用集群(MySQL等), 很少使用, 一般用Operator

实例:

apiVersion: apps/v1

kind: StatefulSet

metadata: ...

spec:

serviceName: \"nginx\"

template:

metadata:

app: nginx

role: stateful

spec.containers: ...

volumeMounts:

\- name: www

mountPath: /user/share/nginx/html

volumeClaimTemplates: #挂载存储卷

\- metadata:

name: www

spec:

accessMode: \[\"ReadWriteOnce\"\]

resources:

requests:

storage: 1Gi

\-\--

aptVersion: v1

kind: Service

metadata:

name: nginx

spec:

ports: ...

selector:

app: nginx

role: stateful

clusterIP: None #创建headless service

查看: kubectl get pods -w -l role=stateful

kubectl get statefulset xxx

删除: kubectl delete pod -l role=stateful

9.3 Service

9.3.1 Service简介及定义格式

创建Service为一组具有相同功能的容器应用提供一个统一的入口地址,
并且将请求负载分发到后端的各个容器应用上。

配置文件模板:

apiVersion: v1

kind: Service

metadata:

name: string

namespace: string 不指定默认为default, 注意和pod在一个

labels: 自定义标签属性

\- name: string

annotations: 自定义注释

\- name: string

spec:

selector: \[\] 调度具有label标签的pod作为管理范围

type: ClusterIP(内部) \| NodePort(外部) \| loadBalancer(负载均衡)
指定servie访问类型

clusterIP: string 虚拟服务ip, 当type=loadBalancer时需要指定,
其可默认自动分配

sessionAffinity: string 负载策略,
该模式会将同一客户ip转发到同一后端pod。默认模式为RoundRobin轮询模式。

ports: 暴露端口

\- name: string 端口名称

protocol: string 端口协议, 默认TCP

port: int 服务监听端口

targetPort: int 转发到后端pod端口, 默认同port

nodePort: int 指定映射到物理机端口

status: 当spec.type=LoadBalancer时, 设置外部负载均衡器地址, 用于公有云

loadBalancer:

ingress:

ip: string 外部负载均衡器ip, 云平台提供

hostname: string 外部负载均衡器主机名

应用: kubectl create -f xx.yaml

查看: kubectl get svc

9.3.2 Headless Service

用于自己控制负载均衡的策略, 不使用Service提供的默认负载均衡的功能,
或者应用程序希望知道属于同组服务的其他实例。实例:

apiVersion: v1

kind: Service

metadata:

name: nginx

labels:

app: nginx

spec:

port:

\- port: 80

clusterIP: None 使Service就不再具有一个特定的ClusterIP地址

selector:

app: nginx

9.3.3 集群外部访问pod和service

1\) 将容器应用端口映射到物理主机

2\) 将service端口映射到主机

通过nodePort映射:需要设置type为NodePort, 同时设置nodePort端口。

通过设置LoadBalancer映射到loadbalancer地址, 用于共有云平台。

9.3.4 DNS服务

1\) dns简介

在集群内通过服务名对服务进行访问, 需要使用DNS服务对ClusterIP进行解析。

创建ConfigMap, Deployment, Service

9.3.5 Ingress

1\) Ingress简介

Ingress资源对象, 用于将不同URL的访问请求转发到后端不同的Service,
以实现HTTP层的业务路由机制。为使用Ingress, 需要创建Ingress
Controller(带一个默认backend服务)和Ingress策略设置来共同完成。

2\) nginx-controller

9.4 网络原理

9.4.1 kubernetes网络介绍

Kubernetes网络模型设计的一个基础原则是:每个Pod都拥有一个独立的IP地址,
并假定所有Pod都在一个可以直接连通的、扁平的网络空间中。其解决:容器到容器之间的直接通信;
抽象的Pod到Pod之间的通信; Pod到Service之间的通信;
集群外部与内部组件之间的通信

9.4.2 CNI网路模型

CNI定义的是容器运行环境与网络插件之间的简单接口规范, 通过一个JSON
Schema定义CNI插件提供的输入和输出参数。一个容器可以通过绑定多个网络插件加入多个网络中。

使用cni网络插件需要在kubelet启动配置中加入:\--network-plugin=\"cni\"

9.4.3 kubernetes网络策略

为了实现细粒度的容器间网络访问隔离策略, 引入了Network
Policy机制。Network
Policy的主要功能是对Pod间的网络通信进行限制和准入控制,
设置方式为将Pod的Label作为查询条件,
设置允许访问或禁止访问的客户端Pod列表。目前查询条件可以作用于Pod和Namespace级别。

网路配置策略说明:

apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:

name: test-network-policy

namespace: default 该网络策略作用的namespace

spec:

podSelector: 该网络策略作用的Pod范围, 如"role=db"标签的Pod。

matchLabels:

role: db

policyType: 网络策略类型

\- Ingress

\- Egress

ingress: 定义允许访问目标Pod的"入站"白名单规则

\- from: 对符合条件的客户端Pod进行网络放行

\- ipBlock: 基于客户端ip范围

cidr: 172.17.0.0/16

except: 不包括

\- 172.17.1.0/24

\- namespaceSelector: 基于namespace

matchLabels: 为namespace的labels标签

project: myproject

podSelector: 与namespace组合使用

......

\- podSelector: 基于客户端Label, 与namespace分开使用

matchLabels:

role: frontend

ports: 允许访问的目标Pod监听端口

\- protocol: TCP

port: 6379

egress: 定义目标Pod允许访问的"出站"白名单规则, 访问外部服务。

\- to: 允许访问的服务端信息。

\- ipBlock:

cidr: 10.0.0.0/24

ports: 目标Pod监听端口和协议

\- protocol: TCP

port: 5978

9.4.4 网络组件

实现所有Pod都在一个可以直接连通的扁平网络空间中。

1\) Flannel

2\) Open vSwitch

3\) Calico

9.5 共享存储

9.5.1 PV

Kubernetes引入PV和PVC两个资源对象对存储管理, 实现数据持久化。

PV(PersistentVolume, 持久化卷)是对底层网络共享存储的抽象,
将共享存储定义为一种\"资源\", 它与共享存储的具体实现直接相关, 如: Ceph,
GlusterFS, NFS. 实例:

apiVersion: v1

kind: PersistentVersion

metadata:

name: pv1

spec:

capacity: 存储能力

storage: 5Gi

accessModes: 访问模式, 三选一, 注意不同的存储卷模式的访问模式可能不一样

\- ReadWriteOnce 读写权限, 只能被单个Node挂载

\- ReadOnlyMany 只读权限, 允许多个Node挂载

\- ReadWriteMany 读写权限, 允许多个Node挂载

persistentVolumeReclaimPolicy: Recycle (回收空间,
只有NFS和HostPath支持), 回收策略, Retain保留策略, delete删除策略。

storageClassName: slow 存储类别, 用于PVC绑定

mountOptions: 挂载参数, 视情况设定。

......

volumeMode: Filesystem(默认) 存储卷模式, 其他:Block(块设备), iSCSI,
FC等。

nfs: 存储内类型, 此处以nfs为例。其他有Local, FC等

path: tmp

server: 172.168.0.2

nodeAffinity: 节点亲和性

required:

nodeSelectorTerms:

\- matchExpressions:

\- {key: kubernetes.io/hostname, operator: In, values: my-node}

应用: kubectl create -f xx.yaml

查看: kubectl get pv

9.5.2 PVC

1\) 创建pvc

PVC(PersistentVolumeClaim, 持久化卷声明)是用户对存储资源的一个"申请",
可以申请特定的存储空间和访问模式。实例:

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

name: myclaim

spec:

accessModes: 访问模式, 与PV设置要相同

\- ReadWriteOnce

resources: 资源请求

requests:

storage: 8Gi

storageClassName: slow 存储类别, 不设置同PV

volumeMode: Filesystem(默认) 存储卷模式,

selector: 选择器

matchLabels:

release: \"stable\"

matchExpressions:

\- {key: environment, operator: In, values: \[dev\]}

查看: kubectl get pvc

2\) 使用pvc

spec.containers: ...

volumeMounts:

\- name: www

subPath: /path #数据卷中路径

mountPath: /data/path #容器中路径

spec.volumes:

\- name: www

persistentVolumeClaim:

claimName: myclaim

9.5.3 StorageClass

资源对象StorageClass,
用于标记存储资源的特性和性能。StorageClass的定义主要包括名称、后端存储的提供者(provisioner)和后端存储的相关参数配置。StorageClass一旦被创建出来,
则将无法修改。如需更改,
则只能删除原StorageClass的定义重建。配置参数实例:

apiVersion: storage.k8s.io/v1

kind: StorageClass

metadata:

name: standard

annotations:

storageclass.beta.kubernetes.io/is-default-class="true"

provisioner: kubernetes.io/aws-ebs 存储资源提供者

parameters: 后端存储资源提供者的参数设置,
不同的Provisioner包括不同的参数设置

type: gp2

zone: ......

注意:不同的提供商, 配置参数将不同。

设置默认StorageClass, 需要在kube-apiserver配置参数添加:

\--admission-control=...,DefaultStorageClass

配置参数同上。查看:kubectl get sc

9.5.4 GlusterFS动态存储管理

1\) 准备工作

在计划用于GlusterFS的各Node上安装GlusterFS客户端:

yum -y install glusterfs glusterfs-fuse

kube-apiserver启动参数添加:\--allow-privileged=true

给部署GlusterFS节点打上storagenode=glusterfs标签:

kubectl label node k8s-node1 storagenode=glusterfs

2\) 创建GlusterFS管理服务容器集群

编辑glusterfs-daemonset.yml文件

kind: DaemonSet

apiVersion: extensions/v1beta1

metadata:

name: glusterfs

labels:

glusterfs: daemonset

annotations:

description: GlusterFS DaemonSet

tags: glusterfs

spec:

template:

metadata:

name: glusterfs

labels:

glusterfs: pod

glusterfs-node: pod

spec:

nodeSelector:

storagenode: glusterfs

hostNetwork: true

containers:

\- image: gluster/gluster-centos:latest

name: glusterfs

imagePullPolicy: IfNotPresent

volumeMounts:

\- name: glusterfs-heketi

mountPath: \"/var/lib/heketi\"

\- name: glusterfs-run

mountPath: \"/run\"

\- name: glusterfs-lvm

mountPath: \"/run/lvm\"

\- name: glusterfs-etc

mountPath: \"/etc/glusterfs\"

\- name: glusterfs-logs

mountPath: \"/var/log/glusterfs\"

\- name: glusterfs-config

mountPath: \"/var/lib/glusterd\"

\- name: glusterfs-dev

mountPath: \"/dev\"

\- name: glusterfs-misc

mountPath: \"/var/lib/misc/glusterfsd\"

\- name: glusterfs-cgroup

mountPath: \"/sys/fs/cgroup\"

readOnly: true

\- name: glusterfs-ssl

mountPath: \"/etc/ssl\"

readOnly: true

securityContext:

capabilities: {}

privileged: true

readinessProbe:

timeoutSeconds: 3

initialDelaySeconds: 60

exec:

command:

\- \"/bin/bash\"

\- \"-c\"

\- systemctl status glusterd.service

livenessProbe:

timeoutSeconds: 3

initialDelaySeconds: 60

exec:

command:

\- \"/bin/bash\"

\- \"-c\"

\- systemctl status glusterd.service

volumes:

\- name: glusterfs-heketi

hostPath:

path: \"/var/lib/heketi\"

\- name: glusterfs-run

\- name: glusterfs-lvm

hostPath:

path: \"/run/lvm\"

\- name: glusterfs-etc

hostPath:

path: \"/etc/glusterfs\"

\- name: glusterfs-logs

hostPath:

path: \"/var/log/glusterfs\"

\- name: glusterfs-config

hostPath:

path: \"/var/lib/glusterd\"

\- name: glusterfs-dev

hostPath:

path: \"/dev\"

\- name: glusterfs-misc

hostPath:

path: \"/var/lib/misc/glusterfsd\"

\- name: glusterfs-cgroup

hostPath:

path: \"/sys/fs/cgroup\"

\- name: glusterfs-ssl

hostPath:

path: \"/etc/ssl\"

3\) 创建Heketi服务

Heketi是一个提供RESTful API管理GlusterFS卷的框架,
支持GlusterFS多集群管理。

先创建一个ServiceAccount对象:heketi-service-account.yml

apiVersion: v1

kind: ServiceAccount

metadata:

name: heketi-service-account

创建Heketi服务:kuketi-deployment-svc.yml

\-\--

kind: Deployment

apiVersion: extensions/v1beta1

metadata:

name: deploy-heketi

labels:

glusterfs: heketi-deployment

deploy-heketi: heket-deployment

annotations:

description: Defines how to deploy Heketi

spec:

replicas: 1

template:

metadata:

name: deploy-heketi

labels:

glusterfs: heketi-pod

name: deploy-heketi

spec:

serviceAccountName: heketi-service-account

containers:

\- image: heketi/heketi

imagePullPolicy: IfNotPresent

name: deploy-heketi

env:

\- name: HEKETI_EXECUTOR

value: kubernetes

\- name: HEKETI_FSTAB

value: \"/var/lib/heketi/fstab\"

\- name: HEKETI_SNAPSHOT_LIMIT

value: \'14\'

\- name: HEKETI_KUBE_GLUSTER_DAEMONSET

value: \"y\"

ports:

\- containerPort: 8080

volumeMounts:

\- name: db

mountPath: \"/var/lib/heketi\"

readinessProbe:

timeoutSeconds: 3

initialDelaySeconds: 3

httpGet:

path: \"/hello\"

port: 8080

livenessProbe:

timeoutSeconds: 3

initialDelaySeconds: 30

httpGet:

path: \"/hello\"

port: 8080

volumes:

\- name: db

hostPath:

path: \"/heketi-data\"

\-\--

kind: Service

apiVersion: v1

metadata:

name: deploy-heketi

labels:

glusterfs: heketi-service

deploy-heketi: support

annotations:

description: Exposes Heketi Service

spec:

selector:

name: deploy-heketi

ports:

\- name: deploy-heketi

port: 8080

targetPort: 8080

4\) 为Heketi设置GlusterFS集群

首先设置GlusterFS集群信息。注意:Heketi要求在一个GlusterFS集群中至少有3个节点。topology.json配置文件:

{

\"clusters\": \[

{

\"nodes\": \[

{

\"node\": {

\"hostnames\": {

\"manage\": \[

\"k8s-master2\" 为主机名

\],

\"storage\": \[

\"20.0.40.52\" 为ip地址

\]

},

\"zone\": 1

},

\"devices\": \[

\"/dev/sdb1\" 为未创建文件系统的裸机设备, 可以有多块盘

\]

},

......

}

进入Heketi容器, 使用命令行工具heketi-cli完成GlusterFS集群的创建:

heketi-cli topology load \--json=topology.json

Heketi完成了GlusterFS集群的创建,
同时在GlusterFS集群的各个节点的/dev/sdb盘上成功创建了PV和VG。

heketi-cli topology info 查看详情

5\) 定义StorageClass

配置文件:storageclass-gluster-heketi.yml

apiVersion: storage.k8s.ip/v1

kind: StorageClass

metadata:

name: gluster-heketi

provisioner: kubernetes.io/glusterfs

parameters:

resturl: \"http://20.0.40.53:8080\" #填写宿主机ip, 不要填写clusterip

restauthenabled: \"false\"

6\) 定义PVC

配置pvc-gluster-heketi.yml

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

name: pvc-gluster-heketi

spec:

storageClassName: gluster-heketi

accessModes:

\- ReadWriteOnce

resources:

requests:

storage: 1Gi

7\) Pod使用PVC资源

配置pod-use-pvc.yaml

apiVersion: v1

kind: Pod

metadata:

name: pod-use-pvc

spec:

containers:

\- name: pod-use-pvc

image:

......

volumeMounts:

\- name: gluster-volume

mounthPath: \"/pv-data\"

readOnly: false

volumes:

\- name: gluster-volume

persistentVolumeClain:

claimName: pvc-gluster-heketi

9.5.5 CSI存储机制

容器存储接口Container StorageInterface (CSI)机制,
目标是在Kubernetes和外部存储系统之间建立一套标准的存储管理接口,
通过该接口为容器提供存储服务,
类似于CRI(容器运行时接口)和CNI(容器网络接口)。

主要包括两种组件:CSI Controller和CSI Node。

CSI
Controller的主要功能是提供存储服务视角对存储资源和存储卷进行管理和操作。将其部署为单实例Pod。其包含辅助sidecar容器(内包含external-attacher和external-provisioner两个容器)和CSI
Driver存储驱动容器(第三方提供)

CSI
Node的主要功能是对主机(Node)上的Volume进行管理和操作。建议将其部署为DaemonSet,
在每个Node上都运行一个Pod。包含:辅助sidecar容器node-driver-registrar和CSI
Driver存储驱动容器

具体配置:https://blog.csdn.net/qq_31136839/article/details/100084930

9.6 Kubernetes集群管理

9.6.1 Node的管理

1\) Node隔离

当节点硬件升级或维护时, 需要隔离节点。

a\) 创建配置文件隔离:unschedule_node.yml

apiVersion: v1

kind: node

metadata:

name: k8s-node1

labels:

kubernetes.io/hostname: k8s-node1 主机名尽量和节点名一致

spec:

unschedulable: true 系统不在调度此节点

b\) 使用kubectl命令隔离:

隔离:kubectl cordon k8s-node1

恢复调度:kubectl uncordon k8s-node1

2\) Node扩容

9.6.2 Namespace

Kubernetes通过命名空间和Context的设置对不同的工作组进行区分,
使得它们既可以共享同一个Kubernetes集群的服务, 也能够互不干扰。

a\) 创建2个Namespace:

+-------------------------------------+--------------------------------+
| namespace-development.yml           | namespace-production.yml       |
+=====================================+================================+
| apiVersion: v1                      | apiVersion: v1                 |
|                                     |                                |
| kind: Namespace                     | kind: Namespace                |
|                                     |                                |
| metadata:                           | metadata:                      |
|                                     |                                |
| name: development                   | name: production               |
+-------------------------------------+--------------------------------+

kubectl create -f namespace-development.yml -f namespace-production.yml

kubectl get namespaces

b\) 为2个命名空间分别定义一个Context, 即运行环境。创建命令:

kubectl config set-cluster kubernetes-cluster
\--server=https:192.168.100.16:8080

kubectl config set-context ctx-dev \--namespace=development
\--cluster=kubernetes-cluster \--user=dev

kubectl config set-context ctx-prod \--namespace=production
\--cluster=kubernetes-cluster \--user=prod

查看:kubectl config view

kubectl config命令在\${HOME}/.kube目录下生成了一个名为config的文件,
可以通过手工编辑该文件来设置Context。

c\) 使用工作组

进入ctx-dev:kubectl config use-context ctx-dev ,
可以在该环境下使用正常命令建立pod及服务, 不需要在配置文件中特别指定。

切换工作组ctx-prod:kubectl config use-context ctx-prod ,
同样可以下该创建pod及服务。

9.6.3 kubernetes资源管理

1\) 更新资源对象的Label

添加label:kubectl label pod redis-pod role=backend

查看label:kubectl get pods -L role

删除label:kubect label pod redis-pod role-

修改label:kubect label pod redis-pod role=master \--overwrite

2\) 计算资源管理

......

containers:

......

resources:

requests: 下限, 保证基本运行就行, 建议不要设置过大造成资源浪费

memory: "1Gi" 单位:Ti, Gi, Mi 大小写敏感

cpu: "250m" \| 0.25 为0.25个cpu, 两种设置方式都可以

limits: 上限

memory: "5Gi"

cpu: "500m"

......

修改大页内存(Huge Page), 可以提高TLB (内存地址映射的缓存表,
用来保存逻辑内存地址与物理内存的对应关系) 缓存命中率。内存页默认为4KB,
一般最大支持2MB。对于那些需要大量内存(1GB以上内存)的程序来说,
大内存页的优势是很明显的。

3\) 资源配置范围管理(LimitRange)

LimitRange机制对Pod和容器的Requests和Limits配置进一步做出限制。实例说明:

创建一个Namespace:kubectl create namespace limit-example

为Namespace设置limitRange, 创建配置文件:(limits.yml)

apiVersion: v1

kind: LimitRange

metadata:

name: mylimits

spec:

limits:

\- max: 为pod上限, 既是pod中容器总和上限

cpu: 4

memory: 2Gi

min: 为pod下限, 既是pod中容器总和下限

cpu: 200m

memory: 6Mi

maxLimitRequestRatio: 限制pod的limits与requests比例上限

cpu: 3

memory: 2

type: pod

\- default: 默认值, 必须指定

cpu: 300m

memory: 200Mi

defaultRequest: 为Pod中container未指定requests的默认值, pod无此参数

cpu: 200m

memory: 100Mi

max: 为pod所有容器上限

cpu: 2 所有容器cpu limits总和\<=2

memory: 1Gi 所有容器limits总和要\>=1Gi

min: 为pod所有容器下限

cpu: 100m

memory: 3Mi

maxLimitRequestRatio: 限制pod中所有容器limits与requests比例上限

cpu: 5

memory: 4

type: Container

创建该限制:kubectl create -f limits.yml \--namespace=limit-example

查看:kubectl describe limits mylimits \--namespace=limit-example

4\) 资源服务质量管理(Resource QoS)

如果Pod配置的Requests值等于Limits值,
那么该Pod可以获得的资源是完全可靠的。

如果Pod的Requests值小于Limits值,
那么该Pod获得的资源可分成两部分:完全可靠的资源,
资源量的大小等于Requests值; 不可靠的资源,
资源量最大等于Limits与Requests的差额。

超售机制能有效提高资源的利用率,
同时不会影响容器申请的完全可靠资源的可靠性。

可压缩资源(CPU):空闲CPU资源按照容器Requests值的比例分配

不可压缩资源(内存):如果Pod使用的内存量小于它的Requests的配置,
那么这个Pod可以正常运行; 如果Pod使用的内存量超过了它的Requests的配置,
那么这个Pod有可能被Kubernetes杀掉;
如果Pod使用的内存量超过了它的Limits设置,
那么操作系统内核会杀掉Pod所有容器的所有进程中使用内存最多的一个,
直到内存不超过Limits为止。

Kubernetes将容器划分成3个QoS等级:

Guaranteed (完全可靠的):所有容器的Limits值都和Requests值全部相等

Burstable (弹性波动、较可靠的):Pod中所有容器都未定义资源配置

BestEffort(尽力而为、不太可靠的):Pod中一部分容器Requests值小于Limits值,
或部分容器未定义资源配置。

当Pod的CPU Requests无法满足时, 优先级低(BestEffort)的pod会被杀掉。

OOM计分系统:分高的容易被杀掉

资源配额管理:

9.6.4 资源稀缺时Pod驱逐机制

9.6.5 kubernetes集群监控

1\) Metrics Server监控

2\) Prometheus+Grafana性能监控平台

9.6.6 集群同一日志管理

Kubernetes推荐采用Fluentd+Elasticsearch+Kibana完成对系统和容器日志的采集、查询和展现工作。

9.6.7 Helm:应用包管理工具

1\) Helm简介

Chart:一个Helm包, 其中包含运行一个应用所需要的工具和资源定义,
还可能包含Kubernetes集群中的服务定义。Helm由HelmClient和TillerServer两个组件组成。

Release:在Kubernetes集群上运行的一个Chart实例。

Repository:用于存放和共享Chart仓库。

2\) Helm安装

HelmClient是一个客户端,
拥有对Repository、Chart、Release等对象的管理能力。二进制软件包:<https://github.com/kubernetes/helm/releases>

TillerServer负责客户端指令和Kubernetes集群之间的交互, 根据Chart定义,
生成和管理各种Kubernetes的资源对象。使用helm init
\[\--tiller-image当网络不行, 先拉取镜像, 然后加上次参数\]命令进行安装。

验证:helm version

3\) Helm常见用法

helm search \[name\] 搜索可用的Chart

helm inspect name 查看chart详细信息

helm install \[options\] name\|path\|url 安装chart,
输出的资源信息都可以通过kubectl查看

可以自定义Chart的配置:先通过helm inspect 查看可配置内容,
创建配置文件config.yml, 以key: value写入配置信息, 安装:helm install name
-f config.yml , 多个配置文件时, 最后一个优先生效。

helm应用更新或回滚:先创建更新配置文件:config.yml

更新:helm upgrade \[options\] -f config.yml nomadic-terrier name

回滚:helm rollback \[options\] nomadic-terrier 2 (回滚到版本2)

查看历史版本:helm history name

options: \--timeout 300命令完成时间, 默认300s; \--wait等待Pod,
直到其状态变为ready, PVC才完成绑定; \--no-hooks该命令会跳过Hook执行;
\--recreate-pods会引起所有Pod的重建(Deployment所属的Pod除外)。

helm delete release(2) 删除一个release

helm list \--deleted \--all 删除所有release

4\) helm仓库

helm repo list 列出所有仓库

helm repo add url 添加仓库

helm repo update 更新仓库中的chart

5\) Chart目录及配置文件

6\) Chart.yml文件

7\) 自定义chart

8\) 搭建私有Repository

9.7 Trouble Shooting

9.7.1故障排除方法

1\) 查看系统Event:

kubectl describe pod\|service name \--namespace=name

2\) 查看容器日志

kubectl logs pod_name -c container_name

3\) 查看Kubernetes服务日志

systemctl status kube-xxx

journalctl -u kube-xxx
