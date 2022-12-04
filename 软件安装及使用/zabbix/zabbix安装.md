### 官方安装
1. 安装zabbix源

```
rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm

#如果出现类似警告
# warning: /var/tmp/rpm-tmp.aYEs0v: Header V4 RSA/SHA512 Signature, key ID a14fe591: NOKEY
# 由于yum安装了旧版本的GPG keys造成的, 则执行以下命令
rpm --import /etc/pki/rpm-gpg/RPM*
# 查询已安装的rpm源
rpm -qa | grep zabbix
#删除源
rpm -e zabbix-agent-5.0.2-1.el7.x86_64

yum clean all
```

2. 安装zabbix server和agent

```
yum install zabbix-server-mysql zabbix-agent
```

3. 安装zabbix前端

```
yum-config-manager --enable rhel-server-rhscl-7-rpms
yum-config-manager --enable zabbix-frontend
yum install zabbix-web-mysql-scl zabbix-nginx-conf-scl
```

4. 创建初始数据库, 编码一定要是utf8和utf8_bin

```
create database zabbix character set utf8 collate utf8_bin;
create user zabbix@localhost identified by 'password';
grant all privileges on zabbix.* to zabbix@localhost;

#导入数据
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

5. 为Zabbix server配置数据库, 编辑/etc/zabbix/zabbix_server.conf

```
DBPassword=password
```

6. Zabbix前端配置PHP
> 编辑配置文件 /etc/opt/rh/rh-nginx116/nginx/conf.d/zabbix.conf, uncomment and set 'listen' and 'server_name' directives.

```
# listen 80;
# server_name example.com;
```

> 编辑配置文件 /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf, add nginx to listen.acl_users directive.

```
listen.acl_users = apache,nginx

; php_value[date.timezone] = Europe/Riga
替换
php_value[date.timezone] = Asia/Shanghai
```
7. 启动Zabbix server和agent进程

```
systemctl restart zabbix-server zabbix-agent rh-nginx116-nginx rh-php72-php-fpm
systemctl enable zabbix-server zabbix-agent rh-nginx116-nginx rh-php72-php-fpm
```

8. 设置selinux 为disabled, 否则数据库连不上

```
#关闭SELINUX, 将SELINUX=enforcing改为SELINUX=disabled
vi /etc/selinux/config

#设置为permissive模式，临时生效
setenforce 0
```

9.agent 安装及配置

```
rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm
yum install -y zabbix-agent


LogFile=/tmp/zabbix_agentd.log
StartAgents=0          #客户端agent模式，仅为主动模式,值为0的时候，被监控端的zabbix_agentd 不监听本地端口，所以无法在 netstat -tunpl 中查看到zabbix_agentd进程
Server=**.**.**.**              #zabbix_server的ip
ServerActive=**.**.**.**    #zabbix_server的ip
Hostname=test_host    #重要：主机名, 可以使用本机ip, 这个与添加主机是的 Host name 一致
RefreshActiveChecks=1800    #被监控端到服务器获取监控项的周期
BufferSize=200        #被监控端存储监控信息的空间大小
Timeout=10            #超时时间

```


10. 关闭防火墙访问


```
systemctl stop firewalld  
```






