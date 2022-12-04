# sed命令的使用

## 1. 选项与参数

- -n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
- -e ：直接在命令列模式上进行 sed 的动作编辑；
- -f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作；
- -r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
- -i ：直接修改读取的文件内容，而不是输出到终端。
## 2. 动作说明： [n1[,n2]]function

- n1, n2 ：选择操作的行数, 2, 5function代表2到5行, 2, +5function 代表第二行开始往后5行
- !function取反

## 3. function

- a ：新增行， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)
- c ：取代行， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行!
- d ：删除行，d 后面通常不接字符串
- i ：插入行， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)
- p ：列印行，将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行
- s ：取代，可以直接进行取代的工作！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是, 末尾不加g只替换每一行的第一个匹配的字符串
- c和s 的区别, 与s的区别s是只替换匹配到的字符串, c是替换匹配到的字符串的行

## 4. 正则表达式元字符

元字符 | 功能 | 示例
--|--|--
^ | 行首定位符 | /^my/匹配所有以my开头的行
$ | 行尾定位符 | /my$/匹配所有以my结尾的行
. | 匹配除换行符以外的单个字符 | /m..y/匹配包含字母m，后跟两个任意字符，再跟字母y的行
\* | 匹配零个或多个前导字符 | /my*/匹配包含字母m,后跟零个或多个y字母的行
[] | 匹配指定字符组内的任一字符 | /[Mm]y/匹配包含My或my的行
[^] | 匹配不在指定字符组内的任一字符 | /[^Mm]y/匹配包含y，但y之前的那个字符不是M或m的行
空 | 保存已匹配的字符 | 1,20s/self/\1r/标记元字符之间的模式，并将其保存为标签1，之后可以使用\1来引用它。最多可以定义9个标签，从左边开始编号，最左边的是第一个。此例中，对第1到第20行进行处理，you被保存为标签1，如果发现youself，则替换为your。
& | 保存查找串以便在替换串中引用 | s/my/**&**/符号&代表查找串。my将被替换为**my**
\< | 词首定位符 | /\<my/匹配包含以my开头的单词的行
\> | 词尾定位符 | /my\>/匹配包含以my结尾的单词的行
x\{m\} | 连续m个x | /9\{5\}/ 匹配包含连续5个9的行
x\{m,\} | 至少m个x | /9\{5,\}/匹配包含至少连续5个9的行
x\{m,n\} | 至少m个，但不超过n个x | /9\{5,7\}/匹配包含连续5到7个9的行

## 4. 例子

```shell
#建一个测试文件
echo -e "you are my love\nhello word\nfuck coding\nhttp://baidu.com" > test.txt
```

> 不加选项, 操作后的结果打印到屏幕, 不会修改test.txt

- function s

> 替换

```sehll
# you替换成he
sed 's/you/he/' test.txt
# 1到2行的a替换成b
sed '1,2s/a/b/' test.txt
```

- function a

> 在有hello行的下一行插入 k, a后面的\可以不要加上好看些

```shell
# 匹配到hello的下一行添加一行k
sed '/hello/a\k' test.txt
# 1到2行每行后面添加一行k
sed '1,2a\k' test.txt
```

- function c

> 替换有hello的的行为 k, c后面的\可以不要加上好看些

```shell
# 匹配到的hello的行替换成k
sed '/hello/c\k' test.txt
# 1到2行替换成k
sed '1,2c\k' test.txt
```

- function i

> 和a一样, 只不过这个是在匹配行的上一行插入

> function p, !p取反, 与-n配合使用

```shell
# 打印匹配到的行, 或取反
sed -n '/hello/p' test.txt
sed -n '/hello/!p' test.txt
```

```shell
# 打印1到2行, 或取反
sed -n '1,2!p' test.txt
sed -n '1,2p' test.txt
```

- function d

```shell
# 删除匹配到的行, 或取反
sed '/hello/d' test.txt
sed '/hello/!d' test.txt

# 删除1到2行, 或取反
sed -n '1,2!p' test.txt
sed -n '1,2p' test.txt
```

> -i 选项, 这个用的比较多, 这个直接操作文件用法一样, 效果和 sed '/hello/d' test.txt > test.txt 一样.
>
>其他选项自己琢磨

