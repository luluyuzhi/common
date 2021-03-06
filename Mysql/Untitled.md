一 、基本概念
InnoDB支持几种不同的行级锁，MyISAM只支持表级锁
行锁(Record Lock)： 对索引记录加锁。
间隙锁(Gap Lock)： 锁住整个区间，包括：区间里具体的索引记录，不存在的空闲空间（可以是两个索引记录之间，也可能是第一个索引记录之前或最后一个索引记录之后的空间）。
next-key锁： 行锁和间隙锁组合起来。

注意：如果检索条件不是索引的话会全表扫描，则是表级锁，不是行级锁

二、行锁（Record Lock）
对主键或者唯一索引进行增删改或显示的加锁，InnoDB会加行锁,如：

-- 显示的加锁

```mysql
select * from  people where id =3 for update;
```



```mysql
update people set name='James' where id=3
```

注意：

正常的查询语句使用的是共享锁。
对于显示的加锁或增删改操作，条件判断必须是精确匹配（也就是=） ，不能用>,<,between或like等范围查询方式，因为这样会使行锁变成next-key Lock。
三、间隙锁（Gap Lock）
官方文档描述：Gap Lock的唯一目的就是阻止其他事务插入到间隙中。Gap Lock可以同时存在，不同的事务可以同时获取相同的Gap Lock，并不会互相冲突。Gap Lock也是可以显示的被禁止的，只要将事务的隔离级别降低到 READ COMMITTED。

对于间隙锁，什么叫锁住不存在的空闲空间，举个例子：
一个表有id为1，2，3，5，6，9行数据，执行如下sql语句

```mysql
select * from  people where id > 3 AND id <7 for update;
```

这是一个范围检索，InnoDB不仅会锁住id为5和6两行的数据，也会锁住id为4(虽然该行并不存在)的纪录。

四、next-key Lock
官方文档描述：Record Lock+Gap Lock，如果一个事务在记录R上的某个索引有共享/互斥锁,也会对其前面一个范围加锁

锁定的区域
根据索引会形成一个个左开右闭的一个区间，根据查询的条件其所在的区间，并且包括其后的区间。

这里给出一个people表

| id   | name  | age  |
| ---- | ----- | ---- |
| 1    | JAMES | 37   |
| 2    | OVEN  | 28   |
| 3    | LOVE  | 34   |

如果age是索引的话，相关的区域有
(-无穷,28]
(28,34]
(34,37]
(37,+无穷)

如果执行如下语句：

```mysql
select * from  people where age =34 for update;
```

那么会锁住(28,37]这么范围

如果执行如下语句：

```mysql
select * from  people where age =33 for update;
```

那么会锁住(28,34)这么范围

**间隙锁的目的是为了防止幻读，其主要通过两个方面实现这个目的：**
**（1）防止间隙内有新数据被插入**
**（2）防止已存在的数据，更新成间隙内的数据**

**innodb自动使用间隙锁的条件：**
**（1）必须在Repeatable Read级别下**
**（2）检索条件必须有普通索引（没有索引的话，mysql会全表扫描，那样会锁定整张表所有的记录，包括不存在的记录，此时其他事务不能修改不能删除不能添加）**

**注意：这里的普通索引不包括主键索引和唯一索引，如果在这两个索引下因为能精确检索出结果，所以会使用Record Lock直接锁定具体的行（范围查询除外）。**