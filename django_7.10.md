[TOC]

#### 学生管理系统填坑`rul`

```
django的路由系统作用就是使views里面处理数据的函数与请求的url建立映射关系
url()函数可以传递4个参数，其中2个是必须的：regex和view，以及2个可选的参数：kwargs和name

如：url(r'^login/$', views.login, name='login'),

第一个参数正则  Django拿着用户请求的url地址，在urls.py文件中对urlpatterns列表中的每一项条目从头开始进行逐一对比，一旦遇到匹配项，立即执行该条目映射的视图函数或二级路由，其后的条目将不再继续匹配。
需注意匹配是否完整  对于重叠的url 需要用^$ 来确定开始和结束
另需要注意，regex不会去匹配GET或POST参数或域名，例如对于https://www.example.com/myapp/，regex只尝试匹配myapp/。对于https://www.example.com/myapp/?page=3,regex也只尝试匹配myapp/。

views  对应的视图函数 
当正则表达式匹配到某个条目时，自动将封装的HttpRequest对象作为第一个参数，正则表达式“捕获”到的值作为第二个参数，传递给该条目指定的视图。如果是简单捕获，那么捕获值将作为一个位置参数进行传递，如果是命名捕获，那么将作为关键字参数进行传递。

kwargs：
任意数量的关键字参数可以作为一个字典传递给目标视图。

name：
对你的URL进行命名，可以让你能够在Django的任意处，尤其是模板内显式地引用它。相当于给URL取了个全局变量名，你只需要修改这个全局变量的值，在整个Django中引用它的地方也将同样获得改变


```

#### 时间过滤器

```
<td>{{ grade.id }}</td>
<td>{{ grade.g_name | lower}}</td>
<td>{{ grade.g_create_time |date :'Y年m月d h:m:s'}}</td>
#过滤器 在变量显示前修改 
```

#### 时间的存储与读取不一致 

```markdown
创建时间默认零时区  但是显示时加8

DateTimeField和DateField和TimeField存储的内容分别对应着datetime(),date(),time()三个对象。

1. UTC时间表示的是格林尼治平均时，即零区时间。北京为东8区 即UTC+8

2. naive time就是不带时区的时间，相关Active time就是带时区的时间 如：
datetime.datetime.utcnow()、datetime.datetime.now()输出的类似2015-05-11 09:10:33.080451就是不带时区的时间（naive time），而使用django.util.timezodatetime.datetime.now()：输出的永远是本地时间（naive time）与配置无任任何关系ne.now()输出的类似2015-05-11 09:05:19.936835+00:00的时间就是带时区的时间（Active time）

3. datetime.datetime.now()、datetime.datetime.utcnow()与django.util.timezone.now()的区别
datetime.datetime.now()：输出的永远是本地时间（naive time）与配置无任任何关系

datetime.datetime.utcnow()：如果setting中配置USE_TZ=True则输出的是UTC时间（naive time），如果setting中配置USE_TZ=False，则该输出时间与datetime.datetime.now()完全相同。

django.util.timezone.now()：如果setting中配置USE_TZ=True则输出的是UTC时间（active time），如果配置USE_TZ=False，则与datetime.datetime.now()完全相同。


Django1.4版本之前，对时区毫无概概念，对时间的存取、展示不做任何处理，数据库里存储的通常是本地时间，当然都是naive time。

Django在1.4版本之后存储如果设置了USE_TZ=True，则存储到数据库中的时间永远是UTC时间。
这时如果settings里面设置了USE_TZ=True与TIME_ZONE = 'UTC'，用datetime.datetime.now()获取的时间django会把这个时间当成UTC时间存储到数据库中去。
如果修改设置为USE_TZ=True与TIME_ZONE = 'Asia/Shanghai'，用datetime.datetime.now()获取的时间由于不带时区，django会把这个时间当成Asia/Shanghai时间，即东八区时间，然后django会把这个时间转成带时区UTC时间存储到数据库中去，而读的时候直接按UTC时间读出来，这就是网上很多人遇到的存储到数据库中的时间比本地时间会小8个小时的原因。

只要设置了USE_TZ=True，django.util.timezone.now()输出地永远是UTC时间，不管你设置的TIME_ZONE是什么。如果USE_TZ=False，则django.util.timezone.now()输出等同于datetime.datetime.now()，也不管TIME_ZONE设置的是什么。 这样得到的就是本地时间

为了统一时间，在django开发时，尽量使用UTC时间，即设置USE_TZ=True，TIME_ZONE = 'Asia/Shanghai'，并且在获取时间的时候使用django.util.timezone.now()。因为后台程序使用时间时UTC时间就能满足，也能保证证模板时间的正确显示。

[原文地址](https://www.cnblogs.com/alan-babyblog/p/5739004.html)
```



#### 用pk代替模型主键

```
pk=g_id  id=g_id
此处pk 是主键 Primary Key 
id 一般来说也是作为主键来定义的  所以把id 也成pk 也就行了  
```

`Paginator`分页（django自带模块）

```
https://docs.djangoproject.com/zh-hans/2.0/topics/pagination/  先扔个官方文档

>>> from django.core.paginator import Paginator
>>> objects = ['john', 'paul', 'george', 'ringo']
#传入参数 被分页的对象 每页显示条数
>>> p = Paginator(objects, 2)
#显示总数
>>> p.count
4
#显示总页数
>>> p.num_pages
2
#查看分页对象类型
>>> type(p.page_range)
<class 'range_iterator'>
>>> p.page_range
#得到页码 动态生成
range(1, 3)

>>> page1 = p.page(1)
>>> page1
<Page 1 of 2>
#第一页所有分页对象
>>> page1.object_list
['john', 'paul']

>>> page2 = p.page(2)
>>> page2.object_list
['george', 'ringo']
#查看是否有下一页
>>> page2.has_next()
False
#查看是否有上一页
>>> page2.has_previous()
True
#查看是否有其他页面
>>> page2.has_other_pages()
True
>>> page2.next_page_number()
Traceback (most recent call last):
...
EmptyPage: That page contains no results
#查看上一页页数
>>> page2.previous_page_number()
1
>>> page2.start_index() # The 1-based index of the first item on this page
3
>>> page2.end_index() # The 1-based index of the last item on this page
4

>>> p.page(0)
Traceback (most recent call last):
...
EmptyPage: That page number is less than 1
>>> p.page(3)
Traceback (most recent call last):
...
EmptyPage: That page contains no results


首先获取所有数据
通过Paginator(grades,2) 对所有数据进行拆分
参数garades 为拆分对象  参数2为拆分每页2条

paginator = Paginator(grades,2)  创建实例
Paginator.page(number)：根据参数number返回一个Page对象。（number为1的倍数）

通过debug模式可以非常直观的观察分页效果

paginator.page
<bound method Paginator.page of <django.core.paginator.Paginator object at 0x05135B30>>
paginator.page(1)
<Page 1 of 3>
paginator.page(2)
<Page 2 of 3>
paginator.page(3)
<Page 3 of 3>
通过获取实例的方法page()填入参数实现页面跳转


paginator.page_range 
range(1, 4)
获取的是分页后的范围  我们用来做页数
如 ： {% for i in grades.paginator.page_range %}
page_range就是循环次数 进行分页取值


[i.g_name for i in paginator.page(1)]
['python', 'java']
此处生成器遍历分页对象 取出分页对象的属性g_name
#查看第一页的第一个分页对象的g_name属性
paginator.page(1)[1].g_name
'java'


  {% for i in grades.paginator.page_range %}
<li><a href="{% url 'app:grade' %}?page_num={{ i }}">{{ i }}</a></li>
    {% endfor %}
常见网页的网址形式在后面加？page= 
所以把herf替换成对应的值 即可实现跳转


```

#### django自带中间件

```
 from django.contrib.auth.decorators import login_required
 
 # 首页 login_required 登录检测
    url(r'index/', login_required(views.index), name='index'),
```

#### 中间件

```
midel
url - prcess request - 路由 - process view - view - process template\response - 
```

####`if表达式`

```
格式1：

    {% if 表达式 %}

    {%  endif %}
格式2：

    {% if表达式 %}

    {% else %}

    {%  endif %}
格式3：

    {% if表达式 %}

    {% elif 表达式 %}

    {%  endif %}


 for表达式
格式1：

    {% for 变量 in 列表 %}

    {% empty %}

    {% endfor %}


注意：当列表为空或者不存在时，执行empty之后的语句

注意一下用法:
{{ forloop.counter }} 表示当前是第几次循环，从1开始
{{ forloop.counter0 }} 表示当前从第几次循环，从0开始
{{forloop.revcounter}}表示当前是第几次循环，倒着数数，到1停
{{forloop.revcounter0}}表示当前是第几次循环，倒着数数，到0停
{{forloop.first}}是否是第一个      布尔值
{{forloop.last}}是否是最后一个      布尔值
```

#### 注释

```
注释
注释可见，可运行
<!-- 注释内容 -->
单行注释注释不可见，不可运行
单行注释(页面源码中不会显示注释内容)

{# 被注释掉的内容 #}
多行注释注释不可见，不可运行
{% comment %}

{% endcomment %}
```

#### 分页视图函数实现及页面分页实现

```python
#视图函数
def grade(request):
    if request.method == 'GET'：
    #这里获取请求里面的page_num值 如果木有则为1
    num = request.GET.get('page_num',1)
    #创建班级对象实例 获取所有对象
    grades = Grade.object.all()
    #创建分页对象实例，传入参数为被分页的对象和每页条数
    paginator = Paginator(grades,2)
    #page 是一个对象列表 num是页码 代表这一页的分页对象
    page = Paginator.page(num)
    #返回请求 这里的因为page已经是分页对象的列表了 所以可以代替grades 
    return render(request, 'grade.html', {'grades': page})
	# return render(request, 'grade.html', {'grades': grades,'page':page},)
    
    #写一个生成器 可以直观的获取2个分页对象
    [i for i in page]
	[<Grade: Grade object>, <Grade: Grade object>]
	#获取分页对象的属性
    [i.g_name for i in page]
	['java', 'python']
    #直观看出这是一个列表 获取下标第一个值的属性
    page[0].g_name
	'java'

```

```html
后端默认的数据为1 所以直接写视图就是第一页
<li><a href="{% url 'app:grade' %}">首页</a></li>
{% if grades.has_previous %}
此处先判断是否有上一页 
<li><a href="{% url 'app:grade' %}?page_num={{ grades.previous_page_number }}">上一页</a></li>
{% endif %}
    {% for i in grades.paginator.page_range %}
page_range表示得到的页码数 动态生成的 循环获取所有页码
<li><a href="{% url 'app:grade' %}?page_num={{ i }}">{{ i }}</a></li>
    {% endfor %}
{% if grades.has_next %}
<li><a href="{% url 'app:grade' %}?page_num={{ grades.next_page_number }}">下一页</a></li>
{% endif %}
<li><a href="{% url 'app:grade' %}?page_num={{ grades.paginator.num_pages }}">尾页</a></li>
```

