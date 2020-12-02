### 作用域

#### provided（compileOnly）

只在编译时有效，不会参与打包
 可以在自己的`module`中使用该方式依赖一些比如`com.android.support`，`gson`这些使用者常用的库，避免冲突。

------

#### apk（runtimeOnly）

只在生成`apk`的时候参与打包，编译时不会参与，很少用。

------

#### testCompile（testImplementation）

`testCompile` 只在单元测试代码的编译以及最终打包测试apk时有效。

------

#### debugCompile（debugImplementation）

`debugCompile` 只在 **debug** 模式的编译和最终的 **debug apk** 打包时有效

------

#### releaseCompile（releaseImplementation）

`Release compile`仅仅针对 **Release** 模式的编译和最终的 **Release apk** 打包。

------

#### compile（api）

这种是我们最常用的方式，使用该方式依赖的库将会**参与编译和打包**。

------

#### implementation

使用了该命令编译的依赖，它仅仅对当前的`Module`提供接口