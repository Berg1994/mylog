date: 7.2



[TOC]

### `Scrapy`

```
Scrapy 是一个基于Twisted的异步处理框架，是纯python实现的爬虫框架。
分为以下几个部分：
1.Engine.引擎 处理整个系统的数据流处理，触发事物，是整个框架的核心。
2.Item.项目 定义了爬取结果的数据结构，爬取的数据会被赋值成改Item对象。
3.Scheduler.调度器 接受引擎放过来的请求并将其加入队列中，在引擎再次请求的时候将请求提供给引擎。
4.Downloader. 下载器，下载网页内容，并将网页内容返回给蜘蛛。
5.Item Pipeline. 项目管道，负责处理由蜘蛛从网页中抽取的项目，主要任务是清洗，验证和存储数据。
6.Downloader Middlewares. 下载器中间件， 位于引擎和蜘蛛之间的钩子框架，主要处理蜘蛛输入的响应和输出的结构以及新的请求。

```

#### `创建项目`

```
输入命令：scrapy startproject <projectname>
```

#### `创建spider`

##### `方法1 手动创建`

```
Spider是自定义的类，但这个类必须继承 Scrapy 提供的Spider类 scrapy.Spider 
并且需要定义Spider的名称和起始请求，及爬取结果的处理方法


```

```python
#如手动在spiders 文件夹下创建 douban.py 
class DouBanSpider(spiders.Spider):
    # 启动项目制定的name参数
    name = 'douban'
    # 制定需要爬取的网页
    # start_url = get_all_url()
    start_urls = []
    for i in range(0, 250, 25):
        start_url = 'https://movie.douban.com/top250?start=%s&filter=' % i
        start_urls.append(start_url)

```

##### `方法2 命令创建`

```python
#进入项目review_work
(spider) E:\workspace\spider\day06_review>cd review_work
# maoyan 为spider名称 第二个参数为网站域名
scrapy genspider maoyan http://maoyan.com/

#运行结果
Created spider 'maoyan' using template 'basic' in module:
  review_work.spiders.maoyan

#点击打开maoyan.py
# -*- coding: utf-8 -*-
import scrapy


class MaoyanSpider(scrapy.Spider):
    #name 是每个项目唯一的名字 用以区分不同spider
    name = 'maoyan'
    #允许爬取的域名 如果初始或后续的链接不在这个域名下 则被过滤掉
    allowed_domains = ['http://maoyan.com/']
    #这个是Spider 在启动时爬取的url列表 定义初始请求
    start_urls = ['http://http://maoyan.com//']
	#此方法负责解析返回响应提取数据或进一步生成要处理的请求
    def parse(self, response):
        pass

```

#### `Item` 

```python
"""
item 是保存爬取数据的容器，使用方法和字典类似，但是多了避免拼写错误或定义字段错误的保护机制
创建Item需继承scrapy.Item类，并定义类型为scrapy.Field的字段。

"""
#如：
import scrapy

# 类似项目里面的model模板
class DoubanSpiderItem(scrapy.Item):
    name = scrapy.Field()
    avator = scrapy.Field()
    directors = scrapy.Field()
    rate = scrapy.Field()
    year = scrapy.Field()
    guojia = scrapy.Field()
    classfication = scrapy.Field()
```

#### `解析Response`

```python
"""
在parse()中，参数response 是 start_urls里链接爬取后的结果，所以可以直接对response变量包含的内容解析，如解析源代码，找出结果中的链接得到下一步请求
"""

 # 解析页面源码
    def parse(self, response):
        
        res = Selector(response)
        # 创建项目对象
        items = DoubanSpiderItem()

        # 导航类型
        items['name'] = res.xpath('//*[@id="content"]/div/div[1]/ol/li/div/div[2]/div[1]/a/span[1]/text()').extract()
        # 电影图片
        items['avator'] = res.xpath('//*[@id="content"]/div/div[1]/ol/li/div/div[1]/a/img/@src').extract()

        # 导演和主演
        director_info = res.xpath('//*[@id="content"]/div/div[1]/ol/li/div/div[2]/div[2]/p[1]/text()[1]').extract()
        items['directors'] = [directors.strip().replace('\xa0', '') for directors in director_info]

        # 年份/国家/类型
        movie_info = res.xpath('//*[@id="content"]/div/div[1]/ol/li/div/div[2]/div[2]/p[1]/text()[2]').extract()
        movie_info = [movies.strip().replace('\xa0', '') for movies in movie_info]
        items['year'], items['guojia'], items['classfication'] = [], [], []
        for info in movie_info:
            items['year'].append(info.split('/')[0])
            items['guojia'].append(info.split('/')[1])
            items['classfication'].append(info.split('/')[2])

            # 评分
            items['rate'] = res.xpath(
                '//*[@id="content"]/div/div[1]/ol/li/div/div[2]/div[2]/div/span[2]/text()').extract()

        return items
```



###### `黑科技1 循环爬取下一页`

```
url = response.urljoin(url)
这里的参数url是后面半截地址  使用urljoin方法 可以拼成完整地址 
如：
取得的url = 23422_2.html
则可以完整拼接为 http://www.jj20.com/bz/nxxz/shxz/23422_2.html

yield scrapy.Request(url=url,callback=self.parse)
把url赋值成新的url 再用回调使用prase方法
循环到最后一页 
```

#### `运行`

##### `方法1`

```
输入命令：
scrapy crawl <name>

```

##### `方法2`

```python
#在项目下创建main.py

from scrapy.cmdline import execute
# weibo 为 name
execute(['scrapy', 'crawl', 'weibo']) 
```

#### `中间件`

`pass`

#### `保存`

```
Scrapy 提供 Feed Exports
命令： scrapy crawl <name> -o <filename>.json
如需每个Item输出一行json
命令 ：
scrapy crawl <name> -o <filename>.jl
或
scrapy crawl <name> -o <filename>.jsonlines
输出格式支持很多种 如csv,xml等 及远程输出ftp
格式：需正确配置用户名，密码，地址，输出路径
scrapy crawl <name> -o ftp://user:pass@ftp.example.com/path/to/<filename>.csv


```



#### `保存到数据库（MongoDB）`

###### `思路`

```
Item Pipeline 为项目管道，当Item生成后，会自动被发送到Item Pipline 进行处理。如：
1.清理HTML数据
2.验证爬取数据，检查爬取字段。
3.查重并丢弃重复内容
4.将爬取结果保存到数据库

```

##### ``开启mongodb``

```
查找mongodb 文件所在位置
本机路径： D:\mongoDB\bin
运行db  首次是需要自己建立的
运行mongodb :  D:\mongoDB\bin>mongod -dbpath D:\mongoDB\data\db
```

##### `创建数据库和表`

```
进入bin下运行mongo.exe

查看所有数据库  show dbs 

创建并切换到该数据库 use <dbname>   
#注意此时创建并不能show 查看到 需建立collection

创建表 db.createcollection <name> 

```

#### `实现Item  Pipeline`

```
实现项目管道 需定义一个类并实现process_item（）方法。 
启动项目管道后，会自动调用这个方法， process_item()方法会自动返回包含数据的字典或者Item对象 或者抛出DropItem异常

process_item()方法有2个参数，一个是item, 是Spider生成的Item作为参数传递过来，另一个是spider，是Spider实例

```

##### 配置settings

```python
ITEM_PIPELINES = {
    'weibospider.pipelines.WeibospiderPipeline': 301,
    'weibospider.pipelines.UserCreateTimePipeline': 300,
}
#通过赋值ITEM_PIPELINES 字典，键名为项目管道的类名，键值数值越小 ，调用优先度度越高 所以先运行值为300的类
```





##### `写入mongoDB数据库`

```python
import pymongo
from scrapy.conf import settings

class DoubanSpiderPipeline(object):

    def __init__(self):
        conn = pymongo.MongoClient(host=settings['MONGODB_HOST'], port=settings['MONGODB_PORT'], )
        db = conn[settings['MONGODB_DB']]
        self.collection = db[settings['MONGODB_COLLECTION']]

    def process_item(self, item, spider):
        for i in range(len(item['name'])):
            data = {}
            data['name'] = item['name'][i]
            data['avator'] = item['avator'][i]
            data['directors'] = item['directors'][i]
            data['year'] = item['year'][i]
            data['guojia'] = item['guojia'][i]
            data['classfication'] = item['classfication'][i]
            data['rate'] = item['rate'][i]

            self.collection.insert(data)

        return item
```









