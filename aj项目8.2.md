[TOC]

#### 房屋图片

```python
#首先房屋和对应的房屋图片关系是一对多，保存对应房屋的图片应先获取房屋ID，再通过请求获取提交的图片，保存图片的地址，并把图片地址绑定到对应房屋的图片属性上。
@house_blueprint.route('/house_images/', methods=['POST', 'GET'])
def house_images():
    #获取房屋ID 
    house_id = request.form.get('house_id')
    #获取对应ID的房屋对象
    house = House.query.filter(House.id == house_id).first()
    if house:
        #获取请求中提交的图片
        img = request.files.get('house_image')
        #设置保存图片的路径
        save_url = os.path.join(upload_folder, img.filename)
        #把图片保存到路劲下
        img.save(save_url)
        # 保存房屋和图片信息
        house_img = HouseImage()
        #绑定图片到对应的房屋
        house_img.house_id = house_id
        #设置图片地址
        img_url = os.path.join('upload', img.filename)
        #绑定图片地址
        house_img.url = img_url
        house_img.add_update()
        # 创建房屋首图
        if not house.index_image_url:
            house.index_image_url = img_url
            house.add_update()
        return jsonify(code=status_code.OK, img_url=img_url)

```



#### 房源详情

```python
#房屋详情一般是通过点击某一个图片进入房屋的详情页面，所以我们应该在点击时获取对应的房屋ID，然后根据ID 在服务器中查找对应的房屋信息返回给客户端。
#如： <a href="/house/detail/?id={{ house.id }}"><img src="/static/media/{{ house.image }}"></a>
#ajax中：
$(document).ready(function(){
	#通过location 可以获取url, 加上search 可以获取后面的信息
    var url = location.search
#通过对url的拼接获取对应的请求url,当匹配上这个ajax执行
    $.get('/house/house_detail/' + url.split('=')[1] + '/', function(data){
        var house_detail = template('house_detail_script', {ohouse:data.house})
        $('.container').append(house_detail)
#下面是一个轮播图函数 因为是自上而下的运行逻辑，所以需要把这个函数放到你需要轮播图片的下面
        var mySwiper = new Swiper ('.swiper-container', {
            loop: true,
            autoplay: 2000,
            autoplayDisableOnInteraction: false,
            pagination: '.swiper-pagination',
            paginationType: 'fraction'
        })
        $(".book-house").show();
    });
})


"""
后端只需要通过ID找到对应的house对象 ，把需要的属性写成键值对的形式返回就行了
"""
@house_blueprint.route('/house_detail/<int:id>/', methods=['GET'])
def house_detail(id):
    house = House.query.get(id)
    #to_full_dict是一个方法，写在model里面 作用把属性转换成键值对 
    #如：'title': self.title,  或  'user_avatar':self.user.avatar if 			self.user.avatar else '',
    return jsonify(code=status_code.OK, house=house.to_full_dict())
```

```
房屋 区域 设备  用户 订单 房屋图片关系整理

用户和订单是 一对多关系  
用户和房子是 一对多关系
订单和房子是 一对多关系
房子和房子照片是 一对多关系
地区和房子 是一对多关系
房子和设备是 多对多关系
在flask中 
一对多关系  模型 如用户和订单的查找关系
模型在多的一方必然有外键  如 user_id  
通过order.user_id  就可以获取对用的用户
而User模型中有 orders 这个relationsship 关联 
通过 模板名可以获取值 如 user.Order

```

####添加区域域名

```
获取模板所有对象，   
通过遍历 对所有对象属性转字典格式  
```

![1533125774328](C:\Users\ADMINI~1\AppData\Local\Temp\1533125774328.png)

```python
#通过模板类的方法 to_dict() 把属性转成字典格式
    def to_dict(self):
        return {
            'id': self.id,
            'name': self.name,
            'css': self.css
            
 areas_json = [area.to_dict() for area in areas]  
areas_json
[{'id': 1, 'name': '锦江区'}, {'id': 2, 'name': '金牛区'}, {'id': 3, 'name': '青羊区'}...]
   
 在ajax 中 对数据遍历取值，每次循环取出的值 通过append（）方法加入下拉列表
   for (var i = 0; i < data.areas.length; i++) {
            area_str = '<option value="' + data.areas[i].id + '">' + data.areas[i].name + '</option>';

            $('#area-id').append(area_str)
  
```







