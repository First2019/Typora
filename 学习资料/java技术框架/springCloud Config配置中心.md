*1.* 在 git 上的配置文件的名字要和 config 的 client 端的 application name 对应；

*2.* 在结合 eureka 的场景中，关于 eureka 和 git config 相关的配置要放在 bootstrap.yml 中，否则会请求默认的 config server 配置，这是因为当你加了配置中心，服务就要先去配置中心获取配置，而这个时候，application.yml 配置文件还没有开始加载，而 bootstrap.yml 是最先加载的。



- spring.cloud.config.server.git.uri: 配置的Git长裤的地址。
- spring.cloud.config.server.git.search-paths: git仓库地址下的相对地址 多个用逗号","分割。
- spring.cloud.config.server.git.username:git仓库的账号。
- spring.cloud.config.server.git.password:git仓库的密码。

### bootstrap.properties文件

- [spring.cloud.config.name](http://spring.cloud.config.name/)： 获取配置文件的名称。
- spring.cloud.config.profile: 获取配置的策略。
- spring.cloud.config.label：获取配置文件的分支，默认是master。如果是是本地获取的话，则无用。
- spring.cloud.config.discovery.enabled: 开启配置信息发现。
- spring.cloud.config.discovery.serviceId: 指定配置中心的service-id，便于扩展为高可用配置集群。
- eureka.client.serviceUrl.defaultZone： 这个是设置与Eureka Server交互的地址，客户端的查询服务和注册服务都需要依赖这个地址。

### springcloud config 的URL与配置文件的映射关系如下:

```
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```

上面的url会映射{application}-{profile}.properties对应的配置文件，{label}对应git上不同的分支，默认为master。