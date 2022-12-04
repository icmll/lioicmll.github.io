### init

###### 运行级别

- 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
  
  > 
- 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆
  
  > 
- 运行级别2：多用户状态(没有NFS)
  
  > 
- 运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式
  
  > 
- 运行级别4：系统未使用，保留
  
  > 
- 运行级别5：X11控制台，登陆后进入图形GUI模式
  
  > 
- 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动

```
init 0
init 2
```

###### 设置默认运行级别

```
vim /etc/inittab
# 加上
# 多用户命令行模式
id:3:initdefault:
```

---

### systemctl

> systemctl命令兼容了service, 也会去/etc/init.d目录下，查看，执行相关程序
> 
> Systemd 默认从目录/etc/systemd/system/读取配置文件。但是，里面存放的大部分文件都是符号链接，指向目录/usr/lib/systemd/system/，真正的配置文件存放在那个目录

/etc/systemd/system/ 优先级更高

- 输出已激活单元
  
  ```
  systemctl
  systemctl list-units
  ```
- 输出运行失败的单元

```
systemctl --failed
```

- 查看所有已安装服务

```
systemctl list-unit-files
```

- 启动, 停止, 重启, 重载, 状态

```
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
systemctl status nginx
```

- 查看和设置开机启动
  
  ```
  systemctl is-enabled nginx
  systemctl enable nginx
  systemctl disable nginx
  ```

- 服务帮助

```
systemctl help nginx
```

- 重新加载systemd的变动

```
systemctl daemon-reload
```

- 重启, 待机, 休眠, 退出系统并断电, 休眠到硬盘并待机

```
systemctl reboot
systemctl poweroff
systemctl suspend
systemctl hibernate
systemctl hybrid-sleep
```

---

### service

> 可在/etc/init.d 中添加脚本

- 使某服务自动启动

```
chkconfig --level 3 httpd on 
```

- 使某服务不自动启动

```
chkconfig --level 3 httpd off
```

- 检查服务状态    

```
service httpd status
```

- 显示所有已启动的服务

```
chkconfig --list
```

- 启动某服务

```
service httpd start
```

- 停止某服务

```
service httpd stop
```

- 重启某服务

```
service httpd restart
```

- 某服务重新加载配置文件

```
service httpd reload
```

### journalctl

- 查看systemd服务日志

```shell
journalctl -xefu kubelet
```
