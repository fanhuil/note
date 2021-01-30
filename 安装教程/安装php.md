# yum安装 

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



# 源码编译安装

## 1、环境准备

​	   **centos7**

　　**php-7.4.8.tar.gz**

## 2、开始安装

　　**添加php用户，用于运行php-fpm进程**

```shell
useradd php7 -s /sbin/nologin # 根据版本添加用户名，可以自定义
```

　　![img](yum%E5%AE%89%E8%A3%85php/1453281-20200727232851240-2118901584.png)

   　**解压php源码包**

```shell
tar -zxf php-7.4.8.tar.gz
```

　　![img](yum%E5%AE%89%E8%A3%85php/1453281-20200727232941809-1351004521.png)

 　   **安装依赖包**

```shell
yum install libxml2-devel sqlite-devel libcurl-devel oniguruma-devel libpng-devel libjpeg-devel freetype-devel libzip-devel openssl-devel -y
```

​	   **编译参数**

```shell
#`configure'将PHP 7.4.14配置为适应多种系统。
`configure' configures PHP 7.4.14 to adapt to many kinds of systems.

# 使用方法
Usage: ./configure [OPTION]... [VAR=VALUE]...

# 要分配环境变量（例如CC，CFLAGS ...），请将其指定为 VAR = VALUE。 有关某些有用变量的说明，请参见下文。
To assign environment variables (e.g., CC, CFLAGS...), specify them as
VAR=VALUE.  See below for descriptions of some of the useful variables.

# 选项的默认值在方括号中指定。
Defaults for the options are specified in brackets.

Configuration:
  -h, --help              display this help and exit # 显示此帮助并退出
      --help=short        display options specific to this package # 显示特定于此软件包的选项
      --help=recursive    display the short help of all the included packages # 显示所有包含的软件包的简短帮助
  -V, --version           display version information and exit # 显示版本信息并退出
  -q, --quiet, --silent   do not print `checking ...` messages # 不打印“检查中...”消息
      --cache-file=FILE   cache test results in FILE [disabled] # 在FILE中缓存测试结果[已禁用]
  -C, --config-cache      alias for `--cache-file=config.cache` # --cache-file = config.cache的别名
  -n, --no-create         do not create output files # 不创建输出文件
      --srcdir=DIR        find the sources in DIR [configure dir or `..`] # 在DIR中找到源文件[configure dir或`..']

Installation directories:
  --prefix=PREFIX         install architecture-independent files in PREFIX 
  						  [/usr/local] # 在PREFIX [/usr/local]中安装与体系结构无关的文件
  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX 
  						  [PREFIX] # 在EPREFIX中安装与体系结构相关的文件[字首]

# 默认情况下，“ make install”将安装所有文件
# / usr / local / bin，/ usr / local / lib等。您可以指定
# 使用`--prefix'的`/ usr / local'以外的安装前缀，
# 例如`--prefix = $ HOME'。
By default, `make install' will install all the files in
`/usr/local/bin', `/usr/local/lib' etc.  You can specify
an installation prefix other than `/usr/local' using `--prefix',
for instance `--prefix=$HOME'.

# 为了更好地控制，请使用以下选项。
For better control, use the options below.

# 微调安装目录
Fine tuning of the installation directories:
  --bindir=DIR            user executables [EPREFIX/bin]
  --sbindir=DIR           system admin executables [EPREFIX/sbin]
  --libexecdir=DIR        program executables [EPREFIX/libexec]
  --sysconfdir=DIR        read-only single-machine data [PREFIX/etc]
  --sharedstatedir=DIR    modifiable architecture-independent data [PREFIX/com]
  --localstatedir=DIR     modifiable single-machine data [PREFIX/var]
  --libdir=DIR            object code libraries [EPREFIX/lib]
  --includedir=DIR        C header files [PREFIX/include]
  --oldincludedir=DIR     C header files for non-gcc [/usr/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
  --infodir=DIR           info documentation [DATAROOTDIR/info]
  --localedir=DIR         locale-dependent data [DATAROOTDIR/locale]
  --mandir=DIR            man documentation [DATAROOTDIR/man]
  --docdir=DIR            documentation root [DATAROOTDIR/doc/php]
  --htmldir=DIR           html documentation [DOCDIR]
  --dvidir=DIR            dvi documentation [DOCDIR]
  --pdfdir=DIR            pdf documentation [DOCDIR]
  --psdir=DIR             ps documentation [DOCDIR]

Program names:
  --program-prefix=PREFIX            prepend PREFIX to installed program names
  --program-suffix=SUFFIX            append SUFFIX to installed program names
  --program-transform-name=PROGRAM   run sed PROGRAM on installed program names

System types:
  --build=BUILD     configure for building on BUILD [guessed]
  --host=HOST       cross-compile to build programs to run on HOST [BUILD]
  --target=TARGET   configure for building compilers for TARGET [HOST]

Optional Features and Packages:
  --disable-option-checking  ignore unrecognized --enable/--with options
  --disable-FEATURE       do not include FEATURE (same as --enable-FEATURE=no)
  --enable-FEATURE[=ARG]  include FEATURE [ARG=yes]
  --with-PACKAGE[=ARG]    use PACKAGE [ARG=yes]
  --without-PACKAGE       do not use PACKAGE (same as --with-PACKAGE=no)
  --with-libdir=NAME      Look for libraries in .../NAME rather than .../lib
  --disable-rpath         Disable passing additional runtime library search
                          paths
  --enable-re2c-cgoto     Enable -g flag to re2c to use computed goto gcc
                          extension
  --disable-gcc-global-regs
                          whether to enable GCC global register variables

SAPI modules:

  --with-apxs2[=FILE]     Build shared Apache 2 handler module. FILE is the
                          optional pathname to the Apache apxs tool [apxs]
  --disable-cli           Disable building CLI version of PHP (this forces
                          --without-pear)
  --enable-embed[=TYPE]   EXPERIMENTAL: Enable building of embedded SAPI
                          library TYPE is either 'shared' or 'static'.
                          [TYPE=shared]
  --enable-fpm            Enable building of the fpm SAPI executable
  --with-fpm-user[=USER]  Set the user for php-fpm to run as. (default:
                          nobody)
  --with-fpm-group[=GRP]  Set the group for php-fpm to run as. For a system
                          user, this should usually be set to match the fpm
                          username (default: nobody)
  --with-fpm-systemd      Activate systemd integration
  --with-fpm-acl          Use POSIX Access Control Lists
  --enable-litespeed      Build PHP as litespeed module
  --enable-phpdbg         Build phpdbg
  --enable-phpdbg-webhelper
                          Build phpdbg web SAPI support
  --enable-phpdbg-debug   Build phpdbg in debug mode
  --enable-phpdbg-readline
                          Enable readline support in phpdbg (depends on static
                          ext/readline)
  --disable-cgi           Disable building CGI version of PHP
  --with-valgrind         Enable valgrind support

General settings:

  --enable-gcov           Enable GCOV code coverage - FOR DEVELOPERS ONLY!!
  --enable-debug          Compile with debugging symbols
  --enable-rtld-now       Use dlopen with RTLD_NOW instead of RTLD_LAZY
  --with-layout=TYPE      Set how installed files will be laid out. Type can
                          be either PHP or GNU [PHP]
  --with-config-file-path=PATH
                          Set the path in which to look for php.ini
                          [PREFIX/lib]
  --with-config-file-scan-dir=PATH
                          Set the path where to scan for configuration files
  --enable-sigchild       Enable PHP's own SIGCHLD handler
  --enable-libgcc         Enable explicitly linking against libgcc
  --disable-short-tags    Disable the short-form <? start tag by default
  --enable-dmalloc        Enable dmalloc
  --disable-ipv6          Disable IPv6 support
  --enable-dtrace         Enable DTrace support
  --enable-fd-setsize     Set size of descriptor sets
  --enable-werror         Enable -Werror

Extensions:

  --with-EXTENSION=shared[,PATH]

    NOTE: Not all extensions can be build as 'shared'.

    Example: --with-foobar=shared,/usr/local/foobar/

      o Builds the foobar extension as shared extension.
      o foobar package install prefix is /usr/local/foobar/


  --disable-all           Disable all extensions which are enabled by default
  --without-libxml        Build without LIBXML support
  --with-openssl          Include OpenSSL support (requires OpenSSL >= 1.0.1)
  --with-kerberos         OPENSSL: Include Kerberos support
  --with-system-ciphers   OPENSSL: Use system default cipher list instead of
                          hardcoded value
  --with-external-pcre    Use external library for PCRE support
  --with-pcre-jit         Enable PCRE JIT functionality
  --without-sqlite3       Do not include SQLite3 support.
  --with-zlib             Include ZLIB support (requires zlib >= 1.2.0.4)
  --enable-bcmath         Enable bc style precision math functions
  --with-bz2[=DIR]        Include BZip2 support
  --enable-calendar       Enable support for calendar conversion
  --disable-ctype         Disable ctype functions
  --with-curl             Include cURL support
  --enable-dba            Build DBA with bundled modules. To build shared DBA
                          extension use --enable-dba=shared
  --with-qdbm[=DIR]       DBA: QDBM support
  --with-gdbm[=DIR]       DBA: GDBM support
  --with-ndbm[=DIR]       DBA: NDBM support
  --with-db4[=DIR]        DBA: Oracle Berkeley DB 4.x or 5.x support
  --with-db3[=DIR]        DBA: Oracle Berkeley DB 3.x support
  --with-db2[=DIR]        DBA: Oracle Berkeley DB 2.x support
  --with-db1[=DIR]        DBA: Oracle Berkeley DB 1.x support/emulation
  --with-dbm[=DIR]        DBA: DBM support
  --with-tcadb[=DIR]      DBA: Tokyo Cabinet abstract DB support
  --with-lmdb[=DIR]       DBA: Lightning memory-mapped database support
  --without-cdb[=DIR]     DBA: CDB support (bundled)
  --disable-inifile       DBA: INI support (bundled)
  --disable-flatfile      DBA: FlatFile support (bundled)
  --disable-dom           Disable DOM support
  --with-enchant          Include Enchant support
  --enable-exif           Enable EXIF (metadata from images) support
  --with-ffi              Include FFI support
  --disable-fileinfo      Disable fileinfo support
  --disable-filter        Disable input filter support
  --enable-ftp            Enable FTP support
  --with-openssl-dir      FTP: Whether to enable FTP SSL support without
                          ext/openssl
  --enable-gd             Include GD support
  --with-external-gd      Use external libgd
  --with-webp             GD: Enable WEBP support (only for bundled libgd)
  --with-jpeg             GD: Enable JPEG support (only for bundled libgd)
  --with-xpm              GD: Enable XPM support (only for bundled libgd)
  --with-freetype         GD: Enable FreeType 2 support (only for bundled
                          libgd)
  --enable-gd-jis-conv    GD: Enable JIS-mapped Japanese font support (only
                          for bundled libgd)
  --with-gettext[=DIR]    Include GNU gettext support
  --with-gmp[=DIR]        Include GNU MP support
  --with-mhash            Include mhash support
  --without-iconv[=DIR]   Exclude iconv support
  --with-imap[=DIR]       Include IMAP support. DIR is the c-client install
                          prefix
  --with-kerberos         IMAP: Include Kerberos support
  --with-imap-ssl         IMAP: Include SSL support
  --enable-intl           Enable internationalization support
  --disable-json          Disable JavaScript Object Serialization support
  --with-ldap[=DIR]       Include LDAP support
  --with-ldap-sasl        LDAP: Build with Cyrus SASL support
  --enable-mbstring       Enable multibyte string support
  --disable-mbregex       MBSTRING: Disable multibyte regex support
  --with-mysqli[=FILE]    Include MySQLi support. FILE is the path to
                          mysql_config. If no value or mysqlnd is passed as
                          FILE, the MySQL native driver will be used
  --with-mysql-sock[=SOCKPATH]
                          MySQLi/PDO_MYSQL: Location of the MySQL unix socket
                          pointer. If unspecified, the default locations are
                          searched
  --with-oci8[=DIR]       Include Oracle Database OCI8 support. DIR defaults
                          to $ORACLE_HOME. Use
                          --with-oci8=instantclient,/path/to/instant/client/lib
                          to use an Oracle Instant Client installation
  --with-odbcver[=HEX]    Force support for the passed ODBC version. A hex
                          number is expected, default 0x0350. Use the special
                          value of 0 to prevent an explicit ODBCVER to be
                          defined.
  --with-adabas[=DIR]     Include Adabas D support [/usr/local]
  --with-sapdb[=DIR]      Include SAP DB support [/usr/local]
  --with-solid[=DIR]      Include Solid support [/usr/local/solid]
  --with-ibm-db2[=DIR]    Include IBM DB2 support [/home/db2inst1/sqllib]
  --with-empress[=DIR]    Include Empress support $EMPRESSPATH (Empress
                          Version >= 8.60 required)
  --with-empress-bcs[=DIR]
                          Include Empress Local Access support $EMPRESSPATH
                          (Empress Version >= 8.60 required)
  --with-custom-odbc[=DIR]
                          Include user defined ODBC support. DIR is ODBC
                          install base directory [/usr/local]. Make sure to
                          define CUSTOM_ODBC_LIBS and have some odbc.h in your
                          include dirs. For example, you should define
                          following for Sybase SQL Anywhere 5.5.00 on QNX,
                          prior to running this configure script:
                          CPPFLAGS="-DODBC_QNX -DSQLANY_BUG" LDFLAGS=-lunix
                          CUSTOM_ODBC_LIBS="-ldblib -lodbc"
  --with-iodbc            Include iODBC support
  --with-esoob[=DIR]      Include Easysoft OOB support
                          [/usr/local/easysoft/oob/client]
  --with-unixODBC         Include unixODBC support
  --with-dbmaker[=DIR]    Include DBMaker support
  --disable-opcache       Disable Zend OPcache support
  --disable-huge-code-pages
                          Disable copying PHP CODE pages into HUGE PAGES
  --enable-pcntl          Enable pcntl support (CLI/CGI only)
  --disable-pdo           Disable PHP Data Objects support
  --with-pdo-dblib[=DIR]  PDO: DBLIB-DB support. DIR is the FreeTDS home
                          directory
  --with-pdo-firebird[=DIR]
                          PDO: Firebird support. DIR is the Firebird base
                          install directory [/opt/firebird]
  --with-pdo-mysql[=DIR]  PDO: MySQL support. DIR is the MySQL base directory.
                          If no value or mysqlnd is passed as DIR, the MySQL
                          native driver will be used
  --with-zlib-dir[=DIR]   PDO_MySQL: Set the path to libz install prefix
  --with-pdo-oci[=DIR]    PDO: Oracle OCI support. DIR defaults to
                          $ORACLE_HOME. Use
                          --with-pdo-oci=instantclient,/path/to/instant/client/lib
                          for an Oracle Instant Client installation.
  --with-pdo-odbc=flavour,dir
                          PDO: Support for 'flavour' ODBC driver. The include
                          and lib dirs are looked for under 'dir'. The
                          'flavour' can be one of: ibm-db2, iODBC, unixODBC,
                          generic. If ',dir' part is omitted, default for the
                          flavour you have selected will be used. e.g.:
                          --with-pdo-odbc=unixODBC will check for unixODBC
                          under /usr/local. You may attempt to use an
                          otherwise unsupported driver using the 'generic'
                          flavour. The syntax for generic ODBC support is:
                          --with-pdo-odbc=generic,dir,libname,ldflags,cflags.
                          When built as 'shared' the extension filename is
                          always pdo_odbc.so
  --with-pdo-pgsql[=DIR]  PDO: PostgreSQL support. DIR is the PostgreSQL base
                          install directory or the path to pg_config
  --without-pdo-sqlite    PDO: sqlite 3 support.
  --with-pgsql[=DIR]      Include PostgreSQL support. DIR is the PostgreSQL
                          base install directory or the path to pg_config
  --disable-phar          Disable phar support
  --disable-posix         Disable POSIX-like functions
  --with-pspell[=DIR]     Include PSPELL support. GNU Aspell version 0.50.0 or
                          higher required
  --with-libedit          Include libedit readline replacement (CLI/CGI only)
  --with-readline[=DIR]   Include readline support (CLI/CGI only)
  --disable-session       Disable session support
  --with-mm[=DIR]         SESSION: Include mm support for session storage
  --enable-shmop          Enable shmop support
  --disable-simplexml     Disable SimpleXML support
  --with-snmp[=DIR]       Include SNMP support
  --enable-soap           Enable SOAP support
  --enable-sockets        Enable sockets support
  --with-sodium           Include sodium support
  --with-password-argon2[=DIR]
                          Include Argon2 support in password_*. DIR is the
                          Argon2 shared library path
  --enable-sysvmsg        Enable sysvmsg support
  --enable-sysvsem        Enable System V semaphore support
  --enable-sysvshm        Enable the System V shared memory support
  --with-tidy[=DIR]       Include TIDY support
  --disable-tokenizer     Disable tokenizer support
  --disable-xml           Disable XML support
  --with-expat            XML: use expat instead of libxml2
  --disable-xmlreader     Disable XMLReader support
  --with-xmlrpc[=DIR]     Include XMLRPC-EPI support
  --with-expat            XMLRPC-EPI: use expat instead of libxml2
  --with-iconv-dir=DIR    XMLRPC-EPI: iconv dir for XMLRPC-EPI
  --disable-xmlwriter     Disable XMLWriter support
  --with-xsl              Build with XSL support
  --enable-zend-test      Enable zend-test extension
  --with-zip              Include Zip read/write support
  --enable-mysqlnd        Enable mysqlnd explicitly, will be done implicitly
                          when required by other extensions
  --disable-mysqlnd-compression-support
                          Disable support for the MySQL compressed protocol in
                          mysqlnd

PEAR:

  --with-pear[=DIR]       Install PEAR in DIR [PREFIX/lib/php]

Zend:

  --enable-maintainer-zts Enable thread safety - for code maintainers only!!
  --disable-inline-optimization
                          If building zend_execute.lo fails, try this switch
  --disable-zend-signals  whether to enable zend signal handling

TSRM:

  --with-tsrm-pth[=pth-config]
                          Use GNU Pth
  --with-tsrm-st          Use SGI's State Threads
  --with-tsrm-pthreads    Use POSIX threads (default)

Libtool:

  --enable-shared=PKGS    Build shared libraries default=yes
  --enable-static=PKGS    Build static libraries default=yes
  --enable-fast-install=PKGS
                          Optimize for fast installation default=yes
  --with-gnu-ld           Assume the C compiler uses GNU ld default=no
  --disable-libtool-lock  Avoid locking (might break parallel builds)
  --with-pic              Try to use only PIC/non-PIC objects default=use both
  --with-tags=TAGS        Include additional configurations automatic


Some influential environment variables:
  PKG_CONFIG  path to pkg-config utility
  PKG_CONFIG_PATH
              directories to add to pkg-config's search path
  PKG_CONFIG_LIBDIR
              path overriding pkg-config's built-in search path
  CC          C compiler command
  CFLAGS      C compiler flags
  LDFLAGS     linker flags, e.g. -L<lib dir> if you have libraries in a
              nonstandard directory <lib dir>
  LIBS        libraries to pass to the linker, e.g. -l<library>
  CPPFLAGS    (Objective) C/C++ preprocessor flags, e.g. -I<include dir> if
              you have headers in a nonstandard directory <include dir>
  CPP         C preprocessor
  SYSTEMD_CFLAGS
              C compiler flags for SYSTEMD, overriding pkg-config
  SYSTEMD_LIBS
              linker flags for SYSTEMD, overriding pkg-config
  VALGRIND_CFLAGS
              C compiler flags for VALGRIND, overriding pkg-config
  VALGRIND_LIBS
              linker flags for VALGRIND, overriding pkg-config
  LIBXML_CFLAGS
              C compiler flags for LIBXML, overriding pkg-config
  LIBXML_LIBS linker flags for LIBXML, overriding pkg-config
  KERBEROS_CFLAGS
              C compiler flags for KERBEROS, overriding pkg-config
  KERBEROS_LIBS
              linker flags for KERBEROS, overriding pkg-config
  OPENSSL_CFLAGS
              C compiler flags for OPENSSL, overriding pkg-config
  OPENSSL_LIBS
              linker flags for OPENSSL, overriding pkg-config
  PCRE2_CFLAGS
              C compiler flags for PCRE2, overriding pkg-config
  PCRE2_LIBS  linker flags for PCRE2, overriding pkg-config
  SQLITE_CFLAGS
              C compiler flags for SQLITE, overriding pkg-config
  SQLITE_LIBS linker flags for SQLITE, overriding pkg-config
  ZLIB_CFLAGS C compiler flags for ZLIB, overriding pkg-config
  ZLIB_LIBS   linker flags for ZLIB, overriding pkg-config
  CURL_CFLAGS C compiler flags for CURL, overriding pkg-config
  CURL_LIBS   linker flags for CURL, overriding pkg-config
  CURL_FEATURES
              value of supported_features for libcurl, overriding pkg-config
  ENCHANT_CFLAGS
              C compiler flags for ENCHANT, overriding pkg-config
  ENCHANT_LIBS
              linker flags for ENCHANT, overriding pkg-config
  FFI_CFLAGS  C compiler flags for FFI, overriding pkg-config
  FFI_LIBS    linker flags for FFI, overriding pkg-config
  PNG_CFLAGS  C compiler flags for PNG, overriding pkg-config
  PNG_LIBS    linker flags for PNG, overriding pkg-config
  WEBP_CFLAGS C compiler flags for WEBP, overriding pkg-config
  WEBP_LIBS   linker flags for WEBP, overriding pkg-config
  JPEG_CFLAGS C compiler flags for JPEG, overriding pkg-config
  JPEG_LIBS   linker flags for JPEG, overriding pkg-config
  XPM_CFLAGS  C compiler flags for XPM, overriding pkg-config
  XPM_LIBS    linker flags for XPM, overriding pkg-config
  FREETYPE2_CFLAGS
              C compiler flags for FREETYPE2, overriding pkg-config
  FREETYPE2_LIBS
              linker flags for FREETYPE2, overriding pkg-config
  GDLIB_CFLAGS
              C compiler flags for GDLIB, overriding pkg-config
  GDLIB_LIBS  linker flags for GDLIB, overriding pkg-config
  ICU_CFLAGS  C compiler flags for ICU, overriding pkg-config
  ICU_LIBS    linker flags for ICU, overriding pkg-config
  CXX         C++ compiler command
  CXXFLAGS    C++ compiler flags
  CXXCPP      C++ preprocessor
  SASL_CFLAGS C compiler flags for SASL, overriding pkg-config
  SASL_LIBS   linker flags for SASL, overriding pkg-config
  ONIG_CFLAGS C compiler flags for ONIG, overriding pkg-config
  ONIG_LIBS   linker flags for ONIG, overriding pkg-config
  ODBC_CFLAGS C compiler flags for ODBC, overriding pkg-config
  ODBC_LIBS   linker flags for ODBC, overriding pkg-config
  EDIT_CFLAGS C compiler flags for EDIT, overriding pkg-config
  EDIT_LIBS   linker flags for EDIT, overriding pkg-config
  LIBSODIUM_CFLAGS
              C compiler flags for LIBSODIUM, overriding pkg-config
  LIBSODIUM_LIBS
              linker flags for LIBSODIUM, overriding pkg-config
  EXPAT_CFLAGS
              C compiler flags for EXPAT, overriding pkg-config
  EXPAT_LIBS  linker flags for EXPAT, overriding pkg-config
  XSL_CFLAGS  C compiler flags for XSL, overriding pkg-config
  XSL_LIBS    linker flags for XSL, overriding pkg-config
  EXSLT_CFLAGS
              C compiler flags for EXSLT, overriding pkg-config
  EXSLT_LIBS  linker flags for EXSLT, overriding pkg-config
  LIBZIP_CFLAGS
              C compiler flags for LIBZIP, overriding pkg-config
  LIBZIP_LIBS linker flags for LIBZIP, overriding pkg-config

Use these variables to override the choices made by `configure' or to help
it to find libraries and programs with nonstandard names/locations.

Report bugs to <https://bugs.php.net>.
PHP home page: <https://www.php.net>.
```



　　**编译参数参考**

```shell
# 刚才创建的用户名和群组
# --with-fpm-user=php 
# --with-fpm-group=php


./configure \
--prefix=/usr/local/php\
--with-config-file-path=/usr/local/php/etc \
--with-mhash \
--with-openssl \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-iconv \
--with-zlib \
--enable-inline-optimization \
--disable-debug \
--disable-rpath \
--enable-shared \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
--enable-gd \
--with-jpeg \
--with-freetype \
--enable-mbregex \
--enable-mbstring \
--enable-ftp \
--enable-pcntl \
--enable-sockets \
--enable-soap \
--without-pear \
--with-gettext \
--enable-session \
--with-curl \
--enable-opcache \
--enable-fpm \
--with-fpm-user=php \
--with-fpm-group=php \
--without-gdbm \
--enable-fast-install \
--disable-fileinfo
```

　　**编译没有问题就开始安装**

```
[root@swarm-node1 php-7.4.8]# make && make install 
```

　　**![img](yum%E5%AE%89%E8%A3%85php/1453281-20200728001104513-824783699.png)**

　　**编译安装完成**

 　![img](yum%E5%AE%89%E8%A3%85php/1453281-20200728002343318-1145420184.png)

　　**复制相关配置文件到php安装路径**

　　启动脚本：php-7.4.8/sapi/fpm/init.d.php-fpm

```
[root@swarm-node1 fpm]# cp init.d.php-fpm /usr/local/php/
```

　　php.ini配置文件：php-7.4.8/php.ini-production　

```
[root@swarm-node1 php-7.4.8]# cp php.ini-production /usr/local/php/etc/php.ini
```

 　**创建php-fpm.conf配置文件**

　　进到php的安装路径下面的etc

```
[root@swarm-node1 etc]# pwd
/usr/local/php/etc
[root@swarm-node1 etc]# 
[root@swarm-node1 etc]# ls
php-fpm.conf.default  php-fpm.d  php.ini
[root@swarm-node1 etc]# cp php-fpm.conf.default php-fpm.conf
```

　　![img](yum%E5%AE%89%E8%A3%85php/1453281-20200728002956005-1077883205.png)

 　   进入php-fpm.d目录，将里面的文件也重命名一下

```
[root@swarm-node1 php-fpm.d]# pwd
/usr/local/php/etc/php-fpm.d
[root@swarm-node1 php-fpm.d]# 
[root@swarm-node1 php-fpm.d]# ls
www.conf.default
[root@swarm-node1 php-fpm.d]# 
[root@swarm-node1 php-fpm.d]# cp www.conf.default www.conf
```

　　![img](yum%E5%AE%89%E8%A3%85php/1453281-20200728003216393-1262699643.png)

 　**启动php-fpm**

　　**执行init.d.php-fpm启动脚本，启动php-fpm**

```
[root@swarm-node1 php]# bash init.d.php-fpm start
```

　　**![img](yum%E5%AE%89%E8%A3%85php/1453281-20200728003426312-2111341540.png)**

　　**测试安装是否成功，可结合nginx配置一个index.php 写一个phpinfo的测试页面**

　　**nginx** **location 段配置文件**

```
　　  location ~ \.php$ {

            root           html;

            fastcgi_pass   127.0.0.1:9000;

            fastcgi_index  index.php;

            fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;

            include        fastcgi.conf;

        }
```

　　**index.php 配置文件**

```
[root@swarm-node1 html]# cat index.php 
<?php
phpinfo()
?>
[root@swarm-node1 html]# 
[root@swarm-node1 html]# 
```



# 多版本兼容

```shell
# 修改www.conf文件中的listen端口
# 将nginx.conf中的fastcgi_pass参数中的端口修改成为一致即可
```

