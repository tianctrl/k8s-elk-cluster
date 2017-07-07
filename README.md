- # 介绍 Introduction 

  在Kubernetes1.6集群上部署ELK(EFK)集群，yaml基于k8s官方example。

  Deploy ELK(EFK) cluster at Kubernetes1.6. yaml modified depend on k8s official example.

- # EFK
  
  ## Elasticsearch 是当今最流行的分布式搜索引擎。

  Elasticsearch 的一个实例为一个节点，若干节点组成集群。 节点可配置为3种角色：

    - 主节点(master): 控制Elasticsearch集群，负责集群中的操作，比如创建/删除一个索引，跟踪集群中的节点，分配分片到节点。主节点处理集群的状态并广播到其他节点，并接收其他节点的确认响应。

      通过配置文件elasticsearch.yml中, node.master属性为true, 则此节点为master节点. 

    - 数据节点(data): 持有数据和倒排索引。

      通过配置文件elasticsearch.yml中, node.data属性为true, 则此节点为data节点. 

    - 客户端节点(client): 扮演一个负载均衡的角色，将到来的请求路由到集群中的各个节点。

      配置文件elasticsearch.yml中, node.master 和 node.data 都为false时, 此节点为client节点.

  ## Fluentd 是一款开源的日志管理工具


- # EFK
  
  TO DO :  Translated into English

- # RBAC

  k8s 1.6 默认开启rbac角色权限管理. 部署EFK时需要在yaml里添加service account, 并创建角色绑定.

  官方example里es为2副本rc, 并没有把es节点分成3种不同角色, 而且我部署的时候也没有组成集群.

  这里我没有修改docker image, 所用image为官方版本, 通过docker hub 中转了一下.

- # 部署

  - 创建namespace, yaml文件默认为 efk-logs 

          kubectl create ns efk-logs

  - 创建config map 

          kubectl create configmap -n efk-logs kibana-config --from-file=kibana.yml --dry-run -o yaml | kubectl apply -n efk-logs -f -

  - 部署

          kubectl create -f ./es
          kubectl create -f ./fluentd
          kubectl create -f ./kibana
  
  - 查看pod启动情况

          kubectl get po -n efk-logs

  - 查看es集群service的ip

          kubectl get svc -n efk-logs

  - 查看es集群状态

          curl -XGET http://<service clusterIP:9200>/_cluster/health?pretty  