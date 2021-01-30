# 操作系统版本：centos7

[root@linux-node3 ~]# uname -r
3.10.0-514.el7.x86_64

# 一、将现有的yum源卸载

1.查看当前yum版本：

```
rpm -qa yum
```

2.卸载yum组件：

```
rpm -qa | grep yum | xargs rpm -e --nodeps
```

注：这一步一定要执行，而且要将所有的组件卸载掉，如果卸载不干净，后面安装会有问题
检查是否删除：

```
rpm -qa yum
```

# 二、下载和yum有关的rpm包

## 1.源地址：

```
http://mirror.centos.org/
```

## 2.所需的三个包:

yum-3.4.3-163.el7.centos.noarch.rpm
yum-metadata-parser-1.1.4-10.el7.x86_64.rpm
yum-plugin-fastestmirror-1.1.31-52.el7.noarch.rpm

## 3.完整地址：

```
http://mirror.centos.org/centos-7/7/os/x86_64/Packages/yum-3.4.3-163.el7.centos.noarch.rpm
http://mirror.centos.org/centos-7/7/os/x86_64/Packages/yum-metadata-parser-1.1.4-10.el7.x86_64.rpm
http://mirror.centos.org/centos-7/7/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-52.el7.noarch.rp
```

注：可使用wget命令：

```
wget http://mirror.centos.org/centos-7/7/os/x86_64/Packages/yum-3.4.3-163.el7.centos.noarch.rpm
```

# 三、安装

1.查看下载：

```
 ls 
```

2.安装：

```
 rpm -ivh yum-* 
```

3.检查安装：

```
rpm -qa yum
```

备注：
1、不同的操作系统版本，要从官网上下载不同的rpm包，官网地址”http://mirror.centos.org/centos/“，然后根据自己操作系统的版本选择即可。 国内也有源地址，比如网易云mirrors.163.com，阿里云https://opsx.alibaba.com/mirror
2、如果出现安装好以后，yum还是不能用的情况，那肯是是原有的yum没有删除干净，再次执行以下命令，重新安装yum即可。

rpm -qa|grep yum|xargs rpm -e --nodeps
3、如果有需要导入证书的情况，请从官网上找到链接，导入即可

rpm --import http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-7

参考链接：https://www.jianshu.com/p/3111ba9c1a61