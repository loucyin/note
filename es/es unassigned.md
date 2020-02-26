# es unassigned

## 环境

elasticsearch : 5.5.0

问题：es 索引不健康，导致数据无法写入，但是数据可以查询。

## 相关内容

es 索引颜色说明：

- 绿色： 所有主分片和副本分片都可用
- 黄色： 所有主分片可用，副本分片部分可用
- 红色： 部分主分片不可用

### unassigned 分片

如果不能分配分片，例如，您已经为集群中的节点数过分分配了副本分片的数量，则分片将保持UNASSIGNED状态。

查看 unassign 原因：<http://191.191.190.194:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason>

unassigned 分片问题可能的原因：

- INDEX_CREATED：由于创建索引的API导致未分配。
- CLUSTER_RECOVERED：由于完全集群恢复导致未分配。
- INDEX_REOPENED：由于打开open或关闭close一个索引导致未分配。
- DANGLING_INDEX_IMPORTED：由于导入dangling索引的结果导致未分配。
- NEW_INDEX_RESTORED：由于恢复到新索引导致未分配。
- EXISTING_INDEX_RESTORED：由于恢复到已关闭的索引导致未分配。
- REPLICA_ADDED：由于显式添加副本分片导致未分配。
- ALLOCATION_FAILED：由于分片分配失败导致未分配。
- NODE_LEFT：由于承载该分片的节点离开集群导致未分配。
- REINITIALIZED：由于当分片从开始移动到初始化时导致未分配（例如，使用影子shadow副本分片）。
- REROUTE_CANCELLED：作为显式取消重新路由命令的结果取消分配。
- REALLOCATED_REPLICA：确定更好的副本位置被标定使用，导致现有的副本分配被取消，出现未分配

## 解决方式

重新分配分片：

```bash
curl -XPOST '191.191.190.194:9200/_cluster/reroute' -d '{ "commands" :
  [ { "allocate_stale_primary" :
      { "index" : "dynamiccar", "shard" : 4, "node": "3s5s2uA-Rr--Bq6n2ff61A","accept_data_loss": true}
  }]
}'
```

## 参考链接

- [es 官方文档 cluster-allocation-explain](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/cluster-allocation-explain.html)
- [es 官方文档 cluster-reroute](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/cluster-reroute.html)
