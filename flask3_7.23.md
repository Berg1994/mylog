[TOC]

flask 微型框架 没有固定的格式，可以全写在一个模块里面，也可以按`MVC`  如

```python
from flask import Flask

app = Flask(__name__)
"""
Flask 类的构造函数只有一个必须指定的参数，即程序主模块或包的名字。在大多数程序 中，Python 的 name 变量就是所需的值。Flask 用这个参数决定程序的根目录，以便稍后能够找到相对于程
序根目录的资源文件位置.
"""

# 构建路由
@app.route('/')
# 找到url 后执行视图
def hello_world():
    return 'Hello World!'


@app.route('/python')
def python():
    return '从入门到住院'


if __name__ == '__main__':
    # __name__=='__main__' 是 Python 的惯常用法，在这里确保直接执行这个脚本时才启动开发
    # Web 服务器。如果这个脚本由其他脚本引入，程序假定父级脚本会启动不同的服务器，因
    # 此不会执行 app.run()
    app.run(debug=True, host='127.0.0.1', port=8081)
"""
所有 Flask 程序都必须创建一个程序实例。Web 服务器使用一种名为 Web 服务器网关接口 （Web Server Gateway Interface，WSGI）的协议，把接收自客户端的所有请求都转交给这 个对象处理。 
"""

```

#### 定义`views.py`

把视图函数单独拿出来，放在一个新建的`viesw.py`文件里，运行函数会无法找到路径

```
@app.route('/')
# 找到url 后执行视图
def hello_world():
    return 'Hello World!'

```

#### `buleprint`

```
因为直接把视图函数移动到其他模块  url无法找到位置
所以需要使用buleprint
1.初始化并管理路由（替换默认的@app.route）
第一个参数是nanespace, 第二个指定当前模块
app_buleprint = Blueprint('first', __name__)


2.绑定蓝图到Flask的对象app上
@app.route('/')
# 找到url 后执行视图
@app_buleprint.rount('/')
def hello_world():
    return 'Hello World!'
    
3.在Flask对象app注册蓝图
app.register_blueprint(app_buleprint)



```

#### 循环引用问题

```
视图和app 相互引用，会报错无法找到模块
```

#### `flask_script`

```
通过Manage 去管理项目 
from flask_script import Manager
manage = Manager(app=app)
manage.run()

运行命令  和django不同 -p 是端口 -d debug -h host   其中-d debug模式在修改代码后保存会立马重启
python hello.py runserver -p 8080 -d -h 0.0.0


```

#### GET/POST

```
#默认是GET请求
@app_buleprint.route('/postname', methods=['POST', 'GET'])
def post_name():
    return 'post请求'
    
在route里面添加参数，可以设置请求的方式，如果只写post请求不设置get请求  访问上面的视图页面是无法访问的

```

### 接收参数类型

string 为默认值，

#### `url/<string:name>`

```
@app_buleprint.route('/postname/<string:name>', methods=['POST', 'GET'])
def get_name(name):
    return '我是：%s' % name
```

#### `url<int:id>`

```python
#如果此处不设置类型 默认为字符串类型 会报错
@app_buleprint.route('/get_id/<id>', methods=['POST', 'GET'])
def get_num(id):
    print(type(id))
    return '数字：%s' % id
    
    
@app_buleprint.route('/get_id/<int:id>', methods=['POST', 'GET'])
def get_num(id):
    print(type(id))
    return '数字：%d' % id
```

#### `url<int:fid>`

```python
@app_buleprint.route('/get_fid/<float:fid>', methods=['POST', 'GET'])
def get_fid(fid):
    print(type(fid))
    return '数字：%.3f' % fid
```

#### request

```
程序收到客户端发来的请求时，要找到处理该请求的视图函数。为了完成这个任务，Flask 会在程序的 URL 映射中查找请求的 URL。URL 映射是 URL 和视图函数之间的对应关系。 Flask 使用 app.route 修饰器或者非修饰器形式的 app.add_url_rule() 生成映射 

flask 不像django 可以直接在函数里传参request 对象 
需要先引用模块 
from flask import request
```

#### 获取request请求参数

```
GET:request.args.get('')
POST:request.from.get('')

验证方式 通过postman 尝试不同方法的提交 通过debug 在variables 添加request
```

#### 设置响应

```
Flask 调用视图函数后，会将其返回值作为响应的内容。大多数情况下，响应就是一个简单的字符串，作为 HTML 页面回送客户端。 但 HTTP 协议需要的不仅是作为请求响应的字符串。HTTP 响应中一个很重要的部分是状 态码，Flask 默认设为 200，这个代码表明请求已经被成功处理。 

导入模块
from flask import Blueprint, request, make_response
#
res = make_response('<h2>我是响应</h2>'，200)
```



#### web框架

```
web.py flask django tornado twisted twisted sanic 多种框架
```