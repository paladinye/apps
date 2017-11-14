- 查看node详情
knife node show nodename
其中结果recipes代表all recipe in role 和recipe include in any recipes.
- 查看role详情
knife role show rolename
- 查看role应用的所有节点
knife search node "roles:$ROLE_NAME"
&only return the node name.
knife search node "roles:$ROLE_NAME" -i
- 查看chef client 属性值
knife node show [hostname] -a [node.http_proxy]
