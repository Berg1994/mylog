[TOC]

#### 引用模块

#### `重定向和错误 abort`

用 [`redirect()`](http://docs.jinkan.org/docs/flask/api.html#flask.redirect) 函数把用户重定向到其它地方。放弃请求并返回错误代码，用 [`abort()`](http://docs.jinkan.org/docs/flask/api.html#flask.abort) 函数。 

```python
@app_blueprint.route('/geterror/')
def get_error():
    try:
        3 / 0
    except Exception as e:
        abort(400)

    return '计算完成'

#定制错误页面， 可以使用 errorhandler() 装饰器
@app_blueprint.errorhandler(400)
def handler(exception):
    return '捕获异常： %s' % exception
```

#### `url_for`

```python
from flask import Blueprint, redirect, url_for, abort, request, render_template, session

app_blueprint = Blueprint('first', __name__)

"""
用 url_for() 来给指定的函数构造 URL。它接受函数名作为第一个参数，也接受对应 URL 规则的变量部分的命名参数。未知变量部分会添加到 URL 末尾作为查询参数。
"""

@app_blueprint.route('/')
def hello():
    return 'hello'


@app_blueprint.route('/getredirect/')
def get_redirect():
    #
    # return redirect('/app/')
    # url_for('初始化蓝图的第一个参数，函数名')
    return redirect(url_for('first.hello'))
"""
上述视图函数 从定向到了hello（）函数， 也可以用注释的代码实现相同功能，但是为什么要构建url 而不是写硬编码
原因如下：
1.反向构建通常比硬编码的描述性更好。更重要的是，它允许你一次性修改 URL， 而不是到处边找边改。
2.URL 构建会转义特殊字符和 Unicode 数据，免去你很多麻烦。
3.如果你的应用不位于 URL 的根路径（比如，在 /getredirect 下，而不是 /app ）， url_for() 会妥善处理这个问题。
"""


```

#### 创建`session`实例，配置`redies`

```python
from flask-session import Session

# 第一种
se = Session()

#第二种
# Session(app=app)

#配置redis
app.config['SESSION_TYPE'] = 'redis'
app.config['SESSION_REDIS'] = redis.REDIS(host=127.0.0.1,port=6379)
# 定义前缀
# app.config['SESSION_KEY_PREFIX'] = 'flask'
#初始化
se.init_app(app)
```

#### 删除`Cookie`

```python
#方法1.直接清空Cookie
@blue.route('/setcookie/')
def set_cookie():
    temp = render_template('index.html')
    response = make_response(temp)
    response.set_cookie('name','',expires=0)
    return response
	
#方法2. del_cookie()，配置cookie 用的是set_cookie()
@blue.route('/setcookie/')
def set_cookie():
    temp = render_template('index.html')
    response = make_response(temp)
	
    response.delete_cookie('name')
    return response
#方法3. set_cookie('key','',expires=0) 重新设置key的值为空，过期时间为0


```

#### 删除`session`

```python
session.pop('key')
#清除所有值
session.clear
```

#### `request请求取值-get\getlist`

```
在视图函数中，args是get请求参数的包装, form是post请求参数的包装

args和form 都是ImmutableMultiDict对象，类字典结构对象，存储方式都是键值对形式

重点：ImmutableMultiDict是类似字典的数据结构，但是与字典的区别是，可以存在相同的键。

在ImmutableMultiDict中获取数据的方式，dict['key']或者dict.get('key')或者dict.getlist('key')

```

#### 在页面定义函数 宏

```html
#Jinja2的宏功能有些类似于传统程序语言中的函数，既然是函数就有其声明和调用两个部分
#要使用宏需要先声明一个宏
#代码中，宏的名称就是”input”，它有三个参数分别是”name”, “type”和”value”，后两个参数有默认值

{% macro input(name, type='text', value='') %}
    <input type="{{ type }}" name="{{ name }}" value="{{ value|e }}">
{% endmacro %}

<p>{{ input('username', value='user') }}</p>
<p>{{ input('password', 'password') }}</p>
<p>{{ input('submit', 'submit', 'Submit') }}</p>
#input 就是函数/宏
#使用宏可以大量编写重复代码

{% macro hello() %}
	<h3>你好</h3>
{% endmacro %}	
{% hello() %}
```

#### request对象

```python
http://docs.jinkan.org/docs/flask/quickstart.html#accessing-request-data
想了解的朋友还是自行阅读官方文档吧。我反正说不清楚，大概就是这里导入的request对象是一个全局的对象，在一个环境作用域，Flask可以只让被响应的视图函数

"""
然后我发现这段宽泛的操作。。。，好像没什么卵用
"""

请求对象
API 章节对请求对象作了详尽阐述（参见 request ），因此这里不会赘述。此处宽泛介绍一些最常用的操作。首先从 flask 模块里导入它:

from flask import request
当前请求的 HTTP 方法可通过 method 属性来访问。通过:attr:~flask.request.form 属性来访问表单数据（ POST 或 PUT 请求提交的数据）。这里有一个用到上面提到的那两个属性的完整实例:

@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # the code below is executed if the request method
    # was GET or the credentials were invalid
    return render_template('login.html', error=error)
当访问 form 属性中的不存在的键会发生什么？会抛出一个特殊的 KeyError 异常。你可以像捕获标准的 KeyError 一样来捕获它。 如果你不这么做，它会显示一个 HTTP 400 Bad Request 错误页面。所以，多数情况下你并不需要干预这个行为。

你可以通过 args 属性来访问 URL 中提交的参数 （ ?key=value ）:

searchword = request.args.get('q', '')
我们推荐用 get 来访问 URL 参数或捕获 KeyError ，因为用户可能会修改 URL，向他们展现一个 400 bad request 页面会影响用户体验。

欲获取请求对象的完整方法和属性清单，请参阅 request 的文档。
```

#### `static`路径/反解析

```
flask：
第一种： /static/css/xxx.css
第二种： {% url_for('static', filename='css/xxx') %}

django:
第一种： /static/js/xxx.js
第二种： {% static 'js/xxx.js' %}
```

#### 购物车添加逻辑

```
以狗东为例，
在没有登录时添加商品，把商品信息添加到cookie中（商品id）
在登录后，讲cookie中的商品信息，添加到用户和购物对应模型(把商品id添加到用户与购物车的关联关系表中)
```

