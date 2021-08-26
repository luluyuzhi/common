]fla有时候随着使用 mysql 占用内存越来越多 ，可以使用该命令，使得连接恢复最初的状态

```mysql
mysql_reset_connection
```

可以查看连接状态

```mysql
show processlist
```

查看数据库

```mysql
show databases;
```

查看表

```mysql
show tables;
```

查看表结构

```mysql
desc table_name;
```

插入数据：

```mysql
INSERT INTO table_name (field1, field2,...fieldN)
                       VALUES
                       (value1, value2,...valueN);
```

全局锁

```mysql
flush tables with read lock;
```

解锁

```mysql
unlock tables;
```

表锁

```mysql
//表级别的共享锁，也就是读锁；
lock tables t_student read;
//表级别的独占锁，也就是写锁；
lock tables t_stuent wirte;
```

[MySQL查看系统状态和系统变量](https://blog.csdn.net/chuanguan1820/article/details/100872541)

