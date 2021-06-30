RabbitMQ启动
rabbitmq-plugins enable rabbitmq_management
cd D:\ProgramFiles\rabbitmq-server-3.7.4\rabbitmq_server-3.7.4\sbin
1.重启服务，双击rabbitmq-server.bat（双击后可能需要等待一会）
2.打开浏览器，地址栏输入http://127.0.0.1:15672 ,即可看到管理界面的登陆页
输入用户名和密码，都为guest 进入主界面：
   1、管理员打开rabbitmq命令行窗口
    2、rabbitmq-service remove(输入正确后按回车)
    3、rabbitmq-service install(输入正确后按回车)
    4、rabbitmq-service start(输入正确后按回车)
    5、rabbitmq-plugins enable rabbitmq_management(输入正确后按回车)
    6、rabbitmq-service stop(输入正确后按回车)
    7、rabbitmq-service start(输入正确后按回车)
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl start_app

*# 列出所有 queues*

 rabbitmqctl list_queues

*# 删除 queue_name*

 rabbitmqctl delete_queue queue_name

删除以hello开头的queue

由于list_queues会列出队列名称以及对应的消息数目，需要过滤掉消息数目，配合awk命令只取第1列

```
 rabbitmqctl list_queues| grep ^hello | awk '{print $1}' | xargs -n1 rabbitmqctl delete_queue
```

update user set password=password('123456') where user='root';