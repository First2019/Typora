### 注解

**@PostConstruct**是java5的时候引入的注解，指的是在项目启动的时候执行这个方法，也可以理解为在spring容器启动的时候执行，可作为一些数据的常规化加载，比如数据字典之类的。

**@ConfigurationProperties**读取配置文件，并映射到实体里面
**@DataTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")**:前端拦截器对该注解做处理，将字符串转为时间类型

