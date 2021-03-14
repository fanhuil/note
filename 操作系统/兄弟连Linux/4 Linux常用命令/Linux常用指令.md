# 文件处理命令

## ls

> 显示目录文件

```bash
ls -dalih

-d	查看目录属性
-a	显示所有文件，包括隐藏文件
-l	详细信息显示
-i	查询inode
-h	人性化显示
```

## mkdir

> 创建新目录

```bash
mkdir [-p] dirname

-p 递归创建
```

## cd 

> 切换目录

```bash
cd [dirname]
```

## pwd

> 显示当前目录的绝对路径

```bash
pwd
```

## rmdir

> 删除空目录

```bash
rmdir dirname
```

## cp

> 复制文件或目录

```bash
cp [原文件或目录] [目标目录]

-r 复制目录
-p 保留文件属性
```

## mv

> 剪切文件、改名

```bash
mv [原文件或目录] [目标目录]
```

## rm

> 删除文件

```bash
rm [文件或目录]

-r 删除目录
-f 强制删除
```

## touch

> 创建空白文件

```bash
touch [filename]
```

## cat

> 显示文件内容

```bash
cat [filename]

-n 显示行号
```

## more

> 分页显示文件内容

```bash
more [filename]

(空格)或f	翻页
(Enter)   换行
q或Q		 退出
```

## less

> 分页显示文件内容，可向上翻页

```bash
less [filename]


/keyword	搜索
n			下一个关键词
pgdn		下一页
pgup		上一页
```

## head/tail

> 显示文件前面/后面几行

```bash
head/tail [filename]

-n	指定行数

-f 动态显示末尾内容
```

## ln

> 生成链接文件

```bash
ls [原文件] [目标文件]

-s 创建软连接
```



# 权限管理命令

## chmod

> 改变文件或者目录权限

```bash
chmod [{ugoa}{+-=}{rwx}] [文件或者目录]
	  [mode=421] [文件或目录]
	  -R 递归修改
```

| 代表字符 | 权限     | 对文件的含义     | 对目录的含义               |
| -------- | -------- | ---------------- | -------------------------- |
| r        | 读权限   | 可以查看文件内容 | 可以列出目录中的内容       |
| w        | 写权限   | 可以修改文件内容 | 可以在目录中创建、删除文件 |
| x        | 执行权限 | 可以执行文件     | 可以进入目录               |

一般目录r和x同时赋予

## chown

> 改变文件或者目录的所有者

```bash
chown [用户] [文件或目录]

# 只有超级管理员可以修改
```

## chgrp

> 改变文件或目录的所属组

```bash
chagrp [用户组] [文件或目录]
```

## umask

> 显示、设置文件的缺省权限

```bash
umask [-S]
```



# 文件搜索命令

## find

> 文件搜索

```bash
find [搜索范围] [匹配条件]

-name option 			# 根据条件查找
-size +filesize 		# 根据文件大小查找
-user nameusername		# 根据所有者进行查找
-cmin/-amin/mmin -5 	# 查找五分钟内被修改过的文件
-type 					# 查找指定类型的文件
```

## locate

> 在文件库中查找文件

```bash
locate filename

updatedb # 更新文件资料库
```

## which

> 搜索命令所在目录及别名

```bash
which 命令
```

## whereis

> 搜索命令所在目录及帮助文档路径

```bash
whereis [命令名称]
```

## grep

> 在文件中搜寻字符串匹配的行并输出

```bash
grep keyword filepath

-i 不区分大小写
-v 排除指定字符串


# 去掉以#开头的文件行
grep -v ^# filepath 
```



# 帮助命令

## man

> 获得帮助信息

```bash
man [命令或者配置文件]

1 命令的帮助
5 配置文件的帮助

whatis 命令
info
命令 --help
```

## help

> 获得shell内置命令的帮助信息

```bash
help umask

apropos 配置文件
```



# 用户管理命令

## useradd

> 添加新用户

```bash
useradd username
```

## passwd

> 设置用户密码

```bash
passwd user
```

## who

> 查看登录用户信息

```bash
who
```



# 压缩解压命令

## gzip

> 压缩文件

```bash
# 后缀.gz 不能压缩目录，不保留源文件
gzip filename
```

## gunzip

> 解压缩.gz的压缩文件

```bash
gunzip filename.gz
```

## tar

> 打包目录

```bash
# 后缀名.tar
tar 选项[-zcf] [压缩后文件名] [目录]
 -c 打包
 -v 显示详细信息
 -f 指定文件名
 -z 打包同时压缩
 
 
 -x 解包
 -v 显示详细信息
 -f 指定解压文件
 -z 解压缩
```

## zip

> 压缩文件或目录

```bash
zip 选项[-r] [压缩后文件名] [文件或目录] -r 压缩目录
```

## bzip2

> 压缩文件

```bash
bzip2 选项 [-k][文件] -k 产生压缩文件后保留
```



# 网络命令

## write

> 给用户发信息，以Ctrl+D保存结束

```bash
write username
```

## wall

> 发广播信息

```bash
wall [message]
```

## ping

> 测试网络连通性

```bash
ping 选项 IP地址(域名)
```

## ifconfig

> 查看和设置网卡信息

```bash

```

## mail

> 查看发送电子邮件

```bash
mail username
```

## last

> 列出目前与过去登入系统的用户信息

```bash
last

laslog -u uid
```

## tracetoute

> 显示数据包到主机间的路径

```bash
traceroute www.baidu.com
```

## netstat

> 显示网络相关信息
>
> -t TCP协议
>
> -u UDP协议
>
> -l 监听
>
> -r 路由
>
> -l 显示IP地址和端口号

```bash
netstat [选项]

netstat -tlun
netstat -an
netstat -rn
```

## setup

> 配置网络

```bash
setup
```



## mount

> 

```bash

```



# 关机重启命令

## shutdown

> 关机
>
> -c 取消
>
> -h 关机
>
> -r 重启

```bash
halt
poweroff
init 0

# 重启
ruboot
init 6
```



# 其他指令

## cat /etc/inittab

> 修改系统默认运行级别

## runlevel

> 查询系统运行级别







