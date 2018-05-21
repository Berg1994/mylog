echo -回声命令 

echo "print('hello')" > hello.py  通过回声 然后重定向 创建文件

定义变量后  通过echo $ 变量名 可以取到对应的值

```
[root@izwz91gdcp8n9k7p4zzb7dz ~]# a=2
[root@izwz91gdcp8n9k7p4zzb7dz ~]# echo $a
2
```

通过取变量的值 可以计算

```
[root@izwz91gdcp8n9k7p4zzb7dz ~]# a=2
[root@izwz91gdcp8n9k7p4zzb7dz ~]# b=3
[root@izwz91gdcp8n9k7p4zzb7dz ~]# echo $((a + b))
5
```

echo $HISTSIZE

```
[root@izwz91gdcp8n9k7p4zzb7dz ~]# echo $HISTSIZE
1000
```

userdel 删除用户

wall ‘string’  超级管理员发警告

scp + 文件名 + 用户名@IP地址:/home/hellokitty/ 安全拷贝

ssh 用户名@ip地址 输入passwd后可以登录到其他用户

ln-link-硬链接  相当于备份  相当于堆栈的引用链接 

多个链接对应同一个硬盘存储 只要有链接在  内容就不会丢失

ln -s 软链接 多用于配环境变量 



### 解压缩文件

压缩文件 .gz .xz 较多

+  .xz  
+ 压缩文件   xz -z \<filename>
+ 压缩文件   xz -d \<filename>
+ xz -z -9 \<filename>    -9是压缩比（最高为9  默认为6 压缩比越高 压缩时间越长）



+ .gz
+ 解压文件  gunzip



+ .tar 归档文件（把多个文件合成一个文件夹）
+ tar -xvf    v(查看过程)  f(文件名) x(解归档)
+ 查看解归档文件 -tf
+ 两者都需要加 -f  因为-f是指定文件名
+ tar -cvf \<filename>  归档   文件名后缀为.tar  
+ tar -cvf \<filename> /hellokitty/*   范围为hellokitty 下所有文件 



wget -o \<newfilename>  + 链接  可以对文件重命名



**alias 别名**  格式  alias 别名 = ‘原方法名’

解除别名  unalias + 别名

```
[root@izwz91gdcp8n9k7p4zzb7dz ~]# alias rm='rm -rf'
[root@izwz91gdcp8n9k7p4zzb7dz ~]# ls
dump.rdb       homework03.py  myredis       test01.py
etc            homework2.py   myredis.conf  test01.sh
hello.py       homework4.py   myredis.log   test03.py
hellotoday.py  index.html     Python-3.6.5  usr
homework01.py  mylog          redis-3.2.11  yum-3.4.3
[root@izwz91gdcp8n9k7p4zzb7dz ~]# rm mylog
[root@izwz91gdcp8n9k7p4zzb7dz ~]# ls
dump.rdb       homework03.py  myredis.conf  test01.sh
etc            homework2.py   myredis.log   test03.py
hello.py       homework4.py   Python-3.6.5  usr
hellotoday.py  index.html     redis-3.2.11  yum-3.4.3
homework01.py  myredis        test01.py
[root@izwz91gdcp8n9k7p4zzb7dz ~]# unalias rm
```



df 查看磁盘 = fdisk 

### -rw-r--r--

#### -rw- 所有者 

r 表示read 

w 表示write 

x 表示 execute 可执行

#### r--同组用户

#### r-- 其他用户

chmod u+x \<filename>   u表示user x 表示可执行  

chmod o+x , g+x  o 表示other 其他用户group 用户组 + 文件名

执行文件  ./\<filename> 执行当前目录下的

chmod 777 guess.py    所有人都能读写执行

 7是二进制  111 是421的和 

766 是110  只能读写不能执行 744 只能读

echo $ PATH path 环境变量

```
[root@izwz91gdcp8n9k7p4zzb7dz ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

ln -s  ~/hellotoday.py  /usr/bin/shit  绑一个软链接 当执行搜索时直接就能运行  

绑定要写绝对路径  最后是文件名 可以更改名字



2to3  python2代码转成python3

2to3 -w 直接改

quit() 退出

