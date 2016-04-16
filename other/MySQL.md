# 全文索引
- 创建表格的时候，声明全文索引  
```
CREATE TABLE articles
(
        id BIGINT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
        title VARCHAR(100),
        FULLTEXT INDEX ngram_idx(title)
) Engine=InnoDB CHARACTER SET utf8mb4;
```
- 表格创建后，新建索引
```
alter table articles add fulltext index ngram_idx(title);
```
- 中文全文索引  
>InnoDB默认的全文索引parser非常合适于Latin，因为Latin是通过空格来分词的。但对于像中文，日文和韩文来说，没有这样的分隔符。一个词可以由多个字来组成，所以我们需要用不同的方式来处理。在MySQL 5.7.6中我们能使用一个新的全文索引插件来处理它们：n-gram parser.  

  还有一种方式，支持中文索引，就是利用sphinx，不过需要自己编译配置，没搞明白。
- 使用n-gram parser  
在创建表格时，声明parser为ngram    
```
CREATE TABLE articles
(
        id BIGINT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
        title VARCHAR(100),
        FULLTEXT INDEX ngram_idx(title) with parser ngram
) Engine=InnoDB CHARACTER SET utf8mb4;
```
在新建索引时
```
alter table articles add fulltext index ngram_idx(title) with parser ngram;
```
- 删除全文索引  
```
alter table articles drop index ngram_idx;
```

  参考链接：  
[InnoDB全文索引：N-gram Parser](http://mysqlserverteam.com/innodb%E5%85%A8%E6%96%87%E7%B4%A2%E5%BC%95%EF%BC%9An-gram-parser/?spm=5176.blog15673.yqblogcon1.4.Bvz18O)  
