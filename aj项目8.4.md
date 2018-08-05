[TOC]

#### 发布房源

```python
#发布房源需要填写完整的房屋信息，当提交form表单后，后端视图函数应当提取相应的值，并保存到实例中。

@house_blueprint.route('/newhouse/', methods=['POST'])
@is_login
def my_newhouse():
    # 保存房屋信息，设施信息
    house_dict = request.form

    house = House()
    house.user_id = session['user_id']
    house.price = house_dict.get('price')
    house.title = house_dict.get('title')
    house.area_id = house_dict.get('area_id')
    house.address = house_dict.get('address')
    house.room_count = house_dict.get('room_count')
    house.acreage = house_dict.get('acreage')
    house.unit = house_dict.get('unit')
    house.capacity = house_dict.get('capacity')
    house.beds = house_dict.get('beds')
    house.deposit = house_dict.get('deposit')
    house.min_days = house_dict.get('min_days')
    house.max_days = house_dict.get('max_days')
	#当同名键对中包含多个值，可以通过get_list获取
    facilitys = house_dict.getlist('facility')
    for facility_id in facilitys:
        facility = Facility.query.get(facility_id)
        # 多对多关联
        house.facilities.append(facility)
    house.add_update()

    return jsonify(code=status_code.OK, house_id=house.id)

```

#### `template.js`使用

```html
#用法是替换文本中标签的内容
#定义解析的变量ohouse
var html_script = template('script_id',{ohouse:data.house})
$('#xx').html(html_script)

{% raw %}
<script type='text/html' id='script_id'>
{{ each ohouse.image as house }}
	{{ image }}
{{ /each }}

{{ ohouse.title }}
</script >
{% endraw %}
```

#### 预约订单

```
Order 订单
House 模型
User 模型
```

#### 客户订单

```



找到我的房源信息   如果我的房源中有客户订单id  则被订

视图 GET
先获取用户id 
判断用户发布的房源
遍历house对象 获取所有房源id 
通过判断订单的房屋是否在用户发布的房源id内 获取用户的订单
Order.query.filter(Order.house_id.in_(house.ids))
遍历返回所有order 信息  字典形式

console.log(data)  在日志答应返回结果


```

#### 修改订单状态

```
使用PATCH 请求
前端传order_id
通过请求获取订单状态
获取订单id 对应对象
修改订单状态
保存

为li 下 id= order_id 在前端赋值
前端做确认接单的点击事件

需要把页面生成Li 放在点击事件之前



ajax 接收订单id和状态


重载页面  location.reload()
```

#### index 首页

```
GET方法 获取index 首页

视图函数 /hindex/ 判断用户是否登录
如果有值则在index 展示登录注册模块   否则展示用户姓名
返回轮播图 
houses = House.query.all()[:3]
houses_image = [house.index_image_url]
ajax 判断 如果有值和没值情况 返回值

```



#### 搜索功能 查询房间

```
视图函数 /search/ GET
先获取区域id
st et 开始结束时间
查询所有的房屋对象 过滤条件为区域
过滤用户自己发布的房屋  
先判断是否登录 
过滤房屋user_id ！= session['user_id'] 的房屋

```

## 搜索排序

```
ajax 获取区域信息
定义变量  tempaltes  定义id areas_script_id  {areas:}

```

#### 首页区域选择

```python
后端视图函数在创建房源时已经写过一个获取区域结果的响应
我们只需要再写一个ajax 获取这个响应返回到页面即可 
因为不同页面加载的js模块不同 所以不会存在重复引用
两者分别是newhouse.js  和 index.js
视图函数如下：

@house_blueprint.route('/area_facility/', methods=['GET'])
def area_facility():
    areas = Area.query.all()
    facilitys = Facility.query.all()

    areas_json = [area.to_dict() for area in areas]
    facilitys_json = [facility.to_dict() for facility in facilitys ]

    return jsonify(code=status_code.OK, areas=areas_json, facilitys=facilitys_json)



```

#### 选择区域后把区域信息显示在输入框

```js
  $.get('/house/area_facility/', function (data) {
        if (data.code == '200') {
            // template 第一个参数填写script的 id   第二个参数填写 返回的data 用键值对表示
            var areas_html_script = template('areas_script_id', {areas: data.areas});
            // 获取标签 填入内容
            $('.area-list').html(areas_html_script)

            $(".area-list a").click(function (e) {
                $("#area-btn").html($(this).html());
                $(".search-btn").attr("area-id", $(this).attr("area-id"));
                $(".search-btn").attr("area-name", $(this).html());
                $("#area-modal").modal("hide");
            });

        }
    })
```

#### 搜索确认 返回url

```js
在搜索按钮绑定点击事件  
在前端页面中
<a class="btn search-btn btn-theme" href="#" onclick="goToSearchPage(this);" area-id="" start-date="" end-date="">搜索</a>


如：
function goToSearchPage(th) {
    var url = "/house/search.html/?";
    url += ("aid=" + $(th).attr("area-id"));
    url += "&";
    var areaName = $(th).attr("area-name");
    if (undefined == areaName) areaName = "";
    url += ("aname=" + areaName);
    url += "&";
    url += ("sd=" + $(th).attr("start-date"));
    url += "&";
    url += ("ed=" + $(th).attr("end-date"));
    location.href = url;
```

#### 获取当前url并把响应展示到页面

```
先通过GET方法 匹配对应的url 获取页面
再用接口的形式  通过
 var search_url = location.search;
    alert(location.search);
 获取当前url的内容
 获取完整url 使用 location 即可
 
 后端视图匹配ajax 的url 返回响应
```

