[TOC]

#### `urllib.parse`

```python
#处理参数或者url的
#处理字典内容，将键值转换为query_string格式，并实现url编码
urllib.parse.quote()
#如：
b_url = 'https://www.baidu.com/s?wd=周杰伦'
str1 = parse.quote(b_url)
https%3A//www.baidu.com/s%3Fwd%3D%E5%91%A8%E6%9D%B0%E4%BC%A6
#会把符号汉字都编译


urllib.parse.unquote()
#如：
a_url = 'https://www.baidu.com/s?wd=%E5%91%A8%E6%9D%B0%E4%BC%A6'
str = parse.unquote(b_url)
https://www.baidu.com/s?wd=周杰伦
#把百分号编码解析成汉字

urllib.parse.urlenconde()
# 把字典解析成query_string，不能解析url  即字典格式转换成 wd=周杰伦
data = {
    'wd': '周杰伦'
}
str = parse.urlencode(data)
wd=%E5%91%A8%E6%9D%B0%E4%BC%A6
```

####`urllib.request`

```python
# 通过拼接方式  解析参数 实现完整的url
import ssl
import urllib.request

ssl._create_default_https_context = ssl._create_unverified_context

url = 'http://www.baidu.com/'
# 如何定制UA
# 在这个头部不仅可以定制ua，还可以定制其他的请求头部，一般只需要定制ua
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36'
}
# 构建请求对象
request = urllib.request.Request(url=url, headers=headers)
# 发送请求，直接打开这个请求对象即可
response = urllib.request.urlopen(request)
a = response.read().decode()
print(a)
```

#### 模拟各种请求方式

```python
get
url = 'https://www.baidu.com/s?'
#让用户输入关键字
key_word = input('请输入搜索内容:')
#get 参数
data = {

    'wd': key_word,

}

# 查询字符集
query_string = urllib.parse.urlencode(data)
print(query_string)

urls = url + query_string
print(urls)

#向url 发送请求  返回响应 写入文件
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.3',
}

request = urllib.request.Request(urls, headers=headers)
#
response = urllib.request.urlopen(request)

filename = key_word + '.html'

with open(filename, 'wb') as f:
    f.write(response.read())



post

url = 'http://fanyi.baidu.com/sug'

data = {
    'kw': 'warcraft'
}
# 通过urlencode 转成query_string 在进行编译转成二级制数据格式
form_data = urllib.parse.urlencode(data).encode('utf-8')
print(form_data)


headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.3',
}
#获取请求
request = urllib.request.Request(url, headers=headers)
#获取响应是通过提交请求和参数 并且需要是二进制 所以需要先编译
response = urllib.request.urlopen(request,form_data)

print(response.read().decode('utf-8'))



ajax-get
url = 'https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&'

page = int(input('请输入页数:'))
limit = int(input('请输入爬取多少行：'))

start = (page - 1) * 50

data = {
    'start': start,
    'limit':limit
}

query_string = urllib.parse.urlencode(data)
print(query_string)

url = url + query_string
print(url)
headers = {
	'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36',
}


request = urllib.request.Request(url,headers=headers)
response = urllib.request.urlopen(request)

print(response.read().decode('utf-8'))

ajax-post


```

#### get_params

```python
# 运行爬取
def main():
    tieba = input('请输入贴吧名:')
    start_page = int(input('请输入开始页数：'))
    end_page = int(input('结束页数:'))
    url = 'https://tieba.baidu.com/f?'

    for page in range(start_page, end_page + 1):
        #通过运行请求方法获取请求
        request = handle_request(page, tieba, url)
        #通过下载方法获取页面并保存
        down_load(request, tieba, page)
        #高频率的爬取容易被服务器封ID  
        time.sleep(3)


def down_load(request, tieba, page):
    #通过发送请求获取响应
    res = urllib.request.urlopen(request)
    #设置文件夹名称
    dirname = tieba
    #避免循环多次创建文件夹报错
    if not os.path.exists(dirname):
        os.mkdir(dirname)
    #设置文件名称格式
    filename = '第%s页.html' % page
    #设置文件路径
    filepath = os.path.join(dirname, filename)
    #写入页面内容
    with open(filepath, 'wb') as f:
        f.write(res.read())


def handle_request(page, tieba, url):
    #根据页面url规律 得到pn
    pn = (page - 1) * 50
    data = {
        'tieba': tieba,
        'pn': pn,
        'ie': 'utf8'
    }
    #拼接url
    url += urllib.parse.urlencode(data)
    #请求头
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36',
    }
    #请求对象
    request = urllib.request.Request(url=url, headers=headers)
    return request


if __name__ == '__main__':
    main()

```

