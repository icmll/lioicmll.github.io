# centos 安装jellyfin

## 1. 配置rpmfusion源，安装ffmpeg

```shell
yum install epel-release
wget https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
rpm -ihv rpmfusion-free-release-7.noarch.rpm
yum install ffmpeg
```

## 2. 下载jellyfin服务端相关rpm包, 并安装

```shell
wget https://repo.jellyfin.org/releases/server/centos/versions/stable/web/10.6.4/jellyfin-web-10.7.7-1.el7.noarch.rpm
wget https://repo.jellyfin.org/releases/server/centos/versions/stable/server/10.6.4/jellyfin-server-10.7.7-1.el7.x86_64.rpm
wget https://repo.jellyfin.org/releases/server/centos/versions/stable/server/10.6.4/jellyfin-10.7.7-1.el7.x86_64.rpm

yum localinstall jellyfin-web-10.6.4-1.el7.noarch.rpm
yum localinstall jellyfin-server-10.6.4-1.el7.x86_64.rpm
yum localinstall jellyfin-10.6.4-1.el7.x86_64.rpm 
```

## 3. 启动

> http://ip:8096

```shell
systemctl enable jellyfin
systemctl start jellyfin
netstat -anp | grep 8096
```

