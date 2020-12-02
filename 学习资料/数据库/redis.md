### redis的数据类型，以及每种数据类型的使用场景 

#### (一)String    

这个其实没啥好说的，常规的set/get操作，value可以是String也可以是数字。一般做一些复杂的计数功能的缓存。  

#### (二)hash   

这里value存放的是结构化的对象，比较方便的就是操作其中的某个字段。博主在做单点登录的时候，就是用这种数据结构存储用户信
息， 以cookieId作为key，设置30分钟为缓存过期时间，能很好的模拟出类似session的效果。 

#### (三)list   

使用List的数据结构，可以做简单的消息队列的功能。另外还有一个就是，可以利用lrange命令，做基于redis的分页功能，性能极佳，用户 体验好。本人还用一个场景，很合适—取行情信息。就也是个生产者和消费者的场景。LIST可以很好的完成排队，先进先出的原则。  

#### (四)set   

因为set堆放的是一堆不重复值的集合。所以可以做全局去重的功能。为什么不用JVM自带的Set进行去重？因为我们的系统一般都是集群部  署，使用JVM自带的Set，比较麻烦，难道为了一个做一个全局去重，再起一个公共服务，太麻烦了。  另外，就是利用交集、并集、差集等操作，可以计算共同喜好，全部的喜好，自己独有的喜好等功能。  

#### (五)sorted set   

sorted set多了一个权重参数score,集合中的元素能够按score进行排列。可以做排行榜应用，取TOP N操作 
key区分大小写
命令不区分大小写：set,SET.  

### RedisTemplate中定义了对5种数据结构操作

```cpp
redisTemplate.opsForValue();//操作字符串
redisTemplate.opsForHash();//操作hash
redisTemplate.opsForList();//操作list
redisTemplate.opsForSet();//操作set
redisTemplate.opsForZSet();//操作有序set
```