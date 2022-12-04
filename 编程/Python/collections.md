# Python Collections

> collections模块：实现了特定目标的容器，以提供Python标准内建容器 dict、list、set、tuple 的替代选择



- Counter

> 字典的子类，提供了可哈希对象的计数功能。

```python
import collections
# 统计字符出现的次数
collections.Counter('hello world')

# 统计单词个数
collections.Counter('hello world hello world'.split())
```

> 常用方法

1. elements() 返回一个迭代器, 所有元素, 重复计算的.
2. ost_common([n])：返回一个列表，提供n个访问频率最高的元素和计数
3. subtract([iterable-or-mapping])：从迭代对象中减去元素，输入输出可以是0或者负数
4. update([iterable-or-mapping])：从迭代对象计数元素或者从另一个 映射对象 (或计数器) 添加。


- defaultdict
> 字典的子类，提供了一个工厂函数，为字典查询提供了默认值。
```python
e = collections.defaultdict(str)
e['hello']
```

- OrderedDict
> 字典的子类，保留了他们被添加的顺序。
```python
o = collections.OrderedDict()
P2 = collections.namedtuple('Person2','name,age,height')
P3 = collections.namedtuple('Person3','name age height')
```

- namedtuple
> 创建命名元组子类的工厂函数。
```python
# 三种实例化方式
P1 = collections.namedtuple('Person1',['name','age','height'])
P2 = collections.namedtuple('Person2','name,age,height')
P3 = collections.namedtuple('Person3','name age height')

lucy = P1('lucy',23,180)
jack = P2('jack',20,190)

# 直接通过 实例名.属性 来调用
lucy.name
jack.age
```



- deque
> 类似列表容器，实现了在两端快速添加(append)和弹出(pop)。
>
> collections.deque 返回一个新的双向队列对象，从左到右初始化（用方法 append()），从 iterable（迭代对象）数据创建。如果 iterable 没有指定，新队列为空。
>
> collections.deque 队列支持线程安全，对于从两端添加（append）或者弹出（pop），复杂度O(1)。
>
> 虽然 list 对象也支持类似操作，但是这里优化了定长操作（pop(0)、insert(0,v)）的开销。
>
> 如果 maxlen 没有指定或者是 None ，deque 可以增长到任意长度。否则，deque 就限定到指定最大长度。一旦限定长度的 deque 满了，当新项加入时，同样数量的项就从另一端弹出。
>
> 支持方法:
1. append(x)：添加x到右端。
2. appendleft(x)：添加x到左端
3. clear()：清除所有元素，长度变为0。
4. copy()：创建一份浅拷贝。
5. count(x)：计算队列中个数等于x的元素。
6. extend(iterable)：在队列右侧添加iterable中的元素。
7. extendleft(iterable)：在队列左侧添加iterable中的元素，注：在左侧添加时，iterable参数的顺序将会反过来添加。
8. index(x[,start[,stop]])：返回第 x 个元素（从 start 开始计算，在 stop 之前）。返回第一个匹配，如果没找到的话，抛出 ValueError 。
9. insert(i,x)：在位置 i 插入 x 。注：如果插入会导致一个限长deque超出长度 maxlen 的话，就抛出一个 IndexError 。
10. pop()：移除最右侧的元素。
11. popleft()：移除最左侧的元素。
12. remove(value)：移去找到的第一个 value。没有抛出ValueError。
13. reverse()：将deque逆序排列。返回 None 。
14. maxlen：队列的最大长度，没有限定则为None。

```python
d = collections.deque(maxlen=10)
```



- ChainMap
> 类似字典的容器类，将多个映射集合到一个视图里面。
>
> 当对ChainMap进行修改的时候总是只会对第一个字典进行修改，如果第一个字典不存在该键，会添加。
>
> 从原理上面讲，ChainMap 实际上是把放入的字典存储在一个队列中，当进行字典的增加删除等操作只会在第一个字典上进行，当进行查找的时候会依次查找，new_child() 方法实质上是在列表的第一个元素前放入一个字典，默认是{}，而 parents 是去掉了列表开头的元素。

```python
d1 = {'apple':1,'banana':2}
d2 = {'orange':2,'apple':3,'pike':1}
c = collections.ChainMap(d1,d2)
print(c)
# ChainMap({'apple': 1, 'banana': 2}, {'orange': 2, 'apple': 3, 'pike': 1})
```

