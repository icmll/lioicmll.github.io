1.  执行字符串代码

-   eval函数执行python表达式，有返回值

```python
eval("1+2+3+4+5")

```

-   exec函数执行的是python语句，没有返回值

```python
exec('print(123)')

```

-   将字符串类型的代码编码., 代码对象能够通过exec语句来执行或者eval()进行求值, c只是编译, 不执行

```python
code = "for i in range(10): print(i)"

comp1 = compile(code1, "", mode="exec")
exec(comp1)

comp2 = compile(code1, "", mode="eval")
eval(comp2)


```

2. 数据类型转换

```python

#函数用于将一个字符串或数字转换为整型
int('123')
# int还有个参数位位数据类型
int(b'101101001', 2)

#将数据转化成字符串, 调用的是对象的__str__()
str(123)


#返回对应的 ASCII 数值，或者 Unicode 数值
ord('a')

#用一个范围在 range（256）内的（就是0～255）整数作参数，返回一个对应的字符
chr(55)

#返回任何对象（字符串，元组，列表等）的可读版本, 所有非 ascii 字符替换为转义字符返回unicode
ascii()

#函数用于将给定参数转换为布尔类型，如果没有参数，返回 False, bool 是 int 的子类
bool()

# 转换为浮点
ﬂoat(123)
```

3. 创建内置对象

```python
#创建字典
dict()
dict(a='a', b='b', t='t')
dict([("a", 123), ("b", 1234), ("c", 1235)])

#创建集合, iterable -- 可迭代的对象，比如列表、字典、元组等等
set(iterable)

#返回一个冻结的集合，冻结后集合不能再添加或删除任何元素
#frozenset([iterable])

#创建列表
list(iterable)

#创建数组
tuple(iterable)

#函数可创建一个整数列表或迭代器，一般用在 for 循环中
range(5)

#函数用于创建一个值为 real + imag * j 的复数或者转化一个字符串或数为复数。如果第一个参数为字符串，则不需要指定第二个参数
#complex([real[, imag]])

#函数用来生成迭代器
#iter(object[, sentinel])
#object -- 支持迭代的集合对象。
#sentinel -- 如果传递了第二个参数，则参数 object 必须是一个可调用的对象（如，函数），此时，iter 创建了一个迭代器对象，每次调用这个迭代器对象的__next__()方法时，都会调用 object。

#函数返回一个反转的迭代器, seq -- 要转换的序列，可以是 tuple, string, list 或 range
reversed(seq)
```

 4. 列表处理

```python

#最小值
min([1,2,3,4,])

#最大值
max([1,2,3,4,])

#求和
sum([1,2,3,4,])

#可迭代对象中全部是True, 结果才是True
all([1,2,3,4,])

#可迭代对象中有一个是True, 结果就是True
any([1,2,3,4,])

#对可迭代对象进行排序操作sorted(iterable, cmp=None, key=None, reverse=False)
sorted([1,2,3,4,])

#获取集合的枚举对象enumerate(sequence, [start=0])
enumerate([1,2,3,4,])


ﬁlter()
map()
reduce()
zip()

```

5. 数学运算

```python
#方法返回 x的y次方 的值
pow(x, y)

#将给的参数转换成八进制
oct(123)

#将给的参数转换成十六进制
hex(123)

#返回传递值的二进制
bin(123)

#返回绝对值
abs(-123)

#返回浮点数x的四舍五入值, 其实也不一定是四舍五入, Python版本不一样貌似是有差别
round( x [, n])
```

6. 对象操作

```python
hash()
vars()
getattr()
type()
issubclass()
hasattr()
len()
delattr()
repr()

#获取到对象的内存地址
id()

#返回给定参数的内存查看对象
#memoryview(obj)


#设置属性值，该属性不一定是存在的
setattr(object, name, value)

# 判断对象类型isinstance(object, class)
isinstance()

#检查一个对象是否是可调用的
#callable(obj)
>>>v = memoryview('abcefg')
>>> v[1]
'b'
>>> v[-1]
'g'
>>> v[1:4]
<memory at 0x77ab28>
>>> v[1:4].tobytes()
'bce'


```

7. 帮助

```python
#函数用于查看函数或模块用途的详细说明
help()

#查看对象的内置属性, 访问的是对象中的__dir__()方法
dir()

#函数会以字典类型返回当前位置的全部全局变量
globals()

#函数会以字典类型返回当前位置的全部局部变量
locals()
```

8. 其他

```python
#函数用于动态加载类和函数
#__import__(name[, globals[, locals[, fromlist[, level]]]])

返回一个空对象, 不能向这个对象添加新的属性或方法, 对象是所有类的基础，它拥有所有类默认的内置属性和方法
object()

#返回函数的静态方法, 一般用在 @staticmethod 装饰器装饰
staticmethod(func)

#返回函数的类方法, 一般用在 @classmethod 装饰器装饰
classmethod(func)

#新式类中返回属性值, 一般用在 @property 装饰器装饰
#property([fget[, fset[, fdel[, doc]]]])
#fget -- 获取属性值的函数
#fset -- 设置属性值的函数
#fdel -- 删除属性值函数
#doc -- 属性描述信息

# 获取用户输出的内容
input()

#迭代器向下执行一次, 内部实际使⽤用了__next__()⽅方法返回迭代器的下一个项目
next()

# 调用父类
super()

#方法返回一个新字节数组。这个数组里的元素是可变的，并且每个元素的值范围: 0 <= x < 256
#bytearray([source[, encoding[, errors]]])
#如果 source 为整数，则返回一个长度为 source 的初始化数组；
#如果 source 为字符串，则按照指定的 encoding 将字符串转换为字节序列；
#如果 source 为可迭代类型，则元素必须为[0 ,255] 中的整数；
#如果 source 为与 buffer 接口一致的对象，则此对象也可以被用于初始化 bytearray。
#如果没有输入任何参数，默认就是初始化数组为0个元素。
bytearray()

#函数返回一个新的 bytes 对象，该对象是一个 0 <= x < 256 区间内的整数不可变序列。它是 bytearray 的不可变版本
#bytes([source[, encoding[, errors]]])
bytes()

#用于打开一个文件, 创建一个文件句柄
#open(name[, mode[, buffering]])
open()

# 输出
print(123)
```