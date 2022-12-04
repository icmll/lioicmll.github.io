# git 
## git bundle用法

> 打包git库

```shell
git bundle created aa.bundle HEAD <branch，tag，commit>
```

> 查看打包的git库里有哪些版本

```shell
git bundle list-heads aa.bundle
```

> 检查bundle文件的可用性

```shell
git bundle verify aa.bundle
```

> 可以用git clone 直接clone出打包的代码和提交记录

```shell
git clone aa.bundle
```

## git push 用法

> 推送说有的分支到远端

```shell
git push -u origin --all
```

> 推送所有tag到远端


```shell
git push -u origin --tags
```

> 提交代码到远程

```shell
# 提交本地master到远程master
git push origin master:master

# 提价本地test到test
git push origin test:master
```

## git命令集

![git-command](../../Image/2010072023345292.png)

## git多个用户ID配置

> 在home目录下的.gitconfig添加配置

- includeIf 支持三个参数指令支持三个关键字：gitdir、gitdir/I(忽略大小写匹配) 和 onbranch
- gitdir:\~/open/表示gitdir:~/open/下的所有仓库, gitdir:\~/open表示这个目录仓库, 不做递归
```ini
[includeIf "gitdir:~/open/"]
        path = ~/gitconfig/.gitconfig_open
[includeIf "gitdir:~/work/"]
        path = ~/gitconfig/.gitconfig_work

```

