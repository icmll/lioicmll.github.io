## 使用powershell是配置快捷命令

> 进入文件夹 C:\Users\Aly\Documents\WindowsPowerShell,创建.ps1结尾文件
>
> 内容:

```pow
function jump {
    ssh yangl2@192.168.101.99 -p 2222
}
```

> 在powershell中, 使用jump命令, 就会链接此服务器