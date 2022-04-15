# Redis
---

Redis是一个开源的先进的Key-value存储数据库软件, 其通常被称为数据结构服务器, 其键可以包含string、hash、list、set和zset(有序集合), 这些数据类型都支持push/pop、add/remove及取交集并集和差集的操作。为了保证效率, 数据都是缓存在内存中, Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件, 并且可轻松实现master-slave同步。