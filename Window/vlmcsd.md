## 1. 激活命令

```bat
# Windows:
slmgr.vbs -ipk W269N-WFGWX-YVC9B-4J6C9-T83GX
slmgr.vbs -skms 192.168.178.128:1688
slmgr.vbs -ato


# Office 要下volumn版本(成功过, 建议使用Office Tool Plus很简单):
cd C:\Program Files\Microsoft Office\Office15
cscript ospp.vbs /inpkey:XQNVK-8JYDB-WJ9W3-YJ8YR-WFG99
cscript ospp.vbs /sethst:192.168.178.128:1688
cscript ospp.vbs /act
```

## 2. vlmcsd搭建

### 2-1. 准备(已存移动硬盘)

1. 这里是列表文本Linux服务器（vps也可以）
2. vlmcsd 或者 vlmcsdmulti [百度](https://links.jianshu.com/go?to=https%3A%2F%2Fpan.baidu.com%2Fs%2F1d8mnX0qHvR4Q0bEXB_ciMQ)/[MEGA](https://links.jianshu.com/go?to=https%3A%2F%2Fmega.nz%2F%23%21Z55AyJBC%21UMhXr2bcUs3LUOA1R2m3reb-7RiTGoG0oncLx7rKDQw)/[githab](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FWind4%2Fvlmcsd%2Freleases)前两个zip的解压密码为2017)
3. Windows/Office

### 2-1. 解压

> 解压下载的文件, 进入binaries, 选择服务器系统和cpu架构, vlmcsd或者vlmcsdmulti文件均可

### 2-2.复制文件到服务器(centos)

![图片](Image/4DBAE3B0-EE67-4b43-8957-E00B9E3CB79C.png)

### 2-3. 执行命令

[Windows KMS客户端安装密钥](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612867(v=ws.11)?redirectedfrom=MSDN)

[Office KMS客户端安装密钥](https://docs.microsoft.com/en-us/DeployOffice/vlactivation/gvlks)

```shell
## vlmcsd服务器运行
#本地
scp ./vlmcsd-x64-musl-static xxx@host.ip:/opt/kms/
#远端
chmod u+x /opt/kms/vlmcsd-x64-musl-static

./vlmcsd-x64-musl-static
#如果没有报错，就运行成功了，如果报错，检查端口占用
#centos开启端口对外访问
# 关闭防火墙
#本地端口连通性测试
telnet 192.168.178.200 1688
#如果能够联通，就可以进行下一步


## 要激活的机器上运行
## 先安装KMS客户端安装密钥
slmgr /ipk W269N-WFGWX-YVC9B-4J6C9-T83GX
# 添加kms服务器地址
slmgr /skms 192.168.178.200:1688
# 激活, 后续的延期只要执行这条就行了, 注意vlmcsd服务器的防火墙也要关闭
slmgr /ato
# 查看
slmgr /xpr

# 检查是否激活
slmgr.vbs -dlv

# 卸载当前kms
slmgr.vbs /upk
```
