### redo/undo log 与 binlog 之间的区别

- 层次不同。redo/undo log是innodb层维护的，而binlog是mysql server层维护的，跟采用何种引擎没有关系，记录的是所有引擎的更新操作的日志记录。innodb的redo/undo log更详细的说明可以参见姜承尧的《mysql技术内幕-innodb存储引擎》一书中相关章节。
- 记录内容不同。redo/undo日志记录的是每个页的修改情况，属于物理日志+逻辑日志结合的方式（redo log物理到页，页内采用逻辑日志，undo log采用的是逻辑日志），目的是保证数据的一致性。binlog记录的都是事务操作内容，比如一条语句`DELETE FROM TABLE WHERE i > 1`之类的，不管采用的是什么引擎，当然格式是二进制的，要解析日志内容可以用这个命令`mysqlbinlog -vv BINLOG`。
- 记录时机不同。redo/undo日志在事务执行过程中会不断的写入; binlog是在事务最终commit前写入的，多谢anti-semicolon 指出。当然，binlog什么时候刷新到磁盘跟参数`sync_binlog`相关。



## 两阶段提交





[MySQL · 引擎特性 · DROP TABLE之binlog解析](https://blog.csdn.net/yang131631/article/details/78719946#:~:text=%E5%AF%B9%E4%BA%8E%E6%99%AE%E9%80%9A%E8%A1%A8%EF%BC%8C%E6%97%A0%E8%AE%BA,%E5%BF%97%E5%B0%B1%E4%B8%8D%E6%98%AF%E5%BF%85%E9%A1%BB%E7%9A%84%E3%80%82)

[什么是MySQL binlog？ MySQL binlog的用途及格式解析](https://m.php.cn/article/361643.html)

