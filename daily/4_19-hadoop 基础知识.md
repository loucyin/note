# hadoop 基础知识

## 大数据学习的重点

- 分而治之
- 并行计算
- 计算向数据移动
- 数据本地化读取

## hadoop 项目包含的内容

- hadoop common
- hdfs
- hadoop yarn
- hadoop mapreduce

## 大数据生态

- 接入层: kafka
- 存储层: hdfs 
- 统一服务层：yarn
- 处理解析层：spark mapreduce

## HDFS 存储模型

- 文件线性按字节切割成块(block)，具有 offset id
- 文件与文件的 block 大小可以不一样
- 一个文件除了最后一个 block ，其他 block 大小一致
- block 的大小依据硬件的 io 调整
- block 被分散存放在集群的节点中，具有 location
- block 具有副本 replication ，没有主从概念，副本不能同时出现在同一节点，副本数为 3 只能找到 3 份数据
- 副本是满足可靠性和性能的关键
- 文件上传时，可以指定 block 大小和副本数，上传后只能修改副本数
- 一次写入多次读取，不支持修改（修改耗费的资源太大，不划算）
- 支持追加数据

## HDFS 架构

- HSFS 是一个主从架构
- 由一个 NameNode 和一些 DataNode 组成
- 面向文件包含：文件数据和文件元数据
- NameNode 负责存储和管理文件元数据，并维护了一个层次型的文件目录树
- DataNode 负责存储文件数据（blcok 块），并提供 block 的读写
- DateNode 与 NameNode 维持心跳，并汇报自己持有的 block 信息
- Client 与 NameNode 交互文件元数据和 DataNode 交互文件 block 数据

NameNode 功能：

- 完全基于内存存储文件元数据、目录结构、文件 block 的映射
- 需要持久化保证数据可靠性
- 提供副本放置策略

DataNode 功能：

- 基于本地磁盘存储 block
- 并保存 block 的校验和数据保证 block 的可靠性
- 与 NameNode 保持心跳，汇报 block 列表状态

## NameNode 的持久化方案

- 日志文件 EditsLog ：完整性好，但是恢复速度慢，体积会不断增大
- 镜像文件 FsImage ：时间是间隔的，容易丢一部分数据

FsImage + 增量的 EditsLog 实现数据持久化与数据恢复

安全模式：

- NameNode 启动后会进入安全模式状态
- 处于安全模式状态的 NameNode 是不会进行数据块的复制的
- NameNode 从所有 DataNode 接收心跳信号和块状态报告
- 每当 NameNode 检测确认某个数据的副本数达到最小值，该副本数被认为是副本安全的
- 在一定百分比的数据块被 NameNode 确认是安全之后（加上一个额外的 30s）NameNode 退出安全模式状态
- 再确定有哪些副本没有达到指定数目，将这些数据复制到其他 DataNode 上

SNN SecondaryNameNode :

- 在 Ha 模式下，会有 2 个 NameNode，不需要 SNN
- 在非 Ha 模式下，SNN 一般是独立的节点，周期完成对 NN 的 EditLog 想 FsImage 合并，减少 EditLog 大小，减少 NN 启动时间
- 根据配置文件设置的时间间隔 fs.checkpoint.period 默认 3600 秒
- 根据配置文件设置的 edits log 大小 fs.checkpoint.size 规定 edits 文件的最大值默认是 64MB 

## 副本放置策略

- 第一个副本：放置在上传文件的 DataNode 上，集群外提交，随机选一台有资源，又不是太忙的节点
- 第二个副本：放置在与第一个副本不同的机架的节点上
- 第三个副本：与第二个副本相同的机架节点上（考虑到数据复制的成本）
- 更多副本：随机节点

## HDFS 写流程

- client 请求 NameNode ，创建文件元数据
- NameNode 触发副本放置策略，返回给客户端一个有序的 DataNode 列表
- client 会与第一个 DataNode 建立连接，并将其他的 DataNode 信息传给第一个 DataNode
- 第一个 DataNode 会与第二的 DataNode 建立链接，并把其他的 DataNode 信息，传给第二个，以此类推
- client 将 block 分成 packet ，并使用 chunk + chunksum 填充
- client 将 packet 放入发送队列，并向第一个 DataNode 发送数据，第一个 DataNode 接收到后发送给第二个 DataNode ，以此类推
- block 传输完成后，DataNode 向 NameNode 汇报，如果副本数不够，会触发副本复制策略，同事 client 继续传输下一个 block

## HDFS 读流程

- client 通过 NameNode 获取文件信息
- client 选择离自己最近的 DataNode 拉取数据（本机、本机架、本数据中心）
- Hdfs 支持 client 给出文件的 offset 自定义连接哪些 block 的DataNode ，自定义获取数据
- 这是支持计算层的分治，并行计算的核心

## 疑问

- 为什么 sprark 比 mapreduce 要快 10 - 100 倍