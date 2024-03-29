

## Consul介绍

​	Consul是HashiCrop公司退出的开源工具，用于实现分布式系统的服务发现与配置，与其他分布式服务注册与发现方案相比，Consul的方案更“一站式”，内置了服务注册与发现框架、分布式一致性协议实现、健康检查、Key/Value存储、多数据中心方案，不需要依赖其它工具，使用起来也较为简单。

​    Consul使用Go语言编写，因此具有天然可移植性；安装包仅包含一个可执行文件，方便部署，与Docker等轻量级容器可无缝配合。

## Consul特性

- Raft算法
- 服务发现
- 健康检查
- Key/Value存储
- 多数据中心
- 支持http和dns协议接口
- 官方提供web管理界面

## Consul角色

- client：客户端，无状态，将HTTP和DNS接口请求转发给局域网内的服务端集群。
- server：服务端，保存配置信息，高可用集群，每个数据中心的server数量推荐为3个或5

## Consul工作原理

## Consul安装

## Consul入门案例

## Consul集群