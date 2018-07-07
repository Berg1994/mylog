#### app应用视图

删除学生

```python
'''
删除学生
'''


def delstu(request):
    if request.method == 'GET':
        s_id = request.GET.get('s_id')
        #通过get请求获取点击标签对应的s_id 
        Student.objects.filter(id=s_id).delete()
        #删除在表中对应ID的学生id

        return HttpResponseRedirect(reverse('app:student'))

```

![1528515791968](C:\Users\ADMINI~1\AppData\Local\Temp\1528515791968.png)

此处对应的html页面 a标签链接映射删除视图 后面跟了s_id并赋值这条信息对应的学生ID   stu源于遍历pages这个集合 ---> pages值源于page_num ---> page_num值源于request.GET.get 赋值 当有这个值 则赋值page_num   没有则赋值成1

  这里打个断点查看DEBUG 如何获取的

![1528526089297](C:\Users\ADMINI~1\AppData\Local\Temp\1528526089297.png)

刷新path 进入视图

![1528526129939](C:\Users\ADMINI~1\AppData\Local\Temp\1528526129939.png)

运行暂停到判断请求方式这里 在variables 里面 没有查到page_num 值 说明请求里面没有这个值

当运行完赋值 就能查看到page_num

![1528526549548](C:\Users\ADMINI~1\AppData\Local\Temp\1528526549548.png)

这里因为没有值 所以被赋值成了1

![1528526924528](C:\Users\ADMINI~1\AppData\Local\Temp\1528526924528.png)

??

#### 添加学生信息

```python
'''
添加学生信息
'''


def addstu(request):
    if request.method == 'GET':
        grades = Grade.objects.all()
        return render(request, 'addstu.html', {'grades': grades})

    if request.method == 'POST':
          #获取请求中的s_name 
        s_name = request.POST.get('s_name')
        #获取班级的ID 
        g_id = request.POST.get('g_id')
		#获取头像
        s_img = request.FILES.get('s_img')
        #  获取对应的班级信息
        grade = Grade.objects.filter(id=g_id).first()
        # 创建学生信息
        # Student.objects.create(s_name=s_name, g_id=grade.id)/
        Student.objects.create(s_name=s_name,
                               g=grade,
                               s_img=s_img)

        # stu = Student(s_name=s_name, g=grade)
        # stu.save()
        return HttpResponseRedirect(reverse('app:student'))
```

### 建立用户

另创建app user 

```python
python manage.py appstart user
```

建urls.py 在项目里拼接路径

```
 url(r'^user/', include('user.urls', namespace='user')),
```

在settings.py里注册应用

```python
'''
注册
'''
def djregister(request):

    if request.method == 'GET':
        return render(request, 'register.html')

    if request.method == 'POST':

        username = request.POST.get('username')
        pwd1 = request.POST.get('pwd1')
        pwd2 = request.POST.get('pwd2')
#获取post请求中提交的信息 username pwd1 pwd2 
        if not all([username, pwd1, pwd2]):
            msg = '请填写完所有参数'
            return render(request, 'register.html', {'msg': msg})     #用all 方法确认必须要求填写的信息是否填写

        if pwd1 != pwd2:
            msg = '两次密码不一致'
            return render(request, 'register.html', {'msg': msg})		#判断密码是否一致

        User.objects.create_user(username=username, password=pwd1)
        #创建用户 

        return HttpResponseRedirect(reverse('app:djlogin'))
```

#### 这里用的是django自带的auth 方法进行判断 

此法可以用于判断登录的用户信息是否正确

```python
from django.contrib import auth
#引入
user = auth.authenticate(username=username, password=password)
#核对用户名密码
auth.login(request, user)
#登录对应的用户
auth.logout(request)
#退出清除请求信息
```

```python
'''
登录
'''
def djlogin(request):

    if request.method == 'GET':
        return render(request, 'login.html')

    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        # 返回验证成功的用户信息
        user = auth.authenticate(username=username, password=password)
        if user:
            auth.login(request, user)
            return HttpResponseRedirect(reverse('app:index'))
        else:
            msg = '用户名或者密码错误'
            return render(request, 'login.html', {'msg': msg})
       
    
```



#### 自己实现登录

```python
def login(request):
    if request.method == 'GET':
        return render(request, 'login.html')
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
		#获取登录请求中的用户名和密码
        user = Users.objects.filter(username=username,
                                    password=password).first()
        #过滤数据库中是否有这个user
        if user:
            # 先产生随机的字符串， 长度28
            s = 'qwertyuiopasdfghjklzxcvbnm1234567890'
            ticket = ''
            for i in range(28):
                ticket += random.choice(s)
            # 保存在服务端
            user.ticket = ticket
            #随机生成一个28位的ticket 并赋值给user
            user.save()
            # 保存在客户端，cookies
            response = HttpResponseRedirect(reverse('app:index'))
            response.set_cookie('ticket', ticket)
            '''
                    def set_cookie(self, key, value='', max_age=None, expires=None, path='/',
                   domain=None, secure=False, httponly=False)
            在set_cookie中传值
           ''' 
      

            return response
```

#### 设置中间键

在项目下建立文件夹 utils 

创建_\_init\_\_.py

创建UserAuthMiddleware.py

```python
from django.http import HttpResponseRedirect
from django.utils.deprecation import MiddlewareMixin

from django.core.urlresolvers import reverse

from user.models import Users

class UserAuthMiddle(MiddlewareMixin):

    def process_request(self, request):

        # 验证cookie中的ticket，验证不通过，跳转到登录
        # 验证通过，request.user当前登录的用户信息
        # return None或者不写return

        path = request.path
        #获取请求中的路径
        s = ['/user/login/', '/user/register/']
        if path in s:
            return None

        ticket = request.COOKIES.get('ticket')
        #从请求中获取ticket

        if not ticket:
            return HttpResponseRedirect(reverse('user:login'))
			
        user = Users.objects.filter(ticket=ticket)
        #获取对应值的user
        if not user:
            return HttpResponseRedirect(reverse('user:login'))

        request.user = user
        #把user赋值给请求中的user
```

#### 通过装饰器来做中间键

在 utils 里写一个装饰器 functions.py 

把这个函数导入视图views  给需要的视图加一个@is_login 就可以实现中间键功能

```python
def is_login(func):
    def check_login(request):
        # 如果登录，返回函数func
        ticket = request.COOKIES.get('ticket')
        if not ticket:
            # 没有登录，跳转到登录页面
            return HttpResponseRedirect(reverse('user:login'))
        user = Users.objects.filter(ticket=ticket)
        if not user:
            # 没有登录，跳转到登录页面
            return HttpResponseRedirect(reverse('user:login'))
        return func(request)
    return check_login
```

