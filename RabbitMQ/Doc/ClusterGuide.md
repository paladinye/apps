>reference:https://www.rabbitmq.com/clustering.html

## 概述
Rabbitmq 由一个或者多个erlang 节点构成。  
每一个运行Rabbitmq的程序共享用户，虚拟主机，队列，exchanges,bindings 和运行时参数。  
通常我们可以把这组nodes看成一个cluster
## 什么会被复制到所有节点？
所有被rabbitmq broker用到的数据/状态会被复制到每一个节点。  
但是队列不会，队列默认绑定到固定节点，尽管其能被所有节点感知和读取。  
如果需要复制队列到所有节点，请参考high availability 一节。
## 需要域名解析
Rabbitmq 使用过域名来工作，所以必须能解析集群其他节点的域名。  
用hostname 或者FQDN，要启用FQDN使用参数RABBITMQ_USE_LONGNAME。  
如果不能修改dns或者hosts文件，可以用rabbitmq自带的方法。
## 集群组建方式

- 直接使用rabbitmqctl
- 通过配置文件
- 使用插件，rabbitmq-autocluster

集群的组建是动态的，任何单节点rabbitmq可以随时加入一个集群或者离开一个集群。

## 如何处理故障节点
rabbitmq可以接受任何节点的故障。节点可以随时关闭，开启，只要他们能在关闭时通知任何已知节点。  
rabbitmq可以有方法处理跨wan通信，但是不建议跨wan使用,可使用shovel,federation解决跨wan通信。
## 硬盘型还是内存型节点
一般情况使用硬盘型节点，如果需要高性能的queue,exchange,binding churn可以使用内存节点。
## 集群搭建
将使用三个节点进行测试：
rabbit1，rabbit2,rabbit3  
后面会介绍如何在一个机器上搭建集群。
### 节点之间通过什么验证身份:erlang cookie
rabbitmq 节点和cli工具使用erlang cookie来验证彼此。  
集群内使用同一个cookie进行验证。  
cookie由任意长度字符+数字串组成。  
rabbitmq-server启动时会创建一个随机cookie,可以拷贝该cookie到集群其他机器完成验证。  
unix-like存储cookie到：/var/lib/rabbitmq/.erlang.cookie or $HOME/.erlang.cookie.
windows存储到：C:\Users\Current User\.erlang.cookie (%HOMEDRIVE% + %HOMEPATH%\.erlang.cookie) or C:\Documents and Settings\Current User\.erlang.cookie, and C:\Windows\.erlang.cookie  
另外，也可以在rabbitmq-server 启动时添加"-setcookie cookie"
### 开启三个独立server
```
rabbit1$ rabbitmq-server -detached
rabbit2$ rabbitmq-server -detached
rabbit3$ rabbitmq-server -detached
```
查看集群状态：  
rabbitmqctl cluster_status
### 组建集群
让02，03 加入01.  
首先让02加入01，加入之后02上的数据会全部丢失。  
操作步骤如下：
```
rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@rabbit2
rabbitmqctl start_app
```
### 集群重启
一个集群中的节点可以被任意重启，剩余的节点负责提供服务，故障节点重启后会自动和其他节点数据保持一致。  
集群节点重启注意事项：  
- 如果整个集群关闭，最后一个关闭的节点需要最先开启。
- 其他节点会等待最后一个硬盘节点启动30s,如果最后节点启动失败，其他节点也不能启动，同时不能使用forget_cluster_node将其移除。
- 如果节点同时挂掉，比如掉电，此时可使用rabbbitctl force_boot强制启动集群，不过不能保证数据一致性。

### 集群拆分
如果节点需要离开集群，必须被明确指定。
操作步骤：
```
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl start_app
```
也可以在其他几点移除节点，操作如下：  
```
rabbitmqctl stop_app#被移除节点上操作
rabbitmqctl forget_cluster_node rabbit@rabbit1 #远程节点操作
```

### 集群升级

集群内不允许节点出现混合版本，除非发型版明确指明兼容。  
rabbitmq会根据需要自动更新数据结构。该更新由集群内第一个启动的disk类型节点完成，所以不能先启动内存型节点，否则会报错。  
所以在升级集群之前，要规划好，明确一个节点最后一个升级并且第一个启动，防止数据不一致。  
2.1.1之后的版本才能自动升级（自动更新数据结构），之前的版本需要手动重建集群。
### 单独物理机上安装集群
用于实验环境，但完全可以在单机部署多docker实例模拟集群环境，该段跳过。
### 修改节点名
由于rabbitmq是基于域名在集群间通讯，所以在集群搭建之初要保证节点能被解析，DNS/host方式，因为一旦改变节点名，节点数据库会被重建，数据丢失。
### 节点防火前设置
服务节点需要暴露以下端口号：  
4369: epmd, a peer discovery service used by RabbitMQ nodes and CLI tools  
5672, 5671: used by AMQP 0-9-1 and 1.0 clients without and with TLS  
25672: used by Erlang distribution for inter-node and CLI tools communication and is allocated from a dynamic range (limited to a single port by default, computed as AMQP port + 20000). See networking guide for details.  
15672: HTTP API clients and rabbitmqadmin (only if the management plugin is enabled)  
61613, 61614: STOMP clients without and with TLS (only if the STOMP plugin is enabled)  
1883, 8883: (MQTT clients without and with TLS, if the MQTT plugin is enabled  
15674: STOMP-over-WebSockets clients (only if the Web STOMP plugin is enabled)  
15675: MQTT-over-WebSockets clients (only if the Web MQTT plugin is enabled)
### 连接到集群
由于集群内部节点角色一致，所以可以直接使用4层代理或者dns轮训来暴露服务。
### 内存型节点
内存型节点保存以下类型数据到内存，添加/删除队列，exchanges,vhosts,但是消息队列始终保存到硬盘，所以内存型节点并不会加快生产和消费速度。  
集群第一个节点不能是内存型，并且集群内最好有足够的硬盘型节点，仅仅使用内存型节点来扩容。  
### 创建内存型节点
```
rabbitmqctl stop_app
rabbitmqctl join_cluster --ram rabbit@rabbit1
rabbitmqctl start_app
```
### 修改节点类型
```
rabbitmqctl stop_app
rabbitmqctl change_cluster_node_type disc/ram
rabbitmqctl start_app
```
### 其他注意事项
集群erlang版本必须保持一致
