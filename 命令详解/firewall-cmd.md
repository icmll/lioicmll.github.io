# 防火墙常用命令

## 1. firewall-cmd

```shell
firewall-cmd --state                          ##查看防火墙状态，是否是running
firewall-cmd --reload                          ##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --get-zones                      ##列出支持的zone
firewall-cmd --get-services                    ##列出支持的服务，在列表中的服务是放行的
firewall-cmd --query-service ftp              ##查看ftp服务是否支持，返回yes或者no
firewall-cmd --query-port 80              ##查看80端口状态，返回yes或者no
firewall-cmd --add-service=ftp                ##临时开放ftp服务
firewall-cmd --add-service=ftp --permanent    ##永久开放ftp服务
firewall-cmd --remove-service=ftp --permanent  ##永久移除ftp服务
firewall-cmd --add-port=80/tcp --zone=public --permanent    ##永久添加80端口
firewall-cmd --zone=public --remove-port=80/tcp --permanent # 删除端口开放
iptables -L -n                                ##查看规则，这个命令是和iptables的相同的
```

## 2. iptables

```shell
## 端口转发
iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8006

## 开放端口访问
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT

# iptables 规则删除
# 查出行号
iptables -L INPUT --line-numbers
# 删除， 后面的数字是前面命令输出的行号
iptables -D INPUT 1
```
