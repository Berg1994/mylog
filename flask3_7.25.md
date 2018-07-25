[TOC]

### `jinja2`

```
Flask中使用jinja2模板引擎

其优点：

速度快，被广泛使用

HTML设计和后端python分离

非常灵活，快速和安全

提供了控制，继承等高级功能
```

#### 模板语法

```python
#模板语法主要分为两种：变量和标签

#模板中的变量：{{ var }}
#特性：1.视图传递给模板的数据 2.前面定义出来的数据 3.变量不存在，默认忽略
"""
模板中的标签：{% tag %}
特性：1.控制逻辑 2.使用外部表达式 3.创建变量 4.宏定义
"""
{% for i in list1 %}
    {% if i%2 == 0 %}
        <P style="color: red">{{ i }}</P>
    {% else %}
        <P style="color: green">{{ i }}</P>
    {% endif %}
{% endfor %}

 #{{  }}和{% %}这两类标签分别用来包含变量和表达式
    
#1. 对于传入的变量 可以使用 {{obj.prop}} {{obj[“prop”]} 来获取属性
#2. 对变量赋值 如：
{% set name = 'Hai feisi' %}  
{{ name }}



```

#### 模板变量过滤

变量可以通过过滤器进行修改，变量和过滤器中间用|进行分隔,使用的基本格式是{{变量|过滤器1|过滤器2}}，`jinja2`内置了很多过滤器，通过这些内置过滤器，可以进行变量的修改，内置过滤器可以参考 http://docs.jinkan.org/docs/jinja2/templates.html#builtin-filters ，比如我们要把变量转成大写，可以用upper过滤器

![](F:\笔记\imgs\TIM截图20180725193838.png)

![1532519123097](F:\笔记\imgs\1532519049530.png)

运行结果：                                          ![1532519151156](F:\笔记\imgs\1532519151156.png) 

函数视图中故意在`p2` 中加空格，发现在页面中，变量的空格也算成长度了，避免错打空格找不到值





#### 模板继承

```html
#母模板挖坑，子模板继承填坑 类似django

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>
        {% block title %}

        {% endblock %}
    </title>
    {% block extCSS %}

    {% endblock %}

</head>
<body>
{% block content %}

{% endblock %}

{% block extJS %}

{% endblock %}
</body>
</html>


```

#### 与`django`模板区别

```
django 子模板 {% block.super() %}
jinja2 字幕版继承 {% super() %}
```

### 模板

```
raw sql 原生语句
```

### 数据库

#### 选择数据库框架

```markdown
> 以下内容摘自Flask+Web 开发：基于Python的Web应用 书籍

SQLAlchemy 是一个数据库抽象层代码包，使用这哥抽象包直接处理高等级的 Python 对象，而不用处理如表、
文档或查询语言此类的数据库实体。
选择数据库框架的选择因素

1.易用性
如果直接比较数据库引擎和数据库抽象层，显然后者取胜。抽象层，也称为对象关系
映射（Object-Relational Mapper，ORM）或对象文档映射（Object-Document Mapper，
ODM），在用户不知觉的情况下把高层的面向对象操作转换成低层的数据库指令。

2.性能
ORM 和 ODM 把对象业务转换成数据库业务会有一定的损耗。大多数情况下，这种性
能的降低微不足道，但也不一定都是如此。一般情况下，ORM 和 ODM 对生产率的提
升远远超过了这一丁点儿的性能降低，所以性能降低这个理由不足以说服用户完全放弃
ORM 和 ODM。真正的关键点在于如何选择一个能直接操作低层数据库的抽象层，以
防特定的操作需要直接使用数据库原生指令优化。

3.可移植性
选择数据库时，必须考虑其是否能在你的开发平台和生产平台中使用。例如，如果你打
算利用云平台托管程序，就要知道这个云服务提供了哪些数据库可供选择。可移植性还
针对 ORM 和 ODM。尽管有些框架只为一种数据库引擎提供抽象层，但其
他框架可能做了更高层的抽象，它们支持不同的数据库引擎，而且都使用相同的面向对
象接口。SQLAlchemy ORM 就是一个很好的例子，它支持很多关系型数据库引擎，包
括流行的 MySQL、Postgres 和 SQLite。

4.FLask集成度
选择框架时，你不一定非得选择已经集成了 Flask 的框架，但选择这些框架可以节省
你编写集成代码的时间。使用集成了 Flask 的框架可以简化配置和操作，所以专门为
Flask 开发的扩展是你的首选。
```

#### 如何使用`Flask-SQLAlchemy`

SQLAlchemy 是一个很强大的关系型数据库框架，支持多种数据库后台。SQLAlchemy 提
供了高层 ORM，也提供了使用数据库原生 SQL 的低层功能。

在 Flask-SQLAlchemy 中，数据库使用 URL 指定

| 数据库引擎       |                                                  |
| ---------------- | ------------------------------------------------ |
| MySQL            | mysql://username:password@hostname/database      |
| Postgres         | postgresql://username:password@hostname/database |
| SQLite（Unix）   | sqlite:////absolute/path/to/database             |
| SQLite（Windows) | sqlite:///c:/absolute/path/to/database           |



我们通过sqlalchemy 创建实例对象db。表示程序使用的数据库，同时还获得了 Flask-SQLAlchemy
提供的所有功能。

模型这个术语表示程序使用的持久化实体。在 ORM 中，模型一般是一个 Python 类，类中
的属性对应数据库表中的列。

Flask-SQLAlchemy 创建的数据库实例为模型提供了一个基类以及一系列辅助类和辅助函
数，可用于定义模型的结构。

如：

```python
class Student(db.Model):
    id = db.Column(db.INTEGER, primary_key=True, autoincrement=True)
    s_name = db.Column(db.String(10), nullable=False)
    s_age = db.Column(db.INTEGER, nullable=True)
    s_create_time = db.Column(db.DateTime)
    # 如果不设置  默认就是Student  django 是app_tablename
    __tablename__ = 'student'
```

db.Column 类构造函数的第一个参数是数据库列和模型属性的类型,类型如 Integer,Float,String，选项如：primary_key，unique，nullable等

#### 对数据库的CRUD

通过数据库会话管理对数据库所做的改动

```python
#在 Flask-SQLAlchemy 中，会话由 db.session表示。准备把对象写入数据库之前，先要将其添加到会话中

#如 ： db.session.add(stu) 或 db.session.add_all（stu_s_name,stu_s_age）

#同理的还有db.session.delete(stu)  即删除会话中的对象

#为了把对象写入数据库，我们要调用 commit() 方法提交会话：

#db.session.commit()
#以上内容解释为什么我们需要使用db.session.add（）等方法来CRUD数据库

#所以一个完整的添加数据的视图函数如下：

@app_blueprint.route('/createstu/', methods=['POST', 'GET'])
def create_stu():
    if request.method == 'GET':
        return render_template('createstu.html')
    if request.method == 'POST':
        s_name = request.form.get('s_name')
        s_age = request.form.get('s_age')

        stu = Student()
        stu.s_name = s_name
        stu.s_age = s_age
        stu.s_create_time = datetime.now()

        # 保存
        db.session.add(stu)
        db.session.commit()
        return '创建数据成功'
```

####数据库配置

```python
#配置类可以定义 init_app() 类方法，其参数是程序实例。在这个方法中，可以执行对当前
#环境的配置初始化。

app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:123456@127.0.0.1:3306/flask3'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

"""
此处有个小坑 即 配置需写在Sqlalchemy()，Session()等实例在绑定Flask初始化代码的上面  即：
"""
from app.models import db
app = Flask(__name__,
            template_folder=template_dir)
se = Session()
app.config['SESSION_TYPE'] = 'redis'
# 访问redis
app.config['SESSION_REDIS'] = redis.Redis(host='127.0.0.1', port=6379)
#session的实例需要获取数据库的配置
se.init_app(app)
# 定义前缀
# app.config['SESSION_KEY_PREFIX'] = 'flask'
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:123456@127.0.0.1:3306/flask3'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
#因为db初始化需要读取上面2个配置 所以必须写在两个配置后面
db.init_app(app)

#如果在config上面init__app() 是无法读取配置的
```

#### CRUD

```python
增
db.session.add()
db.session.commit()

查
Student.query.all() -->list
Student.query.filter(Student.id == id) --->basequery
Student.query.filter(Student.id == id).all()

Student.query.filter_by(id=id)

删
db.session.delete(对象)
db.seesion.commit()

改
对象 = 模型.query.get(id)
对象.字典 == 字段值

db.session.add(对象)
db.session.commit()

模型.query.filter().update({'':''})
db.session.commit()


```

#### 用户CRUD

#####model

```python
from Flask-sqlalchemy import SQLAlchemy
#导入模块

db = SQLAlchemy()
#创建实例

class UserModel(db.Model)：
	#主键 自增
	id = db.Column(db.Integer, primary_key=True,autoincrement=True)
    #名字
    username = db.Column(db.String(10),nullable=Flase)
    #年龄
    age = db.Column(db.Integer,nullable=True)
    #创建时间
    u_create_time = db.Column(db.Datetime)
    
    #设置表名，默认则不变，但没有复数形式。区别于django的<appname>_<tablename>
    __tablename__ = 'users'
  
```

##### views

###### 增

```python
from flask import Blueprint, redirect,url_for,request,render_template,session
#创建蓝图实例 并在Flask实例下的模块注册蓝图
app_blueprint = Blueprint('first',__name__)

#增
@app_blueprint.route('/create_user/',methods=['POST','GET'])
def create_user():
    #判断请求方式
    if request.method == 'GET':
        return render_template('create_user.html')
    if request.method == 'POST':
        username = request.form.get('username')
        age = request.form.get('age')
        #创建实例
        user = UserModel()
        user.username = username
        user.age = age
        user.u_create_time = datetime.now()
        #通过数据库会话管理 添加实例
        db.session.add(stu)
        #提交会话
        db.session.commit()
        return '创建成功'
```

###### 查

```python
@app_blueprint.route('/select_user/',methods=['GET'])
def select_user():
    #查询所有用户
    users = UserModel.query.all()
    #返回用户信息到用户页面
    return render_template('user.html',users=users)

#查询具体用户 
@app_blueprint.route('/detail_user/<int:id>/',methods=['GET'])
def detail_user(id)
#过滤方法1
user = UserModel.query.filter(UserModel.id == id)
#过滤方法2
user = UserModel.query.filter_by(id = id)
#过滤方法3 此法在没有获取数据时不会报错
user = UserModel.query.get(id)

return render_template('user.html',user=user)
```

###### 改

```python
@app_blueprint.route('/update_user/<int:id>/',methods=['PATCH'])
def update_user():
    username = request.form.get('username')
    #方法1
    user = UserModel.query.get(id)
    user.username = username
    
    #方法2
    UserModel.query.filter_by(id=id).update(['usename':username])
    UserModel.query.filter(UserModel.id == id).update(['username':username])
    
    db.session.commit()
    return '修改成功'

```

###### 删

```python
@app.blueprint.route('/del_user/<int:id>',methods=['DELETE'])
def del_user(id):
    user = User.query.get(id)
    db.session.delete(user)
    db.session.commit()
    return '删除成功'
```

