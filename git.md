```markdown
git  -由林纳斯两周内就做出来了-分布式版本控制系统git
git 适合社会化编程
Subversion--svn 集中式控制器系统-版本控制服务器
安装的时候把 Use True font in all consile windows 字体文件和中文显示

cd Desktop 来到目录
mkdir hello 创建文件
cd hello 来到文件
git init 初始化‘
notepad Readme.txt 写文件
git add Readme.txt 纳如版本控制
git add  . 把当前所有东西都纳入版本控制放到暂存区的
git commit -m ‘写了三行诗’


邮箱名字相当于是工号辨识，好生写
git config --global [图片]user.name ''asen'
git config --global user.email "634163114@qq.com

git status  查看状态
git commit -m ‘写修改原因
git log 是显示版本信息详情日志
git reset --hard  版本号  回到版号去的版本
又再回到之前的版本
git reflog 版本号
git reset --hard  版本号

’

修改了 就git add.   
 git status 查看状态
git checkout --  撤回暂存区的所有东西
git log --pretty=oneline  单行显示状态


如果是公司上班
gitlab 搭建服务器
也可以用现在现有的服务器 github

比如：coding
git clone 项目地址
如果是私密仓库
就要用户名 密码

修改了东西后
git add .
git commit -m '项目初始版本'
git log 查看版本
git remote add origin 远端仓库地址  
git push origin master (origin项目上别名 不用改 master是主分支) 
把文件推送到分支上
如果在服务器上有更新：
 git pull 把服务器更新的再拉回到本地
自己代码和服务器同步 


把本地项目推到仓库去
git remote add origin 远端仓库地址  
git push -u origin master 第一次提交 把本地项目和远端origin对应

修改了文件
git push master
git pull 同步代码？？
git pull origin master  加上链接 把这个地址拉下来

本地建仓库 再托管到远端服务网
mkdir hello
git init
git add .
git status 
git commit -m "xz" 提交到本地仓库说明
git log
git reset --hard id版本
git remote add origin <url>

git push -u origin <url>
git push -u origin master 
git pull   拉下来


如果远端服务器已经存在
git clone (url)
cd hello
git add .
git checkout --
git commit -m 'abc'
git push origin master
git pull


git pull

上
进行操作

公司一般会让我新建一个分支，而不会在master

git branch 名字 创建新的分支
git branch
git checkout cooll-fuction 来到这个分支
git branch 查看

git rm example.html 移除这个
git push origin 分支名

先本地提交

git push


把所有的分支合并的话：
在主分支下 git merge cool-funtion  
git push origin master 




git clone 链接
cd 文件
git branch 名字
git checkout 名字
git add .
git rm 文件名  如果要删除文件
git commit -m 'xyz'
git push origin 名字

再切回主分支
git checkout master
git merge 名字   把新分支合并到master上
git push origin master 




markdown 导出html 复制代码就是网页代码
jekyll可以生成markkdown  静态页面
hexo 也可以生成静态页面 
写博客 写文章 --自我营销 
bootstrap里面
Hexo推荐用这个 
```

