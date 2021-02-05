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

### 从服务端获取配置内容

访问地址：http://localhost:8000/config-repo/dev（获取完整配置信息）

访问地址：http://localhost:8000/system/dev（获取完整配置信息）

访问地址：http://localhost:8000/system-dev.properties（获取指定配置文件内容）

访问地址：http://localhost:8001/config-repo/uat（获取完整配置信息）

### 配置属性加解密之非对称加密

**CMD下执行命令**

```java
D:\>keytool -genkeypair -alias mytestkey -keyalg RSA -keypass changeme -keystore server.jks -storepass letmein
您的名字与姓氏是什么?
  [Unknown]:  zhou
您的组织单位名称是什么?
  [Unknown]:  zw
您的组织名称是什么?
  [Unknown]:  zw
您所在的城市或区域名称是什么?
  [Unknown]:  shenzhen
您所在的省/市/自治区名称是什么?
  [Unknown]:  city
该单位的双字母国家/地区代码是什么?
  [Unknown]:  sz
CN=zhou, OU=zhou, O=zhenwei, L=shenzhen, ST=shi, C=sz是否正确?
  [否]:  y
D:\>
```

说明：

- 上面参数的解释如下:
- -genkeypair 表示生成密钥对
- -alias 表示 keystore 关联的别名
- -keyalg 表示指定密钥生成的算法
- -keystore 指定密钥库的位置和名称

  1）会在D盘输入一个文件server.jks；

**将server.jks复制到工程中**

![img](https://img-blog.csdn.net/20180717134709159?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoZW56aGVuX3pzdw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

访问 /encrypt/status 端点，显示状态OK。

加密： 
curl http://localhsot:8080/enrypt -d mysercet 
结果会出来一长串 fdasfa2341sdfa134214…. 
解密： 
curl http://localhost:8080/decrypt -d fdasfa2341sdfa134214…. 
结果会出来 mysercet

**更改配制后只需要重启客户端即可生效**