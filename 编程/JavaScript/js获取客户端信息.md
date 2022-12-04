
#### 网络状态
---

```

#浏览器代号
window.navigator.appCodeName

#浏览器名称
window.navigator.appName

#浏览器版本
window.navigator.appVersion

#启用Cookies
window.navigator.cookieEnabled

#硬件平台
window.navigator.platform

#用户代理
window.navigator.userAgent

#用户代理语言
window.navigator.systemLanguage

#逻辑处理器内核的数量
window.navigator.hardwareConcurrency

#网络是否连接
window.navigator.onLine

#网络连接状态NetworkInformation对象，包括.downlink（网络下行速度）  effectiveType（网络类型） onchange（有值代表网络状态变更） rtt（估算的往返时间） saveData（打开/请求数据保护模式）
window.navigator.connection

#语言 
#"zh-CN"
window.navigator.language
#["zh-CN", "zh"]
window.navigator.languages

#网页可见区域宽： 
document.body.offsetWidth （包括边zhi线的宽）

#网页可见区域高： 
document.body.offsetHeight （包括边线的高）

#网页正文全文宽： 
document.body.scrollWidth

#网页正文全文高： 
document.body.scrollHeight

#网页被卷去的高： 
document.body.scrollTop

#网页被卷去的左： 
document.body.scrollLeft

#网页正文部分上： 
window.screenTop

#网页正文部分左： 
window.screenLeft

#屏幕分辨率的高：
window.screen.height

#屏幕分辨率的宽： 
window.screen.width

#屏幕可用工作区高度： 
window.screen.availHeight

#屏幕可用工作区宽度： 
window.screen.availWidth

#以下是获取浏览器当前窗口视图区域（也就是说不包括工具栏、滚动条等）的实际宽高（旧的HTML标准是写作document.body.clientWidth/clientHeight）
document.documentElement.clientWidth 
document.documentElement.clientHeight 
```
