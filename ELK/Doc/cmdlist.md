- 列出所有index
GET /_cat/indices?v
- 多条件query与时间过滤组合查询实例
```
curl http://127.0.0.1:9200/_search -d '{
  "from": 0,
  "size": 1,
  "query": {
    "filtered": {
      "query": {
        "bool": {
          "must": [
            {
              "term": {
                "applicationID": "MERCURY-NGINX"
              }
            },
            {
              "term": {
                "request_uri": "/notifyClient.php"
              }
            }
          ]
        }
      },
      "filter": {
        "range": {
          "@timestamp": {
            "gte": "now-5m",
            "lte": "now"
          }
        }
      }
    }
  }
}'|python -m json.tool
```
