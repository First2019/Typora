## nginx 文件结构

```
...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```

- 1、**全局块**：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
- 2、**events块**：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
- 3、**http块**：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
- 4、**server块**：配置虚拟主机的相关参数，一个http中可以有多个server。
- 5、**location块**：配置请求的路由，以及各种页面的处理情况。

## 配置文件

```nginx
########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
}
```

### Nginx配置proxy_pass转发的/路径问题

nginx配置proxy_pass时url末尾带“/”与不带“/”的区别如下：

注意：当location为正则表达式匹配模式时，proxy_pass中的url末尾是不允许有"/"的，因此正则表达式匹配模式不在讨论范围内。
proxy_pass配置中url末尾带/时，nginx转发时，会将原uri去除location匹配表达式后的内容拼接在proxy_pass中url之后。

```nginx
测试地址：http://192.168.171.129/test/tes.jsp

场景一：
location ^~ /test/ {
    proxy_pass http://192.168.171.129:8080/server/;
}
代理后实际访问地址：http://192.168.171.129:8080/server/tes.jsp

场景二：
location ^~ /test {
    proxy_pass http://192.168.171.129:8080/server/;
}
代理后实际访问地址：http://192.168.171.129:8080/server//tes.jsp

场景三：
location ^~ /test/ {
    proxy_pass http://192.168.171.129:8080/;
}
代理后实际访问地址：http://192.168.171.129:8080/tes.jsp

场景四：
location ^~ /test {
    proxy_pass http://192.168.171.129:8080/;
}
代理后实际访问地址：http://192.168.171.129:8080//tes.jsp
```

proxy_pass配置中url末尾不带/时，如url中不包含path，则直接将原uri拼接在proxy_pass中url之后；如url中包含path，则将原uri去除location匹配表达式后的内容拼接在proxy_pass中的url之后。

```nginx
 测试地址：http://192.168.171.129/test/tes.jsp
 场景一：
 location ^~ /test/{
	proxy_pass http://192.168.171.129:8080/server;
 }
 代理后实际访问地址：http://192.168.171.129:8080/servertes.jsp
场景二：
location ^~ /test {
    proxy_pass http://192.168.171.129:8080/server;
}
代理后实际访问地址：http://192.168.171.129:8080/server/tes.jsp

场景三：
location ^~ /test/ {
    proxy_pass http://192.168.171.129:8080;
}
代理后实际访问地址：http://192.168.171.129:8080/test/tes.jsp

场景四：
location ^~ /test {
    proxy_pass http://192.168.171.129:8080;
}
代理后实际访问地址：http://192.168.171.129:8080/test/tes.jsp
```

###  参数明细表：

| $remote_addr          | 客户端的ip地址(代理服务器，显示代理服务ip)           |
| --------------------- | ---------------------------------------------------- |
| $remote_user          | 用于记录远程客户端的用户名称（一般为“-”）            |
| $time_local           | 用于记录访问时间和时区                               |
| $request              | 用于记录请求的url以及请求方法                        |
| $status               | 响应状态码，例如：200成功、404页面找不到等。         |
| $body_bytes_sent      | 给客户端发送的文件主体内容字节数                     |
| $http_user_agent      | 用户所使用的代理（一般为浏览器）                     |
| $http_x_forwarded_for | 可以记录客户端IP，通过代理服务器来记录客户端的ip地址 |
| $http_referer         | 可以记录用户是从哪个链接访问过来的                   |

查看日志命令tail -f /usr/local/nginx/logs/access.log

### $uri

nginx中的$uri记录的是执行一系列内部重定向操作后最终传递到后端服务器的URL

包含请求的文件名和路径，不包含包含“?”或“#”等参数。

完整URL链接：http://www.alipay.com/alipay/index.html
$uri：/alipay/index.html

### $request_uri

$request_uri记录的是当前请求的原始URL（包含参数），如果没有执行内部重定向操作，request_uri去掉参数后的值和uri的值是一样的。在线上环境中排查问题是，如果在后端服务器中看到的请求和Nginx中存放的request_uri无法匹配，可以考虑去uri里边进行查找。
包含请求的文件名和路径及所有参数

完整URL链接：http://www.alipay.com/alipay/index.html
$request_uri：/alipay/index.html#参数

```
location / {
            #nginx下HTML文件夹，访问上述域名时会检索此文件夹下的文件进行访问
            root   html/BootStrapDemo;
            #输入网址（server_name：port）后，默认的访问页面
            index  index.html index.htm;
        }
```

3.举例说明：

```nginx
location /images/ {    
    root /opt/html/;
    try_files $uri   $uri/  /images/default.gif; 
}
比如 请求 127.0.0.1/images/test.gif 会依次查找 
1.文件/opt/html/images/test.gif   
2.文件夹 /opt/html/images/test.gif/下的index文件  
3. 请求127.0.0.1/images/default.gif
```



### 1.4 Nginx日志分隔

nginx的日志文件没有rotate功能。编写每天生成一个日志，我们可以写一个nginx日志切割脚本来自动切割日志文件。

第一步就是重命名日志文件，不用担心重命名后nginx找不到日志文件而丢失日志。在你未重新打开原名字的日志文件前，nginx还是会向你重命名的文件写日志，Linux是靠文件描述符而不是文件名定位文件。

第二步向nginx主进程发送USR1信号。nginx主进程接到信号后会从配置文件中读取日志文件名称，重新打开日志文件(以配置文件中的日志名称命名)，并以工作进程的用户作为日志文件的所有者。重新打开日志文件后，nginx主进程会关闭重名的日志文件并通知工作进程使用新打开的日志文件。工作进程立刻打开新的日志文件并关闭重名名的日志文件。然后你就可以处理旧的日志文件了。[或者重启nginx服务]。

 

**nginx日志按每分钟自动切割脚本如下：**

新建shell脚本：vi/usr/local/software/nginx/nginx_log.sh

```
 #!/bin/bash
        #设置日志文件存放目录
        LOG_HOME="/usr/local/software/nginx/logs/"
        #备分文件名称
        LOG_PATH_BAK="$(date -d yesterday +%Y%m%d%H%M)".abc.access.log
        #重命名日志文件
        mv ${LOG_HOME}/abc.access.log ${LOG_HOME}/${LOG_PATH_BAK}.log
        #向nginx主进程发信号重新打开日志 
        kill -USR1 `cat /usr/local/software/nginx/logs/nginx.pid`
```

**创建crontab设置作业**

\#设置日志文件存放目录crontab -e

*/1 * * * * sh /usr/local/software/nginx/nginx_log.sh

 ![img](https://images2015.cnblogs.com/blog/464291/201705/464291-20170522230849882-1245263743.png)

