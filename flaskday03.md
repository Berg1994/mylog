[TOC]



### 跨站请求伪造保护 

默认情况下，Flask-WTF 能保护所有表单免受跨站请求伪造（Cross-Site Request Forgery， CSRF）的攻击。恶意网站把请求发送到被攻击者已登录的其他网站时就会引发 CSRF 攻击。 为了实现 CSRF 保护，Flask-WTF 需要程序设置一个密钥。Flask-WTF 使用这个密钥生成 加密令牌，再用令牌验证请求中表单数据的真伪。 

```python
#设置秘钥
app = Flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'
#app.config 字典可用来存储框架、扩展和程序本身的配置变量
```



### 在视图函数中处理表单 

app.route 修饰器中添加的 methods 参数告诉 Flask 在 URL 映射中把这个视图函数注册为 GET 和 POST 请求的处理程序。如果没指定 methods 参数，就只把视图函数注册为 GET 请求 的处理程序。 

把 POST 加入方法列表很有必要，

1. 因为将提交表单作为 POST 请求进行处理更加便利。

2. 表单 也可作为 GET 请求提交，不过 GET 请求没有主体，提交的数据以查询字符串的形式附加到 URL 中，可在浏览器的地址栏中看到。

   基于这个以及其他多个原因，提交表单大都作为 POST 请求进行处理。 

　### 重定向和用户会话 

​	用户输入名字后提交表单，然后点击浏览器的刷 新按钮，会看到一个莫名其妙的警告，要求在再次提交表单之前进行确认 

因为刷新页面时浏览器会重新发送之前已经发送过的最后一个请求。如果这个 请求是一个包含表单数据的 POST 请求，刷新页面后会再次提交表单 

使用重定向作为 POST 请求的响应，而不是使用常规响应。重定 向是一种特殊的响应，响应内容是 URL，而不是包含 HTML 代码的字符串。浏览器收到 这种响应时，会向重定向的 URL 发起 GET 请求，显示页面的内容。这个页面的加载可能 要多花几微秒，因为要先把第二个请求发给服务器。除此之外，用户不会察觉到有什么不 同。现在，最后一个请求是 GET 请求，所以刷新命令能像预期的那样正常使用了。这个技 巧称为 Post/ 重定向 /Get 模式。 

[url_for] 

```
推荐使用 url_for() 生成 URL，因为这
个函数使用 URL 映射生成 URL，从而保证 URL 和定义的路由兼容，而且修改路由名字后
依然可用。 效果如同django里面的reverse

url_for() 函数的第一个且唯一必须指定的参数是端点名，即路由的内部名字。默认情
况下，路由的端点是相应视图函数的名字。在这个示例中，处理根地址的视图函数是
index()，因此传给 url_for() 函数的名字是 index。

```

#### 使用Flask-SQLAlchemy管理数据库 

抽象层，也称为对象关系 映射（Object-Relational Mapper，ORM）或对象文档映射（Object-Document Mapper， ODM）， 

```
Flask-SQLAlchemy 是一个 Flask 扩展，简化了在 Flask 程序中使用 SQLAlchemy 的操作。
SQLAlchemy 是一个很强大的关系型数据库框架，支持多种数据库后台。SQLAlchemy 提
供了高层 ORM，也提供了使用数据库原生 SQL 的低层功能。

在 Flask-SQLAlchemy 中，数据库使用 URL 指定。最流行的数据库引擎采用的数据库 URL

MySQL mysql://username:password@hostname/database
Postgres postgresql://username:password@hostname/database
SQLite（Unix） sqlite:////absolute/path/to/database
SQLite（Windows） sqlite:///c:/absolute/path/to/database

程序使用的数据库 URL 必须保存到 Flask 配置对象的 SQLALCHEMY_DATABASE_URI 键中
配置对象中还有一个很有用的选项，即 SQLALCHEMY_COMMIT_ON_TEARDOWN 键
将其设为 True时，每次请求结束后都会自动提交数据库中的变动。
```

```python
"""
db 对象是 SQLAlchemy 类的实例，表示程序使用的数据库，同时还获得了 Flask-SQLAlchemy
提供的所有功能。
"""
from flask.ext.sqlalchemy import SQLAlchemy
basedir = os.path.abspath(os.path.dirname(__file__))
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] =\
 'sqlite:///' + os.path.join(basedir, 'data.sqlite')
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
db = SQLAlchemy(app)

```



### 定义模型 

模型这个术语表示程序使用的持久化实体。在 ORM 中，模型一般是一个 Python 类，类中 的属性对应数据库表中的列。 

Flask-SQLAlchemy 创建的数据库实例为模型提供了一个基类以及一系列辅助类和辅助函 数，可用于定义模型的结构。 

```
最常用的SQLAlchemy列类型
类型名 Python类型   说　　明
Integer int        普通整数，一般是 32 位
SmallInteger int   取值范围小的整数，一般是 16 位
BigInteger int 或 long 不限制精度的整数
Float float 浮点数
Numeric decimal.Decimal 定点数
String str 变长字符串
Text str 变长字符串，对较长或不限长度的字符串做了优化
Unicode unicode 变长 Unicode 字符串
UnicodeText unicode 变长 Unicode 字符串，对较长或不限长度的字符串做了优化
Boolean bool 布尔值
Date datetime.date 日期
Time datetime.time 时间
DateTime datetime.datetime 日期和时间
Interval datetime.timedelta 时间间隔
Enum str 一组字符串
PickleType 任何 Python 对象 自动使用 Pickle 序列化
LargeBinary str 二进制文件
```

```
最常使用的SQLAlchemy列选项
选项名 说　　明
primary_key 如果设为 True，这列就是表的主键
unique 如果设为 True，这列不允许出现重复的值
index 如果设为 True，为这列创建索引，提升查询效率
nullable 如果设为 True，这列允许使用空值；如果设为 False，这列不允许使用空值
default 为这列定义默认值
```

##### 常用SQLAlchemy查询过滤器

```
filter() 把过滤器添加到原查询上，返回一个新查询
filter_by() 把等值过滤器添加到原查询上，返回一个新查询
limit() 使用指定的值限制原查询返回的结果数量，返回一个新查询
offset() 偏移原查询返回的结果，返回一个新查询
order_by() 根据指定条件对原查询结果进行排序，返回一个新查询
group_by() 根据指定条件对原查询结果进行分组，返回一个新查询
```





1. 运算符
2. 

语法

filter(model_name.属性名.运算符（）)

contains

startswith

endswith

in_

like

```
__gt__ 大于
__ge__ 大于等于
__lt__ 小于
__le__ 小于等于
```

1. 查询学生的ID在3到15之间的学生信息
2. 查询学生年龄小于19岁的
3. 查询姓名以9结束的学生信息



分页 

运算符

创建班级

一对多关系



学生视图

创建库

删除库

显示学生列表

创建学生

批量创建学生

编辑学生

```
前端发起ajax请求，若是get类型的请求，系统会默认将定义的参数集params以?name=XXX&age=XXX的形式拼接为url地址
：由于是get请求，后台视图函数中看到的还是原生的url地址，但是我们使用request.args可以正常获取参数
所以即便点击获得请求时，URL没有改变 但是值已经获得了
```



查询学生

分页显示