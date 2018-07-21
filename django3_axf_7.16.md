[TOC]

#### meta

```
参考网页 https://blog.csdn.net/lvze0321/article/details/53321755

Meta类

 

在数据模型类中往往有一个Meta类作为内部类。它用于定义一些Django模型类的行为特性。以下对此作一总结：

abstract

     这个属性是定义当前的模型类是不是一个抽象类。所谓抽象类是不会对应数据库表的。一般我们用它来归纳一些公共属性字段，然后继承它的子类可以继承这些字段。比如下面的代码中Human是一个抽象类，Employee是一个继承了Human的子类，那么在运行数据库同步命令时，不会生成Human表，但是会生成一个Employee表，它包含了Human中继承来的字段，以后如果再添加一个Customer模型类，它可以同样继承Human的公共属性：


所以在django 的 models 模板里面 对模型加一个Meta类 abstract = True
下面继承这个类的子类 可以获取下面的属性 如：

class Main(models.Model):
    img = models.CharField(max_length=200)  # 图片
    name = models.CharField(max_length=100)  # 名称
    trackid = models.CharField(max_length=16)  # 通用id

    class Meta:
        abstract = True


class MainWheel(Main):
    # 轮循banner
    class Meta:
        db_table = "axf_wheel"


class MainNav(Main):
    # 导航
    class Meta:
        db_table = "axf_nav"


class MainMustBuy(Main):
    # 必购
    class Meta:
        db_table = 'axf_mustbuy'


class MainShop(Main):
    # 商店
    class Meta:
        db_table = 'axf_shop'
        
        
这里下面继承的类都有img， name， trackid 3个字段  如果写sql语句则需要全部都写上
```

### 爱鲜蜂项目

```python
"""
1.home页面视图思路
home页面需要对轮播，超市，必买，导航等
先获取模板对象实例
通过字典形式data response响应给页面
通过循环取值
"""

def home(request):
    '''
    首页视图函数
    '''
    if request.method == 'GET':
        #轮播
        mainwheels = MainWheel.objects.all()
        #导航
        mainnavs = MainNav.objects.all()
        #必购
        mainbuys = MainMustBuy.objects.all()
        #商店
        mainshops = MainShop.objects.all()
        #物品展示
        mainshows = MainShow.objects.all()
		#作为render的返回参数传到页面再取值
        data = {
            'title': '首页',
            'mainwheels': mainwheels,
            'mainnavs': mainnavs,
            'mainbuys': mainbuys,
            'mainshops': mainshops,
            'mainshows': mainshows,
        }
        return render(request, 'home/home.html', data)
    
    
#轮播图这里使用了js实现 这里主要表达后端返回的响应传到页面如何取值
<div id="home">
        {#   顶部轮播       #}
        <div class="swiper-container" id="topSwiper">
            <div class="swiper-wrapper">
                <!--处理轮播banner图-->
            #通过循环实例获取每一个值
                {% for wheel in mainwheels %}
                    <div class="swiper-slide">
                        <a href="#">
                            <img src="{{ wheel.img }}" alt="">
                        </a>
                    </div>
                {% endfor %}
                <!-- 处理结束 -->
            </div>

            
#展示店铺几个到第几个之间的值
 		<!--处理第一个店铺的数据的图片-->
 			{% for shops in mainshops%}
            #ifequal 后面判断2个值相等的情况
            {% ifequal forloop.counter  1  %}
            <h2>
            	<img src="{{ shops.img }}" alt="">
            </h2>
            {% endifequal %}
            {% endfor %}
            <!--处理结束-->
         
       <fieldset>
            {% for shops in mainshops%}
            #获取第二个到第四个的值 通过forloop.counter来确定第几个
            {% if forloop.counter > 1 and forloop.counter < 4  %}
            <!--处理第二个到第四个数据-->
            	<a href="#">
           			 <img src="{{ shops.img }}" alt="">
            	</a>
            {% endif %}
            {% endfor %}
            <!--处理结束-->
            </fieldset>
    

```

#### 中间件

```python
判断是否登录

class UserMiddle(MiddlewareMixin):

    def process_request(self, request):

        check_path = ['/axf/mine']

        for path in check_path:
            if request.path == path:
                ticket = request.COOKIE.get('ticekt')
                if ticket:
                    ticket_user = UserTicketModel.objects.filter(ticket=ticket).first()
                    if ticket_user:
                        request.user = ticket_user.user
                        return None
                    return render(request, 'user/user_login.html')
                return render(request, 'user/user_login.html')

            return None

```

#### 登录注册

````python
"""
完整的登录信息涉及登录页面填写的值，如：账号，密码，邮箱，性别，图片等 判断长度，字符格式，信息是否填写完整
（这个也可以通过js实现 如注册密码pw1,pw2是否一致 邮箱格式通过input 选择type类型也可以实习）。

通过debug先获取表单提交的Post请求 查看request里面的数据格式， 如图片放在files里面 需要自己查看才能知晓。
"""
# 注册
def register(request):
    if request.method == 'GET':
        return render(request, 'user/user_register.html')
    if request.method == 'POST':
        username = request.POST.get('username')
        email = request.POST.get('email')
        password = request.POST.get('password')
        icon = request.FILES.get('icon')

        if not all([username, email, password, icon]):
            msg = '请填写完整'
            return render(request, 'user/user_register.html', {'msg': msg})
        password = make_password(password)
        user = UserModel.objects.create(username=username,
                                        email=email,
                                        password=password,
                                        icon=icon,
                                        )
        return HttpResponseRedirect(reverse('user:login'))
    
    
    
    
    
# 登录
def login(request):
    if request.method == 'GET':
        return render(request, 'user/user_login.html')
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        if not all([username, password]):
            msg = '请填写完整'
            return render(request, 'user/user_login.html', {'msg': msg})
        user = UserModel.objects.filter(username=username).first()
        if user:
            # checkpassword 返回布尔值  前面填获取的密码 后面是服务器已经加密的密码
            if check_password(password, user.password):
                ticket = get_ticket()
                res = HttpResponseRedirect(reverse('user:mine'))
                # 当前时间加1天
                out_time = datetime.now() + timedelta(days=1)
                # max_age为最大存活时间  expires 是什么时候到期
                res.set_cookie('ticket', ticket, expires=out_time)
                UserTicketModel.objects.create(user=user,
                                               ticket=ticket,
                                               out_time=out_time)

                return res
            msg = '账号或者密码错误'
            return render(request, 'user/user_login.html', {'msg': msg})
        msg = '账号或者密码错误'
        return render(request, 'user/user_login.html', {'msg': msg})

````

#### 个人中心页面登录判断

```

```

