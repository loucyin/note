# redis 

- 二进制安全
- 跳跃表

## redis  使用场景

- 场景1 ：活跃用户统计

时间做 key ，每个用户登录一次占用 1 位

bitset 20200226 1 1
bitop or key 20200226 20200227
bitcount key  

- 场景2 ： 用户活跃天数统计

用户做 key ，每天占用 1 位

- 场景3 ： 抢购，秒杀，点赞数，评论数，可以通过 incr 规避并发下，对数据库的事务操作

扩展：

https://db-engines.com

- 时序
- 关系
- 文档
- 搜索引擎
- 列式

## redis 数据类型

- string
- list : 可以实现：栈、队列、阻塞队列，可以保证存入、取出顺序
- hash : filed 可以做数值运算
- set:无序、但是可以去重，支持集合操作，支持随机事件，可以用于公平抽奖
- zset : 有序、排序的实现-跳跃表
 
 ## redis 作为缓存和数据库的区别

- 缓存可以有可以没有
- 缓存的只是部分数据
- 缓存数据会数据的访问而改变

- 设置有效期，前面设置的过期时间，在发生重写之后会失效
- LRU
- LFU


过期清除：

- 被动访问删除
- 周期轮询删除，随机测试 20 个 keys 删除所有的过期 keys ，如果多于 1/4 ，继续测试。
- 
## 几个概念

- 缓存穿透：查询数据库不一定存在的数据，正常流程，先进行缓存查询，如果 key 

## 扩展

- bloom 过滤器
- Cuckoo 过滤器