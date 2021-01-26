# CentOS 7 yum安装 PHP7.3 教程

1、首先安装 EPEL 源：

```shell
yum install epel-release
```

安装 REMI 源：

```shell
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm  
```

2、安装 Yum 源管理工具：

```shell
yum install yum-utils
```

3、安装 PHP7.3：

```shell
yum install -y php73-php-fpm php73-php-cli php73-php-bcmath php73-php-gd php73-php-json php73-php-mbstring php73-php-mcrypt php73-php-mysqlnd php73-php-opcache php73-php-pdo php73-php-pecl-crypto php73-php-pecl-mcrypt php73-php-pecl-geoip php73-php-recode php73-php-snmp php73-php-soap php73-php-xmll
```

安装完成后最好重启一下计算机，不然有可能无法启动php
设置开机启动、运行服务：

```shell
systemctl enable php73-php-fpm
systemctl start php73-php-fpm1
```

设置 PHP
查找安装包：

```shell
[root@xxx etc]# rpm -qa | grep 'php'
php73-php-cli-7.3.6-1.el7.remi.x86_64
php73-php-mbstring-7.3.6-1.el7.remi.x86_64
php73-php-pecl-mcrypt-1.0.2-1.el7.remi.x86_64
php73-runtime-2.0-1.el7.remi.x86_64
php73-php-pdo-7.3.6-1.el7.remi.x86_64
php73-php-bcmath-7.3.6-1.el7.remi.x86_64
php73-php-fpm-7.3.6-1.el7.remi.x86_64
php73-php-mysqlnd-7.3.6-1.el7.remi.x86_64
php73-php-snmp-7.3.6-1.el7.remi.x86_64
php73-php-gd-7.3.6-1.el7.remi.x86_64
php73-php-json-7.3.6-1.el7.remi.x86_64
php73-php-soap-7.3.6-1.el7.remi.x86_64
php73-php-recode-7.3.6-1.el7.remi.x86_64
php73-php-pecl-crypto-0.3.1-5.el7.remi.x86_64
php73-php-common-7.3.6-1.el7.remi.x86_64
php73-php-opcache-7.3.6-1.el7.remi.x86_64
php73-php-pecl-geoip-1.1.1-6.el7.remi.x86_641234567891011121314151617
```

找到：php73-php-fpm-7.3.6-1.el7.remi.x86_64安装位置

```shell
[root@xxx etc]# rpm -ql php73-php-fpm-7.3.6-1.el7.remi.x86_64
/etc/logrotate.d/php73-php-fpm
/etc/opt/remi/php73/php-fpm.conf
/etc/opt/remi/php73/php-fpm.d
/etc/opt/remi/php73/php-fpm.d/www.conf
/etc/opt/remi/php73/sysconfig/php-fpm
/etc/systemd/system/php73-php-fpm.service.d
/opt/remi/php73/root/usr/sbin/php-fpm
/opt/remi/php73/root/usr/share/doc/php73-php-fpm-7.3.6
/opt/remi/php73/root/usr/share/doc/php73-php-fpm-7.3.6/php-fpm.conf.default
/opt/remi/php73/root/usr/share/doc/php73-php-fpm-7.3.6/www.conf.default
/opt/remi/php73/root/usr/share/fpm
/opt/remi/php73/root/usr/share/fpm/status.html
/opt/remi/php73/root/usr/share/licenses/php73-php-fpm-7.3.6
/opt/remi/php73/root/usr/share/licenses/php73-php-fpm-7.3.6/fpm_LICENSE
/opt/remi/php73/root/usr/share/man/man8/php-fpm.8.gz
/usr/lib/systemd/system/php73-php-fpm.service
/var/opt/remi/php73/lib/php/opcache
/var/opt/remi/php73/lib/php/session
/var/opt/remi/php73/lib/php/wsdlcache
/var/opt/remi/php73/log/php-fpm
/var/opt/remi/php73/run/php-fpm123456789101112131415161718192021
```

查找php.ini位置：

```shell
[root@xxx etc]# find /etc/opt/remi/php73 -name php.ini
/etc/opt/remi/php73/php.ini1
```

编辑/etc/opt/remi/php73/php.ini替换换 ;cgi.fix_pathinfo=1 为 cgi.fix_pathinfo=0 快捷命令：

```shell
sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/' /etc/opt/remi/php73/php.ini
```

重启php73-php-fpm

```shell
systemctl restart php73-php-fpm
```

更多操作：

```shell
systemctl restart php73-php-fpm #重启
systemctl start php73-php-fpm #启动
systemctl stop php73-php-fpm #关闭
systemctl status php73-php-fpm #检查状态123
```

查看 PHP
验证一下是否安装成功：

```shell
root@mf88.biz-service:~# php73 -v
PHP 7.3.0-1+(cli) (built: Dec  6 2018 20:24:55) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.0-dev, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.0-1+ubuntu18.04.1+deb.sury.org+1, Copyright (c) 1999-2018, by Zend Technologies1234
```

更新 PHP
运行下面的命令系统就会更新所有可以更新的软件包括 PHP

```java
yum update
```

安装更多组件
上面的一条命令安装 PHP 只是安装了部分 PHP 拓展，更多的软件可见：

```shell
# yum search php73
Updating Subscription Management repositories.
Last metadata expiration check: 0:27:54 ago on Wed 15 May 2019 10:39:52 AM EDT.
============================================================================================= Name Exactly Matched: php73 ==============================================================================================
php73.x86_64 : Package that installs PHP 7.3
php73.x86_64 : Package that installs PHP 7.3
============================================================================================ Name & Summary Matched: php73 =============================================================================================
php73-syspaths.x86_64 : System-wide wrappers for the php73 package
php73-syspaths.x86_64 : System-wide wrappers for the php73 package
php73-scldevel.x86_64 : Package shipping development files for php73
php73-scldevel.x86_64 : Package shipping development files for php73
php73-php-zstd-devel.x86_64 : php73-php-zstd developer files (header)
php73-runtime.x86_64 : Package that handles php73 Software Collection.
php73-runtime.x86_64 : Package that handles php73 Software Collection.
php73-runtime.x86_64 : Package that handles php73 Software Collection.
php73-php-pecl-psr-devel.x86_64 : php73-php-pecl-psr developer files (header)
php73-php-pecl-psr-devel.x86_64 : php73-php-pecl-psr developer files (header)
php73-php-pecl-raphf-devel.x86_64 : php73-php-pecl-raphf developer files (header)
php73-php-pecl-raphf-devel.x86_64 : php73-php-pecl-raphf developer files (header)
php73-php-pecl-propro-devel.x86_64 : php73-php-pecl-propro developer files (header)
php73-php-pecl-yaconf-devel.x86_64 : php73-php-pecl-yaconf developer files (header)
php73-php-pecl-propro-devel.x86_64 : php73-php-pecl-propro developer files (header)
php73-php-pecl-yaconf-devel.x86_64 : php73-php-pecl-yaconf developer files (header)
php73-php-pecl-xmldiff-devel.x86_64 : php73-php-pecl-xmldiff developer files (header)
php73-php-pecl-swoole4-devel.x86_64 : php73-php-pecl-swoole4 developer files (header)
php73-php-pecl-xmldiff-devel.x86_64 : php73-php-pecl-xmldiff developer files (header)
php73-php-zephir-parser-devel.x86_64 : php73-php-zephir-parser developer files (headers)
php73-php-zephir-parser-devel.x86_64 : php73-php-zephir-parser developer files (headers)
php73-php-pecl-handlebars-devel.x86_64 : php73-php-pecl-handlebars developer files (header)
================================================================================================= Name Matched: php73 ==================================================================================================
php73-php.x86_64 : PHP scripting language for creating dynamic web sites
php73-php.x86_64 : PHP scripting language for creating dynamic web sites
php73-build.x86_64 : Package shipping basic build configuration
……
```