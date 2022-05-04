# [一些知识](readme.md#数据库相关)

# 事务

多条 SQL 语句作为整体进行操作的功能，称为事务。

事务中的操作要么全部执行成功，要么全部失败。

事务的四个特性：

* A(tomic)：原子性，事务作为原子单位，要么全部执行，要么全部不执行
* C(onsistent)：一致性，事务完成后，所有数据的状态是一致的
* I(solation)：隔离性，并发的多个事务间对数据的修改必须相互隔离
* D(uration)：持久性，事务完成后，数据的修改要持久化存储

## 隔离级别

**多个并发执行**的事务，涉及操作**同一条记录**的时候，需要针对性选择不同的隔离级别， 避免数据不一致

| Isolation Level Name                  | Isolation Level | 导致[脏读（Dirty Read）](#脏读)？ | 导致[不可重复读（Non Repeatable Read）](#不可重复读)？ | 导致[幻读（Phantom Read）](#幻读)？ |
| :------------------------------------ | :-------------: | :-------------------------------: | :----------------------------------------------------: | :---------------------------------: |
| [Read Uncommitted](#Read Uncommitted) |        1        |                Yes                |                          Yes                           |                 Yes                 |
| [Read Committed](#Read Committed)     |        2        |                 -                 |                          Yes                           |                 Yes                 |
| [Repeatable Read](#Repeatable Read)   |        3        |                 -                 |                           -                            |                 Yes                 |
| [Serializable](#Serializable)         |        4        |                 -                 |                           -                            |                  -                  |

> 隔离级别越低，越容易导致数据不一致

* ### Read Uncommitted

可理解为：读到了另一个事务未提交的数据

* ### Read Committed

可理解为：读到了另一个事务提交的数据，导致前后数据不一致

* ### Repeatable Read

可理解为：一开始读不到存在的数据，之后能读到

* ### Serializable

可理解为：所有事务串行执行

效率会大大下降，应用程序的性能会急剧降低

### 数据不一致现象

* #### 脏读

最低的一种事务级别。在这种隔离级别下，A 事务会读到 B 事务更新后但未提交的数据，如果 B 事务回滚，那么 A 事务读到的数据就是脏数据，这就是脏读

* #### 不可重复读

多次读取同一数据，数据前后不一致

* #### 幻读

事务 A 修改数据后，事务 B 读不到数据，但可以更新数据，之后就可以再次读取了

# MySql

## 执行顺序

```mysql
(7) SELECT  
(8) DISTINCT <select_list>
(1) FROM  <left_table>
(3) <join_type> JOIN <right_table>
(2) ON <join_condition>
(4) WHERE <where_condition>
(5) GROUP BY <group_by_list>
(6) HAVING <having_condition>
(9) ORDER BY <order_by_condition>
(10) LIMIT <limit_number>
```

# 索引

索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。

相当于书的目录，便于快速检索。

在多列上的索引叫[联合索引](#联合索引的最左匹配原则)。

## 创建索引的条件

索引的效率取决于索引列的值是否散列，即该列的值如果越互不相同，即*离散度*越高，那么索引效率越高。反过来，如果记录的列存在大量相同的值，如性别列，那么索引反而会降低效率。

`where` 判断、`order by` 排序、`join` 的 `on` 条件、`group by` 字段上创建索引。

频繁更新的列不要作为索引。

随机无序的列，不建议作为索引，因为离散度无法确定。

过长的字段，建立前缀索引。

优先创建联合索引。

索引的个数不必过多。

## 普通索引

```mysql
alter table <table_name> add index <index_name>(<column_name1>,<column_name2>)
```

## 唯一索引

保证某一列（多列）具有唯一性。

对于主键，关系数据库会自动对其创建主键索引。使用主键索引的效率是最高的，因为主键会保证绝对唯一。

主键索引是特殊的唯一索引。

```mysql
alter table <table_name> add unique <index_name>(<column_name>)
```

## 全文索引

针对文本类型的列，解决 `like` 全文匹配效率低的问题

```mysql
alter table <table_name> add fulltext <index_name>(<column_name>)
```

## 前缀索引 

```mysql
alter table <table_name> add index <index_name>(<column_name>(<prefix_length>))
```

> 前缀长度根据索引选择性（列上不重复的个数/总个数）决定，在前缀长度下的索引选择性越接近列的索引选择性，越适合作为前缀长度

## 联合索引

### 最左匹配原则

**创建**联合索引时，一定要把最常用的列放在最左边

对于 `index(a, b, c, d)` ，根据最左匹配原则，相当于创建了四个索引：

| 最左（索引） | 右      |
| ------------ | ------- |
| a            | b, c, d |
| a, b         | c, d    |
| a, b, c      | d       |
| a, b, c, d   |         |

## 索引失效的条件

- 在索引列上使用函数（`replace/substr/concat/sum/count/avg`）或表达式计算（`+ - * /`）
- 字符类型不加引号，出现隐式转换
- `like` 条件中前面带 %
- 负向查询，如 `not like`；`!=`、`not in ` 在某些情况下可以
