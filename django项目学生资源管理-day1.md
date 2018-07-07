规范项目文件夹和虚拟环境文件夹  便于明确虚拟环境里面有什么插件

1. 创建虚拟环境

```
pip instatll virtualenv

virtualenv --no-site-packages djangoenv

pip freeze:查看pip安装过的包

pip list：查看所有安装过的包

```

安装django1.11 

```
pip install django==1.11
```

2. 创建项目并且运行

   ```
   django-amdin startproject axf
   
   python manage.py startapp app
   
   python manage.py runserver
   
   ```

以上步骤都可以在cmd命令提示符 中完成

以下步骤在IDE集成开发环境中写 

3. 设置settings.py

   

   ![1528511311672](C:\Users\ADMINI~1\AppData\Local\Temp\1528511311672.png)

   ![1528511367378](C:\Users\ADMINI~1\AppData\Local\Temp\1528511367378.png)

   

   ![1528511252198](C:\Users\ADMINI~1\AppData\Local\Temp\1528511252198.png)

   

   ![1528511163039](C:\Users\ADMINI~1\AppData\Local\Temp\1528511163039.png)

4. 设置 pymsql

   ```python
   在项目下__init__.py
   修改默认MySQLdb 
   
   import pymysql
   
   pymysql.install_as_MySQLdb()
   ```

5. 设置urls.py

   团队开发下 不可能所有人都来修改项目的urls 所以每个app 都应该建立自己的urls 然后在项目urls下建立一个拼接路径

   ```python
   from django.conf.urls import url, include
   from django.contrib import admin
   
   urlpatterns = [
       url(r'^admin/', admin.site.urls),
       url(r'^app/', include('app.urls', namespace='app')),
   ]
   ```

6. 设置静态资源文件夹

   ![1528511729350](C:\Users\ADMINI~1\AppData\Local\Temp\1528511729350.png)

   静态资源文件夹用于存放各种静态资源如图片，js,css等

7. 设置html模板

   在项目下建立文件夹templates 用于存放各种页面模板

![1528511933297](C:\Users\ADMINI~1\AppData\Local\Temp\1528511933297.png)

8.  迁移mysql自带的表格到数据库

   ```
   python manage.py migrate
   ```

   

9. 写应用模型

   在app应用model.py 里 编写模型

   ![1528512242453](C:\Users\ADMINI~1\AppData\Local\Temp\1528512242453.png)

10. 设置urls

    path 需要一个跟一个视图对应  所以需要先创建一个应用 再创建一个视图

    （修改path 需跟一个视图对应）

    ```python
    
    from django.conf.urls import url
    
    from app import views
    
    urlpatterns = [
        url(r'^index/', views.index, name='index'),
        url(r'^left/', views.left, name='left'),
        url(r'^grade/', views.grade, name='grade'),
        url(r'^head/', views.head, name='head'),
        url(r'^addgrade/', views.addgrade, name='addgrade'),
        url(r'^student/', views.students, name='student'),
        url(r'^addstu/', views.addstu, name='addstu'),
    
    
    ]
    
    ```

11. 建立视图

    每个视图（业务逻辑）就是一个函数 用于处理在页面path的处理方法

    首页视图

    ```python
    '''
    首页
    '''
    def index(request):
    #判断请求类型为GET时
        if request.method == 'GET':
        #返回一个渲染方法，里面包含参数request和模板index页面
            return render(request, 'index.html')
     
    ```

    班级视图

    ```python
    
    '''
    班级列表
    '''
    
    
    def grade(request):
        if request.method == 'GET':
            page_num = request.GET.get('page_num', 1)
            #设置页码 默认为1
            grades = Grade.objects.all()
            #建立grades 对象 里面包含了模型Grade属性方法
            paginator = Paginator(grades, 3)
            #设置分页 带入参数grades  每页3条信息
            pages = paginator.page(int(page_num))
            #把字符串转成整数
            return render(request, 'grade.html', {'grades': grades, 'pages': pages})
        	#返回请求 并把grades 和pages的值在页面里替换
    ```

    添加班级视图

    ```python
    '''
    添加班级页面
    '''
    
    
    def addgrade(request):
        if request.method == 'GET':
            return render(request, 'addgrade.html')
    
        if request.method == 'POST':
            #当请求方法为POST时 即带有值的请求
            # 创建班级信息
            g_name = request.POST.get('grade_name')
            #从请求中获取班级民资
            g = Grade()
            #建立班级模型对象（里面包含了g_name属性）
            g.g_name = g_name
            #把请求上次的值赋值给对应的班级对象 班级表格就获取了姓名值
            g.save()
            #保存数据
            return HttpResponseRedirect(reverse('app:grade'))
        	#完成数据添加后，重定向到班级页面
    ```

    添加学生信息视图

    ```python
    '''
    添加学生信息
    '''
    
    
    def addstu(request):
        if request.method == 'GET':
            grades = Grade.objects.all()
            return render(request, 'addstu.html', {'grades': grades})
    
        if request.method == 'POST':
            s_name = request.POST.get('s_name')
            #从post请求里获取s_name
            g_id = request.POST.get('g_id')
            #  获取班级信息
            grade = Grade.objects.filter(id=g_id).first()
            
            #此对象过滤条件为学生表里的ID和请求传输的ID一致
            #这样就得到了对应的学生信息
            # Student.objects.create(s_name=s_name, g_id=grade.id)/
            Student.objects.create(s_name=s_name, g=grade)
            #创建学生信息
    
            # stu = Student(s_name=s_name, g=grade)
            # stu.save()
            return HttpResponseRedirect(reverse('app:student'))
    ```

    

