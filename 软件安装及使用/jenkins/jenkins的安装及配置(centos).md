# Jenkins 的安装与配置

## 1. 安装
### 1-1. 官网安装[ 参考](https://pkg.jenkins.io/redhat-stable/), 国内安装有点困难

```
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum install jenkins
```

## 1-2. 手动安装[清华源jenkins镜像站](https://mirrors.tuna.tsinghua.edu.cn/jenkins/)

```shell
rpm -ivh jenkins-2.263.4-1.1.noarch.rpm
rpm -ql jenkins
```

# 2. 配置
## 2-1. jdk下载及配置[下载地址](https://www.oracle.com/cn/java/technologies/javase/javase-jdk8-downloads.html)

```shell
# 解压后环境变量设置
JAVA_HOME=/data/jdk-8u171-linux-x64
JRE_HOME=$JAVA_HOME/jre
CLASSPATH=$JAVA_HOME/lib/:$JRE_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
```

### 2-2. jenkins配置

> 启动脚本

- /etc/init.d/jenkins
> 启动配置

- /etc/sysconfig/jenkins
> jenkins配置


```shell
# 配置java
# JENKINS_JAVA_CMD="可执行的java命令路径"
JENKINS_JAVA_CMD="/data/jdk1.8.0_171/bin/java"
```

> 设置开机启动

```shell
/sbin/chkconfig jenkins on
```

> 设置UpdateCenter

```shell
cp hudson.model.UpdateCenter.xml_bak hudson.model.UpdateCenter.xml_bak
vim hudson.model.UpdateCenter.xml
# 把里面的url改成
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```

> 启动

```shell
systemctl restart jenkins
```
