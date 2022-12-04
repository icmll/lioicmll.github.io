# Ubuntu初始化

## 1. 网络初始化

> 编辑文件

```bash
sudo vim /etc/netplan/01-network-manager-all.yaml
```

```yaml
network:
  version: 2
  renderer: NetworkManager
  ethernets:
     ens33: #配置的网卡名称,使用ifconfig -a查看得到
       dhcp4: no #dhcp4关闭
       dhcp6: no #dhcp4关闭
       addresses: [192.168.178.12/24] #设置本机IP及掩码
       gateway4: 192.168.178.2 #设置网关
       nameservers:
         addresses: [192.168.178.2] #设置DNS
```

> 执行命令

```bash
sudo netplan apply
```

## 2. 替换源

```bash
cp /etc/apt/sources.list /etc/apt/sources.list_bak
# 没有装vim 可以用cat, 一般cat都有的
sudo vim /etc/apt/sources.list
```

> 全部删掉, 复制以下内容, 这个是清华源
> 
> [清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/)

```text
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
```

```bash
sudo apt update
```

## 3.修改网卡名(可不改)

```bash
sudo vim /etc/default/grub

sudo cat > /etc/default/grub <<- EOF
GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="net.ifnames=0 biosdevname=0"
GRUB_CMDLINE_LINUX=""
EOF

sudo update-grub
sudo reboot
```

## 4. 设置时区,24小时制

```shel
# 修改时间时区
# 本机时间
ln -sfn /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 修改本机时区, 有些程序会通过时区自己计算时间, 并不会直接用本机时间
echo 'Asia/Shanghai' > /etc/timezone


# 设置24小时制
vim /etc/default/locale
# 添加下面哪一行
LC_TIME=en_GB.UTF-8
```

## 5. 安装基础软件

```bash
sudo apt install iproute2  ntpdate  tcpdump telnet traceroute nfs-kernel-server nfs-common  lrzsz tree  openssl libssl-dev libpcre3 libpcre3-dev zlib1g-dev ntpdate tcpdump telnet traceroute  gcc openssh-server iotop unzip zip
```
