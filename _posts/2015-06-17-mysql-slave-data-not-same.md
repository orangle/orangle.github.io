---
layout: post
date: 2015-06-17 17:49:30 +0800
title: mysql主从数据不一致问题解决过程
tags: mysql
---


> 情况时这样的
昨天晚上主动2个机器都迁移了，然后今天才把主动重新连接上，但是从库的偏移量是从今天当前时刻开始的，也就是说虽然现在主动看似正常，其实是少了昨天的部分数据，由于从库的数据丢失了，早晚还是要填坑的。

## 问题
+  要解决问题就是怎么对比不一致，然后在不影响业务的情况下，修复数据不一致的问题，把从库缺少的数据补上

下面是能想到和找到的几个方案

1.  从新从0开始同步，虽然对主库的使用没有影响，但是那么大的数据量，对性能，网络影响有点大，数据丢失的应该很少
2. 主库dump数据，锁库，然后同步，不好。 影响业务使用
3. percona-toolkit 中的工具来校验和同步，从介绍上来看是符合现在的情况的，使用上还需要学习和认识才行。


下面是几个参考链接

+ [percona-toolkit工具]( https://www.percona.com/software/percona-toolkit ) 官方地址
+ [MySQL主从服务器数据一致性的核对与修复](http://huoding.com/2013/05/03/251)  简单描述下过程
+ [用pt-table-checksum校验数据一致性](http://nettedfish.sinaapp.com/blog/2013/06/04/check-replication-consistency-by-pt-table-checksum/)  描述工具原理
+ [用pt-table-sync修复不一致的数据](http://nettedfish.sinaapp.com/blog/2013/06/05/synchronizes-data-efficiently-by-pt-table-sync/) 描述了工具原理

## 操作过程
只把过程和用到的东西解释了下，有些参数选项等还需要查阅文档。两台机器都是centos6.5 mysql版本都是5.6 , 由于是线上环境，这里ip和密码等敏感信息修改了下。

* 主   192.168.1.100
* 从   192.168.1.98
* 修复数据库名   radius

### 工具安装

在`主库服务器`安装

```bash
#安装依赖包
yum install perl-DBI  perl-DBD-MySQL  perl-TermReadKey perl-Time-HiRes

#安装工具
wget percona.com/get/percona-toolkit.tar.gz
tar zxvf percona-toolkit-2.2.14.tar.gz
cd percona-toolkit-2.2.14
perl Makefile.PL && make && make install
```

### 校验数据一致性
#### 建立用户并授权
注意这里要在主从创建一个同名的用户，可以从主库访问从库，主库本地可以访问主库。工具的使用都是在主库的服务器上进行,使用
pt-table-checksum校验数据一致性。

从库mysql操作

```sql
GRANT SELECT,PROCESS, SUPER, REPLICATION SLAVE ON *.* TO 'checksums'@'192.168.1.100' IDENTIFIED BY 'slavecheck';

flush privileges;
```
​
主库mysql操作

```bash
GRANT SELECT, PROCESS, SUPER, REPLICATION SLAVE ON *.* TO 'checksums'@'192.168.1.100' IDENTIFIED BY 'slavecheck';

GRANT SELECT,INSERT,UPDATE,DELETE ON radius.checksums TO 'checksums'@'192.168.1.100';

flush privileges;
```

校验时候需要在主mysql 中新建一张表，新建用户需要有读写的权限，这里是把校验表建立在radius库中。

#### pt-table-checksum 校验
校验是在主库服务器上进行的

```bash
主库shell中执行
pt-table-checksum h='192.168.1.100',u='checksums',p='slavecheck',P=3306 -d radius --nocheck-replication-filters --replicate=radius.checksums

--nocheck-replication-filters ：不检查复制过滤器，建议启用。后面可以用--databases来指定需要检查的数据库。
--no-check-binlog-format      : 不检查复制的binlog模式，要是binlog模式是ROW，则会报错。
--replicate-check-only :只显示不同步的信息。
--replicate=    ：把checksum的信息写入到指定表中，建议直接写到被检查的数据库当中。
--databases=    ：指定需要被检查的数据库，多个则用逗号隔开。
--tables=       ：指定需要被检查的表，多个用逗号隔开
h=192.168.1.100 ：Master的地址
u=checksums         ：用户名
p=slavecheck        ：密码
P=3306          ：端口
```

这个脚本在主库机器上运行，会自动找到从库地址，并用相同的用户登录，然后对比。

--replicate 选项是建立一个表来存储对比信息，这个表一定要能同步到从库中，如果checksums用户没有建表权限，请自行建立好表

建表语句

```sql
CREATE TABLE IF NOT EXISTS `radius`.`checksums` (
     db             CHAR(64)     NOT NULL,
     tbl            CHAR(64)     NOT NULL,
     chunk          INT          NOT NULL,
     chunk_time     FLOAT            NULL,
     chunk_index    VARCHAR(200)     NULL,
     lower_boundary TEXT             NULL,
     upper_boundary TEXT             NULL,
     this_crc       CHAR(40)     NOT NULL,
     this_cnt       INT          NOT NULL,
     master_crc     CHAR(40)         NULL,
     master_cnt     INT              NULL,
     ts             TIMESTAMP    NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
     PRIMARY KEY (db, tbl, chunk),
     INDEX ts_db_tbl (ts, db, tbl)
  ) ENGINE=INNODB;
```


我这里手动建立好表之后出现了如下的错误

```
6-16T16:10:48 The --replicate table `radius`.`checksums` exists on the master but but it has problems on these replicas:
Table radius.checksums does not exist on replica localhost.localdomain
```
之前的错误，导致主从复制有问题，去从库查看主动状态，调整是得主从正常。

错误解决完了继续执行(结果有省略)

```bash
下面继续在主库的shell上检查
[root@localhost portal]# pt-table-checksum h='192.168.1.100',u='checksums',p='slavecheck',P=3306 -d radius --nocheck-replication-filters --replicate=radius.checksums
            TS ERRORS  DIFFS     ROWS  CHUNKS SKIPPED    TIME TABLE
06-16T16:50:21      0      1     8379       4       0   0.322 radius.account_account
06-16T16:50:21      0      1    11429       1       0   0.278 radius.account_mac
06-16T16:50:21      0      1    63747       1       0   0.329 radius.account_smslog
06-16T16:50:21      0      0        0       1       0   0.016 radius.auth_group
06-16T16:50:21      0      0        0       1       0   0.013 radius.auth_group_permissions
06-16T16:50:22      0      0       27       1       0   0.265 radius.auth_permission
06-16T16:50:22      0      1     8384       1       0   0.273 radius.auth_user
...
...
```

出现这种结果，说明已经check了，diffs一栏有不同，说明那些表数据不一致. 现在登录从库的mysql，执行如下语句

```sql
mysql> select * from radius.checksums where master_cnt <> this_cnt OR master_crc <> this_crc OR ISNULL(master_crc) <> ISNULL(this_crc) \G
*************************** 1. row ***************************
            db: radius
           tbl: account_account
         chunk: 2
    chunk_time: 0.028065
   chunk_index: PRIMARY
lower_boundary: 1847
upper_boundary: 9225
      this_crc: 4f43a2
      this_cnt: 7336
    master_crc: 9235f7a2
    master_cnt: 7379
            ts: 2015-06-16 17:00:31
```

一共有8条记录，这8张表数据不一致。 大概能看出来缺少了多少数据等。

### 修复不一致数据
修复不一致数据使用`pt-table-sync` 工具，使用`pt-table-checksum`工具的结果。不过这里还是有些坑。在修复之前最好把主mysql数据备份一下，因为会对主库有些写操作，有一点风险。

主库服务器执行

```bash
[root@localhost portal]# pt-table-sync --execute --replicate radius.checksums --sync-to-master h="192.168.1.98",P=3306,u="checksums",p="slavecheck" --ignore-tables radacct,django_session
DBI connect(';host=124.88.52.100;port=3306;mysql_read_default_group=client','checksums',...) failed: Access denied for user 'checksums'@'124.88.52.100' (using password: YES) at /usr/local/bin/pt-table-sync line 2220
但是直接用mysql连接就没问题
```

最后查了下文档，发现还是用户权限的问题。
从库操作

```sql
mysql> GRANT all ON radius.* TO 'checksums'@'192.168.1.100';
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

主库操作

```sql
mysql> GRANT all ON radius.* TO 'checksums'@'192.168.1.100';
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

新增`增删改查`权限其实就够了 ,我这偷懒下。。

错误基本解决完了

#### 修复数据
先修复一个不重要的表来实验下(主库操作)

```bash
pt-table-sync --execute --replicate radius.checksums --sync-to-master h=192.168.1.98,P=3306,u=checksums,p="slavecheck"  --tables account_smslog,radcheck --print
```

修复完成在执行一次check 主库操作

```bash
pt-table-checksum h='192.168.1.100',u='checksums',p='slavecheck',P=3306 -d radius --nocheck-replication-filters --replicate=radius.checksums
```

在从库mysql中检查下

```sql
mysql> select * from radius.checksums where master_cnt <> this_cnt OR master_crc <> this_crc OR ISNULL(master_crc) <> ISNULL(this_crc) \G
```

的确少了2张表，说明已经修复好了

接着把其他表修复，然后检查下是否有问题就OK了。

## 小结

这里主要的问题就是

1. 脚本在那里执行（都是在主库服务器，从库只是检查下结果）
2. 怎么建立用户，用户应该给予怎样的权限




