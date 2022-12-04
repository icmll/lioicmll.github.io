### 查看当前版本

gitlab版本(8.17.4)

```
cat /opt/gitlab/embedded/service/gitlab-rails/VERSION
```

ruby版本:

```
ruby --version

ruby 2.2.0p0 (2014-12-25 revision 49005) [x86_64-linux]
```

centos版本

```
cat /etc/redhat-release
```

### 官网安装教程

[https://about.gitlab.com/installation/#centos-6](https://about.gitlab.com/installation/#centos-6)

[https://about.gitlab.com/installation/#centos-7](https://about.gitlab.com/installation/#centos-7)

备份&还原 [http://code.corp.rs.com/help/raketasks/backup_restore.md](http://code.corp.rs.com/help/raketasks/backup_restore.md)

手动下载地址:[https://packages.gitlab.com/gitlab/gitlab-ee](https://packages.gitlab.com/gitlab/gitlab-ee)

### Centos6:

#### 安装依赖:

```
sudo yum install -y curl policycoreutils-python openssh-server cronie
sudo lokkit -s http -s ssh

yum install postfix
service postfix start
chkconfig postfix on
```

### yum安装

#### 添加gitlab包存储库

```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
```

#### 安装

```
sudo EXTERNAL_URL ="http://gitlab.example.com" yum -y install gitlab-ee
```

或直接

```
yum -y install gitlab-ee=8.17.4
```

EXTERNAL\_URL可以后面去配置 EXTERNAL\_URL是gitlab的url

### 下载安装, 自行去 [https://packages.gitlab.com/gitlab/gitlab-ee地址下载gitlab安装包](https://packages.gitlab.com/gitlab/gitlab-ee%E5%9C%B0%E5%9D%80%E4%B8%8B%E8%BD%BDgitlab%E5%AE%89%E8%A3%85%E5%8C%85)

```
rpm -ivh gitlab-ce-8.17.4-ce.0.el6.x86_64.rpm
```

### 程序相关目录

```
/etc/gitlab #gitlab的配置文件
/var/log/gitlab #gitlab的主程序和嵌入程序的日志目录
/var/opt/gitlab #gitlab的数据存储目录,数据库,代码库,redis,备份等
/opt/gitlab #gitlab的主程序及嵌入的程序,nginx,postgresql,redis等
```

### 备份还原

> 备份的数据:

1. git库 默认路径:/var/opt/gitlab/git-data
2. 数据库 db/database.sql.gz
3. 配置文件 默认路径:/etc/gitlab/gitlab.rb
4. lfs.tar.gz 大文件存储
5. backup_information.yml 此次备份的一些信息

```
# 这条命令不会备份/etc/gitlab/gitlab.rb, 这个配置文件需要自行备份, 恢复也是要自己拷贝到对应目录
gitlab-rake gitlab:backup:create SKIP=uploads,builds,lfs,artifacts

gitlab-rake gitlab:backup:restore BACKUP=1510472027_2017_11_12_9.4.5
```

自定义hook脚本位置:/opt/gitlab/embedded/service/gitlab-shell/hooks/pre-receive.d

自定义备份脚本位置:/opt/gitlab/embedded/service/gitlab-rails/lib/backup/repository.rb

### 迁移

迁移其实就是比安装几个步骤:

1.拷贝原配置, 文件路径: /etc/gitlab/gitlab.rb, 及自定义的hook脚本, 备份脚本等

2.拷贝秘钥,为了不影响本地客户端clone代码更换秘钥的麻烦(know\_hosts里的),需要替换服务器原有的key,把之前老服务器的key, 在/etc/ssh/ 下 ssh\_host\_rsa\_key.pub和ssh\_host\_rsa\_key拷贝到新机器即可,因为clone代码是本地会保存远程的.pub到know\_host中,不替换的话所有本地都要做修改

3.重载配置gitlab-ctl reconfigure(启动状态下操作,这边会有一步清除缓存gitlab-rake cache:clear,没启动的话会报错.因为要链接redis

4.恢复数据 gitlab-rake gitlab:backup:restore BACKUP=1510472027\_2017\_11\_12\_9.4.5