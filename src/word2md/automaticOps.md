1.  git简介

分布式版本控制软件。

1.2 git基本操作

1.2.1 初始化仓库

git init 初始化本地仓库

git init \--bare docker.git 初始化远程仓库

1.2.2 创建用户

git config \--global user.name \"minj\"

git config \--global user.email \"mminjjie@gmail.com\"
只是起身份识别作用

git config \--list 查看

1.2.3 基本命令

添加文件到缓存区: git add test.txt

提交到本地仓库: git commit -m \"description\"

直接提交到本地仓库: git commit -a -m \"description\" test.txt

查看当前状态: git status

查看文件改变内容: git diff test.txt

查看历史记录: git log \[\--oneline \--graph\] (历史版本拓扑图)

查看历史版本号: git reflog

切换到历史版本: git checkout commit_id

查看某个文件更改历史和更改人: git blame test.txt

1.2.4 分支管理

创建分支: git branch develop

切换分支: git checkout \[-b\] develop 其中-b是同时创建分支

查看当前分支: git branch \[-r\|a\|show-ref\], -r查看远程分支,
-a同时显示本地分支, show-ref查看所有分支信息

删除分支: git branch -d develop

temp分支合并到当前分支: git merge temp ; git rebase

将远程分支dev合并到当前分支: git merge \--no-ff origin/dev

终止本次合并(回到merge前): git merge \--abort

1.2.5 撤销与回滚

未提交撤销修改: git checkout \-- .或test.txt (\'.\'丢弃所有修改,
test.txt丢弃这个文件修改)

回滚: git reset HEAD\^ \[test.txt\] #指针移到指定版本, 更改回撤到工作区,
默认\--mixed

git reset \--soft 6fcfc89 #指针移到指定版本, 更改回撤到暂存区

git reset \--hard 6fcfc89 #指针移到指定版本, 更改不保留

push后回滚: (1) git reset \--hard commit_id ; git push origin HEAD
\--force (不推荐使用)

\(2\) git revert -n commit_id
(将某次提交移动到头部，然后重新提交，并解决冲突,
注意提交前一定要查看内容是否正确)

1.2.6 远程管理

克隆远程项目: git clone \[-b dev\] git@github.com:XX.git //-b指定分支,
不知道clone所有

添加远程仓库: git remote add origin(仓库别名)
git@github.com:XX.git(github的ssh
url)，可以将本地公钥放到github上进行免密钥连接

查看本地远程仓库: git remote -v

修改远程仓库地址: git remote set-url origin git@github.com:XX.git

删除本地远程仓库: git remote rm origin

推送项目到远程仓库: git push \[-u\] origin 本地分支:远程分支
(第一次推送加-u)

删除远程仓库dev分支: git push origin -d dev

(1)将远程分支合并到本地:

比较本地的仓库和远程参考的区别: git log -p master origin/master

合并远程分支: git merge origin/master ; git rebase(统一合并分支)

(2)将远程分支合并到本地:

将远程master分支拉到本地放到temp分支: git fetch origin master:temp

比较master分支于temp分支差异: git diff temp (注意你在master分支)

合并分支: git merge temp ; git rebase

删除temp分支: git branch -d temp

\(3\) 拉取远程分支并合并\[到develop\]: git pull \[\--rebase\] origin
master\[:develop\]

1.2.7 git标签tag

列出所有标签: git tag

查看指定tag: git show tag_id

新建标签: git tag tag_id \[commit_id\] 其中commit_id是在指定位置打标签

删除本地标签: git tag -d tag_id

推送本地标签到远程仓库: git push origin tag_id

1.2.7 git子模块(submodule)

git clone \--recursive url 克隆主库时初始化子模块

git submodule update \--init \--recursive
已经克隆了主库但没初始化子模块, 下载子模块

git submodule update \--recursive
\--remote已初始化子模块，需要更新子模块(提交SHA码)

git submodule add url \[path/dirName \]
在一个库中添加一个子模块，默认添加到url同名目录下,
path/dirName自定义目录，添加记录在.gitmodules中

git diff \--cached \--submodule 查看子模块项目文件夹记录, 或者git diff
\--submodule

在代码开发中更新子模块: 进入子模块目录，用git fetch和git
merge来更新并提交。也以在主目录中用: git submodule update \[
\--recursive \--remote moduleName\]

注意: 每次git
pull远程项目，需要手动更新子模块，自动更新加\--recurse-submodule参数。\--recursive是子模块中还有子模块时必加。

如果子模块url发生改变, 先将新url复制到.gitmodules 中，然后运行git
submodule sync \--recursive，最后重新更新子模块git submodule update
\--init \--recursive

1.2.8 常见问题

1\) 当远程仓库和本地仓库不相干时，首次推送会报错。解决方法:

强行合并两个独立启动仓库的历史: git pull origin master
\--allow-unrelated-histories

然后再推送: git push origin master

2\) 注意远程推送只能推送相同分支，不能由master推到develop。

3\) 正常流程:
先在github上创建项目及相应分支，在本地初始化仓库并创建相应分支，然后在develop分支修改代码，在develop分支推送代码，在github上检查合并，在本地master下载代码，合并到develop分支，在develop分支修改......(实际开发中不一定是这样)

4\) 当使用http拉取和上传代码时，可以使用git config \--global
credential.helper
store记住用户名和密码，密码被保存在\~/.git-credentials文件里，注意该方式不安全。

5\)
合并冲突中\<\<\<HEAD表示主分支内容，\>\>\>dev表示dev分支内容，以===分割。

6\) 忽略某个目录或文件: vim .gitignore; 将要忽略得文件加入即可。

1.3 Git Hooks

Git
Hooks是定制化的脚本程序，它实现的功能与相应的git动作相关。脚本在.git/hooks目录下，只需要将相应文件的.sample尾缀去掉既可以使用。

二 Ansible

2.1 ansible基础

2.1.1 ansible配置

1\) 配置节点间通信

添加节点: /etc/ansible/hosts

\[node1\] #节点名

host1 ansible_ssh_host=xx ansible_ssh_port=x ansible_ssh_user=x
ansible_ssh_pass=\"x\"

x.x.x.x ansible_ssh_port=x ansible_ssh_user=x ansible_ssh_pass=\"x\"
#无主机别名

host2 ansible_ssh_host=192.168.1.32 ansible_ssh_port=22 #免密钥通信

测试: ansible node1 -m ping -vvvv

2.1.2 ansible命令

1\) 命令行

ansible node(节点) \[options\] \[command\] #命令分多个模块

options: -m module 指定模块; -a args 指定模块参数;

2\) 节点匹配

ansible all \| \* -m xx #所有主机

ansible node1 -m xx #匹配组

ansible host1:host2 \| node1:node2 -m xx #多主机匹配

ansible node1:!host2 \| node1:&node2 -m xx #排除host2 \|
node1和node2交集

ansible web\*.com:host1 -m xx #通配符匹配

2.2 命令行模块

2.2.1 命令模块

1\) command模块

ansible test1 -m command -a \"ls /xx\" #执行linux命令

参数: free_form 要执行的指令; chdir 切换到指定目录执行; creates
文件不存在创建; removes 文件存在则删除

2\) 常用模块

ansible all -m shell -a \'echo magedu \| passwd \--stdin user1\'
#复杂linux命令

ansible all -m script -a \'/tmp/test.sh\' #远程执行本地脚本

2.2.2 文件管理模块

1\) file模块

ansible test1 -m file -a \'dest=/path/xx mode=777\' #修改文件权限

其它参数: group属组; owner 属主; mode 权限; path\|dest\|name 指定文件;
recurse 递归设置; src 源文件; state文件状态(值:
directory目录不存在则创建; touch: 文件不存在则创建; absent:
删除文件或目录; link 符号链接)

2\) copy, fetch模块

ansible test1 -m copy -a \'src=/xx dest=/new\' #将文件拷贝到远程主机

参数: src 源文件; dest 目标路径; backup 覆盖前备份; owner, group,
mode属主组, 权限

ansible host1 -m fetch -a \"src=/xx dest=/root force=yes\"
#远程服务器文件拷贝至本机

3\) get_url模块

ansible test -m get_url -a \'url=https://xx.txt dest=/tmp\' #下载文件

2.2.3 服务管理模块

1\) cron模块

ansible test1 -m cron -a \'name=\"xx\" minute=\*/3 job=\"/bin/ls\"\'
#定时任务, 其它时间默认\*

参数: name任务描述; backup 对远程主机上原任务计划内容修改之前备份;
minute, hour, day, month, weekday分时天月周; job 要执行的文件; user
执行用户; sate任务计划是创建或删除(present, absent); special_time
指定执行时间(reboot, yearly, annually, monthly, ...)

2\) user, group模块

ansible test1 -m user -a \'name=www groups=www state=present\' #创建用户

参数: state 创建\|删除用户(present, absent); name 用户名; uid, gid
用户uid和gid; password密码; createhome 是否创建家目录; home 指定家目录;
groups 附加组; system为系统用户; shell 指定用户shell; remove删除家目录

3\) yum模块

ansible test -m yum -a \'state=present name=httpd\' #安装软件

参数: enablerepo 启动某个源; name 软件名\|url; state状态(present 安装,
absent 删除, latest 安装最新)

4\) service, systemd模块

ansible test -m service -a \'name=httpd state=started enabled=yes\'
#启动并设置开机启动

参数: name 服务名; enabled开机启动; runlevel 运行级别; arguments
服务参数; sleep 执行restart休眠时间; state执行started, stoped,
restarted, reloaded;

ansible test -m systemd -a \"name=httpd state=started\" #启动服务

参数: name 服务名, state 状态, enabled 开机, user 是否以调用者启动,
daemon_reload 重新加载守护进程

5\) 其它模块

ansible all -m setup #获取被管理节点详情

2.3 ansible部署

2.3.1 playbook基础

1\) playbook组织结构

inventory #指定主机(host文件); modules #指定调用模块; commands
#运行的命令

playbooks: tasks #任务; variable #变量; templates #模板; handlers
#处理器; roles #角色

2\) 运行: ansible-playbook test.yml

2.3.2 playbook脚本

\- hosts: test #指定主机

remote_user: root #执行任务用户

vars: #指定变量, ansible-playbook xx.yml \--extra-vars \"hosts=xx\"
#使用命令行传递变量

\- package: nginx #定义变量

tasks: #任务列表

\- shell: /usr/bin/foo #执行shell模块

register: result #将输出赋给result

ignore_errors: True #忽略错误

\- name: \'install\'

yum: name={{ package }} state=latest #调用变量

\- name: \'xxx\'

command: /xx

when: xx = \'xx\' 或 result\|failed 或 #条件判断

\- name: \'xx\'

user: name={{ item.name }} state=present groups={{ item.groups }}

with_items: #迭代

\- { name: \'x\', groups: \'xx\' }

\- { name: \'x\', groups: \'xx\' }

\- name: configuration file

copy: src=/path/xx.conf dest=/etc/x/xx.conf

tags: #标签, ansible-playbook test.yml \--tags \'conf\' \--skip-tags
\'test\'

\- conf #只执行该段代码

notify: #触发handlers

\- restart nginx

\- name: start service

systemd: enabled=true name={{ package }} state=started

handlers:

\- name: restart nginx #要和notify列表一致

systemd: name=nginx state=restarted

2.3.3 role配置

1\) role模式目录结构

/xx/xx.yml

/xx/roles

nginx/{ tasks/main.yml, handlers/main.yml, vars/main.yml, meta/main.yml,
files/\* }

mysql/{ ... } #同上

2\) xx.yml

\- hosts: node1

remote_user: root

pre_task: #前工作列表

\- name: xx ; ...

roles: #角色列表

\- nginx

post_task:

\- xx #后工作列表

3\) 角色列表

task/main.yml: - name: xx; \... #类playhook脚本

meta/main.yml: dependencies: - { role: mariadb,var1: \"xx \",when:
\"version == \'x\'\" } #依赖表

三 Jenkins

3.1 Jenkins简介

Jenkins是一款使用Java语言开发的一个可扩展的持续集成(CI)引擎。主要用于:
持续、自动地构建/测试软件项目，监控一些定时执行的任务。

硬件要求: 内存256MB\~1GB以上，磁盘: 10GB\~50GB以上。

持续集成(CI)是在源代码变更后自动检测、拉取、构建和(在大多数情况下)进行单元测试的过程。其目标是快速确保开发人员新提交的变更是好的，并且适合在代码库中进一步使用。CI的流程执行和理论实践让我们可以确定新代码和原有代码能否正确地集成在一起。

CD持续交付Continuous Delivery和持续部署Continuous
Deployment，持续交付是自动将已验证的代码发布到存储库，持续部署是自动将应用发布到生产环境

3.2 Jenkins pipeline

3.2.1 Jenkinsfile定义

pipeline {

agent none \| any 指定代理节点，在stage中也可指定

agent { label \'node1\' } or { label \'node1 && or \|\| node2\' }
指定具体代理节点

agent { docker { 指定docker代理节点

image \'mysql:8.1\'

label \'db\'

args \'-v /tmp:/tmp\' ......

} }

agent { dockerfile { 指定dockerfile代理节点

filename \'Dockerfile.build\'

dir \'build\'

label \'test\'

additionalBuildArgs \'\--build-arg version=1.0.2\'

args \'-v /tmp:/tmp\' ......

} }

agent { kubernetes { 指定k8s代理节点

label \'k8s_test\'

yaml \"\"\"

kind: pod

metadata:

\...\...

\"\"\" } }

triggers { 触发器

cron(\'\* \* \* \* \*\') 定期构建

pollSCM(\'H 8,12,18 \*/3 1-12 \*\') 定期扫描，如果有更新构建

upstream(upstreamProjects: \'job1,job2\', threshold:
hudson.model.Result.SUCCESS)

} 上一个项目关联构建

options { 选项

buildDiscarder logRotator(daysToKeepStr: \'3\', numToKeepStr: \'3\')
历史任务保留数

preserveStashes(buikdCount: 5) 保留已完成构建的存储，用于stage重新启动。

checkoutToSubdirectouy(\'foo\') 在工作区的子目录中执行源代码检出。

disableConcurrentBuilds() 不允许并发构建，用来防止同时访问共享资源等。

disableResume() 当master重启后部恢复流水线

newContainerPerStage() 当agent为docker时，每一个信stage都用新的容器

overrideIndexTriggers(true\|false)

quietPeriod(30) 静默时间，用于减轻构建压力管道生成前等待时间

retry(5) 重试次数

skipDefaultCheckout() 跳过源码自动检出功能

skipStagesAfterUnstable() 如果某个阶段流水线不稳定，不执行下面内容

timeout(time: 1, unit: \'HOURS\') 流水线一次执行超时设置，MINUTES

timestamps() 添加控制台输出时间戳

parallelsAlwaysFailFast() 并行构建时，有一个分支失败，停止所有构建

}

environment { 环境变量

TEST_USER = \'test_user\'

}

parameters { 参数，构建时需要手动输入

string(name: \'USER\', defaultValue: \'jenkins\', description: \'Whe
owns this file?\')

booleanParam(name: \'BUILD\', defaultValue: false, description: \'this
value\')

choice(name: \'NUM\', choices: \[\'One\', \'Two\'\], description: \'pick
num\')

password(name: \'PASSWORD\', defaultValue: \'\', description: \'Enter a
password\')

text(name: \'ssh_key\', defaultValue: \'\', description: \'the ssh
key\')

}

tools { 构建工具

maven \'apache-maven-3.0.1\'

}

stages {

stage(\'test\') {

agent { label \'test\' }

options { 适用于在stage中的4个option选项

skipDefaultCheckout()

timeout(time: 1, unit: \'HOURS\')

retry(3)

timestamps()

}

environment {

ACCESS_KEY = credentials(\'ssh_key_text\') 这里为配置时的凭据id

}

tools { \...\... }

steps { 注意每一个stage中只能有一个steps

sh \'echo \"\$TEST_USER\"\'

echo \"hello \${params.USER}\"

sh \'printenv\'

} }

stage(\'buid\') {

agent { label \'build\' }

input { 用户输入，一般在when之前。

message \'should we continue?\' 必须的说明参数

id \'build\' 为input的标记符，默认的标记符是这个stage的名称。

ok \'yes, we should\' 主要是在ok按钮上显示一些文本

submitter \'admin,xiao,zhang\' 能够输入的构建者列表

submitterParameter \'user\' 构建者标识为环境变量

parameters {

string(name: \'USER\', defaultValue: \'jenkins\', description: \'Whe
owns this file?\')

\...\...

} }

when {

branch \'master\'

expression { BRANXH_NAME ==\~ /(production\|staging)/ }

trigeredBy { \'SCMTrigger\'\|\'TimerTrigger\'\|\... }

tag \"build-\*\"

not { 条件为false时

environment name: \'USER\', value: \'xiao\'

}

allof \| anyof { 所有条件为真\|至少一个为真

\.....

}

beforeAgent\|beforeInput\|beforeOptions true 如果在input等之前需要标记

}

steps {

echo \"hello, \${USER}\"

script {

def var = \['a','b'\]

for (int i = 0 ; i \< var.size() ; ++i) {

echo "\${var\[i\]}"

} } } }

stage(\'check\') {

failFast true

parallel { 并行

stage(\'branch A\') {

agent { label \'for A\' }

steps {

echo \'branch A\'

} }

stage(\'branch B\') {

stages {

stage(\'test\') {

steps { echo \'test A\' }

}

stage(\'id\') {

steps { echo \'identified B\' }

} } } } }

stage(\'deploy\') {

agent { label \'deploy\' }

steps {

sh \'echo deploy\'

}

post { 执行后处理

always {

echo \'on deploy\'

} } } }

post { 执行后处理

always {}

failure\|success\|unsuccessful\|unstable

changed\|fixed\|regression\|aborted\|cleanup

} }

3.3.2 Jenkins内置变量

JOB_NAME 项目名称 ; BRANCH_NAME 多分支项目，分支项目名称

BUILD_NUMBER 目前构建的编号 ; BUILD_ID 当前构建版本ID

BUILD_DISPLAY_NAME 显示当前版本名称

NODE_NAME 和NODE_LABELS节点名和节点标签

WORKSPACE 当前job的工作目录 ; JENKINS_URL 为jenkins的url

BUILD_URL和JOB_URL 为当前build和job的url

STAGE_NAME 当前stage名, 既stage('name')

3.2.3 注意事项

1\) Jenkinsfile中agent { label 'test'
}数目不能超过test节点规定的执行器数目，不然会一直等待不继续执行，解决方法是增加节点执行器数量。

2\) jenkins初始化时一直停在Please wait while Jenkins is getting ready to
work......

进入jenkins工作目录，编辑hudson.model.UpdateCenter.xml，将url中https://updates.jenkins.io/update-center.json中的https改为http，或者是直接整个替换为https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

3.3 Jenkins管理

3.3.1 Jenkins配置github

Jenkins需要安装github插件。

1\) 进入Github \-- settings \-- Developer settings \-- Personal access
tokens \-- Generate new token，填写Note (为token名)，勾选repo,
admin:repo_hook , 确认Generate token，记住token值(该值只显示一次)。

2\) 进入jenkins \-- global setting \-- GitHub \-- 添加GitHub server \--
添加Credentials (选择Secret text, Secret填刚刚的Token, ID, 描述) \--
advanced (勾选Hook URL，注意jenkins的网址能够全网访问，不然无效) \--
复制该地址

3\) 进入github的具体项目中，Settings(项目中) \-- Webhooks \-- Add
webhooks \-- Payload URL(为上一步Hook URL) \-- 选择触发器(trigger) \--
add webhook

4\) 创建jenkins项目，选择GitHub hook trigger for GITScm polling \-- SCM
\--
git。注意这里的Credentials可以时github仓库的用户名和密码；也可以先将服务器公钥拷入github，私钥保存至jenkins中的用户。

5\) 第一次需要手动构建。

3.3.2 Jenkins配置Phabricator

1\) 进入Phabricator，先创建一个创建一个bot账号 ，然后生成token: 编辑设置
\-- Conduit API令牌 \-- 添加令牌 \-- 复制token

2\) 进入jenkins \-- 安装Phabricator插件 \-- 进入global settings \--
Phabricator \-- 添加Credentials

3\) 构建项目，构建SCM的git项目，这里的凭据是Phabricator \-- 编辑设置 \--
SSH公钥 \-- SSH密钥操作 \-- 生产ssh密钥 \-- 将私钥拷入jenkins。

3.3.3 Jenkins权限管理

安装Role-Based Authorization
Strategy插件，在全局安全配置的授权策略中选择: role-based
strategy，在jenkins管理页面进入manage and assign roles，进入manage
roles。

保存，进入Assign Roles。注意这里的权限以全局角色为主，item为辅。

注意: 千万不要把admin给删了。万一删了补救措施:
进入jenkins家目录，编辑config.xml文件，将\<useSecurity\>true\</useSecurity\>
替换为\<useSecurity\>false\</useSecurity\>
，重启jenkins，登陆赋予admin权限，安全域也改回来。

3.3.4 jenkins远程触发构建

可能需要安装插件: Parameterized Trigger和 Parameterized Remote Trigger

进入项目 \-- config \-- Trigger builds remotely (填token name)

远程触发构建:

curl -u user:password
"https://jenkins.forudev.com/job/JOB_Name/build?token=tokenName"

带参数构建:

curl -u user:password "https://jenkins.forudev.com/job/JOB_Name/
buildWithParameters?token=tokenName&DEPLOY=test&BRANCE=master"

或用户管理 \-- 进入用于部署的用户 \-- 配置 \-- API TOKEN

curl -X POST
\"https://deploy:11a0140a76dd492c682452acb92@www.jenkins.com/\...\"

注意后面的url一定要用引号引起来，不然可能得不到想要的结果。

3.3.4 jenkins消息通知

1\) rocket

安装插件: RocketChat Notifier

系统管理 \-- 系统设置 \-- Global RocketChat \-- rocket server
url(<https://chat.xxx.com:443>) \-- login user and password \--
channel(不能设置为只读) \-- Trust Server Certificate(如果是ssl连接)

在jenkinsfile中配置: 一般在post部分发送

rocketSend \"
\${env.JOB_NAME}-\${env.BUILD_NUMBER}-(\<\${env.BUILD_URL}\|Open\>)\"

rocketSend channel: \'general\', emoji: \':sob:\', message: "My
\${BUILD_URL}", rawMessage: true

3.4 搭建Jenkins集群

3.4.1 使用docker快速搭建jenkins集群

节点配置: master: 192.168.100.17，node1: 192.168.100.16，node2:
192.168.100.15

关闭selinux，配置防火墙，安装docker

部署master节点jenkins: 使用docker部署

运行jenkins，安装相关插件，添加节点。

填写节点名子、远程工作目录、标签、用法、启动方式(java web启动)，保存。

进入刚刚添加的节点，执行提示命令即可添加物理节点。

使用docker添加node节点，登陆node1节点，将agent.jar文件下载到/usr/local/jenkins，制定dockerfile文件:

FROM openjdk:8-jdk

ENV TINI_VERSION v0.19.0

RUN curl -fsSL
https://github.com/krallin/tini/releases/download/\${TINI_VERSION}/tini
\\

-o /usr/bin/tini && chmod +x /usr/bin/tini

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install \--assume-yes sshpass

ENTRYPOINT \[\"/usr/bin/tini\", \"\--\"\]

CMD \[\"bash\"\]

构建镜像docker build -t jenkins_node:v1 . ，部署jenkins:

docker run -u root -d \--restart=always \--name jenkins_node \\

-v /usr/local/jenkins:/usr/local/Jenkins jenkins_node:v1 \\

java -jar /usr/local/jenkins/agent.jar \\

-jnlpUrl http://192.168.133.131:8080/computer/agent1/slave-agent.jnlp \\

-secret 44c3e8d1531754b8655b53294bbde6dd99b3aaa91a250092d0d3425534ae1058
\\

-workDir \"/usr/local/jenkins\"
后半部分为系统master提示命令，注意更改目录

node1节点配置完毕，node2节点同理。

3.4.2 使用ssh添加jenkins节点

先安装SSH Build Agents和SSH Agent插件。

将master节点的用户的id_rsa加入到Credentials中，将master公钥拷入各node上。

在个node节点上安装java。

添加节点:
名称，描述，执行器数量，工作目录，标签，用法，启动方式(ssh)，主机(node
ip)，Credentials(master id_rsa)，受信策略(manually trusted key
Verification
strategy)，高级，端口，java_home(.../jdk/bin/java)，连接超时，重试次数，重试间隔，可用性，环境变量(JAVA_HOME)。

四 Gitbook

4.1 官方gitbook

4.1.1 gitbook简介

1\) gitbook

Gitbook是一个使用git和markdown来构建书籍的工具, 其支持多种输出格式: pdf,
ePub, mobi或静态网页。

2\) gitbook相关网站

官网: www.gitbook.com

书籍创建和托管平台: https://app.gitbook.com/ ;
推荐使用github账号登录注册

使用文档: https://docs.gitbook.com/

4.1.2 托管平台使用

1\) 基本设置

Settings - Personal info - USERNAME 是访问书籍链接的前缀, 如:
jieem.gitbook.com

2\) Space设置

创建一个space即可开始编写书籍, 内容编写方法较简单不再复述。

左侧导航栏:

Edits 编辑状态栏, 每次修改后save, 会先保存到drafts(草稿),
然后Merge后才会真正保存到书籍。可通过Merged回滚到历史版本。Archived归档,
用于处理错误编辑。

Activites 编辑历史, 记录你每次的修改的动作。

Share 共享设置, 通过Shareable Link链接访问该书籍,
链接前缀就是上文的username。

Design 设置书本风格。

Teams 书籍的团队管理

Integrations 插件管理, 这里可以设置与github的同步

Analytics 分析

Advance 高级设置, Space URL设置书籍链接url, Custom
Domain设置自定义域名。

3\) 使用技巧

a\) 书籍结构

如果本书由多个话题组成, 使用New Group分组。

![](media/image1.png){width="2.8604068241469816in"
height="1.0327909011373577in"}

b\) 多语言

如果你需要为本书创建多语言版本, 需要通过New
variant来创建不同分支。创建好后通过顶部区域选择你要编辑的分支来编辑不同版本

![](media/image2.png){width="2.4571741032370955in"
height="0.7531856955380577in"}![](media/image3.png){width="1.4593580489938758in"
height="0.7894367891513561in"}![](media/image4.png){width="1.1826312335958005in"
height="0.7788768591426072in"}

4.2 **自**建gitbook

4.2.1 gitbook安装

npm install gitbook-cli -g 或 yarn global add gitbook-cli

查看是否安装成功: gitbook -V

注意: 如果运行报错if (cb) cb.apply(this,
arguments)，编辑.npm/lib/node_modules/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js文件,
将62-64行注释

4.2.2 gitbook常用命令

gitbook init //初始化一本书

gitbook serve //

gitbook build //创建静态网页

4.2.3 书籍目录结构

1\) master分支 (其它语言版本另建分支)

mybook 根目录

README.md 当前的起始页

SUMMARY.md 目录文件

web 分组目录

1.1-di-yi-zhang 分章目录

README.md 章首页

1.1.1-jie-shao.md 小节内容

1.1.2-xiao-jie.md 小节内容

1.2-di-er-zhang 分章目录

css 分组目录

2\) SUMMARY.md 目录文件

\# Table of contents

\* \[web开发笔记\](README.md)

\## 一 WEB \<a id=\"web\"\>\</a\>

\* \[1.1 第一章\](web/1.1-di-yi-zhang/README.md)

\* \[1.1.1 介绍\](web/1.1-di-yi-zhang/1.1.1-jie-shao.md)

\* \[1.1.2 小节\](web/1.1-di-yi-zhang/1.1.2-xiao-jie.md)

\* \[1.2 第二章\](web/1.2-di-er-zhang/README.md)

\## 二 CSS \<a id=\"css\"\>\</a\>

3\) XX.md 内容文件

\-\--

description: 章节目录下的描述部分

\-\--

\# 1.1 第一章 目录名

内容

4.2 markdown语法

4.2.1 文本内容

1\) 普通文本

普通文件即是正常文本内容，换行: 回车+空行

\> 引用

字体: \*\*粗体\*\*， \_斜体\_， \~\~删除线\~\~

2\) 特殊文本

特殊文本需要转义符: \\\` , \\#, \\\*, \\(, \\), \\\_, \\\[, \\\], &lt,
&gt

4.2.2 标题

\# 大标题(章节名)，## 一号标题(h1), \### 二号标题(h2), \####
三号标题(h3)

4.2.3 列表

无序列表: \* one, \* two

有序列表: 1. one, 2. two

可选列表: \* \[x\] one(被选中), \* \[ \] two(未被选中)

4.2.4 代码块(保留原格式)

\`\`\`go \| bash \| javascript \| markup(html) \| css \| css

package main

import \"fmt\" //注释

\`\`\`

4.2.5 表格

\| 标题 \| 内容 \| 方法 \|

\| :\-\-- \| :\-\-- \| :\-\-- \|

\| 一 \| 二 \| 三 \|

4.2.6 标签和提示

{% hint style=\"info\" %}

这是一个提示

{% endhint %}

{% tabs %}

{% tabs title=\"one\" %}

普通内容

{% endtab %}

{% tabs title=\"代码\" %}

\`\`\`go

package main

\`\`\`

{% endtab %}

{% endtabs %}

4.2.7 链接

{% page-ref page=\"1.2.1-jie-xuan.md\" %} 章节跳转

!\[[Fig tu]{.underline}\](../../.gitbook/assets/fig1.1_tu.jpg) 图片

{% file src=\"../../.gitbook/assets/file1.1\\\_nginx.conf\" %} 文件链接

\[web\](https://dev.web/) 链接, 被链接web

4.2.8 其它

\$\$ 数学公式

a = 2 \* c

\$\$
