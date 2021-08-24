# 26

start()

启动服务器，监听所有TCP/UDP端口

启动成功后会创建worker_num+2个进程。Master进程+Manager进程+serv->worker_num个Worker进程

**Master主进程**

主进程内有多个Reactor线程，基于epoll/kqueue进行网络事件轮询。收到数据后转发到Worker进程去处理

**Manager进程**

对所有Worker进程进行管理，Worker进程生命周期结束或者发生异常时自动收回。并创建新的Worker进程

**Worker进程**

对收到的数据进行处理，包括协议解析和响应请求。未设置worker_num,底层会启动与CPU数量一致的Worker进程

