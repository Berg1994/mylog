在Xshell 里  

#是超级管理员  

$是普通管理员



#### 创建普通用户

adduser 创建普通用户命令

passwd 改账户密码

> 如果后面加了用户名 则是修改当前账户密码，密码没有回显，需一次性完成

who 查看用户从哪来

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ who
root     pts/0        2018-05-13 22:25 (182.148.33.14)
Berg     pts/1        2018-05-13 22:26 (182.148.33.14)
```



w 查看用户信息

whoami / who am i 查看我是谁 

> who 是命令 am 和 i 是2个参数

exit 登出服务器

reboot 重启服务器 （Xshell的快捷键 alt + c也行）

shutdown 关闭服务器

init 0 关机（需在控制台开启）

init 6 重启服务器 

uname 查看用的什么系统

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ uname
Linux
```



制表键 2下 可以查看有多少命令

```she
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ 
Display all 1283 possibilities? (y or n)
```

#####  输入不完整时 敲一下制表键  会自动补全  如果无法补全需再敲一次查看有哪些



### 命令说明

+ 如果不知道命令是干嘛的  前面加个man

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ man pwd

PWD(1)                               User Commands                               PWD(1)

NAME
       pwd - print name of current/working directory

SYNOPSIS
       pwd [OPTION]...

DESCRIPTION
       Print the full filename of the current working directory.

       -L, --logical
              use PWD from environment, even if it contains symlinks

       -P, --physical
              avoid all symlinks

       --help display this help and exit

       --version
              output version information and exit

       NOTE:  your  shell may have its own version of pwd, which usually supersedes the
       version described here.  Please refer to your shell's documentation for  details
       about the options it supports.
 Manual page pwd(1) line 1 (press h for help or q to quit)
```



+ 或者命令后加 --help

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ who --help
Usage: who [OPTION]... [ FILE | ARG1 ARG2 ]
Print information about users who are currently logged in.

  -a, --all         same as -b -d --login -p -r -t -T -u
  -b, --boot        time of last system boot
  -d, --dead        print dead processes
  -H, --heading     print line of column headings
  -l, --login       print system login processes
      --lookup      attempt to canonicalize hostnames via DNS
  -m                only hostname and user associated with stdin
  -p, --process     print active processes spawned by init
  -q, --count       all login names and number of users logged on
  -r, --runlevel    print current runlevel
  -s, --short       print only name, line, and time (default)
  -t, --time        print last system clock change
  -T, -w, --mesg    add user's message status as +, - or ?
  -u, --users       list users logged in
      --message     same as -T
      --writable    same as -T
      --help     display this help and exit
      --version  output version information and exit

If FILE is not specified, use /var/run/utmp.  /var/log/wtmp as FILE is common.
If ARG1 ARG2 given, -m presumed: 'am i' or 'mom likes' are usual.

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
For complete documentation, run: info coreutils 'who invocation'
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ 
```

+ 再或者用info 加 命令  <------------  专业解释 不建议普通用时使用

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ info who

File: coreutils.info,  Node: who invocation,  Prev: users invocation,  Up: User informati\
on

20.6 'who': Print who is currently logged in
============================================

'who' prints information about users who are currently logged on.
Synopsis:

     who [OPTION] [FILE] [am i]

   If given no non-option arguments, 'who' prints the following
information for each user currently logged on: login name, terminal
line, login time, and remote hostname or X display.

   If given one non-option argument, 'who' uses that instead of a
default system-maintained file (often '/var/run/utmp' or
'/var/run/utmp') as the name of the file containing the record of users
logged on.  '/var/log/wtmp' is commonly given as an argument to 'who' to
look at who has previously logged on.

   If given two non-option arguments, 'who' prints only the entry for
the user running it (determined from its standard input), preceded by
the hostname.  Traditionally, the two arguments given are 'am i', as in
--zz-Info: (coreutils.info.gz)who invocation, 104 lines --Top-----------------------------
Welcome to Info version 5.1. Type h for help, m for menu item.
```

需要简单了解命令或者软件某干嘛的

可以使用 whatis 命令

如 whatis python

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ whatis python
python3 (1)          - an interpreted, interactive, object-oriented programming language
python (1)           - an interpreted, interactive, object-oriented programming language
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ whatis passwd
passwd (1)           - update user's authentication tokens
```

whereis  是可以帮助你找到位置的

```markdown
[Berg@izwz91gdcp8n9k7p4zzb7dz ~]$ whereis python
python: /usr/bin/python /usr/bin/python2.7 /usr/lib/python2.7 /usr/lib64/python2.7 /etc/python /usr/include/python2.7 /usr/local/python3.6 /usr/share/man/man1/python.1.gz
```

pwd查看当前位置/ 查看目录

/root  根目录 超级管理员用户主目录？？？

su + 用户名 可以切换到其他用户身份？？？



#### 文件和文件夹操作

ls -list 查看列表

```markdown
[root@izwz91gdcp8n9k7p4zzb7dz ~]# ls -list
total 249876
132669      4 -rw-r--r--  1 root root       279 May 11 17:20 test01.py
132668      4 -rw-r--r--  1 root root        77 May 11 14:35 dump.rdb
```



ls -l 长格式 

```markdown
[root@izwz91gdcp8n9k7p4zzb7dz ~]# ls -l
total 249876
-rw-r--r--  1 root root        77 May 11 14:35 dump.rdb
drwxr-xr-x  9 root root      4096 May  9 16:51 etc
```

ls -a  查看所有(这样才能查看到带 .的隐藏文件)

```markdown
[root@izwz91gdcp8n9k7p4zzb7dz ~]# ls -a
.
..
.bash_history
.bash_logout
.bash_profile
.bashrc
.cache
.cshrc
dump.rdb
etc
```

命令可以叠加运用  ls -a -l  / ls -al



ls --help 查看参数

ls -- help |less 分页显示

ls -R 递归显示所有 （显示文件夹的文件  层层显示）

ls -r 反序显示

cd  /root 写的绝对路径的根目录

cd  .. （一个表示当前路径， 2个点表示上一级路径）

cd ../.. 返回上一级的上一级

cd /home/Berg(在前面加斜杠 表示绝对路径  直接写表示相对路径)

cd ~ 回到主目录

cd / 到根目录

bin 是一个连接  连接到uer/bin 系统文件 类似 windows 下的programfiles

boot 启动

dev 设备

查看文件夹 ls   cat 查看文件

etc 所有软件的配置文件

home 其他用户的主目录

lib 库文件

bus 系统总线



#### 创建文件和文件夹

touch 创建文件 （如果已经创建 则修改其创建时间）

mkdir 创建文件夹

rmdir 删除文件夹（只能删除空文件夹）

mkdir/rmdir -p  则可以递归创建/删除 文件夹下的文件夹。。。。必须是空的



rm 可以删除文件夹/文件  默认 -i 交互式删除  

rm -f  非交互式删除（强删）

```mathematica
rm -rf 递归式删除 （）
    rm -f -force
    rm -i interactive
    rm - index* 通配符表示删除这个开头的所有
```

cp-拷贝copy 

history  查看你执行过的命令

> 再用 ！+ 命令编号 就可以再执行一次 

mv -move 剪切（可以移动文件和文件夹
也可以重命名文件（即在同一文件夹下 直接输入 原文件名和新文件名））

wget 获取文件   用于下载资源  

```markdown
[root@izwz91gdcp8n9k7p4zzb7dz ~]# wget http://www.qq.com
--2018-05-13 23:09:21--  http://www.qq.com/
Resolving www.qq.com (www.qq.com)... 180.163.26.39, 240e:e1:8100:28::2:16
Connecting to www.qq.com (www.qq.com)|180.163.26.39|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘index.html’

    [ <=>                  ] 248,362     --.-K/s   in 0.1s    

2018-05-13 23:09:21 (1.82 MB/s) - ‘index.html’ saved [248362]

```



查看下载的网页源代码  cat index.html |more /less  |是管道过滤器



管道过滤器后面写 head -5 index.html 只查看头5行 看尾巴 tail -5 index.html

grep -索引 查找文件里面的内容

cat index.html | grep '<div .*>'
cat index.html | grep \<div.*>\
给文件内容后再加个正则
或者
grep '<div .*>' index.html -n (-n可以告诉你索引的行数)
grep '<div .*>'  -R -n > (大于号 输出重定向) resulet.txt（表示报告到文本保存） &(&表示在后台执行)
grep '<div .*>' / (斜杠表示更目录索引) -R -n > resulet.txt 2> error.txt & (2> 表示错误信息写到一个叫error的文本里面去) 

>> (一个大于号是输出重定向（并且是覆盖式的）  2个叫追加输出重定向（追加在文本后面)
>> jobs -查看有没有在执行的后台任务
>> fg % 加查看job是的编号 就可以放到前台
>> bg % 加编号  就可以继续放到后台



find 查找某文件
-p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！
如 mkdir abc/def/ghi 无法查找
但是可以写 mkdir -p abc/def/ghi

wc- wordcount 数单词数的 -l 多少行 -w 多少个字
uniq - 去除重复的行数 如果不相邻可能无法去重
sort -排序 (不会改变原文件)
diff (different) 版本比较 
比较2个文件  
file 分析文件是什么性质 不会因为文件后缀误判 分析的数据
如 file index.html