**检查是否有旧的安装包，有就直接卸载**

```shell
# 检查mariadb安装包
[root@wing ~]# rpm -qa | grep -i mysql
[root@wing ~]# rpm -qa | grep -i mariadb
mariadb-libs-5.5.50-1.el7_2.x86_64
# 卸载查找到的mariadb安装包
[root@wing ~]rpm -e mariadb-libs-5.5.50-1.el7_2.x86_64
#注：如上命令删除不成功，有别的软件依赖无法删除时，如下操作：
[root@wing ~]yum remove  mariadb-libs-5.5.50-1.el7_2.x86_64
```

下载地址：http://downloads.mysql.com/archives/community/ 