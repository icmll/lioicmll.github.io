
# 聚合

　　aggregate()是QuerySet的一个终止子句,也就是说,他返回一个包含一些键值对的字典,在它的后面不可以再进行点(.)操作.

 　  键的名称是聚合值的标识符,只是计算出来的聚合值.键的名称是按照字段和聚合函数的名称自动生成出来的.

　　聚合函数的导入


```python
from django.db.models import Max, Min, Sum, Avg, Count
```

　　

　　简单使用示例:

　　查询所有书的平均价格.


```python
from django.db.models import Avg, Sum, Max, Min, Count
ret = models.Book.objects.all().aggregate(Avg("price"))
{'price__avg': 13.233333}  # price__avg
```
 是自动成出来的,前边是聚合的字段,后边是聚合函数的名称.
　　

 　　当需要为聚合值指定一个名称,可以向聚合子句提供它.


```python
from django.db.models import Avg, Sum, Max, Min, Count

models.Book.objects.aggregate(average_price=Avg('price'))  # average_price

{'average_price': 13.233333}
```
　　当需要生成不止一个聚合时,可以向aggregate()子句中添加另一个参数.

　　查询所有图书价格的平均值 最大值 最小值.


```
from django.db.models import Avg, Sum, Max, Min, Count

models.Book.objects.all().aggregate(Avg("price"), Max("price"), Min("price"))
{'price__avg': 13.233333, 'price__max': Decimal('19.90'), 'price__min': Decimal('9.90')}
```

查询所有图书中最高的价格和平均价格


```python
from django.db.models import Max, Avg
ret = models.Book.objects.aggregate(Max('price'), avg=Avg('price'))
```

# 分组
现有员工表如下:

需求:
按照部门分组求平均工资
ORM操作如下:


```python
from django.db.models import Avg

models.Employee.objects.values("dept").annotate(avg=Avg("salary").values("dept", "avg")
```

　　按照哪个字段分组,就将哪个字段写在objects后面的values中.

　　注意:

　　　　分组使用的是annotate(),他不是一个终止子句,他的后边可以跟其他操作.

　　连表查询:

　　　　表格如下:

 　　按照部门分组求平均工资:


```python
from django.db.models import Avg

models.Dept.objects.annotate(avg=Avg("employee__salary")).values("name", "avg")
```

　　按照部门分组,就从部门表上开始查,聚合函数Avg()中写需要差的字段,按照双下划线的方法找到字段.

　　其他示例:

　　统计每一本书的作者的数量


```python
from app01 import models
from django.db.models import Count

ret = models.Book.objects.annotate(Count('author')).values()
```
查询每个出版社出版的图书中价格最低的

方式一:


```python
from app01 import models
from django.db.models import Min

ret = models.Publisher.objects.annotate(Min('book__price')).values()
```

　　从出版社表查,聚合函数Min()中写需要查询的字段,使用双下滑线(__)的方法查询到需要的字段.

 　　方式二:


```python
from app01 import models
from django.db.models import Min
ret = models.Book.objects.values('publisher__name').annotate(min=Min('price'))
```

　　从书表中查,objects后面的values()中写分组依据的字段名.然后跟annotate()中写聚合函数,聚合函数中写查询的字段.

查询作者大于1的图书


```python
from app01 import models
from django.db.models import Count

ret = models.Book.objects.annotate(count=Count('author')).filter(count__gt=1).values()
```
　　从书表中查询,先统计每一本书的作者的数量,然后使用filter()过滤出符合条件的对象.

查询每位作者所出书的总价格

```python
from app01 import models
from django.db.models import Sum

ret = models.Author.objects.annotate(Sum('books__price')).values()
```


　　从作者表查询,聚合函数Sum()中填写查询的字段

 

# F

 　　在此之前我们构造的过滤器都只是将字段与某个常量作比较,如果将两个字段做比较时,可以使用F().F()的实例可以再查询中引用字段,来比较同一个model实例(对象)中的两个不同字段的值.

简单点说就是可以用来动态数据对比查询.

F的导入

查询销量大于库存的图书

```python
from django.db.models import F
from django.db.models import F

ret = models.Book.objects.filter(sale__gt=F('kucun')).values()
```

F的用法是F('字段名'),F()是用来取值的.

将所有书的销量更新为原来的2倍.


```python
from app01 import models
from django.db.models import F

ret = models.Book.objects.all().update(sale=F('sale') * 2)
```
　　可以进行翻倍操作.

PS:
知识补充 ---> .update()　　更新

仅对选中的QuerySet对象进行更新,速度快.


```python
from app01 import models

ret = models.Book.objects.filter(id=2).update(sale=100)
```
将id为2的书的销量设置为100.
将所有书的销量更新为原来的2倍.


```python
from app01 import models
from django.db.models import F

ret = models.Book.objects.all().update(sale=F('sale') * 2)
```

 

　　可以进行翻倍操作.

 

知识补充 ---> .save()　　修改后进行保存.

对所有对象都进行保存,操作数据量大,速度慢.


```python
from app01 import models

obj = models.Book.objects.get(id=2)
obj.sale='1000'
obj.save()
```
将id为2的书的销量设置为1000.

# Q
filter()等方法中的关键字参数查询都是一起进行'and'的,如果需要进行更加复杂的查询,可以使用Q对象.

示例:

查询id小于等于3或者id大于等于6的书


```python
from app01 import models
from django.db.models import Q

ret = models.Book.objects.filter(Q(id__lte=3) | Q(id__gte=6))
```

Q()写在filter()中,使用或(or)查询是每个Q()使用管道符(|)连接,管道符表示或(or)的关系.
查询id大于等于10并且销量大于等于3000的书.


```python
from app01 import models
from django.db.models import Q

ret = models.Book.objects.filter(~Q(price__gte=10) & Q(sale__gte=3000)).values()
```
使用&连接时表示and.