[TOC]

#### 商品的增删

```python
#1.创建一个新的 js  functions.js
#实现当点击添加减少时，增加或减少购物车信息
onclick addgoods({{ goods.id }})
定义点击事件  附带的参数为goods的id

#2.定义url映射  添加购物车
url（r'^addtocart/',views.add_to_cart,name='add_to_cart'）,
#写对应视图
"""
获取request里的 user 如果有 获取goods_id
验证当前登录用户是否对统一商品进行添加
获取cart 实例对象 判断user 和good_id
如果有则  数量加1
没有 则创建对应商品
"""

def add_to_cart(request):
    if request.method == 'POST':
        #如果用户登录则必然有user值通过中间件赋值给request
        user = request.user
        #通过onclick 点击事件获取对应的id值后   ajax 传到后端  等待接收响应
        goods_id = request.POST.get('goods_id')
		#类似reseful的端口返回格式
        data = {
            'code': 200,
            'msg': '请求成功',
        }
		#此处user不能直接判断因为user可以是AnonymousUser  如：
        '''
        request.user.username
        '' #空值
        request.user
        <SimpleLazyObject: <django.contrib.auth.models.AnonymousUser object at 0x054024B0>>
        request.user.id #空值

        '''
        # 所以我们可以通过如 id，username 等值来判断
        if user.id:
            #通过商品id 和用户 来确定购物车对应数据 创建实例  这里是集合 取第一个
            cart = CartModel.objects.filter(user=user,
                                            goods_id=goods_id).first()
			
            #判断添加商品逻辑  当有商品时，商品数量加1 没有这个对应商品实例时 创建该实例
            if cart:
                cart.c_num += 1
                data['c_num'] = cart.c_num
                cart.save()
                return JsonResponse(data)

            else:
                cart = CartModel.objects.create(user=user,
                                                goods_id=goods_id)
                cart.c_num = 1
                data['c_num'] = cart.c_num
                cart.save()
                return JsonResponse(data)
        data['code'] = 1001
        data['msg'] = '用户未登录'
        return JsonResponse(data)


#用接口形式返回
data['code]
data['msg']
data['c_num']

#思路 通过html -+键绑定点击事件，编辑对应的ajax
如
#此处传入的id 为点击事件对应的商品id 
# ajax在点击时间后把request带data传递给后端后，卡在判断等后端返回值再返回给客户端
function goods_add(id) {
    var csrf = $('input[name="csrfmiddlewaretoken"]').val();
    $.ajax({
        url: '/axf/addtocart/',
        data: {'goods_id': id},
        type: 'POST',
        headers: {'X-CSRFToken': csrf},
        dataType: 'json',
        success: function (data) {
            if (data.code == '200') {
                $('#goods_' + id).html(data.c_num)
                get_count_price()
            }
        },
        error: function (data) {
            alert('请求失败')

        }
    });
}
```

#### csrf值获取

```python
 var cast = $('input[name="csrfmiddlewaretoken"]').val();
    
 #html 代码位置
 {% csrf_token %}
    
 #源代码位置 
<input type="hidden" name="csrfmiddlewaretoken" value="QmmH8h5WEtN17FeHKjPvHPKdDJUXQjJjgaJ6HUUR3IlS7WdC5aSMBCTeEzl8DNVp">

#通常使用在ajax 赋值headers 如：
headers: {'X-CSRFToken': csrf},

```

###  购物车点击刷新商品数量方法

#### 通过后端传值获取数据

```python
"""
方法1：ajax 用get方法发送request请求 ，url映射对应视图 返回值传递给页面
"""
$(function () {
    $.get('/axf/goodsnum/', function (data) {
        if (data.code == '200') {
            for (i = 1; i < data.carts.length; i++) {
                $('#goods_' + data.carts[i].goods_id).html(data.carts[i].c_num)
            }
        }
    })
});


# 此处的cart 就是多种购物车里的商品 所以一般有多个
def goods_num(request):
    if request.method == 'GET':
        user = request.user
        cart_list = []
        if user.id:
            #通过user去获取用户下的购物车（商品） 用户可能有多个购物车（商品）
            carts = CartModel.objects.filter(user=user)
            #遍历对每个购物车（商品）取值
            for cart in carts:
                data = {
                    'user_id': cart.user.id,
                    'goods_id': cart.goods_id,
                    'c_num': cart.c_num,
                }
                cart_list.append(data)
            return JsonResponse({'carts': cart_list, 'code': 200})
        else:
            return JsonResponse({'carts': '', 'code': 1001})
        
        
 
```





#### 模型反向找数据 

```python
#通过 一对多的查找方式  _set 查找所有关联值
<!--{% if good.cartmodel_set.all %}-->
#遍历判断购物车（商品）
	<!--{% for cart in good.cartmodel_set.all %}-->
    #如果购物车（商品）值的用户和 当前页面的user一致 则取购物车（商品）数量
		<!--{% ifequal cart.user user %}-->
			<!--{{ cart.c_num }}-->
		<!--{% endifequal %}-->
	<!--{% endfor %}-->
<!--{% else %}-->
#否则值就是0
	<!--0-->
<!--{% endif %}-->
```

### 购物车

```python
"""
整个购物车其实没有表示所有商品的实例，比如像订单，这里的cart其实指的是在购物车里的商品 
购物车模型包含 is_select,c_num,goods_id,user_id,id 几个属性 整个购物车的实现逻辑基本也在此，
前端给个判断值如id， ajax 请求带上值，后端根据值找到对应的实例，返回实例， 前端取值填坑展示页面
"""
#1.增添删减逻辑：
"""
先通过请求实例取到good_id 和 user ，通过获取的值筛选数据库获取对应的购物车实例，
判断是否有该实例，如果有 则购物车c_num + 1   没有 则创建对应的购物车实例 默认c_num 会为1 
返回HttpResponse实例
"""
#如:
def sub_to_cart(request):
    if request.method == 'POST':
        user = request.user
        goods_id = request.POST.get('goods_id')

        data = {
            'code': 200,
            'msg': '请求成功',
        }

        if user.id:
            cart = CartModel.objects.filter(user=user,
                                            goods_id=goods_id).first()
            if cart:
                if cart.c_num == 1:
                    cart.delete()
                    data['c_num'] = 0
                    return JsonResponse(data)
                else:
                    cart.c_num -= 1
                    cart.save()
                    data['c_num'] = cart.c_num
                    return JsonResponse(data)

            data['code'] = 1002
            data['msg'] = '添加商品'
            return JsonResponse(data)
        data['code'] = 1001
        data['msg'] = '账户未登录'
        return JsonResponse(data)




```

##### 商品是否被选择

```python
#是否选择商品就是更改购物车（商品）的is_select属性  当值为1时 表示被勾选， 当值为0时表示没有被勾选
#所以视图逻辑只要是对is_select的值进行更改 通过点击事件获取请求 如果为T 改F  逻辑变反就行

function change_cart_status(id) {
    var csrf = $('input[name="csrfmiddlewaretoken"]').val();
    $.ajax({
        url: '/axf/changecartstatus/',
        data: {'cart_id': id},
        type: 'POST',
        dataType: 'json',
        headers: {'X-CSRFToken': csrf},
        success: function (data) {
            if (data.code == '200') {
                if (data.cart_status) {
                    s = '√'
                } else {
                    s = 'x'
                }
                $('#cart_status_' + id).html(s)
                get_count_price()
            }
        },
        error: function (data) {
            alert('请求失败')
        }
    })
}



def change_cart_status(request):
    if request.method == 'POST':
        cart_id = request.POST.get('cart_id')
        cart = CartModel.objects.filter(id=cart_id).first()
        if cart.is_select:
            cart.is_select = False
        else:
            cart.is_select = True
        cart.save()

        return JsonResponse({'cart_status': cart.is_select, 'code': 200})
    
    
    
```

#### 全选/全部选

```

```

#### 商品总价

```python
"""
后端逻辑
通过cart.good_id.price 获取单个商品的价格 在 * cart.c_num  购物车（商品）数量

"""

def goods_count(request):
    if request.method == 'GET':
        user = request.user

        carts = CartModel.objects.filter(user=user,
                                         is_select=True)
        count_prices = 0
        for cart in carts:
            count_prices += cart.goods.price * cart.c_num

        count_prices = round(count_prices, 3)
        return JsonResponse({'count': count_prices, 'code': 200})
    
    
    function get_count_price() {
    $.get('/axf/goodscount/', function (data) {
        if (data.code == '200') {
            $('#all_price').html('总价：' + data.count)
        }
    });
}

get_count_price();
#把 get_count_price(); 放到添加删减的函数下  实现通过添加删减商品个数总价的变化

```









### 小坑（商品数量添加）

```markdown
由于有多个商品 即多个span 对对应商品返回值显示位置的id需要唯一的  即给span添加一个唯一的id 
方法： id="goods_{{ good.id }}" 这样获取的id就是唯一值 ajax返回时不会出错 
在ajax $('#good_' + id).html(data.c_num)
```

#### `JsonResponse`

```
JsonResponse 对象：

class JsonResponse(data, encoder=DjangoJSONEncoder, safe=True, json_dumps_params=None,**kwargs)

这个类是HttpRespon的子类，它主要和父类的区别在于：

1.它的默认Content-Type 被设置为： application/json

2.第一个参数，data应该是一个字典类型，当 safe 这个参数被设置为：False ,那data可以填入任何能被转换为JSON格式的对象，比如list, tuple, set。 默认的safe 参数是 True. 如果你传入的data数据类型不是字典类型，那么它就会抛出 TypeError的异常。

3.json_dumps_params参数是一个字典,它将调用json.dumps()方法并将字典中的参数传入给该方法。
```



