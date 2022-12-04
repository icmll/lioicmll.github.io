环境    IP

master     192.168.155.155

slave     192.168.155.154

一. 配置master

---

1.主服务master修改

```
[mysqld]
# 设置server-id（唯一）
server-id=1

# 开启二进制日志
log-bin=mysql-bin
```

2.重启mysql，在master创建同步的用户帐号

```
# 创建用户 uuu 密码 123456
CREATE USER uuu@192.168.155.154 IDENTIFIED BY '123456';
# 分配权限（uuu帐号只有从服务器slave 192.168.155.154 有访问权限）
GRANT REPLICATION SLAVE ON *.* TO uuu@192.168.155.154;
# 刷新权限
FLUSH PRIVILEGES;
```

3. 查看master状态，记录二进制文件名

```
SHOW MASTER STATUS;
```

二. 配置slave

---

1.从服务器slave修改

```
[mysqld]
# 设置server-id（唯一）
server-id=2
```

2. 重启mysql，打开mysql会话，执行同步SQL语句(需要主服务器主机名，登陆凭据，二进制文件的   名称和位置)

```
mysql> CHANGE MASTER TO
-> MASTER_HOST='192.168.155.155',
-> MASTER_USER='uuu',
-> MASTER_PASSWORD='123456',
-> MASTER_LOG_FILE='mysql-bin.000001',
-> MASTER_LOG_POS=616;

# 也可以横向输入
CHANGE MASTER TO MASTER_HOST='192.168.155.155', MASTER_USER='uuu', MASTER_PASSWORD='123456', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=616;
```

3.启动slave同步进程

```
# 启动slave start slave;

# 关闭slave stop slave;
```

4.查看slave状态

```
SHOW SLAVE STATUS\G;
```
