# On Debian/Ubuntu
> referrnce: https://www.rabbitmq.com/install-debian.html

## 安装方式
- 安装依赖Erlang/OTP  
直接使用系统源可能导致版本过低，最好更新apt源并安装最新稳定版（19.3+）
- 1.通过deb包安装
官网下载deb包安装可以保证版本最新
- 2.通过apt源安装
```
#导入官方apt源
echo 'deb http://www.rabbitmq.com/debian/ testing main' |
     sudo tee /etc/apt/sources.list.d/rabbitmq.list
#导入apt-key
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc |
     sudo apt-key add -'
#更新并安装
apt-get update
apt-get install rabbitmq-server
```
