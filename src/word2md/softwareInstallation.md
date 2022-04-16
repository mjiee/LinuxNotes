1.1 VMware功能

1\) 虚拟机\--快照: 保存虚拟机当前状态, 可用于出错恢复到当前状态。

虚拟机\--管理\--克隆: 快速创建一个相同的虚拟机, 推荐用链接克隆,
但是不能删除原始镜像。

2\) vmware网络模式

Bridged: 桥接模式就是将主机网卡与虚拟机虚拟的网卡利用虚拟网桥进行通信,
最简单, 适用于IP资源充足情况,
ip网段要和主机一致。注意需要将其手动桥接到正在使用的网卡上。

NAT: 主机网卡直接与虚拟NAT设备相连,
然后虚拟NAT设备与虚拟DHCP服务器一起连接在虚拟交换机VMnet8上。适用于ip资源稀缺,
需要将主机VMnet8和NAT配置一致。当选用NAT模式时先使用vmware的网络编辑器,
编写ip规则,
然后更具编写规则更改主机的VMnet8网卡。如果手动配置ip可以关闭dhcp功能。

Host-Only: 就是NAT模式去除了虚拟NAT设备, 然后使用VMnet1虚拟网卡连接,
与外网隔开, 只与主机相互通讯

详情见:
[[https://blog.csdn.net/zkuncn/article/details/78452098]{.underline}](https://blog.csdn.net/zkuncn/article/details/78452098)

1.2 安装linux

1\) Vmware安装

到阿里云或华为云镜像站下载xxx.dvd的镜像文件

安装流程：打开VMware\--典型\--选择安装的镜像文件iso,
然后选稍后安装\--版本选liunx中的centos或Redhat都成\--选择位置,
其他盘\--磁盘大小,
拆分为多块磁盘\--对设备进行调整(cd/dvd选择iso镜像)\--网络模式\--开启虚拟机\--选用US语言\--installation
source不管, software根据需要选择, device (选择custom自定义, 然后done)
\--开始安装

安装centos: 磁盘分区时, 使用LVM(逻辑卷), /boot分区使用ext4(centos8是xfs,
debain时bootable为on )文件系统, swap使用swap(debian使用逻辑分区),
其它使用xfs文件系统。注意/boot, swap和/ 是必须分区。

安装manjaro:
分区时，先创建分区表gpt，然后必须创建一个8M的文件系统为unformatted标记为bios-grub的分区。

注意事项:

a\) 通过克隆虚拟机创建时, MAC地址和UUID重复问题

删除ifcfg-ens160文件中的UUID,
修改IP；删除/etc/udev/rules.d/70-persistent-net.rules文件；重启

或者是通过uuidgen命令生成一串uuid号, 替换掉ifcfg-ens160中的uuid号就行。

2)  物理机安装

    使用ventoy制作u盘启动项：ventoy intstall格式化u盘 \--
    复制镜像文件到u盘。

    bios配置：关闭secure boot；关闭ROM；开启UEFI；开启wipe
    data(删除磁盘所有数据)；设置u盘为第一启动项。保存退出安装系统。

1.3 centos优化

1\) 修改主机名: /etc/hostname中的添加主机名

2\) 关闭提示音: vim /etc/inputrc, 将set bell-style none前面的#去掉,
改为set bell-style visible, 之后重启系统。

3\) 修改/etc/sysconfig/network-scripts/ifcfg-ens160, 详情见1.5

4\) 配置合理的防火墙规则, 关闭SELinux(方便实验, 生产环境需要酌情而定):
setenforce 0\|1 (关闭\|开启) ; getenforce ;
永久关闭/etc/sysconfig/selinux

5\) 配置dnf源,
见https://developer.aliyun.com/mirror/或https://mirrors.huaweicloud.com/

debian: /etc/apt/sources.list

6\) 更改ssh服务端口, 配置ssh秘钥对登录, 及限制密码, root等登陆方式

7\) 生产环境中避免直接使用root, 可以编辑sudo用户

8\) 指定合理的监控策略, 定时备份重要文件

9\) 配置系统同时打开最大文件数及连接线程数, 见1.7

10\) 同步时间服务器, 见1.6

11\) 通过chattr锁定重要系统文件: /etc/passwd, shadow, group, gshadow,
inittab

12\) 禁用不需要的服务

13\) 实验环境建议提前安装软件: vim, lrzsz, java,
python3.8(注意安装依赖), gcc\*, make, cmake, wget, bzip2, zip, unzip

1.4 升级内核

1\) 升级大版本内核

进入官网: <http://elrepo.org/tiki/tiki-index.php>

ELRepo安装: rpm \--import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

yum -y install
https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm

载入elrepo-kernel元数据: yum \--disablerepo=\\\*
\--enablerepo=elrepo-kernel repolist

查看可用的rpm包: yum \--disablerepo=\\\* \--enablerepo=elrepo-kernel
list kernel\*

安装4.4内核: yum -y install \--enablerepo=elrepo-kernel kernel-lt.x86_64

设置开机启动: cat /boot/grub2/grub.cfg \| grep menuentry

grub2-set-default \"CentOS Linux (4.4.221-1.el7.elrepo.x86_64) 7
(Core)\"

grub2-editenv list

先重启系统, 再卸载老版本。

卸载老版本: rpm -qa \| grep kernel \| grep 3.10

yum remove 上面出现的4个

安装4.4内核相关包: yum -y install \--enablerepo=elrepo-kernel
kernel-lt-headers.x86_64 kernel-lt-tools.x86_64
kernel-lt-tools-libs.x86_64 (上面卸载的相应的4.4版本)

重启系统。

内核相关包说明:

kernel-lt: 最核心的包, 所有Linux系统的基础

kernel-lt-devel: 内核开发包,
更多是提供给内核开发人员开发内核的一些功能模块

kernel-lt-doc: 内核包的指引文档

kernel-lt-headers: 内核的头文件,
一般其他应用需要调用内核能力就要引入这些头文件

kernel-lt-tools: 内核级别的一些工具

kernel-lt-tools-libs: 内核级别工具所依赖的包

kernel-lt-tools-libs-devel: 内核级别工具开发所需的依赖包

2\) 升级小版本内核

dnf update

批量卸载老版本内核: dnf remove \$(rpm -qa \| grep kernel \| grep -v
\$(uname -r))

1.5 网卡设置

1\) centos

修改/etc/sysconfig/network-scripts/ifcfg-ens160

TYPE=Ethernet 网卡类型:以太网

PROXY_METHOD=none 代理方式:关闭状态

BROWSER_ONLY=no 只是浏览器(yes\|no)

BOOTPROTO=static 设置网卡获得ip地址的方式(static\|dhcp\|none\|bootp)

DEFROUTE=yes 设置为默认路由(yes\|no)

IPV4_FAILURE_FATAL=no 是否开启IPV4致命错误检测(yes\|no)

IPV6INIT=yes IPV6是否自动初始化

IPV6_AUTOCONF=yes IPV6是否自动配置

IPV6_DEFROUTE=yes IPV6是否可以为默认路由

IPV6_FAILURE_FATAL=no 是不开启IPV6致命错误检测

IPV6_ADDR_GEN_MODE=stable-privacy IPV6地址生成模型

NAME=ens33 网卡物理设备名称

UUID=6e89ea13-f919-4096-ad67-cfc24a79a7e7 UUID识别码

DEVICE=ens33 网卡设备名称

ONBOOT=yes 开机自启(yes\|no)

IPADDR=192.168.100.11 IP地址

NETMASK=255.255.255.0 子网掩码,也可使用掩码长度表示(PREFIX=24)

GATEWAY=192.168.100.2 网关

#DNS1=114.114.114.114 首选DNS

#DNS2=8.8.8.8

配置dns: vi /etc/resolv.conf 添加nameserver 114.114.114.114

重启网络: nmcli connection reload \[ens160\]; 对于centos7:
/etc/rc.d/init.d/network restart

2\) debain

修改/etc/network/interfaces

auto lo #这两行必须有

iface lo inet loopback

auto ens33 开机自动激活

iface ens33 inet dhcp \| static 配置接口连接状态

address 192.168.100.101

netmask 255.255.255.0

gateway 192.168.100.2

双网卡双IP

auto ens33

iface ens33 inet static

address 114.80.0.4

netmask 255.255.255.0

gateway 114.80.0.3

auto ens34

iface ens34 inet static

address 192.168.100.101

netmask 255.255.255.0

单网卡双IP

auto ens33

iface ens33 inet static

address 192.168.1.116

netmask 255.255.255.0

gateway 192.168.1.2

auto ens33:1

iface ens33:1 inet static

address 192.168.2.135

netmask 255.255.255.0

gateway 192.168.2.2

添加域名服务器

vi /etc/systemd/resolved.conf

DNS=114.114.114.114 8.8.8.8

或vi /etc/resolv.conf

nameserver 202.96.128.86

nameserver 8.8.4.4

重启网络: /etc/init.d/networking restart

1.6 时间自动更新设置

1\) 设置时区

设置: cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime或

timedatectl set-timezone Asia/Shanghai

查看: timedatectl

2\) 开机自动更新, 在/etc/rc.d/rc.local中加入:

/usr/sbin/ntpdate -s ntp1.aliyun.com ; /usr/sbin/hwclock -w

chomd +x /etc/rc.d/rc.local

定时更新, 在/etc/crontab中加入:

0 \*/1 \* \* \* root /usr/sbin/ntpdate -s ntp1.aliyun.com

3\) centos8时间同步

vim /etc/chrony.conf, 注释掉pool 2.centos.pool.ntp.org iburst , 添加:

server 210.72.145.44 iburst

server ntp1.aliyun.com iburst

重启chrony并设置开机启动, systemctl restart chronyd.service

检查: chronyc sourcestats -v ; chronyc sources -v

注意: 在docker 中是需要更改时区即可: timedatectl set-timezone
Asia/Shanghai

1.7 内核优化

1\) 设置limit

查看: ulimit -a

修改整个系统能打开的文件描述符: echo 1000000 \> /proc/sys/fs/file-max

修改所有用户每个进程可打开文件描述符: echo fs.nr_open=210000 \>\>
/etc/sysctl.conf

编辑/etc/security/limits.conf, (注意下面的值要比nr.open小,
不然会导致启动问题)

\* soft nofile 200000 打开文件数

\* hard nofile 200000 (\*指所有用户, 可设置单个用户或组,
如:nignx或@mysql)

#\* soft nproc 65535 连接线程数, 看情况设定

#\* hard nproc 65535

在/etc/pam.d/login追加: session required pam_limits.so (使登陆生效)

重启或重新登陆

2\) 开启BBR

BBR是一种拥塞控制算法, 能加速网络传输速度。

添加以下内容到/etc/sysctl.conf, 刷新sysctl -p

net.core.default_qdisc=fq

net.ipv4.tcp_congestion_control=bbr

2 vim8.2

下载vim版本: <https://github.com/vim/vim/releases>

安装依赖: dnf install gcc make ncurses ncurses-devel (debian:
ncurses-dev)

卸载老版本: dnf remove vim

./configure \--prefix=/usr/local/vim \\ #注意提前创建文件夹

\--with-features=huge \\ #支持最大特性

\--enable-multibyte \\ 打开多字节支持, 可以在Vim中输入中文

\--enable-cscope \\ 打开对cscope的支持

\--enable-gui=gtk2 \\ 打开GUI支持

\--enable-python3interp=yes \\ #打开对python3编写的插件的支持

\--with-python-config-dir=/usr/lib64/python3.8/config-3.8-x86_64-linux-gnu
(该路径找不见用/bin/python3-config \--configdir)

make \[VIMRUNTIMEDIR=/usr/local/vim/share/vim/vim82
\](运行时候所需资源的目录)

make install

ln -s /usr/local/vim/bin/\* /usr/local/bin/.

注意: 如果出现找不见新版本情况, 用alias来定位; 发现python3不对用,
在命令行用:echo has(\"python3\") 验证

3 docker

3.1 yum安装docker

卸载老版本: yum remove docker docker-client docker-client-latest
docker-common docker-latest docker-latest-logrotate docker-logrotate
docker-engine

安装依赖: yum install -y yum-utils device-mapper-persistent-data lvm2

添加稳定源: yum-config-manager \--add-repo
https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo ; yum
makecache fast

安装: yum -y install docker-ce ; 启动测试

将其它用户加入docker组

配置docker加速:

在/etc/docker/daemon.json中配置加速:

{

\"registry-mirrors\": \[

\"https://hub-mirror.c.163.com\",

"阿里源"\[在<https://account.aliyun.com/login>获取\]

\]

}

注意默认的数据路径在/var/lib/docker目录下

3.2 二进制包安装docker

下载地址:
<https://download.docker.com/linux/static/stable/x86_64/docker-19.03.9.tgz>

解压包, 将docker目录下所有内容软连接至/usr/local/bin

ln -s .../docker/\* /usr/local/bin

配置systemd管理docker: /usr/lib/system/system/docker.service

\[Unit\]

Description=Docker Application Container Engine

Documentation=https://docs.docker.com

After=network-online.target firewalld.service

Wants=network-online.target

\[Service\]

Type=notify

ExecStart=/usr/local/bin/dockerd

ExecReload=/bin/kill -s HUP \$MAINPID

TimeoutStartSec=0

TimeoutSec=0

RestartSec=2

Restart=always

Restart=on-failure

Delegate=yes

KillMode=process

StartLimitBurst=3

StartLimitInterval=60s

LimitNOFILE=infinity

LimitNPROC=infinity

LimitCORE=infinity

\[Install\]

WantedBy=multi-user.target

配置阿里加速

启动docker: systemctl daemon-reload ; systemctl start docker ; systemctl
enable docker

3.3 安装docker machine

base=https://github.com/docker/machine/releases/download/v0.16.0 &&

curl -L \$base/docker-machine-\$(uname -s)-\$(uname -m)
\>/tmp/docker-machine &&

chmod +x /tmp/docker-machine &&

ln -s /tmp/docker-machine /usr/local/bin/docker-machine

安装脚本(也可不要):

base=https://raw.githubusercontent.com/docker/machine/v0.16.0

for i in docker-machine-prompt.bash docker-machine-wrapper.bash
docker-machine.bash

do

sudo wget \"\$base/contrib/completion/bash/\${i}\" -P
/etc/bash_completion.d

done

执行: source /etc/bash_completion.d/docker-machine-prompt.bash

3.4 安装docker compose

依赖包: py-pip python libffi openssl gcc libc make(正常都有)

1\) 通过pip安装:

pip3 install -U docker compose ; 卸载pip uninstall docker-compose

2\) 二进制包安装:

curl -L
\"https://github.com/docker/compose/releases/download/1.26.0/docker-compose-\$(uname
-s)-\$(uname -m)\" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
(也可以下载到其它地方通过软连接应用)

3.5 windows安装docker

安装最新版wsl, https://aka.ms/wsl2kernel, 如果此前安装过需要设置(wsl
\--set-default-version 2(2换为最新版本号))

下载windows版docker, 点击安装。

重启电脑, 手动运行docker, 右击docker运行小图标, setting,
在resources/network中设置ip, 在docker engine中设置镜像加速,
然后重启docker。

在power中运行: wsl -l -v , 查看docker是否运行正常。

3.6 安装containerd

导入docker源, dnf -y install containerd.io

3.7 安装podman

dnf -y install podman

配置国内源: vi /etc/containers/registries.conf

unqualified-search-registries = \[\"docker.io\"\]

\[\[registry\]\]

prefix = \"docker.io\"

location = \"3ms08qnb.mirror.aliyuncs.com\"

4 python3.8

4.1 centos

安装依赖: yum -y install ncurses-devel openssl-devel zlib-devel
bzip2-devel libffi-devel expat-devel gdbm-devel db4-devel readline-devel
sqlite-devel tk-devel xz-devel libpcap-devel

安装: ./configure \--prefix=/usr/local/python3 \--enable-optimizations ;

make ; make test ; make install

创建软连接: ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3

ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3

更换pip源(pip.conf):

\[global\]

index-url = http://mirrors.aliyun.com/pypi/simple

\[install\]

trusted-host=mirrors.aliyun.com

4.2 debian

apt install libbz2-dev libncursesw5-dev libgdbm-dev liblzma-dev
libsqlite3-dev tk-dev uuid-dev libreadline-dev build-essential
zlib1g-dev libnss3-dev libssl-dev libffi-dev curl

./configure \--prefix=/usr/local/python3 \--enable-optimizations

make ; make test ; make install

5 java11

解压压缩包, 并移动到/opt下: /usr/local/java11/jdk-11.0.7

修改/etc/profile文件: export JAVA_HOME=/usr/local/java11/jdk-11.0.7

export CLASSPATH=\$JAVA_HOME/lib/\[jrt-fs.jar\]:\$CLASSPATH

export PATH=\$JAVA_HOME/bin:\$PATH

source profile

6 Go

6.1 go安装

安装依赖: yum -y install gcc-c++ gcc

下载golang: <https://golang.org/dl/> ,
并解压到/usr/local/go/goxxx(方便更新)下

go命令: ln -s /usr/local/go/goxxx/bin /usr/local/bin/.

添加环境变量/etc/profile: export GOROOT=/usr/local/go

export GOBIN=\$HOME/bin

export GOPATH=\$HOME/.go //与下面的二选一

export GOMODCACHE=\$HOME/.mod 下载缓存

设置代理: go env -w GOPROXY=https://goproxy.cn,direct

6.2 相关设置

/usr/local/go目录下目录内容:

/bin: 包含可执行文件, 如: 编译器, Go工具

/doc: 包含示例程序, 代码工具, 本地文档等

/lib: 包含文档模版

/misc: 包含与支持Go编辑器有关的配置文件以及cgo的示例

/src: 包含源代码构建脚本和标准库的包的完整源代码 (Go是一门开源语言)

/src/cmd: 包含Go和C的编译器和命令行脚本

7 Node.js

7.1 安装node和npm

1\) 二进制版

下载二进制包: https://nodejs.org/en/download/

解压文件: tar xvf node-xxx.tar.xz

配置(/etc/profile): export NODE_HOME=/usr/local/node

验证: node -v

普通用户配置: npm config set registry
https://registry.npm.taobao.org更换国内源

npm config set prefix \$HOME/.npm //全局安装位置, 解决全局安装权限问题

export PATH=\$PATH:\~/.npm/bin //配置普通用户PATH

npm config set cache \"/path\" 缓存位置

2\) apt安装

配置缓存: curl -sL https://deb.nodesource.com/setup_11.x \| bash -
(setup后跟安装的版本)

apt-get -y install nodejs

7.2 安装yarn

1\) Debian

导入存储库的GPG密钥: curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg
\| apt-key add -

启用echo \"deb https://dl.yarnpkg.com/debian/ stable main\" \| tee
/etc/apt/sources.list.d/yarn.list

更新缓存: apt-get update ; 安装软件: apt-get -y install yarn ; 检查:
yarn -v

2\) centos

curl \--silent \--location https://dl.yarnpkg.com/rpm/yarn.repo \| tee
/etc/yum.repos.d/yarn.repo

dnf -y install yarn

3\) npm

npm install -g yarn

4\) 配置

yarn global bin \| dir 查看bin目录, 安装目录

yarn config set prefix \~/.yarn 全局安装位置, 不用配置, 非root默认

yarn config set cache-folder \"/path\" 设置缓存目录

每个用户配置: 配置PATH=\"\$PATH:\$HOME/.yarn/bin\"

配置源:

yarn config set registry https://registry.npm.taobao.org

也可在\~/.yarnrc中编辑

8 PHP

8.1 Debian安装php

1\) 源码包安装

下载安装包: wget https://www.php.net/distributions/php-7.4.0.tar.gz
；解压

安装依赖: apt-get install libzip-dev bison autoconf build-essential
pkg-config git-core libltdl-dev libbz2-dev libxml2-dev libxslt1-dev
libssl-dev libicu-dev libpspell-dev libenchant-dev libmcrypt-dev
libpng-dev libjpeg8-dev libfreetype6-dev libmysqlclient-dev
libreadline-dev libcurl4-openssl-dev librecode-dev libsqlite3-dev
libonig-dev

编译安装: ./configure \--prefix=/usr/local/php7
\--with-config-file-scan-dir=/usr/local/php7/etc/php.d
\--with-config-file-path=/usr/local/php7/etc \--enable-mbstring
\--enable-zip \--enable-bcmath \--enable-pcntl \--enable-ftp
\--enable-xml \--enable-shmop \--enable-soap \--enable-intl
\--with-openssl \--enable-exif \--enable-calendar \--enable-sysvmsg
\--enable-sysvsem \--enable-sysvshm \--enable-opcache \--enable-fpm
\--enable-session \--enable-sockets \--enable-mbregex \--enable-wddx
\--with-curl \--with-iconv \--with-gd \--with-jpeg-dir=/usr
\--with-png-dir=/usr \--with-zlib-dir=/usr \--with-freetype-dir=/usr
\--enable-gd-jis-conv \--with-openssl \--with-pdo-mysql=mysqlnd
\--with-gettext=/usr \--with-zlib=/usr \--with-bz2=/usr
\--with-recode=/usr \--with-xmlrpc \--with-mysqli=mysqlnd

make && make install

复制配置文件:

cp /usr/local/php7/etc/php-fpm.conf.default
/usr/local/php7/etc/php-fpm.conf

cp /usr/local/php7/etc/php-fpm.d/www.conf.default
/usr/local/php7/etc/php-fpm.d/www.conf

cp php.ini-production /usr/local/php7/etc/php.ini

配置环境变量(/etc/profile):
PATH=\$PATH:/usr/local/php7/bin:/usr/local/php7/sbin

启动: php-fpm

2\) apt安装

安装SURY存储库签名密钥: apt install -y gnupg2

导入密钥: wget -qO - https://packages.sury.org/php/apt.gpg \| apt-key
add -

echo \"deb https://packages.sury.org/php/ stretch main\" \| tee
/etc/apt/sources.list.d/php7.x.list

安装: apt-get update && apt-get -y install php7.4 ; 检查: php -v

安装其他模块: apt-get install php7.4-mysql php7.4-zip ......

8.2 composer安装

1\) 使用脚本安装

php -r \"copy(\'https://install.phpcomposer.com/installer\',
\'composer-setup.php\');\"

php composer-setup.php

mv composer.phar /usr/local/bin/composer

切换国内源: composer config -g repo.packagist composer
<https://packagist.phpcomposer.com>

更新composer: composer selfupdate

查看: composer \--version

2\) 下载安装

curl -sS https://getcomposer.org/installer \| php

mv composer.phar /usr/local/bin/composer

9 Nginx

9.1 Nginx安装

安装依赖: yum -y install gcc\* pcre pcre-devel zlib zlib-devel openssl
openssl-devel

创建nginx用户: useradd -r -s /sbin/nologin nginx

安装nginx:

./configure \--prefix=/usr/local/nginx \\ (以下模块都是默认不支持的)

\--with-threads \--with-file-aio \\ (线程池模块, 对于有媒体等服务时使用)

\--with-http_ssl_module \\ (需要openssl lib)

\--with-http_v2_module \\ (支持http/2特性)

\--with-http_realip_module \\ (如果nginx前还有反向代理服务器需要此模块)

\--with-debug \\(开启日志调试功能)

\--with-http_stub_status_module \\(nginx状态统计模块)

\--with-http_addition_module \\(用于响应前后添加响应内容模块)

\--with-http_auth_request_module \\(基于子请求结果实现客户端授权)

\--with-http_gzip_static_module
\\(是否以.gz结尾的预压缩文件替代普通文件)

\--with-stream \\(开启tcp或udp代理stream模块)

make && make install

ln -s /usr/local/nginx/sbin/nginx /usr/local/bin

配置日志轮转功能。

nginx相关目录:

sbin脚本目录, modules运行模块安装目录, logs日志目录(包error.log,
nginx.pid, nginx.lock), conf配置文件目录, html网页文件目录

9.2 Nginx启动管理

编辑nginx启动脚本, 软连接到/ect/rc.d/init.d/:

#!/usr/bin/bash

#chkconfig: 35 85 86

#description: nginx server control tools

ngxc=\"/usr/local/nginx/sbin/nginx\"

pidf=\"\$(/usr/bin/cat /usr/local/nginx/logs/nginx.pid)\"

case \$1 in

-t) \$ngxc -t ;;

start) \$ngxc ;;

restart) kill -s HUP \$pidf ;;

stop) kill -s QUIT \$pidf ;;

\*) echo \"please input start\|restart\|stop\|-t\" ; exit 1 ;;

esac

配置文件语法: nginx -t

或者配置systemd管理(/etc/systemd/system/nginx.service):

\[Unit\]

Description=nginx

After=network.target

\[Service\]

Type=forking

ExecStart=/usr/local/nginx/sbin/nginx

ExecReload=/usr/local/nginx/sbin/nginx -s reload

ExecStop=/usr/local/nginx/sbin/nginx -s quit

PrivateTmp=true

\[Install\]

WantedBy=multi-user.target

9.3 docker部署nginx

拉取稳定镜像: docker pull nginx:stable

运行: docker run -d -p 80:80 -p 443:443 \[-v
/path/nginx.conf:/etc/nginx/nginx.conf:ro -v
/path/html:/usr/share/nginx/html:ro \] \--link develop:web \--name
myNginx nginx:stable

注意: a) 以root用户运行, 对于配置文件建议进入手动更改,
如果要使用其它配置文件放入/etc/nginx/conf.d

b\) 注意日志轮转设置

**10** Apache

10.1 yum安装

yum -y install httpd

启动systemctl start\|enable httpd; 网页位置: /var/www/html/; 配置文件:
/etc/httpd/conf/..；日志文件/var/log/httpd/..

完整性校验MD5算法

10.2 源码包安装

需要先安装apr, apr-util(依赖apr), pcre(不依赖apr)三个依赖包,

安装依赖: yum -y install gcc\* make libxml2-devel pcre-devel
openssl-devel expat-devel python-devel

安装apr: ./configure \--prefix=/usr/local/apr ; make && make install

安装apr-util: ./configure \--prefix=/usr/local/apr-util
\--with-apr=/usr/local/apr ; make && make install

安装pcre: ./configure \--prefix=/usr/local/pcre ; make && make install

安装apache2.4: ./configure \--prefix=/usr/local/httpd
\--with-apr=/usr/local/apr \--with-apr-util=/usr/local/apr-util
\--with-pcre=/usr/local/pcre \--enable-so \--enable-ssl \--with-zlib
\--with-mpm=event \--enable-deflate=shared \--enable-expires=shared
\--enable-rewrite=shared

apache安装在usr/local/httpd, 那么配置文件就在该目录下的/conf/httpd.conf,
网页文件和帮助文档/htdocs/, 日志文件/logs/,
启动方法/usr/local/httpd/bin/apachectl -k start,
配置文件语法检测apachectl -t

10.3配置systemd管理

1\) systemctl启动方法一(推荐)

创建/etc/systemd/system/httpd.service脚本, 如下:

\[Unit\]

Description=httpd - web server

After=network.target 说明在network之后启动, 随便设置

Before=sshd.service 在sshd之前启动, 可不要, 只是说明

\[Service\]

User=username 指定其它用户或组

Group=groupname

Type=forking(后台运行) 启动类型, 默认simple(ExecStart字段启动的为主进程)

ExecStart=/usr/local/httpd/bin/apachectl -k start

ExecReload=/usr/local/httpd/bin/apachectl -k restart

ExecStop=/usr/local/httpd/bin/apachectl -k stop

PrivateTmp=True启动时在/tmp生成systemd-private-xx存放临时文件。可有可无

\[Install\] 定义如何安装这个配置文件, 即怎样做到开机启动

WantedBy=multi-user.target 表示该服务所在的Target,
表示在运行级别3时才能用

设置完后重新加载服务: systemctl daemon-reload

2\) systemctl启动方法二:

ln -s /usr/local/httpd/bin/apachetl /etc/rc.d/init.d/httpd, 修改httpd,
在第三行下面添加:

\# chkconfig: 35(运行级别为3和5) 85(启动顺序) 75(关闭顺序)
后两个不能现有的重复

\# description: Activates/Deactivates Apache Web Server

设置完后重新加载服务: systemctl daemon-reload

11 Tomcat

1\) 先安装java运行环境

2\) 将安装包解压到/usr/local/tomcat

3\) 启动Tomcat: tomcat/bin/catalina.sh start\|stop
或使用tomcat/bin/startup.sh\|shutdown.sh

4\) 如果没有指定java环境变量, 需要在setclasspath.sh中指定

12 keepalived

1\) 直接安装: dnf -y install keepalived

2\) 源码包安装

keepalived下载地址: <http://www.keepalived.org/download.html>

解压文件, 安装依赖: yum install -y gcc openssl-devel pcre-devel
libnl3-devel

进入安装目录: ./configure \--prefix=/usr/local/keepalived

make && make install

3\) 配置开机启动: (keepalived.service, 该文件会自动生成, 没有用下面的)

\[Unit\]

Description=LVS and VRRP High Availability Monitor

After= network-online.target syslog.target

Wants=network-online.target

\[Service\]

Type=forking

PIDFile=/var/run/keepalived.pid

KillMode=process

EnvironmentFile=/usr/local/keepalived/etc/sysconfig/keepalived

ExecStart=/usr/local/keepalived/sbin/keepalived \$KEEPALIVED_OPTIONS

ExecReload=/bin/kill -HUP \$MAINPID

\[Install\]

WantedBy=multi-user.target

4\) 修改配置文件和日志文件路径:

vi /usr/local/keepalived/etc/sysconfig/keepalived

KEEPALIVED_OPTIONS=\"-f
/usr/local/keepalived/etc/keepalived/keepalived.conf -S 0 -D\" #-f
指定配置文件路径 -S 指定日志路径 0 表示local0.\*

vi /etc/rsyslog.conf (追加)

#Save keepalived log

local0.\* /var/log/keepalived.log #指定日志文件路径

13 Mysql

13.1 源码包安装

13.2 二进制包免安装

1\) 环境准备

下载安装包: mysql-5.7.28-linux-glibc2.12-x86_64.tar

卸载maraidb

创建目录: mkdir -p /var/mysql/data /var/mysql/log

将压缩包解压到/usr/local/mysql5.7下, 命名为mysql

创建mysql用户: useradd -r -s /usr/sbin/nologin mysql

修改属主: chown -R mysql.mysql /usr/local/mysql/mysql5.7 /var/mysql/data
/var/mysql/log

创建软链接: ln -s /usr/local/mysql/mysql5.7/bin/\* /usr/local/bin/ ;
建议修改PATH方便升级。

2\) 初始化数据库

mysqld \--initialize \[\--initialize-insecure\] \--user=mysql
\--basedir=/usr/local/mysql/mysql5.7 \--datadir=/var/mysql/data/
(记住最后的初始密码; \--initialize-insecure不要密码)。如果初始化失败,
更具提示安装依赖(libaio-devel), 并清空data/3306目录, 重新初始化

3\)
编写配置文件/etc/my.cnf或/usr/local/mysql/etc/my.cnf(开启数据库加\--defaults-file=)

4\) 启动管理

mysql启动顺序: mysql.server(用于systemctl) \--
mysqld_safe(命令行mysqld_safe &) \-- mysqld(命令行mysqld &)

启动方法1): ln -s /usr/local/mysql/support-files/mysql.server
/etc/rc.d/init.d/mysqld

systemctl daemon-reload

启动方法2): vim /etc/systemd/system/mysqld.service

\[Unit\]

Description=MySQL Server

Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html

After=network.target syslog.target

\[Service\]

User=mysql

Group=mysql

Type=forking

ExecStart=/usr/local/bin/mysqld
\--defaults-file=/usr/local/mysql/etc/my.cnf

LimitNOFILE = 5000

\[Install\]

WantedBy=multi-user.target

5\) 可能需要安装的依赖

yum -y install libaio libaio-devel numactl libncurses\* perl perl-devel
perl-Time-HiRes perl-DBD-MySQL libev

物理备份工具-xtrabackup:

下载安装包:
https://www.percona.com/downloads/XtraBackup/Percona-XtraBackup-2.4.12/binary/redhat/7/x86_64/percona-xtrabackup-24-2.4.12-1.el7.x86_64.rpm

安装: rpm -i\[vh\] percona-xtrabackup-24-2.4.4-1.el7.x86_64.rpm

13.3 mysql配置文件my.cnf

\[mysqld\]

user=mysql

basedir=/usr/local/mysql/mysql5.7

datadir=/var/mysql/data

server_id=6

port=3306

socket=/tmp/mysql.sock

secure_file_priv=/tmp/mysql5.7 设置允许导入导出数据目录, null不允许,
空值为所有。注意该文件一定要提前创建, 不然会启动不起来。

log_error=/var/mysql/log/mysql.err.log 错误日志

log_bin=/var/mysql/log/mysql_bin (binlog, 目录+日志前缀)

sync_binlog=1 设置binlog日志刷盘策略;

binlog_format=row 控制binlog记录格式

max_binlog_size=100M 日志轮替大小, 默认1G。

expire_logs_days=14 日志删除时间(14天)

gtid-mode=on 开启GTID日志管理

enforce-gtid-consistency=true 强制gtid的一致性

log-slave-updates=1 slave更新是否记入日志（主从复制时使用）

slow_query_log=1 开启慢日志

slow_query_log_file=/var/mysql/log/slow.log

long_query_time=0.8 (时间阈值, 0.8s)

log_queries_not_using_indexes=1 记录不走索引的慢日志

autocommit=0\|1 事务自动提交, 对于交易数据库关闭用0

max_connections=1500 最大连接数, 根据配置定

max_connect_errors=50 最大错误连接数

interactive_timeout=1200 交互式连接不操作断开等待时间(s)

wait_timeout=600 非交互式连接不操作断开等待时间(s)

innodb_flush_log_at_trx_commit=1 为redo log写入的时间点, 高安全用1,
高性能用0

innodb_flush_method=fsync 刷盘策略, 高性能。高安全用O_DIRECT

innodb_buffer_pool_size=1G 缓冲区buffer大小, 建议为物理内存50%-80%,
注意大页。

innodb_log_buffer_size=32M 为redo日志缓冲区大小（redo,undo日志等,
默认16M）

innodb_log_file_size=48M 为redo日志文件大小(ib_logfile0-1, 默认48M)

innodb_log_files_in_group=2 为redo日志文件个数, 默认2个。

skip-name-resolve 禁用dns解析, 解决远程连接慢的问题, 但有时回引发错误

auto_increment_increment=2 步进值, 避免自增ID字段的冲突, 主主复制,
有几台就几

auto_increment_offset=1 起始值, 主主复制, 按顺序设定

\[mysql\] 代表我们使用mysql命令登录mysql数据库时的默认设置

socket=/tmp/mysql.sock

prompt=3306 \[\\\\d\]\>

\[client\] 代表客户端默认设置内容

socket=/tmp/mysql.sock 使用XBK备份时要加

host=localhost 保存密码, 生产上不建议使用。

user=root

password=\$MYSQL_ROOT_PASSWORD

13.4 Debian安装mysql

1\) 卸载自带的maraidb

添加加速源: cat \> /etc/apt/sources.list.d/mysql-community.list \<\< EOF

deb https://mirrors.tuna.tsinghua.edu.cn/mysql/apt/debian stretch
mysql-5.7 mysql-8.0 mysql-tools EOF

更新缓存: apt-get update ; 查看: apt-cache policy mysql-community-server

安装依赖: perl-base perl-modules-5.24 libperl5.24 perl psmisc libmecab2
libgdbm3 libncurses5 libtinfo5 mysql-common

安装: apt-get -y install mysql -client mysql-community-client
mysql-community-server

2\) 下载mysql-5.7.28-linux-glibc2.12-x86_64.tar包

安装依赖: apt-get install libaio1 perl-base perl-modules-5.24
libperl5.24 perl psmisc libmecab2 libgdbm3 libnuma1

解压安装, 同上。apt-get install libpcre3-dev libssl-dev perl make
build-essential curl libperl-dev libjpeg-dev libpng-dev libxml2-dev
libmcrypt-dev libmhash-dev libfreetype6-dev libjpeg62-turbo-dev mlocate

3\) 同样是上面的依赖,
下载mysql-server_5.7.30-1debian9_amd64.deb-bundle.tar包

解压, 安装顺序:

dpkg -i mysql-common_5.7.30-1debian9_amd64.deb

dpkg-preconfigure mysql-community-server_5.7.30-1debian9_amd64.deb

dpkg -i libmysqlclient20_5.7.30-1debian9_amd64.deb

dpkg -i libmysqlclient-dev_5.7.30-1debian9_amd64.deb

dpkg -i libmysqld-dev_5.7.30-1debian9_amd64.deb

dpkg -i mysql-community-client_5.7.30-1debian9_amd64.deb

dpkg -i mysql-client_5.7.30-1debian9_amd64.deb

dpkg -i mysql-common_5.7.30-1debian9_amd64.deb

dpkg -i mysql-community-server_5.7.30-1debian9_amd64.deb

dpkg -i mysql-server_5.7.30-1debian9_amd64.deb

4\) debian免安装包

安装依赖libaio\* libnuma\* libncurses5\*

其它同7.2

13.5 docker部署mysql

server:

docker run -d \[-p 3306:3306 \--ip=x -v /path/conf:/etc/mysql/conf.d -v
/path/data:/var/lib/mysql -v /path/log:/var/log/mysql(注意属主)\] -e xxx
\--name myDB mysql:latest

注意: 初始root密码可以通过docker logs myDB查看, 也和用-e来指定环境变量,
如:

-e MYSQL_ROOT_PASSWORD=xxx 设置root初始密码, 注意该root

-e MYSQL_USER=xxx -e MYSQL_PASSWORD=xxx 创建用户并设置密码

-e MYSQL_ALLOW_EMPTY_PASSWORD=yes 初始化数据库时不创建密码

-e MYSQL_RANDOM_ROOT_PASSWORD=yes 初始化数据库时创建随机密码

部署时建议挂载log和backup, 加上\--ip=分配一个固定ip, 不然每次都变

其默认配置文件参数:

\[mysqld\]

pid-file = /var/run/mysqld/mysqld.pid

socket = /var/run/mysqld/mysqld.sock (配置文件中不用再创建)

datadir = /var/lib/mysql

secure-file-priv= NULL

!includedir /etc/mysql/conf.d/ \# Custom config should go here

client:

docker run -it \--rm \--link myDB:myDB mysql:latest mysql -hmyDB -uroot
-pmykey

other:

docker exec myDB sh -c 'exec mysqldump ...' \> /path/db.sql

docker exec -i myDB sh -c 'exec mysql ...' \< /path/dp.sql

14 redis

14.1 centos安装redis

安装依赖: dnf -y install gcc make

解压安装包, 移动安装包至/usr/local下, 进入redis-x.x.x

编译: make, \[编辑后可以直接使用, 也可用make
install安装到默认路径(/usr/local/bin)\]

复制配置文件(redis.conf)到/etc/redis/6379.conf \[启动:src/redis-server
/etc/redis/6379.conf\]

配置systemctl管理: cp utils/redis_init_script
/etc/init.d/redis.server(只适用make install后)

14.2 docker部署redis

1\) 直接安装: dnf -y install redis

2\) docker部署:

server:

docker run -d \[ -p 6379:6379 \--ip=xx -v path/data:/data -v
/path/redis.conf:/etc/redis/redis.conf \] \--name redisServer
redis:latest \[redis-server /etc/redis/redis.conf \| \--appendonly yes
\] 没有配置文件加后面的。

或者Dockerfile: FROM redis

COPY redis.conf /usr/local/etc/redis/redis.conf

CMD \["redis-server", "/etc/redis/redis.conf"\]

client:

docker run -it \--rm \[\--network redis_net\] \--link
redisServer:myredis redis:latest redis-cli -h myredis
(注意需要和redis-server在同一个网络空间)

14.3 redis配置文件

##主配置##

daemonize yes #以守护进程模式运行(后台运行, docker部署不用设置)

port 6379 #端口, 可以不用设置, 默认6379

protected-mode no #关闭保护模式, 允许远程访问,
如果设置了密码和bind可打开。

bind 127.0.0.1 #本机的网卡对应的IP地址, 只允许来自指定网卡的Redis请求

loglevel notice #日志记录级别, debug, verbose, notice(生产用),
warning(只记录警告错误)

logfile \"/var/log/redis/redis.log\" #日志文件名和位置

syslog-enabled no #是否将日志输出到系统日志

databases 16 #设置数据库数量, 默认数据库为0

##快照方式##

dbfilename dump.rdb #为RDB持久化数据文件名

dir /data/6379 #持久化文件存储位置

save 900 1 #持久化保存时间, 900s内有1次更改, 就持久化

save 300 10 #为300秒内有10个更改

save 60 10000 #为60秒内有10000个更改

##附加模式##

appendonly yes #开启AOF持久化, 打开aof日志功能

appendfilename appendonly.aof #指定更新日志文件名

no-appendfsync-on-rewrite no #当AOF日志文件即将增长到指定百分比时,
redis通过调用BGREWRITEAOF是否自动重写AOF日志文件。

appendfsync always \| everysec \| no #同步策略: 每个命令都同步到aof \|
每秒写一次到aof \| 由操作系统判读缓存区大小来写入aof;
注意用aof就不要用下面三个参数。

\# rdbcompression yes
#用rdb备份时,是否进行压缩。注意用rdb备份不要上面两个参数。

\# rdbchecksum yes #存储或者加载rbd文件时进行校验(更耐攻击),
但性能下降10%

\# stop-writes-on-bgsave-error yes #开启快照功能, 后台rdb失败,
Redis会停止更新操作。

##安全##

requirepass 123 #设置密码(默认无密码),
注意记得修改systemctl启动文件将shutdown行修改为 \$CLIEXEC -a \$PASSWORD
-p \$REDISPORT shutdown

##限制##

maxclients 128#设置最大连接数, 0为不限制

maxmemory 1073741824 #最大使用内存(byte)

maxmemory-policy volatile-lru(默认策略,
只对设置过期时间的key进行LRU算法删除)\|allkeys-lru(删除不经常使用的key)\|volatile-random(随机删除即将过期的key)\|allkeys-random(随机删除一个key)\|volatile-ttl(删除即将过期的key)\|noeviction(不过期,
写操作返回报错) #内存清理策略, 如果达到maxmemory值, 采用此策略

maxmemory-samples 3 #默认随机选择3个key, 从中淘汰最不经常用的

##虚拟内存##

vm-enabled no #是否启用虚拟内存机制, (将访问少的数据swap到磁盘,
数据分页存放)

vm-swap-file /data/swap/redis.swap #虚拟内存文件位置

vm-max-memory 0 #redis使用swap的上限,
(0表示所有index存在内存,value存在磁盘)

vm-page-size 32 #每个页面的大小为32byte, 每个page只能存一个值

vm-pages 134217728 #设置swap文件中page数量

vm-max-threads 4 #访问swap文件的线程数, 不要超过机器的核数

##高级配置##

hash-max-zipmap-entries 512 #哈希表中元素(条目)总个数不超过设定数量时,
采用线性紧凑格式存储来节省空间

hash-max-zipmap-value 64 #哈希表中每个value的长度不超过多少byte时,
采用线性紧凑格式存储来节省空间

list-max-ziplist-entries 512
#list数据类型多少节点以下会采用去指针的紧凑存储格式

list-max-ziplist-value 64
#list数据类型节点值小于多少byte会采用紧凑存储格式

set-max-intset-entries 512 #set数据类型内部数据如果全部是数值型,
且包含多少byte以下会采用紧凑格式存储

activerehashing yes #是否激活重置哈希

##主从复制##

slaveof 192.168.100.10 6379 #设置从节点ip:port, 在master节点设置

masterauth 1234 #当master设置密码认证, slave用此选项指定master认证密码

slave-serve-stale-data yes
#当slave与master之间的连接断开或slave正在与master进行数据同步时,
如果有slave请求, 当设置为yes时, slave仍然响应请求, 此时可能有问题,
如果设置no时, slave会返回\"SYNC with master in
progress\"错误信息。但INFO和SLAVEOF命令除外。

##其它配置##

include /path/xxx.conf

16 MongoDB

16.1 dnf安装

导入源: cat \> /etc/yum.repos.d/mongodb-org-4.2.repo \< EOF

\[mongodb-org-4.2\]

name=MongoDB Repository

baseurl=https://repo.mongodb.org/yum/redhat/\$releasever/mongodb-org/4.2/x86_64/

gpgcheck=1

enabled=1

gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc

EOF

安装: dnf install -y mongodb-org

默认目录: /var/lib/mongo #数据目录; /var/log/mongodb #日志目录

16.2 源码包安装

安装依赖: dnf install libcurl openssl

下载并解压源码包: tar xf mongodb-linux-\*-4.2.12.tgz (解压即用)

链接执行文件: ln -s /usr/local/mongodb/bin/\* /usr/loca/bin/

创建mongod用户: useradd -r -s /usr/sbin/nologin mongod

创建数据目录: mkdir -p /var/mongod/{data,log} ; chown -R /var/mongo

配置systemd管理: cat \> /etc/systemd/system/mongod.service \<\<EOF

\[Unit\]

Description=mongodb

After=network.target remote-fs.target nss-lookup.target

\[Service\]

User=mongod

Type=forking

ExecStart=/mongodb/bin/mongod \--config /etc/mongod.conf

ExecReload=/bin/kill -s HUP \$MAINPID

ExecStop=/mongodb/bin/mongod \--config /etc/mongod.conf \--shutdown

PrivateTmp=true

\[Install\]

WantedBy=multi-user.target

EOF

16.3 podman部署

1\) server

podman run -d \[ -v /path/mongo:/etc/mongo -v /path/data:/data/db \] \\

-e MONGO_INITDB_ROOT_USERNAME=root \\

-e MONGO_INITDB_ROOT_PASSWORD=mongo4root \\

\--name mongodb mongo:latest \--config /etc/mongo/mongod.conf

16.4 mongodb配置文件

配置文件: /etc/mongod.conf

systemLog: #系统日志有关

destination: file #声明日志输出目的地: file或syslog

path: \"/var/log/mongodb/mongod.log\" #日志位置

logAppend: true #日志以追加模式记录, 实例重启不创建新日志

storage: #数据存储有关

journal: #回滚日志

enabled: true

dbPath: \"/var/mongod/data\" #数据路径的位置

directoryPerDB: false #数据目录存储模式,
推荐true不同库存在dbPath不同目录

wiredTiger: #wiredTiger引擎, 默认

engineConfig: #引擎配置

cacheSizeGB: 1 #数据缓存区大小, 默认1G或256MB

journalCompressor: snappy #WiredTiger日志数据的压缩类型

directoryForIndexes: false #索引集合storage.dbPath存储,
true存在单独子目录

collectionConfig:

blockCompressor: snappy #用于压缩收集数据的压缩类型

indexConfig:

prefixCompression: true #启用或禁用索引数据的前缀压缩

mmapv1: \# mmapv1存储引擎配置, 4.0以上版本被弃用

inMemory: #inmemory存储引擎配置

processManagement: #进程控制, podman部署不能配置

fork: true #后台守护进程

pidFilePath: /path/mongod.pid #pid文件的位置,
默认自动生成到data中(不推荐配置)

net: #网络配置有关

bindIp: 10.0.0.51,127.0.0.1 #监听地址

port: 27017 #端口号, 默认27017

maxIncomingConnections: 5000 #设置最大连接数

#http:

#enabled: true #开启http接口确保生产环境中的REST API和JSON API都被禁用

security: #安全认证

authorization: enabled #开启权限控制, 关闭删除该行

keyFile: /path/keyfile #秘钥文件的路径,
mongodb分片集群身份验证的共享秘钥

clusterAuthMode: keyFile #集群认证方式, 用秘钥文件进行身份认证, 推荐x509

javascriptEnabled: \<boolean\> #启用或者禁用服务端JavaScript 执行

sasl:

hostName: \<string\>

serviceName: \<string\>

saslauthdSocketPath: \<string\>

enableEncryption: false #启用或禁用WiredTiger存储引擎的加密

encryptionCipherMode: \<string\> #加密的模式适用于企业版

encryptionKeyFile: \<string\> #密钥管理

kmip:

keyIdentifier: \<string\>

rotateMasterKey: \<boolean\>

serverName: \<string\>

port: \<string\>

clientCertificateFile: \<string\>

clientCertificatePassword: \<string\>

serverCAFile: \<string\>

replication: #副本集用到的配置

oplogSizeMB: 1024 #复制操作日志的大小

replSetName: eqxtest3 #副本集名称,
同一个副本集的所有主机必须设置相同的名称

secondaryIndexPrefetch: \<string\> #(mmapv1存储引擎),
设置二进制文件和相关操作索引加载到内存, none不会加载, all二次加载,
\_id_only除了已存在_id不会加载其他索引

enableMajorityReadConcern: false #是否周期性的对当前的数据集进行snapshot

sharding: #分片用到的配置

clusterRole: shardsvr #集群角色, configsvr配置服务启动,
shardsvr分片实例启动

archiveMovedChunks: false #块迁移期间, 一个分片不保存文档从分片迁移

autoSplit: true #启用或者禁用自动分片,

configDB: \<string\>
#mongos分片集群实例必须制定相同的配置服务副本集名字, configDB:
\<configReplSetName\>/cfg1.example.net:27017, cfg2.example.net:27017

chunkSize: 64 #chunk大小(M)

16.5 mongodb优化

1\) 关闭大页内存

在vi /etc/rc.local最后添加如下代码

if test -f /sys/kernel/mm/transparent_hugepage/enabled; then

echo never \> /sys/kernel/mm/transparent_hugepage/enabled

fi

if test -f /sys/kernel/mm/transparent_hugepage/defrag; then

echo never \> /sys/kernel/mm/transparent_hugepage/defrag

fi

2\) 设置ulimiti

17 Elasticsearch

17.1 Debian安装elasticsearch

1\) apt安装

配置jdk, 并配置JAVA_HOME环境变量, 安装依赖apt-transport-https

导入密钥: wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch
\| apt-key add -

导入存储库: echo \"deb https://artifacts.elastic.co/packages/7.x/apt
stable main\" \> /etc/apt/sources.list.d/elastic-7.x.list

安装: apt-get -y update && apt-get -y install elasticsearch

启动服务, 验证curl -X GET \"localhost:9200/\"

2\) deb包安装

配置jdk, 并配置JAVA_HOME环境变量, 下载安装包。

dpkg -i elasticsearch-xxx.deb

启动服务, 验证curl -X GET \"localhost:9200/\"

17.2 二进制包免安装

下载包: wget
https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.13.0-linux-x86_64.tar.gz

解压, 移动至安装目录

修改limits, 修改内核参数: echo \" vm.max_map_count=655360\" \>\>
/etc/sysctl.conf

修改jvm.options中的Xms参数

修改elasticsearch.yml中的path.data和path.logs

配置es启动项, /path/bin/elasticsearch启动es

17.3 docker部署

docker run -d -p 9200:9200 -p 9300:9300 \\

-v /es/data:/usr/share/elasticsearch/data -v
/es/logs:/usr/share/elasticsearch/logs \\

-v -v
/es/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
\\

-e \"discovery.type=single-node\" -e ES_JAVA_OPTS=\"-Xms512m -Xmx512m\"
\\

\--name es elasticsearch:latest

注意: a) es的挂载目录uid:gid必须是1000

18 Git

18.1 git安装

1\) 安装依赖: yum -y install gcc\* zlib-devel perl-ExtUtils-MakeMaker

2\) 下载安装包: wget
https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.9.5.tar.gz

3\) ./configure \--prefix=/usr/local/git ; make ; make install

4\) 创建软链接: ln -s /usr/local/git/bin/\* /usr/local/bin/

18.2 gitlab安装

1\) 源码包安装

安装依赖: yum -y install policycoreutils-python openssh-server

下载安装包: wget
<https://mirror.tuna.tsinghua.edu.cn/gitlab-ee/yum/el7/gitlab-xxx.x8.rpm>

安装: yum -y install gitlab-ee-xxx.rpm

修改访问地址: /etc/gitlab/gitlab.rb

external_url \'http://192.168.100.192:9090\' 或为域名地址

加载配置: gitlab-ctl reconfigure

启动: gitlab-ctl restart

管理: gitlab-ctl start\|stop\|status\|restart

首次登陆需要重置密码。注意: 建议4GB内存

2\) 使用docker部署:

docker run \--detach \--hostname gitlab.example.com \\

\--publish 443:443 \--publish 80:80 \--publish 22:22 \\

\--name gitlab \--restart always \\

\--volume \$GITLAB_HOME/config:/etc/gitlab \\

\--volume \$GITLAB_HOME/logs:/var/log/gitlab \\

\--volume \$GITLAB_HOME/data:/var/opt/gitlab \\

gitlab/gitlab-ce:latest

19 Jenkins

19.1 rpm包安装

1\) rpm包:
https://pkg.jenkins.io/redhat-stable/jenkins-2.222.4-1.1.noarch.rpm

配置java环境, 安装java

2\) 安装: rpm -ih\|\[ivh\] jenkins-2.222.4-1.1.noarch.rpm

3\) 创建jenkins用户: usradd -s /usr/sbin/nologin jenkins
(注意jenkins用户id为1000)

4\) 修改用户名和端口: /etc/sysconfig/jenkins

JENKINS_USER = \"jenkins\" ；JENKINS_PORT = \"8888\"

5\) 配置jdk路径: /etc/init.d/jenkins文件, 在candidates后追加jdk安装路径

/usr/local/java11/jdk-xxx/bin/java

6\) 启动服务: systemctl start jenkis ；开启jenkins防火强服务或端口

7\) 浏览器访问, 如果长时间无响应:

改/var/lib/jenkins/hudson.model.UpdateCenter.xml , 将
"https://updates.jenkins.io/update-center.json\" 修改为
\"http://mirror.xmission.com/jenkins/updates/update-center.json\", 重启

8\) 初始密码位置: /var/lib/jenkins/secrets/initialAdminPassword

9\) jenkins相关目录释义:

/usr/lib/jenkins/: jenkins安装目录, war包会放在这里。

/etc/sysconfig/jenkins: jenkins配置文件, "端口", "JENKINS_HOME" 等

/var/lib/jenkins/: 默认的JENKINS_HOME, 数据文件存放路径。

/var/log/jenkins/jenkins.log: jenkins日志文件

登陆后更换插件下载源: 插件管理-高级-升级站点:

https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

推荐安装的jenkins插件:

19.2 yum安装

限制jenkins内存:

debian: vim /etc/default/jenkins或/etc/sysconfig/Jenkins;
在JAVA_ARGS=中增加\"-Xms1024m -Xmx2048m -XX:PermSize=512M
-XX:MaxPermSize=1024M \" , 原来的参数不要删

centos: 同样的文件, 在JENKINS_JAVA_OPTIONS=中添加

19.3 docker部署

docker run -d \--restart=always \--name jenkins-blueocean \\

-p 8080:8080 -p 50000:50000 \\

-v /usr/local/jenkins:/var/jenkins_home \\ 注意container内外权限问题

\--env JAVA_OPTS=\"-Xms1024m -Xmx1024m -XX:PermSize=512M
-XX:PermSize=1024M\" \\(限制内存)

jenkinsci/blueocean

19.4 Jenkins重要插件

重要: Pipeline, SSH Agent, Role-based Authorization Strategy

其它: Phabricator Differential Plugin, Localization: Chinese
(Simplified),

20 openssl

20.1 安装openssl

安装依赖: dnf -y install perl perl-devel gcc gcc-c++

下载: https://www.openssl.org/source/old/1.1.1/

安装: ./config shared \--openssldir=/usr/local/openssl
\--prefix=/usr/local/openssl

make && make install

移除旧版本, 链接新版本:

mv /usr/bin/openssl /usr/bin/oldopenssl

ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl

链接正确的库到指定位置:

echo \"/usr/local/openssl/lib/\" \>\> /etc/ld.so.conf && ldconfig

如果报找不见下面两个库:

ln -s /usr/local/openssl/lib/libssl.so.1.1 /usr/lib64/

ln -s /usr/local/openssl/lib/libcrypto.so.1.1 /usr/lib64/

20.2 安装cfssl

软件下载地址: https://pkg.cfssl.org/, 下载cfssl_linux-amd64,
cfssljson_linux-amd64, cfssl-certinfo_linux-amd64到/usr/local/cfssl下

将所有文件后缀_linux-amd64去掉

chmod +x cfssl cfssljson cfssl-certinfo && ln -s /usr/local/cfssl/\*
/usr/local/bin/

20.3 安装certbot

环境: python3.6+

debian: apt-get install certbot python-certbot-nginx

centos: install certbot python3-certbot-nginx

config nginx: certbot \--nginx

crontab: 0 0 \* \* \* /usr/bin/certbot renew \--quiet

21 Ansible

21.1 pip安装

curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

python get-pip.py

python -m pip install ansible

21.2 包管理器安装

centos: dnf install epel-release && dnf install ansible

ubuntu: apt install ansible

debian: deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty
main(注意版本)

apt-key adv \--keyserver keyserver.ubuntu.com \--recv-keys
93C4A3FD7BB9C367

apt update && apt install ansible

22 kubernetes

22.1 使用二进制包部署

1\) 部署环境准备

a\) 系统要求

centos7.7(mini，内核4.4)，docker-ce，kubernetes1.18

建议: 将docker换成Containerd(单独使用命令: crictl,
参数和docker差不多)，因为其可使k8s创建容器时调用链更短。

b\) 服务器规划:

  ------------------ ----------------- -------------------------------------
  k8s-master1,2      192.168.100.1,2   apiserver, controller-manager,
                                       scheduler, etcd

  k8s-node1,2        192.168.100.3,4   kubelet, kube-proxy, docker, etcd

  LB-master,backup   192.168.100.5,6   Nginx keepalived
                                       尽量不要和k8s共用节点
  ------------------ ----------------- -------------------------------------

c\) 操作系统初始化

关闭防火墙，时间同步

在master服务器hosts中添加各服务器解析地址, 如192.168.100.13 centos703

调整对于k8s的内核参数: 编辑/etc/sysctl.d/kubernetes.conf

net.bridge.bridge-nf-call-iptables=1 将桥接的IPv4流量传递到iptables的链

net.bridge.bridge-nf-call-ip6tables=1

net.ipv4.ip_forward=1

net.ipv4.tcp_tw_recycle=0

vm.swappiness=0 禁止使用 swap 空间，只有当系统 OOM 时才允许使用它

vm.overcommit_memory=1 不检查物理内存是否够用

vm.panic_on_oom=0 开启OOM

fs.inotify.max_user_instances=8192 初始化 ifd 的数量限制

fs.inotify.max_user_watches=1048576 注册监听目录的数量限制

fs.file-max=6553560 系统所有进程一共可以打开的文件数量

fs.nr_open=6553560 一个进程可以打开的最大文件数

net.ipv6.conf.all.disable_ipv6=1 禁用ipv6

net.netfilter.nf_conntrack_max=2310720 最大跟踪连接数

刷新内核参数: sysctl -p /etc/sysctl.d/kubernetes.conf

永久关闭swap: sed -ri \'s/.\*swap.\*/#&/\' /etc/fstab，临时关闭: swapoff
-a

c\) 安装docker, 配置阿里镜像加速, 设置开机启动项

2\) 部署Etcd集群

a\) 创建etcd证书

安装cfssl证书工具, 创建工作目录: mkdir -p /opt/TLS/{etcd,k8s};

cd /opt/TLS/etcd

自签证书颁发机构(CA): cfssl gencert -initca ca-csr.json \| cfssljson
-bare ca -

使用自签CA签发etcd私钥: cfssl gencert -ca=ca.pem -ca-key=ca-key.pem
-config=ca-config.json -profile=www server-csr.json \| cfssljson -bare
server

+-----------------------+---------------------+-----------------------+
| a)cat \>              | b)cat \>            | c)cat \>              |
| ca-config.json \<\<   | ca-csr.json \<\<    | server-csr.json \<\<  |
| EOF                   | EOF                 | EOF                   |
|                       |                     |                       |
| {                     | {                   | {                     |
|                       |                     |                       |
| \"signing\": {        | \"CN\": \"etcd      | \"CN\": \"etcd\",     |
|                       | CA\",               |                       |
| \"default\": {        |                     | \"hosts\": \[         |
|                       | \"key\": {          |                       |
| \"expiry\":           |                     | \"192.168.100.16\",   |
| \"43800h\"            | \"algo\": \"rsa\",  |                       |
|                       |                     | ......\],             |
| },                    | \"size\": 2048      |                       |
|                       |                     | \"key\": {            |
| \"profiles\": {       | },                  |                       |
|                       |                     | \"algo\": \"rsa\",    |
| \"www\": {            | \"names\": \[{      | \"size\": 2048        |
|                       | \"C\": \"CN\",      |                       |
| \"expiry\":           | \"ST\": \"BJ\",     | },                    |
| \"43800h\",           | \"L\": \"BJ\",      |                       |
|                       | \"O\": \"etcdCA\",  | \"names\": \[{\"C\":  |
| \"usages\":           | \"OU\": \"system\"  | \"CN\", \"ST\":       |
| \[\"signing\", \"key  |                     | \"BJ\", \"L\":        |
| encipherment\",       | }\]}                | \"Bj\", \"O\":        |
| \"server auth\",      |                     | \"etcd\", \"OU\":     |
| \"client auth\"\]     | EOF                 | \"system\"}\]}        |
|                       |                     |                       |
| }}}}                  |                     | EOF                   |
|                       |                     |                       |
| EOF                   |                     |                       |
+-----------------------+---------------------+-----------------------+

b\) 部署etcd的master节点

下载etcd源码包:https://github.com/etcd-io/etcd/releases/download/v3.4.9/etcd-v3.4.9-linux-amd64.tar.gz

创建工作目录并解压:

mkdir -p /opt/etcd/{bin,cfg,ssl,data} ; 将压缩包解压到etcd目录下
；将etcd和etcdctl文件移动到/opt/etcd/bin/

拷贝证书到/opt/etcd/ssl:cp /opt/TLS/etcd/ \*pem /opt/etcd/ssl/

创建etcd配置文件:/opt/etcd/cfg/etcd.conf

#\[Member\]

ETCD_NAME=\"etcd-1\" 节点名称，集群中唯一

ETCD_DATA_DIR=\"/opt/etcd/data/default.etcd\" 数据目录

ETCD_LISTEN_PEER_URLS=\"https://192.168.100.1:2380\" 监听地址,
换172.0.0.1试试

ETCD_LISTEN_CLIENT_URLS=\"https://192.168.100.1:2379\" 客户端访问地址

#\[Clustering\]

ETCD_INITIAL_ADVERTISE_PEER_URLS=\"https://192.168.100.1:2380\"
集群通告地址

ETCD_ADVERTISE_CLIENT_URLS=\"https://192.168.100.1:2379\" 客户端通告地址

ETCD_INITIAL_CLUSTER=\"etcd-1=https://192.168.100.1:2380,etcd-2=https://192.168.100.2:2380,etcd-3=https://192.168.100.3:2380\"
集群节点地址

ETCD_INITIAL_CLUSTER_TOKEN=\"etcd-cluster\" 集群Token

ETCD_INITIAL_CLUSTER_STATE=\"new\"
加入时集群的状态，new加入的是新集群，existing表示加入已有集群

配置systemd管理etcd:/etc/systemd/system/etcd.service

\[Unit\]

Description=Etcd Server

After=network.target

After=network-online.target

Wants=network-online.target

\[Service\]

Type=notify

EnvironmentFile=/opt/etcd/cfg/etcd.conf

ExecStart=/opt/etcd/bin/etcd \\

\--cert-file=/opt/etcd/ssl/server.pem \\

\--key-file=/opt/etcd/ssl/server-key.pem \\

\--peer-cert-file=/opt/etcd/ssl/server.pem \\

\--peer-key-file=/opt/etcd/ssl/server-key.pem \\

\--trusted-ca-file=/opt/etcd/ssl/ca.pem \\

\--peer-trusted-ca-file=/opt/etcd/ssl/ca.pem \\

\--logger=zap

Restart=on-failure

LimitNOFILE=65536

\[Install\]

WantedBy=multi-user.target

c\) 配置node节点etcd

将master节点的/opt/etcd和/etc/systemd/system/etcd.service拷贝到从节点

修改node节点上的/opt/etcd/cfg/etcd.conf文件中的:

ETCD_NAME=\"etcd-2\" 修改此处，节点2改为etcd-2，节点3改为etcd-3

ETCD_LISTEN_PEER_URLS=\"https://192.168.100.2:2380\" 当前服务器IP

ETCD_LISTEN_CLIENT_URLS=\"https://192.168.100.2:2379\" 当前服务器IP

ETCD_INITIAL_ADVERTISE_PEER_URLS=\"https://192.168.100.2:2380\" 当前IP

ETCD_ADVERTISE_CLIENT_URLS=\"https://192.168.100.2:2379\" 当前服务器IP

d\) 启动所有节点的etcd:

systemctl daemon-reload ; systemctl start etcd ; systemctl enable etcd

查看节点状态:

ETCDCTL_API=3 /opt/etcd/bin/etcdctl \--cacert=/opt/etcd/ssl/ca.pem \\

\--cert=/opt/etcd/ssl/server.pem \--key=/opt/etcd/ssl/server-key.pem \\

\--endpoints=\"https://192.168.100.1:2379,https:\...\" \\

endpoint health

3\) 部署k8s的master node

a\) 生成kube-apiserver证书

cd /opt/TSL/k8s

生成证书: cfssl gencert -initca ca-csr.json \| cfssljson -bare ca -

使用CA签发kube-apiserver私钥(HTTPS)证书: cfssl gencert -ca=ca.pem
-ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes
server-csr.json \| cfssljson -bare server

+-----------------+---------------------+-----------------------------+
| a\) cat \>      | b\) cat \>          | c\) cat \> server-csr.json  |
| ca-config.json  | ca-csr.json \<\<    | \<\< EOF                    |
| \<\< EOF        | EOF                 |                             |
|                 |                     | {                           |
| {               | {                   |                             |
|                 |                     | \"CN\": \"kubernetes\",     |
| \"signing\": {  | \"CN\":             |                             |
|                 | \"kubernetes\",     | \"hosts\": \[               |
| \"default\": {  |                     |                             |
|                 | \"key\": {          | \"10.0.0.1\",               |
| \"expiry\":     |                     | \"127.0.0.1\",              |
| \"43800h\"      | \"algo\":           |                             |
|                 | \"rsa\",\"size\":   | \"192.168.100.16\",         |
| },              | 2048                |                             |
|                 |                     | ...... 不要忘记vip          |
| \"profiles\": { | },                  |                             |
|                 |                     | \"kubernetes\",             |
| \"kubernetes\": | \"names\":          | \"kubernetes.default\",     |
| {               | \[{\"C\": \"CN\",   |                             |
|                 | \"ST\": \"BJ\",     | \"kubernetes.default.svc\", |
| \"expiry\":     | \"L\": \"BJ\",      |                             |
| \"43800h\",     | \"O\": \"etcdCA\",  | \"kubern                    |
|                 | \"OU\": \"system\"  | etes.default.svc.cluster\", |
| \"usages\": \[  |                     |                             |
| \"signing\",    | }\]}                | \"kubernetes.               |
| \"key           |                     | default.svc.cluster.local\" |
| encipherment\", | EOF                 |                             |
| \"server        |                     | \],                         |
| auth\",         |                     |                             |
| \"client        |                     | \"key\": {                  |
| auth\"\]        |                     |                             |
|                 |                     | \"algo\": \"rsa\",\"size\": |
| }}}}            |                     | 2048                        |
|                 |                     |                             |
| EOF             |                     | },                          |
|                 |                     |                             |
|                 |                     | \"names\": \[               |
|                 |                     |                             |
|                 |                     | {\"C\": \"CN\", \"ST\":     |
|                 |                     | \"BJ\", \"L\": \"BJ\",      |
|                 |                     | \"O\": \"etcdCA\", \"OU\":  |
|                 |                     | \"system\"}\] }             |
|                 |                     |                             |
|                 |                     | EOF                         |
+-----------------+---------------------+-----------------------------+

b\) 部署kube-apiserver

下载https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1183
，下载server包即可

创建相关目录mkdir -p
/opt/kubernetes/{bin,cfg,ssl,logs}，将压缩包解压到/opt/kubernetes

cd kubernetes/server/bin

将kube-apiserver , kube-scheduler , kube-controller-manager软连接到
/opt/kubernetes/bin

将kubectl 软连接到/usr/local/bin/

拷贝证书到/opt/k8s/ssl:cp /opt/TLS/k8s/\*pem /opt/kubernetes/ssl/

创建kube-apiserver配置文件

cat \> /opt/kubernetes/cfg/kube-apiserver.conf \<\< EOF

KUBE_APISERVER_OPTS=\"\--logtostderr=false \\\\ 启用日志

\--v=2 \\\\ 日志等级

\--log-dir=/opt/kubernetes/logs \\\\ 日志目录

\--etcd-servers=https://192.168.100.16:2379,... ,... \\\\ etcd集群地址

\--bind-address=192.168.100.16 \\\\ 监听地址

\--secure-port=6443 \\\\ https安全端口

\--advertise-address=192.168.100.16 \\\\ 集群通告地址

\--allow-privileged=true \\\\ 启用授权

\--service-cluster-ip-range=10.0.0.0/24 \\\\ service虚拟ip地址段

\--enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,ResourceQuota,NodeRestriction
\\\\ 准入控制模块

\--authorization-mode=RBAC,Node \\\\ 认证授权，启用RBAC授权和节点自管理

\--enable-bootstrap-token-auth=true \\\\ 启用TLS bootstrap机制

\--token-auth-file=/opt/kubernetes/cfg/token.csv \\\\ bootstrap
token文件

\--service-node-port-range=30000-32767 \\\\ service默认分配端口范围

\--kubelet-client-certificate=/opt/kubernetes/ssl/server.pem \\\\

\--kubelet-client-key=/opt/kubernetes/ssl/server-key.pem \\\\
apiserver访问kubelet客户端证书

\--tls-cert-file=/opt/kubernetes/ssl/server.pem \\\\ apiserver https证书

\--tls-private-key-file=/opt/kubernetes/ssl/server-key.pem \\\\

\--client-ca-file=/opt/kubernetes/ssl/ca.pem \\\\

\--service-account-key-file=/opt/kubernetes/ssl/ca-key.pem \\\\

\--etcd-cafile=/opt/etcd/ssl/ca.pem \\\\ 连接Etcd集群证书

\--etcd-certfile=/opt/etcd/ssl/server.pem \\\\

\--etcd-keyfile=/opt/etcd/ssl/server-key.pem \\\\

\--audit-log-maxage=30 \\\\ 审计日志

\--audit-log-maxbackup=3 \\\\

\--audit-log-maxsize=100 \\\\

\--audit-log-path=/opt/kubernetes/logs/k8s-audit.log\"

EOF

启用 TLS Bootstrapping 机制，创建配置文件中的token文件:

生成token:head -c 16 /dev/urandom \| od -An -t x \| tr -d \' \'

cat \> /opt/kubernetes/cfg/token.csv \<\< EOF

c47ffb939f5ca36231d9e3121a252940,kubelet-bootstrap,10001,\"system:node-bootstrapper\"

token，用户名，UID，用户组

EOF

配置systemd启动管理:

cat \> /usr/lib/systemd/system/kube-apiserver.service \<\< EOF

\[Unit\]

Description=Kubernetes API Server

Documentation=https://github.com/kubernetes/kubernetes

\[Service\]

EnvironmentFile=/opt/kubernetes/cfg/kube-apiserver.conf

ExecStart=/opt/kubernetes/bin/kube-apiserver \\\$KUBE_APISERVER_OPTS

Restart=on-failure

\[Install\]

WantedBy=multi-user.target

EOF

启动kube-apiserver，并设置开机启动项。

授权kubelet-bootstrap用户允许请求证书:(cd /opt/kubernetes/ssl)

kubectl create clusterrolebinding kubelet-bootstrap
\--clusterrole=system:node-bootstrapper \--user=kubelet-bootstrap

c\) 部署kube-controller-manager

创建配置文件:

cat \> /opt/kubernetes/cfg/kube-controller-manager.conf \<\< EOF

KUBE_CONTROLLER_MANAGER_OPTS=\"\--logtostderr=false \\\\

\--v=2 \\\\

\--log-dir=/opt/kubernetes/logs \\\\

\--leader-elect=true \\\\ 当该组件启动多个时，自动选举（HA）

\--master=127.0.0.1:8080 \\\\ 通过本地非安全本地端口8080连接apiserver

\--bind-address=127.0.0.1 \\\\

\--allocate-node-cidrs=true \\\\

\--cluster-cidr=10.244.0.0/16 \\\\

\--service-cluster-ip-range=10.0.0.0/24 \\\\

\--cluster-signing-cert-file=/opt/kubernetes/ssl/ca.pem \\\\
自动为kubelet颁发证书的CA

\--cluster-signing-key-file=/opt/kubernetes/ssl/ca-key.pem \\\\

\--root-ca-file=/opt/kubernetes/ssl/ca.pem \\\\

\--service-account-private-key-file=/opt/kubernetes/ssl/ca-key.pem \\\\

\--experimental-cluster-signing-duration=43800h0m0s\"

EOF

配置systemd管理controller-manager

cat \> /usr/lib/systemd/system/kube-controller-manager.service \<\< EOF

\[Unit\]

Description=Kubernetes Controller Manager

Documentation=https://github.com/kubernetes/kubernetes

\[Service\]

EnvironmentFile=/opt/kubernetes/cfg/kube-controller-manager.conf

ExecStart=/opt/kubernetes/bin/kube-controller-manager
\\\$KUBE_CONTROLLER_MANAGER_OPTS

Restart=on-failure

\[Install\]

WantedBy=multi-user.target

EOF

启动kube-controller-manager，并设置开机启动项。

d\) 部署kube-scheduler

创建配置文件:

cat \> /opt/kubernetes/cfg/kube-scheduler.conf \<\< EOF

KUBE_SCHEDULER_OPTS=\"\--logtostderr=false \\

\--v=2 \\

\--log-dir=/opt/kubernetes/logs \\

\--leader-elect \\ 通过本地非安全本地端口8080连接apiserver。

\--master=127.0.0.1:8080 \\ 当该组件启动多个时，自动选举（HA）

\--bind-address=127.0.0.1\"

EOF

配置systemd管理:

cat \> /usr/lib/systemd/system/kube-scheduler.service \<\< EOF

\[Unit\]

Description=Kubernetes Scheduler

Documentation=https://github.com/kubernetes/kubernetes

\[Service\]

EnvironmentFile=/opt/kubernetes/cfg/kube-scheduler.conf

ExecStart=/opt/kubernetes/bin/kube-scheduler \\\$KUBE_SCHEDULER_OPTS

Restart=on-failure

\[Install\]

WantedBy=multi-user.target

EOF

启动kube-scheduler，设置开机启动项。

查看集群状态:kubectl get cs

4\) 部署k8s的worker node (先在master上，生产环境不建议)

a\) 创建工作目录拷贝文件

mkdir -p /opt/kubernetes/{bin,cfg,ssl,logs}

将master节点中/opt/kubernetes/server/bin中的kubelet ,
kube-proxy拷贝到worker node的/opt/kubernetes/bin目录

b\) 部署kubelet

创建配置文件:

cat \> /opt/kubernetes/cfg/kubelet.conf \<\< EOF

KUBELET_OPTS=\"\--logtostderr=false \\\\

\--v=2 \\\\

\--log-dir=/opt/kubernetes/logs \\\\

\--hostname-override=k8s-master1 \\\\ 名称，集群唯一

\--network-plugin=cni \\\\ 启用CNI网络

\--kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \\\\
空路径，用于连接apiserver

\--bootstrap-kubeconfig=/opt/kubernetes/cfg/bootstrap.kubeconfig \\\\
首次启动向apiserver申请证书

\--config=/opt/kubernetes/cfg/kubelet-config.yml \\\\ 配置参数文件

\--cert-dir=/opt/kubernetes/ssl \\\\ kubelet证书生成目录

\--pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0
\" 管理Pod网络容器的镜像，每一个pod必须有一个pause镜像

EOF

配置参数文件:

cat \> /opt/kubernetes/cfg/kubelet-config.yml \<\< EOF

kind: KubeletConfiguration

apiVersion: kubelet.config.k8s.io/v1beta1

address: 0.0.0.0

port: 10250

readOnlyPort: 10255

cgroupDriver: cgroupfs

clusterDNS:

\- 10.0.0.2

clusterDomain: cluster.local

failSwapOn: false

authentication:

anonymous:

enabled: false

webhook:

cacheTTL: 2m0s

enabled: true

x509:

clientCAFile: /opt/kubernetes/ssl/ca.pem

authorization:

mode: Webhook

webhook:

cacheAuthorizedTTL: 5m0s

cacheUnauthorizedTTL: 30s

evictionHard:

imagefs.available: 15%

memory.available: 100Mi

nodefs.available: 10%

nodefs.inodesFree: 5%

maxOpenFiles: 1000000

maxPods: 110

EOF

生成bootstrap.kubeconfig文件:(cd /opt/kubernetes/ssl)

KUBE_APISERVER=\"https://192.168.100.1:6443\" \# apiserver IP:PORT

TOKEN=\"d4ce6c026f378fca76bf8ab0fe38baf4\" \# 与token.csv里保持一致,
可加到.bashrc

\# 生成 kubelet bootstrap kubeconfig 配置文件

kubectl config set-cluster kubernetes \\

\--certificate-authority=/opt/kubernetes/ssl/ca.pem \\

\--embed-certs=true \\

\--server=\${KUBE_APISERVER} \\

\--kubeconfig=bootstrap.kubeconfig

kubectl config set-credentials \"kubelet-bootstrap\" \\

\--token=\${TOKEN} \\

\--kubeconfig=bootstrap.kubeconfig

kubectl config set-context default \\

\--cluster=kubernetes \\

\--user=\"kubelet-bootstrap\" \\

\--kubeconfig=bootstrap.kubeconfig

kubectl config use-context default \--kubeconfig=bootstrap.kubeconfig

拷贝配置文件:cp bootstrap.kubeconfig /opt/kubernetes/cfg
(或者就在该路径下生成)

配置systemd管理kubelet

cat \> /usr/lib/systemd/system/kubelet.service \<\< EOF

\[Unit\]

Description=Kubernetes Kubelet

After=docker.service

\[Service\]

EnvironmentFile=/opt/kubernetes/cfg/kubelet.conf

ExecStart=/opt/kubernetes/bin/kubelet \\\$KUBELET_OPTS

Restart=on-failure

LimitNOFILE=65536

\[Install\]

WantedBy=multi-user.target

EOF

启动kubelet，并设置开机启动项。

c\) 批准kubelet证书申请并加入集群

查看证书请求:kubectl get csr

批准申请:kubectl certificate approve node-csr-...(NAME列值)

查看节点:kubectl get node

d\) 部署kube-proxy

创建配置文件:

cat \> /opt/kubernetes/cfg/kube-proxy.conf \<\< EOF

KUBE_PROXY_OPTS=\"\--logtostderr=false \\\\

\--v=2 \\\\

\--log-dir=/opt/kubernetes/logs \\\\

\--config=/opt/kubernetes/cfg/kube-proxy-config.yml\"

EOF

配置文件参数:

cat \> /opt/kubernetes/cfg/kube-proxy-config.yml \<\< EOF

kind: KubeProxyConfiguration

apiVersion: kubeproxy.config.k8s.io/v1alpha1

bindAddress: 0.0.0.0

metricsBindAddress: 0.0.0.0:10249

clientConnection:

kubeconfig: /opt/kubernetes/cfg/kube-proxy.kubeconfig

hostnameOverride: k8s-master1

clusterCIDR: 10.0.0.0/24

EOF

生成kube-proxy.kubeconfig文件:

先生成kube-proxy证书，cd /opt/TLS/k8s，创建证书请求文件

cat \> kube-proxy-csr.json \<\< EOF

{

\"CN\": \"system:kube-proxy\",

\"hosts\": \[\],

\"key\": {

\"algo\": \"rsa\",

\"size\": 2048

},

\"names\": \[

{

\"C\": \"CN\",

\"L\": \"BeiJing\",

\"ST\": \"BeiJing\",

\"O\": \"k8s\",

\"OU\": \"System\"

}

\]

}

EOF

生成证书:cfssl gencert -ca=ca.pem -ca-key=ca-key.pem
-config=ca-config.json -profile=kubernetes kube-proxy-csr.json \|
cfssljson -bare kube-proxy

cp {kube-proxy-key.pem,kube-proxy.pem} /opt/kubernetes/ssl/

生成kube-proxy.kubeconfig文件:KUBE_APISERVER=\"https://192.168.100.16:6443\"

kubectl config set-cluster kubernetes \\

\--certificate-authority=/opt/kubernetes/ssl/ca.pem \\

\--embed-certs=true \\

\--server=\${KUBE_APISERVER} \\

\--kubeconfig=kube-proxy.kubeconfig

kubectl config set-credentials kube-proxy \\

\--client-certificate=/opt/kubernetes/ssl/kube-proxy.pem \\

\--client-key=/opt/kubernetes/ssl/kube-proxy-key.pem \\

\--embed-certs=true \\

\--kubeconfig=kube-proxy.kubeconfig

kubectl config set-context default \\

\--cluster=kubernetes \\

\--user=kube-proxy \\

\--kubeconfig=kube-proxy.kubeconfig

kubectl config use-context default \--kubeconfig=kube-proxy.kubeconfig

拷贝配置文件:cp kube-proxy.kubeconfig /opt/kubernetes/cfg
(或者就在该路径下生成)

配置systemd管理kube-proxy:

cat \> /usr/lib/systemd/system/kube-proxy.service \<\< EOF

\[Unit\]

Description=Kubernetes Proxy

After=network.target

\[Service\]

EnvironmentFile=/opt/kubernetes/cfg/kube-proxy.conf

ExecStart=/opt/kubernetes/bin/kube-proxy \\\$KUBE_PROXY_OPTS

Restart=on-failure

LimitNOFILE=65536

\[Install\]

WantedBy=multi-user.target

EOF

开启kube-proxy服务，并设置开机启动

e\) 部署CNI网络

下载地址:https://github.com/containernetworking/plugins/releases/download/

创建工作目录并解压:mkdir -p /opt/cni/bin ; tar -zxvf ...tgz -C
/opt/cni/bin

部署cni网络:

wget
https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

docker pull quay.io/coreos/flannel:v0.12.0-amd64
(不然会启动失败，由于网络原因)

或在https://github.com/coreos/flannel/releases/download/v0.12.0/下载docker文件，导入。

kubectl apply -f kube-flannel.yml

查看:kubectl get pods -n kube-system ; kubectl get node

f\) 授权apiserver访问kubelet

cat \> apiserver-to-kubelet-rbac.yaml \<\< EOF

apiVersion: rbac.authorization.k8s.io/v1

kind: ClusterRole

metadata:

annotations:

rbac.authorization.kubernetes.io/autoupdate: \"true\"

labels:

kubernetes.io/bootstrapping: rbac-defaults

name: system:kube-apiserver-to-kubelet

rules:

\- apiGroups:

\- \"\"

resources:

\- nodes/proxy

\- nodes/stats

\- nodes/log

\- nodes/spec

\- nodes/metrics

\- pods/log

verbs:

\- \"\*\"

\-\--

apiVersion: rbac.authorization.k8s.io/v1

kind: ClusterRoleBinding

metadata:

name: system:kube-apiserver

namespace: \"\"

roleRef:

apiGroup: rbac.authorization.k8s.io

kind: ClusterRole

name: system:kube-apiserver-to-kubelet

subjects:

\- apiGroup: rbac.authorization.k8s.io

kind: User

name: kubernetes

EOF

启动:kubectl apply -f apiserver-to-kubelet-rbac.yaml

g\) 新增Worker Node

创建工作目录拷贝文件:

mkdir -p /opt/kubernetes/{bin,cfg,ssl,logs}，将master节点中关于Worker
Node文件拷到对应的Worker Node服务器的相应目录

  ------------------------------------------------------ -----------------------------
  Master node                                            Worker Node

  /opt/kubernetes/server/bin/{kubelet,kube-proxy}        /opt/kubernetes/bin

  /usr/lib/systemd/system/{kubelet,kube-proxy}.service   /usr/lib/systemd/system/

  /opt/kubernetes/ssl/\*                                 /opt/kubernetes/ssl

  /opt/cni/bin                                           /opt/cni/bin

  /opt/kubernetes/cfg/\*                                 /opt/kubernetes/cfg
  ------------------------------------------------------ -----------------------------

删除kubelet证书和kubeconfig文件:

rm /opt/kubernetes/cfg/kubelet.kubeconfig /opt/kubernetes/ssl/kubelet\*

修改主机名:

/opt/kubernetes/cfg/kubelet.conf中的:\--hostname-override=k8s-node1

opt/kubernetes/cfg/kube-proxy-config.yml中的:hostnameOverride: k8s-node1

启动kubelet,kube-proxy，并设置开机启动项

在Master上批准新Node kubelet证书申请:

kubectl get csr ; kubectl certificate approve node-csr......

查看:kubectl get node

如果没有启动起来，可能是quay.io/coreos/flannel:v0.12.0-amd64镜像拉去失败，可以提前导入。

5\) 部署Dashboard和CoreDNS

a\) 部署Dashboard (网页版)

https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml

下载recommended.yaml。默认Dashboard只能集群内部访问，修改Service为NodePort类型，暴露到外部

在ports中添加:nodePort: 30001 ; 在ports下(与ports同级)添加:type:
NodePort

运行:kubectl apply -f recommended.yaml

查看:kubectl get pods,svc -n kubernetes-dashboard

访问地址:https://NodeIP:30001 (192.168.100.15)

创建service account并绑定默认cluster-admin管理员集群角色:

kubectl create serviceaccount dashboard-admin -n kube-system

kubectl create clusterrolebinding dashboard-admin
\--clusterrole=cluster-admin
\--serviceaccount=kube-system:dashboard-admin

kubectl describe secrets -n kube-system \$(kubectl -n kube-system get
secret \| awk \'/dashboard-admin/{print \$1}\')

使用输出的token登录Dashboard。

b\) 部署CoreDNS

CoreDNS用于集群内部Service名称解析。

kubectl apply -f coredns.yaml

kubectl get pods -n kube-system

DNS解析测试:kubectl run -it \--rm dns-test \--image=busybox:1.28.4 sh

22.2 使用kubeadm进行部署

最好的运行环境是4.4内核以上的linux

使用kubeadm进行部署

22.3 高可用架构(扩容多Master架构)

Kubernetes作为容器集群系统，通过健康检查+重启策略实现了Pod故障自我修复能力，通过调度算法实现将Pod分布式部署，并保持预期副本数，根据Node失效状态自动在其他Node拉起Pod，实现了应用层的高可用性。

针对Kubernetes集群，高可用性还应包含以下两个层面的考虑:Etcd数据库的高可用性和Kubernetes
Master组件的高可用性。

1\) 扩容master

拷贝master1文件到master2:

  ----------------------------------- -----------------------------------
  /opt/kubernetes                     /opt/cni/

  /opt/etcd/ssl/\*                    /usr/lib/systemd/system/kube\*
  ----------------------------------- -----------------------------------

创建kubectl软连接:ln -s /opt/kubernetes/server/bin/kubectl
/usr/local/bin/

创建kube-apiserver, kube-controller-manager, kubelet, kube-proxy,
kube-scheduler软链接到/opt/kubernetes/bin

删除kubelet证书和kubeconfig文件:

删除kubelet证书和kubeconfig文件:

rm /opt/kubernetes/cfg/kubelet.kubeconfig /opt/kubernetes/ssl/kubelet\*

修改apiserver、kubelet和kube-proxy配置文件为本地IP和主机名:

+-----------------------------------+-----------------------------------+
| vi                                | vi                                |
| /opt/k                            | /opt/kubernetes/cfg/kubelet.conf  |
| ubernetes/cfg/kube-apiserver.conf |                                   |
|                                   | \--hostname-override=k8s-master2  |
| \--bind-address=192.168.100.17 \\ |                                   |
|                                   |                                   |
| \-                                |                                   |
| -advertise-address=192.168.100.17 |                                   |
| \\                                |                                   |
+-----------------------------------+-----------------------------------+
| vi                                |                                   |
| /opt/kub                          |                                   |
| ernetes/cfg/kube-proxy-config.yml |                                   |
|                                   |                                   |
| hostnameOverride: k8s-master2     |                                   |
+-----------------------------------+-----------------------------------+

设置kube-apiserver, kube-controller-manager, kube-scheduler, kubelet,
kube-proxy启动及开机自启动

查看集群状态:kubectl get cs

批准kubelet证书申请:kubectl get csr ; kubectl certificate approve
node.....

2\) 部署负载均衡器nginx

在负载均衡服务器上安装nginx和keepalived。

yum install -y epel-release nginx keepalived

a\) 配置nginx配置文件(主备一样):

cat \> /etc/nginx/nginx.conf \<\< \"EOF\"

user nginx;

worker_processes auto;

error_log /var/log/nginx/error.log;

pid /run/nginx.pid;

include /usr/share/nginx/modules/\*.conf;

events {

worker_connections 1024;

}

stream { \# 四层负载均衡，为两台Master apiserver组件提供负载均衡

log_format main \'\$remote_addr \$upstream_addr - \[\$time_local\]
\$status \$upstream_bytes_sent\';

access_log /var/log/nginx/k8s-access.log main;

upstream k8s-apiserver {

server 192.168.100.11:6443; \# Master1 APISERVER IP:PORT

server 192.168.100.12:6443; \# Master2 APISERVER IP:PORT

}

server {

listen 6443;

proxy_pass k8s-apiserver;

}

}

http {

log_format main \'\$remote_addr - \$remote_user \[\$time_local\]
\"\$request\" \'

\'\$status \$body_bytes_sent \"\$http_referer\" \'

\'\"\$http_user_agent\" \"\$http_x\_forwarded_for\"\';

access_log /var/log/nginx/access.log main;

sendfile on;

tcp_nopush on;

tcp_nodelay on;

keepalive_timeout 65;

types_hash_max_size 2048;

include /etc/nginx/mime.types;

default_type application/octet-stream;

server {

listen 80 default_server;

server_name \_;

location / {

}

}

}

EOF

b\) keepalived配置文件（Nginx Master）

+-----------------------------------+-----------------------------------+
| Nginx Master                      | Nginx Backup                      |
+-----------------------------------+-----------------------------------+
| cat \>                            | cat \>                            |
| /etc/keepalived/keepalived.conf   | /etc/keepalived/keepalived.conf   |
| \<\< EOF                          | \<\< EOF                          |
|                                   |                                   |
| global_defs {                     | global_defs {                     |
|                                   |                                   |
| notification_email {              | notification_email {              |
|                                   |                                   |
| acassen@firewall.loc              | acassen@firewall.loc              |
|                                   |                                   |
| failover@firewall.loc             | failover@firewall.loc             |
|                                   |                                   |
| sysadmin@firewall.loc             | sysadmin@firewall.loc             |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| notification_email_from           | notification_email_from           |
| Alexandre.Cassen@firewall.loc     | Alexandre.Cassen@firewall.loc     |
|                                   |                                   |
| smtp_server 127.0.0.1             | smtp_server 127.0.0.1             |
|                                   |                                   |
| smtp_connect_timeout 30           | smtp_connect_timeout 30           |
|                                   |                                   |
| router_id NGINX_MASTER            | router_id NGINX_BACKUP            |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| vrrp_script check_nginx {         | vrrp_script check_nginx {         |
| 检查nginx脚本                     | 检查nginx脚本                     |
|                                   |                                   |
| script                            | script                            |
| \                                 | \                                 |
| "/etc/keepalived/check_nginx.sh\" | "/etc/keepalived/check_nginx.sh\" |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| vrrp_instance VI_1 {              | vrrp_instance VI_1 {              |
|                                   |                                   |
| state MASTER                      | state BACKUP                      |
|                                   |                                   |
| interface ens33 #修改为实际网卡名 | interface ens33 #修改为实际网卡名 |
|                                   |                                   |
| virtual_router_id 51 #VRRP路由 ID | virtual_router_id 51 #VRRP路由 ID |
|                                   |                                   |
| priority 100 \#                   | priority 90 \#                    |
| 优先级，备服务器为90              | 优先级，备服务器为90              |
|                                   |                                   |
| advert_int 1 \# VRRP              | advert_int 1 \# VRRP              |
| 心跳通告间隔1秒                   | 心跳通告间隔1秒                   |
|                                   |                                   |
| authentication {                  | authentication {                  |
|                                   |                                   |
| auth_type PASS                    | auth_type PASS                    |
|                                   |                                   |
| auth_pass 1111                    | auth_pass 1111                    |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| virtual_ipaddress { \# 虚拟IP     | virtual_ipaddress { \# 虚拟IP     |
|                                   |                                   |
| 192.168.100.9/24                  | 192.168.100.9/24                  |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| track_script {                    | track_script {                    |
|                                   |                                   |
| check_nginx                       | check_nginx                       |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| }                                 | }                                 |
|                                   |                                   |
| EOF                               | EOF                               |
+-----------------------------------+-----------------------------------+

nginx状态检测脚本(主备都要)

cat \> /etc/keepalived/check_nginx.sh \<\< \"EOF\"

#!/bin/bash

count=\$(ps -ef \| grep nginx \| egrep -cv \"grep\|\$\$\")

if \[ \"\$count\" -eq 0 \];then

exit 1

else

exit 0

fi

EOF

chmod +x /etc/keepalived/check_nginx.sh

启动nginx, keepalived，并设置开机启动

查看keepalived工作状态, ip a

在任意节点: curl -k https://192.168.100.9:6443/version ，多访问几次。

查看:/var/log/nginx/k8s-access.log

3\) 修改worker node连接LB VIP

在所有worker node节点上执行:

sed -i \'s#192.168.100.16:6443#192.168.100.9:6443#\'
/opt/kubernetes/cfg/\*

systemctl restart kubelet kube-proxy

Kubernetes 高可用集群就部署完成:kubectl get node
