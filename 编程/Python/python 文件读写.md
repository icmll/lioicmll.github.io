### open()函数
```python
f1 = open(r'./test.txt', mode='r', encoding='utf-8')
content = f1.read()
print(content)
f1.close()

with open(r'./test.txt', mode='r', encoding='utf-8') as f1:
    content = f1.read()
    print(content)
```

> 1.open()内置函数，open底层调用的是操作系统的接口。

> 2. f1变量，又叫文件句柄，通常文件句柄命名有f1，fh，file_handler,f_h，对文件进行的任何操作，都得通过文件句柄.方法的形式。
> 
> 3. encoding:可以不写。不写参数，默认的编码本是操作系统默认的编码本。windows默认gbk，linux默认utf-8，mac默认utf-8。
> 
> 4. mode:可以不写。默认mode='r'。
> 
> 5. f1.close()关闭文件句柄。
> 
### with open()
> 优点1：不用手动关闭文件句柄。

```python
with open('文件操作的读', encoding='utf-8') as f1:
    print(f1.read())
```

> 优点2：一个语句可以操作多个文件句柄。
```python
with open('文件操作的读', encoding='utf-8') as f1, \
        open('文件操作的写', encoding='utf-8', mode='w') as f2:
    print(f1.read())
    f2.write('hahaha')
```


### read函数使用
> 
> read()全部读取出来:用rb模式打开，不用写encoding
> 
> read(n) 按照字符读取（r模式），按照字节读取（rb模式）
> 
> readline() 读取一行
> 
> readlines() 返回一个列表，列表中的每个元素是原文件的每一行。如果文件很大，占内存，容易崩盘
> 
> for 循环读取, 文件句柄是一个迭代器。特点是每次循环只在内存中占一行的数据，非常节省内存


### write 函数
> w模式, 没有文件，则创建文件，写入内容；如果文件存在，先清空原文件内容，在写入新内容
> 
> wb模式


```python
f2 = open('图片.jpg', mode='wb')
f2.write(content)
f2.close()
```


### 指针定位
> tell() 方法用来显示当前指针的位置


```python
f = open('test.txt')
print(f.read(10))  # read 指定读取的字节数
print(f.tell())    # tell()方法显示当前文件指针所在的文字
f.close()
```


> ### seek(offset,whence)方法用来重新设定指针的位置
> 
> offset:表示偏移量
> 
> whence:只能传入012中的一个数字
> 
>    0表示从文件头开始
> 
>    1表示从当前位置开始
> 
>    2 表示从文件的末尾开始

```python
f = open('test.txt','rb')  # 需要指定打开模式为rb,只读二进制模式
print(f.read(3))
print(f.tell())
f.seek(2,0)   # 从文件的开头开始，跳过两个字节
print(f.read())
f.seek(1,1) # 从当前位置开始，跳过一个字节
print(f.read())
f.seek(-4,2) # 从文件末尾开始，往前跳过四个字节
print(f.read())
f.close()

```

### 实现文件拷贝功能

```python
import os

file_name = input('请输入一个文件路径:')
if os.path.isfile(file_name):
    old_file = open(file_name, 'rb')  # 以二进制的形式读取文件

    names = os.path.splitext(file_name)
    new_file_name = names[0] + '.bak' + names[1]

    new_file = open(new_file_name, 'wb')  # 以二进制的形式写入文件

    while True:
        content = old_file.read(1024)  # 读取出来的内容是二进制
        new_file.write(content)
        if not content:
            break

    new_file.close()
    old_file.close()
else:
    print('您输入的文件不存在')
```


### 将数据写入内存
- StringIO
```python

from io import StringIO

# 创建一个StringIO对象
f = StringIO()
# 可以像操作文件一下，将字符串写入到内存中
f.write('hello\r\n')
f.write('good')

# 使用文件的 readline和readlines方法，无法读取到数据
# print(f.readline())
# print(f.readlines())

# 需要调用getvalue()方法才能获取到写入到内存中的数据
print(f.getvalue())

f.close()
```

- BytesIO
> 它的用法和StringIO相似，只不过在调用write方法写入时，需要传入二进制数据


```python
from io import BytesIO

f = BytesIO()
f.write('你好\r\n'.encode('utf-8'))
f.write('中国'.encode('utf-8'))

print(f.getvalue())
f.close()
```

### sys.stdin, sys.stdout, sys.stderr

> sys.stdin 接收用户的输入，就是读取键盘里输入的数据，默认是控制台。input方法就是读取 sys.stdin 里的数据
```python
import sys
s_in = sys.stdin
while True:
    content = s_in.readline().rstrip('\n')
    if content == '':
        break
    print(content)
```

> sys.stdout 标准输出，默认是控制台

```python
# 标准 输出到stdout.txt
import sys
m = open('stdout.txt', 'w', encoding='utf8')
sys.stdout = m
print('hello')
print('yes')
print('good')
m.close()
```


> sys.stderr 错误输出，默认是控制台


```python
# error 输出到stderr.txt
import sys
x = open('stderr.txt', 'w', encoding='utf8')
sys.stderr = x
print(1 / 0)
x.close()
```