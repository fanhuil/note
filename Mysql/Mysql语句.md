```mysql
 -- 查看数据库
 SHOW DATABASES;

-- 切换数据库
USE DATABASENAME;

-- 查看数据库事务自动提交
-- 如果开启了自动提交事务，每个语句就是一个单独的事务，不需要手动提交；但是当开启事务后，执行完语句则需要进行手动的COMMIT或者ROLLBACK
-- 如果没有开启事务的自动提交，即使没有开启事务，执行完语句后都需要手动提交；
select @@autocommit;

-- 设置数据库隔离级别
-- 读未提交（READ UNCOMMITTED）
-- 读提交 （READ COMMITTED）
-- 可重复读 （REPEATABLE READ）
-- 串行化 （SERIALIZABLE）
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- 修改表的存储引擎：
ALTER TABLE 表名 ENGINE = 存储引擎名称;

-- 查看当前服务器支持的引擎
SHOW ENGINES;

-- 查看数据表创建过程
SHOW CREATE TABLE table_name

-- 启动项
-- 禁止各客户端使⽤TCP/IP⽹络进⾏通信
mysqld --skip-networking
-- 设置mysql默认引擎
mysqld --default-storage-engine=name

-- 查看系统变量
SHOW VARIABLES [LIKE 匹配的模式]

-- 查看当前MySQL中支持的字符集
SHOW (CHARACTER SET|CHARSET) [LIKE 匹配的模式]

-- 查看MySQL中支持的比较规则的命名
SHOW COLLATION SHOW COLLATION [LIKE 匹配的模式]

-- 查看表的统计信息
SHOW TABLE STATUS LIKE `table_name`;

-- 查看优化器⽣成执⾏计划的整个过程开关
SHOW VARIABLES LIKE 'optimizer_trace';
```



​	

