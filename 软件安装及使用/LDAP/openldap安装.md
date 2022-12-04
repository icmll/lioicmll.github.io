# OPENLDAP安装

## 1. yum 安装

### 1-1. 安装

```shell
# yum 安装相关包
yum install -y openldap openldap-clients openldap-servers

# 复制一个默认配置到指定目录下,并授权，这一步一定要做，然后再启动服务，不然生产密码时会报错
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
# 授权给ldap用户,此用户yum安装时便会自动创建, ldap后面有个., 实际就是ldap:ldap的简写
chown -R ldap. /var/lib/ldap/DB_CONFIG
 
 ## 修改监听端口
 vim /etc/sysconfig/slapd
 SLAPD_URLS="ldapi:/// ldap://0.0.0.0:60389/"
 
# 启动服务，先启动服务，配置后面再进行修改
systemctl start slapd
systemctl enable slapd
 
# 查看状态，正常启动则ok
systemctl status slapd

```

### 1-2. 配置

> 从openldap2.4.23版本开始，所有配置都保存在/etc/openldap/slapd.d目录下的cn=config文件夹内，不再使用slapd.conf作为配置文件。配置文件的后缀为 ldif，且每个配置文件都是通过命令自动生成的，任意打开一个配置文件，在开头都会有一行注释，说明此为自动生成的文件，请勿编辑
>
> 安装openldap后，会有三个命令用于修改配置文件，分别为ldapadd, ldapmodify, ldapdelete，顾名思义就是添加，修改和删除。而需要修改或增加配置时，则需要先写一个ldif后缀的配置文件，然后通过命令将写的配置更新到slapd.d目录下的配置文件中去

#### 1-2-3. 创建管理员

```shell
# 生成管理员密码,记录下这个密码，后面需要用到
slappasswd -s 88888
{SSHA}fyZX0O4OGJ/lExEjIcmcPCSQpha5B1qY


# 新增修改密码文件,ldif为后缀，文件名随意，不要在/etc/openldap/slapd.d/目录下创建类似文件
# 生成的文件为需要通过命令去动态修改ldap现有配置，如下，我在家目录下，创建文件

cat > adminpwd.ldif <<-EOF
dn: olcDatabase={0}config,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}fyZX0O4OGJ/lExEjIcmcPCSQpha5B1qY
EOF

# 这里解释一下这个文件的内容：
# 第一行执行配置文件，这里就表示指定为 cn=config/olcDatabase={0}config 文件。你到/etc/openldap/slapd.d/目录下就能找到此文件
# 第二行 changetype 指定类型为修改
# 第三行 add 表示添加 olcRootPW 配置项
# 第四行指定 olcRootPW 配置项的值
# 在执行下面的命令前，你可以先查看原本的olcDatabase={0}config文件，里面是没有olcRootPW这个项的，执行命令后，你再看就会新增了olcRootPW项，而且内容是我们文件中指定的值加密后的字符串
# 执行命令，修改ldap配置，通过-f执行文件
ldapadd -Y EXTERNAL -H ldapi:/// -f adminpwd.ldif
```

#### 1-2-4. 导入一些Schema

> 这些 Schema 文件位于 /etc/openldap/schema/ 目录中，schema控制着条目拥有哪些对象类和属性，可以自行选择需要的进行导入
>
> 依次执行下面的命令，导入基础的一些配置,我这里将所有的都导入一下，其中core.ldif是默认已经加载了的，不用导入

```shell
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/collective.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/corba.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/duaconf.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/dyngroup.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/java.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/misc.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/openldap.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/pmi.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/ppolicy.ldif

```

#### 1-2-5. 修改域名

```shell

cat > changedomain.ldif<<-EOF
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth" read by dn.base="cn=admin,dc=icmll,dc=com" read by * none

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=icmll,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=admin,dc=icmll,dc=com

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootPW
olcRootPW: {SSHA}fyZX0O4OGJ/lExEjIcmcPCSQpha5B1qY

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {0}to attrs=userPassword,shadowLastChange by dn="cn=admin,dc=icmll,dc=com" write by anonymous auth by self write by * none
olcAccess: {1}to dn.base="" by * read
olcAccess: {2}to * by dn="cn=admin,dc=icmll,dc=com" write by * read
EOF


ldapmodify -Y EXTERNAL -H ldapi:/// -f changedomain.ldif
```

#### 1-2-6. 启用memberof功能

```shell
# 新增add-memberof.ldif, #开启memberof支持并新增用户支持memberof配置
cat > add-memberof.ldif<<EOF
dn: cn=module{0},cn=config
cn: modulle{0}
objectClass: olcModuleList
objectclass: top
olcModuleload: memberof.la
olcModulePath: /usr/lib64/openldap

dn: olcOverlay={0}memberof,olcDatabase={2}hdb,cn=config
objectClass: olcConfig
objectClass: olcMemberOf
objectClass: olcOverlayConfig
objectClass: top
olcOverlay: memberof
olcMemberOfDangling: ignore
olcMemberOfRefInt: TRUE
olcMemberOfGroupOC: groupOfUniqueNames
olcMemberOfMemberAD: uniqueMember
olcMemberOfMemberOfAD: memberOf
EOF

# 新增refint1.ldif文件
cat > refint1.ldif<<EOF
dn: cn=module{0},cn=config
add: olcmoduleload
olcmoduleload: refint
EOF

# 新增refint2.ldif文件
cat > refint2.ldif<<EOF
dn: olcOverlay=refint,olcDatabase={2}hdb,cn=config
objectClass: olcConfig
objectClass: olcOverlayConfig
objectClass: olcRefintConfig
objectClass: top
olcOverlay: refint
olcRefintAttribute: memberof uniqueMember  manager owner
EOF


# 依次执行下面命令，加载配置，顺序不能错
ldapadd -Q -Y EXTERNAL -H ldapi:/// -f add-memberof.ldif
ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f refint1.ldif
ldapadd -Q -Y EXTERNAL -H ldapi:/// -f refint2.ldif

```

#### 1-2-7. 创建组织, 管理域

> 管理域 cn=admin,dc=icmll,dc=com 和两个组织单元 ou=People,dc=icmll,dc=com 及 ou=Group,dc=icmll,dc=com

```shell
# 新增配置文件
cat > base.ldif<<EOF
dn: dc=icmll,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: Icmll Company
dc: icmll

dn: cn=admin,dc=icmll,dc=com
objectClass: organizationalRole
cn: admin

dn: ou=People,dc=icmll,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=icmll,dc=com
objectClass: organizationalRole
cn: Group
EOF

# 执行命令，添加配置, 这里要注意修改域名为自己配置的域名，然后需要输入上面我们生成的密码
ldapadd -x -D cn=admin,dc=icmll,dc=com -W -f base.ldif
88888
```

### 1-2. 卸载

```shell
#停止openldap

systemctl stop slapd
systemctl disable slapd

#卸载
yum -y remove openldap-servers openldap-clients

#删除残留文件

rm -rf /var/lib/ldap

//删除ldap用户
userdel ldap

#删除openldap目录
rm -rf /etc/openldap

```

### 1-3. 卸载后重装遇到的问题

> 启动报错: main: TLS init def ctx failed: -1

```shell
# 检查配置
slaptest -u

ll /etc/openldap
# 发现没有certs目录
mkdir -p /etc/openldap/certs
bash /usr/libexec/openldap/create-certdb.sh
bash /usr/libexec/openldap/generate-server-cert.sh

systemctl start slapd
systemctl status slapd
```

