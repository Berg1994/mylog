[TOC]

#### 配置代理池

代理池可以是一个文本，把可能可用的`ip`地址放在里面用于读取  如：

```
218.60.8.98:3129
122.72.18.34:80
124.235.208.252:443
182.88.178.229:8123
```

#####`request`请求实例

```python
url = 'http://www.baidu.com/#ie=UTF-8&wd=ip'
header = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36'
}
urllib.request.Request(url=url,request=request)

```

##### 读取文件

```python
f = open('pool.txt','r')
string = f.read()
f.close()
#文本是一个整字符串 需要对字符串切割
ip = string.splitlines()
```

##### 配置代理 验证可用性

```python
while True:
    #从列表中随机选择一个代理
    daili = random.choice(lt)
    #发送请求
    proxy = {'http':daili}
    print(proxy)
    #创建handler  和opener一起使用
    handler = urllib.request.ProxyHandler(proxies=proxy)
    #创建opener
    opener = urllib.request.build_opener(handler)

    try:
        response = opener.open(request)
        print('使用代理%s成功' % daili)
        with open('ip.html','wb') as f:
            f.write(response.read())
        break
    except Exception as e:
        #如果报错说明不可用， 移除Ip
        lt.remove(daili)
        print('使用代理%s失败' % daili)
    time.sleep(3)

```



#### 使用阿布云代理

使用付费代理，通过把账户和密码放在请求头，把需要访问的`url`作为请求地址 配置成请求对象，再通过构建handler 配置代理 此处代理`ip`换成阿布云的`url`

通过把请求发给代理服务器 判定你的用户可用，再随机选取`ip`帮你访问并返回结果

```python
user = ""
pwd = ''

#将用户名和密码拼接再转换格式
string = user + ':' + pwd
#进行base64编码
ret = 'Basic' + base64.b64enconde(string.encode('utf-8')).decode('utf-8')

url = 'http://www.baidu.com/s?ie=UTF-8&wd=ip'

header = {
    'Proxy-Authorization': ret
}

#构建请求对象
request = urllib.request.Request(url=url,headers=headers)

handler = urllib.request.ProxyHandler('http':'http-dyn.abuyun.com:9020')
opener = urllib.request.build_opener(handler)
r = opener.open(request)
with open('ip.html','wb') as f:
    f.write(r.read())
    
```

#### 模拟登陆

通过代码直接登录页面，一般分为2种， 先通过浏览器登录获取cookie，把cookie 写在在请求头里发送请求。

另一种就是创建`cookiejar`对象，用来保存cookie，在创建handler,之后用opener.open发送 就带有cookie

```python
import urllib.request
import urllib.parse

import http.cookiejar

# 创建对象
ck = http.cookiejar.CookieJar()
# 根据ck 创建handler
handler = urllib.request.HTTPCookieProcessor(ck)
opener = urllib.request.build_opener(handler)

post_url = 'http://www.renren.com/ajaxLogin/login?1=1&uniqueTimestamp=20187410296'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36',
}
request = urllib.request.Request(url=post_url, headers=headers)

formdata = {
    'email': '',  # 账户
    'password': '',  # 密码
    'icode': '',
    'origURL': 'http://www.renren.com/home',
    'domain': 'renren.com',
    'key_id': '1',
    'captcha_type': 'web_login',
    'f': 'https%3A%2F%2Fwww.baidu.com%2Flink%3Furl%3DVwDXbx3oN5RBHzxzj2jwbsO3z8VmHcZ1HZQTdC3enq%26wd%3D%26eqid%3D834642bf0000b410000000055b6bac87',
}
# 因为是post 请求 把字典转换成query_string 格式 并编译成二进制
formdata = urllib.parse.urlencode(formdata).encode('utf-8')

r = opener.open(request, data=formdata)

# 登录成功后

get_url = 'http://www.renren.com/960481378/profile'

request = urllib.request.Request(url=get_url,headers=headers)

r = opener.open(request)

with open('renren.html','wb') as f:
    f.write(r.read())
```

#### 正则复习

##### 使用`re.compile`

```python
re模块中包含一个重要函数是compile(pattern [, flags]) ，该函数根据包含的正则表达式的字符串创建模式对象。可以实现更有效率的匹配。在直接使用字符串表示的正则表达式进行search,match和findall操作时，python会将字符串转换为正则表达式对象。而使用compile完成一次转换之后，在每次使用模式的时候就不用重复转换。当然，使用re.compile()函数进行转换后，re.search(pattern, string)的调用方式就转换为 pattern.search(string)的调用方式。

import re

string = 'i love you very much'

pattern = re.compile(r'love')

# print(pattern)
ret1 = pattern.search(string)
ret2 = pattern.match(string)
ret3 = pattern.findall(string)

print(ret1,ret2,ret3)

# print(ret.group()) #group 查看匹配部分
# print(ret.span()) #查看匹配内容下标位置

```

##### 不适用`re.compile`

```python
在进行search,match等操作前不使用compile函数，会导致重复使用模式时，需要对模式进行重复的转换。降低匹配速度。而此种方法的调用方式，更为直观。
import re

string = 'i love you very much'

a = re.findall(r'love',string)
# ['love']
b = re.search(r'love',string)
#  <_sre.SRE_Match object; span=(2, 6), match='love'>
c = re.match(r'love',string)
# None
d = re.search(r'love',string).group()
# love
e = re.search(r'love',string).span()
# (2, 6)

```

##### 匹配模式

```markdown
1).re.I(re.IGNORECASE): 忽略大小写 
2).re.M(MULTILINE): 多行模式，改变'^'和'$'的行为 
3).re.S(DOTALL): 点任意匹配模式，改变'.'的行为 
4).re.L(LOCALE): 使预定字符类 \w \W \b \B \s \S 取决于当前区域设定 
5).re.U(UNICODE): 使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性 
6).re.X(VERBOSE): 详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释
```

##### 常用方法

```
'''
# 贪婪模式
string = '<div>啦啦啦啦啦啦，我是卖报的小行家</div></div></div></div>'
pattern = re.compile(r'<div>(.*?)</div>')
ret = pattern.search(string)

print(ret.group(1))
'''

'''
# 忽略大小写
string = 'love is a forever topic'
pattern = re.compile(r'LOVE', re.I)

ret = pattern.search(string)

print(ret.group())
'''
"""
# 多行模式
string = '''细思极恐
你的对手在看书
你的敌人在磨刀
你的闺蜜在减肥
隔壁老王在炼腰
'''
pattern = re.compile(r'^你的', re.M)
ret = pattern.search(string)

print(ret.group())
"""

# 单行模式
string = '''<div>沁园春-雪
北国风光，千里冰封，万里雪飘
望长城内外，惟余莽莽
大河上下，顿失滔滔
</div>'''
pattern = re.compile(r'<div>(.*?)</div>', re.S)
ret = pattern.search(string)
print(ret.group(1))
```

