[TOC]

### `cookie`

```
首先我们要知道HPPT的无状态特点，HTTP的无状态是值HTTP协议对事物处理没有记忆能力，即服务器不知道客户端是什么状态。我们提交请求，服务器解析请求，返回响应，整个过程是完全独立，服务器不记录前后状态的变化。当后续是需要处理前面信息，则必须重传。


Cookie在客户端，即浏览器端。当客户端第一次请求服务器时，服务器返回一个请求头中带有Set_Cookie字段的响应给客户端，用以表明是哪个用户，浏览器会把cookie保存，当下一次请求该网站时，浏览器会把cookie放到请求头一起提交给服务器，cookie携带会话ID，服务器查看cookies就可以找到对应的会话，凭借会话判断用户状态
如果cookies无效或者过期 就只能重新登录


cookie一般包含:name,value,domain(允许访问cookie的域名)，max_age,path(该cookie使用的路径，/path/)，size(cookie大小 4KB上限)等


会话cookie是吧cookie放在浏览器内存，关闭浏览器后失效
持久cookie是保存在客户端的硬盘
实则上cookie的寿命还是应该由max_age或expires字段决定


```

###`session`

```
session和cookie的作用有点类似，都是为了存储用户相关的信息。不同的是，cookie是存储在本地浏览器，而session存储在服务器。
session把用户的信息保存在服务器上面， 浏览器第一次访问的时候服务器把sessionID传递到浏览器，然后浏览器把Session_id保存在cookie中， 每次访问把session_id带上，服务器就可以标识这个请求来自于那个用户，然后根据session_id查这个这个用户的seesion里面记录了哪些数据.
存储在服务器的数据会更加的安全，不容易被窃取。但存储在服务器也有一定的弊端，就是会占用服务器的资源.

  当程序需要为某个客户端的请求创建一个session时，服务器首先检查这个客户端的请求里是否已包含了一个session标识（称为session id），如果已包含则说明以前已经为此客户端创建过session，服务器就按照session id把这个session检索出来使用（检索不到，会新建一个），如果客户端请求不包含session id，则为此客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，这个session id将被在本次响应中返回给客户端保存。保存这个session id的方式可以采用cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。
  到当前请求再次发出后，该 SessionID会随着 Http 头部，传到服务器中，服务器依据当前 SessionID 得到与之对应的 Session.
  
  通过 Cookie 的方式存储 Session 状态，只是其中一种方式。如果客户端禁用了 Cookie 的话，很多网站任然可以存储用户的信息。一种处理的方式是URL 重写，将 SesseionID 直接附加在请求地址的后面。另一种处理的方式是，使用隐藏自动的方式。就是服务器自动的在表单中，添加一个隐藏字段，以便在表单提交时，将 SesseionID 一起传到服务器，进行识别。
  


```

#### 区别

```
1.Cookie是存在客户端，Session存在服务器
2.安全性要求高的用Session，要求低用Cookie
3.Cookie只能存储字符串，Session可以存储任何信息
4.Cookie寿命有max_age或expires决定，session是会话结束则结束（接挂电话，开关浏览器）
5.cookie 受限于大小(4KB)和个数（20个？）

```

#### 使用建议

```
将登陆信息等重要信息存放为SESSION
其他信息如果需要保留，可以放在COOKIE中
```

