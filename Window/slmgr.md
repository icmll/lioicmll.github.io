## 1. slmgr命令使用
> slmgr是Software LicenseManger的简称，是Windows软件授权管理工具。slmgr是管理系统激活和密钥、证书的主要组件。slmgr的所有功能都是通过slmgr.vbs提供的，采用VBScript命令行方式执行。
> 图形界面中许多与该命令行相关的激活行为，比如系统属性等，都是通过调用slmgr.vbs脚本来执行授权操作。即使你在COMMAND下使用slmgr命令行，其运行结果或出错信息也是以弹出窗口方式显示的。

### 1-1. 进入并运行slmgr.vbs命令的几种方式：
    1、COMMAND命令提示窗口下，这是一种执行方式，但需要提升管理员权限；
    2、运行命令（Win+R，开始/所有程序/附件/命令提示符，任何一种）；
    3、开始菜单中的开始搜索框，使用这种方法需要输入slmgr的完整名称——slmgr.vbs。

### 1-2. SLMGR语法格式基本语法：
```bat
slmgr.vbs [MachineName [User Password]]
```

> 其中：

```text
MachineName ： 远程计算机名 （缺省为本机）
User ： 具有相应权限的计算机用户
Password： 用户密码
Option： 为SLMGR的参数，参下。
```
### 1-3. 常用参数选项：
```bat
ipk <产品密钥> 安装产品密钥（替换现有密钥）
upk 卸载产品密钥
ato 激活Windows
dli [激活 ID | All] 显示许可证信息（默认：当前许可证）
dlv [激活 ID | All] 显示详细的许可证信息（默认：当前许可证）
xpr 当前许可证状态的截止日期
```
### 1-4. 高级参数选项：
    cpky 从注册表中清除产品密钥（阻止泄露引起的攻击）
    ilc <许可证文件> 安装许可证
    rilc 重新安装系统许可证文件
    rearm 重置计算机的授权状态 （去除水印）
    dti 显示安装 ID以进行脱机激活
    atp <确认 ID> 使用电话方式提供的确认 ID 激活产品
### 1-5. KMS相关选项：
    skms 设置KMS服务器名
    skms 设置KMS服务器端口号
    skms 设置KMS服务器名和端口号
    ckms 清除KMS服务器名和端口号并设为初始状态
### 1-6. slmgr.vbs的使用示例
    1、安装产品密匙
    命令：slmgr.vbs -ipk xxxxx-xxxxx-xxxxx-xxxxx-xxxxx
    2、安装OEM证书方法
    命令：slmgr.vbs -ilc x:\xxx.XRM-MS
    3、激活Windows
    命令：slmgr.vbs -ato
    4、显示许可证激活状态的截止日期
    命令：slmgr.vbs -xpr
    5、查看许可证的概要信息
    命令：slmgr.vbs -dli
    6、查看许可证详细信息
    命令：slmgr.vbs -dlv
    7、卸载当前产品密匙
    命令：slmgr.vbs -upk