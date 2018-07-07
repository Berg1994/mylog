1. flask

用于开发web的微框架,有很多的拓展包

所有 Flask 程序都必须创建一个程序实例。Web 服务器使用一种名为 Web 服务器网关接口 （Web Server Gateway Interface，WSGI）的协议，把接收自客户端的所有请求都转交给这 个对象处理。 

pip install flask_script

pip install flask

2. 创建虚拟环境

安装flask pip install flask

项目 hello.py

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
    
if __name = '__main__'
	app.run()
	
	
	Flask 类的构造函数只有一个必须指定的参数，即程序主模块或包的名字。在大多数程序 中，Python 的 name 变量就是所需的值。
	
    __name__=='__main__' 是 Python 的惯常用法，在这里确保直接执行这个脚本时才启动开发
Web 服务器。如果这个脚本由其他脚本引入，程序假定父级脚本会启动不同的服务器，因
此不会执行 app.run()
```

3. 启动项目

python hello_world.py runserver 

4. 修改启动方式

app.run(debug=Ture, port=8080, host='0.0.0.0')

5. 修改启动方式

pip install flask_script

from flask_script import Manager

manage = Manager(app=app)

启动项目

​	manage.run()

启动命令 python hello.py runserver -p 8000 -h 0.0.0.0 -d

6. 路由规则

string(默认)

int

float

path

uuid



7. blueprint 蓝图

127.0.0.1:8080/user/login/
127.0.0.1:8080/user/register/

127.0.0.1:8080/axf/home/
127.0.0.1:8080/axf/mine/

下午

pip install flask_blueprint

用蓝图来管理路由

项目拆分

```
Flask(__name__)
```

8. 指定static目录，和templates目录地址的

Flask(__name__, 
	static_folder='static',
	templates_fodler='templates'
	)

​	

9. request

程序收到客户端发来的请求时，要找到处理该请求的视图函数。为了完成这个任务，Flask 会在程序的 URL 映射中查找请求的 URL。URL 映射是 URL 和视图函数之间的对应关系。 Flask 使用 app.route 修饰器或者非修饰器形式的 app.add_url_rule() 生成映射 

post请求： request.form

方法

get请求 ：request.args



10. url_for

url_for('蓝图第一个参数.函数名')



11.响应

Flask 调用视图函数后，会将其返回值作为响应的内容。大多数情况下，响应就是一个简 单的字符串，作为 HTML 页面回送客户端。 但 HTTP 协议需要的不仅是作为请求响应的字符串。HTTP 响应中一个很重要的部分是状 态码，Flask 默认设为 200，这个代码表明请求已经被成功处理。 

12. 渲染

Flask 提供的 render_template 函数把 Jinja2 模板引擎集成到了程序中。render_template 函 数的第一个参数是模板的文件名。随后的参数都是键值对，表示模板中变量对应的真实 值。在这段代码中，第二个模板收到一个名为 name 的变量。 

```
from flask import Flask, render_template
# ...
@app.route('/')
def index():
 return render_template('index.html')
@app.route('/user/<name>')
def user(name):
 return render_template('user.html', name=name)
```



