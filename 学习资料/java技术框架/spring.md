### 注解

**@PostConstruct**是java5的时候引入的注解，指的是在项目启动的时候执行这个方法，也可以理解为在spring容器启动的时候执行，可作为一些数据的常规化加载，比如数据字典之类的。

**@ConfigurationProperties**

读取配置文件，并映射到实体里面

如果要赋值静态属性，只会调用 非静态的set方法，所以我们稍微做一下改动，set方法都换成非静态的就可以了

--------

**@DataTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")**

前端拦截器对该注解做处理，将字符串转为时间类型

-------

**@ControllerAdvice 拦截异常并统一处理**

**@AllArgsConstructor注解作用**

它是lombok中的注解,作用在类上;
使用后添加一个构造函数，该构造函数含有所有已声明字段属性参数

 **@Autowired** ：

默认是以byType按类型自动注入。  @Autowired + @Qualifier(""名称"")：将按照名称自动注入   

**@Resource：** 

 @Resource() 如果没有指定name属性，当注解写在字段上时，默认取字段名进行按照名称注入，  如果注解写在setter方法上默认取属性名进行注入。   当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配。  @Resource(name="""")  将按照名称自动注入

### 依赖包

spring-boot-devtools的模块给springboot应用提供热部署的功能。

### SpringCloud

- eureka.client.register-with-eureka：表示是否将自己注册到Eureka Server，默认是true。
- eureka.client.fetch-registry：表示是否从Eureka Server获取注册信息，默认为true。
- eureka.client.serviceUrl.defaultZone： 这个是设置与Eureka Server交互的地址，客户端的查询服务和注册服务都需要依赖这个地址。