## 1. 中科院镜像站安装：http://mirrors.ustc.edu.cn

> 使用 rpm -ivh 依次下载安装common， libs， client， devel， server

> mysql-community-common-5.7.26-1.el7.x86_64.rpm
> mysql-community-libs-5.7.26-1.el7.x86_64.rpm
> mysql-community-client-5.7.26-1.el7.x86_64.rpm
> mysql-community-devel-5.7.26-1.el7.x86_64.rpm
> mysql-community-server-5.7.26-1.el7.x86_64.rpm

```shell
rpm -ivh mysql-community-common-5.7.26-1.el7.x86_64.rpm --force --nodeps
rpm -ivh mysql-community-libs-5.7.26-1.el7.x86_64.rpm --force --nodeps
rpm -ivh mysql-community-client-5.7.26-1.el7.x86_64.rpm --force --nodeps
rpm -ivh mysql-community-devel-5.7.26-1.el7.x86_64.rpm --force --nodeps
rpm -ivh mysql-community-server-5.7.26-1.el7.x86_64.rpm --force --nodeps
```

## 2. 官网安装：

```bash
#下载mysql源：https://dev.mysql.com/downloads/repo/yum/
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

#安装mysql：https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/

#安装源
sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm

#查看包信息：
yum repolist all | grep mysql
yum repolist enabled | grep mysql

#设置可用包：
# 如果没有命令就安装
yum -y install yum-utils
sudo yum-config-manager --disable mysql80-community
sudo yum-config-manager --enable mysql57-community

#安装
sudo yum install mysql-community-server -y

#启动
sudo systemctl start mysqld.service

#查看root用户密码
grep 'temporary password' /var/log/mysqld.log

#修改密码：
mysql -uroot -p
ALTER USER 'root'@'localhost' IDENTIFIED BY '1qaz@WSX3edc!!';
```

## 3. 编译安装

```bash
#安装依赖包
yum -y install gcc gcc-c++ ncurses ncurses-devel cmake

#下载相应源码包
#boost
wget http://downloads.sourceforge.NET/project/boost/boost/1.59.0/boost_1_59_0.tar.gz
或
wget https://nchc.dl.sourceforge.net/project/boost/boost/1.59.0/boost_1_59_0.tar.gz

#mysql
wget http://cdn.mysql.com/Downloads/MySQL-5.7/mysql-5.7.9.tar.gz
或
wget http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.9.tar.gz

#添加mysql用户
useradd -M -s /sbin/nologin mysql
groupadd mysql
useradd -r -g mysql mysql

# 创建log, pid和数据存放的目录, 用mysql用户创建或则后面在去改归宿者和组
mkdir -p /var/run/mysqld
mkdir -p /var/log/mysqld
mkdir -p /data/mysql

tar xzf boost_1_59_0.tar.gz
tar xzf mysql-5.7.9.tar.gz
cd mysql-5.7.9

#编译安装
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/data/mysql \
-DDOWNLOAD_BOOST=1 \
-DWITH_BOOST=/data/boost_1_59_0 \
-DSYSCONFDIR=/etc \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITH_FEDERATED_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DENABLE_DTRACE=0 \
-DDEFAULT_CHARSET=utf8mb4 \
-DDEFAULT_COLLATION=utf8mb4_general_ci \
-DWITH_EMBEDDED_SERVER=1

make -j `grep processor /proc/cpuinfo | wc -l`

make install
```

## 4. 启动脚本,设置开机自启动

```bash
/bin/cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig mysqld on

# 因为pid是放在/var/run/底下, 这下面的目录机器重启后会被删除, 所以再启动脚本中加一句创建目录的命令
vim /etc/init.d/mysqld
# 在开头添加
mkdir -p /var/run/mysqld
chown -R mysql:mysql /var/run/mysqld
```

## 5. /etc/my.cnf参考配置, 根据自己的实际情况配置

```ini
cat > /etc/my.cnf << EOF
[client]
port = 3306
socket = /tmp/mysql.sock
default-character-set = utf8mb4
[mysqld]
port = 3306
socket = /tmp/mysql.sock
basedir = /usr/local/mysql
datadir = /data/mysql
pid-file = /var/run/mysqld/mysql.pid
user = mysql
bind-address = 0.0.0.0
server-id = 1
init-connect = 'SET NAMES utf8mb4'
character-set-server = utf8mb4
#skip-name-resolve
#skip-networking
back_log = 300
max_connections = 1000
max_connect_errors = 6000
open_files_limit = 65535
table_open_cache = 128
max_allowed_packet = 4M
binlog_cache_size = 1M
max_heap_table_size = 8M
tmp_table_size = 16M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
sort_buffer_size = 8M
join_buffer_size = 8M
key_buffer_size = 4M
thread_cache_size = 8
query_cache_type = 1
query_cache_size = 8M
query_cache_limit = 2M
ft_min_word_len = 4
log_bin = mysql-bin
binlog_format = mixed
expire_logs_days = 30
log_error = /var/log/mysqld/error.log
slow_query_log = 1
long_query_time = 1
slow_query_log_file = /var/log/mysqld/slow.log
performance_schema = 0
explicit_defaults_for_timestamp=true
#lower_case_table_names = 1
skip-external-locking
default_storage_engine = InnoDB
#default-storage-engine = MyISAM
innodb_file_per_table = 1
innodb_open_files = 500
innodb_buffer_pool_size = 64M
innodb_write_io_threads = 4
innodb_read_io_threads = 4
innodb_thread_concurrency = 0
innodb_purge_threads = 1
innodb_flush_log_at_trx_commit = 2
innodb_log_buffer_size = 2M
innodb_log_file_size = 32M
innodb_log_files_in_group = 3
innodb_max_dirty_pages_pct = 90
innodb_lock_wait_timeout = 120
bulk_insert_buffer_size = 8M
myisam_sort_buffer_size = 8M
myisam_max_sort_file_size = 10G
myisam_repair_threads = 1
interactive_timeout = 28800
wait_timeout = 28800
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
#不做dns反解
skip-name-resolve
[mysqldump]
quick
max_allowed_packet = 16M
[myisamchk]
key_buffer_size = 8M
sort_buffer_size = 8M
read_buffer = 4M
write_buffer = 4M
EOF
```

## 6. 修改自行创建目录的归宿者和组

```shell
chown -R mysql:mysql /data/mysql
chown -R mysql:mysql /var/run/mysqld
chown -R mysql:mysql /var/log/mysqld
chown -R mysql:mysql /usr/local/mysql/
```

## 7. 初始化数据库

```shell
/usr/local/mysql/bin/mysqld --initialize --user=mysql --console --datadir=/data/mysql
/usr/local/mysql/bin/mysqld install
```

## 8.设置环境变量

```shell
# 编辑~/.bash_profile, 添加
PATH=$PATH:/usr/local/mysql/bin
export PATH

#执行
source ~/.bash_profile
```

## 9. 最后重置root密码

### 9-2. 临时root密码在log文件中, 登入后要修改

```sql
alter user user() identified by "1qaz@WSX3edc!!";
```

### 9-2. 如果你没在log中找到初始的密码可以执行以下操作, 忘记root密码也是执行以下操作

```sql
# 配置my.cnf, 在mysqld中添加
[mysqld]
skip-grant-tables

# 修改密码
use mysql;
# 5.7为 authentication_string字段, 之前的是password
update user set authentication_string=password("1qaz@WSX3edc!!"), password_expired='N' where user="root" and host='localhost';
# 最后把配置还原, 去掉 skip-grant-tables, 重启
```

## docker 安装

```shell
docker pull mysql:5.7

# 创建配置文件目录, 并添加自己的配置文件my.cnf
mkdir /usr/local/mysql/conf
# 创建数据目录
mkdir /usr/local/mysql/data
# 创建日志目录
mkdir /usr/local/mysql/log
chmod 777 /usr/local/mysql/log
# 启动
docker run -itd --name mysql-01 -p 3306:3306 -v /usr/local/mysql/data:/var/lib/mysql -v /usr/local/mysql/conf:/etc/mysql -v /usr/local/mysql/log:/var/log/mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql:5.7

# 这边可能会出现个问题..就是root用户的host是localhost, 容器启动要把host修改成容器的网关地址就可以
update user set host='172.17.0.1' where user="root" and host='localhost';
```
