# 6MySQL常见的集中存储引擎



# 一、MySQL的体系结构

![img](MySQL%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E/img.136.la&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg)

整个MySQLServer有以下组成部分

- Connect Pool：连接池组件
- Management Service & Utilities：管理服务和工作组件
- SQL Interface：SQL接口组件
- Parser：查询分析器组件
- Optimizer：优化器组件
- Caches & Buffers：缓冲池组件
- Pluggable Storage Engines：存储引擎
- File System：文件系统

（1）连接层

最上层是一些客户端和链接服务，包含本地sock通信和大多数基于客户端/服务端工具类实现的类似于TCP/IP的通信。主要完成一些类似于连接处理、授权认证及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同时在该层上可实现基于ssl的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

（2）服务层

第二层架构主要完成大多数的核心功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化，如确定表的查询顺序，是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存，如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

（3）引擎层

存储引擎层，存储引擎真正负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样就可以根据需要来选取合适的引擎。

（4）存储层

数据存储层，主要是将数据存储在文件系统之上，斌完成与存储引擎的交互。与其它数据库相比，MySQL有点与众不同，它的架构可以在多种不同场景中应用并发挥良好作用。主要体现在存储引擎上，插件式的存储引擎架构，将查询处理和其它的系统任务以及数据的存储提取分离。这种架构可以根据业务需求和实际需要选择合适的存储引擎。



# 二、存储引擎

## 2.1、概述

和大多数的数据库不同，MySQL中有一个存储引擎的概念，针对不同的存储需求可以选择最优的存储引擎。存储引擎就是存储数据，建立索引，更新查询数据等等技术的实现方式。存储引擎是基于表的。所以存储引擎也可以称为表类型。插件式存储引擎是MySQL数据库最重要的特性之一。

MySQL5.7支持的存储引擎包含：InnoDB、MyISAM、BDB、MEMORY、MERGE、EXAMPLE、NDB Cluster、ARCHIVE、CSV、BLACKHOLE、FEDERATED等，其中InnoDB和BDB提供十五安全表，其它存储引擎是非事务安全表。MySQL5.5以前的默认存储引擎是MyISAM，5.5之后改为InnoDB

查看MySQL数据库的默认存储引擎：

```mysql
show variables like '%default_storage_engine%'
```



## 2.2、各种常用存储引擎特性

|     特点     |       InnoDB        | MyISAM | MEMORY | MEGRE | NDB  |
| :----------: | :-----------------: | :----: | :----: | :---: | :--: |
|   存储限制   |        64TB         |   有   |   有   | 没有  |  有  |
|   事务安全   |        支持         |        |        |       |      |
|    锁机制    | 行锁（适合高并发）  |  表锁  |  表锁  | 表锁  | 行锁 |
|   B树索引    |        支持         |  支持  |  支持  | 支持  | 支持 |
|   哈希索引   |                     |        |  支持  |       |      |
|   全文索引   | 支持（5.6版本以后） |  支持  |        |       |      |
|   集群索引   |        支持         |        |        |       |      |
|   数据索引   |        支持         |        |  支持  |       | 支持 |
|   索引缓存   |        支持         |  支持  |  支持  | 支持  | 支持 |
|  数据可压缩  |                     |  支持  |        |       |      |
|   空间使用   |         高          |   低   |  N/A   |  低   |  低  |
|   内存使用   |         高          |   低   |  中等  |  低   |  高  |
| 批量插入速度 |         低          |   高   |   高   |  高   |  高  |
|   支持外键   |        支持         |        |        |       |      |



## 2.3、MySIAM存储引擎

MyISAM是mysql5.5之前的默认存储引擎，MyISAM既不支持事务，也不支持外键，每个MyISAM在磁盘上存储成三个文件，其文件名都和表名相同，但拓展名分别是：

- .frm（存储表定义）
- .MYD（存储数据）
- .MYI（存储索引）

数据文件和索引文件可以放置在不同目录，平均分布IO，获得更快的速度。

MyISAM的表还支持3种不同的存储格式

- 静态表
- 动态表
- 压缩表



## 2.4、InnoDB存储引擎

InnoDB是MySQL5.5之后的默认存储引擎，提供了具有提交、回滚和崩溃回复能力的事务安全保障，同时提供了更小的锁粒度的更强的并发能力，拥有自己独立的缓存和日志。但是对比MyISAM的存储引擎，InnoDB的处理效率要差一些，并且会占用更多的磁盘空间以保留数据和索引。

1、InnoDB的自动增长列

- 对于InnoDB表，自动增长列必须被索引，如果是组合索引，也必须是组合索引的第一列
- 自动增长默认是从一开始，可以通过 ' alter table table_name auto_increment = n '语句强制自动增长列的初始值。
- 在MySQL8.0以前对于InnoDB引擎来说自动增长值是保存在内存中的，如果数据库重新启动，name这个值就会丢失，数据库会自动将auto_increment重置为自增列当前存储的最大值+1
- 可以通过LAST_INSERT_ID()查询当前 线程最后插入记录的值，如果一次插入多条数据，那么返回的第一条记录使用的自动增长值，但是如果认为指定自增列的值，LAST_INSERT_ID()的值不会更新。

2、InnoDB引擎存在事务

3、InnoDB的外键约束

​    MySQL支持外键的存储引擎只有InnoDB，在创建外键的时候，要求父表必须有对应的索引(一般关联主表的主       键，因为主键非空且唯一)。

在创建索引时，可以指定在删除、更新父表时，对子表进行相应操作，包括RESTRICT、CASCADE、SET NULL和NO ACTION

- NO ACTION 和 RESTRICT：是指定限制在子表有关联记录的情况下，父表不能更新；
- CASCADE：父表在更新或者删除时，更新或者删除子表对应的记录；
- SET NULL：表示父表在更新或者删除的时候，子表的对应字段被SET NULL

```mysql
-- 如下两张表，子表（city_innodb）的country_id为外键，关联主表(country_innodb)de country_id字段，并且设置了外键之间的级联关系
create table country_innodb(
	country_id int NOT NULL AUTO_INCREMENT,
    country_name varchar(100) NOT NULL,
    primary key(country_id)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;


create table city_innodb(
	city_id int NOT NULL AUTO_INCREMENT,
    city_name varchar(50) NOT NULL,
    country_id int  NOT NULL,
    primary key(city_id),
    key idx_fk_country_id(country_id),
    CONSTRAINT `fk_city_country` FOREIGN KEY(country_id) PREFERENCES country_innodb(country_id) ON DELETE RESTRICT ON UPDATE CASCADE
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```



针对上面创建的两个表，子表的外键指定是ON DELETE RESTRICT ON UPDATE 方式的，那么在主表删除记录的时候，如果子表有对应记录，则不允许删除，主表在更新记录时，如果子表有对应记录，则子表对应更新











