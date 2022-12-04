
## find
####  -name, -iname, path, ipath
> -name 选项可以根据文件名称进行检索(区分大小写),如需要忽略文件名中的大小写，可以使用 -iname 选项
> 
> 搜索时匹配某个文件或目录的完整路径，而不仅仅是匹配文件名。可以使用 -path 或 -ipath 选项
- ? 可以表示任意一个单一的符号
- \* 可以表示任意数量（包括 0）的未知符号


```shell
find /data -name '*.txt'
find /data -name '????'
find /data -path '*/src/*.txt'
```


#### -type

> 搜索得到文件或目录，即不想它们同时出现在结果中。可以使用 -type 选项指定文件类型
- f: 文件
- d: 目录
- l: 符号链接


```shell
find /data -type d -name 'python*'
```

#### -empty
> 检索为空的文件或目录。空文件即文件里没有任何内容，空目录即目录中没有任何文件或子目录


```shell
find ~ -type d -empty
```

#### 反义 !
> 对当前的匹配条件进行“反义”（类似于逻辑非操作）

```shell
find /data -type f ! -name '*.txt'
find /data -type f ! -empty
```
#### -user, -group
> 检索归属于特定用户或组的文件或目录

```shell
find / -type f -user starky
find / -type f -group starky
```

#### -mtime，-atime, -ctime,-mmin，-amin，-cmin 

> 修改时间（Modification time）：最后一次文件内容有过更改的时间点
>
> 访问时间（Access time）：最后一次文件有被读取过的时间点
>
> 变更时间（Change time）：最后一次文件有被变更过的时间点（如内容被修改，或权限等 metadata 被修改）



```shell
find /data -type f -mtime 2
find /data -type f -mtime 2 -amin 5
```

#### -size

> 通过文件大小进行搜索（只适用于文件，目录没有大小……）, 还可以使用 + 或 - 符号表示大于或小于当前条件.

- c：字节
- k：Kb
- M：Mb
- G：Gb


```shell
find / -size +1G
```

#### -perm
> 以文件权限为依据进行搜索


```shell
find /data -perm u=rwx,g=rx,o=rx
find /data -perm a=rx
# 匹配文件权限的一个子集
find / -type f -perm /a=x
find /data -perm 644
```

#### -maxdepth

> 限制 find 命令递归的层数

```shell
find / -maxdepth 1
```

#### -a, -o

>  “and” 和 “or” 两种逻辑运算，对应的命令选项分别是 -a 和 -o。通过这两个选项可以对搜索条件进行更复杂的组合



```shell
find /data -type d -name 'python*'

#该命令等同于：
find /data -type d -a -name 'python*'

更复杂的组合形式如：
find / '(' -mmin -5 -o -mtime +50 ')' -a -type f

```

#### -delete


```shell
find ~ -type d -empty -delete
```


#### -exec
> 可以对搜索到的结果执行特定的命令
>
> 大括号（{}）作为检索到的文件的 占位符 ，而分号（ ;）作为命令结束的标志。因为分号是 Shell 中有特殊含义的符号，所以需要使用单引号括起来。
> 
> 每当 find 命令检索到一个符合条件的文件，会使用其完整路径取代命令中的 {}，然后执行 -exec 后面的命令一次
>
> ';' 也可替换成  \; 注意\ 前有空格

```shell
find ~ -type f -name '*.mp3' -exec cp {} /media/MyDrive ';'
# 等价于
find ~ -type f -name '*.mp3' -exec cp {} /media/MyDrive \;

find ~ -type f -exec grep -l hello {} ';'
# -exec 选项中的 + 符号
# 命令每次发现一个音频文件，都会再执行一次 -exec 选项后面的压缩命令。导致先前生成的压缩包被覆盖
find ~ -type f -name '*.mp3' -exec tar -czvf music.tar.gz {} ';'
# find 命令检索出所有符合条件的音频文件，再将得到的文件列表传递给后面的压缩命令
find ~ -type f -name '*.mp3' -exec tar -czvf music.tar.gz {} +

```

#### -ls
> 显示文件信息


```shell
find / -type file -size +1G -ls
```

#### -print0

>  find 每输出一个文件名, 后面都会接着输出一个换行符 ('\n'), 因此我们看到的 find 的输出都是一行一行的, -print0 让 find命令在打印出一个文件名之后接着输出一个 NULL 字符而不是换行符

---


## xargs
> xargs识别字符段的标识是空格或者换行符，所以如果一个文件名里有空格或者换行符，xargs就会把它识别成两个字符串

- -0：用 NULL 字符来作为记录的分隔符
- -t：表示先打印命令，然后再执行。
- -a: <file>:从文件中读入作为sdtin。
- -i: xargs的输出每一项参数，单独赋值给后面的命令，参数需要用{}代替
- -I：某些Linux版本不支持。将xargs的输出每一项参数，-I 参数名, -I {} 等于 -i

