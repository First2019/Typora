### 添加build配置

1，生成3个目录/lib，/conf，/bin目录
2，把所有的jar目录编译、拷贝到/lib目录（包括maven的jar包和lib目录下的jar，以及编译的jar包）
3，把所有的启动脚本从工程根目录拷贝到/bin目录
4，把所有的配置文件从src/main/resources拷贝到/conf

```xml
<build>
    <sourceDirectory>src/main/java</sourceDirectory>
    <resources>
      <!-- 把src/main/resources目录下所有的文件拷贝到conf目录中 -->
      <resource>
        <directory>src/main/resources</directory>
        <targetPath>${project.build.directory}/conf</targetPath>
      </resource>
      <!-- 把lib目录下所有的文件拷贝到lib目录中
      （可能有些jar包没有办法在maven中找到，需要放在lib目录中） -->
      <resource>
        <directory>lib</directory>
        <targetPath>${project.build.directory}/lib</targetPath>
      </resource>
      <!-- 把放在根目录下的脚本文件.sh,.bat拷贝到bin目录中 -->
      <resource>
        <directory>.</directory>
        <includes>
          <include>**/*.sh</include>
          <include>**/*.bat</include>
        </includes>
        <targetPath>${project.build.directory}/bin</targetPath>
      </resource>
    </resources>
<plugins>
  <!-- 用于编译的plugin -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <configuration>
      <fork>true</fork>
      <defaultLibBundleDir>lib</defaultLibBundleDir>
      <source>1.7</source>
      <target>1.7</target>
      <encoding>UTF-8</encoding>
      <!-- 如果配置了JAVA_HOME,下面应该可以不用配 -->
      <executable>C:\Program Files (x86)\Java\jdk1.8.0_91\bin\javac.exe</executable>
    </configuration>
  </plugin>
   
  <!-- 用于生成jar包的plugin -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>2.6</version>
    <configuration>
      <!-- 把生成的jar包放在lib目录下（和其他所有jar包一起） -->
      <outputDirectory>${project.build.directory}/lib</outputDirectory>
      <archive>
        <manifest>
          <addClasspath>true</addClasspath>
          <classpathPrefix>lib/</classpathPrefix>
        </manifest>
      </archive>
      <excludes>
      <!-- 排除掉一些文件,不要放到jar包中，
      这里是为了排除掉src/main/resources中的文件（它们应该放到conf目录）
      这里只能指定要排除的目标文件，而不能指定源文件，虽然不够完美，但是基本能达到目的。 -->
        <exclude>*.xml</exclude>
        <exclude>*.properties</exclude>
      </excludes>
    </configuration>
  </plugin>
   
  <!-- 用于拷贝maven依赖的plugin -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>2.10</version>
    <executions>
      <execution>
        <id>copy-dependencies</id>
        <phase>package</phase>
        <goals>
          <goal>copy-dependencies</goal>
        </goals>
        <configuration>
        <!-- 把依赖的所有maven jar包拷贝到lib目录中（这样所有的jar包都在lib目录中） -->
          <outputDirectory>${project.build.directory}/lib</outputDirectory>
        </configuration>
      </execution>
    </executions>
  </plugin>
   
  <!-- 用于拷贝resource的plugin -->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <version>2.7</version>
    <configuration>
      <encoding>UTF-8</encoding>
    </configuration>
  </plugin>
 
  <!-- 配置生成源代码jar的plugin -->
  <plugin>
    <artifactId>maven-source-plugin</artifactId>
    <version>2.4</version>
    <configuration>
      <attach>true</attach>
      <encoding>UTF-8</encoding>
      <!-- 配置源代码jar文件的存放路径，和其他jar文件一起放在lib目录 -->
      <outputDirectory>${project.build.directory}/lib</outputDirectory>
    </configuration>
    <executions>
      <execution>
        <phase>compile</phase>
        <goals>
          <goal>jar</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
</plugins>
</build>
```

```xml
<resources>  
        <resource>  
 			<!--描述了资源的目标路径。该路径相对target/classes目录
			(例${project.build.outputDirectory})。 -->  
            <!--举个例子，如果你想资源在特定的包里(org.apache.maven.messages)，你就必须该元素设置			  为org/apache/maven/messages。 -->  
            <!--然而，如果你只是想把资源放到源码目录结构里，就不需要该配置。 -->  
            <targetPath>resources</targetPath>  
  
            <!--是否使用参数值代替参数名。参数值取自properties元素或者文件里配置的属性，文件在				filters元素里列出。 -->  
            <filtering>true</filtering>  
  
            <!--描述存放资源的目录，该路径相对POM路径 -->  
            <directory>src/main/resources</directory>  
  
            <!--包含的模式列表 -->  
            <includes>  
                <include>**/*.properties</include>  
                <include>**/*.xml</include>  
            </includes>  
  
            <!--排除的模式列表 如果<include>与<exclude>划定的范围存在冲突，以<exclude>为准 -->  
            <excludes>  
                <exclude>jdbc.properties</exclude>  
            </excludes>  
        </resource>  
    </resources>  
```

### 关于Lifecycle

**1.clean**
用于清除之前构建生成的所有文件

其中具体为清楚了Target目录中的所有文件，包括该目录

i.e：删除了install生成的所有文件

**2.validate**
用于验证项目是否真确，并且其说有必要信息是否都可用

**3.compile**
编译项目的源代码，主要是java文件

一般是编译scr/main/java或是scr/test/java里面的文件

**4.test**
用合适的测试框架来进行测试，测试compile中编译出来的代码

测试的东西一般不加包和部署

**5.packaging**
获取compile中编译好的代码并将其打包成可分类的格式，i.e:JAR

**6.vertify**
这步是用来验证test

检查test的结果是否满足标准

**7.install**
将软件包安装到本地存储库中

确保本地其他项目可能需要使用他（eg:装了core才能用oms）

**9.deploy**
复制最终的包至远程仓库,共享给其它开发人员和项目

PS:在install的时候可能会出现乱码，此时对着install点右键，选择create xxx install

在command line里写 install -Dmaven.test.skip=true -f pom.xml 然后用新写的命令代替旧的install即可

plugin
<dependency>
帮组分析项目依赖

依赖就是在maven里面要用哪个包就在<denpendency>标签里面写东西

一般不用自己写

可以在google里面搜索“maven xxx repository”

或者直接在http://mvnrepository.com/里面搜索xxx

<resources>
将资源文件过滤

resources用来处理资源

compiler用来编译java文件

<jetty>
快速在web上部署

进行调试的时候比较方便和节省时间

<build>
可以分为

<project build>全局配置：为全局有效

<profile build>配置：为针对不同的profile配置

build里面有<resource>和<plugin>两种标签

他们都是把一些默认方法放在其他文件路径的文件放到“src/main/java”里面

<packaging>
打包方式主要有JAR和WAR两种

其中JAR用于比较小的项目，好处为不用依赖包，因为他把应用依赖的所有依赖包和程序打包在一个全量包里，他说packaging的默认方式

WAR适用于需要部署的项目

<scope>
适用范围主要分为test和provided两种

test对测试范围有效

provided对编译和测试过程都有效

1.匹配符**可以匹配路径，*只能匹配名字

2.如果启动失败先看错误信息

3.jetty：run要create一个再运行，不用直接运行，因为直接运行可能会调用到了其他人的profiles

4.运行maven之前先看一块profile的配置环境有没有勾选错别人的环境

### 异常

建议和总结：

1. 先部分升级打包，再升级其余部分
2. 或者把引用的版本降低，先将本组件的最新版本进行发布（父pom那一层全部打包），再更新引用最新的版本
3. 或者直接把子模块的引入先注释掉，直接把父pom先打包即可，后续可单独再打包（接口包之类的，暂测可行，但是非长久之计）。

另外也有可能是jar包下载错误，可以先把本地maven的jar包缓存先删除掉，再直接项目右键【Maven==》》Update Project==》》Force Update of Snapshots/Release（勾选上）】，强制更新jar包引用。