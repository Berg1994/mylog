[TOC]

### urllib

1. request: 最基本的HTTP请求模块，可以用来模拟发送请求，需给库方法传入url 以及额外参数。
2. error: 异常处理模块。
3. parse:提供多种URL处理方法，如拆分，解析，合并。

#### 发送请求

##### urlopen()

​	 urlopen 包含了url, data=None, timeout=socket._GLOBAL_DEFAULT_TIMEOUT, *, cafile=None, capath=None, cadefault=False, context=None

```
urllib.request模块提供了最基本的构造HTTP请求的方法，利用它可以模拟浏览器的一个请求发起过程。
同时，它带有处理授权验证，重定向，浏览器Cookie及其他功能。
例如：
import urllib.request
response = urllib.request.urlopen('https://www.python.org')
print(response.read().decode('utf-8'))
	这段代码就可以爬去python官网的源代码，之后我们就可以提取想要的链接，图片地址，文本信息

```

```python
"""
打印response类型，会发现它是一个HTTPResponse类型的对象。主要包含了read(),readinto(),getheader(name),getheaders(),fileon()等方法，及msg,version,status,reason,debuglevel,closed等属性
"""
#例如：
import urllib.request
response = urllib.request.urlopen('https://www.python.org')
print(response.status)
print(response.getheaders())
print(response.getheader('Server'))

#得到的结果：
200 #状态码
[('Server', 'nginx'), ('Content-Type', 'text/html; charset=utf-8'), ('X-Frame-Options', 'SAMEORIGIN'), ('x-xss-protection', '1; mode=block'), ('X-Clacks-Overhead', 'GNU Terry Pratchett'), ('Via', '1.1 varnish'), ('Content-Length', '48801'), ('Accept-Ranges', 'bytes'), ('Date', 'Mon, 25 Jun 2018 16:11:46 GMT'), ('Via', '1.1 varnish'), ('Age', '2796'), ('Connection', 'close'), ('X-Served-By', 'cache-iad2142-IAD, cache-nrt6130-NRT'), ('X-Cache', 'HIT, HIT'), ('X-Cache-Hits', '4, 204'), ('X-Timer', 'S1529943106.345792,VS0,VE0'), ('Vary', 'Cookie'), ('Strict-Transport-Security', 'max-age=63072000; includeSubDomains')] #响应的头信息
nginx  #响应头中的Server值
```

##### Request

```
"""
在请求中加入Headers 用Request类来构建
Request类的构造方法 （url, data=None, headers={},origin_req_host=None, unverifiable=False,method=None）
url 用户请求rul 必传参数，其他为可选
headers 参数可通过构造请求时直接构造 也可以通过调用请求实例方法 add_header()添加
添加请求头 主要用法是通过修改User-Agent 来伪装浏览器，
如： header = {
        'User-Agent': ' Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.139 Safari/537.36'
    }
method 是一个字符串，写请求方法 如POST GET
"""

import urllib.request
request = urllib.request.Request('https://python.org')
response = urllib.request.urlopen(request)
print(response.read().decode('utf-8'))
```

##### ulrparse()

```
url, scheme='', allow_fragments=True
urlstring 是必填 要解析的URL
scheme 默认协议 如果链接没带协议信息 就会使用默认协议，如果携带 则使用解析的
allow_fragments 是否忽略fragment 
```

该模块定义了处理URL的标志接口，如实现URL各部分的抽取，合并，以及连接转换。如 file,http,https等（后面协议的URL太多不想打了）

```python
#实现URL的识别和分段
#例如
from urllib.parse import urlparse

result = urlparse('http://baidu.com/index.html;user?id=5#comment')
print(type(result),result)
#输出结果：
<class 'urllib.parse.ParseResult'> ParseResult(scheme='http', netloc='baidu.com', path='/index.html', params='user', query='id=5', fragment='comment')
"""
这里返回了一个ParseResult类型对象，包含了6个部分
scheme 协议
netloc 域名
path 访问路径
params 参数
query 搜索条件
一般用作GET类型的URL
#后面是锚点

"""
```

