# 逻辑分层

> **连接层**:提供与客户端连接服务
>
> **服务层**:提供各种用户使用的接口，提供sql优化器（MySQL Query Optimizer）
>
> **引擎层**:提供各种数据存储方式
>
> ​	InnoDB:事务优先，适合高并发，行锁
>
> ​	MyISAM：性能优先，表锁
>
> **存储层**:存储数据



# 相关SQL

> **查询支持引擎**

```bash
show engines
```

> **查看当前使用的引擎**

```bash
show variables like '%storage_engine%'
```

> **查询索引**

```bash
show index from table
```

> **添加索引**

```bash
alter table tb_name add index_type index_name(column_name1,[column_name2])
```

> **删除索引**

```bash
drop index index_name on tb_name
```



# SQL优化

> **原因**

性能低、执行时间太长、等待时间太长、sql语句欠佳（连接查询）、索引失效、服务器参数设置不合理（缓冲区、线程数）

> SQL

	MySQL编写过程：
	select [dinstinct（去重）] ..from ..join..on ..where ..group by ..having ..order ..limit
	
	MySQL解析过程：
	from .. on..  join  ..where  ..group by . .having ..select dinstinct（去重） ..oder by ...limit
> SQL优化

主要就是在优化索引，索引本身很大，可以存放在内存或者硬盘中，是一个数据结构（B+树（mysql默认）、Hash树。。。）

少量数据，经常改动的列，很少使用的列不适合使用索引



# SQL性能问题

	a、分析SQL的执行计划，explain，可以模拟SQL优化器执行SQL语句，从而让开发人员知道自己编写的SQL状况
	b、MySQL查询优化会干扰我们的优化


# explain查询结果字段详解


	id：编号
	
	select_type：查询类型
		
		PRIMARY：包含子查询SQL中的 主查询（最外层）
	 
		SUBQUERY：包含子查询SQL中的 子查询
	
		SIMPLE：简单查询（不包含子查询和union）
	
		DERIVED：衍生查询（在查询过程中使用到了临时表）
	
			a、在from子查询中只有一张表
	
			b、在from子查询中，如果有table1 union table2，则table1就是derived
	
	table：操作的数据表
	
	type：索引类型、类型（优化前提，需要有索引）
	
	          system>const>eq_ref>ref>range>index>all    (性能越来越低。system,const,基本达不到)
	
	          system：只有一条数据的系统表，或衍生表只有一条数据的主查询 
	
	          const：仅仅能查到一条数据的SQL，用于Primary key 或 unique 索引
	
	          eq_ref：唯一性索引，对于每个索引键的查询。返回匹配的唯一行数据（有且只有一个）
	
	          ref：非唯一性索引，对于每个索引键的查询，返回匹配的所有行
	
	          range：检索指定范围的行，where后面是一个范围查询(between,in.> < >=)，in有时会失效，就变成all
	
	          index：查询全部索引中的数据。
	
	          all：查询表中全部数据
			  =====================================================================
	          system/const：结果只有一条数据
	           
	          eq_ref：结果多条，但每条唯一
	                     
	          ref：结果多条；但是每条数据是0或者是多条
	
	possible_keys：预测用到的索引
	
	key：实际使用的索引
	
	key_len：判断复合索引是否完全被使用
	
		varchar，实际使用索引的长度，用于判断符合索引是否被完全引用，在utf8中，一个字符占三个字节，gbk一个字符2个字节，lantin，一个字符3个字节。
	    char，如果索引字段可以为null，则会使用1个字节用于标识null，用两个字节表示可变长度。
	
	ref：指明当前表所参照的字段，注意与type中的ref区分。
	
	rows：被索引优化查询的数据个数。
	
	Extra：额外的优化信息
	          
	          using filesort：一般常见与 order by 语句中，先查询再排序，性能消耗大；需要“额外”一次排序，排序之前先查询。
	          对于单索引，如果排序和查找是同一个字段，则不会出现using filesort，
		
			  where哪些字段，就order by 哪些字段 对于复合索引，不能跨列（最佳左前缀），跨列则会出现using filesort，
	
			  where和order by 按照复合索引的顺序使用，不要跨列或无序使用
	
	          using temporary：性能损耗大，用到了临时表，一般出现在 group by 语句中，额外多使用一张表。
	
	          using index：性能提升；索引覆盖，不需要回表查询。
	
	          原因：不读取原文件，只从索引文件中获取数据，只要使用到的列全部在索引中，就是索引覆盖using index。
	
			  如果用到了索引覆盖，会对possible_key h和 key有影响。
	
			  如果没有where，则索引只会出现在key中，
	
			  如果有where，则索引出现在key和possible_key中
	            
	          using where：查询无索引或者失效索引，需要回表查询
		
	           impossible where：where字句永远为false；
	
	           useing join buffer：sql语句太差，mysql帮优化


# 索引

	主键索引：不能重复，不能为null
	
	单值索引：单列
	
	唯一索引：不能重复，可以为null
	
	复合索引：多个列构成
	
			如果复合索引和使用的顺序全部一致（且不跨列使用），则复合索引全部使用。如果部分一致（且不跨列使用），则部分使用。


# 表优化

	单表优化：
	
		a、最佳左前缀，保持索引的定义和使用的顺序一致性
	
	    b、索引需要逐步优化
	
		c、将含In的范围查询放到where条件的最后，防止索引失效，范围查询会是索引失效，跳过范围查询的字段建立复合索引
	
	两表优化：
		a、左连接给右边建立所以索引，右连接给左边建立索引
	
	多表优化：
	
		a、尽可能减少Join语句中的NestedLoop的循环总次数；永远用小结果集驱动大结果集
	
		b、优先优化NestedLoop的内层循环
	
		c、 保证Join语句中被驱动表上Join条件字段已经被索引
	
		d、 当无法保证被驱动表的Join条件字段被索引且内存资源充足的前提下，不要太吝惜JoinBuffer的设置



# 避免索引失效的一些原则

	a、复合索引，不要跨列或无序使用（最佳左前缀）
	
	b、复合索引，尽量使用全索引匹配
	
	c、不要在索引上进行任何操作（计算、函数、类型转换），否则索引失效，
	
	    对于复合索引，左边失效，右边全部失效。
	
	d、复合索引不能使用不等于或者is not null，否则自身及右边索引全部失效
	
	尽量使用索引覆盖，like尽量以常量开头，否则索引失效，尽量不要使用类型转换，
	
	尽量不要使用or，否则索引失效
	
	like尽量以常量开头，尽量使用索引覆盖，不要使用类型转换


# 其他的优化方法

	exists和in优化：
	
		将主查询的结果，放到子查询结果中进行条件校验（判断是否有数据），如果有数据则校验成功
	
		exist和in，如果主查询的数据集大，则使用In，如果子查询的数据集大，则使用exist
	
	order by 优化：
	
		using filesort 两种算法，单路排序，双路排序。
	
		MySQL4.1之前， 默认使用 双路排序：扫描磁盘两次（1.从硬盘读取排序字段，对排序字段继续进行排序。
	
		在buffer缓冲区中进行的。2、扫描其他字段）
	
		MySQL4.1之后，默认使用 单路排序：IO比较消耗性能，只读取一次，在buffer缓冲区中进行的（全部数据）
		
		不一定是真的一次IO，比双路排序占用更多的buffer。
	
		提高 order by 查询的策略：
		
		a、选择使用单路双路；调整buffer容量的大小
	
		b、避免select *
	
		c、复合索引 不要跨列使用
	
		d、保证全部的排序字段 都是升序或降序


# SQL排序

	慢查询日志：MySQL提供的一种日志记录，用于记录MySQL中响应时间超过阀值的SQL语句，默认是关闭的。
	
	SHOW VARIABLES LIKE '%slow_query_log%'    查询是否开启慢查询
	
	临时开启：set global slow_query_log = 1 -- 在内存中开启
	
	慢查询阀值：SHOW variables like '%long_query_time%'
	
	修改阀值：set global long_query_time = 5    需要重新登录登陆
	
	查看超过阀值的SQL数量：show global status like '%slow_queries%'


# 分析海量数据：profiles

	show profiles 默认关闭
	
	show variables like 'profiling'; 查看profiles开启状态
	
	set profiling = on 打开profiles
	
	a、粗略分析
	show profiles    会记录所有profiling打开之后的全部SQL查询语句所花费的时间。
	
	缺点，只能开到总共消费的时间
	
	b、精确分析
	show profile all for query    上一步查到的query_id
	
	show profile cpu,block io for query    上一步查到的query_id
	
	危险警告信息：
	converting HEAP to MyISAM 查询结果太大，内存都不够用往磁盘上搬
	Creating tmp table 创建临时表
	Copying to tmp table on disk 把内存中临时表复制到磁盘
	locked
	
	c、全局查询日志：记录开启之后的全部sql语句。（开发时打开，在生产环境时关闭）
	
	show variables like '%general_log%'   查看开启状态
	
	set global general_log = 1    打开全局查询日志 
	
	set global log_output = 'table'    设置将数据存到数据表中
	
	开启后会记录所有sql：会被记录到mysql.general_login数据表中


# 常用SQL语句

	ALTER TABLE old_table_name RENAME TO new_table_name    修改数据表名称
	
	SHOW ENGINE    查询数据库支持引擎
	
	SHOW VARIABLES LIKE %storage_engine%    查看当前使用引擎
	
	CREATE 索引类型 索引名 ON 表（字段）    建立索引
	
	ALERT TABLE 表名 索引类型 索引名（ 字段 ）    建立索引
	
	DROP 索引类型 索引名 ON 表名   删除索引
	
	SHOW 索引名 from 表名    查看索引
	
	Table：表名
	
	Non_unique：如果索引不能包括重复值则为0，如果可以则为1,也就是唯一索引。
	
	Key_name：索引名称，如果名字相同则表明是同一个索引，而并不是重复。
	
	Seq_in_index ：索引中的列序列号，就是表明在联合索引中的顺序，我们就能推断出联合索引中索引的前后顺序。
	
	Column_name ：索引的列名。
	
	Collation  ：列以什么方式存储在索引中，大概意思就是字符序。有值 A（升序）或NULL（无分类）。
	
		   默认的类型是utf8_general_ci，这样的大小写不敏感。
	
	Cardinality：基数的意思，表示索引中唯一值的数目的估计值。
	
		   基数根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要是精确的。
	
	 	   基数越大，当进行联合时，MySQL使用该索引的机会就越大。
	
		   我们知道某个字段的重复值越少越适合建索引，所以我们一般都是根据Cardinality来判断索引是否具有高选择性，
	
		   如果这个值非常小，那就需要重新评估这个字段是否适合建立索引。
	
		   因为MySQL数据库中有各种不同的存储引擎，而每种存储引擎对于B+树索引的实现又各不相同。
	
		   所以对Cardinality统计时放在存储引擎层进行的，至于它是如何统计更新的在这里就不再做更深入的介绍了。
	
	Sub_part：前置索引的意思，如果列只是被部分地编入索引，则为被编入索引的字符的数目。
	
		 如果整列被编入索引，则为NULL。
	
		 对于BLOB，TEXT，或者很长的VARCHAR类型的列，必须使用前缀索引，
	
		 因为MySQL不允许索引这些列的完整长度，这会让索引变得大且慢。
	
		 选择长度的诀窍在于要选择足够长的前缀以保证较高的选择性，同时又不能太长以便节约空间。
		 
	Packed ：指示关键字如何被压缩。如果没有被压缩，则为NULL。
	
		压缩一般包括压缩传输协议、压缩列解决方案和压缩表解决方案。
	Index_type：索引类型，Mysql目前主要有以下几种索引类型：FULLTEXT，HASH，BTREE，RTREE。 
	
		   a、FULLTEXT    即为全文索引，目前只有MyISAM引擎支持。
	
		        其可以在CREATE TABLE ，ALTER TABLE ，CREATE INDEX 使用，不过目前只有 CHAR、VARCHAR ，TEXT 列上可以创建全文索引。
	
		        全文索引并不是和MyISAM一起诞生的，它的出现是为了解决WHERE name LIKE “%word%"这类针对文本的模糊查询效率较低的问题。
	
	 	   b、HASH     由于HASH的唯一（几乎100%的唯一）及类似键值对的形式，很适合作为索引。
	
		        ASH索引可以一次定位，不需要像树形索引那样逐层查找,因此具有极高的效率。
	
		       但是，这种高效是有条件的，即只在“=”和“in”条件下高效，对于范围查询、排序及组合索引仍然效率不高。
	
		  c、BTREE    BTREE索引就是一种将索引值按一定的算法，存入一个树形的数据结构中（二叉树），
	
		       每次查询都是从树的入口root开始，依次遍历node，获取leaf。这是MySQL里默认和最常用的索引类型。
	
		  d、RTREE    RTREE在MySQL很少使用，仅支持geometry数据类型，支持该类型的存储引擎只有MyISAM、BDb、InnoDb、NDb、Archive几种。
	
		       相对于BTREE，RTREE的优势在于范围查找。
	
	Comment 、ndex_comment ：注释的意思。



# 主从同步

	主从同步：
	1、master将改变的数据记录在本地的二进制日志（binary log）中；该过程称为二进制日志事件
	2、slave将master的二进制日志（binary log）拷贝到自己的中继日志文件（relay log）中
	3、中继日志文件（relay log），将数据读取到自己的数据库之中。
	
	MSYQL主从复制是异步的，串行化的，有延迟的。master：relay = 1：n
	同步前设置：
		允许从计算机远程连接：GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'Fhl@0905360000' WITH GRANT OPTION;
			      FLUSH PRIVILEGES;


​	
	主机操作：
		mysql.ini配置：
			server-id = 1
			log-bin= 配置二进制日志文件的路径
			log-error = 配置的错误记录文件路径
			binlog-ignore-db = 指定忽略的数据库不进行同步
			binlog-do-db = 指定同步的数据库
			授权从数据库：
		GRANT REPLICATION slave,reload,super ON *.* TO 'root'@'39.98.140.205' IDENTIFIED BY 'Fhl@0905360000';
		flush privileges;
	查看主数据库状态(每次同步前观察最新状态)：
		show master status
		File Position 
		
		从机操作：
		my.cnf配置：
			server-id = 2
			log-bin= 配置二进制日志文件的路径
			replicate-do-db = 指定主数据库
		授权主数据库链接：
			CHANGE MASTER TO
			MASTER_HOST = '192.168.1.102',
			MASTER_USER = 'root',
			MASTER_PASSWORD = 'root',
			MASTER_PORT = 3306,
			master_log_file = 'mysql-bin.000002',
			master_log_pos = 357;
		开启主从同步：
			start slave;
		查看从数据库状态:
			show slave status \G;



# 分区

     RANGE：基于属于一个给定连续区间的列值，把多行分配给分区。
                  CREATE TABLE employees(
                  id INT NUO NULL
                  fname VARCHAR(30)
                  lanem VARCHAR(30)
    	。。。
                  store_id
                  )	
     			 partition BY RANGE(store_id)(
                 partition p0 VLAUES LESS THAN(value1),
     partition p1 VLAUES LESS THAN(value2),
     partition p2 VLAUES LESS THAN(value3),
     partition p3 VLAUES LESS THAN(value4),
             )
    LIST：类似按range分区，区别在于LIST分区是基于列值匹配一个离散集合中的某个值来进行选择
             partition BY RANGE(store_id)(
                 partition p0 VLAUES IN (LIST1),
     partition p1 VLAUES IN (LIST2),
     partition p2 VLAUES IN (LIST3),
     partition p3 VLAUES IN (LIST4),
             )
             HASH：基于用户自定义的表达式的返回值来进行选择的分区，该表达式使用将要插入到表中的这些行的列值进行计算。
                这个函数可以包含mysql中有效的，产生非负整数值得任何表达式
    
    KEY：类似于按hash分区，区别在于key分区值支持计算一列或多列，切mysql服务器提供器自身的哈希函数。
            必须有一列或多列包含整数值


​    
    查看是否支持分区：
    	show variables like '%partition%'


​    
    其它注意事项：
    
    1、最大分区数目不能超过1024，一般建议对单表的分区输不要超过150个
    
    2、如果含有唯一索引或者主键，则分区列必须包含在所有的唯一索引或者主键在内
    
    3、不支持外键索引
    
    4、不支持全文索引，对分区表的分区键创建索引，那么这个索引也将被分区
    
    5、按日期进行分区很合适，因为很多日期函数可以用。但是对于字符串来说合适的分区函数不太多
    
    6、只有RANGE和LIST分区能进行子分区，HASH和KEY分区不能进行子分区
    
    7、临时表不能创建分区
    
    8、分区表对于单条记录的查询没有优势
    
    9、要注意选择分区的成本，每插入一行数据都需要按照表达式筛选插入的分区
    
    10、分区字段尽量不要可以为null


​    



# 分库分表

为什么要分库分表：

	数据库的复制能解决访问问题，并不能解决大规模的并发写入问题，由于无法进行分布式部署，而一台服务器
	
	的资源（CPU、磁盘、内存、IO等）是有限的，最终数据库所能承受的数据量、数据处理能力都将遭遇瓶颈。
	
	1、解决磁盘系统最大文件限制，
	
	2、减少增量数据写入时对锁查询的影响，减少长时间查询造成的表锁，影响写入操作等锁竞争的情况，
	
	     节省排队的时间开支，增加吞吐量。
	
	3、由于单表数据下降，常见的查询操作由于减少了需要扫描的记录，使得单表单次查询所需的检索行数减少，
	
	     ，减少了磁盘IO，时延变短。

什么是分库：

	分库又叫垂直切分，就是把原本储存于一个库的表拆分存储到多个库上，通常是将表按照功能模块、关系密切程度划分出来，
	
	部署到不同的库上。如果数据库是因为表太多而造成海量数据，而且项目的各项业务逻辑划分清晰、低耦合，
	
	那么规则简单明了、容易实施的首选就是分库。
	
	优点：实现简单，库与库之间界限分明，便于维护，缺点是不利于频繁跨库操作，单表数据量大的问题解决不了。

什么是分表：

	分表又叫水平切分，是按照一定的业务规则逻辑，将一个表的数据拆分成多份，分别存储在多个表结构一样的
	
	表中，这多个表可以存在一到多个库中。分表又分成垂直分表和水平分表：
	
	垂直分表：将本来可以在同一个表的内容，人为划分为多个表。（就是拆分字段）
	
	水平分表：数据分片，是把一个表复制成同样表结构的不同表，然后把数据按照一定的规则划分，分别存储到这些表中
	
	，从而保证单表的容量不会太大，提升性能；当然这些结构一样的表，可以放在一个或多个数据库中。
	
	优点：能解决分库的不足点，但是缺点是实现起来比较复杂，特别是分表规则的划分，程序的编写，
	
	以及后期的数据库拆分移植维护。

如何分库：

	基本的思路就是分析业务功能，以及表间的聚合关系，把关系紧密的表放在一起。
	
	分库的粒度指的是在做切分时允许几级的关联表放在一起。

如何分表：

	对于垂直分表，就是拆分字段。对于水平分表，就是拆分数据。