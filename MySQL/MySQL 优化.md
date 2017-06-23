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

## MyISM & Innodb

## 聚簇索引 非聚簇索引

## 参考链接
- [SQL GUID和自增列做主键的优缺点](http://www.cnblogs.com/allen0118/p/4103322.html)
- [MySQL 使用自增ID主键和UUID 作为主键的优劣比较详细过程（500W单表）](http://blog.csdn.net/mchdba/article/details/52279523)
- [Mysql 用UUID做主键可行么？一直想尝试，但没条件测试。](https://www.zhihu.com/question/19742113)
- [分布式系统唯一ID生成方案汇总](http://www.cnblogs.com/haoxinyue/p/5208136.html)
- [Mysql一主多从和读写分离配置简记](http://www.cnblogs.com/boonya/p/5545960.html)
- [MySQL Innodb数据库性能实践——VARCHAR vs CHAR](http://blog.csdn.net/yunhua_lee/article/details/7038780)
