#### 创建数据库

```sql
CREATE DATABASE IF NOT EXISTS dbname default CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

### 字符设置

#### 变量说明

> 一、character_set_client
> 　　主要用来设置客户端使用的字符集。
> 
> 二、character_set_connection
> 　　主要用来设置连接数据库时的字符集，如果程序中没有指明连接数据库使用的字符集类型则按照这个字符集设置。
> 
> 三、character_set_database
> 　　主要用来设置默认创建数据库的编码格式，如果在创建数据库时没有设置编码格式，就按照这个格式设置。
> 
> 四、character_set_server
> 　　服务器安装时指定的默认编码格式，这个变量建议由系统自己管理，不要人为定义。
> 
> 五、character_set_results
> 　　数据库给客户端返回时使用的编码格式，如果没有指明，使用服务器默认的编码格式。
> 
> - <font color=red>后面三个系统变量我们不需要关心，不会影响乱码等问题</font>
> 
> 六、character_set_filesystem
> 　　文件系统的编码格式，把操作系统上的文件名转化成此字符集，即把 character_set_client转换character_set_filesystem， 默认binary是不做任何转换的。
> 
> 七、character_set_system
> 　　数据库系统使用的编码格式，这个值一直是utf8，不需要设置，它是为存储系统元数据的编码格式。
> 
> 八、character_sets_dir
> 　　这个变量是字符集安装的目录。

#### 确认字符集

```sql
select * from information_schema.SCHEMATA where schema_name="dbname";
show variables like 'character_set_%'
```

#### 变更DB的字符集

```sql
ALTER DATABASE dbname CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

#### 如果是在客户端，已经是在dbname库中执行查询时需要将库名去掉：

```sql
ALTER DATABASE CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

#### 变更表的字符集

```sql
ALTER TABLE boo CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

#### /etc/my.cnf 初始化字符集配置

```ini
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci

[client]
default-character-set = utf8mb4

# 配置以上两个就可以了
[mysql]
default-character-set = utf8mb4

[mysql.server]
default-character-set = utf8mb4

[mysqld_safe]
default-character-set = utf8mb4
```

#### 授权

```sql
grant all privileges on dbname.* to username@'%' identified by 'Yl123456!!';
flush privileges;

# 服务器权限
GRANT Process ON *.* TO `iron_man`@`%`;
revoke Process on *.* from `iron_man`@`%`;
```

#### 查看 mysql 初始的密码策略

```sql
SHOW VARIABLES LIKE 'validate_password%';
```

#### 忘记 mysql 初始的密码策略

```ini
# 配置my.cnf, 在mysqld中添加
[mysqld]
skip-grant-tables
```

```sql
# 修改密码
use mysql
# 5.7为 authentication_string字段, 之前的是password
update user set authentication_string=password("123QWEasd"), password_expired='N' where user="root" and host='localhost';
# 最后把配置还原, 去掉 skip-grant-tables, 重启

# 或者登录后直接执行

alter user user() identified by "Yl123456!!";
```

#### 增删改查

##### 删除数据

```sql
DELETE FROM <表名> [WHERE 子句] [ORDER BY 子句] [LIMIT 子句]
```

#### 锁

##### 查询进程（如果您有SUPER权限，您可以看到所有线程。否则，您只能看到您自己的线程）

```sql
show processlist
```

##### 杀死进程id（就是上面命令的id列）

```sql
kill id
```

##### 查看下在锁的事务

```sql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
```

##### 查看当前的事务

```sql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
```

##### 查看当前锁定的事务

```sql
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;
```

##### 查看当前等锁的事务

```sql
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS;
```

#### 索引

##### 查看对应表的索引

```sql
show index from table_name;
```

##### 创建索引的语句

```sql
ALTER TABLE table_name ADD INDEX index_name (column_list);
```

##### 删除索引

```sql
ALTER TABLE table_name DROP INDEX index_name;
```

#### 删除行

```sql
DELETE FROM 表名称 WHERE 列名称 = 值
#删除某行
DELETE FROM Person WHERE LastName = 'Wilson' 
#删除所有行
DELETE FROM table_name
DELETE * FROM table_name
```

#### 添加, 修改和删除字段

```sql
#添加字段
alter table MyClass add passtest int(4) default '0'; 
#修改字段
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型定义;
#删除字段
alter table 表名 drop column 字段名
```

#### mysqldump

```shell
#导出
mysqldump -uroot -p db_name table_name > file.sql
# 或
# --single-transaction  InnoDB的数据表很有用，且不会锁表
mysqldump -uroot -p'1qaz@WSX3edc!!' --single-transaction --default-character-set=utf8mb4 dboptimusprime > /data/mysql_backup/optimus-prime-backup_$(date +%Y%m%d).sql

# 导入
#命令行
mysql -uroot -p db_name < file.sql  (最好使用绝对路径)

#登入后
source file.sql (最好使用绝对路径)


######################
# 如果有二进制的字段longblod, blod要加一个参数

# 导出要加--hex-blob
mysqldump -uroot -p --single-transaction --hex-blob --default-character-set=utf8mb4 dboptimusprime > optimus-prime-backup_m2_20211129.sql
# 导入要加--binary-mode
mysql --binary-mode -uoptimusprime -p dboptimusprime < optimus-prime-backup_m2_20211129.sql
```

#### 主外键检查

```sql
# 停止主外键检查
SET foreign_key_checks = 0
# 启用主外键检查
SET foreign_key_checks = 1
```

#### 查看变量

```sql
SELECT * FROM information_schema.global_variables

# 或
SHOW GLOBAL VARIABLES LIKE "%AA%"
```

## kill 某个用户下的慢sql

```sql
select concat('kill ',id,';') from information_schema.processlist where user='simba_acs' and Time >20;
```

## insert

> 不存在则插入

```sql
insert into `uc_user_role` (user_id, role_id) select 2, 5 from dual where not exists (select *  from `uc_user_role` where `user_id` = 2 and `role_id` = 5);
```

 > 查看表size

```sql
SELECT CONCAT(table_schema,'.',table_name) AS 'Table Name', CONCAT(ROUND(table_rows/1000000,4),'M') AS 'Number of Rows', CONCAT(ROUND(data_length/(1024*1024*1024),4),'G') AS 'Data Size',CONCAT(ROUND(index_length/(1024*1024*1024),4),'G') AS 'Index Size', CONCAT(ROUND((data_length+index_length)/(1024*1024*1024),4),'G') AS'Total'FROM information_schema.TABLES  ORDER BY total DESC;
```
