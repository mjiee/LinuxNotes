1.1.1 数据库介绍

数据库: 存储数据的仓库,
是长期存放的在计算机内、有组织、可共享的大量数据的集合。数据库中的数据按照一定的数据模型组织、描述和存储,
具有较小的冗余度, 较高的独立性和易扩展性,
并为各种用户共享。其具有如下特点: 数据结构化; 数据的共享性高, 冗余度低,
易扩展; 数据独立性高; 数据由DBMS统一管理和控制(安全性、完整性、并发控)

数据库系统(database management system, DBMS):
是一种操纵和管理数据库的大型软件,
用于建立、使用和维护数据库。它对数据库进行统一的管理和控制,
以保证数据库的安全和完整性。

关系型数据库: 关系数据库是建立在关系模型基础上的数据库,
借助于集合代数等数学概念和方法来处理数据库中的数据。关系型数据库是由多张能够互联的二维行列表格组成的数据库。

关系模型就是指二维表格模型,
一个关系型数据库就是由二维表及其之间的联系组成的一个数据组织。当前流行的有Oracle、DB2、Microsoft
SQL Server、MySQL等。

1.1.2 mysql启动连接

1\) 初始化配置

mysqld \--help \--verbose \| grep my.cnf 查看配置文件读取顺序,
重复配置以后面的为准

如果启动时加入了\--defaults-file=xxxx时, 以上的所有文件都不会读取.

2\) 配置文件书写

+----------------------------------+-----------------------------------+
| 服务器端标签:                    | 客户端标签:                       |
|                                  |                                   |
| \[mysqld\]                       | \[mysql\]                         |
|                                  |                                   |
| \[mysqld_safe\]                  | \[mysqldump\]                     |
|                                  |                                   |
| \[server\]                       | \[client\]                        |
+==================================+===================================+
+----------------------------------+-----------------------------------+

3\) mysql 启动

常规: 将support-files/mysql.server文件加入systemctl管理

mysql启动: support-files/mysql.server → /bin/mysqld_safe → /bin/mysqld

关闭数据库: 进入数据库, shutdown或mysqladmin -uroot -ppassword shutdown

4\) mysql连接管理

本地连接:

  -----------------------------------------------------------------------
  -u 用户名                           -p 密码
  ----------------------------------- -----------------------------------
  -S 本地socket文件位置               -h 数据库ip地址

  -P 数据库端口号                     -e 免交互执行数据库命令

  \< /dir/data.sql 导入sql脚本        
  -----------------------------------------------------------------------

设置密码: mysqladmin -uroot -poldpasswd password 123456

登录: mysql -uusername -ppasswd \[-h hostname\|ip\] -P port -S
/tmp/mysql.sock

退出: exit ; 或ctrl+d

查看连接线程: show processlist ;

1.1.3 mysql用户管理

1\) 用户查询:

describe mysql.user ;

select user,host,authentication_string from mysql.user ;

3\) 创建登录用户:

create user zhangsan@\'192.168.100.%\' identified by \'123456\';

4\) 修改用户信息:

alter user zhangsan@\'192.168.100.0\' identified by \'huohuo\' ;

rename user zhangsan@\'192.168.100.0\' to zhangsan@\'localhost\' ;

root找回自己密码:

关闭数据库, 使用安全模式登录:

mysql_safe \--skip-grant-tables \--skip-networking & (只能本地登录)

免用户登录, 执行flush privileges ; (手工加载授权表)

alter user root@\'localhost\' identified by \'123\' ;

正常重启数据库, systemctl restart mysql.server

5\) 删除用户:

drop user minj@'192.168.100.%' ;

1.1.4 mysql用户权限管理

1\) 查看权限说明: show privileges ;

查询用户权限: show grants for zhangsan@'192.168.100.%' ;

通过属性查权限: select \* from mysql.user\\G (基于全局授权); select \*
from mysql.db\|tables\\G (基于库\|表的授权)

2\) 设置权限:

> grant 权限1,...,权限n on 对象 to zhangsan@\'ip\' ;
>
> 权限:

ALL(管理员, 不包括grant option) ;

权限1,...,权限n(其它用户) ;

Grant option (给别的用户授权): grant 权1,... on 对象 to zhang@'ip' with
grant option

对象范围: 库, 表

\*.\* 相当于chmod -R 755 / , 全局库授权, 管理员

database.\* 相当于chmod -R 755 /database , 给database库授权, 普通用户

database.table1 相当于chmod -R 755 /database/table1 ,
给database库中的table1表授权

database.table1(name) 将权限授权到某一列

3\) 回收权限

revoke 权限 on 对象 from minj@'ip' ;

注意: a) 授权后推荐使用flush privileges刷新授权表

b\) mysql不能像linux一样重复授权

4\) mysql权限列表

  -----------------------------------------------------------------------
  create 数据库、表、索引 创建        drop 数据库,表,视图 删除
  ----------------------------------- -----------------------------------
  alter 表 修改表结构                 insert 表,列 插入行

  update 表,列 更新行                 delete 表 删除行

  select 表,列 选择行                 index 表 创建/删除索引

  show databases 服务器               process 服务器 查看线程信息
  查看数据库名称                      

  create temporary tables 表          create view 视图 创建视图
  创建临时表                          

  show view 视图 查看视图             super 服务器 超级权限

  all 服务器 所有权限                 shutdown 服务器 关闭服务

  reload 服务器 允许使用flush语句     grant option 库,表,存储 授权

  lock tables 数据库 锁表             event 数据库
                                      创建/更改/删除/查看事件

  create user 服务器 创建用户         create tablespace 服务器
                                      创建表空间/日志

  replication client 服务器           replication slave 服务器 主从复制
  允许二进制日志                      

  create routine 存储过程             alter routine 存储过程
  创建存储过程                        修改/删除存储过程

  execute 存储过程 执行               references 数据库,表 外键约束的父表

  file 服务器 文件操作                trigger 表 触发器

  proxy 服务器 代理成为其它用户       usage 服务器 没有权限
  -----------------------------------------------------------------------

5\) 推荐权限设置:

只读: SELECT, LOCK TABLES, SHOW VIEW \[, PROCESS, REPLICATION SLAVE,
REPLICATION CLIENT\] 后面三个是全局时用

读写(开发): SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, REFERENCES,
INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE
VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER \[,
PROCESS, REPLICATION SLAVE, REPLICATION CLIENT\]

仅DDL: CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES,
CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE \[, PROCESS,
REPLICATION SLAVE, REPLICATION CLIENT\]

仅DML(开发): SELECT, INSERT, UPDATE, DELETE, CREATE TEMPORARY TABLES,
LOCK TABLES, EXECUTE, SHOW VIEW, EVENT, TRIGGER \[, PROCESS, REPLICATION
SLAVE, REPLICATION CLIENT\]

1.2 SQL语法

1.2.1 SQL语句基础

1\) sql帮助: help contents ;

select @@sql_mode ; 规范sql语句书写规范

字符集: utf8, utf8mb4(mysql8默认), 建库时指定: create database dbName
charset utf8mb4;

查看: show create database dbName;

校对规则: show collation ; 影响排序

存储引擎: InnoDB(默认)

sql注释: /\* ...... \*/

2\) 数据类型

整数: tinyint, int(size), bigint(size); size可省略

浮点数: float(size, d), double(size, d), decimal(n,m) (整数位,小数位)

字符串: char(64) 定长字符串, 0-255bytes;

varchar(255) 变长字符串, 0-65535bytes

text 长文本, 0-65535 bytes

enum('bj','sh') 枚举类型, 从定义好的值中选一个;

set(\'a\',\'b\',\'c\',...) 集合类型, 只能从set中选(可以选多个)

时间类型: data(年月日); datetime(占用8字节, 年月日时刻);
timestamp(占用4字节)

布尔类型: bool;

3\) 约束和属性

  -----------------------------------------------------------------------
  primary key(主键约束) 唯一且非空,   
  每张表只有一个主键                  
  ----------------------------------- -----------------------------------
  not null(非空约束) 非空             unique key(唯一约束) 不能重复

  unsigned 针对数字列, 非负数         default 默认值

  auto_increment 自增长,              comment 注释
  针对数字列(\>0)                     

  references外键约束(其它表主键)      check 条件检测
  -----------------------------------------------------------------------

注意: a) 主键约束和非空、唯一约束不能同时出现, 自增和非负不能同时出现。

b\) 外键约束列必须与父表关联列一致。删数据: 子表再父表; 插入数据:
先父表再子表; 更新父表子表自动更新。只有数据小且用户少场景使用外键。

4\) client命令

\\c 类似于ctrl+C ; \\G格式化输出

source /dir/script 导入脚本, 也可用于导入数据。

1.2.2 DDL

DDL(数据定义语言)

1\) 库定义:

创建库: create database school charset utf8mb4 ;

查库: show databases ; 或 show create database school ;

需改库: alter database school charset utf8mb4 ; 注意从小往大改

删库: drop database school ;

2\) 表定义:

创建表: use school ;

a\) CREATE TABLE student(

id INT PRIMARY KEY AUTO_INCREMENT COMMENT \'学号\',

name VARCHAR(255) NOT NULL COMMENT \'姓名\',

age TINYINT UNSIGNED NOT NULL CHECK (age\>=18) COMMENT \'年龄\',

sgender ENUM(\'m\',\'f\',\'n\') NOT NULL DEFAULT \'n\' COMMENT \'性别\'
,

sfz CHAR(18) NOT NULL UNIQUE KEY COMMENT \'身份证\',

tId INT REFERENCES teacher(id) COMMENT \'班主任id\',

intime TIMESTAMP NOT NULL DEFAULT NOW() COMMENT \'入学时间\'

) ENGINE=INNODB CHARSET=utf8mb4 COMMENT \'学生表\';

b\) create table teacher as select \* from student where ...;
从旧表中导入到新表

查询表: show tables; describe student; show create table student;

修改表: alter table student(表名) add qq(列名) int comment '添加'
\[after 列名 \| first\] ;

alter table student(表名) modify qq(列名) char(24) comment '修改属性' ;

alter table student change qq(旧列名) chat(新列名) char(24) comment
'改列名' ;

alter table student drop qq(列名) ; 删除列名

alter table student engine=innodb ; 直接修改表属性

删表: drop table student ;

修改表名: alter table oldtable rename to newtable;

3\) 定义视图

create view view_name as select id, name from student where ...;
#创建视图(存储结果集)

create or replace view view_name as select id, name from student where
...; #更新视图

select \* from view_name; #查询视图

drop view view_name; #删除视图

注意: DDL语句对表进行操作时, 要锁定\"元数据表\"。此时,
所有修改类的命令无法正常运行。所以对业务繁忙的表要谨慎。

1.2.3 DCL和DML

1\) DCL(数据控制语言)

grant, revoke

2\) DML(数据操作语言)

insert插入: insert into student(id, name, ...) values(1, 'min', ...),
(2, 'xiao', ...) ;

update更新: update student set name='zhang', age=18 where id=1 ;
#必须加where

update student set name=replace(name, 'old', 'new') \[where id=1\]; 替换

update student set name=\"\"; 清空name列的值

delete删除行: delete from student where id=2 \[limit=50\]; #必须加where,
建议加limit

伪删除: alter table student add state(添加一个状态列) tinyint not null
default 1 ;

update student set state=0 where id=1 ;

select \* from student where state=1 ;

清空表中数据: truncate table student ; 从物理层次删除全表数据,
磁盘空间立即释放。

1.2.4 DQL

DQL数据查询语言, 属于DML

1\) select单独使用(mysql独有):

select 内置函数: select now(); select database(); select user() ;

select n1\*n2; 直接做计算

select @@port\|datadir\|socket; 查询设置的参数, show variables;
显示所要参数, show variable like '%trx%'; 匹配模式查询

2\) select标准用法

select语句用于查询数据库中的数据, 并输出数据集。

select 语句默认顺序: from→where→group by→select_list→having→order
by→limit

  -----------------------------------------------------------------------
  from table1 table2 ...                where 过滤条件1 过滤条件2 ...
  ------------------------------------- ---------------------------------
  group by 条件列1 条件列2 ...          select_list 列名

  having 过滤条件1 过滤条件2 ...        order by 条件列1 条件列2 ...

  limit 限制                            
  -----------------------------------------------------------------------

a\) where

select id \[as A\], name \[as B\] from student ; #查询数据, as
A是自定义列别名

select \* from student where id=\[\|\>\|\<\|\>=\|\<=\|\<\>\]n ;
#范围查询

select \* from student where id\>2 and\|or (state=1 or state=2) ;
#逻辑关系

select \* from student where id \[not\] between 2 and 5; #id在2和5之间,
同id\>=2 and id\<=5

select \* from student where age \[not\] in (18,22); #年龄为18或22岁,
not是不包含

select \* from student where id in (select ...); #多重select查询

select \* from student where score is null\|\[is not null\];
#null字段测试(生产环境尽量不用null)

select \* from student where name \[not\] like 'min%'; #模糊查询,
\'%\'表示任意个字符, \'\_\'表示任意一个字符, \'\[a-z\]\'任意一个字母,
\'\[\^a-c\]\'除了a-c的字母, 通配符不要放在开头(不走索引)

select name from student where exists (select name from teacher where
student.tid = teacher.id and teacher.id=2);
#用于测试子查询语句是否返回记录

select name from student where sid = any\|all (select sId from teacher
where age = 10); #子查询处理, any子查询中只要有age=10就为true,
all子查询用所有记录为age=10就为true。

b\) group by:

select name, age from student group by age; #分组

c\) having

select age,count(id),... from student group by age having count(id)\>2;
#用于后过滤

d\) order by:

select name from student order by age \[asc(升)\], id \[desc(降)\];
#先按age排, 相同排id

e\) limit:

select name,age from student order by age limit n\|n1,n2;
#显示n行\|跳过n1行显示n2行

f\) 其它select

select \* into newTable \[in otherDB\] from student where id\>10;
#将查询结果插入到新表或其它库中, 该表自动创建

insert into newTable(name, ...) select name, ... from student;
#将查询结果插入到一个已存在的表中, 注意字段属性要一致。

select name,age from student into outfile \'/tmp/mysql/bak.sql\';
#结果集导出, 需要在my.cnf中加入secure_file_priv=\'/tmp/mysql\',
不然没有权限。

3\) select多表连接查询

笛卡尔乘积连接: select \*\|id,name from table1,table2,... ;
#返回所有组合

内连接:

select \* from table1 \[A\]

join table2 \[B\] on table1.id=table2.id and table1.id\>n
\[\|A.id=B.id\]

join table3 on table3.id=table2.id and table3.var=\'value\'

where ......; #将table1和table3关联(table1-table2, table2-table3)。

外连接: 一般用于强制驱动表方式, 将小表设置为驱动表

左外连接:

select table1.name,table2.age from table1

left join table2 on table1.id=table2.id and table1.id\>2;
#显示所有table1和相关联的table2, 左表为驱动表

右外连接:

select table1.name,table2.age from table1 right join

table2 on table1.id=table2.id and table1.id\>2;
#显示所有table2和相关联的table1, 右表为驱动表

4\) 去重复:

结果集去重: select distinct(age) from student ;

聚合结果, 去重复: select name from student where age=23 union select
name from student where age=24 ......;

聚合结果, 不去重复: select name from student where age=23 union all
select name from student where age=24;

1.2.5 mysql条件语句

1\) case语句

select name,

case

when age\>18 and age=18 then \'adult \' #类似if-else语句,
可以有多个when语句

else \'Minor \'

end as age

from student;

2\) if语句

if(command, value1, value2); 如果command为真且不为0和null, 返回value1

ifnull(value1,value2); 如果value1为空, 返回value2

3\) 复杂查询(CTE)

WITH t1 AS (SELECT sno FROM student),t2 AS (SELECT cno FROM sc)

SELECT t1.\*, t2.\* FROM t1, t2 ;

1.2.6 mysql函数

1\) select常用函数

  ---------------------------------------------------------------------------------------------
  max() 最大值                      min() 最小值       avg()平均值           count() 统计结果集
  --------------------------------- ------------------ --------------------- ------------------
  sum() 结果集求和                  concat()           group_concat(name)    
                                    组合结果集         列转行(order by)      

  truncate(x, y) x保留到小数点后y位                    now()                 
                                                       获取当前日期和时间    

  curdate() 获取当前日期                               curtime()             
                                                       获取当前时间          

  length(s) 返回s长度                                  ucase(s)\|lcase(s)    
                                                       将s中字母大写\|小写   

  replace(s ,\'old\' ,\'new\')                         instr(s, \'sub\')     
  将s中old替换为new                                    返回s中sub位置        

  trim(s)\|ltrim(s)\|rtrim(s)                          left(s, n)            
  去除s空格                                            返回s的前n个字符      

  substring(s, n, len)                                                       
  从s的第n个开始取长为len的字符串                                            
  ---------------------------------------------------------------------------------------------

如: select concat(name, \'+\', age, \'-\') as A from student;
#自己组合结果集

注意: 复合函数也可以和其它语法共用。

2\) 存储过程

3\) 自定义函数

创建函数:

delimiter \$\$ #修改定界符

create function myFunc(sId int\[, sName char(255)\]) #可以有多个参数

returns varchar(50)

begin

declare stdName varchar(50); #声明变量

select name stdName varchar from student where sId = sName;

return stdName; #返回值

end \$\$

delimiter ; #改回界定符

调用函数: select myFunc(2\[, \"chi\"\])

删除函数: drop function \[if exists\] myFunc

1.2.7 常用show语句

show databases; show tables; show tables from school;

show processlist; show full processlist; 查询连接线程

show charset; show collation; show engines;

show privileges; show grants for user@'ip';

show create database school; show create table student;

show index from table\\G ; 查看索引

show engine innodb status \\G; 引擎状态

show status like \'school\' \\G; 数据库状态

show table status like \'student\' \\G; 表状态

show variables \[like \'%id%\'\] ; 查看数据库参数

show binary logs ; 查看所有二进制文件信息

show master status 查询二进制日志的位置点信息

show binlog events in mysql_bin.000004 查询二进制日志事件

show slave status 查询从库状态信息

show relaylog events in 查看中继日志事件

1.3 mysql管理

1.3.1 mysql元数据

元数据库: information_scheam

SCHEMATA表: 提供了当前mysql实例中所有数据库的信息

  -----------------------------------------------------------------------
  schema_name 数据库名                  
  ------------------------------------- ---------------------------------

  -----------------------------------------------------------------------

TABLES表: 提供了关于数据库中的表的信息(包括视图)

  -----------------------------------------------------------------------
  table_schema 库名                     table_name 表名
  ------------------------------------- ---------------------------------
  engine 引擎                           table_rows 表行数

  avg_row_length 表平均行长度           index_length 索引占用空间

  data_length 表使用空间大小            data_free 表中是否有碎片
  -----------------------------------------------------------------------

COLUMNS表: 提供了表中的列信息

STATISTICS表: 提供了关于表索引的信息

USER_PRIVILEGES表: (用户权限)给出了关于全程权限的信息

注意: a) 查询时要忽略sys, mysql, information_schema,
performance_schema四个系统库

1.3.2 mysql索引

1\) 索引简介

a\) 提供了类似于书中目录的作用,目的是为了优化查询

种类: B树索引, Hash索引, R树, Full text, GIS

BTREE种类: B-tree、B+Tree(在范围查询方面提供了更好的性能(\> \< \>= \<=
like))、B\*Tree

b\) B+Tree构建过程

聚簇索引: 建表时指定主键列,
innodb会自动将主键列作为聚集索引。如果没有主键,会选择唯一键作为聚集索引。聚集索引必须在建表时才有意义,一般是表的无关列(ID)

辅助索引: 使用普通列作为条件构建的索引,
需要人为按需创建。优化非聚簇索引的检索过程。可分为单列索引、联合索引、前缀索引

单列索引:

联合索引: 按建立索引顺序来创建搜索条件, 不然不走。

前缀索引: 选取大字段的前面部分作为索引条件,
防止索引树高度增高。MySQL索引树建议3-4层。

c\) B+tree索引树高度影响: 数据量级, 解决方法: 分表,分库,分布式;
索引列值过长, 解决方法: 前缀索引; 数据类型: 用varchar, enum

2\) 索引管理命令

按照业务要求建立合适的索引。将索引建立在经常用的条件列上。

查询索引:

describe student ; 其中的key: PRI聚簇索引, MUL辅助索引, UNI唯一索引

show index from student ; 显示详细索引情况

建立索引:

alter table student add index idx_name(name); #单列索引

alter table student add index idx_name(name, age); #联合索引

alter table student add index idx_name(name(5)); #前缀索引

create index idx_name on student (name, ...); #适用所有sql版本

删除索引: alter table student drop index idx_na ;

隐藏\|取消隐藏索引: alter table student index inx_na invisible\|visible
; 用于测试(mysql8)

3\) MySQL执行计划

语句执行前,先看执行计划信息,可以有效的防止性能较差的语句带来的性能问题.

查看执行计划: explain\|describe select \* from student \\G

table此次查询涉及的表, 针对多表时, 精确指定定表。

type查询类型, 全表扫(ALL),
索引扫描(index\<range\<ref\<eq_ref\<const(system)), 尽量用索引。

possible_keys可能用到的索引; key 最后选择索引

key_len联合索引覆盖长度, 利用越充分越好。key_len=a长度+b长度+c长度;
长度指列的最大存储字节长度。数字tinyint为1, int为4, bigint为8;
字符utf8(字符最大长度3), char(10)为3\*10, varchar(10)为3\*10+2

rows 此次需要扫描的行数; extra 额外信息, using
filesort表示使用到文件排序, 可以用联合索引解决。

4\) 建索引的规范

5\) 不走索引

当大量更改表时, 可能会导致索引失效。

查看表和索引信息: select \* from innodb_index_stats\|innodb_table_stats
; 表更新不及时

及时更新: optimize table student ;

索引失效: 在查询条件上使用算数运算, 函数运算, 子查询, 隐式转换, \<\> ,
not in

模糊查询时, %在前面不走, like '%sting'

6\) 优化器算法

查询算法: select @@optimizer_switch\\G ;

临时修改: set global optimizer_switch='batched_key_access=on' ;

永久修改: my.cnf 或 set persist optimizer_switch='batched_key_access=on'
; (后者保存到数据目录下的 mysqld-auto.cnf中, 下次启动时会读取该文件,
还会覆盖缺省的配置文件。)

7\) ICP索引下推

解决联合索引只能部分应用的情况。

1.3.3 mysql存储引擎

1\) 存储引擎简介

相当于Linux文件系统, 只不过比文件系统强大

innodb优点: 事务(Transaction)、MVCC(Multi-Version Concurrency
Control多版本并发控制)、行级锁(Row-level Lock)、ACSR(Auto Crash Safey
Recovery自动的故障安全恢复)、支持热备份(Hot Backup)、Replication: Group
Commit , GTID(Global Transaction ID) ,多线程(Multi-Threads-SQL)

2\) 存储引擎管理命令

查看: select @@default_storage_engine;

修改: set global default_storage_engine=innodb ; 全局生效

永久生效: 修改my.cnf: default_storage_engine=innodb 或 set persist
default\....

查看表的存储引擎: show create table student\\G; 或 show table status
like 'student'\\G;

修改表引擎: alter table student engine=innodb;
该命令还可用于innodb表碎片整理

碎片查看: select table_schema,table_name,data_free from
information_schema.tables;

常见碎片整理方法: 将数据逻辑导出, 手工drop表,
然后导入。或对表进行按月分表(partition)或归档表(pt-archive)

3\) mysql存储体系结构

a\) innodb宏观存储结构: ibdata1:系统数据字典信息(统计信息),
UNDO表空间等数据(undo独立了); ib_logfile0 \~ ib_logfileN:REDO日志文件,
事务日志文件; ibtmp1:临时表空间磁盘位置, 存储临时表;
frm:存储表的列信息(mysql8没了); ibd:表的数据行和索引

b\) innodb微观结构: 表空间: 最初为了解决存储空间的扩展问题(ibdata1)

共享表空间: 某一个数据库的所有的表数据, 索引文件全部放在一个文件中

独立表空间: 每一个表都将会生成以独立的文件方式来进行存储,5.7默认

通用表空间:可用于自定义; Undo表空间:存储undo日志; 临时表空间:存储临时表

查看表空间模式: select @@innodb_file_per_table; 其中1为独立表空间,
0为共享表。

切换表空间: set global innodb_file_per_table=1;

永久生效, 修改my.cnf文件: innodb_file_per_table=1

c\) 事务日志

redo log重做日志: ib_logfile0 \~ ib_logfileN, 用来存储,
mysql在做修改类(DML)操作时的数据页的变化过程,
属于物理日志。默认两个文件存储redo, 循环使用。

undo logs回滚日志: ibdataN和ibtmp1(msyql8为undo_0001,undo_0002),
用来存储回滚日志, 记录每次操作的反操作,
属于逻辑日志。提供innodb多版本读写。提供回滚功能。

4\) innodb内存结构

数据内存区域分为: 共享内存区域, 会话内存区域

日志: 负责redo日志缓存

5\) innodb核心特性

a\) 事务

事务的ACID特性:

Atomic(原子性): 所有语句(DML)作为一个单元全部成功执行或全部取消。

Consistent(一致性): 事务发生前、中、后都应该保证数据的始终一致状态。

Isolated(隔离性): 事务之间不相互影响。

Durable(持久性): 事务成功完成后,
所做的所有更改都会准确地记录在数据库中。

b\) 事务生命周期管理

标准事务控制语句: begin开启事务, commit提交事务,
rollback回滚事务(注意commit后将不能再回滚)

标准事务控制语句: (DML语句) delete, update, insert, select

自动提交功能: 配置文件autocommit=1,
执行语句时自动加begin。对于交易类事务可以设为0, 每次都需要手动commit,
临时生效set global autocommit=0。

隐式事务控制语句: 设置了autocommit=1, 或执行DDL,
DCL等非DML语句时会触发隐式提交。

隐式回滚: 会话关闭, 数据库宕机, 事务语句执行失败

c\) 事务工作流程

redo log

undo

d\) 隔离级别和锁机制

transaction_isolation 隔离级别(参数); 负责的是,MVCC,读一致性问题

RU: 读未提交,可脏读,一般不允许叙出现;

RC: 读已提交,可能出现幻读,可以防止脏读.

RR: 可重复读,功能是防止\"幻读\"现象
,利用的是undo的快照技术+GAP(间隙锁)+NextLock(下键锁);

SR: 可串行化,可以防止死锁,但是并发事务性能较差

修改个例机制: set global transaction_isolation='repeatable read' ;
或改my.cnf文件

6\) mysql锁机制

在事务ACID过程中, \"锁\"和\"隔离级别\"一起来实现\"I\"隔离性和\"C\"
一致性 (redo也有参与).

内存锁: mutex, latch保证内存数据页资源不被争用

对象锁: MDL(元数据锁) 修改元数据(DDL)。Table_locak表锁

record(row) lock行锁, 索引锁, 锁定聚簇索引

GAP间隙锁, RR级别, 辅助索引间隙锁

Next-lock 下一键锁, GAP+record lock是辅助索引的范围锁

功能分类: IS意向共享锁, 表级别; S共享锁, 读锁, 行级别; IX意向排他锁,
表级别; X排他锁, 写锁, 行级别。

7\) innodb核心参数

a\) 缓冲区池大小

innodb_buffer_pool_size=2G (默认128M,一般建议最多是物理内存的 75-80%,
根据show engine innodb status\\G 监控情况设定, 生产中注意大页内存问题)。

b\) 数据存储过程参数

innodb_flush_log_at_trx_commit=1/0/2 控制了innodb将log
buffer中的数据写入日志文件并flush磁盘的时间点。默认1,
每次事物的提交都会引起日志文件写入、flush磁盘。

Innodb_flush_method=fsync/O_DIRECT/O_DSYNC 控制mysql刷写磁盘, 是否走OS
buffer。fsync :日志和数据缓冲区写磁盘,都走OS buffer, 默认。O_DIRECT:
数据缓冲区直接写磁盘,不走OS
buffer。O_DSYNC:日志缓冲(redo)区直接写磁盘,不走 OS buffer。

c\) redo日志有关的参数

innodb_log_buffer_size=16777216 为redo日志缓冲区大小（redo,undo日志等,
默认16M）

innodb_log_file_size=50331648 为redo日志文件大小(ib_logfile0-1, 默认48M)

innodb_log_files_in_group=2 为redo日志文件个数, 默认2个。

1.3.4 mysql日志管理

1\) 错误日志

记录启动\\关闭\\日常运行过程中,状态信息,警告,错误

配置方法: 默认在数据路径下../data/mysql/...err,

自己定制my.cnf中log_error=../mysql.err.log

2\) binlog二进制日志

记录数据库变化(DDL,DCL,DML), 逻辑层性质日志; 用于数据恢复,
主从复制依赖二进制日志

配置方法(my.cnf): server_id=6 ; log_bin=...data/binlog/mysql_bin
(目录+日志前缀) ; sync_binlog=1 设置binlog日志刷盘策略;
binlog_format=row 控制binlog记录格式

生产中一定要和数据盘分开

二进制日志查看: show binary logs ; show master status;

event事件查看: show binlog events in 'mysql_bin.00002' ; (在用的日志)'

在shell中查看使用:

mysqlbinlog \[\--base64-output=decode-rows -vvv\] mysql_bin.0001

mysqlbinlog \--database school mysql_bin.0001 \> event.txt 查看指定库

3\) 日志截取恢复

a\) flush logs ; 刷新出一个新日志文件, 用于滚动日志

b\) 数据恢复: 分析binlog获取起点(154)和终点(200)

截取日志: mysqlbinlog \--start-position=154 \--stop-position=200
.../...\_bin.000n \> .../bin.sql

进入数据库: set sql_log_bin=0(导入数据可以关) ; source ../bin/sql ; set
sql_log_bin=1

c\) 只恢复某个库的

mysqlbinlog -d school \--start-position=154 \--stop-position=200
.../...\_bin.000n \> .../bin.sql

d\) 多个日志文件

起点: mysql_bin.0001 4600, 终点: mysql_bin.0005 980 ; 这时候要用事件截取

mysqlbinlog -d school \--start-datatime= \--stop-datatime=
.../...\_bin.000\[1-n\] \> .../bin.sql

注意: 恢复日志时可以加上\--disable-log-bin (相当于SQL_LOG_BIN=0),
但恢复完后进入数据库打开。

e\) 多年的数据库被删

一般配合备份数据库进行恢复

4\) binlog日志管理

a\) 日志轮替

强制轮替: flush logs ; 默认轮替大小(1G): select @@max_binlog_size;

命令行: mysqladmin -uroot -pcentos7 flush-logs

重启数据库会自动轮替

b\) 日志删除

注意不要使用rm命令删除

自动删除: select @@expire_logs_days=0(天), 一般为2个全备周期15天

手工删除: PURGE BINARY LOGS BEFORE now() - INTERVAL 3 day ;

PURGE BINARY LOGS TO \'mysql-bin.000010\' ;

5\) binlog的GTID模式管理

开启(my.cnf): gtid-mode=on ; enforce-gtid-consistency=true

查看: select @@gtid_mode ;

日志截取: mysqlbinlog \--include-gtids=\'uuid:1-6\'(包含的范围)
\--exclude-gtids=\'uuid:4\'(排除的日志) .../mysql_bin.000003
.../mysql_bin.000004 \> /tmp/bin.sql

GTID的幂等性: 开启GTID后,MySQL恢复Binlog时,重复GTID的事务不会再执行了

mysqlbinlog \--skip-gtids=true \--include-gtids=\'uuid:1-6\'
.../mysql_bin.000003 \> /tmp/bin.sql

6\) slowlog慢日志

a\) 记录慢SQL语句的日志,定位低效SQL语句的工具日志, 用于优化, 默认不开启

配置慢日志(my.cnf): slow_query_log=1;
slow_query_log_file=/data/mysql/slow.log

long_query_time=0.8 (时间阈值, 0.8s);
log_queries_not_using_indexes(没走索引的记录)

b\) 慢日志分析及使用

mysqldumpslow -s c -t 5 .../slow.log

1.3.5 备份恢复

1\) 数据损坏, 备份方式

mysql数据损坏类型: 物理损坏, 逻辑损坏

mysql备份方式:

逻辑备份: msyqldump(MDP), replication, mydumper和load data in file

物理备份: percon Xtrabackup(PBK,XBK)

2\) mysqldump(MDP)备份

备份的是sql语句, 应用场景数据量较小\<100G

a\) MySQLdump的核心参数

连接参数: -u, -p, -h, -P, -S

备份参数:

-A, \--all-databases全备: mysqldump -uroot -p123 -A \> .../full.sql

-B, \--databases备份部分库: mysqldump -uroot -p123 -B school test \>
.../test.sql

\--tables备份表: mysqldump ... school(库) \--tables student(表)
teacher(表) \> .../full.sql

\--where按条件备份: mysqldump ... school student \--where=\'id\<10\' \>
test.sql

备份高级参数:

\--no-data 导出表结构, 不要数据

\--no-create-db 不要建库语句

\--no-create-info 不要建表语句

\--ignore-table school.student 忽略某张表, 多次添加忽略多个表

\--add-drop-database 每个建库语句前添加一个drop语句

\--add-drop-table 每个建表语句前添加一个drop语句

\--add-drop-trigger 在每个create trigger前添加drop语句

\--skip-add-drop-table 在建表语句前不加drop table

\--master-data=2 以注释的形式,保存备份开始时间点的binlog的状态信息

\--single-transaction 对innodb进行快照备份, 不加所有表都锁定

\--max_allowed_packet=128M 服务器发送和接受的最大包长度。

\--set-gtid-purged=OFF 保证从库gtid和主库一样, 主从复制打开

\--triggers 备份触发器

-R, \--routines 备份存储过程及函数

-E, \--events 导出调度事件,备份定时任务

-F, \--flush-logs 在备份开始时,刷新一个新binlog日志

b\) 常用备份语句:

全库备份: mysqldump -uroot -p123 \--all-databases \--routines
\[\--flush-logs\] \--triggers \--set-gtid-purged=OFF \--master-data=2
\--single-transaction \| gzip \> .../full\_\$(date +%F).sql.gz

在构建主从复制环境时:

mysqldump -uroot -p123 -A -R -E \--triggers \--master-data=2
\--single-transaction \--set-gtid-purged=ON \> /data/backup/full.sql

恢复数据库: set sql_log_bin=0; ; source /dir/data.sql; ; set
sql_log_bin=1;

c\) 从mysqldump备份中提取表或库

获得表结构: sed -e \'/./{H;\$!d;}\' -e \'x;/CREATE TABLE \`city\`/!d;q\'
full.sql \> createtable.sql

获得INSERT INTO语句: grep -i \'INSERT INTO \`city\`\' full.sqll
\>data.sql &

获取单库的备份: sed -n \'/\^\-- Current Database: \`world\`/,/\^\--
Current Database: \`/p\' all.sql \> world.sql; 注意如果表中有中文,
可能会出现乱码。

恢复时修改备份库名: sed -i \'/USE \`old\`;/s/old/new/\' xxx.sql ;
修改后用 sed -n '/../p'查看

sed -i \'/CREATE DATABASE/s/old/new/\' xxx.sql ;

sed -i \'1,35s/old/new/\' xxx.sql; #当库较大时建议使用

4\) xbk备份

先安装percona-xtrabackup (mysql5.7和8.0不一样)工具,
在my.cnf中设置\[client\] socket=/tmp/mysql.sock。该工具是服务端工具,
不能远程备份。

a\) mysql5.7

增量备份:

全备: login=\"\--user=root \--password=123 \--no-timestamp\"
#\--no-timestamp自定义路径

innobackupex \${login} /path/full

增备: innobackupex \${login} \--incremental
\--incremental-basedir=/path/full /path/inc1

innobackupex \${login} \--incremental \--incremental-basedir=/path/inc1
/path/inc2

数据恢复:

全备整理: innobackupex \--apply-log \--redo-only /path/full

合并inc1: innobackupex \--apply-log \--redo-only
\--incremental-dir=/path/inc1 /path/full

合并inc2: innobackupex \--apply-log \--incremental-dir=/path/inc2
/path/full

恢复数据: innobackupex \--apply-log /path/full

注意: 最后一次合并不需要加\--redo-only。如果只有全备,
只需最后一步即可恢复数据。

b\) mysql8.0

增量备份:

全备: login=\"\--defaults-file=/etc/my.cnf \--user=x \--password=x
\--port=x \--socket=x.sock\"

xtrabackup \${login} \--backup \--target-dir=/path/full

增备: xtrabackup \${login} \--backup \--incremental-basedir=/path/full
\--target-dir=/path/inc1

xtrabackup \${login} \--backup \--incremental-basedir=/path/inc1
\--target-dir=/pat/inc2

数据恢复:

全备整理: xtrabackup \--prepare(还原) \--apply-log-only(不回滚)
\--target-dir=/path/full

合并inc1: xtrabackup \--prepare \--apply-log-only
\--incremental-dir=/path/inc1 \--target-dir=/path/full

合并inc2: xtrabackup \--prepare \--incremental-dir=/path/inc2
\--target-dir=/path/full

恢复数据: xtrabackup \--copy-back \--target-dir=/path/full

1.4 mysql集群

1.4.1 mysql主从备份(普通)

1\) 前提: 时间同步(ntpdate), 至少两台, server_id(不能相同),
主库开binlog, 网络畅通, 开启专用用户和线程, 确认复制起点

slave服务器主动把master服务器上的数据同步到本地(备份),
slave服务器分摊master服务器的查询压力(负载均衡)。

2\) 在主服务器上授权, 让从服务器保存授权信息, 在主服务器进入mysql

创建复制用户: create user slave@\'ip\' identified with
\'mysql_native_password\' by \'123\';

授权: grant replication slave on \*.\* to slave@' ip';

查看主服务器状态: show master status;

先在slave上将master服务器备份数据导入。

进入slave服务器的mysql, 输入: (帮助help change master to)

change master to master_user='授权用户'

master_password='密码'

master_host='主服务器ip'

master_log_file='mysql-bin.000003' (主服务器的二进制日志)

master_log_pos=257 (当日志起点, 可在全备的输出文件中查看)

master_connect_retry=30 (主服务器断了尝试连接秒数)

进入从mysql输入: start\|stop slave (开启从线程); 查看从服务器内容: show
slave status\\G;

3\) 主从复制过程

4\) 主从监控

主库: show processlist; show slave hosts;

从库: show slave status\\G; (Slave_IO_Running: Yes ; Slave_SQL_Running:
Yes 表示正常)

5\) 主从故障分析及处理

清除主从关系: reset slave all ; 要先停止主从线程, 重搭必须要清除

IO故障: 连接故障, 通过手动连接试;

注意mysql8主从复制会出现: \'caching_sha2_password\' reported error
密码验证问题, 解决方法:

a\) 在master从新创建slave用户。

create user slave@\'ip\' identified with \'mysql_native_password\' by
\'centos7\' ; 授权, 刷新。

或改配置文件default_authentication_plugin=mysql_native_password

b\) 先在 slave 上从 master 获取 public-key: mysql \--ssl-mode=DISABLED
-h \[masterIP\] -uroot -p123456 \--get-server-public-key ;
然后再启动start slave

server_id重复: set persist server_id=7 ; 或者修改my.cnf 重启

uuid相同: mv \.../mysql/data/auto.cnf \.../auto.cnf.bak 重启mysql

sql线程故障:

防止sql错误方法: a) 从库只读将read_only和super_read_only两参数打开;
(b)通过中间件分流, 写分到master, 读分到slave

主从延时问题: 监控show salve status\\G ;
中的seconds_behind_master:0(从库落后主库时间)。然后通过对比master的binlog位置点(show
master status ,位置就是大小)和slave的relay(relay-log.info)执行位置点,
查看落后量。

延时原因:

1.4.2 高级主从备份

1\) 延时从库

为了处理逻辑损坏, 如drop database;

配置: 先停止从库stop slave ; 从库设置CHANGE MASTER TO MASTER_DELAY = 300
;其它同上, 开启从库: start slave ;

单独开启\|停止slave的sql线程: start\|stop slave sql_thread ;

单独开启\|停止slave的io线程: start\|stop slave io_thread ;

2\) 过滤复制

一般用于读多写少的架构, 配置方法:

a\) 主库(my.cnf):

binlog_do_db=school(白名单), binlog_iignore_db(黑名单,
一般不用)。其它不变。

b\) 从库(sql过滤, my.cnf):

库级别(常用)replicate_do_db=school ; replicate_ignore_db=xxx ;

表级别(不常用)replicate_do_table=school.student ;
replicate_ignore_table=xxx.xxx

3\) 半同步复制

在主从服务器中加入半同步复制的插件。控制从库IO是否将relaylog落盘,
一但落盘就返回ACK。但是有弊端, 一般不用。

推荐使用MGR架构或PXC等一致性架构。

4\) GTID主从复制(推荐)

配置参数(主库从库my.cnf): gtid-mode=on ; enforce-gtid-consistency=true
(强制GTID的一致性); log-slave-updates=1 (slave更新是否记入日志)

主库全备, 初始数据导入从库。

从库: change master to master_host=\'10.0.0.51\' ,
master_user=\'slave\', master_password=\'123\' ,
MASTER_AUTO_POSITION=1(自动获取起点信息);

start slave;

5\) 主主复制

在两个服务器上分别创建复制用户, 并授权。

分别修改其配置文件(my.cnf):

auto_increment_increment=2或n 步进值, 避免自增ID字段的冲突, 有几台就几

auto_increment_offset=1或n 起始值, 主主复制, 按顺序设定

分别在两个从库上加入对方信息, start slave, show slave status\\G;

6\) 主从复制架构演变

原生态架构: 1主1从, 1主多从, 多级主从, 双主结构, 延时从库, 过滤复制

非原生态架构: 高可用, 读写分离方案, 分布方案

1.4.3 MHA高可用

1\) 下载地址

mha官网: https://code.google.com/archive/p/mysql-master-ha/

github下载地址:
https://github.com/yoshinorim/mha4mysql-manager/wiki/Downloads

2\) 注意事项

mysql8版本需要将密码加密模式由sha2改为native, 修改配置文件my.cnf:
default_authentication_plugin=mysql_native_password

MHA版本用0.58以上

3\) MHA安装部署

a\) 所有节点(mysql)安装node包:

yum install -y perl perl-devel perl-DBD-MySQL perl-Config-Tiny
perl-Log-Dispatch perl-Parallel-ForkManager
perl-Time-HiRes（所有节点都要安装）

rpm -ivh mha4mysql-node-0.56-0.el6.noarch.rpm

各节点之间配置ssh免密要登录

b\) 主库创建mha管理用户

create user mha@\'172.17.0.%\' identified by \'mha\';

grant all privileges on \*.\* to mha@\'172.17.0.%\';

c\) 在管理节点上安装manger软件(可以不是mysql服务器)

rpm -ivh mha4mysql-manager-0.56-0.el6.noarch.rpm

1.4.4 读写分离

1.4.5 分布式Mycat

1\) Mycat简介

MyCat是目前最流行的基于java语言编写的数据库中间件,
其核心功能是分库分表。配合数据库的主从模式还可实现读写分离。

下载地址: http://dl.mycat.org.cn/

2\) 重要概念

纵向切分/垂直切分: 把原本存储于一个库的数据存储到多个库上

横向切分/水平切分: 把原本存储于一个表的数据分块存储到多个表上。

逻辑库-Schema: Mycat中定义的database, 是逻辑上存在的,
主要是针对纵向切分。

逻辑表-table: Mycat中定义的table, 是逻辑上存在,
主要是针对横向切分提供的概念。

默认端口: 8066

二 Redis

2.1 redis基础

2.1.1 NoSQL简介

1\) 非关系型数据库(NoSQL: Not Only SQL):
是一种轻量、开源、不兼容SQL功能的数据库,
强调Key-Value存储和文档数据库的优点,
是为了解决大规模数据集合多重数据种类带来的挑战,
尤其是大数据应用难题。主要类型有:

键值(key-value): 有Redis、Oracle BDB等, 应用于内容缓存,
处理大量数据的高访问负载,
也用于一些日志系统等。其数据模型是Key指向value的键值对, 通常用hash
table来实现。其优点时查询速度快。

列存储数据库: Cassandra, HBase等, 应用于分布式文件系统。以列簇式存储,
将同一列数据存在一起。其查找速度快, 可扩展性强, 更容易进行分布式扩展。

文档型数据库: CouchDB、MongoDb, 应用于Web应用。数据模型与Key-Value相似,
value是结构化的, 数据库能够了解Value的内容。优点是数据结构要求不严格,
表结构可变, 不像关系型数据库一样需要预先定义表结构。

图形(Graph)数据库: Neo4J、InfoGrid、Infinite Graph, 用于社交网路,
推荐系统等。专注于构建关系图谱。数据模型为图结构。优点是利用图结构相关算法,
如短路寻址, N度关系查找等。

2\) NoSQL是以key-value形式存储, 其不一定遵循传统数据库的一些基本要求,
比如SQL标准、ACID属性、表结构等。这类数据库主要以下特点:
非关系型的、分布式、开源的、水平可扩展的、处理超大量数据、击碎了性能瓶颈、对数据高并发读写、对海量数据的高效率存储和访问、对数据的高可扩展性和高可用性。

2.1.2 redis简介

1\) redis简介

是一个开源的先进的Key-value存储数据库软件, 其通常被称为数据结构服务器,
其键可以包含string、hash、list、set和zset(有序集合),
这些数据类型都支持push/pop、add/remove及取交集并集和差集的操作。为了保证效率,
数据都是缓存在内存中,
Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件,
并且可轻松实现master-slave同步。

2.2.2 redis连接及管理

1\) 客户端连接

redis-cli -h ip -p 6379 -a passwd -n numDb

验证连接: ping #连接正常输出PONG

quit #关闭当前连接

client list #客户端连接列表

client pause 60 #60s后终止来自客户端命令

2\) redis权限管理

3\) redis配置

info #获取服务器的详细信息

config get xxx #获取redis服务器配置文件中的参数(支持通配符)

config set para val #修改redis配置, 无需重启

2.2 redis数据操作

2.2.1 redis键和库操作

1\) key查找

keys pattern #按照键名查找指定key(支持通配符)

exitsts key #确认键是否存在, 1表示存在

randomkey #从当前数据库中随机返回一个key

scan dbNum \[match pattern\] \[count n\] #迭代数据库中的键

type key #返回键的类型

2\) key修改

dump key #序列化给定key, 并返回被序列化的值

rename key newkey #修改key名, 如果newkey存在会覆盖

renamenx key newkey #仅当newkey不存在时修改key名

del key #删除一个键

3\) key时间

expire key seconds #设置一个已存在键的过期时间(s), 过期就删除

pexpire key milliseconds #设置过期时间(毫秒)

expireat key timestamp #设置过期时间, 时间格式是UNIX时间戳

ttl key #以秒为单位返回指定键剩余有效时间, -2不存在了, -1没有剩余时间了

pttl key #以毫秒为单位返回key的剩余的过期时间

persist key #移除key过期时间永久保存

4\) 库操作

select dbNum #切换到指定数据库, 默认0

move key dbNum #将当前数据库的键移动到指定的数据库中

dbsize #返回当前库中键的数量

flushdb #删除当前数据库中所有的数据

flushall #删除所有数据库中的所有数据

2.2.2 string类型操作

string类型可以包含任何数据, 一个key对应一个value, value最大支持512M。

1\) string存储

set key value #存储key=string, 如果key存在会覆盖, 如set name google

setnx key value #设置key-value, 如果key已经存在, 返回0

setex key seconds value #设置key-value值, 并附带时间限制(s)

psetex key milliseconds value #设置key-value值, 并附带时间限制(毫秒)

mset key1 value1 \[key2 value2 ... \] #设置多个key-value

msetnx key1 value1 \[key2 value2 ... \] #设置多个key-value,
如果key已经存在, 返回0

2\) string获取

get key #获取key对应的string值, 如果key不存在返回nil。

getrange key start end #获取key对应string的子字符串, 如getrange name 0 2
=\> goo

mget key1 \[key2...\] #获取多个key的值

strlen key #返回key长度

3\) string修改

getset key value #将key的旧值修改为新值, 并返回旧值

setrange key offset value #从offset开始用value覆盖原值, 如setrange name
3 hi =\> goohie

append key value #将value追加到key存储的值后

incr key #将key中存储的数字加1, 并返回新的值, value必须是数字

incrby key increment #对key存储的数字加上increment, 并返回新的值(整数)

incrbyfloat key increment #对key存储的数字加上浮点数

decr key #将key存储的数字减1

decrby key decrement #将key存储的数字减去decrement

del key #删除key

2.2.2 hash类型操作

hash是一个string类型的filed和value的映射表, 适合用于存储对象,
内存占用更少。

1\) hash存储

hset key field value #存储单个field, 如: hset people name \"chi\"; hset
people age 18

hmset key field1 value1 field2 value2 #存储多个field, 如: hmset people
name \"chi\" age 18

hsetnx key field value #当field不存在时存储value

2\) hash获取

hexists key field #判断指定字段是否存在

hget key field #获取字段value

hmget key field1 \[field2\] #获取多个field的value

hkeys key #获取key的所有field

hvals key #获取key中所有value

hgetall key #获取key所有字段和值

hlen key #获取key中field数量

hscan key cursor \[match pattern\] \[count n\]
#从cursor开始迭代key中匹配的value

3\) hash修改

hincrby key field increment #指定字段的value加上increment(整数)

hincrbyfloat key field increment #指定字段的value加上浮点数increment

hdel key field1 \[field2\] #删除一个或多个字段

2.2.3 list类型操作

list是一个按插入顺序排序的字符串列表, 具有链表结构。(左是头, 右是尾)

1\) list存储

lpush key value1 \[value2\] #在key对应list的头部插入数据

lpushx key value #将值插入已存在的key列表头部, key不存在插入无效

rpush key value1 \[value2\] #在list尾部插入数据

rpushx key value #将值插入已存在的key列表尾部, key不存在插入无效

linsert key before\|after pivot value #在pivot元素前或后插入元素

2\) list获取

lpop key #移出一个列表头部元素

rpop key #移出一个列表尾部元素

blpop key1 \[key2\] timeout #移出头部元素, 如果不存在会阻塞,
超出timeout会返回nil

brpop key1 \[key2\] timeout #移出尾部元素, 同上

lindex key index #通过索引获取列表中的元素

lrange key start stop #获取指定区间\[start, stop)的元素, (0
-1表示从头到尾所有元素)

llen key #获取list长度

3\) list修改

lrem key count value #移除列表元素, 如: lrem mylist -2 \"hello\"
从尾部开始移除2个hello

lset key index value #通过索引设置列表元素的值

ltrim key start stop #删除key中不在\[start, stop)的元素

rpoplpush source destination #移出列表尾部元素插入到列一个列表

brpoplpush src dest timeout #移出列表尾部元素插入到列一个列表,
元素不存在会堵塞

2.2.4 set类型操作

set是string类型的无序集合, set值是唯一的。

1\) set存储

sadd key value1 \[value2\] #添加一个或多个元素到集合中

2\) set获取

scard key #获取集合里面的元素个数

sismember key value #判断value是否为key中的值

smembers key #获取key里面所有的值

srandmember key \[count\] #随机从key中获取count个value

spop key #随机从key中移出一个value

sscan key cursor \[match pattern\] \[count n\]
#从cursor开始迭代key中匹配的value

sdiff key1 key2 #获取key1与key2的差集

sinter ke1 key2 #获取key1与key2的交集

sunion key1 key2 #获取key1与key2的并集

3\) set修改

srem key value1 \[value2\] #从key中移除一个或多个值

smove src dest value #将src中的value移动到dest中

sdiffstore dest key1 key2 #将key1与key2的差集存储在destination

sinterstore dest key1 key2 #将key1与key2的交集存储在destination

sunionstore dest key1 key2 #获取key1与key2的并集存储在destination

2.2.5 zset类型操作

zset是string的有序集合(无重复), 每个值有一个分数(double类型),
值按分数大小排序。

1\) zset存储

zadd key score1 value1 \[score2 value2\] #向key中添加元素或更新分数

2\) zset获取

zrangebyscore key min max \[withscores limi offset count\]
#返回分数区间内的所有值

zrangebylex key val1 val2 \[limit offset count\] #返回值区间内的所有值

zrange key start stop \[withscores\] #返回key下标在\[start,
stop\]之间的值和分数

zrevrangebyscore key max min \[withscores\] #返回分数区间内的值,
分数由高到低

zrevrange key start stop \[withscores\] #返回下标区间内的值,
分数由高到低

zrevrank key value #返回value在key中的排名, 分数最大的排第一, 返回0

zscore key value #返回value的分数

zrank key value #返回value在key中的索引

zcard key #获取有序集合value数目

zcount key min max #统计key分数在\[min, max\]中的value数

zlexcount key val1 val2 #统计key的值在\[value1, value2\]间的value数

zscan key cursor \[match pattern count n\] #迭代有序集合

注意: 分数区间不带区间符号是闭区间, 值区间必须带区间符号, 如(a b\], (2,
4)

3\) zset修改

zincrby key increment value #为key中指定value加上increment的增量

zinterstore dest n key \[keys\] #给n个key求交集并存储在dest中

zrem key val1 \[val2\] #从key中移除value

zremrangebyscore key min max #移除分数区间内的值

zremrangebylex key val1 val2 #移除值区间内的值

zremrangebyrank key start stop #移除下标在\[start, stop\]之间的值

zunionstore des num key1 \[key2\] #计算num个key的并集, 并存储到des中

2.2.6 redis功能

1\) reids事务

multi #标记开始一个事务

exec #执行所有事务块内的事务

discard #取消事务, 放弃执行事务块内的所有事务

watch key1 \[key2\] #监视一个或多个key

unwatch #取消watch监视

注意: a) 所有事务总是以multi开始, 以exec或discard结束

d\) redis事务的执行并不是原子性的,
单个命令的执行是原子性的。既exec执行事务中, 任意命令执行失败,
其余的命令依然被执行

c\) exec事务执行中, 其它client提交的命令不会插入到正在执行的事务中

2\) hyperloglog (用于做基数统计的算法)

pfadd key value1 \[value2 ...\] #添加元素到hyperloglog中

pfcount key1 \[key2 ...\] #返回hyperloglog的基数估数

pfmerge dest key1 \[key2 ...\] #将多个hyperloglog合并为一个hyperloglog

3\) geo (地理位置)

4\) 发布订阅

subscribe channel \[channel ...\] #订阅给定频道

psubscribe pattern \[pattern ...\] #订阅一个或多个匹配的频道

publish channel message #将信息发布到指定频道

unsubscribe \[channel ...\] #退订指定频道

punsubscribe \[pattern ...\] #退订给定模式的频道

pubsub subcommand \[arg ...\] #查看订阅与发布系统状态

5\) stream (消息队列)

a\) 消息队列

xadd key \[maxlen \~ n\] id\|\* field value \[field value ...\]
#添加消息到末尾, 队列名key, 消息id(如: \*自动创建id),
maxlen消息队列最大长度

xrange key start end \[count n\] #获取消息列表(小到大), -最小, +最大

xrevrange key end start \[count n\] #反向获取消息列表(大到小)

xread \[count n\] \[block t\] streams key \[key ...\] id \[id ...\]
#以阻塞\|非阻塞获取消息列表

xtrim key maxlen \[\~\] n \| xtrim key start end #修剪队列长度, 范围:
\'-\'开头, \'+\'结尾

xdel key id \[id ...\] #删除队列中消息, 并不会真正删除

xlen key #消息队列长度

b\) 消费者

xgroup key groupname id\|\$ #创建消费者组, id指定消费组初始位置,
\$尾部开始

xgroup setid key groupname id\|\$ #重置消费者组id

xgroup destroy key groupname id\|\$ #删除消费者组

xgroup delconsumer key groupname consumername #删除消费者

xreadgroup group groupname consumer \[count n block t noack\] streams
key id\|\> #读取消息

xack key groupname id \[id ...\] #将消息标记为\"已处理\"

xpending key groupname \[idie t start end n\] #显示待处理消息,
idie为空闲t毫秒以上消息

xinfo \[groups \| stream\] key #流和消费者组信息 \| 消费者组信息 \|
数据流信息

xinfo consumers key groupname #组中消费者信息

xclaim #转移消息的归属权

注意: a) 推荐使用stream, 因为发布订阅不能持久化消息

2.3 redis进阶

2.3.1 redis优化

1\) Linux内核设置

修改: /etc/sysctl.conf, 添加vm.overcommit_memory=1 (不加该项,
当内存不足可能会保存数据失败)

添加: \'echo madvise \> /sys/kernel/mm/transparent_hugepage/enabled\'
并将其添加到/etc/rc.local中(禁用大页内存, 解决延迟和内存使用问题)

2.3.2 数据备份和恢复

1\) 数据备份

bgrewriteaof #异步执行一个aof文件重写操作

save\|bgsave #保存所有的数据, 推荐用bgsave, 出错用save

2\) 数据恢复

只需要将保存数据移动到redis安装目录并启动服务即可

获取安装目录: config get dir

2.3.3 redis集群

三 MongoDB

3.1 mongoDB基础

3.1.1 简介

1\) mongoDB介绍

mongoDB是一个基于分布式文件存储的数据库,
旨在为web应用提供可扩展的高性能数据存储解决方案。

2\) mongoDB组件

数据库: 集合容器, 存储自己的文件集

集合: 文档分组(类似表), 没有固定结构, 存在于单个数据库中

文档: 集合中的记录(类似行), 包含字段和值

字段: 文档中的键值对(类似列)

\_id: 文档唯一标识, 必须字段, 不指定系统默认创建

游标: 指向查询结果集的指针

3\) CAP定理

分布式数据存储不可能同时满足CAP, 只能满足其中的两部分, 它们分别是:
一致性, 可用性, 分区容错性

3.1.2 连接

1\) 本地连接

mongo \--host xx \--port 27017 \\ //连接主机信息

\--username myName \--password xx \--authenticationDatabase admin
//用户信息, 认证库

2\) 远程连接

mongodb://user:pwd@host1:port1\[,hostn:portn\]/database?options

options:

  -----------------------------------------------------------------------
  authSource=admin 认证库(存储用户信息) slaveOk=true\|false
                                        主服务器连接模式
  ------------------------------------- ---------------------------------
  replicaSet=name 连接复制集            ssl=true\|false 使用tls连接

  fsync=true\|false 同步策略            key=value 向getLastError添加参数

  journal=true\|false 同步到journal     safe=true\|false 更新发送结果消息

  wtimeoutMS \| connectTimeoutMS \|     
  socketTimeoutMS=ms 超时 \| 连接 \|    
  发送接收                              
  -----------------------------------------------------------------------

3.1.3 角色验证

1\) 创建角色

use admin; //要先创建管理员用户, 再开启权限验证

db.createUser({

user: \"name\", pwd: \"xx\", //用户名和密码

roles: \[{ role: \"root \| readWrite \| read\", db: \"dbName\"}, ...\]
//分配角色和库

});

db.auth(\'root\', \'xx\'); //验证用户

db.dropUser(\"name\"); //删除用户, 以root身份切换到认证库

show users \| roles; //显示用户 \| 内建角色

3.1.4 服务器管理

db.serverStatus(); //服务器状态查询

db.currentOp(); //评估mongod业务

3.2 mongoDB数据操作

3.2.1 库和集合

1\) 数据库

use dbName; //创建或切换数据库

db \| show dbs; //查看当前库 \| 查看所有库

db.dropDatabase(); //删除当前数据库

show logs; //查看日志

2\) 集合

db.createCollection(name, options); //创建集合, 选项: {capped:
true(固定集合), size: 1024(集合大小b), max: 100(文档数量)};
集合在插入数据时会自动创建。

show collections; \| db.getCollectionNames(); //查看库中所有集合

db.colOld.renameCollection(colNew); //重命名集合

db.col.drop(); //删除指定集合

db.col.count(); //统计集合中文档总数

3.2.2 文档

1\) 插入文档

db.col.insert(document); //可插入json文档

db.col.save(document, { writeConcern: 1 }); //插入数据, \_id存在就更新

db.col.insertOne(document, { writeConcern: 1\|0 }); //插入一个文档,
1确认, 0不确认

db.col.insertMany(\[doc1, ...\], { writeConcern: 1, ordered: true });
//插入多个文档

注意: a) BSON文档最大为16M.

2\) 更新文档

db.col.update(query, update, options); \| db.col.replaceOne(...);
//更新文档 \| 替换文档

db.col.updateOne(...); \| db.col.updateMany(...); //更新单个 \|
更新多个文档

query条件: {\'key\': \'value\'}

update更新:

{ \$set: { key: \'val\' }, \$currentDate: { date: true }}; //更新值
\|更新当前时间 (不存在创建)

{ \$rename: { oldName: newName }}; //修改字段名

{ \$inc: { key: 2 }}; { \$bit: { key: { and: 5 }}}; //增减数字值 \|
位操作数字值

{ \$push: { key: \'val\' }}; { \$addToSet: { key: val };
//向数组添加一个值 \| 不存在才添加

{ \$pushAll: { key: \'arry\' }}; //向数组内增加多个值或数组

{ \$pull: { key: \'val\' }}; { \$pop : { field: 1 } }; //移除指定值 \|
移除头部或尾部值

options选项: { upsert: false(不存在不插入), multi: false(只更新第一条),
writeConcern: 1\|0 }

3\) 删除文档

db.col.remove(query, { justOne: false(删除所有), writeConcern: 1 });
//删除数据

db.col.deleteOne(query); \| db.col.deleteMany(query); //删除单个文档 \|
删除多个文档

注意: a) 删除文档时, 索引不会被删除

3.2.3 查询

db.col.find(query, opt); \| db.col.findOne(query, opt); //查询数据,
无返回null

1\) query条件

比较查询: {key: \'val\'}, {key: {\$lt: \'val\'}}, 其它: \$lte, \$gt,
\$gte, \$ne

逻辑查询: {k1: \'v1\', k2: \'v2\'}; {k1: {\$in: \[\'v1\', \'v2\'\]}};
{\$or: \[{k1: \'v1\'}, {k2: \'v2\'}\]}; //可组合

类型查询: {\'key\': {\$type: \'string\'}}; //其它: Double, Object,
Array, Boolean, Date, Timestamp,

空和存在: {key: null} \| {key: {\$exists: false}}; //值为空 \|
字段不存在

正则查询: {\'key\': {\$regex: \'xx\', \$options: \'\$i\'}}; //选项:
i(大小写), m(多行), s(单行), x(空白)

{\'key\': \'/xx/i\'}; {\'key\': eval(\"/\" + var + \"/i\")}; //其它形式

2\) 嵌入文档 //key: \[{k1: \'x\', k2: \'y\'}, {...}\] 数组嵌套, key: {
k1: x, k2: y, ..} 文档嵌套

数组: {key: \[\'x\', \'y\'\]} \| {key: {\$all: \[\'x\', \'y\'\]}};
//按值和顺序 \| 不按顺序

{key: {opt1, opt2}} \| {key: {\$elemMatch: {opt1, opt2}}}; //按序满足
\|任意同时匹配

{\'key.0\': {opt}} \| {key: {\$size: 3}}; //指定下标匹配 \| 元素数量匹配

嵌入数组: {key: {k1: \'x\', k2: \'y\'}}; //查询嵌入式数组,
值和顺序都要匹配

{key: {\$elemMatch: {k1: \'x\', k2: \'y\'}}}; //指定多条件, 任意同时匹配

{\'key.k1\': {\$gt: 1, \$lte: 4}}; //指定字段

嵌套文档: {key: {k1: x, k2: y}} \| {\'key.k1\':{opt}, \'key.k2\':
\'x\'}; //按序匹配 \| 指定字段匹配

3\) opt限制

返回字段: db.col.find(query, {key1: 1, key2.child: 0}); //其中1返回,
0排除

db.col.find(query, {key: {\$slice: -1}}); //指定查询数组下标

4\) 查询处理

查询处理: db.col.find().limit(n); \| db.col.find().skip(n);//限制为n查询
\| 跳过前n个查询

db.col.find().sort(\'key\': 1\|-1); //指定键排序, 升序\|倒序

db.col.find().count(); //统计查询总数

显示方法: db.col.find(...).pretty();

查询分析: db.col.find(...).explain() \| explain(\"executionStats\");
//查询计划 \| 性能分析

5\) 结果处理

建立游标: var data = db.xx.find(); //结果赋给变量, 使用游标方法进行处理

遍历游标: data.forEach(function(doc) {printjson(doc);});

while(data.hasNext()) { printjson(data.next()); };

转数组: var doc = data.toArray();

游标管理: var data = db.xx.find().noCursorTimeout(); //不自动关闭,

data.close(); //手动关闭游标, 默认游标在用完或10分钟后会自动关闭

db.serverStatus().metrics.cursor; //游标状态查询

3.2.4 聚合

1\) 聚合

db.col.aggregate(operation); //聚合用于处理数据返回结果, operation方法:

  -----------------------------------------------------------------------
  \$sum, \$avg 求和 \| 平均值         \$min, \$max 最小值 \| 最大值
  ----------------------------------- -----------------------------------
  \$push, \$addToSet 插入值,          \$first, \$last 获取第一 \|
  不创建副本                          最后文档数据

  -----------------------------------------------------------------------

2\) 管道

将当前输出结果作为下一个命令的参数, 如: db.col.aggregate(\[{ \$match: {
score: { \$gt: 70, \$lte: 90 }}}, { \$group: { \_id: \'\${by_user}\' },
count: { \$sum: 1 }}\])

  -----------------------------------------------------------------------
  \$project, \$addFields 文档输出字段 \$match 过滤数据, 类似find
  ----------------------------------- -----------------------------------
  \$limit 限制返回文档数              \$skip 跳过指定文档数

  \$group 文档分组, 用于统计          \$sort 文档排序后输出

  \$unwind 将数组字段拆分             \$geoNear 接近某地理的有序文档
  -----------------------------------------------------------------------

3\) Map-Reduce

db.col.mapReduce( //用于批处理大规模数据

function() { emit(key,value); }, //map函数, 生成reduce参数, 类似分组

function(key,values) { return reduceFunction }, //reduce统计函数,
处理values

{ out: colNew, query: document, sort: document, limit: number }

) //结果存放集合 \| 查询条件 \| 排序 \| 限制

3.2.5 关联

1\) 文档关联

嵌入式: {\"name\": \"xx\", \"addr\": \[{xx}, {xx}\]}

引用式: {\"name\": \"xx\", \"addr_id\": \[{ObjectId(\"xx\")}, {xx}\]};
//存储格式

var result = db.users.findOne({\"name\":\"xx\"},{\"addr_id\":1});
//获取地址id

var addresses =
db.addr.find({\"\_id\":{\"\$in\":result\[\"addr_id\"\]}}); //查询地址

2\) 库引用

库引用: {xx, \"addr\": {\"\$ref\": \"colName\", \"\$id\":
ObjectId(\"x\"), \"\$db\": \"test\", xx}}

查询: var user = db.users.findOne({\"name\": \"xx\"});

var dbRef = user.addr;

db\[dbRef.\$ref\].findOne({\"\_id\": (dbRef.\$id)}});

3.2.6 索引

1\) 索引创建

db.col.createIndex(\'key\': 1\|-1, options); //创建索引,
指定key升序\|倒序

options:

  -----------------------------------------------------------------------
  background: false                   unique: false 索引是否唯一(true)
  在后台创建索引true                  
  ----------------------------------- -----------------------------------
  name: \'xx\' 索引名                 sparse: false
                                      字段数据不存在不启用idx

  weights: n 索引权重1-99999          expireAfterSeconds : n 有效时间s

  default_language \|                 
  language_override: \'xx\'           
  文本索引的语言                      
  -----------------------------------------------------------------------

2\) 索引管理

db.col.getIndexes(); //查看集合索引

db.col.totalIndexSize(); //集合索引大小

db.col.dropIndexes(\"idxName\"); //删除集合索引

db.col.find(...).hint({xx: 1, ...}); //强制使用某索引查询(某些场景有用)

3\) 全文索引

创建: db.col.ensureIndex({ key: \"text\", ... }); //指定key为文本索引,
可指定多个

查询: db.col.find({ \$text: { \$search: \"val ok -txt\" }});
//每个词用或查询, \'-\'排除该词

db.col.find({\$text: { \$search: \"\\\"chi xin\\\"\" }}); //使用短语查询

db.stores.find({\$text: {\$search: \"a b\"}}, {score: {\$meta:
\"textScore\"}}).sort({score: {\$meta: \"textScore\"}});
//将结果按相关性排序

db.articles.aggregate(\[{\$match: {\$text: {\$search: \"a b\"}}},
{\$sort: {score: {\$meta: \"textScore\"}}}\]); //使用管道查询

3.2.7 其它

show profile; //打印耗时1ms以上的操作

load(); //执行js脚本

3.3 mongoDB进阶

3.3.1 备份恢复

1\) mongodump备份 //10g以下

mongodump \--host ip \--port 27017 \--username name \--password pwd \\
//连接信息

\[\--authenticationDatabase dbName \--authenticationMechanism name\] \\
//认证库和算法

\--db dbName \--collection colName \--oplog \--drop \\ //库, 集合,
备份时的日志, 添加删库

\--query json \[ \| \--queryFile /path \] \--excludeCollection string \\
//备份查询数据, 排除集合

\--dumpDbUsersAndRoles \--quit \\ //备份库用户和角色, 暂停库用于备份

\--gzip \--out /path //开启压缩, 备份路径

2\) cp或rsync备份

备份前需要停止mongoDB

3\) 单节点停机恢复

mongod \[ \--dbpath /path/data/xx \] \--repair //恢复所有 \| xx库恢复

4\) mongorestore恢复 //恢复mongodump

mongorestore -h ip:port -u xx -p xx \\ //连接信息 \| \[认证如上\]

-d dbName -c colName \--oplogReplay \[\--oplogFile /path\] \\ //库,
集合, 恢复日志

\--maintainInsertionOrder true \--restoreDbUsersAndRoles \\ //插入顺序,
恢复角色

\--objcheck \--gzip /path \[ \| \--dir /path\] //开启验证, 从gz恢复,
路径

5\) 监控

mongostat //检查运行状态

mongotop \[10(休眠时间) \--locks(库锁的使用)\] //统计读写时间

3.3.2 副本集

1\) 模式

副本集: 保障数据安全, 数据高可用, 分布式读取

模式: 一主一从, 一主多从

2\) 设置副本集

mongod \--port 27017 \--dbpath \"/path/data\" \--replSet replName
//要先关闭mongodb

启动后登陆: rs.initiate(); //启动副本集

查看: rs.status 状态; rs.conf() 配置; db.isMaster() 是否为主

添加: rs.add(\"host:port\");

3.3.3 分片
