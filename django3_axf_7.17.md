[TOC]



#### 判断ticket是否过期

```python
#判断ticket只要根据Httpresponse中max_age或expires来显示一个时间期限
如:
    #这里的时间单位是秒
    max_age=10  
    #设置时间期限加1天 
 out_time = datetime.now() + timedelta(days=1) 

>>> datetime.now()
datetime.datetime(2018, 7, 21, 8, 57, 33, 541492)

>>> out_time = datetime.now() + timedelta(days=1)
>>> out_time
datetime.datetime(2018, 7, 22, 8, 56, 39, 174383)

#这里对ticket的时间限制后， 在中间件对时间比对判断
#在自定义中间件加入判断  这是在settings里面默认配置 USE_TZ = True 的情况
if ticket_user.out_time.replace(tzinfo=None) < datetime.now()：
# 因为此时 时间措 一个是offset-naive  一个是 offset-aware 所以需要替换时区UTC为None
"""
当我们把settings 里USE_TZ = False
"""
#我们就可以直接用两个时间判断大小
ticket_user.out_time < datetime.now()
False
type(datetime.now())
<class 'datetime.datetime'>
type(ticket_user.out_time)
<class 'datetime.datetime'>



```

#### 闪购页面

```python
"""
闪购页面主要展示所有的分类，分类的子类
实现功能有各种排序 
"""
#1.首先是获取market页面视图 

def market(request):
    if request.method == 'GET':
        return render(request,'market/market.html')
#GET请求打开页面发现没有数据，所以页面的主要目的是提取数据库的数据给页面填坑
"""
#思路：点开页面需要一个固定的展示型页面 也就是先给一个全分类综合排序的商品展示页 这里需要填写默认参数值，考虑之后还需要点击获取各种分类页面响应 所以把这个页面单独写 并返回到分类页面视图
"""

#视图改为规定的kwargs，分别表示分类ID，排序ID，子分类ID
def market(request):
    if request.method == 'GET':
        return HttpResponseRedirect(reverse('axf:marketparams',
                                            kwargs={'typeid': 104749,
                                                    'sort_id': 0,
                                                    'child_id': 0}))

    

    
   
#当点击闪购 url 映射匹配到对应值，再跳转到market视图，返回的response带有固定参数再重定向到下面的视图
def marketparams(request, typeid, sort_id, child_id):
    if request.method == 'GET':
        #获取所有的分类的实例
        foodtypes = FoodType.objects.all()
		#获取有对应分类id的商品实例
        goods = Goods.objects.filter(categoryid=typeid)
        if child_id:
            #判断是否有子分类 如果有，则再筛选带有子分类的商品实例
            goods = Goods.objects.filter(categoryid=typeid,
                                         childcid=child_id)
		#获取对应分类id下的子分类信息 这里包含了子分类名称和ID值
        childtypenames = FoodType.objects.filter(typeid=typeid).first().childtypenames
        if childtypenames:
            #通过debug 查看子分类信息 根据格式切出内容
            childtypenames_list = [i.split(':') for i in childtypenames.split('#')]
			# 闪购页面的排序在a标签里  通过点击可以获取对应的sort_id值 如：
            #<a href="{% url 'axf:marketparams' typeid 1 child_id %}">
            # 中间的 1 就是sort_id 值 是自定义编写的  3个id的顺序根据url映射的正则顺序排序
            if sort_id == '0':
                pass
            elif sort_id == '1':
                goods = goods.order_by('productnum')

            elif sort_id == '2':
                #order_by 的排序根据字典字段值 如果前面加- 则是倒序
                goods = goods.order_by('-price')

            elif sort_id == '3':
                goods = goods.order_by('price')
			
            data = {
                'foodtypes': foodtypes,
                'goods': goods,
                'typeid': typeid,
                'sort_id': sort_id,
                'child_id': child_id,
                'childtypenames_list': childtypenames_list
            }
            return render(request, 'market/market.html', data)
    

#url映射
from django.conf.urls import url

from app import views

urlpatterns = [
    ...
    url(r'^market/$', views.market, name='market'),
    #此处正则 通过写捕获组 实现匹配 如 http://127.0.0.1:8081/axf/marketparams/103557/2/103560/
    #好像没什么卵用 写复杂了 - -   当然 每个组名是唯一的 便于区分排序
    url(r'^marketparams/(?P<typeid>\d+)/(?P<sort_id>\d+)/(?P<child_id>\d+)/', views.marketparams, name='marketparams')
]


```

##### `order_by使用`

```python
#这是order_by 底层运行代码 这里传的参数是字段
    def order_by(self, *field_names):
        """
        Returns a new QuerySet instance with the ordering changed.
        返回一个新的QuerySet实例，顺序发生了变化
        """
        assert self.query.can_filter(), \
            "Cannot reorder a query once a slice has been taken.一旦被取走，就不能重新排序查询"
            
        obj = self._clone()
        obj.query.clear_ordering(force_empty=False)
        obj.query.add_ordering(*field_names)
        return obj
```

##### 正则表达式捕获

```
捕获组就是把正则表达式中子表达式匹配的内容，保存到内存中以数字编号或显式命名的组里，方便后面引用。当然，这种引用既可以是在正则表达式内部，也可以是在正则表达式外部。
捕获组有两种形式，一种是普通捕获组，另一种是命名捕获组，通常所说的捕获组指的是普通捕获组。
普通捕获组：(Expression)

命名捕获组：(?<name>Expression)

Python 新增了一个扩展语法到 Perl 扩展语法中。如果在问号后的第一个字符是 "P"，你就可以知道它是针对 Python 的扩展。目前有两个这样的扩展: (?P<name>...) 定义一个命名组，(?P=name) 则是对命名组的逆向引用。如果 Perl 5 的未来版本使用不同的语法增加了相同的功能，那么 re 模块也将改变以支持新的语法，与此同时为了兼容性的目的而继续保持的 Python 专用语法。
命令组的语法是 Python 专用扩展之一： (?P<name>...)。名字很明显是组的名字。除了该组有个名字之外，命名组也同捕获组是相同的。`MatchObject` 的方法处理捕获组时接受的要么是表示组号的整数，要么是包含组名的字符串。命名组也可以是数字，所以你可以通过两种方式来得到一个组的信息：


>>> p = re.compile(r'(?P<word>\b\w+\b)')
>>> m = p.search( '(((( Lots of punctuation )))' )
>>> m.group('word')
'Lots'
>>> m.group(1)
'Lots'

```

##### `html页面 href 参数`

```
<a href="{% url 'axf:marketparams' typeid 0 child_id %}">
href通过{% %} 反解析url映射函数 后面带的是参数
```

##### 关于`typeid `值传递思路

```html
          <ul>
                {% for foodtype in foodtypes %}
                <!--闪购分类展示-->
                    <li>
                        <!--链接地址，点击获取分类下的商品信息-->
                        <a href="{% url 'axf:marketparams' foodtype.typeid 0 0 %}">{{ foodtype.typename }}</a>
                            {% ifequal foodtype.typeid typeid %}
                            <span class="yellowSlide"></span>
                            {% endifequal %}
                    </li>
                <!--处理数据结束-->
                {% endfor %}
            </ul>

 #此处的ifequal 判断foodtype.typeid 和 typeid 是否相等  问题在于后面的typeid从哪来的
#思路 当我们点击进入闪购页面是 有market视图重定向到marketparam视图，request已经有typeid参数， 但是只有一个。
#但是当我们点击分类时，<a href="{% url 'axf:marketparams' foodtype.typeid 0 0 %}"> 请求视图marketparames函数，这里request传过去的typeid 就是点击的a标签对应的分类id  如：
		<a href="/axf/marketparams/103581/0/0/">卤味熟食</a>
所以 使用ctrl + 左键找不到 typeid从哪来的，是因为只有点击a标签发送请求才有对应的值

```

##### 前端{%%} 列表取值

```html
 {% for childtypename in childtypenames_list %}
    <a href="{% url 'axf:marketparams' typeid sort_id childtypename.1 %}">
        <span>{{ childtypename.0 }}</span>
    </a>
    <!--处理数据结束-->
{% endfor %}
```

