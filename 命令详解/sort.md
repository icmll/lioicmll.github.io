# sort

>  用法：sort [选项]... [文件]...
> 串联排序所有指定文件并将结果写到标准输出。

- 排序选项：

```shell
-b, --ignore-leading-blanks 忽略前导的空白区域
-d, --dictionary-order 只考虑空白区域和字母字符
-f, --ignore-case 忽略字母大小写
-g, --general-numeric-sort 按照常规数值排序
-i, --ignore-nonprinting 只排序可打印字符
-n, --numeric-sort 根据字符串数值比较
-r, --reverse 逆序输出排序结果

-c, --check, --check=diagnose-first 检查输入是否已排序，若已有序则不进行操作
-k, --key=位置1[,位置2] 在位置1 开始一个key，在位置2 终止(默认为行尾)
-m, --merge 合并已排序的文件，不再进行排序
-o, --output=文件 将结果写入到文件而非标准输出
-t, --field-separator=分隔符 使用指定的分隔符代替非空格到空格的转换
-u, --unique 配合-c，严格校验排序；不配合-c，则只输出一次排序结果
```