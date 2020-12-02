### 传参

#### 1.无参数

以GET方式请求

服务提供者

```java
@RequestMapping("/hello")
public String Hello(){ 
	return "hello,provider";
}
```

服务消费者

```
@GetMapping("/hello")
String hello();
```

2.单个参数

（1）GET——@PathVariable

服务提供者

```java
@GetMapping("/test/{name}")
public String test(@PathVariable String name){ 
    return "hello,"+name;
}
```

服务消费者

```java
@GetMapping("/test/{name}")
String test(@PathVariable("name") String name);
```

（2）GET——@RequestParam

服务提供者

```java
@RequestMapping("/test")
public String test(String name){
    return "hello,"+name;
}
```

服务消费者

```java
@RequestMapping("/test")
String test(@RequestParam String name);
```

会遇到报错

```
RequestParam.value() was empty on parameter 0
```

解决方法：

　　加上注解的描述，修改为

```java
@RequestMapping("/test")
String test(@RequestParam("name") String name);
```

（3）POST

@RequestBody

不需要注解的描述

```java
@RequestMapping("/test")
String test(@RequestBody String name);
```

注：

- 　　参数前使用了@RequestBody注解的，都以POST方式消费服务
- 　　@RequestBody注解的参数，需要POST方式才能传递数据

#### 2.Feign多参数的问题

（1）GET——@PathVariable

服务提供者

```java
@GetMapping("/test/{name}/{xyz}")
public String test(@PathVariable String name,@PathVariable String xyz){  
    return "hello,"+name+","+xyz;
} 
```

服务消费者

```java
@GetMapping("/test/{name}/{xyz}")
String test(@PathVariable("name") String name,@PathVariable("xyz") String xyz);
```

（1）GET——@RequestParam

服务提供者

```java
@RequestMapping("/test")
public String test(String name,Integer type){ 
    if(type==1){  
        return "hello,"+name; 
	}else{  
        return "hello,provider-"+name; 
    }
}
```

服务消费者

```java
@RequestMapping("/test")
String test(String name, Integer type);
```

会遇到报错Method has too many Body parameters

说明：

　　如果服务消费者传过来参数时，全都用的是@RequestParam的话，那么服务提供者的Controller中对应参数前可以写@RequestParam,也可以不写

　　服务消费者feign调用时，在所有参数前加上@RequestParam注解

正确的写法

```java
@RequestMapping("/test")
String test(@RequestParam("name") String name, @RequestParam("type") Integer type);
```

（2）POST

如果接收方不变

服务消费者

```java
@RequestMapping("/test")
String test(@RequestBody String name, @RequestBody Integer type);
```

会遇到报错Method has too many Body parameters

服务消费者为

```java
@RequestMapping("/test")
String test(@RequestBody String name, @RequestParam("type") Integer type);
```

name的值会为null

说明：

如果服务消费者传过来参数，有@RequestBody的话，那么服务提供者的Controller中对应参数前必须要写@RequestBody

正确的写法

服务提供者

```java
@RequestMapping("/test") 
public String test(@RequestBody String name, Integer type){  
    if(type==1){   
        return "hello,"+name;  
    }else{   
        return "hello,provider-"+name;  
    } 
}
```

服务消费者正确的写法

```java
@RequestMapping("/test")
String test(@RequestBody String name, @RequestParam("type") Integer type);
```

可以接收到参数

#### 总结：

- 　　请求参数前加上注解@PathVariable、@RequestParam或@RequestBody修饰
- 　　可以有多个@RequestParam，但只能有不超过一个@RequestBody
- 　　使用@RequestParam注解时必须要在后面加上参数名
- 　　@RequestBody用来修饰对象，但是既有@RequestBody也有@RequestParam，那么参数就要放在请求的url中，@RequestBody修饰的就要放在提交对象中
- 　　当参数比较复杂时，feign即使声明为get请求也会强行使用post请求