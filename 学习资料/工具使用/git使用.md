# [Git如何fork别人的仓库并作为贡献者提交代码](https://www.cnblogs.com/javaIOException/p/11867988.html)

例如 要fork一份google的MLperf/inference代码，下面介绍具体做法：
预备知识
git里的参考有几种表示，分别是上游仓库，远程仓库和本地仓库，逻辑关系如下
拉取代码的顺序：
别的大牛的代码(上游仓库)---------->你fork的代码(远程仓库)---------->你电脑的代码(本地仓库)
提交代码的顺序：
别的大牛的代码(上游仓库)<----------你fork的代码(远程仓库)<----------你电脑的代码(本地仓库) 
每个仓库主分支是master，还可以有其它分支
上游仓库的表示为 upstream，远程仓库表示为origin

具体步骤
1、进入MLperf/inference仓库，点击fork按钮，拷贝代码到自己的git账号的仓库里

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170540249-1521549965.png)

 

2、之后再自己的账号里就可以看到fork的代码了，可以看到代码的fork来源，即上游仓库地址

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170556693-1274084019.png)
3、下载代码到你的本地电脑里，windows可以使用GitBash工具类似linux命令行的操作
下面是在本地电脑里进行的操作：
\# 创建inference目录
$ mkdir inference
\# 切换到inference目录
$ cd inference
\# 创建并初始化git库
$ git init
\# 添加远程git仓库
$ git remote add origin https://github.com/yananYangYSU/inference.git
\# 添加SSH秘钥到git远程库，邮箱可以从git账号里查看
$ ssh-keygen -t rsa -C "1131074225@qq.com"

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170607881-702715715.png)

\# 查看秘钥
cat ~/.ssh/id_rsa.pub

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170620928-894115837.png)

\# 复制添加到你git账号里的ssh key列表里，就可以通过安全认证传输数据了

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170628371-1103038399.png)
\# 将远程git库代码下载到本地 (origin代表远程仓库，master代表主分支)
git pull origin master

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170657490-2128316892.png)

代码下载完了之后，这时候你本地仓库和远程仓库的代码一致了，接下来对代码进行修改
4、修改本地仓库的代码并提交到远程仓库
假设我们要修改README.md文件，有两种方式可供选择：
\---------------------------------------------------------------------------------------------------------------------------
第一种方式，直接在master分支上做修改，命令如下：
\# 修改README.md
$ echo "abc" >> README.md
\# 添加要提交的临时文件
$ git add README.md
\# 提交更改
$ git commit -m 'commit'
\# 将本地仓库的修改上传到远程仓库
$ git push -u origin master

\---------------------------------------------------------------------------------------------------------------------------
第二种方式，在master分支基础上切出一个dev分支，然后在dev分支上修改，修改完成后，再将dev分支merge到master分支
\# 首先切换到dev分支
$ git checkout -b dev
$ echo "abc" >> README.md
\# 添加要提交的临时文件
$ git add README.md
\# 提交更改
$ git commit -m 'commit'
\# 将本地仓库的修改上传到远程仓库
$ git checkout master
$ git merge dev
\# 将本地仓库的修改上传到远程仓库
$ git push -u origin master
\---------------------------------------------------------------------------------------------------------------------------
注意如果再push之前，上游仓库有人做了代码修改，那么此时你本地和远程仓库的代码就不是最新的了，此时需要把上游仓库的代码合并到本地，然后再push到远程仓库
\# 添加上游仓库地址
$ git remote add upstream https://github.com/mlperf/inference.git 
\# 查看 origin 和 upstream 对应的仓库是否正确
$ git remote -v

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170712685-804244303.png)

origin对应的是自己github的地址，即yourname/project
upstream对应的是原项目的地址，即sourcename/project
\# 从上游仓库获取最新的代码合并到自己本地仓库的master分支上
$ git pull upstream master
推荐每次代码待提交前,都从原项目拉取一下最新的代码，最后再使用git push命令将改动同步到自己的Github远程仓库中：
$ git push -u origin master
5、将远程仓库的代码提交到上游仓库:
进入GitHub账号的远程仓库，此时已经能够看到刚刚从本地仓库提交的修改了，然后点击New pull request

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170729897-1220168656.png)

进入结果对比页面，如下图所示：

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170737667-1034728945.png)

可以看到从远程库的master分支向上游仓库的master分支申请提交代码
Able to merge代表你的代码与上游代码没有冲突，可以提交
然后点击 create pull request，进入下面页面：

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170754280-975824028.png)

填写注释，描述你所作的修改，然后点击右下角提交

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170801476-226808358.png)

然后进入上游仓库的地址，在上游仓库的Pull requests列表里就可以看到自己的提交请求了，等待作者审核即可


附录：
如果是google的代码，那么提交代码要同意开发者协议，此时需要注册Gmail邮箱，申请Google Individual CLA，然后使用通过验证的gmail邮箱去修改并提交代码，google的robot才会通过你的代码提交验证，不然提交的pull request申请是无法通过校验的
准备好gmail邮箱后，在github的账号里配置个人配置github邮箱

![img](https://img2018.cnblogs.com/common/1237731/201911/1237731-20191115170820889-1239767564.png)

然后在git命令行里也要设置提交代码的邮箱
git config --global user.email example@gmail.com
然后再进行步骤1