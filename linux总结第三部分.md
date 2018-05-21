#### 映射 

inoremap在(insert)插入模式使用

vnoremap在visual模式下使用

nnoremap在normal模式下使用

这样可以减少快捷键所用到的键位组合的个数 

```shell
inoremap pymain if__name__ == '__main__': 
#当输入 pymain 后 可以映射出  if__name__ == '__main__':  在插入模式下
```

在写Python时  开头一般写入

> #!/usr/bin/python  
>
> #!是一个约定标记？用于告诉系统 需要什么解释器来执行
>
> 运行文件使用 ./  因为没有配置软连接  所以系统会在默认PATH里面找 并不能找到   需要你告诉系统 运行的是 当前路径下的  ./hello.sh·



```markdown
脚本语言的第一行，目的就是指出，你想要你的这个文件中的代码用什么可执行程序去运行它

#!/usr/bin/python是告诉操作系统执行这个脚本的时候，调用/usr/bin下的python解释器。

#!/usr/bin/env python这种用法是为了防止操作系统用户没有将python装在默认的/usr/bin路径里。当系统看到这一行的时候，首先会到env设置里查找python的安装路径，再调用对应路径下的解释器程序完成操作。这种写法会去环境设置寻找python目录

```

#### [vim指令部分](https://blog.csdn.net/berg_1994/article/details/80372548)

#### 查看进程

+ ps
+ ps -aux
+ ps -df 显示进程
+ top
+ netstat -na 查看网络状态 后面加 p  可以查看进程号



#### 设置防火墙 开端口

>  Nginx是一款[轻量级](https://baike.baidu.com/item/%E8%BD%BB%E9%87%8F%E7%BA%A7/10002835)的[Web](https://baike.baidu.com/item/Web/150564) 服务器/[反向代理](https://baike.baidu.com/item/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86)服务器及[电子邮件](https://baike.baidu.com/item/%E7%94%B5%E5%AD%90%E9%82%AE%E4%BB%B6)（IMAP/POP3）代理服务器 

###### 安装 nginx

```markdown
yum list installde  查看已经安装了什么东西
yum list installed | grep nginx  查看有没有安装 nginx
yum search nginx 看看有没有转过nginx
yum update nginx 更新nginx 
yum install nginx 安装
nginx 执行

```

##### 反向代理 

有很多后台服务器，所以会查找最近的最快的服务器

#### 打开端口

1. 登陆阿里云控制台
2. 选择实例
3. 进入本实例安全组
4. 内网入方向全部规则
5. 配置规则
6. 添加安全组
7. 选择HTTP（80）
8. 设置授权对象 0.0.0.0/0

网站内容地址   /usr/share/nginx/html 

相关配置 /etc/nginx/nginx.conf

#### Xftp

用于在Xshell里上传文件

在 xshell 里面点击添加文件，从左边拉倒右边就可以了

如果放进去没有反应，在xshell里执行  nginx -s reload  重载页面



#### 查看服务器状态

命令： ps 或者 ps -aux 或者 ps -ef

> ps -aux | grep nginx  添加管道查看nginx进程
>
> netstat -nap  查看网络状态 查看进程号
>
> netstat -nap | grep 80 查看网络duan 



#### yum、rpm

```
yum update 更新yum 本身具体某个带名字
yum install <filename> 安装缺失依赖项
list 显示全部
```

```
rpm 红帽子的包管理工具
-e erase 删除
-i install 移除
-vh 查看安装过程
rep -ivh <filename> 安装文件
rpm -qa(查询所有) | grep <filename>
rpm -e <filename>删除文件
rpm -qa | grep <filename> | xargs rpm -e  搜索到的多个文件都删除
把刚才通过管道过滤的内容当做参数 xargs
```

#### mariadb 

```markdown
yum install mariadb-server mariadb  
安装mariadb

systemctl start mariadb  
启动服务

systemctl stop mariadb
暂停服务

ps -ef | grep mysqld
ps -aux | grep mysqld
mysqld 此处 Daemon 守护进程

systemctl enable start mariadb
把 enable 改为 disable
netstat -nap | grep 3306 端口3306

```

#### 配置防火墙

```markdown
system start firewalld 开启防火墙
friewall-cmd --zone=dmz --add-port=8080/tcp
或
firewall-cmd --add-port=80/tcp --permanent --zone=public

```

```
[root@izwz91gdcp8n9k7p4zzb7dz ~]# firewall-cmd --zone=dmz --add-port=8080/tcp
success
```

如果是centos 6 或者更早的版本 

可以写 service firewalld start   

查看状态 service firewalld status 或 systemctl status firewalld

chkconfig centOS6  配置开启自启

chkconfig -list 查看需要配置



##### tar.gz/tar.xz

	- src 源代码 -make && make install 源代码构建安装



export PATH =$PATH:/root 文件名 export 导出

注册环境变量  此变量是临时的  当重启服务器后就没了

```markdown
防火墙配置 
打开防火墙设置：
vi /etc/sysconfig/iptables 

-A INPUT -m state –state NEW -m tcp -p tcp –dport 80 -j ACCEPT（允许80端口通过防火墙） 
-A INPUT -m state –state NEW -m tcp -p tcp –dport 3306 -j ACCEPT（允许3306端口通过防火墙） 
特别提示：很多网友把这两条规则添加到防火墙配置的最后一行，导致防火墙启动失败，正确的应该是添加到默认的22端口这条规则的下面 
a.添加.允许访问端口{80: http}. 
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
b.关闭防火墙{不推荐}. 
service iptables stop 
c.重置加载防火墙 
service iptables restart

# Firewall configuration written by system-config-firewall 
# Manual customization of this file is not recommended. 
*filter 
:INPUT ACCEPT [0:0] 
:FORWARD ACCEPT [0:0] 
:OUTPUT ACCEPT [0:0] 
-A INPUT -m state –state ESTABLISHED,RELATED -j ACCEPT 
-A INPUT -p icmp -j ACCEPT 
-A INPUT -i lo -j ACCEPT 
-A INPUT -m state –state NEW -m tcp -p tcp –dport 22 -j ACCEPT 
-A INPUT -m state –state NEW -m tcp -p tcp –dport 80 -j ACCEPT 
-A INPUT -m state –state NEW -m tcp -p tcp –dport 3306 -j ACCEPT 
-A INPUT -j REJECT –reject-with icmp-host-prohibited 
-A FORWARD -j REJECT –reject-with icmp-host-prohibited 
COMMIT 

[root@centos httpd]# /sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
[root@centos httpd]# /etc/rc.d/init.d/iptables save
[root@centos httpd]# /etc/init.d/iptables restart
```



