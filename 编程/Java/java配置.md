# java配置

## 1. jdk环境变量设置

```shell
JAVA_HOME=/data/jdk-8u171-linux-x64
JRE_HOME=$JAVA_HOME/jre
CLASSPATH=$JAVA_HOME/lib/:$JRE_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
```

## 2. jar 包启动参数

```shell
java -Dserver.port=38000 -Xms1024m -Xmx1024m -XX:MetaspaceSize=128M -XX:MaxMetaspaceSize=256M -XX:+PrintCompilation -verbose:gc -Dlog.path.prefix=/app -Dspring.cloud.config.enabled=true -Dspring.cloud.config.uri=http://debug-config-server.szy.net:8099 -Dspring.cloud.config.label=develop -Xloggc:/app/logs/gc.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/app -XX:CICompilerCount=4 -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=38002 -Ddubbo.protocol.port=38001 -Dlog.path.prefix=/app -jar /app/app.jar
```

```shell
-Dserver.port=38000
-Xms1024m
-Xmx1024m
-XX:MetaspaceSize=128M
-XX:MaxMetaspaceSize=256M
-XX:+PrintCompilation
-verbose:gc
-Dlog.path.prefix=/app
-Dspring.cloud.config.enabled=true
-Dspring.cloud.config.uri=http://debug-config-server.szy.net:8099
-Dspring.cloud.config.label=develop
-Xloggc:/app/logs/gc.log
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
# 内存溢出自动dump
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/app
-XX:CICompilerCount=4
# jvm 远程调试配置
# transport：debugee与debuger调试时之间的通讯数据传输方式
# server：是否监听debuger的调试请求。
# suspend：是否等待启动，也即设置是否在debuger调试链接建立后才启动debugee JVM。
# address：debugee的地址，用于debuger建立调试链接
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=38002
-Ddubbo.protocol.port=38001
-Dlog.path.prefix=/app

```

