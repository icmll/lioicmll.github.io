#### 1. Linux Centos7x系统优化

```shell
目录
1、修改网卡名为eth0
2、更新系统
3、禁用selinux
4、关闭防火墙安装iptables
5、设置字符集
6、安装yum(常用工具)
7、安装阿里源
8、配置sshd
9、加大打开文件数的限制(open files)
10、用户进程限制
11、优化内核
12、时间同步

1、修改网卡名为eth0
cd /etc/sysconfig/network-scripts/
mv ifcfg-eno16777736 ifcfg-eth0

cat > ifcfg-eth0 << EOF
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=static
IPADDR=192.168.2.2
NETMASK=255.255.255.0
GATEWAY=192.168.2.1
DNS1=114.114.114.114
DNS2=218.85.85.199
EOF

#编辑vi /etc/default/grub 文件，加入“net.ifnames=0 biosdevname=0 ”到GRUBCMDLINELINUX变量来实现的。

GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap net.ifnames=0 biosdevname=0 rhgb quiet"
GRUB_DISABLE_RECOVERY="true"

#################========================================================#################
#linux centos7.4 修改方法，ATTR{address}==MAC地址，NAME=网卡名字
vim /usr/lib/udev/rules.d/60-net.rules
#ACTION=="add", SUBSYSTEM=="net", DRIVERS=="?*", ATTR{type}=="1", PROGRAM="/lib/udev/rename_device", RESULT=="?*", NAME="$result"
 ACTION=="add", SUBSYSTEM=="net", DRIVERS=="?*", ATTR{type}=="1", ATTR{address}==00:50:56:90:9d:dc NAME="eth0"

#将eth0网卡重命名成eth1，立即生效，重启后修改丢失。
ip link set dev eth0 name eth1
#################========================================================#################


运行命令 grub2-mkconfig -o /boot/grub2/grub.cfg 来重新生成GRUB配置并更新内核参数。

2、#更新系统
yum update -y

3、#禁用selinux
#查看SELinux状态：enabled/Disabled=开启/关闭
/usr/sbin/sestatus -v
#也可以用这个命令检查
getenforce

#关闭SELinux：
#临时关闭（无需重启）
setenforce 0
#临时开启（无需重启）
setenforce 1

#永久关闭SELINUX=enforcing/disabled  开启/关闭
vi /etc/selinux/config
或下列sed命令直接执行
[root@localhost ~]# grep -i  ^selinux   /etc/selinux/config
SELINUX=enforcing
SELINUXTYPE=targeted
[root@localhost ~]# sed -i '/^SELINUX/s/enforcing/disabled/g' /etc/selinux/config
[root@localhost ~]# getenforce
Enforcing
[root@localhost ~]# reboot

4、#关闭防火墙安装iptables
systemctl stop firewalld.service
systemctl disable firewalld.service
yum install iptables-services -y

#查看并管理服务
[root@localhost ~]# systemctl -t service
[root@localhost ~]# systemctl list-unit-files -t service

5、#设置字符集
[root@localhost ~]# echo $LANG
zh_CN.UTF-8
[root@localhost ~]# vi /etc/locale.conf
LANG="en_US.UTF-8"
#刷新生效
[root@localhost ~]# source /etc/locale.conf

6、#安装yum
yum install -y vim wget lrzsz tree net-tools bind-utlis 

7、#安装阿里源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

8、#配置sshd
sed -i -e '49s/^#//g' /etc/ssh/sshd_config            ##启用49行配置
sed -i -e '49s/yes/no/g' /etc/ssh/sshd_config         ##禁止root使用ssh登录
sed -i -e '129s/#/ /g' /etc/ssh/sshd_config           ##禁止UseDNS 
sed -i -e '129s/yes$/no/g' /etc/ssh/sshd_config
sed -i '/^GSS/s/yes/no/g' /etc/ssh/sshd_config        ##禁用GSSAPI认证加快登录速度
systemctl restart sshd                                 ##重新启动服务
systemctl enable  sshd                                 ##设置为开机启动
systemctl status  sshd                                 ##查看状态

9、#加大打开文件数的限制（open files）
ulimit -n
ulimit -a
vi /etc/security/limits.conf
最后添加
* soft nofile 1024000
* hard nofile 1024000
hive   - nofile 1024000
hive   - nproc  1024000

10、#用户进程限制
[root@hequan ~]# sed -i 's#4096#65535#g'   /etc/security/limits.d/20-nproc.conf  #加大普通用户限制  也可以改为unlimited
[root@hequan ~]# egrep -v "^$|^#" /etc/security/limits.d/20-nproc.conf

11、#优化内核
cat > /etc/sysctl.conf << EOF
#CTCDN系统优化参数
#关闭ipv6
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
#决定检查过期多久邻居条目
net.ipv4.neigh.default.gc_stale_time=120
#使用arp_announce / arp_ignore解决ARP映射问题
net.ipv4.conf.default.arp_announce = 2
net.ipv4.conf.all.arp_announce=2
net.ipv4.conf.lo.arp_announce=2
# 避免放大攻击
net.ipv4.icmp_echo_ignore_broadcasts = 1
# 开启恶意icmp错误消息保护
net.ipv4.icmp_ignore_bogus_error_responses = 1
#关闭路由转发
net.ipv4.ip_forward = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
#开启反向路径过滤
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
#处理无源路由的包
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
#关闭sysrq功能
kernel.sysrq = 0
#core文件名中添加pid作为扩展名
kernel.core_uses_pid = 1
# 开启SYN洪水攻击保护
net.ipv4.tcp_syncookies = 1
#修改消息队列长度
kernel.msgmnb = 65536
kernel.msgmax = 65536
#设置最大内存共享段大小bytes
kernel.shmmax = 68719476736
kernel.shmall = 4294967296
#timewait的数量，默认180000
net.ipv4.tcp_max_tw_buckets = 6000
net.ipv4.tcp_sack = 1
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_rmem = 4096        87380   4194304
net.ipv4.tcp_wmem = 4096        16384   4194304
net.core.wmem_default = 8388608
net.core.rmem_default = 8388608
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
#每个网络接口接收数据包的速率比内核处理这些包的速率快时，允许送到队列的数据包的最大数目
net.core.netdev_max_backlog = 262144
#限制仅仅是为了防止简单的DoS 攻击
net.ipv4.tcp_max_orphans = 3276800
#未收到客户端确认信息的连接请求的最大值
net.ipv4.tcp_max_syn_backlog = 262144
net.ipv4.tcp_timestamps = 0
#内核放弃建立连接之前发送SYNACK 包的数量
net.ipv4.tcp_synack_retries = 1
#内核放弃建立连接之前发送SYN 包的数量
net.ipv4.tcp_syn_retries = 1
#启用timewait 快速回收
net.ipv4.tcp_tw_recycle = 1
#开启重用。允许将TIME-WAIT sockets 重新用于新的TCP 连接
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_mem = 94500000 915000000 927000000
net.ipv4.tcp_fin_timeout = 1
#当keepalive 起用的时候，TCP 发送keepalive 消息的频度。缺省是2 小时
net.ipv4.tcp_keepalive_time = 1800
net.ipv4.tcp_keepalive_probes = 3
net.ipv4.tcp_keepalive_intvl = 15
#允许系统打开的端口范围
net.ipv4.ip_local_port_range = 1024    65000
#修改防火墙表大小，默认65536
net.netfilter.nf_conntrack_max=655350
net.netfilter.nf_conntrack_tcp_timeout_established=1200
# 确保无人能修改路由表
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0
EOF

#阿里云优化内核
cat > /etc/sysctl.conf << EOF
net.ipv4.ip_forward = 0
net.ipv4.conf.default.accept_source_route = 0
kernel.sysrq = 0
kernel.core_uses_pid = 1
kernel.msgmnb = 65536
kernel.msgmax = 65536
kernel.shmmax = 68719476736
kernel.shmall = 4294967296
vm.swappiness = 0
net.ipv4.neigh.default.gc_stale_time=120
net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.default.rp_filter=0
net.ipv4.conf.default.arp_announce = 2
net.ipv4.conf.lo.arp_announce=2
net.ipv4.conf.all.arp_announce=2
net.ipv4.tcp_max_tw_buckets = 5000
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 1024
net.ipv4.tcp_synack_retries = 2
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
EOF

#生效
sysctl -p

12、#时间同步
# 安装
yum install chrony 或 yum install ntp
# 启用
systemctl start chronyd
systemctl enable chronyd

/etc/init.d/chronyd restart
chkconfig chronyd on
--------------------------------------
systemctl start ntpd
systemctl enable ntpd

cat >> /etc/ntp.conf <<EOF
driftfile  /var/lib/ntp/drift
pidfile   /var/run/ntpd.pid
logfile /var/log/ntp.log
restrict    default kod nomodify notrap nopeer noquery
restrict -6 default kod nomodify notrap nopeer noquery
restrict 127.0.0.1
server 127.127.1.0
fudge  127.127.1.0 stratum 10
server ntp.aliyun.com iburst minpoll 4 maxpoll 10
restrict ntp.aliyun.com nomodify notrap nopeer noquery
EOF



chkconfig ntpd on
/etc/init.d/ntpd restart
# 设置亚洲时区 将系统时区改为上海时间 (亦即CST时区)
timedatectl set-timezone Asia/Shanghai
-----------------------------------------------------------------
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 启用NTP同步
timedatectl set-ntp yes

yum install chrony

cat >> /etc/chrony.conf <<EOF
server ntp.aliyun.com iburst
stratumweight 0
driftfile /var/lib/chrony/drift
rtcsync
makestep 10 3
bindcmdaddress 127.0.0.1
bindcmdaddress ::1
keyfile /etc/chrony.keys
commandkey 1
generatecommandkey
logchange 0.5
logdir /var/log/chrony
EOF

systemctl start chronyd
systemctl enable chronyd
```



#### 1. 修改linux TCP连接数

```shell
vim /etc/sysctl.conf
net.ipv4.tcp_max_tw_buckets = 50000

#最后追加
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_timestamps = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_recycle = 1

#刷新sysctl
sysctl -p

cat > /etc/sysctl.conf <<EOF
net.ipv4.ip_forward = 0
net.ipv4.conf.default.accept_source_route = 0
kernel.sysrq = 0
kernel.core_uses_pid = 1
net.bridge.bridge-nf-call-ip6tables = 0
net.bridge.bridge-nf-call-iptables = 0
net.bridge.bridge-nf-call-arptables = 0
kernel.msgmnb = 65536
kernel.msgmax = 65536
kernel.shmmax = 68719476736
kernel.shmall = 4294967296
vm.swappiness = 0
net.ipv4.neigh.default.gc_stale_time=120
net.ipv4.conf.all.rp_filter=0
net.ipv4.conf.default.rp_filter=0
net.ipv4.conf.default.arp_announce = 2
net.ipv4.conf.all.arp_announce=2
net.ipv4.tcp_max_tw_buckets = 1000000
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 1024
net.ipv4.tcp_synack_retries = 2
net.ipv4.conf.lo.arp_announce=2
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_timestamps = 1
net.ipv4.tcp_tw_reuse = 1
EOF

vim /etc/profile
#添加搜索高亮显示
alias grep='grep --color=auto'
#添加history时间,用户显示
export HISTTIMEFORMAT="%F %T `whoami`  "
export PS1='[\u@\H \W]\$ '
```

#### 2. 调整Linux系统其它用户打开进程数(默认是1024)

```shell
vim /etc/security/limits.d/90-nproc.conf 
#不限制硬件线程
* soft nproc 102400
#不限制帐号软件线程
* hard nproc 102400

unlimited
```

#### linux 回收站

```shell
# 创建回收站目录
mkdir -p /data/.recycling
chmod 777 /data/.recycling

# 使全局生效，在/etc/bashrc文件底部添加如下内容
alias rm=trash
alias rl='ls /data/.recycling'
alias ur=undelfile
undelfile()
{
  mv -i /data/.recycling/$@ ./
}
trash()
{  
  mv $@ /data/.recycling/
}
cleartrash()
{
    read -p "Clear trash?[n]" confirm
    [ $confirm == 'y' ] || [ $confirm == 'Y' ]  && /usr/bin/rm -rf /data/.recycling/*
}

# 刷新使全局变量生效
source /etc/bashrc

# 删除一个文件夹（包含helloworld下所有文件夹及文件）
rm helloworld

# 删除一个文件
rm abc.txt

# 撤销abc.txt删除
ur abc.txt

# 撤销helloworld文件夹删除
ur helloworld

# 列出回收站
rl

# 清空回收站
cleartrash
```



