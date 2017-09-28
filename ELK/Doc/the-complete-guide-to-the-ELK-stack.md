




> 翻译自：
https://logz.io/learn/complete-guide-elk-stack/

The complete guide to the ELK stack

>作者注：
logz.io关于ELK的官方指导会随时保持更新，请确定访问的是最新版。


# 1.什么是ELK Stack?   
ELK 每个月超过50万的下载量，使其成为最为流行的日志管理平台。  
相比而言，splunk-曾经的领先平台，宣称拥有10000的使用者。  

ELk究竟是什么，为什么受到如此高的关注和采用，让我们继续深入了解。

## 什么是ELK Stack?
ELK stack 是三个开源产品的集合，elasticsearch,logstash,kibana(来自Elastic).  
elasticsearch 是基于lucence搜索引擎的nosql数据库。  
logstash是一个流式的日志工具，可接受多种数据源，执行多种转换，将数据输出到不同的目标。  
kibana是elasticsearch的是可视层。  
这三个开源工具结合在一起用于IT架构中的日志分析。（使用场景包括，BI，安全，审计，web分析）。  
logstash收集，解析日志，然后elasticsearch索引，存储这些信息。最终kibana基于这些数据为你的环境提供视图。

## 为什么ELK如此流行？

ELK如此流行是因为其完全满足日志分析所需要的所有条件。  
splunk企业版长期出入市场主导地位，但是其功能不匹配其日益增加的价格。尤其不适合做saas平台的小公司和刚起步的公司。  
基于上述原因，splunk总用户比ELK一个月的下载量还小。  
ELK也许不具备splunk的所用功能，但其也不需要这些额外的花哨功能。  
ELK 是一个花销小，简单但健壮的日志分析平台。
全局来看，IT机构也越来越倾向于使用开源产品，这也导致了一些新的收费项目如sumo logic不容易流行。
## 为什么日志分析平台变得更重要？
越来越多的IT架构迁移到云上，如aws,azure,public cloud security tools 和日志分析随之变得越来越关键.  
在云化的架构中，非常难做到性能隔离-尤其是系统负载标高的情况下。  
而云中的虚拟机性能表现会随着以下情况的变化产生巨大的波动，系统负载，服务器数量，环境，活动用户。  
所以，云化环境下，可靠性和节点失效将会成为一个大问题。  
日志管理平台可以监控上述问题，包括处理系统日志，Nginx/IIS服务日志（用于SEO），web流量分析，应用日志，elb/s3日志。  
devops,sa,sre,dev能够基于这些数据做出更好的决定。  
总之，所谓的"big data analytics" 变得越来越重要，尤其是云化之后的架构。  
“The future state of Big Data will be a hybrid of on-premises and cloud,” Forrester Research analyst Brian Hopkins told Computer World.  
以下举几个实例：  
- Hadoop,用于处理超大数据的框架，现在可以运行在云上而不仅仅是物理机。
- Intuit,已经可以基于云环境进行分析，因为他们需要一个“安全，可靠，可审计的环境”
- 工程师可以在云上用更便宜的资源消耗进行机器学习。  

## 如何使用ELK进行日志分析？
如上提及，ELK是最常用的日志分析平台。  
但是，产品级ELK平台的部署，维护需要大量的额外工作和其他附加软件。  
更多的ELK安装，部署将在本文档后面提及。
## logz.io能提供什么？
logz.io使ELK作为cloud-based 对外提供服务。使用dev/devops/sa/sre能集中化存储日志，监控基础设施，问题排错，BI分析，提升用户体验。  
我们提供ELK as a service ,其拥有，高可用，无限扩容，五分钟完成部署。不止如此，我们还追加了企业级服务，包括，告警， ELK app，基于多用户的权限控制。

# 2.An Elasticsearch Tutorial
Elasticsearch 通常被认为是一个搜索服务器。
更通用的讲，就是一个NoSQL数据库-使用非机构化存储，不能用sql进行查询。  
与大多数NoSQL数据库不一样，尽管，Elasticsearch有强大的搜索能力和功能，但是Elasticsearch提供的最简单的查询方法是通过REST API。  
## 如何安装Elasticsearch
前提，保证系统被Elasticsearch支持，安装Java 7 。  
Elasticsearch可以通过从下载独立发布包或者从yum源安装。这里采用下载独立发布包，因为他适用所有操作系统。  
从这里下载：  
https://www.elastic.co/downloads/elasticsearch  
unix-like 系统使用bin/elasticsearch  
windows 使用bin/elasticsearch.bat  
运行之后，访问http://127.0.0.1:9200，如果运行正常可以看到如下输出：
```
{
 "name" : "Bloodhawk",
 "cluster_name" : "elasticsearch",
 "version" : {
 "number" : "2.1.1",
 "build_hash" : "40e2c53a6b6c2972b3d13846e450e66f4375bd71",
 "build_timestamp" : "2015-12-15T13:05:55Z",
 "build_snapshot" : false,
 "lucene_version" : "5.3.1"
 },
 "tagline" : "You Know, for Search"
 }
```
