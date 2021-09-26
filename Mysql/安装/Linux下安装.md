# MySQL的安装方式

## （一）二进制方式安装

- RPM版本

  需要在特定linux版本下安装

- 基于glibc版本

  依赖glibc库，可以安装在通用的Linux系统下

## （二）源代码编译安装

​	   通用的Linux下都可以安装编译

## （三）安装方式总结

| 安装方式   | 有点                             | 缺点                              |
| ---------- | -------------------------------- | --------------------------------- |
| rpm        | 安装卸载简单                     | 可定制性差                        |
| glibc      | 可定制性比rpm包                  | 安装相比rpm复杂写，需要手动初始化 |
| 源码包安装 | 可定制性最强，根据需求和功能定制 | 安装麻烦，需要手动初始化数据库    |



# MySQL数据库的安装

## 1、glibc方式安装

### （一）安装须知

- 软件包介绍

> mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz
>
> ==说明==：通用linux下的二进制包，已编译好，只需放到相应的安装目录里即可

- 依赖库安装

> 如果已安装则可以忽略，没安装需要安装

```powershell
# yum install libaio -y
```

- 下载对应的压缩包，解压

```bash
# https://dev.mysql.com/doc/refman/5.7/en/binary-installation.html
ls filename # 查看目录结构
```

![image-20210328104607292](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328104607292.png)

### （二）软件安装

```powershell
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
shell> cd /usr/local
shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
shell> ln -s full-path-to-mysql-VERSION-OS mysql
shell> cd mysql
shell> mkdir mysql-files
shell> chown mysql:mysql mysql-files
shell> chmod 750 mysql-files
shell> bin/mysqld --initialize --user=mysql
shell> bin/mysql_ssl_rsa_setup
shell> bin/mysqld_safe --user=mysql &
# Next command is optional
shell> cp support-files/mysql.server /etc/init.d/mysql.server
```

**1.安装需求**

| 安装路径/ | /mysql_3306      |
| --------- | ---------------- |
| 数据路径  | /mysql_3306/data |
| 端口号    | 3306             |

**2.创建mysql用户**（其所属组也叫mysql）

```bash
useradd -rs /sbin/nologin mysql
```

**3.拷贝程序到安装目录**

1）清空环境（清空系统原有的mariadb的配置文件）

```bash
rm -rf /etc/my.cnf
```

2）创建安装目录

```bash
mkdir /mysql_3306
```

3）解压软件并copy（glibc版本）到安装位置

```bash
cp filename /mysql_3306/filename
```

4）切换到mysql工作目录/mysql_3306，创建一个mysql-files文件

```bash
mkdir mysql-files
chowm mysql:mysql mysql-files

# 说明：
# mysql-files目录为secure_file_priv系统变量提供一个位置，该位置将导入和导出操作限制到特定目录
```

5）更改权限

```bash
chmod 750 mysql-files
```

6）初始化数据库（可以实现数据库的初始化，而且会产生一个随机密码）

```bash
bin/mysqld --initialize --user=mysql --basedir=/mysql_3306
# 选项说明：
# --initialize 初始化（真正开始干活）
# --user=mysql 以mysql用户的身份初始化数据库，产生文件都是mysql作为拥有者
# --basedir    mysql其安装目录，非常重要
```

> 是否初始化成功：主要看mysql目录下有没有产生data文件，且文件夹中至少要有一个mysql的文件夹。

7）设置安全加密链接（SSL）

```bash
# bin/mysql_ssl_rsa_setup --datadir=/mysql_3306/data
```

8）启动数据库

```bash
cp support-files/mysql.server /etc/init.d/mysql_3306
```

> 注意：默认情况下，GLIBC版本的数据库要求安装到/usr/local/mysql目录，其mysql.server脚本中对应的目录也是/usr/local/mysql，这会导致mysql无法启动。所以可以更改其basedir以及datadir两个变量.
>
> 打开mysql.server文件，修改basedir和datadir

### （三）GLIBC版本后续配置

- 配置环境变量

``` bash
echo "export PATH=$PATH:/mysql_3306/bin" >> /etc/profile
source /etc/profile
```





## 2、源码安装方式

> 文档：https://dev.mysql.com/doc/refman/5.7/en/source-configuration-options.html

### （一）安装须知

- 软件包安装

> mysql-boost-5.7.31.tar.gz

- 配置--》编译--》安装

### （二）软件安装

| 安装需求            | 具体配置            |
| ------------------- | ------------------- |
| 安装目录（basedir） | /mysql_3307         |
| 数据目录（datadir） | /mysql_3307/data    |
| 端口号              | 3307                |
| socket文件位置      | $basedir/mysql.sock |
| 字符集              | utf8mb4             |

![image-20210328142255031](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328142255031.png)

![image-20210328142335707](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328142335707.png)

![image-20210328142436044](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328142436044.png)

![image-20210328142624709](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328142624709.png)

![image-20210328142643612](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328142643612.png)

![image-20210328142658553](Linux%E4%B8%8B%E5%AE%89%E8%A3%85/image-20210328142658553.png)

1）安装MySQL编译需要用到的库

> yum -t install ncurses-devel cmake libaio-devel openssl-devel

2）下载Boost库源代码（选做）

```bash
codeing...
```

3）解压软件包

```bash
tar -zxvf 
```

4）进入软件包

```bash
cd 
```

5）进行配置(基于cmake进行配置)

```bash
# 配置，可以编写脚本文件进行配置，不容易出错
cmake . \
-DCMAKE_INSTALL-PREFIX =
-DMYSQL_DATADIR =
-DMYSQL_TCP_PORT = 
-DMYSQL_UNIX_ADDR = 
```

6）编译和安装

```bash
make -j2 && make intsall
选项说明：
-j 代表同时开启多个线程实现同时编译
```

> 部分报错处理

```bash
# 安装boost

rm -f CMakeCache.txt
```

7）初始化数据库

注意：需要进入到mysql的安装目录

```bash
cd /mysql——3307
```

一、创建mysql-files目录

```bash
mkdir mysql-files
chown mysql:mysql mysql-files
chmod 750 mysql-files
# 说明：
# mysql-files目录为secure_file_priv系统变量提供一个位置，该位置将导入和导出操作限制到特定目录
```

二、初始化数据库

```bash
bin/mysqld --initialize --user=mysql --basedir=/mysql_3307 --datadir=/mysql_3307/data
# 选项说明：
# --initialize 初始化（真正开始干活）
# --user=mysql 以mysql用户的身份初始化数据库，产生文件都是mysql作为拥有者
# --basedir    mysql其安装目录，非常重要
# --datadir    数据目录
```

三、拷贝mysql.server脚本到/etc/init.d目录，然后启动数据库

```bash
cp support-files/mysql.server /etc/init.d/mysql_3307
```

### （三）MySQL源码编译安装后的后续配置

1）编写MySQL配置文件

```bash

```



2）设置管理员密码

```bash
bin/mysqladmin -uroot password '123456' -p
```

3）安全设置

```bash
bin/mysql_secure_installation
```

4）添加mysql_3307到开机启动

```bash
chkconfig -add mysql_3307
chkconfig mysql_3307 on
```







