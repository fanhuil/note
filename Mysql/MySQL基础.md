# SQL简介

## SQL基本介绍

> 结构化查询语言（Struceured Query Language）简称SQL，是一种特殊的变成语言，是一种数据库查询和程序设计语言，用于存取数据一级查询、更新和管理关系数据库系统；同事也是数据库脚本文件的扩展名。
>
> SQL就是专门为关系型数据库设计出来的

## SQL分类

> 1) 数据库查询语言（DQL：Data Query Language）：其语句，也成为“数据检索语句”，用以从表中获得数据，确定数据怎样在应用程序给出。保留字SELECT是DQL（也是所有SQL）用的最多的动词，其他DQL常用的保留子又WHERE，ORDER BY， GROUP BY，HAVING。这些DQL保留子常与其他类型SQL语句一起使用。
>
>    专门用于查询语句：代表指令为select/show
>
> 2) 数据操作语言（DML：Data Manipulation Language）：其语句包括动词INSERT，UPDATE和DELETE。它们分别用于添加，修改和删除表中的行。也成为动作查询语句。
>
>    专门用于写数据：
>
> 3) 事务处理语言（TPL）：他的语句能确保被DML语句影响的表的所有行及时得意更新。TPL语句包括BEGIN、TRANSACTION、COMMIT、ROLLBACK。（不是所有的关系型数据库都提供事物安全）
>
>    专门用于事务安全处理：transaction
>
> 4) 数据控制语言（DCL）：他的语句通过GRANT或REVOKE获得许可，确定单个用户和用户组对数据库对象的访问。某些RDBMS可用GRANT或REVOKE控制对表 单个列的访问。
>
>    专门用于权限管理：
>
> 5) 数据定义语句（DDL）：其语句包括动词CREATE和DROP。在数据库中创建新表或删除表；为表加入索引等。DDL包括许多与人数据库目录中获得数据有关的保留字。他也是动作查询的一部分。
>
>    专门用于结构管理：

# MySQL基本介绍

## 启动和停止MySQL服务

> MySQL是一种C/S结构

## 登录和退出MySQL



# MySQL服务端架构

> 由以下几层构成：
>
> 1、数据库管理系统（最外层）：DBMS，专门管理服务端的所有内容
>
> 2、数据库（第二层）：DB，专门用于存储数据的仓库（可以有多个）
>
> 3、二维数据表（第三层）：Table，专门用于存储具体实体的数据
>
> 4、字段（第四层）：Field，具体存储某种类型的数据（实际存储单元）



# 数据库基本操作

## 创建数据库

> 基本语法：create database 数据库名称 [库选项];
>
> 库选项：数据库的相关属性
>
> 字符集：charset字符集，代表当前数据库下的所有表存储的数据默认指定的字符集（如果不指定，那么采用DBMS默认的）
>
> 校对集：collate校对集

## 显示数据库

> 每当用户通过SQL指令通过SQL指令创建一个数据库，那么系统就会产生一个对应的存储数据的文件夹
>
> 