## 1. redis命令有两种运行方式

### 1-1. 用redis-cli到交互页面直接运行命令

```shell
# 参数按需要使用
redis-cli -h {host} -p {port} -a {密码} -n {db}
```

### 1-2. redis-cli 后直接跟命令在交互总端运行

```shell
# 参数按需要使用
redis-cli -h {host} -p {port}  -a {密码} -n {db} {command}
```

## 2. 数据结构

> String: 字符串
> 
> Hash: 散列
> 
> List: 列表
> 
> Set: 集合
> 
> Sorted Set: 有序集合

## 3. redis命令使用

### 3-1. 对key的操作

```redis
#该命令用于在 key 存在时删除 key
del key

#序列化给定 key ，并返回被序列化的值
dump key

#检查给定 key 是否存在
exists key

#为给定 key 设置过期时间，以秒计
expire key seconds

#expireat 的作用和 expire 类似，都用于为 key 设置过期时间。 不同在于 expireat 命令接受的时间参数是 unix 时间戳(unix timestamp)
expireat key timestamp

#设置 key 的过期时间以毫秒计
pexpire key milliseconds

#设置 key 过期时间的时间戳(unix timestamp) 以毫秒计
pexpireat key milliseconds-timestamp 

#查找所有符合给定模式( pattern)的 key
keys pattern

#将当前数据库的 key 移动到给定的数据库 db 当中
move key db

#移除 key 的过期时间，key 将持久保持
persist key

#以毫秒为单位返回 key 的剩余的过期时间
pttl key

#以秒为单位，返回给定 key 的剩余生存时间(ttl, time to live)
ttl key

#从当前数据库中随机返回一个 key
randomkey

#修改 key 的名称
rename key newkey

#仅当 newkey 不存在时，将 key 改名为 newkey
renamenx key newkey

#返回 key 所储存的值的类型
type key
```

### 3-2. string数据结构操作

```redis
1.set key value 
设置指定 key 的值

2.get key 
获取指定 key 的值

3.getrange key start end 
返回 key 中字符串值的子字符

4.getset key value
将给定 key 的值设为 value ，并返回 key 的旧值(old value)

5.getbit key offset
对 key 所储存的字符串值，获取指定偏移量上的位(bit)

6.mget key1 [key2..]
获取所有(一个或多个)给定 key 的值

7.setbit key offset value
对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)

8.setex key seconds value
将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)

9.setnx key value
只有在 key 不存在时设置 key 的值

10.setrange key offset value
用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始

11.strlen key
返回 key 所储存的字符串值的长度

12.mset key value [key value ...]
同时设置一个或多个 key-value 对

13.msetnx key value [key value ...] 
同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在

14.psetex key milliseconds value
这个命令和 setex 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 setex 命令那样，以秒为单位

15.incr key
将 key 中储存的数字值增一

16.incrby key increment
将 key 所储存的值加上给定的增量值（increment）

17.incrbyfloat key increment
将 key 所储存的值加上给定的浮点增量值（increment）

18.decr key
将 key 中储存的数字值减一

19.decrby key decrement
key 所储存的值减去给定的减量值（decrement）

20.append key value
```

### 3-3. hash数据结构操作

```reiis
1.hdel key field1 [field2] 
删除一个或多个哈希表字段

2.hexists key field 
查看哈希表 key 中，指定的字段是否存在

3.hget key field 
获取存储在哈希表中指定字段的值

4.hgetall key 
获取在哈希表中指定 key 的所有字段和值

5.hincrby key field increment
为哈希表 key 中的指定字段的整数值加上增量 increment 

6.hincrbyfloat key field increment 
为哈希表 key 中的指定字段的浮点数值加上增量 increment 

7.hkeys key 
获取所有哈希表中的字段

8.hlen key 
获取哈希表中字段的数量

9.hmget key field1 [field2] 
获取所有给定字段的值

10.hmset key field1 value1 [field2 value2 ] 
同时将多个 field-value (域-值)对设置到哈希表 key 中

11.hset key field value 
将哈希表 key 中的字段 field 的值设为 value 

12.hsetnx key field value 
只有在字段 field 不存在时，设置哈希表字段的值

13.hvals key 
获取哈希表中所有值

14.hscan key cursor [match pattern] [count count] 
```

### 3-4. list数据结构操作

```redis
1.blpop key1 [key2 ] timeout 
移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止

2.brpop key1 [key2 ] timeout 
移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
3.brpoplpush source destination timeout 
从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止

4.lindex key index 
通过索引获取列表中的元素

5.linsert key before|after pivot value 
在列表的元素前或者后插入元素

6.llen key 
获取列表长度

7.lpop key 
移出并获取列表的第一个元素

8.lpush key value1 [value2] 
将一个或多个值插入到列表头部

9.lpushx key value 
将一个值插入到已存在的列表头部

10.lrange key start stop 
获取列表指定范围内的元素

11.lrem key count value 
移除列表元素

12.lset key index value 
通过索引设置列表元素的值

13.ltrim key start stop 
对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除

14.rpop key 
移除列表的最后一个元素，返回值为移除的元素

15.rpoplpush source destination 
移除列表的最后一个元素，并将该元素添加到另一个列表并返回

16.rpush key value1 [value2] 
在列表中添加一个或多个值

17.rpushx key value 
为已存在的列表添加值
```

### 3-5. 集合(set)数据结构操作

```redis
1.sadd key member1 [member2] 
向集合添加一个或多个成员

2.scard key 
获取集合的成员数

3.sdiff key1 [key2] 
返回给定所有集合的差集

4.sdiffstore destination key1 [key2] 
返回给定所有集合的差集并存储在 destination 中

5.sinter key1 [key2] 
返回给定所有集合的交集

6.sinterstore destination key1 [key2] 
返回给定所有集合的交集并存储在 destination 中

7.sismember key member 
判断 member 元素是否是集合 key 的成员

8.smembers key 
返回集合中的所有成员

9.smove source destination member 
将 member 元素从 source 集合移动到 destination 集合

10.spop key 
移除并返回集合中的一个随机元素

11.srandmember key [count] 
返回集合中一个或多个随机数

12.srem key member1 [member2] 
移除集合中一个或多个成员

13.sunion key1 [key2] 
返回所有给定集合的并集

14.sunionstore destination key1 [key2] 
所有给定集合的并集存储在 destination 集合中

15.sscan key cursor [match pattern] [count count] 
迭代集合中的元素
```

### 3-6. 有序集合(sorted set)数据结构操作

```redis
1.zadd key score1 member1 [score2 member2] 
向有序集合添加一个或多个成员，或者更新已存在成员的分数

2.zcard key 
获取有序集合的成员数

3.zcount key min max 
计算在有序集合中指定区间分数的成员数

4.zincrby key increment member 
有序集合中对指定成员的分数加上增量 increment

5.zinterstore destination numkeys key [key ...] 
计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中

6.zlexcount key min max 
在有序集合中计算指定字典区间内成员数量

7.zrange key start stop [withscores] 
通过索引区间返回有序集合成指定区间内的成员

8.zrangebylex key min max [limit offset count] 
通过字典区间返回有序集合的成员

9.zrangebyscore key min max [withscores] [limit] 
通过分数返回有序集合指定区间内的成员

10.zrank key member 
返回有序集合中指定成员的索引

11.zrem key member [member ...] 
移除有序集合中的一个或多个成员

12.zremrangebylex key min max 
移除有序集合中给定的字典区间的所有成员

13.zremrangebyrank key start stop 
移除有序集合中给定的排名区间的所有成员

14.zremrangebyscore key min max 
移除有序集合中给定的分数区间的所有成员

15.zrevrange key start stop [withscores] 
返回有序集中指定区间内的成员，通过索引，分数从高到底

16.zrevrangebyscore key max min [withscores] 
返回有序集中指定分数区间内的成员，分数从高到低排序

17.zrevrank key member 
返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序

18.zscore key member 
返回有序集中，成员的分数值

19.zunionstore destination numkeys key [key ...] 
计算给定的一个或多个有序集的并集，并存储在新的 key 中

20.zscan key cursor [match pattern] [count count] 
迭代有序集合中的元素（包括元素成员和元素分值）
```

### 3-7. 发布订阅命令

```redis
1.psubscribe pattern [pattern ...] 
订阅一个或多个符合给定模式的频道

2.pubsub subcommand [argument [argument ...]] 
查看订阅与发布系统状态

3.publish channel message 
将信息发送到指定的频道

4.punsubscribe [pattern [pattern ...]] 
退订所有给定模式的频道

5.subscribe channel [channel ...] 
订阅给定的一个或多个频道的信息

6.unsubscribe [channel [channel ...]] 
指退订给定的频道
```

### 3-8. 事务命令

```redis
1.discard 
取消事务，放弃执行事务块内的所有命令

2.exec 
执行所有事务块内的命令

3.multi 
标记一个事务块的开始

4.unwatch 
取消 watch 命令对所有 key 的监视

5.watch key [key ...] 
监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断
```

### 3-9. 脚本命令

```redis
1.eval script numkeys key [key ...] arg [arg ...] 
执行 lua 脚本

2.evalsha sha1 numkeys key [key ...] arg [arg ...] 
执行 lua 脚本

3.script exists script [script ...] 
查看指定的脚本是否已经被保存在缓存当中

4.script flush 
从脚本缓存中移除所有脚本

5.script kill 
杀死当前正在运行的 lua 脚本

6.script load script 
将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本
```

### 3-10. 连接命令

```redis
1.auth password 
验证密码是否正确

2.echo message 
打印字符串

3.ping 
查看服务是否运行

4.quit 
关闭当前连接

5.select index 
切换到指定的数据库
```

### 3-11. 服务器命令

```reids
1.bgrewriteaof 
异步执行一个 aof（appendonly file） 文件重写操作

2.bgsave 
在后台异步保存当前数据库的数据到磁盘

3.client kill [ip:port] [id client-id] 
关闭客户端连接

4.client list 
获取连接到服务器的客户端连接列表

5.client getname 
获取连接的名称

6.client pause timeout 
在指定时间内终止运行来自客户端的命令

7.client setname connection-name 
设置当前连接的名称

8.cluster slots 
获取集群节点的映射数组

9.command 
获取 redis 命令详情数组

10.command count 
获取 redis 命令总数

11.command getkeys 
获取给定命令的所有键

12.time 
返回当前服务器时间

13.command info command-name [command-name ...] 
获取指定 redis 命令描述的数组

14.config get parameter 
获取指定配置参数的值

15.config rewrite 
对启动 redis 服务器时所指定的 redis.conf 配置文件进行改写

16.config set parameter value 
修改 redis 配置参数，无需重启

17.config resetstat 
重置 info 命令中的某些统计数据

18.dbsize 
返回当前数据库的 key 的数量

19.debug object key 
获取 key 的调试信息

20.debug segfault 
让 redis 服务崩溃

21.flushall 
删除所有数据库的所有key

22.flushdb 
删除当前数据库的所有key

23.info [section] 
获取 redis 服务器的各种信息和统计数值

24.lastsave 
返回最近一次 redis 成功将数据保存到磁盘上的时间，以 unix 时间戳格式表示

25.monitor 
实时打印出 redis 服务器接收到的命令，调试用

26.role 
返回主从实例所属的角色

27.save 
同步保存数据到硬盘

28.shutdown [nosave] [save] 
异步保存数据到硬盘，并关闭服务器

29.slaveof host port 
将当前服务器转变为指定服务器的从属服务器(slave server)

30.slowlog subcommand [argument] 
管理 redis 的慢日志

31.sync 
用于复制功能(replication)的内部命令
```
