Linux下面的shell文件内容如下：**#!** 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。

```linux
#!/bin/bash
 \# 删除30天之前的文件
 find /var/usr/nginx/logs/ -mtime +30 -type f -name \*.gz | xargs rm -f
```

定时任务：https://blog.csdn.net/capecape/article/details/78515558

同样的将上面的目录换成自己指定的目录，后面的\*.gz表示文件扩展名，-mtime后面的参数与上面Windows的相反，正数表示多少天之前的文件。将上面的内容保存成.sh并使用chmod +x 设置成可执行权限，然后放到定时任务中去执行即可。

每天凌晨三点启动删除shell:

在终端键入   crontab -e # m h dom mon dow command 0 3 * * *  /home/config/dropOldFile.sh

```
参数说明
find /temp：查找temp目录
-atime：访问时间
+30 ：30天以前
-exec ：执行其后面的命令
rm -rf {} ：删除查找到的内容，{} 代表find查找到的内容
\; ：结束符号，\用来转义
命令格式
find 对应目录 -name "文件名" -type f -mtime +n -exec rm -rf {} \;
-type
f:普通文件
d:目录
-mtime
修改时间（modify time）
-atime
访问时间（access time）
-ctime
状态变更时间（change time）
n
+n 第n天之前的，不包括第n天当天
-n 第n天到今天的，不包括第n天当天
备注： n为整数，以天为单位，0x24表示今天，1x24表示昨天；n有一位小数，以小时为单位，如0.5x24；n有两位小数，以分钟为单位，如0.55x24;
```

```
for dir in `ls ./tt`
do
  echo "删除文件：./tt/${dir}";
  `sudo rm -rf ./tt/${dir}`
done
```

```shell
#!/bin/bash
将  当前目录下所有 7 天前带 "." 的文件删除 
find ./tmp -mtime +7 | xargs ls 
echo "文件清理定时任务"
for dir in `find ./tt -mtime +7`
do
  echo "删除文件：${dir}";
  `sudo rm -rf ${dir}`
done
```

```shell
#以指定格式输出7天前的日期
date -d "7 days ago" "+%Y_%m_%d"
```

```shell

#!/bin/bash
logs_path="/var/log/nginx";
#往前第八天的文件路径
delete_path=${logs_path}/$(date -d "7 days ago" "+%Y_%m_%d");
#昨天的文件路径
yestoday_log_path=${logs_path}/$(date -d "yesterday" "+%Y_%m_%d");
rm -rf ${delete_path}
#rm -rf ${yestoday_log_path}
cp ${logs_path}/today ${yestoday_log_path}
#清空文件
truncate -s 0 ${logs_path}/today
#mkdir -p ${logs_path}/today
#/etc/init.d/nginx reload

#3、将这个脚本加入到crontab中去，每天0点0分执行一次：

#0 0 * * * /root/log.nginx.sh

#上面这个脚本的作用就是每天凌晨0点0分的时候创建一个以昨天的日期命名的文件夹，然后把/var/log/nginx/today/这个文件夹里面的日志文件全部移动到新建的文件夹里面去。today这个文件夹每天凌晨都会被清空一次，这样就实现了nginx按天切割日志的功能了。
```

##### 1、提取文件名

```
[root@localhost log]# var=/dir1/dir2/file.txt
[root@localhost log]# echo ${var##*/}
file.txt
123
```

##### 2、提取后缀

```
[root@localhost log]# echo ${var##*.}
txt
12
```

##### 3、提取不带后缀的文件名，分两步

```
[root@localhost log]# tmp=${var##*/}
[root@localhost log]# echo $tmp
file.txt
[root@localhost log]# echo ${tmp%.*}
file
12345
```

##### 4、提取目录

```
[root@localhost log]# echo ${var%/*}
/dir1/dir2
12
```

使用文件目录的专有命令basename和dirname

##### 1、提取文件名，注意：basename是一个命令，使用(),而不是{}

```
[root@localhost log]# echo $(basename $var)
file.txt
12
```

##### 2、提取不带后缀的文件名

```
[root@localhost log]# echo $(basename $var .txt)
file
12
```

##### 3、提取目录

```
[root@localhost log]# dirname $var
/dir1/dir2
[root@localhost log]# echo $(dirname $var)
/dir1/dir2
```

