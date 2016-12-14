# MySQL 使用

## 数据库导出

```
mysqldump -u 用户名 -p 数据库名 > 导出的文件名
```

## 数据库导入

```
mysql -u 用户名 -p 数据库名 < 存放位置
```

## 全文索引

- 创建表格的时候，声明全文索引

  ```sql
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

  > InnoDB默认的全文索引parser非常合适于Latin，因为Latin是通过空格来分词的。但对于像中文，日文和韩文来说，没有这样的分隔符。一个词可以由多个字来组成，所以我们需要用不同的方式来处理。在MySQL 5.7.6中我们能使用一个新的全文索引插件来处理它们：n-gram parser.

  还有一种方式，支持中文索引，就是利用sphinx，不过需要自己编译配置，没搞明白。

- 使用n-gram parser<br>
  在创建表格时，声明parser为ngram

  ```sql
  CREATE TABLE articles
  (
        id BIGINT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
        title VARCHAR(100),
        FULLTEXT INDEX ngram_idx(title) with parser ngram
  ) Engine=InnoDB CHARACTER SET utf8mb4;
  ```

  在新建索引时

  ```sql
  alter table articles add fulltext index ngram_idx(title) with parser ngram;
  ```

- 全文索引使用

  ```sql
  select * from articles where match(title) against("测试用户0写的第0" in natural language mode);
  select * from articles where match(title) against("测试用户0写的第0" in boolean mode);
  ```

- 查看全文索引有哪些词

  > 我们可以通过查询INFORMATION_SCHEMA.INNODB_FT_INDEX_CACHE和INFORMATION_SCHEMA.INNODB_FT_TABLE_TABLE来查询哪些词在全文索引里面。

  使用方法：

  ```sql
  SET GLOBAL innodb_ft_aux_table="test/articles";
  SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_CACHE;
  ```

- 删除全文索引

  ```sql
  alter table articles drop index ngram_idx;
  ```

## 搜索的实现

- 需求

  1. 对三个表进行搜索；
  2. 将搜索结果合并；
  3. 按时间排序；
  4. 需要知道数据来自那张表。

- 实现<br>
  搜索通过select语句完成，合并通过union完成，排序通过order by 语句完成。

  ```sql
  select id,title,publishTime as time from j_bbs.essay where publishTime is not null and match(title,content) against("用户")
  union all
  select id,title,publishTime as time from j_bbs.vote where publishTime is not null and match(title,content) against("用户")
  union all
  select id,content as title,createTime as time from j_bbs.discuss where createTime is not null and match(content) against("用户")
  order by time desc;
  ```

  利用变量实现标记数据

  ```sql
  select id,title,@type:= 0 as type,publishTime as time from j_bbs.essay where publishTime is not null and match(title,content) against("用户")
  union all
  select id,title,@type:= 1 as type,publishTime as time from j_bbs.vote where publishTime is not null and match(title,content) against("用户")
  union all
  select id,content as title,@type:= 2 as type,createTime as time from j_bbs.discuss where createTime is not null and match(content) against("用户")
  order by time desc;
  ```

## 多表查询

```sql
select a.*, b.name as startCityName, c.name as endCityName from sccx.fast_xl a
left join sccx.area b on a.startCity = b.ID
left join sccx.area c on a.endCity = c.ID
```

通过 join on 语句拼接数据。

## You are using safe update mode

使用 update 或者 delete 语句时产生的错误。

```
Error Code: 1175\. You are using safe update mode and you tried to update a table without   
a WHERE that uses a KEY column To disable safe mode, toggle the option in Preferences ->  
SQL Editor and reconnect.
```

解决方法：

```sql
SET SQL_SAFE_UPDATES = 0;
```

## 根据一张表的字段更新另一张表的数据

根据 a 表的 kcCx 对应 b 表的 id ,从 b 表中查询出对应的 name ,更新 a 表中的 zws

```sql
update sccx.kc a set a.zws = (select b.name from sccx.kc_cx b where b.id = a.kcCx);
```

## 字符串处理

### trigger

```sql
delimiter $
create trigger tg1
after insert on orders
for each row
begin
update goods set amount=amount-new.number where id = new.gid;
end$
```

### 判断是否有汉字

```sql
SELECT * FROM h_bfzzr WHERE NOT (jtrk REGEXP "[u0391-uFFE5]");
```

### 判断数据是否有数字

```sql
SELECT * FROM h_bfzzr WHERE  (zrrdw REGEXP '^[0-9]*$');
```

## 参考链接

- [UPDATE SET = (SELECT ) 语法的总结](http://blog.itpub.net/133735/viewspace-731988/)
- [mysql处理字符串的两个绝招：substring_index,concat](http://blog.csdn.net/wolinxuebin/article/details/7845917)
- [mysql之触发器trigger](http://www.cnblogs.com/zzwlovegfj/archive/2012/07/04/2576989.html)
- [InnoDB全文索引：N-gram Parser](http://mysqlserverteam.com/innodb%E5%85%A8%E6%96%87%E7%B4%A2%E5%BC%95%EF%BC%9An-gram-parser/?spm=5176.blog15673.yqblogcon1.4.Bvz18O)
