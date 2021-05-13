```mysql
 # 选择数据库
 SHOW DATABASES;
```



```mysql
# 切换数据库
USE DATABASENAME;
```



```mysql
# 查看数据库事务自动提交
select @@autocommit;

# 如果开启了自动提交事务，每个语句就是一个单独的事务，不需要手动提交；但是当开启事务后，执行完语句则需要进行手动的COMMIT或者ROLLBACK
# 如果没有开启事务的自动提交，即使没有开启事务，执行完语句后都需要手动提交；
```



```mysql
-- 设置数据库隔离级别
-- 读未提交（READ UNCOMMITTED）
-- 读提交 （READ COMMITTED）
-- 可重复读 （REPEATABLE READ）
-- 串行化 （SERIALIZABLE）
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

