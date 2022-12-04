## 改主机名hostname

```shell
hostnamectl --static set-hostname aaa
```

## 网络配置

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33

#加上或修改以下两个, 先要能上网再说
ONBOOT=yes

# 然后重启网络
systemctl restart network
```

> 或者配置静态ip

```conf
#描述网卡对应的设备别名，例如ifcfg-ens33的文件中它为ens33
DEVICE=ens33
#设置网卡获得ip地址的方式，选项为static（静态），dhcp，bootp
BOOTPROTO=static
#类型=以太网络
TYPE=Ethernet
#代理模式
PROXY_METHOD=none
# 网卡物理设备名称
NAME=ens33
#启动或者重启网络时，是否启动该设备，yes是启动，no是不启动
ONBOOT=yes
IPADDR=192.168.178.3
NETMASK=255.255.255.0
#网关
GATEWAY=192.168.178.2
#DNS服务器地址
DNS1=192.168.178.2
# 默认路由
DEFROUTE=yes
# ipv4致命错误检测
IPV4_FAILURE_FATAL=no
# ipv6 自动初始化
IPV6INIT=yes
# ipv6自动配置
IPV6_AUTOCONF=yes
# ipv6 默认路由
IPV6_DEFROUTE=yes
# ipv6致命错误检测
IPV6_FAILURE_FATAL=no
# ipv6地址生成模型
IPV6_ADDR_GEN_MODE=stable-privacy

BROWSER_ONLY=no
```

## 设置时区

```
rm -f /etc/localtime
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

echo 'Asia/Shanghai' > /etc/timezone
```

## 配置阿里源

```shell
# 先下载wget
yum install wget
# 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 更新
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

# 个人建议卸载epel-release, 因为我们使用了阿里源, 不然每次安装老是要更新epel
yum erase epel-release -y

# 生成缓存
yum makecache fast
# 更新yum源 
yum -y update
```

## 安装常用工具

```shell
yum install ntpdate -y

yum install net-tools -y

yum install wget vim -y

yum install epel-release -y

yum install ntp -y

yum install htop -y

yum install iotop -y

yum install lsof -y

yum install gcc gcc-c++ -y

yum install openssl openssl-devel -y

yum install -y yum-fastestmirror

yum -y install yum-utils

yum install nfs-utils

## ss 命令
yum install iproute iproute-doc -y


```

## 同步网络时间

```shell
ntpdate ntp1.aliyun.com

# 设置定时
crontab -e
0 */1 * * * /usr/sbin/ntpdate ntp1.aliyun.com
```

## 命令行提示符配置

- \s      表示所用shell
- \d      代表日期，格式为weekday month date，例如："Mon Aug 1"
- \H      完整的主机名称
- \h      仅取主机的第一个名字
- \t      显示时间为24小时格式，如：HH：MM：SS
- \T      显示时间为12小时格式
- \A      显示时间为24小时格式：HH：MM
- \u      当前用户的账号名称
- \v      BASH的版本信息
- \w     完整的工作目录名称
- \W    利用basename取得工作目录名称，所以只会列出最后一个目录
- \\#     下达的第几个命令
- \$       提示字符，如果是root时，提示符为：# ，普通用户则为：$

```shell
vim ~/.bash_profile
# 添加
export PS1='\[\e[32;1m\][\u@\H \W]\$ \[\e[m\]'
或
export PS1='\[\e[33;1m\][\u@\h \w]\$\[\e[m\]'

source ~/.bash_profile
```

## history配置

```shell
vim ~/.bash_profile
# 添加
export HISTTIMEFORMAT="%y-%m-%d %H:%M:%S "
export HISTSIZE=10000

source ~/.bash_profile
```
