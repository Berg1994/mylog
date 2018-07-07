### day1

```
a）框架分析
	a1）用户中心
	a2）首页商品展示
	a3) 闪购（商品列表）
	a4）购物车
		a4.1）订单系统
		a4.2）收货地址
		a4.3）支付
		a4.4）积分系统
		a4.5）vip
		a4.6）物流
		a4.7）评论系统
		a4.8）优惠券
b）开发过程
	b1）首页--闪购--购物车--个人模块
```



#### 创建项目及虚拟环境，配置settings，初步运行项目

1. 虚拟环境搭建

   ```python
   	虚拟环境搭建
   		a1）pychram的解释器interpreter的配置
   		a2）debug的配置
   		virtualenv --no-site-packages -p 
   	在pip安装插件 如django pymysql
   ```

2. 创建项目

   + 创建项目axf

   ```python
   
   django-admin startproject axf
   
   创建app: python manage.py 
   创建模板templates
   创建静态文件static 
   创建开发接口文档doc目录？？？
   
   
   
   
   ```

   + 配置settings.py

   ```python
   注册app
   INSTALLED_APPS = [
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
       'app',
       ]
   ```

   ```python
   templates配置
   TEMPLATES = [
       {
           'BACKEND': 'django.template.backends.django.DjangoTemplates',
           'DIRS': [os.path.join(BASE_DIR, 'templates')],
           'APP_DIRS': True,
           'OPTIONS': {
               'context_processors': [
                   'django.template.context_processors.debug',
                   'django.template.context_processors.request',
                   'django.contrib.auth.context_processors.auth',
                   'django.contrib.messages.context_processors.messages',
               ],
           },
       },
   ]
   ```

   ```python
   databases配置
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.mysql',
           'NAME': 'axf',
           'USER': 'root',
           'PASSWORD': '123456',
           'HOST': '127.0.0.1',
           'PORT': '3306',
       }
   }
   
   ```

   ```python
   语言时区配置
   LANGUAGE_CODE = 'zh-hans'
   
   TIME_ZONE = 'Asia/Shanghai'
   ```

   ```python
   静态资源，音频资源路由，目录配置
   STATIC_URL = '/static/'
   STATICFILES_DIRS = [
       os.path.join(BASE_DIR, 'static')
   ]
   
   MEDIA_URL = '/media/'
   MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
   ```

   3. 初始化pymysql

   ```python
   在项目init中
   
   import pymysql
   
   pymysql.install_as_MySQLdb()
   ```

   4. 前端页面模板渲染

   模板的继承 

   挖坑{% block title%}{% endblock %}以及填坑

   静态css，js，img的渲染。{% load static %}以及直接/static/css/xxx.css

   

   

   