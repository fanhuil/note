# 编译安装libiconv

```shell
# 下载压缩包
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz
# 解压压缩包
tar xvzf libiconv-1.14.tar.gz
# 进入解压后目录
cd libiconv-1.14
# 执行配置并指定需要的参数
./configure --prefix=/usr/local/libiconv-1.14
# 安装
make && make install
# 加上软连接
ln -s  /usr/local/libiconv-1.14 /usr/local/libiconv
```

