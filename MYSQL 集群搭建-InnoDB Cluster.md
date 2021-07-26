# MYSQL 集群搭建-InnoDB Cluster





## InnoDB Cluster 使用了哪些技术

1.  MySql Shell （client 连接， 既可以通过 MySql Roter 连接 集群， 也可以通过 Admin api 连接 集群）

2. MySql Server 和 **[Group Replication](https://dev.mysql.com/doc/refman/8.0/en/group-replication.html)** （成员管理， 既可以实现主从的管理，也可以多主，甚至可以线上更改集群的拓扑结构）

3. MySql Router （为应用程序与 Shell 提供 Mysql 路由功能 与 负载均衡，保证可用性）

   

   以下是 InnoDB Cluster 的示意图，来自 Mysql 官网。

   

![Three MySQL servers are grouped together as a high availability cluster. One of the servers is the read/write primary instance, and the other two are read-only secondary instances. Group Replication is used to replicate data from the primary instance to the secondary instances. MySQL Router connects client applications (in this example, a MySQL Connector) to the primary instance.](https://dev.mysql.com/doc/refman/8.0/en/images/innodb_cluster_overview.png)



## 集群搭建要求



1.  集群的各个要求相同 （没说 软硬件， 但是可以通过 Admin Api `dba.checkInstanceConfiguration() ` 进行检测）
2. 必须开启 Performance Schema
3. python  （8.0.18-~： 3）
4.  8.0.17 - ~ ,  集群服务实例应在提供一个 `server_id` 时应提供一个唯一 `id `, 方法如下`Cluster.addInstance(instance)`
5. ^ 8.0.23 - ~,  可以开启 并行复制需要，[Configuring the Parallel Replication Applier](https://dev.mysql.com/doc/mysql-shell/8.0/en/configuring-innodb-cluster.html#configuring-parallel-applier)
6. During the process of configuring an instance for InnoDB Cluster, the majority of the system variables required for using an instance are configured. But AdminAPI does not configure the [`transaction_isolation`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_transaction_isolation) system variable, which means that it defaults to `REPEATABLE READ`. This does not impact a single-primary cluster, but if you are using a multi-primary cluster then unless you rely on `REPEATABLE READ` semantics in your applications, we recommend using the `READ COMMITTED` isolation level. See [Group Replication Limitations](https://dev.mysql.com/doc/refman/8.0/en/group-replication-limitations.html).

^ 标记的可选， 6. 没有太看懂，需要回头复习的时候再看一下



### 系统环境



之前在高版本下 MySql 依赖于 Python3 ，这里需要注明一点 MySql 官方文档指定 python 必须存在于 `/usr/bin/` 目录下



```bash
 ln -s /usr/bin/python3 /usr/bin/python
```

 创建用户与用户组

```bash
groupadd mysql && useradd -g mysql -s /bin/nologin mysql -M
```



MySql Server 安装

一些有用的脚本

```bash
rsync  -rvlt ./mysql-8.0.26-linux-glibc2.12-x86_64 root@10.0.0.57:/root/
```



这里采用的是 [MySql Server 8.0.26](https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.26-linux-glibc2.12-x86_64.tar.xz), 二进制源码包  : **Linux - Generic (glibc 2.12) (x86, 32-bit), Compressed TAR Archive**

```bash
wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.26-linux-glibc2.12-x86_64.tar.xz

tar xvf mysql-8.0.26-linux-glibc2.12-x86_64.tar.xz

cp -r  mysql-8.0.26-linux-glibc2.12-x86_64 /usr/local/ && cd /usr/local/  && rm mysql ; ln -s mysql-8.0.26-linux-glibc2.12-x86_64 mysql

```



建立环境变量

```bash
vim /etc/profile

export PATH=$PATH:/usr/local/mysql/bin

source /etc/profile
```

进入 MySql 目录

```bash
cd mysql; mkdir mysql-files; chown mysql:mysql mysql-files && chmod 750 mysql-files

chown -R mysql:mysql ./
```

初始化 MySql

``` bash
mysqld --initialize --user=mysql

#* 建立SSL/RSA相关文件
bin/mysql_ssl_rsa_setup
# 启动mysql服务器
mysqld_safe --user=mysql &
```

登录到 MySql

```bash
mysql -u root -p 

# mysql 初始化的时候会生成临时密码 
```

设置 mysql 用户组密码

```bash
passwd mysql #123456
```

 -修改默认密码

```sql
alter user user() identified by "123456";
```

创建一个新的管理员账号

```sql
create user 'luluyuzhi'@'%' identified with mysql_native_password by '123456';
grant all on *.* to 'luluyuzhi'@'%' with grant option;
```

## MySql Shell 安装

这里采用的 [MySQL Shell 8.0.26](https://dev.mysql.com/downloads/shell/)

下载

```bash
wget https://cdn.mysql.com//Downloads/MySQL-Shell/mysql-shell-8.0.26-linux-glibc2.12-x86-64bit.tar.gz
```

安装

```bash
cp -r mysql-shell-8.0.26-linux-glibc2.12-x86-64bit /usr/local/ && cd  /usr/local/ && ln -s mysql-shell-8.0.26-linux-glibc2.12-x86-64bit mysql-shell
```

使用 `mysqlsh` 进入终端，依次检查服务器配置

```js
dba.checkInstanceConfiguration('luluyuzhi@123.56.26.211:3306')
```

**放心肯定有错误**

```


+----------------------------------------+---------------+----------------+--------------------------------+
| Variable                               | Current Value | Required Value | Note                                             |
+----------------------------------------+---------------+----------------+--------------------------------+
| binlog_transaction_dependency_tracking | COMMIT_ORDER  | WRITESET       | Update the server variable                       |
| enforce_gtid_consistency               | OFF           | ON             | Update read-only variable and restart the server |
| gtid_mode                              | OFF           | ON             | Update read-only variable and restart the server |
| replica_parallel_type                  | DATABASE      | LOGICAL_CLOCK  | Update the server variable                       |
| replica_preserve_commit_order          | OFF           | ON             | Update the server variable                       |
| server_id                              | 1             | <unique ID>    | Update read-only variable and restart the server |
+----------------------------------------+---------------+----------------+--------------------------------+
```

修复要求：

```js
dba.configureInstance('luluyuzhi@123.56.26.211:3306')
```

等待修复值并重启服务

重新检测：

```js
dba.checkInstanceConfiguration('luluyuzhi@123.56.26.211:3306')
```

```js
Please provide the password for 'luluyuzhi@123.56.26.211:3306': ******
Validating MySQL instance at luluyuzhi002:3306 for use in an InnoDB cluster...

This instance reports its own address as luluyuzhi002:3306
Clients and other cluster members will communicate with it through this address by default. If this is not correct, the report_host MySQL system variable should be changed.

Checking whether existing tables comply with Group Replication requirements...
No incompatible tables detected

Checking instance configuration...
Instance configuration is compatible with InnoDB cluster

The instance 'luluyuzhi002:3306' is valid to be used in an InnoDB cluster.

{
    "status": "ok"
}
```

我们需要重复检测所有节点直到结束

## MySQL Router

安装

```bash
wget https://dev.mysql.com/get/Downloads/MySQL-Router/mysql-router-8.0.26-linux-glibc2.12-x86_64.tar.xz

tar xvf mysql-router-8.0.26-linux-glibc2.12-x86_64.tar.xz

cp -r mysql-router-8.0.26-linux-glibc2.12-x86_64 /usr/local/

ln -s mysql-router-8.0.26-linux-glibc2.12-x86_64/ mysql-router
```

创建路由规则

```bash
[logger]
level = INFO
 
[routing:secondary]
bind_address = localhost
bind_port = 7001
destinations = 10.0.0.54:3306,10.0.0.55:3306,10.0.0.56:3306
routing_strategy = round-robin
 
[routing:primary]
bind_address = localhost
bind_port = 7002
destinations = 10.0.0.54:3306,10.0.0.55:3306,10.0.0.56:3306
routing_strategy = first-available
```

​	这里设置了两个路由策略：通过本地7001端口，循环连接到三个MySQL实例，由`round-robin`路由策略所定义；通过本地7002端口，对同样的三个MySQL实例设置首个可用策略。首个可用策略使用目标列表中的第一个可用服务器，即当10.0.0.54:3306可用时，所有7002端口的连接都转发到它，否则转发到10.0.0.54:3306，以此类推。Router不会检查数据包，也不会根据分配的策略或模式限制连接，因此应用程序可以据此确定将读写请求发送到不同的服务器。本例中可将读请求发送到本地7001端口，将读负载均衡到三台服务器。同时将写请求发送到7002，这样只写一个服务器，从而实现的读写分离。

启动

```bash
mysqlrouter -c mysql-router.config &
```

查看路由是否启动了

```
ps -ef | grep router
```

查看 router 日志

```bash
more ~/mysql-router/mysqlrouter.log 

2021-07-25 12:59:01 io INFO [7f8e8c5a3780] starting 4 io-threads, using backend 'linux_epoll'
2021-07-25 12:59:01 routing INFO [7f8e85912700] [routing:primary] started: routing strategy = first-available
2021-07-25 12:59:01 routing INFO [7f8e85111700] [routing:secondary] started: routing strategy = round-robin
2021-07-25 12:59:01 routing INFO [7f8e85912700] Start accepting connections for routing routing:primary listening on 7002
2021-07-25 12:59:01 routing INFO [7f8e85111700] Start accepting connections for routing routing:secondary listening on 7001
```

查看监听服务

```
netstat -tnlp

Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      995/sshd            
tcp6       0      0 ::1:7001                :::*                    LISTEN      2160/mysqlrouter    
tcp6       0      0 ::1:7002                :::*                    LISTEN      2160/mysqlrouter    
tcp6       0      0 :::33060                :::*                    LISTEN      1494/mysqld         
tcp6       0      0 :::3306                 :::*                    LISTEN      1494/mysqld  
```



路由测试，每次返回的 hostname 都不一样

```
mysql -ululuyuzhi -p123456 -P7001 --protocol=TCP -e"select @@hostname"
```



routing_strategy是MySQL Router的核心选项，从8.0.4版本开始引入，当前有效值为

first-available、next-available、round-robin、round-robin-with-fallback

顾名思义，该选项实际控制路由策略，即客户端请求最终连接到哪个MySQL服务器实例。相对于以前版本mode的选项，routing_strategy选项更为灵活，并且不能同时设置routing_strategy和mode，静态路由的设置只能选择其中之一。对于InnoDB Cluster而言，该设置时可选的，缺省使用round-robin策略。

round-robin：每个新连接都以循环方式连接到下一个可用的服务器，以实现负载平衡。
round-robin-with-fallback：用于InnoDB Cluster。每个新的连接都以循环方式连接到下一个可用的SECONDARY服务器。如果SECONDARY服务器不可用，则以循环方式使用PRIMARY服务器。
first-available：新连接从目标列表路由到第一个可用服务器。如果失败，则使用下一个可用的服务器，如此循环，直到所有服务器都不可用为止。
next-available：与first-available类似，新连接从目标列表路由到第一个可用服务器。与first-available不同的是，如果一个服务器被标记为不可访问，那么它将被丢弃，并且永远不会再次用作目标。重启Router后，所有被丢弃服务器将再次可选。此策略向后兼容MySQL Router 2.x中mode为read-write的行为。



​	可以在网络上的单台或多台主机上运行多个MySQL路由器实例，而无需将MySQL Router隔离到单个机器上。这是因为MySQL Router对任何特定服务器或主机都不具有亲和性。要停止MySQL Router，只需用kill或killall命令直接杀掉相关进程。



## 配置组复制



在`mysql` 中安装 组复制插件

```
 install plugin  group_replication soname 'group_replication.so'
```

如果查看以下的目录均没有 my.cnf 则需要我们手动去创建一个 `my.cnf` (高版本不在提供 default.cnf)

```bash
mysql --help | grep my.cnf

# /etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf
```



配置 组复制（my.cnf）

```cnf
[mysqld]
server_id=54
# server_id=55
# server_id=56

gtid_mode=ON
enforce-gtid-consistency=true
binlog_checksum=NONE
innodb_buffer_pool_size=4G
 
disabled_storage_engines="MyISAM,BLACKHOLE,FEDERATED,ARCHIVE,MEMORY"
 
log_bin=binlog
log_slave_updates=ON
binlog_format=ROW
master_info_repository=TABLE
relay_log_info_repository=TABLE
 
transaction_write_set_extraction=XXHASH64 # default value
group_replication_group_name="4605b82a-9153-4c64-8583-d6d713a323db"
# group_replication_group_name="c42af047-4215-4a4a-af39-739c6dbbed09"
# group_replication_group_name="4bdd375a-a2e7-46ec-a570-b88817de9310"
group_replication_start_on_boot=off

group_replication_local_address= "10.0.0.54:33061"
#group_replication_local_address= "10.0.0.55:33061"
#group_replication_local_address= "10.0.0.56:33061"

group_replication_group_seeds= "10.0.0.54:33061,10.0.0.55:33061,10.0.0.56:33061"
```

 主要参数说明：

组复制数据必须存储在InnoDB事务存储引擎中，使用其它存储引擎可能导致组复制出错。因此设置`disabled_storage_engines`禁用其它存储引擎。

配置`transaction_write_set_extraction`指示服务器对于每个事务，必须收集写集并使用XXHASH64哈希算法编码。从MySQL 8.0.2开始，此设置是默认设置，因此可以省略此行。
配置group_replication_group_name告诉插件它正在加入或创建的组名为 aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa。**group_replication_group_name的值必须是有效的UUID。在二进制日志中为组复制事件设置GTID时，将在内部使用此UUID。可以使用SELECT UUID()或Linux的uuidgen命令生成UUID。**
配置`group_replication_start_on_boo`t指示插件在服务器启动时不自动启动操作。这在设置组复制时很重要，因为它确保可以在手动启动插件之前配置服务器。配置成员后，可以将`group_replication_start_on_boot`设置为`on`，以便在服务器引导时自动启动组复制。
配置`group_replication_local_address`告诉插件使用网络地址`"10.0.0.54:33061"`和端口`33061`与组中的其它成员进行内部通信。组复制将此地址用于涉及组通信引擎（XCom，Paxos变体）的远程实例的内部成员到成员连接。此端口必须与用于客户端连接的端口不同，并且不得用于客户端应用程序。在运行组复制时，必须为组成员之间的内部通信保留它。
配置`group_replication_group_seeds`设置组成员的`IP`和端口，这些成员称为种子成员。建立连接后，组成员身份信息将列在`performance_schema.replication_group_members`中。通常，`group_replication_group_seeds`列表包含每个组成员的group_replication_local_address的 ip:port，但这不是强制性的，可以选择组成员的子集作为种子。启动该组的服务器不使用此选项，因为它是初始服务器，负责引导组。换句话说，引导该组的服务器上的任何现有数据都是用作下一个加入成员的数据。第二个服务器上的任何缺失数据都从引导成员上的数据中复制，然后组扩展。加入的第三个服务器可以要求这两个服务器中的任何一个作为捐赠者，数据被同步到新成员，然后该组再次扩展。后续服务器在加入时重复此过程。
配置`group_replication_bootstrap_group`指示插件是否引导该组。此选项只能在一个服务器实例上使用，通常是第一次引导组时（或者在整个组关闭并重新备份的情况下）。**如果多次引导组，例如当多个服务器实例设置了此选项时，则可以创建一个人工脑裂的情景，存在两个具有相同名称的不同组。**

重启 MySql

```bash
mysqladmin -uroot -p123456 shutdown
mysqld_safe --defaults-file=/etc/my.cnf &
```

启动组复制 (只能在一个实例内进行启动)

组复制使用异步复制协议实现分布式恢复，在将组成员加入组之前同步数据。分布式恢复过程依赖于名为`group_replication_recovery` 的复制通道，该通道用于将事务从捐赠者转移到加入该组的成员。因此需要设置具有正确权限的复制用户，以便组复制可以建立直接的成员到成员恢复复制通道。

```bash
create user 'repl'@'%' identified with 'mysql_native_password' by '123456';
grant replication slave on *.* to 'repl'@'%';
```

配置异步复制的复制通道

```sql
change master to master_user='repl', master_password='123456' for channel 'group_replication_recovery';
```


要启动该组，需指示某一服务器引导该组，然后启动组复制。此引导程序应仅由单个服务器完成，该服务器启动组并且只执行一次。

```sql
set global group_replication_bootstrap_group=on;
start group_replication;
set global group_replication_bootstrap_group=off;
```

重启 MySql

```bash
mysqladmin -uroot -p123456 shutdown
mysqld_safe --defaults-file=/etc/my.cnf &
```

次节点同步

```sql
-- 重置relay log info
reset slave all;
-- 设置复制通道
change master to master_user='repl', master_password='123456' for channel 'group_replication_recovery';
-- 添加到组
start group_replication;
```

查看是否成功

```sql
select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+-------------+-------------+--------------+-------------+----------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION |
+---------------------------+--------------------------------------+-------------+-------------+--------------+-------------+----------------+
| group_replication_applier | 5c93a708-a393-11e9-8343-005056a5497f | hdp4        |        3306 | ONLINE       | SECONDARY   | 8.0.16         |
| group_replication_applier | 5f045152-a393-11e9-8020-005056a50f77 | hdp3        |        3306 | ONLINE       | SECONDARY   | 8.0.16         |
| group_replication_applier | 8eed0f5b-6f9b-11e9-94a9-005056a57a4e | hdp2        |        3306 | ONLINE       | PRIMARY     | 8.0.16         |
+---------------------------+--------------------------------------+-------------+-------------+--------------+-------------+----------------+
3 rows in set (0.00 sec)
```

## 创建集群

连接数据库

```javascript
\connect luluyuzhi@10.0.0.54:3306
```

创建集群

```js
 dba.createCluster('luluyuzhi_innodb_cluster');
```

输出

> A new InnoDB cluster will be created on instance 'luluyuzhi003:3306'.
>
> You are connected to an instance that belongs to an unmanaged replication group.
> Do you want to setup an InnoDB cluster based on this replication group? [Y/n]: Y
> Creating InnoDB cluster 'luluyuzhi_innodb_cluster' on 'luluyuzhi003:3306'...
>
> Adding Seed Instance...
> Adding Instance 'luluyuzhi003:3306'...
> Adding Instance 'luluyuzhi004:3306'...
> Adding Instance 'luluyuzhi002:3306'...
> Resetting distributed recovery credentials across the cluster...
> Cluster successfully created based on existing replication group.

## 配置 MySql Router

```bash
mysqlrouter --bootstrap luluyuzhi@10.0.0.54:3306
```

该命令需要向集群写入数据，必须连接到可写的服务器。同时，该命令基于检索到的InnoDB Cluster元数据，MySQL Router 自动配置`mysqlrouter.conf`文件，包括带有bootstrap_server_addresses的metadata_cache部分，其中包含集群中所有服务器实例的地址。

生成的MySQL Router配置会创建用于连接到群集的TCP端口，包括使用经典MySQL协议和X协议与群集通信的端口，缺省值如下：

> 6446：用于经典MySQL协议读写会话，MySQL Router将传入连接重定向到主服务器实例。
> 6447：对于经典MySQL协议只读会话，MySQL Router将传入连接重定向到其中一个辅助服务器实例。
> 64460：用于X协议读写会话，MySQL Router将传入连接重定向到主服务器实例。
> 64470：用于X协议只读会话，MySQL Router将传入连接重定向到其中一个辅助服务器实例。

## 测试实例

```bash

mysql -uwxy -123456 -P6446 --protocol=TCP -N -r -B -e"select @@hostname"
mysql -uwxy -123456 -P6446 --protocol=TCP -N -r -B -e"select @@hostname"
mysql -uwxy -123456 -P6447 --protocol=TCP -N -r -B -e"select @@hostname"
mysql -uwxy -123456 -P6447 --protocol=TCP -N -r -B -e"select @@hostname"
mysql -uwxy -123456 -P6447 --protocol=TCP -N -r -B -e"select @@hostname"
mysqlsh --sql -uwxy -123456 -P64460 -e"select @@hostname"
mysqlsh --sql -uwxy -123456 -P64460 -e"select @@hostname"
mysqlsh --sql -uwxy -123456 -P64470 -e"select @@hostname"
mysqlsh --sql -uwxy -123456 -P64470 -e"select @@hostname"
mysqlsh --sql -uwxy -123456 -P64470 -e"select @@hostname"
```

## MySql Shell 一些常见的 Api

```js

// 向集群添加实例
var cluster = dba.getCluster()
cluster.addInstance('luluyuzhi@10.0.0.54:3306')

// 查看集群状态
cluster.status()
```

---

## 其他参考

[Group Replication Limitations](https://dev.mysql.com/doc/refman/8.0/en/group-replication-limitations.html)

[MySQL AdminAPI](https://dev.mysql.com/doc/mysql-shell/8.0/en/admin-api-overview.html#admin-api-installing-components)

[MySQL Router 8 详解](https://wxy0327.blog.csdn.net/article/details/100518636)

[组复制基本原理](https://wxy0327.blog.csdn.net/article/details/95195028)

[InnoDB Cluster详解](https://blog.csdn.net/wzy0623/article/details/100779450)

