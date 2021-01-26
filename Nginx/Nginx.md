# Nginx安装

## yum方式安装

```shell
# 初始化基本目录
mkdir /soft/{app,logs,package/src} -p

[root@hecs-x-medium-2-linux-20200815230644 soft]# tree
.
├── app
├── logs
└── package
    └── src

4 directories, 0 files
```



```shell
# 配置Nginx官方Yum源
vim /etc/yum.repos.d/nginx.repo

yum makecache

# 写入下面内容
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/OS/7/$basearch/
gpgcheck=0
enabled=1

yum install nginx
```



## 源码编译方式安装

> 安装指令

```shell
# 下载压缩的安装包
nginx-1.10.2.tar.gz
# 解压压缩包
tar -xzf nginx-1.10.2.tar.gz
# 进入解压后的nginx安装包文件
cd nginx-1.10.2
tree
# 目录结构
.
├── auto
│   ├── cc
│   │   ├── acc
│   │   ├── bcc
│   │   ├── ccc
│   │   ├── clang
│   │   ├── conf
│   │   ├── gcc
│   │   ├── icc
│   │   ├── msvc
│   │   ├── name
│   │   ├── owc
│   │   └── sunc
│   ├── define
│   ├── endianness
│   ├── feature
│   ├── have
│   ├── have_headers
│   ├── headers
│   ├── include
│   ├── init
│   ├── install
│   ├── lib
│   │   ├── conf
│   │   ├── geoip
│   │   │   └── conf
│   │   ├── google-perftools
│   │   │   └── conf
│   │   ├── libatomic
│   │   │   ├── conf
│   │   │   └── make
│   │   ├── libgd
│   │   │   └── conf
│   │   ├── libxslt
│   │   │   └── conf
│   │   ├── make
│   │   ├── md5
│   │   │   ├── conf
│   │   │   ├── make
│   │   │   ├── makefile.bcc
│   │   │   ├── makefile.msvc
│   │   │   └── makefile.owc
│   │   ├── openssl
│   │   │   ├── conf
│   │   │   ├── make
│   │   │   ├── makefile.bcc
│   │   │   └── makefile.msvc
│   │   ├── pcre
│   │   │   ├── conf
│   │   │   ├── make
│   │   │   ├── makefile.bcc
│   │   │   ├── makefile.msvc
│   │   │   └── makefile.owc
│   │   ├── perl
│   │   │   ├── conf
│   │   │   └── make
│   │   ├── sha1
│   │   │   ├── conf
│   │   │   ├── make
│   │   │   ├── makefile.bcc
│   │   │   ├── makefile.msvc
│   │   │   └── makefile.owc
│   │   ├── test
│   │   └── zlib
│   │       ├── conf
│   │       ├── make
│   │       ├── makefile.bcc
│   │       ├── makefile.msvc
│   │       └── makefile.owc
│   ├── make
│   ├── module
│   ├── modules
│   ├── nohave
│   ├── options
│   ├── os
│   │   ├── conf
│   │   ├── darwin
│   │   ├── freebsd
│   │   ├── linux
│   │   ├── solaris
│   │   └── win32
│   ├── sources
│   ├── stubs
│   ├── summary
│   ├── threads
│   ├── types
│   │   ├── sizeof
│   │   ├── typedef
│   │   ├── uintptr_t
│   │   └── value
│   └── unix
├── CHANGES
├── CHANGES.ru
├── conf
│   ├── fastcgi.conf
│   ├── fastcgi_params
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types
│   ├── nginx.conf
│   ├── scgi_params
│   ├── uwsgi_params
│   └── win-utf
├── configure
├── contrib
│   ├── geo2nginx.pl
│   ├── README
│   ├── unicode2nginx
│   │   ├── koi-utf
│   │   ├── unicode-to-nginx.pl
│   │   └── win-utf
│   └── vim
│       ├── ftdetect
│       │   └── nginx.vim
│       ├── indent
│       │   └── nginx.vim
│       └── syntax
│           └── nginx.vim
├── html
│   ├── 50x.html
│   └── index.html
├── LICENSE
├── Makefile
├── man
│   └── nginx.8
├── objs
│   ├── autoconf.err
│   ├── Makefile
│   ├── nginx
│   ├── nginx.8
│   ├── ngx_auto_config.h
│   ├── ngx_auto_headers.h
│   ├── ngx_modules.c
│   ├── ngx_modules.o
│   └── src
│       ├── core
│       │   ├── nginx.o
│       │   ├── ngx_array.o
│       │   ├── ngx_buf.o
│       │   ├── ngx_conf_file.o
│       │   ├── ngx_connection.o
│       │   ├── ngx_cpuinfo.o
│       │   ├── ngx_crc32.o
│       │   ├── ngx_crypt.o
│       │   ├── ngx_cycle.o
│       │   ├── ngx_file.o
│       │   ├── ngx_hash.o
│       │   ├── ngx_inet.o
│       │   ├── ngx_list.o
│       │   ├── ngx_log.o
│       │   ├── ngx_md5.o
│       │   ├── ngx_module.o
│       │   ├── ngx_murmurhash.o
│       │   ├── ngx_open_file_cache.o
│       │   ├── ngx_output_chain.o
│       │   ├── ngx_palloc.o
│       │   ├── ngx_parse.o
│       │   ├── ngx_parse_time.o
│       │   ├── ngx_proxy_protocol.o
│       │   ├── ngx_queue.o
│       │   ├── ngx_radix_tree.o
│       │   ├── ngx_rbtree.o
│       │   ├── ngx_regex.o
│       │   ├── ngx_resolver.o
│       │   ├── ngx_rwlock.o
│       │   ├── ngx_shmtx.o
│       │   ├── ngx_slab.o
│       │   ├── ngx_spinlock.o
│       │   ├── ngx_string.o
│       │   ├── ngx_syslog.o
│       │   └── ngx_times.o
│       ├── event
│       │   ├── modules
│       │   │   └── ngx_epoll_module.o
│       │   ├── ngx_event_accept.o
│       │   ├── ngx_event_connect.o
│       │   ├── ngx_event.o
│       │   ├── ngx_event_pipe.o
│       │   ├── ngx_event_posted.o
│       │   └── ngx_event_timer.o
│       ├── http
│       │   ├── modules
│       │   │   ├── ngx_http_access_module.o
│       │   │   ├── ngx_http_auth_basic_module.o
│       │   │   ├── ngx_http_autoindex_module.o
│       │   │   ├── ngx_http_browser_module.o
│       │   │   ├── ngx_http_charset_filter_module.o
│       │   │   ├── ngx_http_chunked_filter_module.o
│       │   │   ├── ngx_http_empty_gif_module.o
│       │   │   ├── ngx_http_fastcgi_module.o
│       │   │   ├── ngx_http_geo_module.o
│       │   │   ├── ngx_http_gzip_filter_module.o
│       │   │   ├── ngx_http_headers_filter_module.o
│       │   │   ├── ngx_http_index_module.o
│       │   │   ├── ngx_http_limit_conn_module.o
│       │   │   ├── ngx_http_limit_req_module.o
│       │   │   ├── ngx_http_log_module.o
│       │   │   ├── ngx_http_map_module.o
│       │   │   ├── ngx_http_memcached_module.o
│       │   │   ├── ngx_http_not_modified_filter_module.o
│       │   │   ├── ngx_http_proxy_module.o
│       │   │   ├── ngx_http_range_filter_module.o
│       │   │   ├── ngx_http_referer_module.o
│       │   │   ├── ngx_http_rewrite_module.o
│       │   │   ├── ngx_http_scgi_module.o
│       │   │   ├── ngx_http_split_clients_module.o
│       │   │   ├── ngx_http_ssi_filter_module.o
│       │   │   ├── ngx_http_static_module.o
│       │   │   ├── ngx_http_upstream_hash_module.o
│       │   │   ├── ngx_http_upstream_ip_hash_module.o
│       │   │   ├── ngx_http_upstream_keepalive_module.o
│       │   │   ├── ngx_http_upstream_least_conn_module.o
│       │   │   ├── ngx_http_upstream_zone_module.o
│       │   │   ├── ngx_http_userid_filter_module.o
│       │   │   ├── ngx_http_uwsgi_module.o
│       │   │   └── perl
│       │   ├── ngx_http_copy_filter_module.o
│       │   ├── ngx_http_core_module.o
│       │   ├── ngx_http_file_cache.o
│       │   ├── ngx_http_header_filter_module.o
│       │   ├── ngx_http.o
│       │   ├── ngx_http_parse.o
│       │   ├── ngx_http_postpone_filter_module.o
│       │   ├── ngx_http_request_body.o
│       │   ├── ngx_http_request.o
│       │   ├── ngx_http_script.o
│       │   ├── ngx_http_special_response.o
│       │   ├── ngx_http_upstream.o
│       │   ├── ngx_http_upstream_round_robin.o
│       │   ├── ngx_http_variables.o
│       │   ├── ngx_http_write_filter_module.o
│       │   └── v2
│       ├── mail
│       ├── misc
│       ├── os
│       │   ├── unix
│       │   │   ├── ngx_alloc.o
│       │   │   ├── ngx_channel.o
│       │   │   ├── ngx_daemon.o
│       │   │   ├── ngx_dlopen.o
│       │   │   ├── ngx_errno.o
│       │   │   ├── ngx_files.o
│       │   │   ├── ngx_linux_init.o
│       │   │   ├── ngx_linux_sendfile_chain.o
│       │   │   ├── ngx_posix_init.o
│       │   │   ├── ngx_process_cycle.o
│       │   │   ├── ngx_process.o
│       │   │   ├── ngx_readv_chain.o
│       │   │   ├── ngx_recv.o
│       │   │   ├── ngx_send.o
│       │   │   ├── ngx_setaffinity.o
│       │   │   ├── ngx_setproctitle.o
│       │   │   ├── ngx_shmem.o
│       │   │   ├── ngx_socket.o
│       │   │   ├── ngx_time.o
│       │   │   ├── ngx_udp_recv.o
│       │   │   ├── ngx_udp_send.o
│       │   │   ├── ngx_user.o
│       │   │   └── ngx_writev_chain.o
│       │   └── win32
│       └── stream
├── README
└── src
    ├── core
    │   ├── nginx.c
    │   ├── nginx.h
    │   ├── ngx_array.c
    │   ├── ngx_array.h
    │   ├── ngx_buf.c
    │   ├── ngx_buf.h
    │   ├── ngx_conf_file.c
    │   ├── ngx_conf_file.h
    │   ├── ngx_config.h
    │   ├── ngx_connection.c
    │   ├── ngx_connection.h
    │   ├── ngx_core.h
    │   ├── ngx_cpuinfo.c
    │   ├── ngx_crc32.c
    │   ├── ngx_crc32.h
    │   ├── ngx_crc.h
    │   ├── ngx_crypt.c
    │   ├── ngx_crypt.h
    │   ├── ngx_cycle.c
    │   ├── ngx_cycle.h
    │   ├── ngx_file.c
    │   ├── ngx_file.h
    │   ├── ngx_hash.c
    │   ├── ngx_hash.h
    │   ├── ngx_inet.c
    │   ├── ngx_inet.h
    │   ├── ngx_list.c
    │   ├── ngx_list.h
    │   ├── ngx_log.c
    │   ├── ngx_log.h
    │   ├── ngx_md5.c
    │   ├── ngx_md5.h
    │   ├── ngx_module.c
    │   ├── ngx_module.h
    │   ├── ngx_murmurhash.c
    │   ├── ngx_murmurhash.h
    │   ├── ngx_open_file_cache.c
    │   ├── ngx_open_file_cache.h
    │   ├── ngx_output_chain.c
    │   ├── ngx_palloc.c
    │   ├── ngx_palloc.h
    │   ├── ngx_parse.c
    │   ├── ngx_parse.h
    │   ├── ngx_parse_time.c
    │   ├── ngx_parse_time.h
    │   ├── ngx_proxy_protocol.c
    │   ├── ngx_proxy_protocol.h
    │   ├── ngx_queue.c
    │   ├── ngx_queue.h
    │   ├── ngx_radix_tree.c
    │   ├── ngx_radix_tree.h
    │   ├── ngx_rbtree.c
    │   ├── ngx_rbtree.h
    │   ├── ngx_regex.c
    │   ├── ngx_regex.h
    │   ├── ngx_resolver.c
    │   ├── ngx_resolver.h
    │   ├── ngx_rwlock.c
    │   ├── ngx_rwlock.h
    │   ├── ngx_sha1.h
    │   ├── ngx_shmtx.c
    │   ├── ngx_shmtx.h
    │   ├── ngx_slab.c
    │   ├── ngx_slab.h
    │   ├── ngx_spinlock.c
    │   ├── ngx_string.c
    │   ├── ngx_string.h
    │   ├── ngx_syslog.c
    │   ├── ngx_syslog.h
    │   ├── ngx_thread_pool.c
    │   ├── ngx_thread_pool.h
    │   ├── ngx_times.c
    │   └── ngx_times.h
    ├── event
    │   ├── modules
    │   │   ├── ngx_devpoll_module.c
    │   │   ├── ngx_epoll_module.c
    │   │   ├── ngx_eventport_module.c
    │   │   ├── ngx_kqueue_module.c
    │   │   ├── ngx_poll_module.c
    │   │   ├── ngx_select_module.c
    │   │   └── ngx_win32_select_module.c
    │   ├── ngx_event_accept.c
    │   ├── ngx_event.c
    │   ├── ngx_event_connect.c
    │   ├── ngx_event_connect.h
    │   ├── ngx_event.h
    │   ├── ngx_event_openssl.c
    │   ├── ngx_event_openssl.h
    │   ├── ngx_event_openssl_stapling.c
    │   ├── ngx_event_pipe.c
    │   ├── ngx_event_pipe.h
    │   ├── ngx_event_posted.c
    │   ├── ngx_event_posted.h
    │   ├── ngx_event_timer.c
    │   └── ngx_event_timer.h
    ├── http
    │   ├── modules
    │   │   ├── ngx_http_access_module.c
    │   │   ├── ngx_http_addition_filter_module.c
    │   │   ├── ngx_http_auth_basic_module.c
    │   │   ├── ngx_http_auth_request_module.c
    │   │   ├── ngx_http_autoindex_module.c
    │   │   ├── ngx_http_browser_module.c
    │   │   ├── ngx_http_charset_filter_module.c
    │   │   ├── ngx_http_chunked_filter_module.c
    │   │   ├── ngx_http_dav_module.c
    │   │   ├── ngx_http_degradation_module.c
    │   │   ├── ngx_http_empty_gif_module.c
    │   │   ├── ngx_http_fastcgi_module.c
    │   │   ├── ngx_http_flv_module.c
    │   │   ├── ngx_http_geoip_module.c
    │   │   ├── ngx_http_geo_module.c
    │   │   ├── ngx_http_gunzip_filter_module.c
    │   │   ├── ngx_http_gzip_filter_module.c
    │   │   ├── ngx_http_gzip_static_module.c
    │   │   ├── ngx_http_headers_filter_module.c
    │   │   ├── ngx_http_image_filter_module.c
    │   │   ├── ngx_http_index_module.c
    │   │   ├── ngx_http_limit_conn_module.c
    │   │   ├── ngx_http_limit_req_module.c
    │   │   ├── ngx_http_log_module.c
    │   │   ├── ngx_http_map_module.c
    │   │   ├── ngx_http_memcached_module.c
    │   │   ├── ngx_http_mp4_module.c
    │   │   ├── ngx_http_not_modified_filter_module.c
    │   │   ├── ngx_http_proxy_module.c
    │   │   ├── ngx_http_random_index_module.c
    │   │   ├── ngx_http_range_filter_module.c
    │   │   ├── ngx_http_realip_module.c
    │   │   ├── ngx_http_referer_module.c
    │   │   ├── ngx_http_rewrite_module.c
    │   │   ├── ngx_http_scgi_module.c
    │   │   ├── ngx_http_secure_link_module.c
    │   │   ├── ngx_http_slice_filter_module.c
    │   │   ├── ngx_http_split_clients_module.c
    │   │   ├── ngx_http_ssi_filter_module.c
    │   │   ├── ngx_http_ssi_filter_module.h
    │   │   ├── ngx_http_ssl_module.c
    │   │   ├── ngx_http_ssl_module.h
    │   │   ├── ngx_http_static_module.c
    │   │   ├── ngx_http_stub_status_module.c
    │   │   ├── ngx_http_sub_filter_module.c
    │   │   ├── ngx_http_upstream_hash_module.c
    │   │   ├── ngx_http_upstream_ip_hash_module.c
    │   │   ├── ngx_http_upstream_keepalive_module.c
    │   │   ├── ngx_http_upstream_least_conn_module.c
    │   │   ├── ngx_http_upstream_zone_module.c
    │   │   ├── ngx_http_userid_filter_module.c
    │   │   ├── ngx_http_uwsgi_module.c
    │   │   ├── ngx_http_xslt_filter_module.c
    │   │   └── perl
    │   │       ├── Makefile.PL
    │   │       ├── nginx.pm
    │   │       ├── nginx.xs
    │   │       ├── ngx_http_perl_module.c
    │   │       ├── ngx_http_perl_module.h
    │   │       └── typemap
    │   ├── ngx_http.c
    │   ├── ngx_http_cache.h
    │   ├── ngx_http_config.h
    │   ├── ngx_http_copy_filter_module.c
    │   ├── ngx_http_core_module.c
    │   ├── ngx_http_core_module.h
    │   ├── ngx_http_file_cache.c
    │   ├── ngx_http.h
    │   ├── ngx_http_header_filter_module.c
    │   ├── ngx_http_parse.c
    │   ├── ngx_http_postpone_filter_module.c
    │   ├── ngx_http_request_body.c
    │   ├── ngx_http_request.c
    │   ├── ngx_http_request.h
    │   ├── ngx_http_script.c
    │   ├── ngx_http_script.h
    │   ├── ngx_http_special_response.c
    │   ├── ngx_http_upstream.c
    │   ├── ngx_http_upstream.h
    │   ├── ngx_http_upstream_round_robin.c
    │   ├── ngx_http_upstream_round_robin.h
    │   ├── ngx_http_variables.c
    │   ├── ngx_http_variables.h
    │   ├── ngx_http_write_filter_module.c
    │   └── v2
    │       ├── ngx_http_v2.c
    │       ├── ngx_http_v2_filter_module.c
    │       ├── ngx_http_v2.h
    │       ├── ngx_http_v2_huff_decode.c
    │       ├── ngx_http_v2_huff_encode.c
    │       ├── ngx_http_v2_module.c
    │       ├── ngx_http_v2_module.h
    │       └── ngx_http_v2_table.c
    ├── mail
    │   ├── ngx_mail_auth_http_module.c
    │   ├── ngx_mail.c
    │   ├── ngx_mail_core_module.c
    │   ├── ngx_mail.h
    │   ├── ngx_mail_handler.c
    │   ├── ngx_mail_imap_handler.c
    │   ├── ngx_mail_imap_module.c
    │   ├── ngx_mail_imap_module.h
    │   ├── ngx_mail_parse.c
    │   ├── ngx_mail_pop3_handler.c
    │   ├── ngx_mail_pop3_module.c
    │   ├── ngx_mail_pop3_module.h
    │   ├── ngx_mail_proxy_module.c
    │   ├── ngx_mail_smtp_handler.c
    │   ├── ngx_mail_smtp_module.c
    │   ├── ngx_mail_smtp_module.h
    │   ├── ngx_mail_ssl_module.c
    │   └── ngx_mail_ssl_module.h
    ├── misc
    │   ├── ngx_cpp_test_module.cpp
    │   └── ngx_google_perftools_module.c
    ├── os
    │   └── unix
    │       ├── ngx_alloc.c
    │       ├── ngx_alloc.h
    │       ├── ngx_atomic.h
    │       ├── ngx_channel.c
    │       ├── ngx_channel.h
    │       ├── ngx_daemon.c
    │       ├── ngx_darwin_config.h
    │       ├── ngx_darwin.h
    │       ├── ngx_darwin_init.c
    │       ├── ngx_darwin_sendfile_chain.c
    │       ├── ngx_dlopen.c
    │       ├── ngx_dlopen.h
    │       ├── ngx_errno.c
    │       ├── ngx_errno.h
    │       ├── ngx_file_aio_read.c
    │       ├── ngx_files.c
    │       ├── ngx_files.h
    │       ├── ngx_freebsd_config.h
    │       ├── ngx_freebsd.h
    │       ├── ngx_freebsd_init.c
    │       ├── ngx_freebsd_sendfile_chain.c
    │       ├── ngx_gcc_atomic_amd64.h
    │       ├── ngx_gcc_atomic_ppc.h
    │       ├── ngx_gcc_atomic_sparc64.h
    │       ├── ngx_gcc_atomic_x86.h
    │       ├── ngx_linux_aio_read.c
    │       ├── ngx_linux_config.h
    │       ├── ngx_linux.h
    │       ├── ngx_linux_init.c
    │       ├── ngx_linux_sendfile_chain.c
    │       ├── ngx_os.h
    │       ├── ngx_posix_config.h
    │       ├── ngx_posix_init.c
    │       ├── ngx_process.c
    │       ├── ngx_process_cycle.c
    │       ├── ngx_process_cycle.h
    │       ├── ngx_process.h
    │       ├── ngx_readv_chain.c
    │       ├── ngx_recv.c
    │       ├── ngx_send.c
    │       ├── ngx_setaffinity.c
    │       ├── ngx_setaffinity.h
    │       ├── ngx_setproctitle.c
    │       ├── ngx_setproctitle.h
    │       ├── ngx_shmem.c
    │       ├── ngx_shmem.h
    │       ├── ngx_socket.c
    │       ├── ngx_socket.h
    │       ├── ngx_solaris_config.h
    │       ├── ngx_solaris.h
    │       ├── ngx_solaris_init.c
    │       ├── ngx_solaris_sendfilev_chain.c
    │       ├── ngx_sunpro_amd64.il
    │       ├── ngx_sunpro_atomic_sparc64.h
    │       ├── ngx_sunpro_sparc64.il
    │       ├── ngx_sunpro_x86.il
    │       ├── ngx_thread_cond.c
    │       ├── ngx_thread.h
    │       ├── ngx_thread_id.c
    │       ├── ngx_thread_mutex.c
    │       ├── ngx_time.c
    │       ├── ngx_time.h
    │       ├── ngx_udp_recv.c
    │       ├── ngx_udp_send.c
    │       ├── ngx_user.c
    │       ├── ngx_user.h
    │       └── ngx_writev_chain.c
    └── stream
        ├── ngx_stream_access_module.c
        ├── ngx_stream.c
        ├── ngx_stream_core_module.c
        ├── ngx_stream.h
        ├── ngx_stream_handler.c
        ├── ngx_stream_limit_conn_module.c
        ├── ngx_stream_proxy_module.c
        ├── ngx_stream_ssl_module.c
        ├── ngx_stream_ssl_module.h
        ├── ngx_stream_upstream.c
        ├── ngx_stream_upstream.h
        ├── ngx_stream_upstream_hash_module.c
        ├── ngx_stream_upstream_least_conn_module.c
        ├── ngx_stream_upstream_round_robin.c
        ├── ngx_stream_upstream_round_robin

# 进行编译
./configure --prefix=/usr/local/nginx 

Configuration summary
  + using system PCRE library
  + OpenSSL library is not used
  + md5: using system crypto library
  + sha1: using system crypto library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
 # 会生成obj中间文件夹
 
 # 执行make编译
 make
 # 执行安装make install安装
 make install
 
 
 # 进入安装目录，查看文件结构
 cd /usr/local/nginx
 tree
 
 .
├── client_body_temp
├── conf
│   ├── fastcgi.conf
│   ├── fastcgi.conf.default
│   ├── fastcgi_params
│   ├── fastcgi_params.default
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types
│   ├── mime.types.default
│   ├── nginx.conf
│   ├── nginx.conf.default
│   ├── scgi_params
│   ├── scgi_params.default
│   ├── uwsgi_params
│   ├── uwsgi_params.default
│   └── win-utf
├── fastcgi_temp
├── html
│   ├── 50x.html
│   └── index.html
├── logs
│   ├── access.log
    │   ├── error.log
│   └── nginx.pid
├── proxy_temp
├── sbin
│   └── nginx
├── scgi_temp
└── uwsgi_temp
```



> 编译参数

```shell
# 在"./configure"配置中，"--with"表示启用模块，也就是说这些模块在编译时不会自动构建"--without"表示禁用模块，也就是说这些模块在编译时会自动构建，若你想Nginx轻量级运行，可以去除一些不必要的模块

--help
打印帮助信息。
 
--prefix=PATH
设置软件安装目录路径。
 
--sbin-path=PATH
设置可执行文件安装目录路径。
 
--modules-path=PATH
设置模块安装目录路径。
 
--conf-path=PATH
设置配置文件安装目录路径。
 
--error-log-path=PATH
设置错误日志文件安装目录路径。
 
--pid-path=PATH
设置进程文件安装目录路径。
 
--lock-path=PATH
设置NGINX锁文件安装目录路径，当NGINX运行时会自动创建该文件，用于在一台服务器上只允许运行一个NGINX服务。
 
--user=USER
设置运行进程时所使用的系统用户，如果没有指定，则默认为nobody，就算安装时不指定，后期也可以通过修改"nginx.conf"配置文件中的"user"项修改。
 
--group=GROUP
设置运行进程时所使用的用户组。
 
--build=NAME
设置编译名，一个描述，没有任何其他作用。
 
--builddir=DIR
设置编译目录，会将编译后生成的文件写入到这个目录中。
 
--------------------------------------------------------------------------------
 
--with-select_module
--without-select_module
启用或禁用select事件驱动模型。默认情况下在Linux2.6以上的内核版本中，Nginx支持使用Epoll高效的事件模型，我们可以在配置文件中使用"use epoll"指令开启它。
 
--with-poll_module     
--without-poll_module                                 
启用或禁用poll事件驱动模型。默认情况下在Linux2.6以上的内核版本中，Nginx支持使用Epoll高效的事件模型，我们可以在配置文件中使用"use epoll"指令开启它。
 
--with-threads
--with-file-aio
启用线程池功能。一般情况下主机有几核处理器在启动Nginx时就会创建几个Worker工作进程，进程创建线程处理每一个请求，当在CPU密集型计算、资源访问的环境下，很多请求都会开启对应的线程，可能会由于磁盘IO限制导致的线程处理请求时间变长，这不是我们期望看到的，我们就可以启用线程池功能，让请求排队等待处理，并且可以充分利用CPU提高处理效率。开启线程池需要AIO的支持。
启用异步文件IO（AIO）支持。一般用于大文件传输的场景下。
 
--------------------------------------------------------------------------------
 
--with-http_ssl_module
启用HTTP_SSL模块，用于构建HTTPS服务。默认情况下不构建此模块。
 
--with-http_v2_module
启用HTTP_V2模块，新的HTTP协议，相比HTTP1更优更快。默认情况下不构建此模块。
 
--with-http_realip_module
启用HTTP_Realip模块，用于修改客户端请求头中客户端IP地址值，一般用于反向代理中，将真实的客户端IP传送给后端的应用服务器。默认情况下不构建此模块。
 
--with-http_addition_module
启用HTTP_Addition模块，用于在响应之前和之后添加文本。默认情况下不构建此模块。
 
--with-http_xslt_module 
--with-http_xslt_module=dynamic
启用HTTP_Xslt模块，这个模块是一个过滤器，它可以通过XSLT模板转换成XML响应。需要ibxml2和libxslt库的支持。默认情况下不构建此模块。
启用HTTP_Xslt动态模块，允许在配置文件中通过"load_module"指令手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-http_image_filter_module
--with-http_image_filter_module=dynamic
启用HTTP_Image_Filter模块，这个模块是一个集成图片处理器，我们可以使用它转换JPEG、GIF、PNG和WEBP格式的图像，验证这些格式图像的有效型（是不是此格式的图像），输出JSON格式的图像信息，旋转图像，按比例缩放图像，剪切图片等。默认情况下不构建此模块。
启用HTTP_Image_Filter动态模块，允许在配置文件中通过"load_module"指令手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-http_geoip_module
--with-http_geoip_module=dynamic
启用HTTP_Geoip模块，这个模块用于处理不同地区的访问，当来自某一个区域的访问时将其重定向到对应的服务或者项目上，需要MaxMind GeoIP库的支持。默认情况下不构建此模块。
启用HTTP_Geoip动态模块，允许在配置文件中通过"load_module"指令手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-http_sub_module
启用HTTP_Sub模块，这个模块是一个过滤器，用于修改响应的内容，可以将一个指定的字符串替换成另一个字符串。默认情况下不构建此模块。
 
--with-http_dav_module     
启用HTTP_DAV模块，用于通过WEBDAV协议提供WEB的文件管理功能，类似于一个WEB的文件管理器，可以对服务器的文件进行管理。默认情况下不构建此模块。
 
--with-http_flv_module
--with-http_mp4_module
启用HTTP_FLV模块，用于为Flash Video（FLV）文件提供伪流视频服务端支持，开启它则允许在网页上播放FLV格式的视频。默认情况下不构建此模块。
启用HTTP_MP4模块，用于为MP4格式的视频文件提供伪流视频服务端支持，开启它则允许在网页上播放MP4格式的视频。默认情况下不构建此模块。
 
--with-http_gunzip_module
--with-http_gzip_static_module
启用HTTP_Gunzip模块，用于为不支持"gzip"编码方式的客户端解压响应，有些浏览器不支持"gzip"编码格式的请求和响应传输，若服务器开启了内容传输压缩功能（Gzip），则需要开启此项，服务器会本地解压数据，将数据传送给浏览器客户端。默认情况下不构建此模块。
启用HTTP_Gzip_Static模块，用于将静态内容压缩成".gz"为文件扩展名的预压缩文件，并缓存在本地，在响应时会将此文件发送以替代普通文件，运用此模块的好处就是不需要（Gzip）每次传输时都需要对文件进行处理压缩。在用于Squid+Nginx环境下，当Nginx启用（Gzip）内容传输压缩功能时，在使用Squid3.0以前版本搭建环境时会发现，Squid返回给客户端的并不是压缩状态，这就是由于没有启用此模块导致的。默认情况下不构建此模块。
 
--with-http_auth_request_module
启用HTTP_Auth_Request模块，此模块是一个请求验证模块，可以使用外部服务器或服务对网站的每个请求进行身份验证，当用户访问时，Nginx会向用于验证请求的外部服务器发出验证请求，若返回的状态码为200，则通过允许访问，若返回401或403，则访问会被拒绝。默认情况下不构建此模块。
 
--with-http_random_index_module
启用HTTP_Random_Index模块，随机主页模块，当用户访问时，随机响应一个主页，而并非由"index"指令定义的一个主页，而是从主页池中随机选中一个主页面返回。默认情况下不构建此模块。
 
--with-http_secure_link_module
启用HTTP_Secure_Link模块，防盗链模块，用于检查请求链接的权限以及是否过期，多用于文件下载防盗链。默认情况下不构建此模块。
 
--with-http_degradation_module
启用HTTP_Degradation模块，用于当主机剩余内存较低时，用户请求访问，Nginx会对某些"location"的请求返回204或444的响应码。默认情况下不构建此模块。
 
--with-http_slice_module
启用HTTP_Slice模块，此模块是一个过滤器，用于将一个大的完整的文件分割成多个小块文件，分段传送给用户，一般用于大文件传输的场景下，使用它可以让用户快速的得到响应。默认情况下不构建此模块。
 
--with-http_stub_status_module
启用HTTP_Stub_Status模块，状态信息统计模块，用于返回一个Nginx状态信息统计信息页面，管理员访问这个页面可以获取Nginx的请求处理、当前连接、等待连接等统计信息，一般用于监控Nginx的运行状态。默认情况下不构建此模块。
 
--------------------------------------------------------------------------------
 
--without-http_charset_module
禁用HTTP_Charset模块，此模块用于将指定的字符集添加到"Content-Type"响应头字段中。此外此模块还可以将数据从一个字符集转换为另一个字符集，此模块用于字符集设置。不建议禁用。
 
--without-http_gzip_module
禁用HTTP_Gzip模块，此模块用于HTTP响应内容传输压缩，可以将响应内存在传输时将其压缩成Gzip编码格式的响应传送给客户端，使用Gzip编码格式响应内容体积会变小，会提高传输效率。不建议禁用。
 
--without-http_ssi_module
禁用HTTP_SSI模块，此模块是一个过滤器，用于处理通过它响应中的SSI（Server Side Includes）命令。目前支持的SSI命令列表并不完整，SSI指令是一种可以嵌入WEB页面的一种语法指令。
 
--without-http_userid_module
禁用HTTP_Userid模块，此模块用于识别客户端的Cookie。可以使用嵌入变量"$uid_got"和"$uid_set"记录已接受和设置的Cookie。
 
--without-http_access_module
禁用HTTP_Access模块，此模块用于限制对某些客户端地址的访问，Allow or Deny。不建议禁用。
 
--without-http_auth_basic_module
禁用HTTP_Auth_Basic模块，该模块用于HTTP基本身份验证，使用用户名和密码来限制对资源的访问。
 
--without-http_mirror_module
禁用HTTP_Mirror模块，该模块用于将正式环境的流量拷贝到镜像（测试）环境下，一般用于测试环境引入真实环境的流量实现对测试环境的压力测试。
 
--without-http_autoindex_module
禁用HTTP_Autoindex模块，该模块用于在处理以斜杠字符（'/'）结尾的请求，并在找不到索引文件的情况下生成目录列表。
 
--without-http_geo_module
禁用HTTP_Geo模块，该模块用于从指定变量中获取客户端的IP地址，并将其嵌入到另外一个变量中。默认情况下从"$remote_addr"变量中取得客户端的IP地址。我们可以通过它结合"HTTP_Upstream"实现对来源客户端的负载均衡，当来自不同的客户端请求时，将其负载均衡给后端的不同的服务器处理；还可以使用它结合"HTTP_Map"+"HTTP_Limit_Conn"模块实现对来源客户端的限速功能。
 
--without-http_map_module
禁用HTTP_Map模块，该模块用于创建一个变量的映射表，结果变量可以是一个字符串也可以是另外一个变量。
 
--without-http_split_clients_module
禁用HTTP_Splic_Clients模块，该模块用于创建适用于A/B测试的变量，AB测试也称之为拆分测试，也就是将一个项目的两个不同版本发布，看用户更喜欢用于那个版本，若版本A受欢迎则发布版本A。
 
--without-http_referer_module
禁用HTTP_Referer模块，该模块用于防盗链，用于阻止对请求头部"referer"字段具有无效值的请求访问，可以设置一个白名单，非白名单的无效来源网址的连接则会拒绝请求，使用此模块我们还需考虑到，即使对于有效的请求，常规浏览器也可能不发送"referer"字段。不建议禁用。
 
--without-http_rewrite_module
禁用HTTP_Rewerte模块，该模块用于地址重写，用于将来源请求地址重定向到指定的地址上，可以保护真实的地址，增加安全性，该模块需要PCRE库的支持。不建议禁用。
 
--without-http_proxy_module
--without-http_fastcgi_module
--without-http_uwsgi_module
--without-http_scgi_module
--without-http_grpc_module
禁用HTTP_Proxy模块，该模块用于将请求代理传递到另外一台WEB服务器去处理，Nginx的核心模块。不建议禁用。
禁用HTTP_FastCGI模块，该模块用于将请求代理传递到另外一台FastCGI服务器去处理，一般用于反代PHP。不建议禁用。
禁用HTTP_UwSGI模块，该模块用于将请求代理传递给另外一台UwSGI服务器去处理。
禁用HTTP_SCGI模块，该模块用于将请求代理传递给另外一台SCGI服务器去处理。
禁用HTTP_Grpc模块，该模块用于将请求代理传递给另外一台Grpc服务器去处理。
 
--without-http_memcached_module
禁用HTTP_Memcached模块，该模块用于Nginx从Memcached服务器获取响应内容。一般用于Nginx+后端服务器+Memcached的环境下，当用户第一请求时，Nginx去Memcached中读取缓存数据，若没有则就请求后端的服务器去处理，后端服务器将静态页面的数据写入到Memcached缓存服务器中并返回响应给Nginx传递给用户，当用户第二次请求时则Nginx直接从Memcached缓存服务器中获取缓存的静态页面内容，Memcached缓存服务器是基于内存的，所以可以减少磁盘IO的使用，提高响应效率。
 
--without-http_limit_conn_module
禁用HTTP_Limit_Conn模块，该模块用于限制并发连接数量以及下载带宽限制。
 
--without-http_limit_req_module
禁用HTTP_Limit_Req模块，该模块用于限制请求数量，可以限制请求的频率。
 
--without-http_empty_gif_module
禁用HTTP_Empty_Gif模块，该模块会在内容中常驻的一个1X1的透明空白的GIF图像，当用户请求时，返回该图像，一般用于测试。
 
--without-http_browser_module
禁用HTTP_Browser模块，该模块用于创建变量，变量的值取决于请求头中"user-agent"的值，一般用于区别新式或者旧式浏览器，若新式浏览器则将请求重定向到新式的WEB页面中，呈现新页面，若为旧式浏览器则将返回旧式的WEB页面。
 
--without-http_upstream_hash_module
--without-http_upstream_ip_hash_module
--without-http_upstream_least_conn_module
禁用HTTP_Upstream_Hash模块，该模块提供了由"Upstream"指令定义的一组服务器的负载均衡方法"Hash"，该方法基于散列键值（hash），它会将客户端+服务端的映射关系存放到一个散列键值表中，当客户端第二次请求时则会匹配关系将请求转发至后端的同一台服务器上，实现会话保持功能。该模块提供指令"hash",在会话保持中，我们唯一能标识客户端的标志就是SessionID，所以我们可以使用指令"hash $cookie_jsession"实现会话保持功能。不建议禁用。
禁用HTTP_Upstream_IP_Hash模块，该模块提供了由"Upstream"指令定义的一组服务器的负载均衡方法"ip_hash"，该方法也用于会话保持，不过它是基于客户端IP的Hash方法，由于用户可能是ADSL接入的网络，所以客户端可能受动态IP影响会发生变化，所以一般不建议采用这种方法。
禁用HTTP_Upstream_Least_Conn模块，该模块提供了由"Upstream"指令定义的一组服务器的负载均衡方法"least_conn"，该方法用于将请求传递到具有最少活动连接、权重较高（性能最好）的后端服务器上去处理。
 
--without-http_upstream_keepalive_module
禁用HTTP_Upstream_Keepalive模块，该模块可以为由"Upstream"指令定义的一组服务器提供保持长连接的功能，使用它则会为每个Worker工作进程与后端服务器保持空闲的长连接，连接数由"keepalive"指令指定，当空闲的长连接数量超过指定值时，将关闭最近最少使用的连接。
 
--without-http_upstream_zone_module
禁用HTTP_Upstream_Zone模块，该模块可以将由"Upstream"指令定义的服务器组运行时的状态存储在共享内存区域中。
 
--------------------------------------------------------------------------------
 
--with-http_perl_module
--with-http_perl_module=dynamic
启用HTTP_Perl模块，用于在Perl中实现位置和变量处理程序，并可以将Perl调用到SSI中。默认情况下不构建此模块。
启用HTTP_Perl动态模块，允许在配置文件中通过"load_module"指定手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-perl_modules_path=PATH
设置一个用于保留Perl模块的目录路径。
 
--with-perl=PATH
设置Perl可执行命令文件的路径。
 
--------------------------------------------------------------------------------
 
--http-log-path=PATH
设置访问日志文件存放目录路径。安装后，可以在主配置文件中使用"access_log"指令修改。
 
--http-client-body-temp-path=PATH
设置用于存储客户端请求主体的临时文件存放目录路径。安装后，可以在主配置文件中使用"client_body_temp_path"指令修改。
 
--http-proxy-temp-path=PATH
设置用于存储从代理服务器接受的数据临时文件存放目录路径。安装后，可以在主配置文件中使用"proxy_temp_path"指令修改。
 
--http-fastcgi-temp-path=PATH
设置用于存储从FastCGI服务器接受的数据临时文件存放目录路径。安装后，可以在主配置文件中使用"fastcgi_temp_path"指令修改。
 
--http-uwsgi-temp-path=PATH
设置用于存储从UwSGI服务器接受的数据临时文件存放目录路径。安装后，可以在主配置文件中使用"uwsgi_temp_path"指令修改。
 
--http-scgi-temp-path=PATH
设置用于存储从SCGI服务器接受的数据临时文件存放目录路径。安装后，可以在主配置文件中使用"scgi_temp_path"指令修改。
 
--------------------------------------------------------------------------------
 
--without-http
禁用HTTP_Core模块，该模块为Nginx的核心模块，用于提供HTTP服务所有核心功能。
 
--without-http-cache
禁用HTTP缓存。
 
--------------------------------------------------------------------------------
 
--with-mail
--with-mail=dynamic
启用HTTP_Mail_Core模块，该模块为Nginx的核心模块，用于提供POP3/IMAP4/SMTP邮件代理服务。默认情况下不构建此模块。
启用HTTP_Mail_Core动态模块，允许在配置文件中通过"load_module"指令手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-mail_ssl_module
启用Mail_SSL模块，用于邮件代理服务支持SSL/TLS协议，需要OpenSSL库的支持。默认情况下不构建此模块。
 
--without-mail_pop3_module
禁用Mail_POP3模块，当启用HTTP_Mail_Core模块时，若你不想使用POP3协议，则可以考虑单独禁用此模块。不建议禁用。
 
--without-mail_imap_module
禁用Mail_IMAP模块，当启用HTTP_Mail_Core模块时，若你不想使用IMAP4协议，则可以考虑单独禁用此模块。不建议禁用。
 
--without-mail_smtp_module
禁用Mail_SMTP模块，当启用HTTP_Mail_Core模块时，若你不想使用SMTP协议，则可以考虑单独禁用此模块。不建议禁用。
 
--------------------------------------------------------------------------------
 
--with-stream
--with-stream=dynamic 
启用Stream_Core模块，Nginx的核心模块，用于实现TCP/UDP代理和四层负载均衡功能。默认情况下不构建此模块。此模块自Nginx1.9.0版本开始可用。
启用Stream_Core动态模块，允许在配置文件中通过"load_module"指令手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-stream_ssl_module
启用Stream_SSL模块，用于提供SSL/TLS协议支持，需要OpenSSL库的支持。该模块用于Nginx四层负载功能中使用，需要开启Stream_Core模块。默认情况下不构建此模块。
 
--with-stream_realip_module
启用Stream_Realip模块，用于修改客户端请求头中客户端IP地址值，一般用于反向代理中，将真实的客户端IP传送给后端的应用服务器。该模块用于Nginx四层负载功能中使用，需要开启Stream_Core模块。默认情况下不构建此模块。
 
--with-stream_geoip_module
--with-stream_geoip_module=dynamic
启用Stream_Geoip模块，用于处理不同地区的访问，当来自某一个区域的访问时将其重定向到对应的服务或者项目上，需要MaxMind GeoIP库的支持。该模块用于Nginx四层负载功能中使用，需要开启Stream_Core模块。默认情况下不构建此模块。
启用Stream_Geoip动态模块，允许在配置文件中通过"load_module"指令手动启用和禁用模块的使用。默认情况下不构建此模块。
 
--with-stream_ssl_preread_module
启用Stream_SSL_Preread模块，用于从客户端Hello消息中提取信息，而不会终止SSL/TLS。该模块用于Nginx四层负载功能中使用，需要开启Stream_Core模块。默认情况下不构建此模块。
 
--without-stream_limit_conn_module
禁用Stream_Limit_Conn模块，该模块用于限制并发连接数量以及下载带宽限制功能。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。不建议禁用。
 
--without-stream_access_module
禁用Stream_Access模块，该模块用于限制对某些客户端地址的访问。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。不建议禁用。
 
--without-stream_geo_module
禁用Stream_Geo模块，该模块用于从指定变量中获取客户端的IP地址，并将其嵌入到另外一个变量中。默认情况下从"$remote_addr"变量中取得客户端的IP地址。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。不建议禁用。
 
--without-stream_map_module
禁用Stream_Map模块，该模块用于创建一个变量的映射表，结果变量可以是一个字符串也可以是另外一个变量。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。不建议禁用。
 
--without-stream_split_clients_module
禁用Stream_Splic_Clients模块，该模块用于创建适用于A/B测试的变量，AB测试也称之为拆分测试，也就是将一个项目的两个不同版本发布，看用户更喜欢用于那个版本，若版本A受欢迎则发布版本A。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。
 
--without-stream_return_module
禁用Stream_Return模块，该模块用于向客户端发送指定值，然后关闭连接。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。不建议禁用。
 
--without-stream_upstream_hash_module
--without-stream_upstream_least_conn_module
禁用Stream_Upstream_Hash模块，该模块提供四层负载均衡的一种调度方法，一般用于基于SessionID的会话保持场景下，当开启Stream_Core模块时自动开启此功能。不建议禁用。
禁用Stream_Upstream_IP_Hash模块，该模块提供四层负载均衡的一种调度方法，基于来源IP的会话保持方法，由于来源IP的不稳定性，我们一般很少采用此种方法。当开启Stream_Core模块时自动开启此功能。
 
--without-stream_upstream_zone_module
禁用Stream_Upstream_Zone模块，该模块可以将由"Upstream"指令定义的服务器组运行时的状态存储在共享内存区域中。该模块用于Nginx四层负载功能中使用，当开启Stream_Core模块时自动开启此功能。
 
--------------------------------------------------------------------------------
 
--with-google_perftools_module
启用Google_Perftools模块，用于可以使用Google Performance Tools分析Nginx的工作进程，分析程序性能瓶颈。该模块适用于Nginx开发人员，默认情况下不构建此模块。
 
--with-cpp_test_module
启用Cpp_Test模块，用于C++测试。该模块适用于Nginx开发人员，默认情况下不构建此模块。
 
--add-module=PATH
--add-dynamic-module=PATH
添加第三方模块，需要指定第三方模块所在目录路径。
添加第三方动态模块，需要指定第三方动态模块所在目录路径。
 
--with-compat
启用动态模块兼容性。
 
--with-cc=PATH
设置GCC编译器所在目录路径。
 
--with-cpp=PATH
设置GCC-C++编译器所在目录路径。
 
--with-cc-opt=OPTIONS
设置将添加到CFLAGS变量的其他参数，若在FreeBSD系统下使用PCRE库时，应指定"--with-ccc-opt="-I /usr/local/include""。若你在使用select事件驱动模型时，还可以使用它设置可打开的最大文件描述符数量，突破1024的限制，比如"--with-ccc-opt="-D FD_SETSIZE=2048""
 
--with-ld-opt=OPTIONS
设置将在连接期间使用的其他参数，若在FreeBSD系统下使用PCRE库时，应指定"--with-ccc-opt="-L /usr/local/lib""。
 
--with-cpu-opt=CPU
设置CPU型号，为特定的CPU执行编译操作，有效的值：pentium, pentiumpro, pentium3, pentium4, athlon, opteron, sparc32, sparc64,ppc64。
 
--without-pcre
禁用PCRE库的使用。
 
--with-pcre
启用PCRE库的使用。PCRE库是一个Perl库，包含Perl兼容的正则表达式。
 
--with-pcre=DIR
若你是源码安装的PCRE库，则需要通过此项设置PCRE库的所在目录路径。
 
--with-pcre-opt=OPTIONS
为PCRE设置其他要编译的选项。
 
--with-pcre-jit
启用"即时编译"的支持，开启此项，则会利用"pcre_jit"指令快速编译PCRE库。
 
--with-zlib=DIR
若你是源码安装的Zlib库，则需要通过此项设置Zlib库的所在目录路径。当启用HTTP_Gzip模块的时候需要此库的支持。
 
--with-zlib-opt=OPTIONS
为Zlib设置其他要编译的选项。
 
--with-zlib-asm=CPU
为Zlib库的编译设置特定CPU，会加快编译速度，有效值：pentium, pentiumpro。
 
--with-libatomic
启用Libatomic_Ops库的使用。
 
--with-libatomic=DIR
若你是源码安装的Libatomic_Ops库，则需要通过此项设置Libatomic_Ops库的所在目录路径。
 
--with-openssl=DIR
若你是源码安装的OpenSSL库，则需要通过此项设置OpenSSL库的所在目录路径。
 
--with-openssl-opt=OPTIONS
为OpenSSL设置其他要编译的选项。
 
--with-debug
启用调试级别的日志。也可以手动修改主配置文件，使用"error_log /path/to/log debug;"指令设置调试级别的日志
```



> Nginx配置语法

```
配置文件由指令与指令块构成
每条指令以;结尾，指令与参数间以空格符号分割
指令块以{}大括号将多条指令组织在一起
include语句允许组合多个配置文件以提升可维护性
使用#符号添加注释，提高可读性
使用$符号使用变量

部分指令的参数支持正则表达式
```



## 学习配置文件

1. CoreModule(核心模块)
2. EventModule(事件驱动模块)
3. HttpCoreModule(http内核模块)

>  Nginx配置文件

```shell
#--------------------------------------------核心模块----------------------------------------
user  nobody;			# nginx进程运行的用户
worker_processes  1;	# nginx工作的进程数量
error_log  logs/error.log warn;  # 错误日志
pid        logs/nginx.pid;	# nginx进程运行后的进程id
#--------------------------------------------核心模块----------------------------------------


#--------------------------------------------事件驱动模块-------------------------------------
events {
    worker_connections  1024;   # 一个work进程的最大连接数 25535左右
    use epool;		# 使用epool网络模型
}
#--------------------------------------------事件驱动模块-------------------------------------


#-------------------------------------------http内核模块-------------------------------------
http {
    include       mime.types;	# 包含资源类型文件
    default_type  application/octet-stream;	  # 默认返回类型,变成下载的方式

	# 日志格式定义
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # 记录日志到logs/access.log文件中
    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;		#长连接超时时间
    #gzip  on;	#是否开启压缩功能		

	# 可以以include引入文件的方式进行引入	include /usr/local/nginx/conf/*.conf
    server {	# 定义一个网站
        listen       80;	# 监听端口
        server_name  localhost;	#域名

        #charset koi8-r;	# 字符集

        #access_log  logs/host.access.log  main;	#访问日志，优先于http中的日志文件

        location / {	# 位置
            root   html;	# 代码的主文件配置
            index  index.html index.htm;	# 服务端默认返回给用户的文件
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


http server location扩展了解项
http{}层下允许有多个Server{}层，一个Server{}层下又允许有多个Location
http{} 标签主要用来解决用户的请求和相应，接受到请求会抛给Server标签
server{} 标签主要用来响应具体的某一个网站
location{} 标签主要用于匹配网站具体URL路径

nginx -t 检查配置文件语法
```



> Nginx命令行

```
格式：nginx -s reload
帮助：-? -h
使用指定的配置文件：-c
指定配置指令：-g
指定运行目录：-p
发送信号：-s
测试配置文件是否有语法错误：-t -T
发音nginx的版本信息、编译信息等：-v -V
```



# Nginx常用变量

```shell
$uri:当前请求的uri，不带参数
$request_uri:请求的uri，带完整参数
$host:http请求报文中host首部，如果没有则以处理此请求的虚拟主机的主机名代替
$hostname:nginx服务运行在主机的主机名
$remote_addr:客户端IP
$remote_port:客户端端口
$remote_user:使用用户认证时客户端用户输入的用户名
$require_filename:用户请求中URI经过本地root或alias转换后映射的本地文件路径
$require_method:请求方法
$server_addr:服务器地址
$server_name:服务器名称
$server_port:服务器端口
$server_protocol:服务器向客户端发送响应时的协议
$scheme:在请求中使用scheme
$http_HEADER:匹配请求报文中指定的HEADER
$http_host:匹配请求报文中的host首部
$dcoument_root:当前请求映射到的root配置
```



# Nginx启动方式

>方式一

```shell
nginx 启动

nginx -s stop 停止

nginx -s reload | restart 重载服务
```

> 方式二

```shell
systemctl start nginx 启动

systemctl resatrt nginx 重载服务

systemctl stop nginx 停止
```



# Nginx虚拟主机

> 方式一：基于主机多IP方式

> 方式二：基于端口的配置方式

> 方式三：基于多个hosts名称方式（多域名方式）



# Nginx卸载

> 卸载指令

```shell
rm -rf /usr/local/nginx	
rm -rf /etc/init.d/nginx
```



# Nginx日志





# Nginx常用模块

## Nginx目录索引

ngx_http_autoindex_module模块处理以斜杠字符(‘/’)结尾的请求，并生成目录列表。

当ngx_http_index_module模块找不到索引文件时，通常将请求传递给模块。

```shell
server {	# 定义一个网站
    listen       80;	# 监听端口
    server_name  localhost;	#域名

    location / {	# 位置
    	root html;	# 代码的主文件配置
    	autoindex on;
    }
}
```



## Nginx状态监控



## Nginx访问控制



## Nginx资源限制



## Nginx访问限制



## Nginx Location





# LNMP架构

## LNMP架构工作原理

当用户发起http请求，请求会被Nginx处理，如果是静态资源请求，Nginx则直接返回，如果是动态请求Nginx则通过fastcgi协议转交给后端的PHP处理程序。

------

浏览器-------------.php文件-------------------------》nginx代理----------------fastcgi协议--------------------------------------》PHP

------

1. 用户通过http协议发起请求，请求会先抵达LNMP架构中的Nginx
2. Nginx会根据用户的请求进行Location规则匹配
3. Localtion如果匹配是静态，则有Nginx读取本地资源直接返回
4. Location若果匹配到请求会死动态，则有Nginx请求直接转发给fastcgi协议
5. fasecgi收到后会将请求交给php-fpm管理进程，php-fpm(php-fpm.conf控制)管理进程接收到后会调用具体的工作进程warrap
6. warrap进程会调用php程序进行解析，如果只是解析代码，php(php.ini控制)直接返回
7. 如果有查询数据库操作，则有php连接数数据库发情查询操作
8. 最终数据由mysql->php->php-fpm->fastcgi->nginx->http->user



## LNMP架构安装







