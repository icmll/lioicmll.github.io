### 介绍

> Ansible是一款简单的运维自动化工具，只需要使用ssh协议连接就可以来进行系统管理，自动化执行命令，部署等任务。

### Ansible的特点

> 1、ansible不需要单独安装客户端，也不需要启动任何服务
> 
> 2、ansible是python中的一套完整的自动化执行任务模块
> 
> 3、ansible playbook 采用yaml配置，对于自动化任务执行过一目了然

### Ansible组成结构

> Ansible: 是Ansible的命令工具，核心执行工具；一次性或临时执行的操作都是通过该命令执行。
> 
> Ansible Playbook: 任务剧本（又称任务集），编排定义Ansible任务集的配置文件，由Ansible顺序依次执行，yaml格式。
> 
> Inventory: Ansible管理主机的清单，默认是/etc/ansible/hosts文件。
> 
> Modules: Ansible执行命令的功能模块，Ansible2.3版本为止，共有1039个模块。还可以自定义模块。
> 
> Plugins: 插件，模块功能的补充，常有连接类型插件，循环插件，变量插件，过滤插件，插件功能用的较少。
> 
> API: 提供给第三方程序调用的应用程序编程接口。

### Ansible安装

> 配置epel源

```shell
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all
yum makecache
```

> 安装

```shell
yum -y install ansible
ansible --version
```

### Ansible Inventory文件

> Inventory文件通常用于定义要管理的主机的认证信息，例如ssh登录用户名、密码以及key相关信息。可以同时操作一个组的多台主机，组与主机组之间的关系都是通过inventory文件配置。配置文件路径为：/etc/ansible/hosts

#### 基于密码连接

```ini
# 方法一 主机+端口+密码
[webserver]
192.168.1.31 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass="123456"
192.168.1.32 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass="123456"
192.168.1.33 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass="123456"
192.168.1.36 ansible_ssh_port=22 ansible_ssh_user=root ansible_ssh_pass="123456"


# 方法二 主机+端口+密码
[webserver]
192.168.1.3[1:3] ansible_ssh_user=root ansible_ssh_pass="123456"


# 方法二 主机+端口+密码
[webserver]
192.168.1.3[1:3]
[webserver:vars]
ansible_ssh_pass="123456"
```

#### 基于秘钥连接

```ini
# 方法一 主机+端口+密钥
[webserver]
192.168.1.31:22
192.168.1.32
192.168.1.33
192.168.1.36

# 方法一 别名主机+端口+密钥
[webserver]
node1 ansible_ssh_host=192.168.1.31 ansible_ssh_port=22
node2 ansible_ssh_host=192.168.1.32 ansible_ssh_port=22
node3 ansible_ssh_host=192.168.1.33 ansible_ssh_port=22
node6 ansible_ssh_host=192.168.1.36 ansible_ssh_port=22
```

#### 主机组的使用

```ini
# 主机组变量名+主机+密码
[apache]
192.168.1.36
192.168.1.33
[apache:vars]
ansible_ssh_pass='123456'

# 主机组变量名+主机+密钥
[nginx]
192.168.1.3[1:2]

# 定义多个组，把一个组当另外一个组的组员
[webserver:children]  #webserver组包括两个子组：apache nginx
apache
nginx

# 匹配apache组中有，但是nginx组中没有的所有主机
ansible 'apache:!nginx' -m ping -o

# 匹配apache组和nginx组中都有的机器（并集）
ansible 'apache:&nginx' -m ping -o

# 匹配apache组nginx组两个组所有的机器（并集）；等于ansible apache,nginx -m ping
ansible 'apache:nginx' -m ping -o
```

#### 临时指定inventory

```
ansible 主机组 -m ping -i inventory文件 -o
```

#### Inventory内置参数

| 参数                           | 用途                               | 例子                                            |
| ---------------------------- | -------------------------------- | --------------------------------------------- |
| ansible_ssh_host             | 定义hosts ssh 地址                   | ansible_ssh_host=192.169.1.100                |
| ansible_ssh_port             | 定义 hosts ssh 端口                  | ansible_ssh_port=6022                         |
| ansible_ssh_user             | 定义hosts ssh 认证用户                 | ansible_ssh_user=user                         |
| ansible_ssh_pass             | 定义hosts ssh密码                    | ansible_ssh_pass=pass                         |
| ansible_sudo                 | hosts sudo 用户                    | ansible_sudo=nginx                            |
| ansible_sudo_pass            | hosts sudo 密码                    | ansible_sudo_pass=pass                        |
| ansible_sudo_exe             | hostssudo 路径                     | ansible_sudo_exe=/usr/bin/sudo                |
| ansible_connection           | hosts 连接方式(local, docker, ssh等 ) | ansible_connection=local                      |
| ansible_ssh_private_key_file | hosts 私钥                         | ansible_ssh_private_key_file=/root/key        |
| ansible_ssh_shell_type       | hosts shell 类型                   | ansible_ssh_shell_type=bash                   |
| ansible_python_interpreter   | hosts python路径                   | ansible_python_interpreter=/usr/bin/python2.7 |
| ansible_*_interpreter        | hosts 其他语言解析器                    | ansible_*_interpreter=/usr/bin/ruby           |
|ansible_ssh_common_args       | 其他参数                           | 代理连接: ansible_ssh_common_args='-o ProxyCommand="ssh -W %h:%p -p 22 -q root@47.95.140.208"'
|

#### ansible命令格式

```shell
[root@ansible ~]# ansible -h
Usage: ansible <host-pattern> [options]
-a MODULE_ARGS   #模块参数
-C, --check  #检查语法
-f FORKS #并发
--list-hosts #列出主机列表
-m MODULE_NAME #模块名字
-o 使用精简的输出

# 不加m默认为command
ansible webserver -m shell -a 'uptime' -o
```

#### 最常用模块

##### command

> command模块可以帮助我们在远程主机上执行命令, 指令模块、也是默认模块、不支持管道、通配符、脚本等
> \不会经过远程主机的shell处理，在使用command模块时，如果需要执行的命令中含有重定向、管道符等操作时，这些符号也会失效，比如"<", “>”, “I”, ";“和”&"这些符号，如果你需要这些功能，可以参考后面介绍的shell模块，还有一点需要注意，如果远程节点是windows操作系统，则需要使用win_command模块
> 
> chdir参数:此参数的作用就是指定一个目录，在执行对应的命令之前，会先进入到chdir参数指定的目录中。
> creates参数:看到creates,你可能会从字面上理解这个参数，但是使用这个参数并不会帮助我们创建文件，它的作用是当指定的文件存在时，就不执行对应命令，比如，如果/testdir/test文件存在，就不执行我们指定的命令。
> removes参数:与creates参数的作用正好相反，它的作用是当指定的文件不存在时，就不执行对应命令，比如，如果/testdir/tests文件不存在，就不执行我们指定的命令,此参数并不会帮助我们删除文件

```shell
ansible webserver -m command -a "chdir=/data ls -l"
```

##### shell

> shell模块可以帮助我们在远程主机上执行命令，与command模块不同的是，shell模块在远程主机中执行命令时，会经过远程主机上的/bin/sh程序处理。
> 
> chdir参数:此参数的作用就是指定一个目录，在执行对应的命令之前，会先进入到chdir参数指定的目录中。
> creates参数:使用此参数指定一个文件，当指定的文件存在时，就不执行对应命令，可参考command模块中的解释。
> removes参数:使用此参数指定一个文件，当指定的文件不存在时，就不执行对应命令，可参考command模块中的解释。
> executable参数:默认情况下，shell模块会调用远程主机中的/bin/bash去执行对应的命令，通常情况下，远程主机中的黑状认shell都是bash,如果你想要使用其他类型的shell执行命令，则可以使用此参数指定某种类型的shell去执行对应的命令，指定shell文件时，需要使用绝对路径。

```shell
ansible webserver -m copy -a "src=./anaconda-ks.cfg  dest=/data"
```

##### script

> script模块可以帮助我们在远程主机上执行ansible主机上的脚本，也就是说，脚本一直存在于ansible主机本地，不需要手动拷贝到远程主机后再执行。
> 
> chdir参数:此参数的作用就是指定一个远程主机中的目录，在执行对应的脚本之前，会先进入到chdir参数指定的目录中。
> creates参数:使用此参数指定-个远程主机中的文件,当指定的文件存在时，就不执行对应脚本，可参考command模块中的解释。
> removes参数:使用此参数指定-个远程主机中的文件,当指定的文件不存在时，就不执行对应脚本，可参考command模块中的解释

```shell
ansible webserver -m shell -a "ps -ef |grep /[s]sh"
```

##### copy

> 将管理机文件复制到远程主机中

```shell
# ansible webserver -m copy -a 'src=/root/lw.txt dest=/root'
```

##### fetch

> 从远程主机拉取文件到管理主机（和copy的功能相反），但是只能拉取单个文件（多个文件的话可以打包拉取）

```
ansible webserver -m fetch-a 'src=/root/a.out dest=/root/test'
```

##### yum

> 软件包管理模块：（状态默认为present、也就是install）

```shell
ansible webserver -m yum -a 'name=python'
```

##### user模块

> 管理用户（创建用户的时候，这边有个提醒：是密码方面，可以使用加密）

```shell
ansible webserver -m user -a 'name=test password=redhat'
```

##### file模块

> 创建一个文件（也可以使用shell模块），state：创建为touch，删除为：absent

```shell
ansible webserver -m file -a'name=host_test.txt state=touch'
ansible webserver -m shell -a 'touch host_test2.txt'
#这边提示是ansible有自己的模块来创建文件，可以使用file模块（不影响）
```

##### file模块

> 创建目录，state值为：directory

```shell
ansible webserver -m file -a 'name=ansible1 state=directory'
```

##### service模块

> 管理服务
> 
> name参数：此参数用于指定需要操作的服务名称，比如 nginx。 
> 
> state参数：此参数用于指定服务的状态，比如，我们想要启动远程主机中的 nginx，则可以将 state 的值设置为 started；如果想要停止远程主机中的服务，则可以将 state 的值设置为 stopped。此参数的可用值有 started、stopped、restarted、reloaded。 
> 
> enabled参数：此参数用于指定是否将服务设置为开机 启动项，设置为 yes 表示将对应服务设置为开机启动，设置为 no 表示不会开机启动。
> arguments 给命令提供一些选项
> 
> runlevel  运行等级
> 
> sleep 如果运行看restarted 则stop and start 之间沉睡几秒中

```shell
ansible webserver -m service -a 'name=vsftpd state=started'
```

##### cron


| 参数                           | 说明                               |
| ---------------------------- | -------------------------------- |
| name | 定时任务的描述, 会议注释在配置问题件里显示 |
| job | 定时任务要执行的命令 |
| minute | 分 |
| hour | 时 |
| day | 日 |
| month | 月 |
| weekday | 周: 0-6 |
| disabled | yes: 禁用, no: 启用, 默认no, 可以不写 |
| state | absent: 删除计划 , 只要name就好了,根据name删除的, 默认可以不写|


```shell
# 设置
ansible NginxWebs -m cron -a 'name="synctime" job="ntpdate 192.168.20.1 &> /dev/null" minute=*/10'

# 禁用
ansible NginxWebs -m cron -a 'name="ls" job="ls &> /dev/null" minute=0 hour=2,5 disabled=yes'

# 删除
ansible NginxWebs -m cron -a 'name="synctime" state=absent'
```

##### synchronize