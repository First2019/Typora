# 前台启动

```
java -jar XXX.jar
```

# 后台启动

```
java -jar xxx.jar &
```

区别:前台启动ctrl+c就会关闭程序,后台启动ctrl+c不会关闭程序

> -client，-server

这两个参数用于设置虚拟机使用何种运行模式，client模式启动比较快，但运行时性能和内存管理效率不如server模式，通常用于客户端应用程序。相反，server模式启动比client慢，但可获得更高的运行性能。 在 Linux，Solaris上缺省采用server模式。

# 制定控制台的标准输出

```
java -jar xxx.jar > catalina.out  2>&1 & 
```

- catalina.out将标准输出指向制定文件catalina.out
- 2>&1 输出所有的日志文件
- & 后台启动

# 脚本启动

```
#!/bin/sh
#功能简介：启动上层目录下的jar文件
#参数简介：
#    $1:jar文件名（包含后缀名）
#    注意：jar文件必须位于startup.sh目录的上一层目录。

#启动参数
JAVA_OPTS="-server -Xms400m -Xmx400m -Xmn300m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=128m -Xverify:none -XX:+DisableExplicitGC -Djava.awt.headless=true"

jar_name=$1
this_dir="$( cd "$( dirname "$0"  )" && pwd )"
parent_dir=`dirname "${this_dir}"`
log_dir="${parent_dir}/logs"
log_file="${log_dir}/catalina.out"
jar_file="${parent_dir}/userapps/${jar_name}"

#参数个数<1或者参数空值时，中断执行
if [ $# -lt 1 ] || [ -z $1 ]; then
    echo -e "\033[31m请输入要部署的jar包名称!\033[0m"
    exit 1
fi

#日志文件夹不存在，则创建
if [ ! -d "${log_dir}" ]; then
    mkdir "${log_dir}"
fi

#父目录下jar文件存在
if [ -f "${jar_file}" ]; then
    #启动jar包；重定向标准错误输出到文件，丢掉标准输出
    java $JAVA_OPTS -jar ${jar_file} 1>/dev/null 2>"${log_file}" &
    exit 0
else
    echo -e "\033[31m${jar_file}文件不存在！\033[0m"
    exit 1
fi
```

 启动方式

```
./startup.sh xxx.jar
```

说明

![img](https://images2015.cnblogs.com/blog/1006037/201612/1006037-20161208171432741-871972136.png)

 

```shell
#!/bin/bash
jarname='demo-0.0.1-SNAPSHOT'
#通过程序名获取进程ID
pid=`ps aux | grep $jarname | grep -v grep | awk '{print $2}'`
echo $pid
kill -9 $pid
java -jar $jarname.jar
nohup java -jar /zz/$jarname.jar  >/zz/run.log &
```

