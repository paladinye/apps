- 数据库认证

use db  
db.auth("user","password")
- 查看集群状态

sh.status
"partitioned" : true, 表示该db支持分片，并且分片具体信息会一并列出，如：
```
{  "_id" : "cluster",  "partitioned" : true,  "primary" : "shard0000"     }
    cluster.fs.chunks
        shard key: { "files_id" : 1, "n" : 1 }
        chunks:
            shard0000   1
        { "files_id" : { "$minKey" : 1 }, "n" : { "$minKey" : 1 } }
...
...
...
```
- 查看数据库拥有集合

use db  
show tables(show collections)
