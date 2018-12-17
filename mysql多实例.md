## 实现MySQL多实例    
背景：计划在centos7系统上实现MySQL多实例，所使用的程序为源码编译安装的mysql，路径为/app/mysql/bin。计划实现3个实例，端口号分别用3306,3307和3308。    
1. 确认mysql程序是否存在？  
```bash
[root@centos7 ~]#ls /app/mysql/bin
aria_chk           mysqlcheck                  mysqltest
aria_dump_log      mysql_client_test           mysql_tzinfo_to_sql
aria_ftdump        mysql_config                mysql_upgrade
aria_pack          mysql_convert_table_format  mysql_waitpid
aria_read_log      mysqld                      mytop
innochecksum       mysqld_multi                perror
mariabackup        mysqld_safe                 replace
mariadb_config     mysqld_safe_helper          resolveip
mbstream           mysqldump                   resolve_stack_dump
msql2mysql         mysqldumpslow               sst_dump
myisamchk          mysql_find_rows             tokuftdump
myisam_ftdump      mysql_fix_extensions        tokuft_logprint
myisamlog          mysqlhotcopy                wsrep_sst_common
myisampack         mysqlimport                 wsrep_sst_mariabackup
my_print_defaults  mysql_ldb                   wsrep_sst_mysqldump
myrocks_hotbackup  mysql_plugin                wsrep_sst_rsync
mysql              mysql_secure_installation   wsrep_sst_rsync_wan
mysqlaccess        mysql_setpermission         wsrep_sst_xtrabackup
mysqladmin         mysqlshow                   wsrep_sst_xtrabackup-v2
mysqlbinlog        mysqlslap
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：路径为源码编译mysql时指定的路径。  

2. 确认没有mysqld服务启动（即无3306端口打开）。  
```bash
[root@centos7 ~]#ss -ntl            
State       Recv-Q Send-Q Local Address:Port               Peer Address:Port              
LISTEN      0      128        *:48700                  *:*                  
LISTEN      0      128        *:111                    *:*                  
LISTEN      0      5      192.168.122.1:53                     *:*                  
LISTEN      0      128        *:22                     *:*                  
LISTEN      0      128    127.0.0.1:631                    *:*                  
LISTEN      0      100    127.0.0.1:25                     *:*                  
LISTEN      0      128       :::45260                 :::*                  
LISTEN      0      128       :::111                   :::*                  
LISTEN      0      128       :::22                    :::*                  
LISTEN      0      128      ::1:631                   :::*                  
LISTEN      0      100      ::1:25                    :::* 
```

3. 规划相关目录，/etc 放配置文件，/data 为数据库目录，/log 为日志目录，/socket 为套接字目录，/pid 为记录pid的目录；并更改目录属性，所有者和所属组都为mysql。    
```bash
[root@centos7 ~]#mkdir -pv /mysql/{3306,3307,3308}/{etc,pid,log,data,socket}
mkdir: created directory ‘/mysql/3306/etc’
mkdir: created directory ‘/mysql/3306/pid’
mkdir: created directory ‘/mysql/3306/log’
mkdir: created directory ‘/mysql/3306/data’
mkdir: created directory ‘/mysql/3306/socket’
mkdir: created directory ‘/mysql/3307/etc’
mkdir: created directory ‘/mysql/3307/pid’
mkdir: created directory ‘/mysql/3307/log’
mkdir: created directory ‘/mysql/3307/data’
mkdir: created directory ‘/mysql/3307/socket’
mkdir: created directory ‘/mysql/3308/etc’
mkdir: created directory ‘/mysql/3308/pid’
mkdir: created directory ‘/mysql/3308/log’
mkdir: created directory ‘/mysql/3308/data’
mkdir: created directory ‘/mysql/3308/socket’
[root@centos7 ~]#tree /mysql
/mysql
├── 3306
│   ├── data
│   ├── etc
│   ├── log
│   ├── pid
│   └── socket
├── 3307
│   ├── data
│   ├── etc
│   ├── log
│   ├── pid
│   └── socket
└── 3308
    ├── data
    ├── etc
    ├── log
    ├── pid
    └── socket

18 directories, 0 files
```
```bash
[root@centos7 mysql]#chown -R mysql:mysql /mysql
[root@centos7 mysql]#ll
total 0
drwxr-xr-x 7 mysql mysql 65 Dec  1 09:43 3306
drwxr-xr-x 7 mysql mysql 65 Dec  1 09:43 3307
drwxr-xr-x 7 mysql mysql 65 Dec  1 09:43 3308
[root@centos7 mysql]#ll -d 
drwxr-xr-x 5 mysql mysql 42 Dec  1 09:42 .
```
4. 下面以3306为例，在/mysql/3306/data下生成数据库。生成数据库的脚本在/app/mysql/scripts下（该脚本执行时依赖/app/mysql/bin下的文件，必须在/app/mysql目录下执行。    
```bash
[root@centos7 mysql]#ls /app/mysql/scripts/
mysql_install_db
[root@centos7 mysql]#cd /app/mysql/
[root@centos7 mysql]#./scripts/mysql_install_db --user=mysql --datadir=/mysql/3306/data
Installing MariaDB/MySQL system tables in '/mysql/3306/data' ...
2018-12-01 10:47:16 140481849399424 [Warning] 'THREAD_CONCURRENCY' is deprecated and will be removed in a future release.
2018-12-01 10:47:17 140481737561856 [Warning] Failed to load slave replication state from table mysql.gtid_slave_pos: 1146: Table 'mysql.gtid_slave_pos' doesn't exist
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system


PLEASE REMEMBER TO SET A PASSWORD FOR THE MariaDB root USER !
To do so, start the server, then issue the following commands:

'./bin/mysqladmin' -u root password 'new-password'
'./bin/mysqladmin' -u root -h centos7.localdomain password 'new-password'

Alternatively you can run:
'./bin/mysql_secure_installation'

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the MariaDB Knowledgebase at http://mariadb.com/kb or the
MySQL manual for more instructions.

You can start the MariaDB daemon with:
cd '.' ; ./bin/mysqld_safe --datadir='/mysql/3306/data'

You can test the MariaDB daemon with mysql-test-run.pl
cd './mysql-test' ; perl mysql-test-run.pl

Please report any problems at http://mariadb.org/jira

The latest information about MariaDB is available at http://mariadb.org/.
You can find additional information about the MySQL part at:
http://dev.mysql.com
Consider joining MariaDB's strong and vibrant community:
https://mariadb.org/get-involved/
[root@centos7 mysql]#ls /mysql/3306/data
aria_log.00000001  ibdata1      mysql             mysql-bin.state
aria_log_control   ib_logfile0  mysql-bin.000001  performance_schema
ib_buffer_pool     ib_logfile1  mysql-bin.index   test
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：mysql用户在源码编译时已经创建好。  

5. 准备配置文件，可以拷贝rpm包安装mariadb时生成的配置文件/etc/my.cnf再做修改。   
```bash
[root@centos7 ~]# scp /etc/my.cnf @192.168.120.130:/root/
root@192.168.120.130's password: 
my.cnf                                     100%  570   354.0KB/s   00:00
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：从其他centos7主机上拷贝到本机root家目录下。  
```bash
[root@centos7 ~]#ls
anaconda-ks.cfg  Downloads             Music     playbook
core.2226        f1                    my.cnf    Public
Desktop          initial-setup-ks.cfg  passwd    Templates
Documents        ks.cfg                Pictures  Videos
[root@centos7 ~]#cp my.cnf /mysql/3306/etc/
[root@centos7 ~]#vim /mysql/3306/etc/my.cnf 
[mysqld]
port=3306
datadir=/mysql/3306/data
socket=/mysql/3306/socket/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

[mysqld_safe]
log-error=/mysql/3306/log/mariadb.log
pid-file=/mysql/3306/pid/mariadb.pid

#
# include all files from the config directory
#
#!includedir /etc/my.cnf.d                                                   
                                                                     
"/mysql/3306/etc/my.cnf" 20L, 585C written   
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：配置文件主要修改端口号（默认为3306），datadir、socket、log、pid等的文件路径，均在/mysql/3306/下。最后一行有关客户端配置文件加注释。  

6. 准备服务启动脚本。  
```bash
[root@centos7 3306]#vim mysqld
#!/bin/bash
#chkconfig: 345 80 2
port=3306
mysql_user="root"
mysql_pwd=""
cmd_path="/app/mysql/bin"
mysql_basedir="/mysql"
mysql_sock="${mysql_basedir}/${port}/socket/mysql.sock"

function_start_mysql()
{
    if [ ! -e "$mysql_sock" ];then
      printf "Starting MySQL...\n"
    else
      printf "MySQL is running...\n"
      exit

#!/bin/bash                                                                  
#chkconfig: 345 80 2
port=3306
mysql_user="root"
mysql_pwd=""
cmd_path="/app/mysql/bin"
mysql_basedir="/mysql"
mysql_sock="${mysql_basedir}/${port}/socket/mysql.sock"

function_start_mysql()
{
    if [ ! -e "$mysql_sock" ];then
      printf "Starting MySQL...\n"
      ${cmd_path}/mysqld_safe --defaults-file=${mysql_basedir}/${port}/etc/my
.cnf  &> /dev/null  &
    else
      printf "MySQL is running...\n"
      exit
    fi
}


function_stop_mysql()
{
    if [ ! -e "$mysql_sock" ];then
       printf "MySQL is stopped...\n"
       exit
    else
       printf "Stoping MySQL...\n"
       ${cmd_path}/mysqladmin -u ${mysql_user} -p${mysql_pwd} -S ${mysql_sock
} shutdown
   fi
}


function_restart_mysql()
"mysqld" 54L, 1024C written          
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：修改端口号，口令为空。  

7. 给启动脚本加权限，并启动。也可以把启动脚本放到/etc/init.d中，加到chkconfig列表，用service启动。    
```bash
[root@centos7 3306]#chmod 700 mysqld
[root@centos7 3306]#ls
data  etc  log  mysqld  pid  socket
[root@centos7 3306]#./mysqld start
Starting MySQL...
[root@centos7 3306]#ss -ntl
State       Recv-Q Send-Q Local Address:Port               Peer Address:Port              
LISTEN      0      128        *:48700                  *:*                  
LISTEN      0      128        *:111                    *:*                  
LISTEN      0      5      192.168.122.1:53                     *:*                  
LISTEN      0      128        *:22                     *:*                  
LISTEN      0      128    127.0.0.1:631                    *:*                  
LISTEN      0      100    127.0.0.1:25                     *:*                  
LISTEN      0      80        :::3306                  :::*                  
LISTEN      0      128       :::45260                 :::*                  
LISTEN      0      128       :::111                   :::*                  
LISTEN      0      128       :::22                    :::*                  
LISTEN      0      128      ::1:631                   :::*                  
LISTEN      0      100      ::1:25                    :::* 
```
8. 登录mysql。  
```bash
[root@centos7 3306]#mysql -uroot -p -S /mysql/3306/socket/mysql.sock 
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 10.2.19-MariaDB Source distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：以root身份，口令为空，指定套接字文件本地登陆成功。  

9. 同样流程操作3307和3308（部分结果省略，同3306）。  
+ 生成数据库  
```bash
[root@centos7 mysql]#./scripts/mysql_install_db --user=mysql --datadir=/mysql/3307/data/
[root@centos7 mysql]#./scripts/mysql_install_db --user=mysql --datadir=/mysql/3308/data/  
```
+ 配置文件，端口号分别改为3307/3308    
```bash
[root@centos7 mysql]#cd /mysql
[root@centos7 mysql]#ls
3306  3307  3308
[root@centos7 mysql]#cp 3306/etc/my.cnf  3307/etc/
[root@centos7 mysql]#cp 3306/etc/my.cnf  3308/etc/
```
+ 准备服务启动脚本，并修改端口号    
```bash
[root@centos7 mysql]#cp 3306/mysqld 3307/
[root@centos7 mysql]#cp 3306/mysqld 3308/
```
+ 启动服务  
```bash
[root@centos7 mysql]#./3307/mysqld start
Starting MySQL...
[root@centos7 mysql]#./3308/mysqld start 
Starting MySQL...
[root@centos7 mysql]#ss -ntl
State       Recv-Q Send-Q Local Address:Port               Peer Address:Port              
LISTEN      0      128        *:48700                  *:*                  
LISTEN      0      128        *:111                    *:*                  
LISTEN      0      5      192.168.122.1:53                     *:*                  
LISTEN      0      128        *:22                     *:*                  
LISTEN      0      128    127.0.0.1:631                    *:*                  
LISTEN      0      100    127.0.0.1:25                     *:*                  
LISTEN      0      80        :::3306                  :::*                  
LISTEN      0      80        :::3307                  :::*                  
LISTEN      0      80        :::3308                  :::*                  
LISTEN      0      128       :::45260                 :::*                  
LISTEN      0      128       :::111                   :::*                  
LISTEN      0      128       :::22                    :::*                  
LISTEN      0      128      ::1:631                   :::*                  
LISTEN      0      100      ::1:25                    :::*  
```

## 总结  
至此，多实例实验已经完成。登录mysql，若想执行安全脚本mysql_secure_installation，后面需要加-S 套接字文件路径选项。     




