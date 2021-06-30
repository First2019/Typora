##### 三个核心组件

Subject, SecurityManager 和 Realms.
 ①Subject：即“当前操作用户”。但是，在Shiro中，Subject这一概念并不仅仅指人，也可以是第三方进程、后台帐户（Daemon Account）或其他类似事物。它仅仅意味着“当前跟软件交互的东西”。但考虑到大多数目的和用途，你可以把它认为是Shiro的“用户”概念。Subject代表了当前用户的安全操作，SecurityManager则管理所有用户的安全操作。
 ②SecurityManager：它是Shiro框架的核心，典型的Facade模式，Shiro通过SecurityManager来管理内部组件实例，并通过它来提供安全管理的各种服务。
 ③Realm： Realm充当了Shiro与应用安全数据间的“桥梁”或者“连接器”。也就是说，当对用户执行认证（登录）和授权（访问控制）验证时，Shiro会从应用配置的Realm中查找用户及其权限信息。

##### 登录认证过程简述

①调用subject.login方法进行登录，其会自动委托给securityManager,login方法进行登录;
 ②securityManager通过 Authenticator(认证器)进行认证
 ③Authenticator的实现ModularRealmAuthenticaton调用realm从ini配置文件取用户真实的账号和密码，这里使用的是IniRealm ( shiro自带，相当于数据源) ;

④IniRealm先根据token中的账号去ini中找该账号,如果找不到则给ModularRealmAuthenticator返回null ,如果找到则匹配密码，匹配密码成功则认证通过。

![img](https:////upload-images.jianshu.io/upload_images/15309906-504855da985de6d3.png?imageMogr2/auto-orient/strip|imageView2/2/w/1018/format/webp)

```java
AuthenticatingRealm:assertCredentialsMatch()，凭证匹配器，提交的凭证和存储的凭证进行匹配比较 
```

