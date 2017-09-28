# 日常维护

- 文件句柄数调整  
echo "ulimit -S -n 4096" >>/etc/default/rabbitmq-server  
查看限制数是否生效:  
cat /proc/$RABBITMQ_BEAM_PROCESS_PID/limits
- 启动服务  
service rabbitmq-server start  
- 状态查看  
rabbitmqctl status
- 日志  
默认存放在/var/log/RABBITMQ_NODENAME.log  
默认配置了weekly logrotate切割日志，可根据实际情况调整。
- 命令man page  
https://www.rabbitmq.com/manpages.html
