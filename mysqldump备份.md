## mysqldump  
### 语法:    
mysqldump \[OPTIONS] database \[tables]   
mysqldump \[OPTIONS] --databases \[OPTIONS] DB1 [DB2 DB3...]  
mysqldump \[OPTIONS] --all-databases \[OPTIONS]   
备注：该命令默认在屏幕上打印输出，备份需重定向。  
### 常见选项：  
-A=--all-databases：备份所有数据库，含create database。  
-B=--databases db_name…：指定备份的数据库，包括create database语句。  
-E=--events：备份相关的所有event scheduler。    
-R=--routines：备份所有存储过程和自定义函数。  
--triggers：备份表相关触发器，默认启用，用--skip-triggers，不备份触发器。  
--default-character-set=utf8：指定字符集，默认是utf8。  
--master-data=1|2：此选项须启用二进制日志，且会自动关闭--lock-tables功能，自动打开-x | --lock-all-tables功能（除非开启--single-transaction）    
&ensp;&ensp;&ensp;&ensp;1：所备份的数据之前加一条记录为CHANGE MASTER TO语句，非注释，不指定#，默认为1;  
&ensp;&ensp;&ensp;&ensp;2：记录为注释的CHANGE MASTER TO语句。  
-F=--flush-logs：备份前滚动日志，锁定表完成后，执行flush logs命令,生成新的二进制日志文件，配合-A 或 -B 选项时，会导致刷新多次数据库。建议在同一时刻执行转储和日志刷新，可通过和--single-transaction或-x，--master-data 一起使用实现，此时只刷新一次日志。  
--compact：去掉注释，适合调试，生产不使用。  
-d=--no-data：只备份表结构。  
-t=--no-create-info：只备份数据,不备份create table。  
-n=--no-create-db：不备份create database，可被-A或-B覆盖。  
--flush-privileges：备份mysql或相关时需要使用。  
-f=--force：忽略SQL错误，继续执行。  
--hex-blob：使用十六进制符号转储二进制列，当有包括BINARY，VARBINARY，BLOB，BIT的数据类型的列时使用，避免乱码。  
-q=--quick：不缓存查询，直接输出，加快备份速度。   
-x=--lock-all-tables：（MyISAM备份使用，InnoDB不推荐）加全局读锁，锁定所有库的所有表，同时加--singletransaction或--lock-tables选项会关闭此选项功能。  
-l,--lock-tables：对于需要备份的每个数据库，在启动备份之前分别锁定其所
有表，默认为on，--skip-lock-tables选项可禁用,对备份MyISAM的多个库,可能
会造成数据不一致。InnoDB不推荐使用。  
--single-transaction：不适用MyISAM，此选项在开始备份前，先执行start transaction指令开启事务。和--lock-tables选项相互排斥。  

## 语法示例   
背景环境：  
```bash  
 [root@centos7 ~]#mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hellodb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.01 sec)
```
### 语法1：mysqldump \[OPTIONS] database \[tables]  指定单个数据库做备份   

备份：  
```bash
[root@centos7 ~]#mysqldump hellodb > hellodb_bak.sql   
```
备注：存放了创建表、插入表记录的命令，没有创建数据库的命令。  

删库：  
```bash
[root@centos7 ~]#mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 9
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> drop database hellodb;
Query OK, 7 rows affected (0.01 sec)

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)
```

还原：  
需要先创建数据库，且和原hellodb的结构一模一样。创建好后再还原。（此处hellodb数据库都是按照默认创建）  
```bash
[root@centos7 ~]#mysql -e 'create database hellodb'
[root@centos7 ~]#mysql -e 'show databases;'        
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hellodb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
[root@centos7 ~]#mysql hellodb < hellodb_bak.sql 
[root@centos7 ~]#mysql -e 'show databases;'      
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hellodb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
```
备注：数据库结构必须一模一样，数据库名字可做变更。  

总结：用语法1还原数据库，必须知道原数据库的完整结构，否则会有风险（如字符集不一致，可能会出现乱码）。此方法不推荐备份数据库时使用，可用作备份表。  

### 语法2：mysqldump \[OPTIONS] --databases \[OPTIONS] DB1 [DB2 DB3...]   备份多个数据库  

备份：  
```bash
[root@centos7 ~]#mysqldump --databases hellodb > hellodb_bak_2.sql
```
备注：文件存放了创建数据库，创建表，插入表记录的命令。  

删库：  
```bash
[root@centos7 ~]#mysql -e 'drop database hellodb'
[root@centos7 ~]#mysql -e 'show databases'       
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
```

还原：  
```bash
[root@centos7 ~]#mysql < hellodb_bak_2.sql 
[root@centos7 ~]#mysql -e 'show databases' 
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hellodb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
```
备注：不需要指定库名，因为文件中有创建数据库的命令。  

总结：该语法既备份数据库内容，也备份结构，可同时备份几个数据库，还原时不需要指定。--database 选项等同于-B。  


### 语法3：mysqldump \[OPTIONS] --all-databases \[OPTIONS]   传说中的完全备份  

备份：  
```bash
[root@centos7 ~]#mysqldump --all-databases > all_bak.sql
```
备注：存放了创建数据库、表和插入表记录的命令。  

删库：  
```bash
[root@centos7 ~]#rm -rf /var/lib/mysql/*
```

还原：  
```bash
[root@centos7 ~]#mysql < all_bak.sql 
[root@centos7 ~]#mysql -e 'show databases'
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hellodb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
```

总结：--all-databases选项等同于-A，完全备份，包括存储过程和触发器等。对初始数据库来说，完全备份只备份其中的mysql数据库，其他三个不备份(test是测试)。  


### 压缩  
生产中备份文件一般很大，可做备份压缩。  
mysqldump -A | xz > all_bak.sql.xz  


## 分库备份  
1. 循环  
```bash
[root@centos7 ~]#for db in `mysql -e 'show databases' |egrep -v '^(information_schema|performance_schema|test|Database)$'`;do mysqldump -B $db > $db`date +%F`.bak.spl;done
[root@centos7 ~]#ll *2018*     
-rw-r--r-- 1 root root   7800 Dec 12 15:51 hellodb2018-12-12.bak.spl
-rw-r--r-- 1 root root 514806 Dec 12 15:51 mysql2018-12-12.bak.spl
```

2. sed   
```bash
[root@centos7 ~]#mysql -e 'show databases' |egrep -v '^(information_schema|performance_schema|test|Database)$' |sed -r 's/(.*)/mysqldump -B \1 > \1_2.sql/' |bash  
[root@centos7 ~]#ll *_2.sql              
-rw-r--r-- 1 root root   7800 Dec 12 16:05 hellodb_2.sql
-rw-r--r-- 1 root root 514806 Dec 12 16:05 mysql_2.sql
```

## 生产环境备份策略  
mysqldump -uroot -A -F --single-transaction --master-data=2 --flush-privileges --default-character-set=utf8 --hex-blob > backup\`date +%F`.sql  


## 实验模拟：备份删库还原  
背景环境：centos7操作系统，每天凌晨1点做完全备份，周四上午10点发现误删库，且二进制日志文件（/data/logbin下）与数据库（/var/log/mysql）分离存放，现要求还原。  
要点：先用完全备份还原到周四凌晨1点的状态，再利用二进制日志还原到周四上午10点的状态。 
### 模拟周四凌晨1点的完全备份  
1.确定各数据库的字符集：  
```bash
[root@centos7 ~]#mysql -e "show variables like 'character%'"
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
```
2.开始完全备份：  
```bash
[root@centos7 ~]#mysqldump -uroot -A -F --single-transaction --master-data=2 |gzip > all_bak_`date +%F`.sql.gz
```
### 模拟周四凌晨1点到10点数据库被使用，有修改变动  
```bash
[root@centos7 ~]#mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 5
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MariaDB [(none)]> use hellodb
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [hellodb]> insert teachers (name,age) values('hhh',25),('sss',26);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

MariaDB [hellodb]> select * from teachers;                                
+-----+---------------+-----+--------+
| TID | Name          | Age | Gender |
+-----+---------------+-----+--------+
|   1 | Song Jiang    |  45 | M      |
|   2 | Zhang Sanfeng |  94 | M      |
|   3 | Miejue Shitai |  77 | F      |
|   4 | Lin Chaoying  |  93 | F      |
|   5 | hhh           |  25 | NULL   |
|   6 | sss           |  26 | NULL   |
+-----+---------------+-----+--------+
6 rows in set (0.00 sec)
```

### 模拟删库  
```bash
[root@centos7 ~]#rm -rf /var/lib/mysql/*
```

### 还原  
1. 停止用户访问，避免对数据库在做修改。  
2. 重启服务  
```bash
[root@centos7 ~]#systemctl restart mariadb
```
3. 登录mysql，禁用二进制日志并确认。（到还原完前mysql不可退出）  
```bash
[root@centos7 ~]#mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> set sql_log_bin=off;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> show variables like 'sql_log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| sql_log_bin   | OFF   |
+---------------+-------+
1 row in set (0.00 sec) 
```
4. 克隆当前终端，解压完全备份文件。  
```bash
[root@centos7 ~]#ls
all_bak_2018-12-12.sql.gz  hellodb2018-12-12.bak.spl  mysql_2.sql
all_bak.spl                hellodb_2.sql              Pictures
all_bak.sql                hellodb_bak.sql            Public
anaconda-ks.cfg            hellodb_innodb.sql         Templates
Desktop                    initial-setup-ks.cfg       Videos
Documents                  Music
Downloads                  mysql2018-12-12.bak.spl
[root@centos7 ~]#gunzip all_bak_2018-12-12.sql.gz 
[root@centos7 ~]#ls
all_bak_2018-12-12.sql  hellodb2018-12-12.bak.spl  mysql_2.sql
all_bak.spl             hellodb_2.sql              Pictures
all_bak.sql             hellodb_bak.sql            Public
anaconda-ks.cfg         hellodb_innodb.sql         Templates
Desktop                 initial-setup-ks.cfg       Videos
Documents               Music
Downloads               mysql2018-12-12.bak.spl
```
5. 当前终端还原完全备份，使数据库还原到周四凌晨1点状态。    
mysql > source /root/all_bak_2018-12-12.sql  
6. 还原二进制日志。在克隆终端查看备份文件，确认周四凌晨1点的完全备份是从哪个二进制日志文件的什么位置开始。然后把需要还原的二进制日志文件重定向到一个文件中。在当前终端利用此文件进行二进制日志还原。  
```bash
[root@centos7 ~]#cat all_bak_2018-12-12.sql |less
-- MySQL dump 10.14  Distrib 5.5.56-MariaDB, for Linux (x86_64)
--
-- Host: localhost    Database: 
-- ------------------------------------------------------
-- Server version       5.5.56-MariaDB

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Position to start replication or point-in-time recovery from
--

-- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000004', MASTER_LOG_POS=245;
```
确认二进制日志文件为mysql-bin.000004，位置是245。因为245是开始位置，所以需要的文件是4以后的文件。  
```bash
[root@centos7 ~]#ls /data/logbin -l
total 1072
-rw-rw---- 1 mysql mysql     264 Dec 12 16:53 mysql-bin.000001
-rw-rw---- 1 mysql mysql     288 Dec 12 17:05 mysql-bin.000002
-rw-rw---- 1 mysql mysql     288 Dec 12 17:05 mysql-bin.000003
-rw-rw---- 1 mysql mysql     510 Dec 12 18:34 mysql-bin.000004
-rw-rw---- 1 mysql mysql   30373 Dec 12 18:34 mysql-bin.000005
-rw-rw---- 1 mysql mysql 1038814 Dec 12 18:34 mysql-bin.000006
-rw-rw---- 1 mysql mysql     245 Dec 12 18:34 mysql-bin.000007
-rw-rw---- 1 mysql mysql     210 Dec 12 18:34 mysql-bin.index
[root@centos7 ~]#mysqlbinlog /data/logbin/mysql-bin.00000{4,5,6,7} > incremental.sql
```
当前终端还原：  
mysql > source /root/incremental.sql  

7. 查看数据库文件，是否还原。  
```bash
MariaDB [mysql]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hellodb            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)

MariaDB [mysql]> use hellodb
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [hellodb]> select * from teachers;
+-----+---------------+-----+--------+
| TID | Name          | Age | Gender |
+-----+---------------+-----+--------+
|   1 | Song Jiang    |  45 | M      |
|   2 | Zhang Sanfeng |  94 | M      |
|   3 | Miejue Shitai |  77 | F      |
|   4 | Lin Chaoying  |  93 | F      |
|   5 | hhh           |  25 | NULL   |
|   6 | sss           |  26 | NULL   |
+-----+---------------+-----+--------+
6 rows in set (0.00 sec)
```
8. 数据还原无误，没问题后开启二进制日志。  
```bash
MariaDB [hellodb]> set sql_log_bin=on;
Query OK, 0 rows affected (0.00 sec)

MariaDB [hellodb]> show variables like 'sql_log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| sql_log_bin   | ON    |
+---------------+-------+
1 row in set (0.00 sec)
```
9. 恢复用户访问。  












