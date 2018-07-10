[TOC]

#### 安装插件文本

```
requeirement.txt 里面写入你的插件版本
click==6.7
Django==1.11
Flask==1.0.2
Flask-Script==2.0.6
itsdangerous==0.24
Jinja2==2.10
MarkupSafe==1.0
Pillow==5.1.0
PyMySQL==0.8.1
pytz==2018.4
virtualenv==16.0.0
Werkzeug==0.14.1


pip install -r requeirement.txt
使用此条命令可以直接安装文本里面所有的版本

```

#### 创建虚拟环境`virtualenv `

```
pip install virtualenv --no-site-packages
-p PYTHON_EXE  指定python版本路径 


```

#### 创建项目

```
django-admin startproject day01 
#项目名后加 . 可以去掉一层文件夹
```

#### 配置settings

```
1.注册 app应用
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app'
]


2.添加templates模板路径
'DIRS': [os.path.join(BASE_DIR, 'templates')]

3.配置数据库（mysql）

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME' : 'dj03',
        'USER': 'root',
        'PASSWORD':'123456',
        'HOST':'localhost',
        'PORT':'3306',
    }
}

4. 修改语言
LANGUAGE_CODE = 'zh-hans'

5 修改时区
TIME_ZONE = 'Asia/Shanghai'

6.配置静态文件路径
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]

```



#### `namespace`

```python
# 注册
def register(request):
    if request.method == 'GET':
        return render(request, 'register.html')

    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('pasword')

        User.objects.create_user(username=username,password=password)

        #return HttpResponseRedirect('/app/login')
    
   #此处路径写死后不便于后期修改  所以使用namespace方式写路径
		User.objects.create_user(username=username, password=password)
    
{% url 'app:head' %}   namespace:name
#URL命名空间可以保证反查到唯一的URL，即使不同的app使用相同的URL名称
以后即便修改了名称  也不需要大面积的更改
```

#### `django自带登录auth`

```python
#django自带登录模块主要分为4部分
#先需要导入模块
from django.contrib import auth
from django.contrib.auth.models import User

#1. 登录视图中 可以通过模块对request中获取的信息进行验证
    if requset.method == 'POST':
        username = requset.POST.get('username')
        password = requset.POST.get('password')
        # 验证用户名和密码是否能从数据库中匹配user对象
        # User.objects.filter(username=username,password=password)
        user = auth.authenticate(username=username, password=password)
        
        
#2.在登录视图中  通过模块验证后 进行登录
 if user:
            # 通过验证
            auth.login(requset, user)
            return HttpResponseRedirect(reverse('app:index'))
            
#3.在注销视图中  可以通过模块对request中session值删除到达退出登录效果
# 注销
def logout(request):
    if request.method == 'GET':
        auth.logout(request)
        return HttpResponseRedirect(reverse('app:login'))
```



#### 静态文件加载

```
在settings.py中最底下有一个叫做static的文件夹，主要用来加载一些模板中用到的资源，提供给全局使用
这个静态文件主要用来配置css，html，图片，文字文件等

STATIC_URL = ‘/static/’
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, ‘static’)
]

只后在模板中，首先加载静态文件，之后调用静态，就不用写绝对全路径了
2. 使用静态配置文件
a) 加载渲染静态配置文件 模板中声明

{% load static %} 或者 {% load staticfiles %}
在引用资源的时候使用

{% static ‘xxx’ %} xxx就是相当于staticfiles_dirs的一个位置
b) 直接定义静态配置

<img src="/static/images/mvc.png">
其中: 展示static文件夹下有一个images文件夹，下面有一个mvc.png的图片
```



#### 创建时间、创建修改时间

```
auto_now_add 创建时间  对象第一次创造的时候，用于“创造”的 时间戳。
字段在实例第一次保存的时候会保存当前时间，不管你在这里是否对其赋值。但是之后的save()是可以手动赋值的。也就是新实例化一个model，想手动存其他时间，就需要对该实例save()之后赋值然后再save()。（保持首次创建的时间，后再对实例变动修改，这个值不会变化的）

auto_now=Ture，字段保存时会自动保存当前时间，但要注意每次对其实例执行save()的时候都会将当前时间保存，也就是不能再手动给它存非当前时间的值。每一次执行修改等动作，时间保持当前的。而非首次的创建时间。
```



#### 自定义表名

```
class Meta:
	db_table = 'grade'
#db_table是用于指定自定义数据库表名的。Django有一套默认的按照一定规则生成数据模型对应的数据库表名，如果你想使用自定义的表名，就通过这个属性指定
```

#### `html`页面反向解析写法

```
<script src="/static/js/side.js" type="text/javascript"></script>

<script src="{% static 'js/side.js' %}" type="text/javascript"></script>
对于django下的模板页面  替换静态资源路径 还要在html标签下加上 {% load staticfiles %}


<option value="{{ grade.id }}">{{ grade.g_name }}</option>
{{}} 用于替换内容  值由对应的视图最后返回的response传过来 


<frame src="{% url 'app:left' %}" name="leftmenu" id="mainFrame" title="mainFrame">
<a class="cks" href="{% url 'app:addgrade' %}"

把网页地址替换成url路径  响应对应的视图 因为是GET请求  视图返回的还是页面
```



#### `urls`

```python
"""
django框架中  有多个app  每个app都有对应的urls，但是项目只有一个 多人开发中不能多人修改主项目的urls
所以从项目的ursl拼接
"""
from django.contrib import admin
from django.conf.urls import url,include
urlpatterns = [
    url(r'admin/', admin.site.urls),
    url(r'app/', include('app.urls',namespace='app')),
]

#在app应用下的urls
from django.conf.urls import url
from django.contrib.auth.decorators import login_required

from app import views

urlpatterns = [
    # 登录视图
    url(r'login/', views.login, name='login'),
    # 注册视图
    url(r'register/', views.register, name='register'),
    # 注销视图
    url(r'logout/', views.logout, name='logout'),
    # 首页 login_required 登录检测
    url(r'index/', login_required(views.index), name='index'),
    # 导航栏
    url(r'left/', views.left, name='left'),
    # 班级
    url(r'grade/', views.grade, name='grade'),
    # 头部
    url(r'head/', views.head, name='head'),
    #学生页面
    url(r'student/', views.student, name='student'),
    #添加学生
    url(r'addstu/', views.addstu, name='addstu'),
    #班级页面
    url(r'grade/', views.grade, name='grade'),
    #添加班级
    url(r'addgrade/', views.addgrade, name='addgrade'),

]
```



