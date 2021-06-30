### 注解

```
@PostConstruct该注解被用来修饰一个非静态的void（）方法。被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器执行一次。PostConstruct在构造函数之后执行，init（）方法之前执行。
通常我们会是在Spring框架中使用到@PostConstruct注解 该注解的方法在整个Bean初始化中的执行顺序：
Constructor(构造方法) -> @Autowired(依赖注入) -> @PostConstruct(注释的方法)
```

```java
@ConfigurationProperties
读取配置文件，并映射到实体里面
如果要赋值静态属性，只会调用 非静态的set方法，所以我们稍微做一下改动，set方法都换成非静态的就可以了
```

--------

```
@DataTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")
前端拦截器对该注解做处理，将字符串转为时间类型
```

-------

```
@ControllerAdvice 拦截异常并统一处理
```

```java
@AllArgsConstructor注解作用
它是lombok中的注解,作用在类上;
使用后添加一个构造函数，该构造函数含有所有已声明字段属性参数
```

```java
@Autowired
默认是以byType按类型自动注入。  @Autowired + @Qualifier(""名称"")：将按照名称自动注入   
```

```
@Resource
@Resource() 如果没有指定name属性，当注解写在字段上时，默认取字段名进行按照名称注入，  如果注解写在setter方法上默认取属性名进行注入。   当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配。  @Resource(name="""")  将按照名称自动注入
```

```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
写入响应码
```



### 依赖包

spring-boot-devtools的模块给springboot应用提供热部署的功能。

### SpringCloud

- eureka.client.register-with-eureka：表示是否将自己注册到Eureka Server，默认是true。
- eureka.client.fetch-registry：表示是否从Eureka Server获取注册信息，默认为true。
- eureka.client.serviceUrl.defaultZone： 这个是设置与Eureka Server交互的地址，客户端的查询服务和注册服务都需要依赖这个地址。