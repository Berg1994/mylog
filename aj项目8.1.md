[TOC]

##`Flask项目爱家`

#### 建立项目框架

1. 构建虚拟环境

```python
vritualenv --no-site-package aj
```

2.在虚拟环境下创建项目文件夹

```
mkdir aj
```

3.创建虚拟环境安装包

```
在项目下创建文件夹 requeirement
在文件夹下创建安装包的文本  re_install.txt
把需要配置的环境内容写入  如：
flask
flask-script
flask-blueprint
flask-sqlalchemy
pymysql
redis
flask-debugtoolbar
flask-session


在当前文件夹路径下运行命令： pip install -r re_install.txt
```



####  按`MVC`模式对项目部署

#### `manage.py`

```python
这里只展示启动内容
通过调用模块获取创建对象方法  获取对象后运行项目
from flask_script import Manager

from utils.app import create_app

# 这里只需要展示启动内容
app = create_app()

manager = Manager(app)

if __name__ == '__main__':
    manager.run()

```

##### 创建`utils`文件夹

#####  `app.py`

```python
"""
app.py 内实现创建Flask对象 ，确认模板和静态文件的位置， 设置数据库配置， 对各个模块的实例初始化
"""
#实现一个创建flask对象的方法
def create_app():

    app = Flask(__name__,
                static_folder=static_folder,
                template_folder=template_folder)

    app.register_blueprint(user_blueprint, url_prefix='/user')

    # 设置数据库配置
    app.config['SQLALCHEMY_DATABASE_URI'] = get_sqlalchemy_uri(MYSQL_DATABASE)
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

    # 配置redis-->session
    app.config['SESSION_TYPE'] = 'redis'
    app.config['SESSION_REDIS'] = redis.Redis(host=REDIS_DATABASE['HOST'],
                                              port=REDIS_DATABASE['PORT'])

    app.config['SECRET_KEY'] = 'secret_key'
    app.debug = True
	#对需要绑定的实例进行初始化
    db.init_app(app)
    se = Session()
    se.init_app(app)
    bar = DebugToolbarExtension()
    bar.init_app(app)

    return app
```

##### `settings.py`

```python
"""
用于配置数据库的具体信息， 及各个文件的路径，类似django的settings
"""

import os
#设置基础路径
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
#根据基础路径配置静态资源路径
static_folder = os.path.join(BASE_DIR, 'static')
#模板路径
template_folder = os.path.join(BASE_DIR, 'templates')
#mysql数据库配置
MYSQL_DATABASE = {
    'USER':'root',
    'PASSWORD':'123456',
    'HOST':'127.0.0.1',
    'PORT':3306,
    'DB':'aj3',
    'ENGINE':'mysql',
    'DRIVER':'pymysql'
}

REDIS_DATABASE = {
    'HOST':'127.0.0.1',
    'PORT': 6379
}

```

##### `status_code.py`

```python
"""
配置状态码 
用于匹配视图函数返回响应时的状态码，并在函数错误时，返回相应的值
"""
#如：

SUCCESS = {'code': 200, 'msg': '请求成功'}
DATABASE_ERROR = {'code': 500, 'msg': '数据库崩了'}

# 用户模块
USER_REGISTER_CODE_ERROR = {'code': 1000, 'msg': '验证码错误'}
USER_REGISTER_PARAMS_VALID = {'code': 1001, 'msg': '请填写完整的注册参数'}
USER_REGISTER_MOBILE_INVALID = {'code': 1002, 'msg': '手机格式不正确'}
USER_REGISTER_PASSWORD_ERROR = {'code': 1003, 'msg': '两次密码不正确'}
USER_REGISTER_MOBILE_EXSIST = {'code': 1004, 'msg': '手机号已存在，请登录'}

USER_LOGIN_PARAMS_VALID = {'code': 1005, 'msg': '请填写完整的登录信息'}
USER_LOGIN_PASSWORD_INVALID = {'code': 1006, 'msg': '登录密码不正确'}
USER_LOGIN_PHONE_INVALID = {'code': 1007, 'msg': '请填写正确的手机号'}
```

##### `functions.py`

```python
#配置数据库方法，在app.py直接使用方法获取值
def get_sqlalchemy_uri(DATABASE):
    return '%s+%s://%s:%s@%s:%s/%s' % (DATABASE['ENGINE'],
                                       DATABASE['DRIVER'],
                                       DATABASE['USER'],
                                       DATABASE['PASSWORD'],
                                       DATABASE['HOST'],
                                       DATABASE['PORT'],
                                       DATABASE['DB'],
                                       )

#配置装饰器 用于登录验证
def is_login(fuc):
    #被装饰的函数需要使用各种参数
    @wraps(func)
    def check_login(*args，**kwargs)
    	if 'user_id' in session:
            return func(*args,**kwargs)
        else:
            return redirecte(url_for('user.login'))
    return check_login


```

#### `app`

> 创建文件夹`app`用于部署各种views和模型

##### 实现模板，创建模板，删除模板

实现模板前需要理清楚各个模块间的关系

![1533373705938](F:\笔记\imgs\1533373705938.png)

##### 实现模板

```python
#定义基础模型类，用于子类继承属性或者方法，之后继承父类的模型都有create_time he update_time属性  并且可以使用保存和删除的方法
class BaseModel(object):
    # 定义基础的模型
    create_time = db.Column(db.DATETIME, default=datetime.now())
    update_time = db.Column(db.DATETIME, default=datetime.now(),
                            onupdate=datetime.now())

    def add_update(self):
        db.session.add(self)
        db.session.commit()

    def delete(self):
        db.session.delete(self)
        db.session.commit()

```

##### User

```python
"""
在给用户表配置好字段后， 可以设置验证密码，和转成字典格式的方法，
通过装饰器@property 可以把方法变成属性调用
"""
  #读  只读下 密码返回为空
    @property
    def password(self):
        return ''
    #写  设置密码时，返回的是生成为哈希的密码
    @password.setter
    def password(self, pwd):
        self.pwd_hash = generate_password_hash(pwd)

    #对比 对比密码时 把生成的密码和提交的密码对比
    def check_pwd(self, pwd):
        return check_password_hash(self.pwd_hash, pwd)
#以上方法配置好了之后，在视图函数中直接调用即可
"""
转换为字典类
"""
#由于使用的restful风格，所以所有响应都是通过返回一个json格式的信息到ajax 再到客户端页面，所以需要大量的写键值对
    def to_auth_dict(self):
        return {
            'id_name': self.id_name,
            'id_card': self.id_card
        }

    def to_basic_dict(self):
        return {
            'id': self.id,
            'avatar': self.avatar if self.avatar else '',
            'name': self.name,
            'phone': self.phone
        }
#通过方法返回对象的键值对，直接调用方法获取字典格式的内容，提高效率
```

##### 创建数据库

```python
#通过方法db.create_all() 可以创建模板已经配置好的数据库
#如：
@user_blueprint.route('/create_db/', methods=['GET'])
def create_db():
    db.create_all()
    return '创建成功'
#同理 可以实现的还有db.drop_all(), 此类方法具有一定风险性，不建议在视图中设置。
```

##### `user_views.py`

主要实现用户的登录注册及用户中心的配置

###### 实现验证码

```python
@user_blueprint.route('/get_code/', methods=['GET'])
def get_code():
    code = ''
    s = '1234567890qwertyuiopasdfghjklzxcvbnm'
    for i in range(4):
        code += random.choice(s)
    # 往session 存值
    session['code'] = code
    session
    return jsonify(code=200, msg='请求成功', data=code)
#通过接口，视图函数返回一个随机字符串，并保存在session中，返回到ajax ,插入到对应的标签展示出来即可， 每次请求页面都会刷新session中的值
```

###### 头像图片保存

```python
#由于在生成flask对象时发现只有默认的static和templates,没有meida 所以把media设置在了staitc静态文件夹下面

#思路，保存图像到数据库并非保存的图片本身， 而是保存图片的地址。
"""
上传图片需要注意把表单属性添加 enctype="multipart/form-data"

表单中的enctype值如果不设置，则默认是application/x-www-form-urlencoded，它会将表单中的数据变为键值对的形式

当我们上传的含有非文本内容，即含有文件（txt、MP3等）的时候，需要将form的enctype设置为multipart/form-data。

将表单中的数据变成二进制数据进行上传，所以这时候这时如果用request是无法直接获取到相应表单的值的

"""
    # 修改头像
    avatar = request.files.get('avatar')
    if avatar:
        # 验证图片 mimetype = 'image/jpeg' png
        if not re.match(r'image/*', avatar.mimetype):
            return jsonify(status_code.USER_USERINOF_PORFILE_AVATAR_INVALID)

        # 图片保存  media/uplode/xxx.jpg
        avatar.save(os.path.join(upload_folder, avatar.filename))
        # 修改用户的avatar字段
        user = User.query.get(session['user_id'])
        #join方法把字段组合起来 
        avatar_addr = os.path.join('upload', avatar.filename)
        #赋值用户头像就是头像地址，获取用户头像就是获取用户头像的地址
        user.avatar = avatar_addr
        try:
            user.add_update()
            return jsonify(code=status_code.OK, avatar=avatar_addr)
        except Exception as e:
            print(e)
            return jsonify(status_code.DATABASE_ERROR)
    return jsonify(status_code.OK)
```


