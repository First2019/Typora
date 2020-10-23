**Namesrv**: 存储当前集群所有Brokers信息、Topic跟Broker的对应关系。          
**Broker**: 集群最核心模块，主要负责Topic消息存储、消费者的消费位点管理（消费进度）。          
**Producer**: 消息生产者，每个生产者都有一个ID(编号)，多个生产者实例可以共用同一个ID。同一个ID下所有实例组成一个生产者集群。          
**Consumer**: 消息消费者，每个订阅者也有一个ID(编号)，多个消费者实例可以共用同一个ID。同一个ID下所有实例组成一个消费者集群。

 1，启动Namesrv，Namesrv起来后监听端口，等待Broker、Produer、Consumer连上来，相当于一个路由控制中心。
 2，Broker启动，跟所有的Namesrv保持长连接，定时发送心跳包。心跳包中包含当前Broker信息(IP+端口等)以及存储所有topic信息。注册成功后，namesrv集群中就有Topic跟Broker的映射关系。
 3，收发消息前，先创建topic，创建topic时需要指定该topic要存储在哪些Broker上。也可以在发送消息时自动创建Topic。
 4，Producer发送消息，启动时先跟Namesrv集群中的其中一台建立长连接，并从Namesrv中获取当前发送的Topic存在哪些Broker上，然后跟对应的Broker建长连接，直接向Broker发消息。
 5，Consumer跟Producer类似。跟其中一台Namesrv建立长连接，获取当前订阅Topic存在哪些Broker，然后直接跟Broker建立连接通道，开始消费消息。         

#### Topic       

topic是消息的一种分类，消息发送/接收都要指定topic。topic和生产组/消费组之间是多对多的关系。       

#### Queue       

topic的细分。       

#### Tag（EventCode）       

每条发送的message都可以有一个tag；这样同一个topic可以按tag区分不同的业务场景。       在实践上，一个业务系统使用一个topic，用不同的tag区分不同的消息。       

#### ConsumerGroup       

在集群模式下（默认），一个消息只会被同一消费组中的一个节点消费；同一消费组的多个节点均衡的消费topic。在实践上，一个应用/微服务一个消费组。   ![img](https://uploadfiles.nowcoder.com/images/20200928/401579103_1601286166833_670383114B8D67B1601FCEC8C32A78F9)
 

**Message**是RocketMQ对消息的封装，我们也只能将消息封装为Message实例，才能通过RocketMQ发送出去。 

#### 集群模式(默认)：           

Consumer 实例平均分摊消费生产者发送的消息          

例子：订单消息，一般是只被消费一次（被标记为同一个 ConsumerGroup 组的消费者不会对消息重复消费）          

#### 广播模式：           

广播模式下消费消息：投递到 Broker 的消息会被每个 Consumer 进行消费，一条消息被多个 Consumer 消费，广播消费中 ConsumerGroup 暂时无用。          

**例子**：群公告，每个人都需要消费这个消息 ,怎么切换模式：通过 setMessageModel()      

 一个 Message 只有一个 Tag，Tag 是二级分类。过滤分为 Broker 端和 Consumer 端过滤。           

**Broker** 端过滤，减少了无用的消息的进行网络传输，增加了 broker 的负担          

**Consumer** 端过滤，完全可以根据业务需求进行过滤，但是增加了很多无用的消息传输          

一般是监听 * ，或者指定 tag，|| 运算，SLQ92，FilterServer 等；           

Tag 性能高，逻辑简单      

```java
  //使用生产者组名实例化一个生产者      
​      DefaultMQProducer producer = new DefaultMQProducer("DefaultProducer");     
​      // 指定RocketMQ nameServer地址      
​      producer.setNamesrvAddr("10.0.10.63:9876");     
​      // 启动生产者      
​      producer.start();     
​      //创建Message实例      
​      Message msg = new Message("BenchmarkTest" , "TagA", ("Hello                       ​         RocketMQ").getBytes(RemotingHelper.DEFAULT_CHARSET)
​      //调用sendOneway()发送消息，该方法不会管消息是否发送成功      
​      producer.sendOneway(msg);     
​      //同步发送消息，根据sendResult结果处理      
​      SendResult sendResult = producer.send(msg);     
​      //异步发送消息      
​      producer.send(msg, new SendCallback() {     
​      public void onSuccess(SendResult sendResult) {     
​      //发送成功,业务处理      
​      }     
​      public void onException(Throwable e) {     
​      //发送异常，业务处理      
​      }     
​      });     
```

