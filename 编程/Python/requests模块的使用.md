## 1. 单字段发送单个文件

在requests中发送文件的接口只有一种，那就是使用requests.post的files参数， 请求形式如下：
```python
url = "http://httpbin.org/post"
data = None
files = { ... }
# 使用files参数发送formdata
r = requests.post(url, data, files=files)
```
而这个files参数是可以接受很多种形式的数据，最基本的2种形式为：字典类型, 元组列表类型

### 1-1 字典类型的files参数

官方推荐使用的字典参数格式如下：

```python
{
    "field1" : ("filename1", open("filePath1", "rb")),
    "field2" : ("filename2", open("filePath2", "rb"), "image/jpeg"),
    "field3" : ("filename3", open("filePath3", "rb"), "image/jpeg", {"refer" : "localhost"})
}
```
> 这个字典的key就是发送post请求时的字段名， 而字典的value则描述了准备发送的文件的信息；从上面可以看出value可以是2元组，3元组或4元组。这个元组的每一个字段代表的意思一次为：
> 
> ("filename", "fileobject", "content-type", "headers")

缺省的话则会使用默认值。除了上面的使用形式，其实requests还是支持一个更简洁的参数形式，如下：
```python
{
  "field1" : open("filePath1", "rb"),
  "field2" : open("filePath2", "rb"),
  "field3" : open("filePath3", "rb")
}
```
这种形式的参数其等同效果如下, 其中filename是filepath的文件名：
```python
{
  "field1" : ("filename1", open("filePath1", "rb")),
  "field2" : ("filename2", open("filePath2", "rb")),
  "field3" : ("filename3", open("filePath3", "rb"))
}
```
也可以是如下， 这里的filename的值为field1
```python
{
  "field1" : open("filePath1", "rb").read()
}
```

### 1-2 元组列表类型的files参数

其实元组列表的形式与字典的形式基本一样，除了最外层的包装不一样；而在requests内部最终会把字典参数形式 转换 为 元组列的形式。官网推荐的用法如下：
```python
[
  ("field1" : ("filename1", open("filePath1", "rb"))),
  ["field2" : ("filename2", open("filePath2", "rb"), "image/jpeg")],
  ("field3" : ("filename3", open("filePath3", "rb"), "image/jpeg", {"refer" : "localhost"}))
]
```

列表里面的子项可以是元组，也可以是列表；同样这里也支持简介的形式，如下：
```python
[
  ("field1" : open("filePath1", "rb"))),  ##filename 使用的是filepath的文件名
  ("field2" : open("filePath2", "rb").read())) ##filename 使用的是键值，即 field2
]
```

## 2. 单字段发送多个文件【即上传文件时，设置为多选了】
### 2-1 字典参数形式
```python
{
  "field1" : [
                 ("filename1", open("filePath1", "rb")), 
                 ("filename2", open("filePath2", "rb"), "image/png"), 
                 open("filePath3", "rb"),
                 open("filePath4", "rb").read()
               ]
}
```
### 2-1 元组列表形式
```python
[
  ("field1" , ("filename1", open("filePath1", "rb"))),
  ("field1" , ("filename2", open("filePath2", "rb"), "image/png")), 
  ("field1" , open("filePath3", "rb")),
  ("field1" , open("filePath4", "rb").read())
]
```

上面2种形式发送的请求，所有的文件都会在同一个字段下，后台服务只要从field1字段就可以获取全部的文件对象

## 3. 同时发送普通数据字段
上面介绍的是使用发送文件内容请求，而有时候我们在发送文件的同时还需要发送普通的数据字段，此时普通数据字段直接存在data参数中即可，如下：
```python
data = {"k1" : "v1"}
files = {
　　"field1" : open("1.png", "rb")
}
r = requests.post("http://httpbin.org/post", data, files=files)
```

## 4. MultipartEncoder流式上传
```python
import requests
from requests_toolbelt.multipart.encoder import MultipartEncoder

m = MultipartEncoder(
    fields={'field0': 'value', 'field1': 'value',
            'field2': ('filename', open('file.py', 'rb'), 'text/plain')}
    )

r = requests.post('http://httpbin.org/post', data=m,
                  headers={'Content-Type': m.content_type})

```

## 5. 实例
### 5-1 通过data参数和files参数发送请求
```python
import requests

request_url = 'http://www.demo.com/studentInfo/saveNewInfo'
# 构造字典，键值对方式传参
request_data = {
    'id': '9',
    'name': '赵云',
    'tel':'13212345678',
    'school': '西南科技大学',
    'major': '计算机',
    'age': '30'
}
# 上传文件单独构造成以下形式
# 'img' 上传文件的键名
# 'demo' 上传到服务器的文件名，可以和上传的文件名不同
# open('D:/demo.jpg') 打开的文件对象，注意文件路径正确
# 'image/jpeg' Content-Type类型
request_file = {'img':(('demo',open('D:/demo.jpg')),'image/jpeg')}

requests.post(url=request_url, data=request_data, files=request_file)   # url,data,files
```

### 5-2 仅通过files参数模拟文件发送请求
```python
import requests

request_url = 'http://www.demo.com/studentInfo/saveNewInfo'
# 构造字典，键值对方式传参
# 不是文件的构造键值对，键值为一个元组形式，元组第0位为None，第1位为键值.
request_files = {
    'id': (None, '9'),
    'name': (None, '赵云'),
    'tel':(None, '13212345678'),
    'school': (None, '西南科技大学'),
    'major': (None, '计算机'),
    'age': (None, '30'),
    'img':(('demo',open('D:/demo.jpg')),'image/jpeg')
    # 'img':(None,'','image/jpeg')   不传文件的写法. form-data格式发送键值对
}

requests.post(url=request_url, files=request_files)     # url,files
```