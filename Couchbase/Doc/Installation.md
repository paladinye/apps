###  生产环境的准备
这部分将讨论在生产环境搭建couchbase的必要信息。  
在生产环境部署要确保相关的软硬件符合要求，部署步骤和安全性也要纳入考虑。  
couchbase可以选择在本地部署或者虚拟化部署。
### 下载
根据环境从官网下载对应安装包  
https://www.couchbase.com/downloads
### 安装
这里直接采用在debian8上使用本地安装模式。  
>只支持64位操作系统

- 1.如果没有则需要安装libssl  
apt-get install libssl version
- 2.直接安装
dpkg -i couchserver.deb

### 升级&迁移
涉及到再补充
### 初始化
可用
