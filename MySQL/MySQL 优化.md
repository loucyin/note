# MySQL 优化

## UUID INT 自增主键比较
### INT
优点：
- 占据空间小，4 byte
- int 性能好
缺点：
- 有合并表格需求，会出现主键重复
- 数值范围有限
- 很难处理分布存储数据


### UUID
优点：
- 重复概率极低
- 跨服务器数据合并方便

缺点：
- 存储空间大
- 若是InnoDB引擎，因为其是索引组织表，创建的每个非簇索引，都会带上主键，这样占用存储空间更大，需要消耗更多内存和IO；
- join 操作性能比 int 低
- 没有内置的函数获取最新产生的guid主键
- UUID做主键将会添加到表上的所以其他索引中，因此会降低性能

int 性能好，数据量大用 guid。

## char & varchar
MyISM 引擎：
- 优先选CHAR

Innodb 引擎：
- 一般情况下优先使用VARCHAR，特别是字符串的平均长度比最大长度要小很多的情况；
- 如果字符串本来就很短，而且长度固定，那么就优先选CHAR了。

## 索引

索引可以提高查询速度、排序速度、分组统计速度。

MySql 中的两种索引实现形式：btree 索引 hash 索引

### Btree 索引

Btree索引，它是以B+树为存储结构实现的。

左前缀原理：

多列索引是先按照第一列进行排序，然后在第一列排好序的基础上再对第二列排序，如果没有第一列的话，直接访问第二列，那第二列肯定是无序的，直接访问后面的列就用不到索引了

**根据查询**

### hash 索引

hash 索引缺点：
- hash 索引只能进行等式比较，不能用于范围查询
- hash 值映射的数据在表中不一定按照顺序排列，所以无法利用 hash 索引加速任何排序操作
- 没有办法使用前缀索引

> Innodb和MyISAM默认的索引是Btree索引；而Mermory默认的索引是Hash索引。

### 聚簇索引 非聚簇索引

这两个名字虽然都叫做索引，但这并不是一种单独的索引类型，而是一种数据存储方式。

innodb 引擎使用的是 聚簇索引，MyISAM 使用的是非聚簇索引

- 聚簇索引的顺序就是数据的物理存储顺序，一个表最多只能有一个聚簇索引
- 非聚簇索引：索引顺序与数据物理排列顺序无关

> MyISAM 索引文件和数据文件是分离的，在 MyISAM中，主索引和辅助索引（Secondary key）在结构上没有任何区别，只是主索引要求 key 是唯一的，而辅助索引的 key 可以重复。

> InnoDB 的数据文件本身就是索引文件，InnoDB 的辅助索引 data 域存储相应记录主键的值而不是地址。辅助索引搜索需要检索两遍索引：首先检索辅助索引获得主键，然后用主键到主索引中检索获得记录。

## 参考链接
- [SQL GUID和自增列做主键的优缺点](http://www.cnblogs.com/allen0118/p/4103322.html)
- [MySQL 使用自增ID主键和UUID 作为主键的优劣比较详细过程（500W单表）](http://blog.csdn.net/mchdba/article/details/52279523)
- [Mysql 用UUID做主键可行么？一直想尝试，但没条件测试。](https://www.zhihu.com/question/19742113)
- [分布式系统唯一ID生成方案汇总](http://www.cnblogs.com/haoxinyue/p/5208136.html)
- [Mysql一主多从和读写分离配置简记](http://www.cnblogs.com/boonya/p/5545960.html)
- [MySQL Innodb数据库性能实践——VARCHAR vs CHAR](http://blog.csdn.net/yunhua_lee/article/details/7038780)
