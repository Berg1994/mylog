[TOC]

#### 模板继承

```html
目的 减少代码量  结构清晰 配置一个base.html模板骨架  其他模板继承并填充内容 


base.html如下：

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
        {% block extJS %}
        {% endblock %}
    </head>
    {% block indexbody %}
    {% endblock %}
    <body>

    {% block content %}
    {% endblock %}

    {% block footer %}
    {% endblock %}
    </body>
</html>




#不是所有的模板部分都必须全部继承，但是继承的部分顺序必须一致 如base的{% block extJS %}写在{% block content %}内 ， 继承的html的JS就不能写在{% block content %} 外面 严格按照定义的模板位置填坑

设置base_main.html模板，用于配置一些公共的js或者css 进一步减少代码量和优化，不能继承base_main.html的页面可以直接继承base.



base_main.html如下：
{% extends 'base.html' %}

{% block extJS %}
    <script type="text/javascript" src="/static/js/jquery.min.js"></script>
{% endblock %}





继承base_main.html 及 下面的js写法 





{% extends 'base_main.html' %}

{% block title %}
	首页{{ user.username }}
{% endblock %}

{% block extCSS %}
	<link rel="stylesheet" type="text/css" href="/static/css/public.css" />
{% endblock %}

{% block extJS %}
	{{ block.super }}
	<script type="text/javascript" src="/static/js/public.js"></script>
{% endblock %}

{% block indexbody %}
<frameset rows="100,*" cols="*" scrolling="No" framespacing="0"
	frameborder="no" border="0">
	<frame src="{% url 'app:head' %}" name="headmenu" id="mainFrame" title="mainFrame"><!-- 引用头部 -->
<!-- 引用左边和主体部分 -->
	<frameset rows="100*" cols="220,*" scrolling="No"
	framespacing="0" frameborder="no" border="0">
		<frame src="{% url 'app:left' %}" name="leftmenu" id="mainFrame" title="mainFrame">
		<frame src="{% url 'app:grade' %}" name="main" scrolling="yes" noresize="noresize"
	id="rightFrame" title="rightFrame">
	</frameset>
</frameset>
{% endblock %}


```

#### `RESTful`

```python
"""
REST指的是一组架构约束条件和原则。如果一个架构符合REST的约束条件和原则，我们就称它为RESTful架构。
"""
#1.任何事物，只要有被引用到的必要，就是资源。资源需要一个唯一标识URL去识别，URL可看成是资源地址或是名称
#2.RESTful架构应该遵循统一接口原则，接口应该使用标准的HTTP方法如GET，PUT和POST，并遵循这些方法的语义。
#3.api 定义规范 如http://xxx.com/api       http://xxx.com/v2/
#4.资源  在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数
#5. http请求方式 如下：
"""
 GET（SELECT）：从服务器取出资源（一项或多项）

POST（CREATE）：在服务器新建一个资源

PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）

PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）

DELETE（DELETE）：从服务器删除资源
"""
#6.filter过滤
"""
?page=2&per_page=100：指定第几页，以及每页的记录数。

?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。

?animal_type_id=1：指定筛选条件
"""


#1. 安装restful
pip install djangorestframework #这里使用的是3.4.8
pip install django-filter  # Filtering support

#2.配置settings
#在工程目录中的settings.py文件的INSTALLED_APPS中需要添加rest_framework
INSTALLED_APPS = [
	...
    'rest_framework',
]


#3.定义urls
#在应用项目下的urls里  创建一个路由实例并添加url
from django.conf.urls import url
from rest_framework.routers import SimpleRouter

from stu import views
#生成一个路由
router = SimpleRouter()
#处理这个视图类
router.register('^student',views.StudentSource)

urlpatterns = [

    url(r'^s_index/',views.s_index,name='s_index'),
]

urlpatterns += router.urls


#4.写视图类
#上面url映射的地址/stu/student/的视图是一个类
#所以在views里实现这个类

from rest_framework import mixins, viewsets
					#获取get
class StudentSource(mixins.ListModelMixin,
					#修改update/put
                    mixins.UpdateModelMixin,
                    	
                    mixins.RetrieveModelMixin,
                    	#删除delete
                    mixins.DestroyModelMixin,
                    	#创建post
                    mixins.CreateModelMixin,
                    viewsets.GenericViewSet):

    #查询学生的数据
    queryset = Student.objects.all()
    #序列化
    serializer_class = StuSerializer
    
    
#5.创建序列化器stu_serializer.py，序列化器可以把模型转换成需要返回的 json、xml 类型数据。在应用文件下创建
from rest_framework import serializers

from stu.models import Student


class StuSerializer(serializers.ModelSerializer):
    class Meta:
        # 指定序列化的模型
        model = Student
        # 指定需要展示的字段
        fields = ['id', 's_name','g']

#6.使用REST框架构建一个简单的模型支持的API的快速示例,REST框架API的任何全局设置都保存在名为的单个配置字典中REST_FRAMEWORK。首先将以下内容添加到settings.py模块中: 
#这个配置是在3.4.8版本中完成的

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 2,
    'DEFAULT_FILTER_BACKENDS':(
      'rest_framework.filters.DjangoFilterBackend',
      'rest_framework.filters.SearchFilter',

    ),
    'DEFAULT_RENDERER_CLASSES': (
        'utils.renderers.MyJsonRenderer',

    )
}

        

        
        

修改接口返回的错误信息为中卫
```

#### rest_framework CRUD

```

```

