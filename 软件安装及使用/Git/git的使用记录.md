## 简单说下git 的几个分区
```text
工作区: 当前所看到的所有文件, 其实就是工作区
暂存区: 运行git add 后工作去的修改就会到暂存区
版本区: 运行git commit 后的暂存区的修改就会到版本区
远程: 运行 git push 本地版本去的提交就会被推到远程仓库
```

## git bundle用法, 打包代码库

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
## git clone
> clone 提交数
```bash
# clone时获取最后两条提交
git clone url -depth=1
```

> 指定分支
```bash
git clone url -b branch_name
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

## git remote

```shell
#查看远程仓库
git remote -v
#添加远程仓库
git remote add [name] [url]
#删除远程仓库
git remote rm [name]
#修改远程仓库
git remote set-url --push [name] [newUrl]

# name 为仓库名, 默认为origin
```
## git branch
```shell
#查看本地分支
git branch
#查看远程分支
git branch -r
#创建本地分支
git branch [branchname]
#切换分支
git checkout [branchname]
#创建新分支并立即切换到新分支
git checkout -b [branchname]
#删除分支, -d选项会检查分支中的提交是否有被合并到其他分之了, 如果没禁止删除。强制删除使用-D选项
git branch -d [branchname]
# 一下两个都是强制删除
git branch -D [branchname]
git branch --delete --force [branchname]
#合并分支, 将名称为[name]的分支与当前分支合并
git merge [branchname]
#创建远程分支
git push origin [branchname]
#删除远程分支, push一个空到远程
git push origin :heads/[branchname]
#或 
gitpush origin :[branchname]

#重名名, M为强制重名名, 即使new存在
git branch -m/M old new
```

## git tag
```shell
#查看版本
git tag
# 打印符合检索条件的标签
git tag -l 1.*.*

#创建tag
git tag [tagname]

#删除tag
git tag -d [tagname]

#查看远程tag
git tag -r

#创建远程版本
git push origin [tagname]

#删除远程版本
git push origin :refs/tags/[tagname] 或 git push origin :[tagname]
# GIT > 1.7
git push origin --delete 1.0.0

#合并远程仓库的tag到本地
git pull origin --tags
#上传本地tag到远程仓库
git push origin --tags

#创建带注释的tag
git tag -a [tagname] -m 'v2.2.2.2'
# 指定SHA值创建tag
git tag -a <版本号> <SHA值> -m "<备注信息>"
```
## git checkout

```shell
#创建分之并切换到该分支
git checkout -b [branchname]

# 还原工作去的修改
. 是还原所有工作去
git checkout .
或
git checkout path

#切换分之, tag, commit
git checkout [branchname, tagname, commitid]

#创建一个新分支但是不要提交记录, 然后创建该分支的第一条提交
git checkout --orphan latest_branch
git add -A
git commit -m "init"

```

## git diff比较差异

```shell
 #比较的是工作区和暂存区的差别
  git diff
  #比较的是暂存区和版本库的差别
  git diff --cached
  #可以查看工作区和版本库的差别
  git diff HEAD
  # 比较两个分支, 提交, tag 间的差异
  git diff name1 name2
  或
  # 有...  的是name1比name2少的提交
  git diff name1...name2
```

## git add

```shell
# 这个命令会监控工作区的状态树，会把工作区的所有变化提交到暂存区，包括修改内容的文件(modified)和新文件(new)，但是不包括被删除的文件
 git add .
 
# (git add --update缩写)这个命令不会提交新文件(untracked file)-----仅仅作为修改提交被修改(modified)和被删除(delete)文件，不包括新文件(new)
git add -u

# (git add -all缩写)是上面两个命令的集合，会提交所有的文件包括修改新建和删除的
git add -A
```

## git show 查看提交内容(git reflog 可以查看所有分支的所有操作记,包括已经被删除的 commit 记录和 reset 的操作)

```shell
# 查看某条提交的提交内容
git show commitid
```

## git log

```shell

git log --pretty=oneline --decorate=full

#--pretty=format:"%H"  format后的选项
#选项	说明
# %H     提交对象(commit)的完整哈希字串
# %h    提交对象的简短哈希字串
# %T    树对象(tree)的完整哈希字串
# %t     树对象的简短哈希字串
# %P      父对象(parent)的完整哈希字串
# %p     父对象的简短哈希字串
# %an    作者(author)的名字
# %ae    作者的电子邮件地址
# %ad   作者修订日期(可以用 -date= 选项定制格式)
# %ar    作者修订日期，按多久以前的方式显示
# %cn   提交者(committer)的名字
# %ce   提交者的电子邮件地址
# %cd   提交日期
# %cr    提交日期，按多久以前的方式显示
# %s    提交说明
#还有其他选项
# --stat	显示每次更新的文件修改统计信息
# --since, --after 仅显示指定时间之后的提交, --since=2.weeks
# --until, --before 仅显示指定时间之前的提交
# --author 仅显示指定作者相关的提交
# --committer 仅显示指定提交者相关的提交
# --grep 搜索提交说明中的关键字, 如果要得到同时满足这两个选项搜索条件的提交，就必须用--all-match 选项
# git log -- <path> 某些文件或者目录的历史提交, 选项后指定路径
# git log --raw 列出修改的文件
# git log --patch 修改详情, 有点像git show , 但是这个是列出所有的

## 实际使用中可以用作生成release note, 前提是你的commit说明写的规范
git log -15 --pretty=format:"- %s    (%an--%cd)"
```

## git reset 是回滚到某次提交, 但是回滚操作建议用git revert
```shell
#提交之后的修改会被退回到暂存区
git reset --soft
#提交之后的修改不做任何保留，git status干净的工作区
git reset --hard
```

## git config

```shell
# 添加 --global 设置全局配置, 不添加则设置当前代码库配置

# 查看git config 配置列表
git config --list 
或
git config -l

# 添加配置 和 修改配置
git config 命名 '值';

# 删除配置
git config --unset 命名

# 这个配置是git会导入这个配置文件, 全局忽略跟踪的文件
# .gitconfig中配置
[core]
	quotepath = false
	excludesfile = C:/Users/Aly/.gitignore_global
# 或命令
git config --global core.excludesfile C:/Users/Aly/.gitignore_global


## 使用http是记住账号密码
# 永久记住密码, 存在磁盘, 默认~/.git-credentials
git config --global credential.helper store
# 可以自定义文件
git config --global credential.helper 'store --file ~/.my-credentials'
# 设置时间, 秒, 存在内存
git config --global credential.helper 'cache --timeout=300'
# 删除配置
git config --global --unset credential.helper store
git config --global --unset credential.helper cache
```

## git revert 放弃某次提交, 之前的提交仍会保留在git log中，而此次撤销会做为一次新的提交,.其实就是创建了一跳回滚之前提交的提交
## git cherry-pick 合并指定的commit 到分支
## git rebase