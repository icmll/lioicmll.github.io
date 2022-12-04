### 1. 下载文件

```shell
curl --remote-name "https://example.com/linux-distro.iso"
# 或
curl "http://example.com/foo.html" --output bar.html

# 纯文本还可以通过管道或重定向处理
curl "http://example.com/foo.html" > fool.html
curl "http://example.com/foo.html" | tail -n 10
```

### 2. 列出带有远程目录

```shell
curl --list-only "https://example.com/foo/"
```

### 3. 继续中断下载

- --continue-at: 如果你知道下载中断时的字节数，你可以提供给 curl；否则，你可以使用单独的一个破折号（-）指示 curl 自动检测
  
  ```shell
  curl --remote-name --continue-at - "https://example.com/linux-distro.iso"
  ```

### 4. 下载文件序列

```shell
# #1 表示第一个变量
curl "https://example.com/file_[1-4].webp" --output "file_#1.webp"

# #1 指目录 images_000 到 images_009，而 #2 指目录 file_1.webp 至 file_4.webp
curl "https://example.com/images_00[0-9]/file_[1-4].webp" --output "file_#1-#2.webp"
```

### 5. 下载所有 PNG 文件

```shell
curl https://example.com |\
grep --only-matching 'src="[^"]*.[png]"' |\
cut -d\" -f2 |\
while read i; do \
curl https://example.com/"${i}" -o "${i##*/}"; \
done
```

### 6. 获取 HTML 头

```shell
curl --head "https://example.com"
curl -I "https://example.com"
```

### 7. 失败时迅速退出

```shell
curl --fail-early "http://opensource.ga"
```

### 8. 重定向查询

```shell
curl --location "https://iana.org"
curl -L "https://iana.org"
```

### 9. 发送User-Agent

```shell
curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://google.com

# 移除User-Agent标头
curl -A '' https://google.com

# 也可以通过-H参数直接指定标头，更改User-Agent
curl -H 'User-Agent: php/1.0' https://google.com
```

### 10. 发送 Cookie或把服务器cookie写入文件

```shell
curl -b 'foo1=bar;foo2=bar2' https://google.com
# 读取本地文件
curl -b cookies.txt https://www.google.com

# 把服务器cookie写入文件
curl -c cookies.txt https://www.google.com
```

### 11. POST 请求的数据体

> -d参数以后，HTTP 请求会自动加上标头Content-Type : application/x-www-form-urlencoded。并且会自动将请求转为 POST 方法，因此可以省略-X POST

```shell
curl -d'login=emma＆password=123'-X POST https://google.com/login
# 或者
curl -d 'login=emma' -d 'password=123' -X POST  https://google.com/login

# data.txt文件的内容，作为数据体向服务器发送
curl -d '@data.txt' https://google.com/login

# --data-urlencode参数等同于-d，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码
# hello world之间有一个空格，需要进行 URL 编码
curl --data-urlencode 'comment=hello world' https://google.com/login
```

### 12. HTTP 的标头Referer

```shell
curl -e 'https://google.com?q=example' https://www.example.com
或
curl -H 'Referer: https://google.com?q=example' https://www.example.com
```

### 13. 上传二进制文件

```shell
# HTTP 请求加上标头Content-Type: multipart/form-data，然后将文件photo.png作为file字段上传
curl -F 'file=@photo.png' https://google.com/profile

# 指定 MIME 类型为image/png，否则 curl 会把 MIME 类型设为application/octet-stream
curl -F 'file=@photo.png;type=image/png' https://google.com/profile

# -F参数也可以指定文件名
curl -F 'file=@photo.png;filename=me.png' https://google.com/profile
```

### 14. 构造 URL 的查询字符串

```shell
curl -G -d 'q=kitties' -d 'count=20' https://google.com/search
或
curl -G --data-urlencode 'comment=hello world' https://www.example.com
```

### 15. 添加 HTTP 请求的标头

```shell
curl -H 'Accept-Language: en-US' https://google.com
curl -H 'Accept-Language: en-US' -H 'Secret-Message: xyzzy' https://google.com
curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login
```

### 16. 打印出服务器回应的 HTTP 标头

```shell
curl -i https://www.example.com
```

### 17. 向服务器发出 HEAD 请求

> -I 与 --head参数一样

```shell
curl -I https://www.example.com
# 或
curl --head https://www.example.com
```

### 18. 跳过 SSL 检测

```shell
curl -k https://www.example.com
```

### 19. 限制 HTTP 请求和回应的带宽

```shell
# 带宽限制在每秒 200K 字节
curl --limit-rate 200k https://google.com
```

### 19. 保存成文件

```shell
curl -o example.html https://www.example.com
# 或
# 将 URL 的最后部分当作文件名
curl -O https://www.example.com/foo/bar.html

# basic auth
> 设置用户名为bob，密码为12345，然后将其转为 HTTP 标头Authorization: Basic Ym9iOjEyMzQ1
```shell
curl -u 'bob:12345' https://google.com/login
# 或
# 自动从url中获取
curl https://bob:12345@google.com/login
# 或
# 交互输入密码
curl -u 'bob' https://google.com/login
```

### 20. 打印通信过程

```shell
curl -v https://www.example.com

#--trace参数也可以用于调试，还会输出原始的二进制数据
curl --trace - https://www.example.com
```

### 21. 指定 HTTP 请求的代理

```shell
curl -x socks5://james:cats@myproxy.com:8080 https://www.example.com
```

### 22. 指定 HTTP 请求的方法

```shell
curl -X POST https://www.example.com
```

## 23. 取状态码

> 使用-w参数可以直接取

```shell
curl -I -m 2 -o /dev/null -s -w %{http_code} "https://www.example.com"
```
