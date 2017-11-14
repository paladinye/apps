###  生产环境的准备
这部分将讨论在生产环境搭建couchbase的必要信息。  
在生产环境部署要确保相关的软硬件符合要求，部署步骤和安全性也要纳入考虑。  
couchbase可以选择在本地部署或者虚拟化部署。
### 下载
根据环境从官网下载对应安装包  
https://www.couchbase.com/downloads
### 安装
这里直接采用在centos6上使用本地安装模式。  
>只支持64位操作系统

- rpm 直接安装下载的couchbase包
- 三种方式初始化couchbase server ,UI,cli,api,
验证环境ui最快，
- 初始化完成master之后，分别进入从节点初始化页面，输入IP，用户名，密码加入master
- 加入完成之后，手动rebanlance 集群，rebanlance 不会影响集群对外服务，但是如果需要
平衡的数据够多，可能对I/O,CPU，network性能产生影响。


### 升级&迁移
涉及到再补充
### 初始化
可用
