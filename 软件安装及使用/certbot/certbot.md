# 1. 安装

## 1-1. yum安装

```shell
yum install epel-release -y
yum install certbot -y
```

# 2. 申请证书

```shell
#生成的证书默认在 /etc/letsencrypt/live/路径下
certbot --server https://acme-v02.api.letsencrypt.org/directory  -d "*.k8s.8158.online" --manual --preferred-challenges dns-01 certonly

```
