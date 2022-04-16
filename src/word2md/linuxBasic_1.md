1.1.1 linux简介

Linux是一种自由和开放源码的类UNIX操作系统,

1.1.2 centos基本知识

1\) centos7启动流程

开机前硬件检查(BIOS初始化)\--加载磁盘主引导记录到内存(加载MBR到内存)\--grub2引导阶段\--引导内核阶段和initramfs\--systemd阶段开启服务(centos7通过systemctl修改默认运行级别)

2\) 系统终端号

本地终端: tty1-6 通过alt+F1-6切换, 图像终端: tty7,
用ctrl+alt+F7切换(三秒), 远程终端pts/0-255

3\) 查看centos版本: cat /etc/redhat-release ; 内核版本: uname -a

1.2 Linux基础

1.2.1 Linux目录结构

/home/: 用户家目录; /root/: root用户家目录

/etc/: 配置文件保存, rpm安装默认安装的配置文件,
用户文件、服务的启动脚本等。

/usr/: 系统资源目录

/usr/bin/: 系统命令, 普通用户和root用户可执行; /usr/sbin/: 系统命令,
root用户

/usr/lib/: 系统调用库函数位置, 程序函数库; /usr/lib64/:
64位库函数保存位置

/usr/local/: 手工软件安装位置; /usr/src/: 源码包文件位置,
如kernels是内核源码

/usr/share: 共享数据, 如man文档; /usr/include: 公共的头文件

/var/: 动态数据保存位置, 保存缓存、日志等软件运行产生的文件

/var/tmp/: 临时文件目录; /var/www/html/:
rpm包安装apache的网页主默认安装目录

/var/lib/: 运行中需要调用或改变的数据保存位置,
MySQL默认的数据库在/var/lib/mysql

/var/log/: 系统日志保存位置; /var/spool/: 队列数据目录,
排队等待程序使用的数据

/var/spool/mail/: 邮件保存位置; /var/spool/cron/:
用户级定时任务队列保存位置

/boot/: 开机启动文件级内核文件和启动引导程序(grub2)

/dev/: 设备文件, /dev/null一般用于将不用的输出重定向进来

/opt/: 第三方软件安装位置, 一般用/usr/local

/proc/: 内存挂载点, 虚拟文件系统, 包含系统信息等; /sys/: 服务数据目录。

/run/: 系统运行时产生数据, 如PID; /media, /mnt: 空目录,
用来挂载光盘、u盘等

1.2.2 常用命令

命令提示符\[root@localhost \~\]# : root用户, localhost系统主机名,
\~当前目录, #或\$超级用户或普通用户。

1\) 文件和目录普通操作

ls, cd, mkdir, echo, touch, mv, cat, more, less, tail, head, tree, pwd

cp: 复制文件, -f强制复制; -a保留源文件或目录的属性, 目录递归处理,
保留链接; -r递归处理; cp中的询问是.bashrc中的别名作祟,
可以使用/usr/bin/cp避免。

rm: 删除, -f强制删除(文件不存在也不报错), -r递归删除

stat: 查看文件详情信息

ln: 创建硬链接, ln -s创建软链接,
注意创建软连接一定要写绝对路径。-f强制删除已存在的文件,
-n如果LINK_NAME是指向目录的符号链接, 则将其视为普通文件,
-r创建相对于链接位置的符号链接。注意删除目录软链接时千万不要使用rm -rf
dir/, 而是rm dir。

readlink -f test.txt 查看软链接真实目录位置

file test判断文件类型

strings test.txt 打印文件中可打印的字符

gzexe \[-d\] test.txt 对test.txt文件进行简单加密,
注意删除备份文件test.txt\~

cmp test.sh txt.sh 输出第一个不同处的位置,
注意一般用来比较复制后修改文件的情况

diff \[-bBwu -r\] test.sh txt.sh 比较文本的差异,
并输出不同处。-b不检测空格的不同, -B不检测空白行, -w忽略全部空格符,
-i检查大小写不同, -r递归比较子目录中的文件件。

basename oldname \[SUFFIX\] 输出文件名, suffix不要\*\*尾缀; dirname name
父目录名

md5sum test.txt 获取文件MD5值(可用于验证下载或生成随机字符串)

2\) split分割和dd拷贝文件

split \[-b 10m \] \[-l 100\] file.txt \[prefix\] \[-d -a 3\]

  -------------------------- ---------------------- ---------------------
  -b 按指定切割大小, k, m    -l 按指定行数切割      prefix 指定文件前缀

  -d 指定数字尾缀, -a                               
  指定数子尾缀长度; 如:                             
  split -b 5m test.txt url\_                        
  -d -a 3                                           
  -------------------------- ---------------------- ---------------------

dd: 用指定大小的块拷贝一个文件, 并在拷贝的同时进行指定的转换。

dd if=/dev/zero(输入文件) of=/root/test.txt(输出文件)
count=10(拷贝块个数) bs=1024k(每个块大小) \[conv=(指定转换参数, lcase,
ucase(大小写转换), noerror(出错不停止))\]

3\) 系统命令管理

alias 别名='原名' , 永久生效需要将命令加入\~/.bashrc文件

which: 搜索系统命令, 有软连接时用 ls -lrt \$(which command)

whereis: 参看命令所在目录, 有别名信息

whatis: 查看命令功能和级别, 有帮助信息, makewhatis更新系统命令

locate: 搜索普通文件命令, 这能收索数据库中的文件,
可以用updatedb更新一下数据库再搜, 配置文件/etc/updated.conf

history: 历史命令, -c清空历史命令, ctrl+r搜索历史命令回车执行

历史命令调用: !n执行第n条命令(history查), !!重复执行上一条,
!string重复执行最后一条string开头的命令, !\$重复上一条命令最后一个参数

type command 命令类型

man, info: 查看命令帮助文档

command -v yarn 搜索yarn命令

exec command 命令代替shell程序, 命令退出, shell 退出; 比如 exec ls;
一般用于dockerfile的启动shell脚本。

time command 测试command运行时间

sleep 1s \| 1m \| 1h 睡眠1秒\|分钟\|小时

4\) 排序

sort \[options\] file 默认以首字母进行排序

  ------------------------------------- -----------------------------------
  -f忽略大小写                          -b忽略每行的空白

  -n以数值排序                          -r反向排序

  -u删除重复行                          -t指定分割符, 默认tab制表符

  -k n1指定排序的字段,                  
  不加option默认以每行第一个字符排序,   
  列数从1开始                           
  ------------------------------------- -----------------------------------

uniq \[-c\|d\|u\] test #去连续重复行, -c统计重复行, -d显示重复行,
-u显示没有重复的行

wc \[-lwc test.txt\]: 统计有多少行(-l)、单词(-w)、字符(-c)

5\) 压缩

zip name.zip test1 test2 .... ; unzip \[-d dir\] name.zip

gzip \[options\] test1 test2 ; options: -c将压缩数据输出到屏幕,
-d解压缩, -r压缩目录。不会打包。gzip -c test1 test2 \> name.gz
可以保留原文件

bzip2 \[options\] test1 test2; options: -d解压, -k压缩时保留源文件,
-v显示压缩详情。不能压缩目录。

tar \[options\] \[-f name.tar\] test1 test2; options: -c打包;
-v显示打包过程; -f指定包名; -x解包; -t测试, 不解包只看有哪些文件;
-P忽略'/'包错, 要在-f前面。只能打包不能压缩。

tar -cvf/xvf name.tar test1 test2, 一般顺序是先打包在压缩和先解压再解包

一般压缩打包操作: tar -jcvf dir/name.tar.bz2 dir/test1 dir/test2

tar -zcvf dir/name.tar.gz dir/test1 dir/test2 (-C dir test去掉路径dir)

解压解包: tar -jxvf name.tar.bz2 或 tar -zxvf name.tar.gz
(将x换成t只看不解压)

只解压部分文件: tar -zxcf(-jxcf) name.tar.gz(.bz2) -C dir(解压到dir目录)
test1(包中文件)

对于xx.tar.xz文件的解压: xz -d xxx.tar.xz ; tar -xf xxx.tar

6\) 其它命令

write/wall: 不同用户之间的通信write user 终端号(ctrl+D发送); wall
\"信息\"给所有人发。

mail: 不同用户发邮件, mail user (ctrl+D发送), 邮件保存位置:
/var/spool/mail/user; mail -s \"标题\" user \< test.sh 发文件

1.2.3 注意事项

1\) 重启服务器或关机之前, 停止程序, 使用sync命令同步数据, 通过shutdown
-r/h now重启/关机。shutdown -h 22:30

2\) 防火墙:
基本功能是数据过滤(IP、MAC地址、端口号、协议类型、数据包中的数据),
注意防火墙的配置。

3\) 安装extundelete可用于恢复误删的数据。

4\) 目录权限: r可以ls该目录下的文件以及子目录,
w可以添加、删除、修改目录下的文件, x使cd生效。

5\) 常用快捷键: tab补全, ctrl+A命令行开头, ctrl+E命令行结尾, ctrl+L清屏,
ctrl+U删除或剪切光标前命令, ctrl+K删除或剪切光标后命令,
ctrl+W删除光标前一个字段, ctrl+Y粘贴ctrl+U或K内容, ctrl+backspace删除\^H
\^H删不掉的。

6\) 写脚本时rm -f一定要谨慎使用

7\) 尽量不要使用telnet这种明文传输方式, 不安全。

1.3 vim

1.3.1 基本用法

1\) 移动: h/l/k/j左右上下, w\|e左, b右, \$行尾, \^行首, G文档尾,
gg文档首, :n第n行

2\) 搜索:命令行 :/string (nohl取消选区, n查下一个, N查上一个)

3\) 插入: i, a, A, o, ESC退出编辑

4\) 退出: q退出, q!退出不保存, w保存, x保存退出, wq强制保存退出

5\) 选择: v, V行选, ctrl+V列选

6\) 删除: x删光标字符, X删前面字符, d, dd, dw, d0, d\$,
:n,.d从n到当前行删除, :g/\^\\s\*\$/d删除全文空白行, :n1,n2g/xxx/d
删除n1, n2匹配到xxx的行。

7\) 撤销: u ; 撤回撤销: ctrl + r

8\) 复制: y, yy, yw前面都可以加数, y\$, y0

9\) 粘贴: p粘贴在光标后

10\) 替换: r, R插入式替换; 命令行: s/old/new/替换光标后,
s/old/new/g替换光标行, n,ms/old/new/g替换n到m行,
%s/old\\C/new/g全文(\\C大小写敏感), 行前删和加#: 4,7s/\^#//g,
4,7s/\^/#/g, 删除行前空格: %s/\^\\s\*//g, 删除行尾空格: %s/\\s\*\$//g

11\) 折叠: zf 根据定义方式折叠(zf5G 重当前行到第5行创建折叠),
zo\|zO打开折叠, zc\|zC关闭折叠, zR\|zM打开\|关闭所有折叠,
zd\|zD删除光标折叠, zE删除所折叠

12\) 录制宏: qa 开始录制, q结束录制,
n@a调用刚刚的宏n次。注意a可以是a-z的任意字母, 宏的作用是避免重复工作。

1.3.2 vim设置

1\) 自定义功能, 修改/etc/vimrc或\~/.vimrc文件, 参考:

https://blog.csdn.net/Liukairui/article/details/107392243

2\) tab为4字符:

  ------------------------------------ -----------------------------------
  set tabstop=4(用4个空代替tab)        set softtabstop=4(i模式退格长度)

  set shiftwidth=4(每一级缩进长度)     set expandtab
                                       (插入模式空格代替tab键)

  set autoindent(自动缩进)             set cindent (c语言风格缩进)

  set                                  
  smartindent每一行都有相同的缩进量,   
  直到遇到右大括号(do})取消缩进形式    
  ------------------------------------ -----------------------------------

3\) 颜色更亮set background=dark 对于xshell ;

4\) nmap \<F5\> \<C-w\>h 自定义快捷键, 代替默认设置

设置快捷键生效范围: nmap在Normal模式下生效; vmap在Visual模式下生效;
imap只有在Insert模式下生效; cmap在命令行模式下生效

为避免快捷键冲突一般会加上\<leader\>前缀, 如: nmap \<leader\>hh \<C-w\>
用\\+hh替换ctrl+w; 默认\<leader\>为\"\\\", let mapleader =
\"\\\<space\>\"表示默认\<leader\>为空格

5\) vim8 不能右键粘贴问题: vim /usr/share/vim/vim80/defaults.vim,
注释掉: if has(\'mouse\') 代码块, 或者在本地设置set mouse=\"\"

6\) vim粘贴退格问题: set paste ; 粘贴完后: set nopaste

7\) autocmd \[event\] \[\*.type,...\] command
设置根据不同event不同type自动执行command

1.3.3 vim其它用法

1\) \>\>行右缩进, \<\<行左缩进

2\) :! command 执行shell命令

3\) vim test +n 打开第n行

4\) :r dir/test 将test内容导入光标位置; :r !command
将命令结果导入光标位置

5\) :ab string1 string2 输入string1时, 自动替换为string2

6\) 多文件vim test1 test2 ; 切换 :open test2; vim内打开 :e /path/test3

比较两个文件的不同: vimdiff test1 test2

6\) 分屏: vim -o\|-O test1 test2上下\|左右分屏; vim内: split \| vsplit;
切换: ctrl+w+h/l/j/k

7\) vim打开bash终端 :term bash; 隐藏 ctrl+w :hide; 恢复 :buffer 2
(:ls查看);

退出ctrl+w+c; 切换 ctrl+w+w

1.4 软件包管理

1.4.1 Liunx软件包基础

1\) 源码包: 服务器有大量使用时建议安装, 执行效率更高5%。RPM(二进制)包:
快速安装。按生产环境需要选择安装方式。

2\) 软件包依赖: 树形依赖, 环形依赖(同时安装), 模块依赖

1.4.2 rpm安装(手工)

rpm -ivh 包全名 ( -i安装, -v显示详情, -h输出进度, \--prefix
/指定绝对路径, \--force强制重装, \--test测试一下依赖性)

rpm -Uvh 包全名 (升级包)

rpm -e 包名 卸载(按包依赖顺序卸载, 卸载时一定要确认依赖包的用处)

rpm -q 包名 查询是否安装, rpm -qa查询所有包配合grep使用, rpm -qip 包全名
查询未安装包, rpm -ql 包名 查询安装文件列表, rpm -qlp 包全名
查询未安装包的安装位置, rpm -qf 系统文件名 查询文件属于那个包,

rpm -V 包名 验证是否安装, rpm -Vf 系统文件 验证是否修改系统文件

数字证书: rpm -import 包全名 导入安全证书, -qa \| grep gpg
查询是否有安全证书 gpg-pubkey

注意修改配置文件, 注意保留源文件

rpm包默认安装位置, /etc/配置文件, /usr/bin/可执行命令,
/usr/lib/函数库(部分软件的安装位置), /usr/share/doc/手册,
/usr/share/man/帮助文档

1.4.3 yum安装(在线)

yum源配置文件/etc/yum.repos.d/...repo文件, 有时候会用光盘yum源, 注意备份

yum list 列出源中所有可安装软件包, yum list pkgName 查询指定包

yum search pkgName 查找相关的软件包

yum whatprovides command 通过命令搜索安装包

yum info packageName 查看软件详情(要用包全名)

yum -y install pkgName\|xxx.rpm\|url.rpm 安装软件

yum -y update pkgName 更新软件包

yum upgrade 升级整个系统

yum remove pkgName 卸载, 注意包的依赖性, 小心系统崩溃, 所以不要加-y

yum组管理: yum grouplist : 查询安装列表, yum groupinfo \"软件组名\"
查询包含软件, yum -y groupinstall \"软件组名\" 安装软件组,
包含所有需要的软件, yum groupremove

yum clean all ; yum makecache 更新yum源缓存

注意, yum和rpm安装的都是二进制包, centos8用dnf替换

dnf源管理：

添加源：dnf config-manager \--add-repo repository_url

启用源：dnf config-manager \--set-enable repository

禁用源：dnf config-manager \--set-disable repository

1.4.4 apt-get 和apt

1\) apt-get命令

apt-cache search name 查询软件包

apt-cache policy name 查询软件是否可以安装

apt-get -y\|q\|\--no-upgrade\|\--reinstall install name\[=2.0.1\] 安装包
yes\|静默\|不安装已安装包\|重新安装\[安装版本\]

apt-get update 更新/etc/apt/sources.list索引

apt-get upgrade \[nginx=1.14.2-1\~stretch\] 更新所有软件包

apt-get dist-upgrade 升级整个系统

apt-get \[\--purge\] remove name 卸载包 \[同时卸载配置文件\]

apt-get autoclean && apt-get clean 安装失败后清理安装文件

2\) dpkg安装deb格式软件包

dpkg -i name.deb安装

dpkg -l 列出已安装包, 软件包状态: ii已经安装; iu已经安装该软件,
但未正确配置; rc已经被删除, 但配置文件未清理。

dpkg -r\|P name 卸载软件包 不动配置文件\|清理配置文件

dpkg -c\|s\|L name 查看包\|内容详细信息\|安装位置

3\) 降低Debian版本

apt-get install apt-show-versions

更换低配置源source.list

apt-show-versions \| grep \'newer than version in archive\'
查看新版本软件

apt-show-versions \| awk \'/newer than version in archive/{print \$1}\'
查看需要降配软件

执行以下命令降配:

apt-get install \$(apt-cache policy \`apt-show-versions \| awk
\'/newer/{print \$1}\'\` \| awk \'/\^\[\^ \]/{split(\$1,a,\":\");printf
a\[1\]\"=\"};/\\/var\\/lib\\/dpkg\\/status/{j=1;next};j{printf \$1\"
\";j=0}END{printf \"\\n\"}\')

注意: 如果途中遇到某个包不能下载, 添加\--fix-missing参数忽略, 事后安装

1.4.5 源码包安装

1)安装过程

./configure 编译准备, 检查安装环境, 将检测结果写入Makefile文件,
定义功能, 通过./configure -help查询有哪些功能, 一般会用./configure
\--prefix=安装路径 来指定绝对安装位置, \--with-name=/dir
指定依赖软件name的位置, \--enable-设置需要开启的模块。

源码包报错: 报错安装过程必须停止,
出现no、warning、error出现这几个关键字都是可能报错。注意报错后一定要执行make
distclean, 清除./configure或make中的产生的Makefile或.o文件。

make 编译, 出错make clean清除编译产生的可执行文件及目标文件

make install 编译安装。如果报错, 不但要make clean, 还要删除安装目录,
一般在make install 前先make test \| make check 看看有没有错误,
免得从来。

卸载: 直接删除安装路径文件夹 或 make uninstall

1.4.6 打入补丁和脚本安装

1\) 打入补丁

补丁生成: diff \[options\] old new \> pat.txt , options:
-a任何文档当文本处理, -N, -r, -u, 注意补丁文件路径都是绝对路径

打入补丁: patch -pn \< pat.txt 注意n为数字, -pn指定更新文件的位置

2\) 脚本安装

setup.sh 就是写一个安装脚本

1.5 用户和管理

1.5.1 用户信息

1\) 用户信息在/etc/passwd

用户名:密码标识:uid(用户id):gid(组id):用户说明:家目录:登录shell

将普通用户变为超级用户, 将uid设为0, 超级管理员是将其加入wheel组

2\) 用户密码/etc/shadow

用户名:加密码(!!或\*表示为伪用户):更新密码的时间:两次修改秘密间隔:密码有效期:密码到期前警告天数:密码过期后宽限天数:密码失效时间:保留

要想让用户临时不能登录, 在加密密码前加一个!

3\) 组信息文件/etc/group

组名:组密码位:gid:组中的其他用户

组密码文件/etc/gshadow

4\) 用户模板目录 /etc/skel

5\) /var/spool/mail/usrname 邮箱

1.5.2 用户管理命令

1\) centos创建用户

useradd \[options\] name ; -u指定uid, -g初始组名或组gid, -G附加组名,
-c说明, -d家目录, -r创建系统用户(如nginx用户等), -s 登录shell
(/usr/sbin/nologin)

passwd \[option\] name , 建立新用户必须设置密码, option : -l
暂时锁定用户, -u解锁用户, \--stdin将管道输出作为用户密码,
用于批量添加用户。echo \"123\" \| passwd \--stdin user

usermod \[options\] user 修改用户信息, -u修改uid, -d改家目录,
-g改初始组名, -G group将user加到group组中, -e修改失效日期, -L临时锁定,
-U解锁

userdel \[-r\] user 删除用户, -r删除家目录

groupadd/groupdel \[options\] name 创建/删除组, -g指定组gid

gpasswd -a/d user groupname 将user加入/删除groupname组

su \[-\] username 用户切换 ; su - username \[-s /bin/sh\] -c \"command\"
以user身份执行command, 不切换用户, 注意env可能不是user的。

id username 查看用户id信息

2\) debian创建用户

useradd -m \[-u uid -g gid -G gid -r -s /bin/bash -d /home/name\]
username 如果要在home下创建相应目录, 必须加-m, -r创建系统账号。

userdel -r username 删除用户, -r同时删除家目录

1.5.3 权限管理

1\) 文件属主和属组管理

chown: 修改文件所有者和属组, chown \[-R -L\] user.group txt
,需要root用户, -R递归, -L遍历到目录的每个符号链接。

chgrp: 只能修改文件所属组, chgrp \[-R\] group txt

2\) 基本权限管理

chmod: 权限管理, chmod u+x,g-wx,o-r txt.sh; chmod u=rwx,g=r,o=x; chmod
755 txt.sh (r:4, w:2, x:1)

chmod +t test 粘滞位, 用来替代'x',
表明在目录中文件只能被拥有者和root删除。

umask: 默认权限, 文件最大权限666, 文件夹最大777,
用默认减去umask值就是实际权限, /etc/profile中更改

3\) ACL权限: 解决用户对文件身份不足的问题, 但存在权限溢出问题

setfacl \[options\] 文件名, -m设定acl权限, -b删除acl权限; setfacl -m
u:用户名:权限(rwx) 文件名; setfacl -m g:组名:权限 文件名

setfacl -m u:user:rwx -R dir 赋予acl, 只对已经存在的生效,
对之后新建的不生效

setfacl -m d:u:user:rwx -R dir 赋予acl, 只对以后新建的生效,
对之前创建的不生效

setfacl -x u:user test 删除指定用户acl权限

setfacl -b -R test 删除test所有acl权限

getfacl filename 查看acl权限

4\) 锁定文件

chattr \[-R\] \[+\|-\|=options\] file1 file2 ...改变文件属性,
用于限制root权限

chattr +a /etc/passwd 只能追加内容, 不能删除文件

chattr +i /etc/passwd 文件不能删除, 更改, 移动

lsattr /etc/passwd 查看加锁

1.5.4 管理员权限

visudo 可以编辑给普通用户的超级权限, 设置越简单, 权限越大,
尽量设置详细权限。

格式: user 主机(主机名或ip) = (切换到什么身份执行) command

username ALL=(ALL) /usr/sbin/shutdown -r now

%group ALL = (ALL) ALL 表示group组中的成员, 可以用root身份执行任何命令。

sudo command 执行超级权限

sudo -l 查看被赋予的权限

1.5.5 文件特殊权限

1\) SetUID: chmod 4755 test (4为SUID, 755普通权限)

只有可执行的文件能设置SUID, 要有x权限才能执行,
执行程序时执行者会变成属主身份, 只在程序执行过程生效

2\) SetGID: chmod 2755 test (2为设置SGID)

SGID即针对文件, 也针对目录, 对文件时和SUID相似。针对目录时,
普通用户必须对此目录拥有r、x权限, 才能进入目录,
普通用户有效组会变成目录属组, 若普通用户有w权限,
新建文件的默认属组是目录属组。

3\) Sticky BIT: chmod 1777 test (1为SBIT权限)

SBIT权限只对目录有效, 作用: 普通用户对目录有w、x权限,
普通用户将只能删除自己建立的文件, 而不能删除其它用户的权限,
只有root能删所有文件。

1.6 文件系统管理

1.6.1 文件系统介绍

1\) 磁盘分区

磁盘分区有两种形式:

MBR分区表(主引导记录): 最多只支持2.1TB, 最大支持4个分区。分区类型:
主分区(最多4个)、扩展分区(最多只能有1个, 主分区加扩展分区最多4个,
扩展分区中只能包含逻辑分区)、逻辑分区。

GPT分区表(全局唯一标示分区表): 支持9.4ZB, 理论支持无数分区, parted
-l查看。

2\) 格式化(逻辑格式化)

根据用户选定的文件系统NTFS、EXT4(/boot)、XFS(主流)等,
在磁盘的特定区域写入特定数据,
在分区中划出一片用于存放文件分配表、目录表等用于文件管理磁盘空间。

3\) 设备文件名(MBR):

/dev/sda1 (sd:SCSI硬盘接口、SATA硬盘接口, a:第一块硬盘, 1:第一个分区,
逻辑分区从5开始), /dev/sdb5（第二块SATA接口的第一个逻辑分区）。

4\) 挂载点: 使用已经存在的空目录作为挂载点

5\) 挂载: 把设备文件(/dev/sdb5)和挂载点(已经建立的空目录)连接的过程

6\) 文件系统: ext4(centos6.x), xfs (centos7.x)

1.6.2 常用内存管理命令

df \[-ahTi\] ; 显示系统内存使用情况, -a显示特殊文件系统, -h单位,
-T显示文件系统类型, -i查看磁盘Inodes使用情况。

du \[-ahs\] 目录/文件 ; 显示目录或文件大小, -a显示每个文件占用率,
-h单位, -s只统计总占用。

free \[-h\|-b\|-k\|-m\|-g\] 内存使用, 后面是单位b, KB, MB, GB
(buff/cache 缓存占用)

iostat -x 1 2 磁盘io负载查看

fsck 文件系统修复命令, 开机会自动执行。

dumpe2fs \[-h\] /dev/sda1 显示磁盘状态, 只支持ext系列

lsblk 查看分区和磁盘

fdisk -l \[/dev/sda1\]查看分区信息

parted -l 查看分区信息

1.6.3 磁盘分区

1\) fdisk分区

fdisk /dev/sdb(未分区磁盘)

常用命令:

  ----------------------------------- -----------------------------------
  n 创建新分区                        d 删除已有分区

  t 修改分区类型                      l 查看所有支持的类型

  p 显示现有分区信息                  w 保存并退出

  q 不保存并退出                      m 查看帮助信息
  ----------------------------------- -----------------------------------

n\--p主\--1分区号\--1起始柱面\--分区大小+50GB\--w

n\--e扩展\--2分区号\--起始柱面\--分区大小, 剩余空间

n\--1逻辑\--起始柱面\--分区大小+50GB---w

2\) parted分区(大于2TB)

parted /dev/sbd(未分区磁盘)

mktable gpt(创建GPT分区表) \-- mkpart primary 0 100M(0 -1为所有) \--
toggle n(用p看number) lvm \-- p \-- pv

1.6.4 格式化及挂载

1\) 格式化

mkfs.ext4 /dev/sdb4(刚刚分区的)

mkfs.xfs /dev/sdb4

mkfs -t xfs\|ext4 /dev/sdb4

2\) 挂载: 将设备文件名和空目录连接起来

mount 查询已有挂载

mount /dev/sdb4 dirName(空目录)

unmount 设备名(目录名): 卸载设备

mount -a 扫描/etc/fstab文件

mount \[-t 文件系统\] \[-o 特殊选项\] 设备文件名 空目录 挂载u盘

linux驱动保存位置/usr/lib/modules/...\...64/kernel中

3\) 自动挂载

必须要自动挂载, 不然每次重启后手动挂载的都要消失,
修改分区自动挂载文件/etc/fstab

设备文件名或uuid 挂载点 文件系统 挂载选项(default) 1 2

第五列: 是否可以备份, 0不备份, 1每天备份, 2不定期备份, 推荐1

第六列: 是否检测磁盘fsck, 0不检测, 1启动时检测, 2启动后检测, 推荐2

查看设备文件uuid: ls -l /dev/disk/by-uuid/

设置完后重启

/etc/fstab/文件修复, 如果fstab写错, 不能开机, 需要到本机开机, mount -o
remount, rw / ; 然后再修改/etc/fstab文件

注意删除分区时, 要先改/etc/fstab文件中的自动挂载, 然后umount,
然后再删除设备。

3\) 增加swap分区

fdisk 设备文件名 \-- t \-- 选择分区 \-- 82 \-- w

格式化: mkswap 设备文件名, 加载: swapon 设备文件名, 修改/etc/fstab

设备文件名/uuid swap swap default 0 0

重启

4\) 挂载移动硬盘

先使用fdisk -l查看磁盘列表

挂载: mount /dev/sda5 /mnt/diskd

弹出: umount /dev/sda5 /mut/diskd

如果不能正常挂载可能需要安装NTFS格式:

下载: https://tuxera.com/opensource/ntfs-3g_ntfsprogs-2017.3.23.tgz ,
安装

挂载: mount -t ntfs-3g /dev/sdc1 /mnt/usb ; 卸载: umount /dev/sdc1
/mnt/usb

1.6.5 逻辑卷LVM

扩容方便, 在实际运用中使用方便, 不建议用普通分区。

1\) 物理卷(PV)就是真正的物理硬盘或分区。卷组(VG)将多个物理卷组合,
可以看成是一个逻辑硬盘。逻辑卷(LVM)将卷组分区。物理扩展(PE)用来保存数据的最小单元,
实际数据写在PE中, 默认位4MB。

2\) 建立LVM步骤

把物理硬盘分成区,
也可以就是整块硬盘。把物理分区建立成物理卷(PV)。把物理卷整合成卷组(VG),
卷组可以调整大小, 不建议删除。最后把卷组划分为逻辑卷(LV),
可看成分区。然后是格式化和挂载。

3\) 命令行创建LVM

先将设备分区: fdisk 设备文件名 \-- 先新建扩展分区 \-- n \-- 逻辑分区 \--
t修改id \-- 分区号5开始 \-- 8e逻辑分区代码 \-- w \-- 重启 ;
推荐使用parted

刷新分区: partprobe /dev/sda

建立物理卷PV: pvcreate 设备文件名

pvscan或pvdisplay查看物理卷; pvremove 物理卷名 删除物理卷

建立卷组VG: vgcreate \[-s n\] 卷组名 物理卷名(可以是多个), -s指定PE大小,
一般不写

vgscan或vgdisplay 查看卷组; vgextend 卷组名 物理卷名 卷组扩容 ; vgremove
卷组名 删除卷组

建立逻辑卷LVM: lvcreate \[-L 大小MB, GB, TB\] \[-n 逻辑卷名\] 卷组名

lvscan或lvdisply 查看

格式化: mkfs -t 文件系统 /dev/卷组名/逻辑卷名

挂载: mount /dev/卷组名/逻辑卷名 挂载点 , 自动挂载修改/etc/fstab

调整逻辑卷大小: lvresize \[-L 大小GB, TB\] 逻辑卷名 , 这里注意不是扩容,
数值一定要比建立时大, 调整大小也不需要卸载, 调整大小后执行更新resize2fs
逻辑卷名。

扩容lvm: lvextend -L +numG 逻辑卷名(/dev/centos/root)

使扩容生效: xfs_growfs 逻辑卷名(对于xfs文件系统); resize2fs
逻辑卷名(对于ext4)

1.6.6 磁盘配额

允许使用的磁盘大小, 内核必须支持磁盘配额 grep CONFIG_QUOTA
/boot/config-......, 系统必须安装quota工具 rpm -qa \| grep quota

用户配额, 限制磁盘容量和文件个数, 软限制和硬限制, 宽限时间

规划: 需要建立一个分区, 建立需要限制的用户和用户组, 分配软限制和硬限制,

1\) 建立分区, 挂载-建立用户-开启配额功能, mount -o
remount,usrquota\[,grpquota\] 目录(挂载点), 永久生效改/etc/fstab

设备文件名 挂载点 文件系统 defaults,usrquota\[,grpquota\] 0 0 , 重启

2\) 建立磁盘配额的配置文件: quotacheck \[-avug\] 分区名, option:
-a扫描/etc/mtab文件中启用磁盘配额的分区, -v显示扫描过程,
-u建立用户配额的配置文件aquota.user文件, -g组配额配置文件aquota.group

关闭SELinux, 进入到挂载点看到产生aquota.user文件,

3\) 设置用户和组配额: edquota \[-ugtp\] user或group, 修改soft和hard项

4\) 开启用户配额: quotaon \[-augv\] \[挂载点或分区名\],
-a启动/etc/mtab中所有配额分区, 不加后面跟挂载点, -u/g用户和组,
-v显示详情

quotaoff \[-aug\] \[挂载点或分区名\] 关闭配额, 选项同上

repquota \[-qugvs\] \[挂载点或分区名\] 查询配额, -s单位, 其它同上

测试: dd if=/dev/zero of=/目录/testfile bs=1M count=60

5\) 复制配额: edquota -p 源用户 -u 目标用户; 宽限时间: edquota -t

6\) 非交互式磁盘配额, 写shell脚本

setquota -u 用户名 容量软限制 容量硬限制 个数软限制 个数硬限制
分区名/挂载点

1.6.7 常见问题

1\) 缓存(cached)占用过高问题

cached主要负责缓存文件使用,
当文件过大会造成cached区内存增大把内存占用完。free命令中的buffer和cache:
它们都是占用内存:

buffer作为buffer cache的内存, 是块设备(磁盘)的缓冲区, 包括读、写磁盘

cache作为page cache的内存, 文件系统的cache, 包括读、写文件,
如果cache的值很大, 说明cache住的文件数很多。

手动清除缓存: 先执行sync (将缓存的内容写回到硬盘中)

echo 0\|1\|2\|3 \> /proc/sys/vm/drop_caches 其中: 0是不释放缓存,
1释放页缓存, 2释放dentries和inodes缓存, 3是释放1和2中的所有缓存。

或直接用临时修改: sync && sysctl vm.drop_caches=3
(只是一个清理缓存的动作)

注意: 释放完后缓存后不用将drop_caches设置为0, 只能系统设置。

2\) 清理swap

当清理完buffers/cache后, 发现swap任在使用, 使用:

swapoff -a && swapon -a (将数据写到内存中)

3\) 磁盘Inodes占满

创建文件时出现no space left on device ,但df -h 查看磁盘还有剩余空间,
可能是inodes占满了, 通过df -i查看。

进入可疑目: ls -lR\| grep \"\^-\" \| wc -l 递归统计目录下的所有文件数

ls -lR\|grep \"\^d\"\|wc -l 递归统计目录下的所有目录数

清理通过find过期文件

4\) 清理长时间不使用旧文件

tmpwatch(centos)\|tmpreaper(debian)
path/dirName默认以atime和h单位进行文件清理

tmpwatch 5 /tmp 清理tmp中5小时内没有访问的文件

tmpwatch -m 30d /tmp/downloads 清理30天内没有修改(mtime)的文件,
未被使用: -am

tmpwatch -am 10d \--exclude=/tmp/move /tmp
删除除/tmp/move外10天内未使用文件

tmpwatch -am 5d \--exclude-pattern='\*.pdf' /tmp 删除时pdf文件除外

注意: 千万不要在根目录下使用该名令, 会删除系统重要文件。

可以用tmpwatch设置定时任务去清除旧文件, 如果命令不存在直接安装yum -y
install tmpwatch

5\) 设置打开最大文件数

查看: ulimit -a

修改整个系统能打开的文件描述符: echo 1000000 \> /proc/sys/fs/file-max

修改内核参数: echo \"fs.nr_open=1000000\" \>\> /etc/sysctl.conf

echo \"fs.file-max=1100000\" \>\> /etc/sysctl.conf

编辑/etc/security/limits.conf, (注意下面的值要比nr.open小,
不然会导致启动问题)

\* soft nofile 200000 打开文件数

\* hard nofile 200000 (\*指所有用户, 可设置单个用户或组,
如:nignx或@mysql)

#\* soft nproc 65535 连接线程数, 看情况设定

#\* hard nproc 65535

在/etc/pam.d/login追加: session required pam_limits.so (使登陆生效)

重启或重新登陆

二 shell

2.1 shell基础

2.1.1 shell基础命令

1\) echo \[-en\] \"string\" , -e支持反斜杠的字符转义,
-n取消输出后的行末换行符

eval \"command\"; #扫描字符串为命令并执行

2\) 多命令执行: command1 ; command2

3\) 逻辑与或: command1 && command2 ; command1 \|\| command2

4\) \| :管道命令, command1 \| command2, 将1的输出作为2的操作对象

5\) 重定向: 0输入, 1输出, 2错误

\>, \>\> 输出, cat test1.txt 1\>test2.txt, &\>\>混合输出

\<, \<\< 输入

cat \> test.txt \<\< EOF command EOF 将内容写入到文件, 清除原有的, cat
\>\> 用于追加。

echo -e \"test\" \| tee \[-a\] test1.txt test2.txt
将echo内容写入test1-2.txt中, 并保持屏幕输出, -a 追加到文件末尾。

6\) 特殊符号: \'command\'单引号没有转义, \"command\"双引号有转义

\`command\`反引号同\$( command )将里面的命令执行结果当变量处理

(command)括号中的命令会在子shell中执行与\$()不一样

{ command }其中的命令会在当前shell中执行

\[\]用于变量测试, #注释, \$var调用变量, \\转义

7\) read输入

read \[option\] var #接收键盘输入并存入var中, option: -p \"提示信息\" ;
-t秒数 ; -n字符数 ; -s隐藏输入数据

8\) find: 搜索文件, 通配符

find dir options \[file\]

options:

-name/iname \"test\" 按文件名/不区分大小写,

-regex \"正则\" 以正则模式匹配。

-size \[+/-\]n\[k\|M\|G\] 比num大或小

-atime/mtime \[+/-\]num 访问时间/修改时间, 前/内/num那一天

-perm \[+/-\] num 只要包含一个大于num的权限/所有权限都要大于给出的权限

-inum n 用inode查找, 用于删除乱码文件, ls -i查看inode

-uid num/ -gid num/ -user name/ -group name/ -nouser 按所有者和组搜索

-type d/f/l 目录/文件/连接

逻辑搜索: -a与, -o或, -not非, find dir -size +15k -a -atime -5 ; find
dir -not -name test.sh

find dir \[option\] test -exec/ok command {} ...... \\; 注意:
\";\"属于命令行, 将搜索结果\'{}\'用command处理,
-ok会询问是否用command处理。find不支持管道符

find dir -mtime +30 -exec cp -r {} newdir \\; 复制30天前的数据到newdir。

写脚本删除文件操作时dir一定要使用绝对路径, 尽量询问。

9\) grep: 搜索文件中的字符串

grep \[option\] \"string\" filename

  ----------------------------------- -----------------------------------
  -i忽略大小写                        -n输出行号

  -v取反                              -c符合条件的行数

  -E以正则模式匹配                    \--color=auto显示颜色

  -w精确匹配整个string                -Fx完全匹配的行

  -l输出匹配字符的文件名              
  ----------------------------------- -----------------------------------

注意: 如果grep没有查到, 退出状态码为非0, 如果在循环中,
循环将不在继续执行, 在循环体中推荐使用sed。

10\) ack 代码收索工具

ack安装: curl https://beyondgrep.com/ack-v3.4.0 \> /usr/local/bin/ack &&
chmod 0755 /usr/local/bin/ack

简单文本搜索:

ack \[option\] test 递归搜索当前目录下所有包含test字段的文件

  ------------------------------------- ---------------------------------
  -i 忽略大小写                         -v 取反

  -w 精确匹配test整个词                 -Q 'test\*' 使用通配符

  -l 只显示文件名                       -f 搜索文件名为test的文件

  -g test\$                             \--sort-files 搜索文件后排序
  正则查找文件名以test结尾的文件        

  -grep \--python                       
  过滤查找内容有test的python文件, 其它: 
  \--html, \--js, \--conf               
  ------------------------------------- ---------------------------------

注意: 如果ack查不到退出状态码为非0, 注意在循环体中的使用。

2.1.2 变量和运算符

1\) 变量

var=\"value\" 注意变量和值之间不能有空格, 调用\"\$var\"或\${var}

变量分类: 自定义变量, 环境变量(用户自定义, 系统定义), 位置参数变量,
预定义变量

变量查看: env查看部分环境变量, set \[-u/x\] 查看部分变量,
-u调用未声明变量会报错, -x在执行命令前, 先将命令输出。

删除变量: unset var

2\) 环境变量

export var=\"value\"

查看删除: set, env, unset

PATH: 系统查找命令的环境变量, PATH=\"\$PATH\":dir 将dir加入PATH中

PS1命令提示符变量

3\) 位置参数变量

\$n : \$0表示命令本身, \$1-9表示第1到第9个参数, 10以上的用\${10}

\$\* : 所有参数, 所有参数是一个整体

\$@ : 所有参数, 每个参数是分开的

\$# : 参数个数

4\) 预定义变量

\$? : 最后一次执行命令的返回状态码, 0表示成功, 非0表示失败

\$\$ : 当前进程的进程号PID

\$! : 后台运行的最后一个进程的进程号PID

2.1.3 shell运算

1\) declare \[+/-\] \[option\] var 声明变量的类型

\"-\"给变量设定类型属性, \"+\"取消属性, -a声明为数组型, -i整数型,
-r只读变量, 不能修改、不能删, -x声明为环境变量, -p显示变量类型

2\) 数组变量

var\[n\]=\"value\", n从0开始

var=(\"value1\" \"value2\" \"valuen\")

调用\${var\[n\]}, 调用所有\${var\[\*\]}或\${var\[@\]}, 长度:
\${#var\[\*\]}

集合{a,b,c...}, touch file{1..9} 创建file1到file9的文件

3\) 数学运算

var=\$(expr \$var1 + \$var2) ; var=\$(let \$var1+\$var2)

var=\$(( \$var1+\$var2 )) ; var=\$\[ \$var1+\$var2 \]

\[0,n)的随机数：\$\[RANDOM % n\]

4\) shell运算符

5\) bc浮点运算器

支持的运算: 基本运算(+, -, \*, /, %), 幂运算(a\^b), 条件判断(&&, \|\|,
\<, \<=, \>, \>=, ==, !=)

交互模式: bc \[option\] : -l使用标准数学库, -i强制交互, -w显示警告,
-s使用POSIX标准处理, -q不显示欢迎信息。

> 内置变量: scale=2指定精度, 两位小数; ibase=16指定输入进制;
> obase=16指定输出进制,注意obase尽量放在ibase前面; last或 .
> 表示最近打印数字

内置函数(-l):

  ---------------------------------- ------------------------------------
  s(x) 计算sin(x)                    c(x) 计算cos(x)

  a(x) 计算arctan(x)                 e(x) 计算e\^x

  j(n,x) 贝塞尔函数, 从n到x的阶数    sqrt(x) 计算平方根

  length(x) 获取x数值的长度          
  ---------------------------------- ------------------------------------

bc支持变量: m=2; n=3; (m\*2)+(n/2)

在shell中使用bc: echo \"expression\" \| bc ; 例: echo \"x=3; scale=5;
n=x+2; e(n)\" \| bc -l

使用重定向: var=\$( bc \<\< EOF; expression; EOF )

2.1.4 环境变量配置文件

1\) source profile 或 . profile , 用于设置环境变量后, 使配置文件生效,
特别是每次改配置文件时要用。

2\) export \[-fnp\] var\[=value\] 新增, 修改或删除环境变量,
只限于本次登录; -p列出所有shell赋予的环境变量, -n删除指定环境变量,
但变量本身不会删除, -f其中var是函数名称

3\) 环境变量配置文件

/etc/profile

/etc/profile.d/\*.sh

\~/.bash_profile 可以将用户PATH加入, export PATH=\"\$PATH:\$HOME/bin\" ;

\~/.bashrc 可以将用户设置的别名(加在if前)、PATH(加在fi后)加入到里面,
\~/下的这两个文件权限都是644。注意每次改完环境配置文件需要source。

/etc/bashrc

正常登录配置文件加载顺序: /etc/profile \-- \~/.bash_profile \--
\~/.bashrc \-- /etc/bashrc \-- 命令提示符

\~/.bash_logout 退出登录时的配置文件, 退出前需要执行logout才生效

4\) shell登录信息

/etc/issue, /etc/issue.net linux版本信息, unmae -a 查看内核版本

/etc/motd 登录之后的提示信息

2.2 shell编程

2.2.1 通配符与正则表达式

1\) 通配符

正常shell命令都是以通配符进行匹配

  ------------------------------------ ----------------------------------
  \"?\" 为任意一个字符                 \"\*\" 为0或任意多个字符

  \"\[abc\]\" 为括号中的任意一个字符   \"\[a-z\]\"
                                       为a-z范围内的任意一个字符

  \"\[\^0-9\]\"                        
  为不是括号范围内的一个字符           
  ------------------------------------ ----------------------------------

2\) 基础正则

模式语法: /pattern/mode

转义:

  ----------------------------------- -----------------------------------
  \"\\\" 转义符                       \"\\0\" 查找null字符

  \"\\s\" 查找一个空白符              \"\\S\" 查找非空白符

  \"\\d\" 查找一个数字                \"\\D\" 查找非数字

  \"\\w\" 查找一个数字, 字母, 下划线  \"\\W\" 查找非单词字符

  \"\\b\" 匹配单词边界必须是\\w       \"\\B\" 匹配非单词边界

  \"\\n\", \"\\f\", \"\\r\", \"\\t\", 
  \"\\v\" 查找换行, 换页, 回车,       
  制表符, 垂直制表符                  
  ----------------------------------- -----------------------------------

特殊字符:

  ----------------------------------- -----------------------------------
  \"/\^ab/\" 以ab开头的行             \"/ab\$/\" 以ab结尾的行

  \"/./\" 任意一个字符(除换行符)      \"/a\*/\" 其中a出现0或多次

  \"/a+/\" 表示a出现1次或多次         \"/a?/\" 其中a出现0或1次, 同{0,1}

  \"/a\|b\|c/\" 匹配a或b或c           
  ----------------------------------- -----------------------------------

范围:

  ----------------------------------- -----------------------------------
  \"/\[abc\]/\" 匹配abc中任意一个字符 \"/\[a-z\]/\"
                                      匹配a到z之间任意一个字符

  \"/\[\^0-9\]/\" 排除0-9间的数字     \"/a{n}/\" 表示a出现n次\"

  \"/a{n, }/\" 表示a出现n次及以上     \"/a{n,m}/\" 表示a出现n到m次
  ----------------------------------- -----------------------------------

捕获组:

  ------------------------------------ ----------------------------------
  \"/(...)/\" 捕获组,                  \"/(a\|b)/\" 匹配出现a或b的行
  如/(go)+/匹配gogo                    

  \"/(a(\\w)\\s\*(\\d)b\$)/\" 嵌套组   \"/a(b)?(c)?/\" 可选组, 可选项b c

  \"/x(?=y)/\" 当x后为y时匹配x         \"/x(?!y)/ \" 当x后不是y时匹配x

  \"/(?\<=y)x/ 当x前面是y时匹配x       \"/(?\<!y)x/\" 当x前面不是y时匹配x

  \"/(?:xx)/\" 排除匹配的xx            

  \"/(?\<name\>...)/\" 命名组, 如:     
  /(?\<y\>\\d{4})-(?\<m\>\\d{2})/,     
  通过y, m调用匹配结果                 
  ------------------------------------ ----------------------------------

2.2.2 字符串处理

1\) 字符串操作(var=\"string\")

字符串长度: \${#var}

删除: \${var#\*str}删除从左往右匹配到第1个\"str\"内容, 右边内容保留

\${var##\*str}删除从左往右匹配到最后1个\"str\"内容, 右边内容保留

\${var%str\*}删除从右往左匹配到第1个\"str\"内容, 左边内容保留

\${var%%str\*}删除从右往左匹配到最后1个\"str\"内容, 左边内容保留

切片: \${var:n1:n2}, n1为索引值, n2为切片长度, 注意与python不一样

内容替换: \${var/old/new}将字符串中的第一个old字符换为new字符,
\${var//old/new}替换所有。

2\) 字符串大小写转换

首字母转换为大写: \${var\^} ; 所有字母转换为大写: \${var\^\^}

首字母转换为小写: \${var,} ; 所有字母转换为小写: \${var,,}

3\) cut \[-fdc\] file

列提取命令, -f以tab为分割符提取第几列; -d指定分割符;
-c通过字符范围提取列, 行首为0, \"n-\"第n个字符到行尾,
\"n-m\"从第n个字符到第m个字符, \"-m\"从第1个字符到第m个字符。

4\) printf格式化输出, linux不支持print

printf \"format string\" \$var1 \$var2 ....

format string: %ns输出字符串, n代表几个字符; %nd输出整数, n是几个数字;
%m.nf输出浮点数, m总共位数, n小数位, 整数m-n, 一般为%.nf;
%m.ne输出科学计数法

格式: \\n换行, \\r回车, \\t是tab

示例: printf \"%-16s %.3e\\n\" \$a \$b 其中\"-\"号是左对齐, 默认右对齐

5\) tr \[-c/d/s/t\] \[SET1\] \[SET2\]
用于转换或删除文件中的字符(SET1和SET2)

-c 取反; -d 删除; -s 缩减连续重复的字符成指定的单个字符; -t
将SET1用SET2转换

echo \"aabbcc\" \| tr -s \[a-z\] 去重 ; cat test.txt \| tr -cd
\"\[0-9\]\" 删除非数字字符

cat test.txt \| tr \[a-z\] \[A-Z\] 将小写转换为大写

2.2.3 sed编辑器

sed \[option\] 'command' file

option: -n只输出改变行, -i直接修改文本, -e输出改变后的整个文本,
-E使用扩展正则

command: a, i, d, s, c, p

sed -i 'n1,n2a str' file 在file的第n1到n2行的每行下一行添加一行str

sed -i 'n1,n2i str' file 在file的第n1到n2行的每行上一行添加一行str

sed -i 'n1d;n2d' file 在file的删除第n1和n2行, n2为\$时表示到最后一行

sed -i 'n1,n2s/old/new/g' file 将file中第n1到n2行中所有old换成new,
没有g就只换第一个old, 没有n1,n2替换全文

sed -i 'n1,n2c str' file 将file中的n1到n2行用str替换

sed -n 'n1,n2p' file 打印file中的n1到n2行

sed -i '/正则表达式/{command1 ; command2}' file 命令组合形式,
前面也可是行数。

sed -i '/正则表达式/{ N ; command }' file
在匹配到的正则行和下一行进行操作

sed -i '/正则表达式/!command \[str\]' file
在排除匹配正则行的其它行进行操作

sed -i '{/正则/b label ; s/old/new/ ; :label ; s/OLD/NEW/}' file
匹配到正则行用s/OLD/NEW处理, 其它行用s/old/new/处理,
如果没有label则跳过匹配行。

如果需要引用变量用双引号。

2.2.4 awk程序

awk options program file1 file2

awk '{print}或{print \$0}' file 打印file, \$0表示整个文本行

awk -F \":\" '{\$n1=\"str\" ; print \$1 , \$n2}' file
以\":\"为分割符(默认为空格), 将file中的n1行赋值为str, 打印\$1和\$n2列,
{print \"str\"\$n}如果要在\$n前或后放其它字符, 需要用引号引起来。

awk -f script file 从script中读取command处理file

awk 'BEGIN { command1} { print \$n } END { command2 }' file
数据处理前后执行命令。注意command命令为awk程序内部命令,
shell命令有些不执行。

awk 'BEGIN {FS=\"\\n\" ; RS=\"\\t\" ; OFS=\"-\" ; ORS=\"/n\"} ; {print
\$n1,\$n2 }' file 其中FS和OFS为输入和输出分割符, 默认值分别为空格;
RS和ORS为输入和输出纪录符, 记录符是标记数据行的符号,
默认为换行符\"\\n\"。注意sed和gawk都是按数据行处理数据的。

awk内部也可以自定义变量: gawk \'BEGIN{x=4; x= x \* 2 + 3; print x}\'

awk \'BEGIN{FS=\",\"} ; /正则表达式/{print \$n}\' file

awk \'BEGIN{FS=\",\"} ; \$n1 \[!\]\~ /正则表达式/{print \$n2}\' file
当\$n1和正则匹配的行, ！\~和正则不匹配的行。

awk \'\$n1 == 0{print \$n2}\' file 其它数学比较\>=, \<=, \>, \<

awk \'{ if (condition) statement1; else statement2 }\' file

awk '{ i = 1 ; while (i \< 3) ; { i++ }; print \$i}' 冒号换行

awk 'function name(\[var\]) ;{ statements } ; {name(\[var\])}' 冒号换行

注意格式化输出需要使用printf

awk内建变量, 可通过变量名直接调用不用\$, 一般用于控制输出

  ----------------------------------- -----------------------------------
  ARGC 当前命令行参数个数             ARGV 包含命令行参数的数组

  ARGIND 当前文件在ARGV中的位置       ENVIRON 当前shell环境变量及值

  FNR 当前文件处理行数的记录          IGNORECASE 非0时忽略命令中大小写

  NR 已处理的总行数记录, NR == n 控制 NF每行的字段总数, \$NF每行最后字段
  ----------------------------------- -----------------------------------

2.2.5 条件判断

\[ command \] 或 test command \[file\]

1\) 文件类型判断

  ----------------------------------- -----------------------------------
  -d file 文件存在并且是目录          -e file 文件是否存在

  -f file 文件存在且为普通文件        -s file 文件是否存在, 且文件非空

  -L file 文件存在且为符号链接文件    -h file 文件是否为软连接
  ----------------------------------- -----------------------------------

注意在脚本中判断文件时尽量使用绝对路径。

2\) 文件权限判断

  ----------------------------------- -----------------------------------
  -r file 文件存在且有读权限          -w file 文件存在且有写权限

  -x file 文件存在且有执行权限        -u file 文件存在且有SUID权限

  -g file 文件存在且有SGID权限        -k file 文件存在且有SBIT权限
  ----------------------------------- -----------------------------------

3\) 数值比较(只能比较整数, 浮点数比较要用bc)

  ----------------------------------- -----------------------------------
  n1 -eq n2 等于                      n1 -ne n2 不等于

  n1 -gt n2 大于                      n1 -lt n2 小于

  n1 -ge n2 大于等于                  n1 -le n2 小于等于
  ----------------------------------- -----------------------------------

4\) 字符串比较

  ----------------------------------- -----------------------------------
  \[ -n \"\$var\" \] 字符长度不为零   \[ -z \"\$var\" \] 字符长度为零

  \[ \"\$var1\" == \"val\" \] 相等,   \[ \"\$var1\" != \"val\" \] 不相等
  可用通配符                          

  \[\[ \"\$var\" =\~ \"val\" \]\]     \[\[\"\$var\" != \"val\" \]\]
  包含val                             不包含val
  ----------------------------------- -----------------------------------

5\) 逻辑运算符

\[ command1 -a\|o command2 \] 与\|或 ; \[ ! command \] 非

\[ command1 -o command2 \] &&/\|\| \[ command3 -a command4 \] 与/或

2.2.6 流程控制

1\) if和case

+---------------------------------+------------------------------------+
| if语句:                         | case语句                           |
|                                 |                                    |
| if expression1 ; then           | case variable in                   |
|                                 |                                    |
| command1                        | pattern1) commands1 ;; //匹配执行  |
|                                 |                                    |
| elif expression2 ; then         | pattern2 \| pattern3) commands2 ;; |
|                                 |                                    |
| command2                        | \*) default commands ;;            |
|                                 | //都不匹配执行                     |
| else                            |                                    |
|                                 | esac                               |
| command3                        |                                    |
|                                 |                                    |
| fi                              |                                    |
+---------------------------------+------------------------------------+

2\) for循环

+---------------------------------+------------------------------------+
| for遍历                         | C风格for                           |
|                                 |                                    |
| for var in list ; do            | for (( i ; i\<n ; i++ )) ; do      |
| 其中list是由一组空格            |                                    |
|                                 | commands                           |
| commads ; 或换行隔开的一组列表  |                                    |
|                                 | done                               |
| done                            |                                    |
+---------------------------------+------------------------------------+

3\) while循环, until循环

+---------------------------------+------------------------------------+
| while循环                       | until循环, 不满足expression执行    |
|                                 |                                    |
| while expression ; do           | until expression ; do              |
|                                 |                                    |
| command                         | command                            |
|                                 |                                    |
| done                            | done                               |
+---------------------------------+------------------------------------+
| 按行处理文本:                   | 按行处理文本:                      |
|                                 |                                    |
| cat file \| while read line ;   | while read line ; do               |
| do                              | #变量是\${line}                    |
|                                 |                                    |
| command; #变量只在子shell生效   | command; #变量在当前shell也生效    |
|                                 |                                    |
| done                            | done \< file                       |
+---------------------------------+------------------------------------+
| 命令行参数:                     |                                    |
|                                 |                                    |
| while getops :a:b:c: opt ; do   |                                    |
| #第一个\':\'去错误消息,         |                                    |
| 后面\':\'表示接收参数           |                                    |
|                                 |                                    |
| case \"\${opt}\" in             |                                    |
|                                 |                                    |
| a\) var=\$OPTARG ;; #当前参数值 |                                    |
|                                 |                                    |
| b\) ...; \$OPTIND ;;            |                                    |
| #当前参数位置,                  |                                    |
| 在循环前使用OPTIND=1防止漏参    |                                    |
|                                 |                                    |
| \*) ... ;;                      |                                    |
|                                 |                                    |
| esac                            |                                    |
|                                 |                                    |
| done #用法: test.sh -a xx -b xx |                                    |
|                                 |                                    |
| shift \$\[ \$OPTIND - 1 \]      |                                    |
| #移动参数位置                   |                                    |
+---------------------------------+------------------------------------+

4\) 循环控制

break #跳出当前整个循环本身

continue #跳出当前单次循环, 进行下一个循环

exit n #退出当前脚本, n必须是整数, 默认为0, 可用\$?调用

2.2.7 函数

1\) 创建函数

function name() {

local var\[=\"value\"\] 声明局部变量, 防止该变量在全局生效,
而全局变量可以直接在函数内部使用。

commands \$1 \$2 ... \${n} \$# \${n}给函数传递的参数,
\$#是传递参数的个数, 函数的输出要用echo

\[return int\] 返回的是函数结束的状态码, 通过\$?调用, 默认0和1,
只能是整数。

}

name \"value1\" \"value2\" 调用name函数

2\) 创建函数库

函数库文件funcs.io

function myfunc() {

command

}

在脚本中使用:

source dir/funcs.io 调用库函数, 尽量使用绝对路径, 注意当有多个库时,
不能批量导入

myfunc 直接调用函数名, 也可以传参数

2.2.8 shell脚本技巧

1\) 语法检查

在脚本开头设置: set -e ,如果有语法错误就退出,
否则即使出错脚本依然会继续运行。

2\) 打印脚本执行过程

在脚本开头设置: set -x , 会打印脚本执行过程

3\) 打印未定义变量

在脚本开头设置: set -u ,当脚本中有清空目录等操作时, 该设置十分必要

4\) 管道命令一个失败整个停止

在脚本开头设置: set -o pipefail , 因为在长管道中,
即使有一个失败也不会影响后面的命令, 所以该设置在有长管道时十分必要。

5\) 对于静态变量防止被篡改

readonly MY_PATH=/usr/local ,可以避免在别处该变量被修改

6\) 给变量设置默认值

name=\${1-test} 如果有参数传入name=\$1, 没有参数传入name=\"test\";
特别注意传入变量的作用域, 不能在函数中接收命令行变量。

name=\${var+string} 若var不为空, 将string值赋给name

name=\${var:-string} 若var为空, 将string赋给name;

name=\${var:=string} 若var为空, 将string同时赋给name和var;

name=\${var:+string} 若var不为空, 将string赋给var;

name=\${var:?string} 若var为空, 将string输入到标准错误, 并退出程序。

7\) 多条命令

cmd1 ; cmd2 ; cmd3 如果cmd1失败, cmd2, cmd3依然执行

cmd1 && cmd2 && cmd3 如果cmd1失败, 整条终止

8\) #!/usr/bin/python3 相当于写死了 python3 路径;

#!/usr/bin/env python3 这种用法是为了防止操作系统用户没有将 python3
装在默认的 /usr/bin 路径里。当系统看到这一行的时候, 首先会到 env
设置里查找 python3 的安装路径, 再调用对应路径下的解释器程序完成操作,
推荐使用。

9\) 并发执行

command1 &

command1 &

wait #wait是等待并发完成后在执行接下来的任务。

三 Linux系统管理

3.1 linux启动管理

3.1.1 启动引导与修复

1\) 系统运行级别: 0关机(shutdown.target); 1单用户模式(emergency.target),
用于系统修复; 2不完全的命令行模式(rescure.target), 不含NFS服务;
3完全命令行模式(multi-user.target), 标准界面; 4系统保留;
5图形模式(graphical.target); 6重启动。

运行级别查看: runlevel 或 systemctl get-default

运行级别切换: systemctl isolate runlevel5.target\|graphical.target

系统默认运行级别修改/etc/inittab 或 systemctl set-default
multi-user.target

2\) /etc/rc.d/rc.local: 该配置文件会在用户登录之前读取,
每次系统启动时都会执行一次里面的命令。可以将需要系统启动时就运行的工作写入此配置文件。如果设置了,
就需要赋予执行权限: chmod +x /etc/rc.d/rc.local

设置开机更新时间: /usr/sbin/ntpdate -s ntp1.aliyun.com ;
/usr/sbin/hwclock -w(写入硬件, -r查看)

3\) 启动引导程序(Boot Loader): grub2

配置文件: /boot/grub2/grub.cfg 或者是/etc/grub2.conf, 两者修改效果一致

timeout=5是开机默认等待5s, 在这期间可以按e键进行编辑操作,
注意不能是0和-1.

title以下就是可用操作系统, 每一个title开头就是一个操作系统

grub加密: 运行grub2-mkpasswd-pbkdf2, 然后设置密码, 将产生的密码复制,
修改/etc/grub2.conf文件, 只能放在timeout下一行, 设置为password \--md5
密码(刚刚复制的) , 保存重启, 编辑模式由e键变成p键, p键输入密码,
然后再按e才能进入编辑模式。

查看内核: cat /boot/grub2/grub.cfg \| grep menuentry

设置默认启动项: grub2-set-default \"CentOS Linux
(4.4.221-1.el7.elrepo.x86_64) 7 (Core)\"

查看默认启动: grub2-editenv list

centos8的配置略有不同。

4\) 系统修复

系统修复需要到本机操作。

单用户模式: 主要用于破解密码; 进入init 1(一般不会用到),
一般是在开机时按e键进入, 光标到第二行, 又按e键, 然后输入空格和1,
进入后按b键启动, 然后登录passwd root改密码就行了。

光盘修复模式: 先将光盘或优盘插入, F2进入BIOS, boot改成光盘或u盘启动,
移动到目标按+号变成第一项, 保存退出, 进入rescue installed system,
然后进入安装流程, continue, 通过shell start shell进入安全模式,
进入到光盘根目录

真正的根目录在/mnt/sysimage/目录中, chroot
/mnt/sysimage切换到真正的系统目录, 然后修改出错的地方。修改完后,
关机将BIOS改成硬盘启动。

3.2 linux服务管理

3.2.1 服务分类

RPM包默认安装服务: 独立服务、基于xinetd服务(不用了)

源码包安装的服务

服务分类查询: chkconfig \--list, 能看到0-6的服务都是独立服务

3.2.2 服务管理命令

1\) systemctl命令:

systemctl status\|start\|stop\|reload\|restart\|kill server
查看\|启动\|停止\|重载\|重启\|杀死 服务

systemctl enable\|disable\|is-enabled server_name
允许\|禁止\|查看服务开机自启动

systemctl list-unit-files \[ \| grep enabled\] 查看服务开机设置列表

systemctl \--failed 查看启动失败的服务

systemctl get-default 显示当前运行级别

systemctl isolate runlevel5.target\| graphical.target 启动运行级别5

systemctl set-default runlevel3.target\| multi-user.target
设置开机默认运行级别3

systemctl daemon-reload 修改完配置文件后, 重新加载systemctl服务

注意: 所有服务配置文件在/etc/systemd/system和/usr/lib/systemd/system

2\) service和chkconfig

service server_name status\|start\|stop\|restart\|kill
查看\|启动\|停止\|重启\|杀死服务

chkconfig server_name on\|off 允许\|禁止\|查看服务开机自启动

chkconfig \--list 查看服务列表

注意: service管理的服务配置文件在/etc/rc.d/init.d

3\) journalctl -ex 启动日志详情查看

3.2.3 服务启动

1\) 独立服务:

启动(两种):

/usr/local/httpd/bin/apachectl \[-k\] start (绝对路径启动, 标准启动,
-k属于apache)

systemctl start\|stop\|restart httpd

开机自启动(两种):

systemctl enable\|disable 服务名

修改/etc/rc.d/rc.local , 将标准启动命令加入

注意开机自启动方法只能二选一, 因为这两种方法没有直接关联

2\) 源码包服务

注意源码包安装默认不能被systemctl识别

a\) 启动: /usr/local/httpd/bin/apachetl -k start\|stop\|restart\|...
手工启动(推荐)

b\) systemctl启动方法一(推荐):

需要在/usr/lib/systemd/system/或/etc/systemd/system/目录下创建一个httpd.service脚本:

\[Unit\]

Description=httpd - web server

After=network.target 说明在network之后启动, 随便设置

Before=sshd.service 在sshd之前启动, 可不要, 只是说明

\[Service\]

User=username 指定其它用户或组(root不用指定)

Group=groupname

Type=forking(后台运行) 启动类型, 默认simple(ExecStart字段启动的为主进程)
, notify 与simple 相同, 但约定服务会在就绪后向 systemd 发送一个信号。

ExecStart=/usr/local/httpd/bin/apachectl -k start

#ExecReload=/usr/local/httpd/bin/apachectl -k restart

ExecStop=/usr/local/httpd/bin/apachectl -k stop

Restart=always\|no\|on-failure 失败后重启策略

PrivateTmp=True
服务启动时会在/tmp目录下生成类似systemd-private-xx的文件夹,
用于存放apache的临时文件。可有可无

LimitNOFILE=60000 最大文件打开数

LimitNPROC=20000 最大连接线程数

\[Install\] 定义如何安装这个配置文件, 即怎样做到开机启动

WantedBy=multi-user.target 表示该服务所在的Target,
表示在运行级别3时才能用

设置完后重新加载服务: systemctl daemon-reload

c\) systemctl启动方法二:

ln -s /usr/local/httpd/bin/apachetl /etc/rc.d/init.d/httpd,
修改软连接过来的httpd, 在第三行下面添加:

\# chkconfig: 35(运行级别为3和5) 85(启动顺序) 75(关闭顺序)
后两个不能现有的重复

\# description: Activates/Deactivates Apache Web Server

设置完后重新加载服务: systemctl daemon-reload

d\) 开机自启动: 修改/etc/rc.d/rc.local, 将上面命令加入, 或编写脚本加入

注意: 手动配置的文件, 在服务卸载时一定要记得删除, 不然下一次安装会冲突。

3.2.4 用户级服务管理

将用户级systemd配置文件保存在: \~/.config/systemd/user/xxx.service
或\~/.local/share/systemd/user/xxx.service 或
/etc/systemd/user/xxx.service

调用: systemctl \--user daemon-reload 加载配置

systemctl \--user start\|status\|reload\|stop xxx.service

systemctl \--user enable\|disable xxx.service

systemctl \--global enable\|disable xxx.service #使用root管理,
使其具有全局性

3.2.5 常见问题

1\) systemctl管理的limit

修改/etc/systemd/system.conf默认值(对所有服务生效):

DefaultLimitNOFILE=100000

DefaultLimitNPROC=65535

或修改每个服务的systemctl配置文件/etc/systemd/system/nginx.service

LimitNOFILE=60000

LimitNPROC=20000

查看: cat /proc/pid/limits (进程号通过ps查看)

3.3 linux进程管理与工作管理

3.3.1 查看系统进程

1\) ps -aux \| -ef

-a显示所有进程; -u进程归属用户及负载(-u root); -x后台进程;

-e程序使用得环境变量; -f显示UID,PPIP,C,STIME

其中进程状态STAT有: R运行, S休眠(等待某个信号唤醒或中断);
D不可中断(不可唤醒只能终止进程); Z僵尸进程(进程终止但描述符还在);
T停止状态; X死掉的进程; 后缀状态: N低优先级; s包含子进程; l多线程;
+后台运行; \<较高优先级进程

ps -T -p 916(PID号) \[-L -o pcpu,pmem,pid,tid,time,tname,cmd\]
查看该进程的线程

cpu排序: ps -aux \| head -1 ; ps -aux \| grep -v PID \| sort -rn -k +3
\| head

ps -aux \--sort -pcpu \| head

内存排序: ps -aux \| head -1 ; ps -aux \| grep -v PID \| sort -rn -k +4
\| head

ps -aux \--sort -pmem \| head

合并: ps -aux \--sort -pcpu,+pmem \| head -n 10

ps -axjf 列出类似程序树的程序显示

ps -aef \| grep inetd 列出奇怪得守护进程

ps -ef \| awk \'{print}\' \| sort -n \|uniq \>1 列出隐藏进程

2\) pstree 查看进程树, 帮助查看同一个进程启动数量

3\) top \[option\]

实时更新进程状态, 是以交互模式进入的, ?\|h帮助, P以cpu使用率排序,
M以内存使用率排序, N以PID排序, q退出.

option: -d 秒数, 指定更新间隔; -b使用批处理模式输出; -n 次数,
指定top刷新次数(top -b -n 1 \> test.log); -p指定PID; -u 用户名,
监听某个用户的进程。

top输出重要内容如下: load average: 64.66, 64.53,
64.43平均负载(如果CPU是4核, 这个数超过4为高负载, 一般不要超过cpu核数);
%Cpu(s): 0.0 id(空闲cpu占用率); MiB Mem : 82775.5 free空闲内存; MiB
Swap: 2917.0 free空闲swap内存

4\) pidstat -r -p PID \[t n\] 查看进程的内存占用 \[刷新频率 次数\]

5\) lsof 系统监视工具, 用于进程打开的文件, 打开文件的进程,
进程打开的端口等。

lsof输出各列信息的意义:
https://blog.csdn.net/qq_27870421/article/details/92803453

lsof \[+D\] dir/file 查看使用该文件的进程, +D递归查看目录所有文件情况

lsof -u username 查看某个用户打开的文件信息

lsof -c mysql -c nginx \[-u test \]查看某个程序打开的所有文件,
可组合使用。

lsof -p 2,3 查看某进程(pid)打开的文件

lsof -i tcp:80 指定端口; lsof -i \@192.168.10.16 显示连接到该ip的信息

lsof \| wc -l 查看系统当前打开得文件总数

3.3.2 结束进程

1\) killall \[option\] \[server_name\]

用杀死指定名字的进程, 可以配合ps , grep使用

  ----------------------------------- -----------------------------------
  -Z 只杀死拥有scontext 的进程        -e 要求匹配进程名称

  -I 忽略小写                         -g 杀死进程组而不是进程

  -i 交互模式, 杀死进程前先询问用户   -l 列出所有的已知信号名称

  -s 发送指定的信号                   -q 不输出警告信息
  ----------------------------------- -----------------------------------

2\) kill \[option\] \[id\]

常用于杀死单个进程

  ----------------------------------- -----------------------------------
  -l 列出所有信号                     -a 不限制命令名和进程号的对应关系

  -s 指定发送信号                     -u 指定用户
  ----------------------------------- -----------------------------------

kill -1 PID 重启进程; 等价于kill -s HUP PID

kill -3 PID 或kill -s QUIT pid 退出进程, 尽量用这个来停止服务

kill -9 PID 强制终止进程

3\) pkill 类似于killall

pkill -9 -t 终端号 把某个登录终端踢掉

3.3.3 资源查看

1\) 监控系统资源: vmstat \[2 5(每两秒刷新一次, 共5次)\]

2\) lscpu查看系统cpu信息, 同/proc/cpuinfo

3\) uptime 显示系统启动时间和平均负载, 就是top和w的第一行

4\) uname -a/r/s 显示系统信息, -a详情, -r内核版本, -s内核名称

cat /etc/redha-releases查看linux发行版本(debian : lsb_release -a)

5\) dstat \[option\] 监控工具; dstat 1 3(每秒1次, 共3次); dstat
\[\--top-cpu\|\--top-mem\] 3 3\\

6\) ntsysv 服务器优化, 关闭不常用的应用

7\) 大页内存设置情况: cat /proc/meminfo \| grep -i huge

3.3.4 工作管理

1)后台运行作业

command & 命令在后台执行

命令执行过程中ctrl+z, 命令在后台是暂停状态

2)后台作用查看: jobs \[-l\] , -l查看PID号

3)后台作业恢复运行: bg %工作号

4)恢复到前台运行: fg %工作号

5)后台命令脱离终端运行(三种方法)

1\) 将需要执行的命令写入/etc/rc.local中, 系统启动时就在后台执行

2\) 使用系统定时任务

3\) nohup command &

3.3.5 系统定时任务

1\) at一次性执行定时任务

at命令需要开启atd服务

at配置文件: /etc/at.allow(用户白名单,
只有写入的用户能用atd服务,需要自己创建)和/etc/at.deny(用户黑名单,写入的用户不能使用at,
一个用户名一行, 如果有at.allow, 其将会失效)

at \[options\] time ; /bin/sh commamd ; ctrl+D ; option: -m当at工作完后,
无论命令是否有输出, 都用email通知at命令用户; -c 工作号
显示at工作的实际内容

time格式: HH:MM(02:30); HH:MM YYYY-MM-DD(02:30 2020-02-02);
HH:MM\[am\|pm\] \[mouth\] \[date\](02:30pm July 25); HH:MM\[am\|pm\]
+num \[minutes\|hours\|day\|weeks\](now +5 minutes, 指定时间后多久执行)

atq 和at -c 工作号 查询定时任务

atrm \[工作号\] 删除任务

2\) crontab 循环执行定时任务(三种)

crontab命令需要crond服务(debian为cron服务)支持, 启动方法同atd;
同样有/etc/cron.allow(白名单)和/etc/cron.deny(黑名单)

命令执行: crontab \[option\] ; command ; 保存退出 ; options: -e编辑任务,
-l查询任务, -r删除用户所有crontab任务, 删一个用-e; -u
name修改或删除其它用户任务

\(1\) crontab -e ; 回车进入编辑模式; \* \* \* \* \* username
要执行的任务; 保存退出

时间格式: \*(第几分钟0-59) \*(第几小时0-23) \*(第几天0-31)
\*(第几月1-12) \*(第几星期1-7); '\*'表示任何时间; ','表示不连续时间0
8,12,20 \* \* \*; '-'表示连续时间0 5 \* \* 1-6;
'\*/n'每隔多久执行一次。注意星期几和几号不要一起用

\(2\) 修改配置文件执行: /etc/crontab;

\(3\)
将执行脚本放入相应的/etc/cron.{d,daily,weekly,monthly}中相应的日期文件夹中,
也会自动执行。注意放入cron.d只能放系统级别任务, 其中的文件权限为644,
尽量加上 \> /dev/null 2\>&1将输出重定向到/dev/null,
而放入cron.daily等文件夹中的是可执行脚本(744)。

注意定时任务一定要使用绝对路径, 如0 \*/1 \* \* \* root /usr/sbin/ntpdate
-s ntp1.aliyun.com

\(4\) /var/spool/cron/存放用户级定时任务(建议使用),
定时任务文件名为用户名, 文件权限(600), 文件属主.属组为用户名, 格式: \*
\* \* \* \* path/test.sh (不用指定用户)。

注意定时任务执行脚本中的命令、文件等都要使用绝对路径,
包括执行脚本中路径都要使用绝对路径, 不然会以\$HOME为路径执行。

3\) anacron

防止关机漏执行的任务,
/var/spool/anacron文件夹中保存了相关任务上一次执行时间,
其配置文件/etc/anacrontab中(不需要修改),
你只需要将你相关的脚本放到/etc/cron.xxx相应的文件夹中就行

3.4 日志管理

open3.4.1 常见的日志/var/log/

1\) dmesg开机时内核自检信息,
可使用dmesg查看。程序挂掉后快速查找途径之一: dmesg \| grep programName

2\) btmp记录错误登录(二进制文件), 通过lastb查看, 防止黑客。

3\) lastlog所有用户最后一次登录, lastlog查看;

4\) wtmp记录所有用户登录、注销信息、系统启动、关机、重启, 用last查看;

5\) utmp当前已经登录用户信息, 通过w, who, users查看。

6\) message记录系统重要信息的日志, 系统出错首先查看;

7\) secure记录验证和授权相关信息, 只要涉及账户和密码的程序都会记录。

注意只有通过rpm包方式安装的软件, 会默认将日志记录在/var/log/目录,
源码包安装的日志在安装目录中。

3.4.2 rsyslogd服务

1\) message日志格式: 时间 主机名 产生日志的服务 具体日志消息

2\) 配置文件/etc/rsyslog.conf;

修改完服务后: systemctl restart rsyslog

3.4.3 日志切割轮替

1\) 日志轮替

把旧日志文件移动并改名, 同时建立新的空日志文件,
当旧日志超出保存范围后就删除。主要依靠/etc/logrotate.conf配置文件,
其在/etc/logrotate.conf文件或放在/etc/logrotate.d目录。

主要参数:

  -------------------------------------------------- ---------------------------------
  daily, weekly, monthly 日期                        rotate num 保留日志文件个数

  notifempty如果日志为空, 不轮替                     missingok 忽略更改日志的警告信息

  size=5M日志轮替大小, 不按时间轮替                  mail address 当日志轮替时发邮件

  dateext使用日期作为轮替文件尾缀                    compress日志压缩

  delaycompress当日志存入下一个文件才压缩            

  create 0600 root root                              
  建立新日志时指定权限、所有者和属组                 

  sharedscripts在此关键字后的脚本只执行一次,         
  脚本执行的必须关键字                               

  prerotate/endscript在日志轮替前执行脚本            

  postrotate/endscript在日志轮替之后执行脚本命令。   
  -------------------------------------------------- ---------------------------------

设置appache日志轮替

/usr/local/httpd/logs/access_log /usr/local/httpd/logs/error_log {

rotate 5

missingok

notifempty

dateext

create 0600 root root

size=5M

sharedscripts

postrotate

/usr/bin/kill -HUP \$(/usr/bin/cat /var/run/syslogd.pid) &\> /dev/null

/usr/local/httpd/bin/apachectl -k graceful &\> /dev/null

endscript

}

手工安装包, 每次日志轮转时可能需要重启服务。

logrotate \[-vf\] /etc/logrotate.d/nginx 查看轮替设置,
-v显示日志轮替过程, -f强制轮替

3.5 防火墙

3.5.1 firewalld简介

![](media/image1.png){width="0.8173611111111111in"
height="1.1145833333333333in"}firewalld是用来管理netfilter子系统,
不过底层调用的命令仍然是iptables等。由netfilter即网络过滤器来读取,
从而真正实现防火墙功能。

firewalld将网卡对应到不同的区域(zone), zone 默认共有9个。

丢弃区域(drop): 流入网络包直接丢弃, 不作出任何响应, 只允许流出。

阻塞区域(block): 拒绝流入的网络, 并返回报文。

公共区域(public): 不可信, 只接受被选中的网络连接, 默认区域。

外部区域(external): 用在路由器等启用伪装的外部网络。不可信,
只接受被选中的连接。

隔离区域(DMZ): 内外网络之间增加的一层网络,
起到缓冲作用。只接受被选中的连接。

工作区域(work): 信任网络上的其他计算机, 只接受被选中的连接。

家庭区域(home): 信任网络上的其他计算机, 只接受被选中的连接。

内部区域(internal): 信任网络上的其他计算机, 只接受被选中的连接。

信任区域(trusted): 所有的网络连接都可以接受。

3.5.2 firewalld配置文件

1\) 系统默认配置文件

zones配置文件在/usr/lib/firewalld/zones/

services服务配置文件/usr/lib/firewalld/service/

2\) 用户配置文件

zones配置文件在/etc/firewalld/zones/

services服务配置文件/etc/firewalld/service/

用户需要更改服务端口等: cp -a /usr/lib/firewalld/service/jenkins.xml
/etc/firewalld/service/.
然后更改。用户可以自己添加到services到/etc/firewalld/service/下

加载配置: firewall-cmd \--reload

管理firewalld服务: systemctl start\|stop\|restart firewalld

3.5.3 firewall-cmd命令

1\) 查看相关信息: firewall-cmd options

  ------------------------------------------------------------------ -------------------------------------------
  \--get-active-zones 查看所有活动zone                               \--get-zones\|services支持zones和services

  \--get-default-zone 查看默认zone                                   \--get-zone-of-interface=ens33 查看接口zone

  \--list-all-zones 查看所有zones的配置                              \--info-zone=public 查看详情

  \--zone=public                                                     
  \--list-services\|ports\|\--list-all查看相关zone开放的服务和端口   

  \--zone=\<zone\> \--query-masquerade 查询区域伪装状态              

  \--zone=\<zone\> \--list-rich-rules 查看设置的富规则               
  ------------------------------------------------------------------ -------------------------------------------

2\) 设置默认区域

firewall-cmd \[\--permanent\] \--set-default-zone=drop

3\) 重载防火墙

firewall-cmd \--reload 注意加了永久生效\--permanent参数的设置都要重载

firewall \--complete-reload 完全重启防火墙, 当防火墙出问题时用

3\) 开启、移除端口或服务

firewall-cmd \--zone=public \[\--permanent\] \--add-port=8080/tcp\|udp
端口可以是范围80-90

firewall-cmd \--zone=XX \[\--permanent\] \--add-service=jenkins
\[\--remove-service=jenkins\]

时间组合(s): \--timeout=1200

可以同时有多种组合: \--add-service=http \--add-service=https

4\) 添加\|移除网卡到zone

firewall-cmd \--zone=XX \--permanent \--add\|remove-interface=ens33
(默认为public)

5\) 启用应急模式阻断所有网络连接, 以防出现紧急状况

firewall-cmd \--panic-on(开启) \| \--panic-off(关闭)
注意禁止在远程使用此命令

6\) 启用区域中的IP伪装功能

firewall-cmd \[\--zone=\<zone\>\] \--permanent \--add-masquerade

用于路由转发, 需要开启echo \"net.ipv4.ip_forward = 1\"
\>\>/etc/sysctl.conf ; sysctl -p

将两块网卡绑定在同一个zone, 该命令自动实现SNAT, DNAT转换。

7\) 禁用, 信任某网段

firewall-cmd \--zone=drop\|trusted \--permanent
\--add-source=192.168.100.0/24

8\) 端口和服务转发

firewall-cmd \[\--permanent\] \--zone=\[public\]
\--add-forward-port=port=\[源端口\]:proto=\[tcp\|udp\]:toport=\[目标端口\]:toaddr=\<目标IP
\>

firewall-cmd
\--add-forward-port=port=80:proto=tcp:toport=8080:toaddr=192.168.100.16

firewall-cmd \--permanent \--service=http
\--set-destination=ipv:address\[/mask\]

9\) 富规则(实现精细控制)

a\) 拒绝10.0.0.0/24网段中的10.0.0.10主机发起的SSH请求,但是允许访问http

firewall-cmd \--permanent \[\--zone=public\] \--add-rich-rule=\'rule
family=ipv4 source address=10.0.0.10 service name=ssh drop \'
(拒绝reject有回信, drop没有)

注意: 不指定family该规则同时用于ipv4和ipv6

b\) 只允许192.168.1.1的主机连接3306端口

firewall-cmd \--permanent \--add-rich-rule=\'rule family=ipv4 source
address=192.168.1.1 port protocol=tcp port=3306 accept\'

c\) 白名单\|黑名单, 允许源地址192.168.100.16所有连接

firewall-cmd \--add-rich-rule \'rule family=ipv4 source
address=192.168.100.16 accept\|reject type=icmp-admin-prohibited \'

d\) 开启地址伪装(NAT)

firewall-cmd \--permanent \--zone=external \--change-interface=eno34
外网

firewall-cmd \--permanent \--zone=internal \--change-interface=eno33
内网

firewall-cmd \--permanent \--zone=external \--add-rich-rule=\'rule
family=ipv4 source address=192.168.100.0/24 masquerade\' (实现SNAT)

e\) 将远程请求 22端口转发到后端192.168.100.11的2222端口 (实现DNAT)

firewall-cmd \--permanent \--zone=public \--add-rich-rule=\'rule
family=ipv4 \[source address=0.0.0.0/0\] forward-port port=22
protocol=tcp to-port=2222 to-addr=192.168.100.11 \'

移除相应富规则\--remove-rich-rule

10\) 直接规则(类似iptables但不同)

firewall-cmd \--direct \--add-rule ipv4 filter INPUT 2 -s 172.25.254.204
-p tcp \--dport 80 -j REJECT/ACCEPT/DROP (移除\--remove-rule(单条) ,
\--remove-rules ipv4 filter INPUT(所有))

查看: firewall-cmd \--direct \--get-all-rules

3.5.4 iptables

centos6自带防火墙工具, centos7要用先关闭firewall, 安装iptable
iptable-service

五链:

INPUT: 处理入站数据包; OUTPUT: 处理出站数据包; FORWARD: 处理转发数据包

POSTROUTING链: 在进行路由选择后处理数据包

PREROUTING链: 在进行路由选择前处理数据包

![](media/image2.png){width="2.2840277777777778in"
height="1.1979166666666667in"}四表:

raw表: 确定是否对该数据包进行状态跟踪

mangle表: 为数据包设置标记

nat表: 修改数据包中的源、目标IP地址或端口

filter表: 确定是否放行该数据包(过滤)。

重要的链表组合:

nat表: PREROUTING链(DNAT), POSTROUTING链(SNAT)

filter表: INPUT, OUTPUT, FORWARD

3.5.5 iptables命令

语法构成: iptables \[-t 表名\] 选项 \[链名\] \[条件\] \[-j 控制类型\]

注意: 不指定表名时, 默认指filter表; 不指定链名时, 默认指表内的所有链;
除非设置链的默认策略, 否则必须指定匹配条件;
选项、链名、控制类型使用大写字母, 其余均为小写书写规则。

控制类型:

  ------------------------------------ ----------------------------------
  ACCEPT: 允许通过                     DROP: 直接丢弃, 不给出任何回应

  REJECT: 拒绝通过, 必要会给出提示log  REDIRECT: 重定向

  SNAT: 修改数据包源地址               DNAT: 修改数据包目的地址

  MASQUERADE: 地址伪装                 
  ------------------------------------ ----------------------------------

选项:

  ------------------------------------ ----------------------------------
  -A: 在链的末尾追加一条规则           -I n: 指定规则插入序号,
                                       不指定在开头

  -D n: 删除指定序号的一条规则         -F: 清空所有的规则

  -n -L \[链名\] \--line-numbers       
  查看已有规则                         
  ------------------------------------ ----------------------------------

条件

  ------------------------------------ ----------------------------------
  -p tcp\|udp协议名地址匹配            -p ! tcp\|udp 协议名地址匹配取反

  -s ip 源地址(来的ip)                 -d ip 目的地址

  -i ens33 入站网卡                    -o ens34 出站网卡

  \--sport 22源端口                    \--dport 80 目的端口

  -m multiport \--sport                -m multiport \--dport
  22,80,8080源端口列表                 80-90目的端口列表

  -m iprange \--src-range              
  192.168.100.11-192.168.100.20        
  为IP范围                             
  ------------------------------------ ----------------------------------

实列:

清空原有规则

iptables -F ; iptables -t nat -F

拒绝ip为10.20.30.0网段从ens33网卡进入访问web服务

iptables -t filter -I \[2\] INPUT -i ens33 -s 10.20.30.0/24 -j DROP

拒绝访问web服务

iptables -t filter -I INPUT -p tcp \--dport 80 -j REJECT

SNAT转换(一般将内网转为公网上网)

iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -o ens34 -j SNAT
\--to-source 218.29.0.5

对于外网ip变化(拨号上网), 使用地址伪装

iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -o ens34 -j
MASQUERADE

DNAT转换(用于外网访问内网web)

iptables -t nat -A PREROUTING -i ens34 -d 218.29.0.5 -p tcp \--dport 80
-j DNAT \--to-destination 192.168.100.11:8080

3.5.6 SELinux

SELinux全称为安全增强式 Security-Enhanced Linux（SELinux）,
是一个在内核中实践的强制存取控制(MAC)安全性机制。

配置文件: /etc/selinux/config

查看状态: getenforce

设置selinux状态: setenforce 0\|1 关闭\|开启 永久有效改配置文件。

安全上下文: 用户:角色:类型 , 查看ls -Z

设置安全上下文: chcon\[-R\] \[-t type\] \[-u user\] \[-r role\] file

user: 指示登录系统的用户类型,如root,user_u,system_u

Role: 定义文件, 进程和用户的用途: 文件:object_r, 进程和用户: system_r

Type: 指定数据类型, public_content_t, sshd_key_t

Sensitivity: 限制访问的需要, 由组织定义的分层安全级别, 分0-15级, s0最低

Category: 对于特定组织划分不分层的分类, 如FBI Secret, NSA secret

3.5.7 TCP wrappers简单防火墙

1\) TCP wrappers是工作在第四层(传输层)的安全工具,
对有连接状态的TCP服务进行安全检测和实现访问控制,
凡是调用libwrap.io库文件的程序就可以接受TCP wrappers的安全控制,
常见的程序有rpcbind, vsftpd, sshd, telnet

ldd /usr/sbin/sshd \| grep libwrap.so 查看是否有调用libwrap.so文件

2\) TCP Wrappers配置: /etc/hosts.allow(白名单)和/etc/hosts.deny(黑名单),
allow是高优先级, 配置文件编写规则:

service_list@host: client_list (如: httpd,sshd@192.168.76.10:allow)

其中, service_list服务列表, 是程序服务列表, 多个时用','隔开;
\@host设置允许或禁止他人从自己的哪个网口进入, 不写代表全部;
client_list是访问者地址, 控制多个用户时使用','隔开,
包括基于IP的(192.168.88.1,
192.168.88.)、基于主机名的、基于网络/掩码(192.168.0.0/24),
基于内置ACL(ALL所有主机, LOCAL本地主机)

只允许一个IP远程登录: hosts.allow里sshd:ip ; 再hosts.deny里sshd:ALL

3.5.8 Ubuntu防火墙UFW

1\) 基本设置

ufw status 查看ufw状态, inactive\|active 关闭\|开启

ufw enable\|disable\|reset 启动\|关闭\|重置 ufw防火墙

ufw default allow\|deny \# 启动默认防御 放行对外联接\|阻止对外部联接

2\) 防火墙规则

ufw \[delete\] \[insert NUM\] allow\|deny \[in\|out\]
\[PORT\[/PROTOCOL\] \] \[comment COMMENT\]

delete: 删除这个规则

insert Num: 代表你要插入到防火墙规则的那个位置, 规则是有序排列的。

allow\|deny: 这条规则是允许的还是禁用的

in\|out: 这条规则对发送还是接收数据生效

PORT: 端口号

protocol: 协议, 例如TCP还是UDP

comment: 注释

实例: ufw insert 2 allow in 22/tcp 允许ssh的规则(端口22/TCP),
插入到位置2。

3\) ip规则

ufw \[delete\] \[insert NUM\] allow\|deny \[in\|out \[on INTERFACE\]\]
\[proto PROTOCOL\] \[from ADDRESS \[port PORT\]\] \[to ADDRESS \[port
PORT\]\] \[comment COMMENT\]

INTERFACE: 网卡, 如ens33

form ADDRESS: 源IP地址

to ADDRESS: 目标IP地址

proto PROTOCOL: 协议

port PORT: 端口号

实例: ufw allow proto tcp from 192.168.0.2 port 22

ufw allow from 192.168.0.2 port 80 to 192.168.0.2 port 8080

3.6 加密

3.6.1 文件加密

1\) gzexe加密

gzexe \[-d\] test.txt 加密并压缩文件, -d为解密; 该加密方式并不保险,
加密的可执行文件依然可以执行, 隐蔽脚本中的密码等信息,
注意加密后删除备份文件test.txt\~ 。

2\) tar加密

tar -zcf - /test/\* \| openssl des3 -salt -k 123 \| dd of=test.des3
将test目录下的所有文件进行加密, 注意加密后要删除源文件

dd if=test.txt.des3 \| openssl des3 -d -k 123 \| tar zxf - 解密文件

3\) openssl加密tar或gz

tar -czf - \* \| openssl enc -e -aes256 -out test.tar.gz
加密当前目录下的所有内容, 需要交互式输入密码。

openssl enc -d -aes256 -in test.tar.gz \| tar xz -C /test/
解压到test目录, 需要输入密码

4\) shc加密shell脚本

shc是将shell脚本转换为一个可执行的二进制文件。

下载: wget http://www.datsi.fi.upm.es/\~frosal/sources/shc-3.8.9.tgz ;
解压

安装: mkdir -p /usr/local/man/man1 ; make install

用法: shc -e 28/02/2018 -m \"this script file is about to expire\" -v -r
-f text.sh 其中: -e有效期, -m过期后输出信息, -v输出详情,
-r可知相同操作系统上执行, -f制定shell源文件。

注意: 系统会产生两个文件text.sh.x.c, text.sh.x,
其中text.sh.x为可执行文件。

5\) zip加密

zip -e \[-r\] test.txt.zip test.txt 加密文件或文件夹, 需要输入密码

unzip test.txt.zip 解密文件, 需要输入密码

3.6.2 GnuPG加密

GnuPG的全称是GNU隐私保护(GNU Privacy Guard), 注意:
gpg只能对文件进行加密, 对目录则无法完成加密。

生成密钥: gpg \--gen-key, 填写自己的信息

生成撤销证书: gpg -o revoke.asc \--gen-revoke uid
(生成密钥对后马上要生成)

列出密钥: gpg -k \| \--list-keys \| \--list-secret-keys \| \--list-sigs

删除密钥: gpg \--delete-secret-keys uid 先删私钥, gpg \--delete-keys uid
再删公钥

导出密钥: gpg \[-a\] -o pubkey \--export -r user\|uid //导出公钥,
-a以ascii格式显示

gpg \[-a\] -o subkey \--export-secret-keys -r user\|uid //导出私钥

导入密钥: gpg \--import \[密钥文件\] //导入密钥文件中的密钥

非对称加密: gpg -e \[-a \--trust-mode always\] -r user\|uid test.txt
其中test.txt.gpg为加密后的文件, -e表示加密,
-a表示以ascii格式输出尾缀.asc, -r表示指定用户或uid, \--trust-mode
always信任重其它主机导入的公钥。

解密: gpg \[\--pinentry-mode loopback \--batch \--passphrase string -o
test2\] -d test.txt.gpg 其中: -o表示解密后输出文件名,
-d表示需要解密的文件, \--batch免交互, \--passphrase从命令行读取密码,
\--pinentry-mode用户代理模式改为loopback(debian需要, 不然还是会交互)

echo \"124\" \| gpg \--batch \--passphrase-fd 0 -d test.txt.gpg
从管道输出中获取密码

gpg \--batch \--passphrase-file ./conf/passfile -d test.txt.gpg
从passfile文件第一行获取密码

对称加密: gpg \--symmetric test.txt (需要输入临时密码) ; 解密: gpg -d
test.gpg

数字签名: gpg -o test.sig -s test.txt test.sig包含源文件和签名

gpg -u \[发信者ID\] -r \[接收者ID\] -a -s -e test.txt 既签名又加密

文本签名: gpg -o doc.sig \--clearsign doc 源文件doc不变

分离式签名: gpg -o doc.sig \[-a\] -b doc 源文件和签名分开的

验证签名: gpg \--verify doc.sig \[doc\] 分离式签名需要加上源文件\[doc\],
注意这里验证需要使用对方的公钥, 注意加密后的验证是在解压的时候。

其它操作:

将公钥传至密钥服务器: gpg \--keyserver hkp://keys.gnupg.net (默认)
\--send-keys uid

搜索密钥服务器的公钥: gpg \--keyserver hkp://keys.gnupg.net
\--search-keys uid

从公钥服务器导入密钥: gpg \--keyserver hkp://keys.gnupg.net \--recv-keys
uid

密钥回收: 当该密钥对不在使用, 或私钥丢失, 需要回收。

导入回收证书: gpg \--import revoke.asc (上面生成的)

发送回收证书到服务器: gpg \--keyserver hkp://keys.gnupg.net \--send-keys
uid

注意事项:

1\) 注意产生的密钥文件在\~/.gnupg下

2\) 当使用gpg \--gen-key生成密钥对时, 到最后一步一直卡住不动。解决方法:
先yum -y install rng-tools, 然后运行rngd -r /dev/urandom ;
注意debian可能不能启动rng-tools服务, 但是没关系运行rngd -r /dev/urandom
; gpg \--list-keys查看;

不成功尝试mv /dev/random /dev/random.bak ; ln -s /dev/urandom
/dev/random

3\) 当解压时遇到gpg: cannot open \`/dev/tty\': No such device or
address错误, 只需要在解压时加上\--no-tty参数即可。

4\) No pinentry

dnf install pinentry

如果还不行, vim /etc/gnupg/gpg-agent.conf, 添加pinentry-program
/usr/bin/pinentry-curses

3.7 生产中遇见的问题

3.7.1 常见排错工具

1\) addr2line

addr2line是一个可将指令的地址和可执行映像转换为文件名、函数名和源代码行数的工具。当内核执行过程中出现崩溃时,
可用于快速定位出错的位置, 帮助找出bug。

用法: addr2line \[options\] addr \[addr \...\]

-a \--addresses: 在函数名、文件和行号信息之前, 显示地址,
以十六进制形式。

-b \--target=\<bfdname\>: 指定目标文件的格式为bfdname。

-e \--exe=\<executable\>: 指定需要转换地址的可执行文件名。

-i \--inlines: 如果需要转换的地址是一个内联函数,
则输出的信息包括其最近范围内的一个非内联函数的信息。

-j \--section=\<name\>: 给出的地址代表指定section的偏移, 而非绝对地址。

-p \--pretty-print: 使得该函数的输出信息更加人性化:
每一个地址的信息占一行。

-s \--basenames: 仅仅显示每个文件名的基址（即不显示文件的具体路径,
只显示文件名）。

-f \--functions: 在显示文件名、行号输出信息的同时显示函数名信息。

-C \--demangle\[=style\]: 将低级别的符号名解码为用户级别的名字。

-h \--help: 输出帮助信息。-v \--version: 输出版本号。

实列:

\[150100.451504\] traps: test\[7593\] trap divide error ip:4005f5
sp:7ffeebd4ba70 error:0 in test\[400000+1000\]

addr2line -e /dir/test -f -s -C 4005f5

3.7.2 segfault错误

产生原因: 可能是缺少某个配置文件或配置文件参数设置不当。

快速定位: 查看message或dmesg programName, 错误如示例:

\[11699990.110966\] nginx\[176208\]: segfault at 0 ip 0000000000457e81
sp 00007fffc9da3bc0 error 4 in nginx\[400000+437000\],
其中红色字体为出错位置。

快速定位错误位置: addr2line -e sbin/nginx 0000000000457e81
