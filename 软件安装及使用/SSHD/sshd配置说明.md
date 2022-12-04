## 1. 免密配置[官方文档](https://www.ssh.com/ssh/config/)

### 1-1. 配置

```shell
vim /etc/ssh/sshd_config


#允许root认证登录, 非root可以不要
PermitRootLogin yes
#允许密钥认证, centos7.4中不需要RSAAuthentication,第1代ssh通讯协议使用的配置项，在CentOS7.4中被废除了, 如果添加了会报错: reprocess config line 66: Deprecated option RSAAuthentication Dec 14 10:55:2
RSAAuthentication yes
PubkeyAuthentication yes
#默认公钥存放的位置
AuthorizedKeysFile  .ssh/authorized_keys

# 允许密码登录
PasswordAuthentication yes
```

### 1-2. 目录权限

> .ssh:700
> 
> authorized_keys:600
> 
> id_rsa:600
> 
> id_rsa.pub:644
> 
> known_hosts:644
> 
> 登录用户的home: 700 或 755等, 只要不是是77x, 还要注意查看用户home目录的所有者和组是否正确, 否则会报以下错误
> 
> Authentication refused: bad ownership or modes for directory /root

### 1-3. 日志查看

```shell
less /var/log/secure
```

### 1-4. 客户端配置

```shell
# 生成指定密钥
ssh-keygen
# 拷贝公钥到服务器
ssh-copy-id -i /c/Users/yangl/.ssh/id_rsa_development.pub root@192.168.234.137

# 登入, 如果公钥没有别名(id_rsa), 不要-i指定, 也可在config中配置
ssh -i /c/Users/yangl/.ssh/id_rsa_development root@192.168.234.137

# .ssh/config

Host 192.168.234.137
    HostName 192.168.234.137
    User root
    IdentityFile C:\Users\yangl\.ssh\id_rsa_development
```

## 2. 使用

### 2-1. 根据私钥生成公钥

```shell
ssh-keygen -y -f id_rsa > id_rsa.pub
```

### 2-2. .ssh/config 的使用

#### 2-2-1. 代理转发
```shell
# 转发服务器
Host test_machine_jump
    HostName 87.98.180.288

# 可以匹配那些服务器走转发
Host 10.101.*.*
   ProxyCommand ssh test_machine_jump -W %h:%p
   # 这边要把转发服务器的私钥复制到客户端机器, 再指定私钥, 因为他是会用转发服务器的公钥取访问目标服务器, 所以客户端机器要有私钥
   IdentityFile ~/.ssh/id_rsa_abc
```
