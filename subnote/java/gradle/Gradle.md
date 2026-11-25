# Gradle

- 官网：<https://gradle.org/>

- 选择Groovy还是Kotlin作为Gradle的DSL？
    - `推荐Kotlin`，Kotlin语法更接近java，学习成本更低，并且可以作为以后打算开发Android铺垫
    - 而Groovy，语法更加灵活，也就意味着各种写法都可以，学习成本更高，并且就只用在了Gradle上，不值得

## 安装Gradle

- 参考Spring Boot支持的Gradle版本

- 参考Idea支持的Gradle版本：`idea_home\plugins\gradle\lib`

- 参考Gradle支持的jdk版本，需要先安装jdk并配置好

- 下载完整版（包含了文档和源码）

### 修改maven下载源

在`GRADLE_HOME/init.d`目录下创建init.gradle文件，内容如下

- gradle-9.x版本这样写

```groovy
settingsEvaluated { settings ->
    settings.dependencyResolutionManagement {
        // Android项目会将这里的仓库整合到settings.gradle的仓库配置
        // Java项目还是相当于全局配置
        repositoriesMode.set(RepositoriesMode.PREFER_SETTINGS)
        // 项目依赖的下载地址
        repositories {
            mavenLocal()
            maven {
                name "aliyun";
                url "https://maven.aliyun.com/repository/public"
            }
            google()
            mavenCentral()
        }
    }
}

allprojects {
    // build.gradle构建脚本使用的依赖的下载地址，一般是各种插件的下载地址
    buildscript {
        repositories {
            mavenLocal()
            maven {
                name "aliyun";
                url "https://maven.aliyun.com/repository/public"
            }
            google()
            mavenCentral()
        }
    }
}
```

- gradle-9.9.2 用kotlin这样写

```kotlin
// $GRADLE_HOME/init.d/init.gradle.kts
// 全局插件仓库
gradle.settingsEvaluated {
    pluginManagement {
        repositories {
            maven {
                setUrl("https://maven.aliyun.com/repository/gradle-plugin")
            }
            maven {
                setUrl("https://maven.aliyun.com/repository/public")
            }
            google()
            mavenCentral()
            gradlePluginPortal()
        }
    }
}

// 全局依赖仓库
allprojects {
    repositories {
        maven {
            setUrl("https://maven.aliyun.com/repository/public")
        }
        maven {
            setUrl("https://maven.aliyun.com/repository/google")
        }
        mavenCentral()
        google()
    }
}
```

### 配置环境变量

- `GRADLE_HOME`
- `GRADLE_USER_HOME`
    - gradle的本地仓库路径和gradle wrapper的缓存路径、缓存的路径
    - 下载的依赖会存放在`GRADLE_USER_HOME/caches/modules-2/files-2.1`目录下
    - 除了依赖目录还有别的目录，建议不要指定为maven的repository路径，避免目录混乱
- `M2_HOME`，maven家目录，gradle默认会查找`~/.m2/repository`作为maven本地仓库路径，修改了maven本地仓库路径的话才需要定义

- 配置Path：`GRADLE_HOME\bin`

- 测试：`gradle -v`

### IDEA配置Gradle

- Settings->Build,Execution,Deployment->Gradle
    - Gradle user home，指定一个路径作为gradle的本地仓，gradle和maven的结构不一样，不要设置为maven本地仓库的路径
    - Build and run using，设置为IntelliJ IDEA，IDEA会对纯Java项目做优化，启动更快，如果构建有问题再改回默认Gradle
    - Run test using，设置为IntelliJ IDEA，不然用默认Gradle的话初始化测试都要等好几秒了，浪费时间
    - Distribution，设置为Local installation，并选择本地安装的gradle家目录
    - Gradle JVM，设置为本地安装的JDK即可

## 创建一个java项目

```sh
# 创建一个目录
mkdir my-project

# 进入目录
cd my-project

# 根据交互进行操作
gradle init
```

因为gradle的版本兼容性很差，项目默认是用wrapper的，还会根据wrapper的设置从网络再下载一个gradle，并且下载速度很慢

因此个人建议直接把之前下载的gradle放到`GRADLE_USER_HOME//wrapper/dists`下

然后用idea打开该项目

`gradle init` 生成的是多项目结构，用idea创建gradle项目生成的结构更加接近maven

## Gradle项目目录结构

- Gradle项目默认目录结构和Maven项目的一致，都是基于约定大于配置

- gradlew、gradlew.bat执行的是gradle/wrapper里面指定版本的gradle指令，不是本地安装的gradle的指令

- 如果要执行的是本地安装的gardle的指令，gradle/wrapper，gradlew、gradlew.bat，这三个玩意是用不到的，是可以删了的

- project-root
    - build，存放编译后的字节码，打成的包、测试报告等，类似maven的target目录
    - gradle，封装包装器文件夹
        - wrapper
            - gradle-wrapper.jar
            - gradle-wrapper.properties
    - src
        - main
            - java
            - resources
            - webapp，war工程才有这个目录
                - WEB-INF
                    - web.xml
                - index.jsp
        - test
            - java
            - resources
    - gradlew，包装器在非Windows系统的启动脚本
    - gradlew.bat，包装器在Windows系统的启动脚本
    - build.gradle，构建脚本，类似maven的pom.xml
    - settings.gradle，配置文件，定义项目和子项目名称信息，一个项目只能有一个此文件（子项目是没有这个文件的）

## gradle常用命令

```sh
# 查看可用的命令
gradle task

# 启动项目
gradle run

# 删除构建目录
gradle clean
```

## settings.gradle.kts

`settings.gradle.kts` 是构建的入口点，Gradle会实例化一个`settings`对象

`rootProject.name`实际上就是`settings.rootProject.name`的简写

```kotlin
// 当前项目
rootProject.name = "gradle-demo"
```

### 配置子项目

```kotlin
// 如果有子项目，通过指定子项目目录来包含它们
// 将其添加到构建列表中
include("app1")
include("app2")

// 也可以写在一起
include("app1", "app2")
```

### 配置全局插件

主要用于全局插件，单项目很少使用

```kotlin
plugins { 
    id("org.springframework.boot") version "4.2.0"
    id("io.spring.dependency-management") version "1.1.7"
}
```

### 配置插件仓库和依赖仓库

插件仓库和依赖仓库虽然可以写在里面，不过更推荐在`build.gradle.kts`中进行配置

```kotlin
// 全局插件仓库
pluginManagement {
    repositories {
        maven {
            setUrl("https://maven.aliyun.com/repository/public")
        }
        // Gradle插件仓库
        gradlePluginPortal()
        // Google插件仓库
        // google()
    }
}

// 全局依赖仓库
dependencyResolutionManagement {
    // 这种写法目前还在孵化阶段，没孵化出来可能未来会移除
    repositories {
        maven {
            setUrl("https://maven.aliyun.com/repository/public")
        }
    }
}
```

## build.gradle.kts

Gradle会实例化一个`project`对象

`group`实际上就是`project.group`的简写

- project对象包含以下常见属性
    - plugins 项目使用的插件列表
    - group 项目组
    - version 项目版本
    - repositories 依赖仓库
    - dependencies 依赖列表
    - name 项目（目录）名称
    - path 项目的完全限定名称
    - description 项目描述
    - layout 通过此对象的属性可以访问项目中的各种关键位置

### 配置插件

插件分为官方插件和第三方插件

每个插件都包含了一个或多个任务，添加了插件后，就可以执行其提供的任务

官方插件不用指定版本，非官方插件要指定版本

id和version相当于插件的坐标

```kotlin
// kotlin的lambda写法
// plugins是一个方法
plugins {
    // 指定使用的插件
    // java插件包含编译、测试等各种任务
    id("java")
    // 包含java插件的全部功能并且支持生成可执行应用程序
    id("application")
    
    id("org.springframework.boot") version "4.2.0"
    id("io.spring.dependency-management") version "1.1.7"
}
```

#### 配置java

当添加了java插件后，可以对java插件进行配置

```kotlin
plugins {
    id("java")
}

java {
    // 写法一
    // 相当于 javac -source 27 -target 27
    // 依赖当前运行 Gradle 的 JDK
    sourceCompatibility = JavaVersion.VERSION_27
    targetCompatibility = JavaVersion.VERSION_27

    // 写法二
    // 自动查找或下载指定版本的 JDK
    // 新项目首选这种写法
    toolchain {
        languageVersion = JavaLanguageVersion.of(27)
    }
}
```

#### 配置项目插件仓库和依赖仓库

##### Java 项目

```kotlin
repositories {
    // 使用maven本地仓，不建议设置
    // mavenLocal()
    // 阿里云仓库
    maven {
        name = "aliyun"
        url = uri("https://maven.aliyun.com/repository/public")
        // 或者
        // setUrl("https://maven.aliyun.com/repository/public")
    }
    // Maven Central 用来兜底
    mavenCentral()
}

buildscript {
    repositories {
        // 使用maven本地仓，不建议设置
        // mavenLocal()
        // 阿里云仓库
        maven {
            name = "aliyun"
            url = uri("https://maven.aliyun.com/repository/public")
            // 或者
            // setUrl("https://maven.aliyun.com/repository/public")
        }
        // Maven Central 用来兜底
        mavenCentral()
    }
}
```

##### Android 项目

```kotlin
repositories {
    // 使用maven本地仓，不建议设置
    // mavenLocal()
    // 阿里云的 Google 专属镜像
    maven {
        setUrl("https://maven.aliyun.com/repository/google")
    }
    // 阿里云的常规库镜像
    maven {
        setUrl("https://maven.aliyun.com/repository/public")
    }
    // Maven Central 用来兜底常规库
    mavenCentral()
    // Google 官方仓库用来兜底 Android 库
    google()
}

buildscript {
    repositories {
        // 使用maven本地仓，不建议设置
        // mavenLocal()
        // 阿里云的 Google 专属镜像
        maven {
            setUrl("https://maven.aliyun.com/repository/google")
        }
        // 阿里云的常规库镜像
        maven {
            setUrl("https://maven.aliyun.com/repository/public")
        }
        // Maven Central 用来兜底常规库
        mavenCentral()
        // Google 官方仓库用来兜底 Android 库
        google()
    }
}
```

### 配置依赖

```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web:4.2.0")
    // 分开这样写
    implementation("org.springframework.boot", "spring-boot-starter-web", "4.2.0")
    // 始终使用最新版本，不推荐
    implementation("org.springframework.boot:spring-boot-starter-web:+")
    implementation("org.springframework.boot", "spring-boot-starter-web", "+")

    // 导入项目根目录下lib目录中的jar依赖
    implementation(files("lib/file.jar", "lib/file2.jar"))

    // 导入项目根目录下lib目录中所有的jar依赖
    implementation(fileTree("lib"))

    implementation("org.springframework.boot:spring-boot-starter-data-redis") {
        // 排除指定组名的所有依赖
        // exclude("io.lettuce")
        // 排除指定组名和模块名的依赖
        exclude("io.lettuce", "lettuce-core")
    }
    
    testImplementation(platform("org.junit:junit-bom:6.0.0"))
    testImplementation("org.junit.jupiter:junit-jupiter")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}
```

```kotlin
// kotlin的lambda写法
// plugins是一个方法
plugins {
    // 指定使用的插件
    // java插件包含编译、测试等各种任务
    // 官方插件不用指定版本，非官方插件要指定版本
    id("java")
}

// 项目的group和version
group = "com.handle"
version = "1.0-SNAPSHOT"

// 配置仓库
repositories {
    mavenCentral()
}

// 配置依赖
dependencies {
    testImplementation(platform("org.junit:junit-bom:6.0.0"))
    testImplementation("org.junit.jupiter:junit-jupiter")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}

// 测试任务配置
tasks.test {
    useJUnitPlatform()
}
```

### Lombok配置

```kotlin
dependencies {
    compileOnly("org.projectlombok:lombok:1.18.48")
    annotationProcessor("org.projectlombok:lombok:1.18.48")
    testCompileOnly("org.projectlombok:lombok:1.18.48")
    testAnnotationProcessor("org.projectlombok:lombok:1.18.48")
}
```

## Gradle依赖

### Gradle依赖类型

- implementation不支持依赖传递，api支持依赖传递
- 除了模块间依赖引用其它情况优先使用implementation

|依赖类型|说明|用例|
|:-|:-|:-|
|compileOnly|for dependencies that are necessary to compile your production code but shouldn’t be part of the runtime classpath，Java插件提供，只在编译期需要|Lombok|
|runtimeOnly (supersedes runtime)|only used at runtime, not for compilation，Java插件提供，只在运行期需要|数据库驱动|
|implementation (supersedes compile)|used for compilation and runtime，Java插件提供，编译、运行都需要|spring-context|
|testCompileOnly|same as compileOnly except it’s for the tests，Java插件提供，只在测试类编译期需要||
|testRuntimeOnly|test equivalent of runtimeOnly，Java插件提供，只在测试类运行期需要||
|testImplementation|test equivalent of implementation，Java插件提供，测试类编译、运行都需要||
|providedCompile|war插件提供支持，编译、测试需要，运行时由容器提供支持，无需打入war包中|servlet-api、jsp-api|
|api|Java Library Plugin offers two additional configurations,for dependencies that are required for compiling both the module and any modules that depend on it，java-library插件提供，支持依赖传递，编译和运行期需要||
|compileOnlyApi|Java Library Plugin offers two additional configurations,for dependencies that are required for compiling both the module and any modules that depend on it，java-library插件提供，被依赖模块和依赖模块，编译期需要，运行期不需要||

### Gradle依赖写法

- 直接依赖

```groovy
dependencies {
    // 简写
    implementation 'org.springframework.boot:spring-boot:3.3.5'

    // 遍历所有仓库用最新版本，属于动态版本声明，不建议使用
    implementation 'org.springframework.boot:spring-boot:+'

    // 全写
    implementation group: 'org.springframework.boot', name: 'spring-boot', version: '3.3.5'
}
```

- 项目依赖

```kotlin
// 这个项目要在setting.gradle中声明才可以
implementation(project(":subProjectName"))
```

```groovy
dependencies {
    // 这个项目要在setting.gradle中声明才可以
    implementation project(':subproject01')
}
```

- 本地依赖

```groovy
dependencies {
    // 假设项目根目录下有个lib文件夹存放了各种jar包
    implementation files('lib/xxx1.jar', 'lib/xxx2.jar')
    implementation fileTree('dir':'lib', includes:['*.jar'], excludes:['*.war'])
}
```

```groovy
// 实际上是dependencies({})的另一种写法
dependencies {
    
    // Use JUnit Jupiter for testing.
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.3'

    implementation 'org.springframework.boot:spring-boot:3.3.5'
}
```

- 传递依赖

```kotlin
plugins {
    // 首先引入插件
    id("java-library")
}

dependencies {
    // 假设子项目a依赖b，b依赖c
    // 由于implementation不支持依赖传递，因此子项目a是不能使用c的
    // 而用api，在子项目a中这样写就可以使用c了
    api(project(":b"))
}
```

## Gradle依赖冲突解决

- 默认采用最新版本（官方默认的方式就够了）

- exclude排除依赖

```groovy
dependencies {
    implementation('org.springframework.boot:spring-boot:3.3.5') {
        // 支持如下排除写法
        exclude group: 'org.slf4j'
        exclude module: 'slf4j-api'
        exclude group: 'org.slf4j',module: 'slf4j-api'
    }
    // 排除后手动引入
    implementation 'org.slf4j:slf4j-api:1.4.0'
}
```

- 不允许依赖传递，不建议使用

```groovy
dependencies {
    implementation('org.springframework.boot:spring-boot:3.3.5') {
        transitive(false)
    }
}
```

- 强制使用某个版本，官方推荐

```kotlin
dependencies {
    // 加两个英文感叹号
    implementation("org.slf4j:slf4j-api:1.4.0!!")

    // 或者这种写法
    implementation("org.slf4j:slf4j-api:1.4.0") {
        version {
            strictly("1.4.0")
        }
    }
}
```

```groovy
dependencies {
    // 加两个英文感叹号
    implementation 'org.slf4j:slf4j-api:1.4.0!!'

    // 或者这种写法
    implementation('org.slf4j:slf4j-api:1.4.0') {
        version {
            strictly("1.4.0")
        }
    }
}
```

- 配置遇到依赖冲突的时候，构建失败

```groovy
configuration.all() {
    Configuration configuration -> configuration.resolutionStrategy.failOnVersionConflict()
}
```

## Gradle插件

### 脚本插件

- 本质是一个脚本文件，它是模块化的基础，可以通过它来做依赖的版本管控

- 自定义一个xxx.gradle文件，然后在build.gradle引入进来

```groovy
apply from: "xxx.gradle"
```

### 内部插件

- 对象插件，就是实现了org.gradle.api.Plugin接口的插件，每个Java Gradle插件都有一个plugin id

```groovy
// dsl写法
plugins {
    id 'java'
}

// apply的map具名参数写法，value可以是插件id、插件的全限定类名、插件的类名（插件所在类的包已经被引入的情况）
apply plugin:'java'

// apply的闭包写法
apply {
    plugin 'java'
}
```

### 第三方插件

```groovy
// 这种写法必须保证插件已经被托管了，否则要用传统方式
plugins {
    id 'org.springframe.boot' version '2.4.1'
}

// 传统方式，要求这个buildscript要在文件开头
buildscript {
    ext {
        // 定义版本
        v="3.2.0"
    }
    repositories {
        // 插件从哪里下载
    }
    dependencies {
        // 引入插件插件gav
        classpath("g:a:${v}")
    }
}

apply plugin: "io.spring.dependency-management"
```

### 用户自定义插件

- 创建buildSrc目录，它是Gradle默认的插件目录
    - 新建gradle模块，模块名为buildSrc
    - 只保留里面的build文件夹、src目录和build.gradle文件
    - 项目根目录settings.gradle中对buildSrc的声明删了

```groovy
// 实现接口
class MyPlugin implements Plugin<Project> {
    void apply(Project project) {
        // todo
    }
}
// 应用插件
apply plugin: MyPlugin
```

## 自定义任务

可以用register或create方法来注册任务

register方法以懒加载方式注册任务

### 通过lambda来自定义任务

```kotlin
// 参数1 任务名
// 参数2 lambda编写的任务具体操作
tasks.register("myTask") {
    // 默认在other组
    // 如果是用idea，刷新项目后会在右边的gradle面板的other列表看到自定义的任务
    // 可以自定义任务在那个组下
    group = "build"

    // 命令行执行 gradle tasks时
    // 可以看到 myTask - 我是任务描述
    description = "我是任务描述"

    // 前置任务
    // 执行该任务时会自动先执行前置任务
    // 写法一 传任务名
    dependsOn("classes")
    // 写法二 传任务对象
    dependsOn(tasks.classes)
    
    // 每个任务包含一个完整的操作（Action）列表
    // 每个操作都可以做一些自定义的具体内容
    // 传入对应的操作（Action）到队列中，就可以依次执行了
    // 用doFirst向任务队列头部插入新的操作
    doFirst {
        // 这种获取命令行参数的方式废弃了，但是还能用，先凑合
        println("我是myTask，自定义任务开始，命令行参数：${project.properties["myParam"]}")
    }
    // 用doLast向任务队列尾部插入操作
    doLast {
        println("我是myTask，自定义任务结束")
    }
}
```

### 通过自定义任务类来自定义任务

```kotlin
open class MyTask : DefaultTask() {
    private var name: String = ""

    // @TaskAction 声明此方法为任务的 Action
    // 执行任务的时候会自动执行这些 Action，顺序：myFun2->myFun1
    @TaskAction
    fun myFun1() {
        println("我是myFun1")
    }

    @TaskAction
    fun myFun2() {
        println("我是myFun2")
    }

    // 无 @TaskAction 就是普通方法
    fun setName(name: String) {
        this.name = name
        println(this.name)
    }
}

// 指明自定义任务类
tasks.register<MyTask>("myTask") {
    // 调用普通方法
    setName("handle")

    dependsOn(tasks.classes)
}
```

### 执行自定义任务

```sh
gradle myTask

# 传入参数
gradle myTask -PmyParam=hello
```

### 默认任务

```kotlin
// 默认任务，可以指定多个
// 当执行 gradle 命令时，会自动执行默认任务
defaultTasks("myTask")
```

### 前置任务

#### 给自定义任务设置前置任务

```kotlin
tasks.register("myTask") {
    // 前置任务
    // 执行该任务时会自动先执行前置任务
    // 写法一 传任务名
    dependsOn("classes")

    // 写法二 传任务对象
    dependsOn(tasks.classes)
}
```

#### 给插件的任务设置前置任务

```kotlin
// 写法一 通过任务对象
tasks.classes {
    dependsOn("myTask")
}

// 写法二 通过任务名
tasks.named("classes") {
    dependsOn("myTask")
}
```

### Gradle内置的任务

除了插件提供的任务和自定义任务外

Gradle也提供了一些内置的任务类型，如：Copy、Delete等

```kotlin
tasks.register<Copy>("myCopy") {
    from("build/classes")
    into("build/app")
    dependsOn(tasks.classes)
}
```

## buildSrc模块

在Gradle中有一个特殊机制

可以创建一个名为buildSrc的模块

它支持自定义能够在kts脚本中使用的内容

buildSrc模块不能在settings.gradle中include，它是自动加载的

### 自定义仓库

- 创建buildSrc模块
- 删掉include该模块的语句

- 在buildSrc模块的build.gradle.kts中

```kotlin
plugins {
    // 这种写法没研究过，先跟着做
    `kotlin-dsl`
}
```

- 创建目录src/main/kotlin，在该目录下创建文件MyFile.kt

```kotlin
import org.gradle.api.artifacts.dsl.RepositoryHandler

// 自定义一个仓库
fun RepositoryHandler.alibaba() = maven {
    setUrl("https://maven.aliyun.com/repository/public")
}
```

- 然后在父项目可以这样引用这个自定义仓库

```kotlin
subprojects {
    repositories {
        alibaba()
    }
}
```

### 自定义版本管理

- 在src/main/kotlin目录下创建对象Version.kt

```kotlin
import org.gradle.api.JavaVersion

object Version {
    val sourceVersion = JavaVersion.VERSION_27
    val targetVersion = JavaVersion.VERSION_27
    val springBootVersion = "4.2.0"
}
```

- 在父项目可以这样引用

```kotlin
subprojects {
    apply(plugin = "java")
    configure<JavaPluginExtension> {
        sourceCompatibility = MyVersion.sourceVersion
        targetCompatibility = MyVersion.targetVersion
    }
}
```

- 在别的子项目中这样引用

```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web:${MyVersion.springBootVersion}")
}
```

### 自定义插件

```kotlin
package com.handle.plugin

import org.gradle.api.Plugin
import org.gradle.api.Project

class MyPlugin : Plugin<Project> {
    // 插件应用时会直接调用apply方法
    override fun apply(target: Project) {
        println("MyPlugin 被加载了")

        // 可以在这里注册任务
        target.tasks.register("myTask") {
            doFirst {
                println("myTask 执行了")
            }
        }
    }
}
```

- 在buildSrc模块的build.gradle.kts中

```kotlin
// 声明自定义插件
gradlePlugin {
    plugins {
        create("myPlugin") {
            // 插件ID
            id = "my-plugin"
            // 插件实现类
            implementationClass = "com.handle.plugin.MyPlugin"
        }
    }
}
```

- 在别的子项目中使用

```kotlin
plugins {
    id("my-plugin")
}
```

## 生命周期钩子

Gradle项目构建流程
    - 开始构建
    - 初始化 执行settings.gradle
    - 配置项目 执行build.gradle
    - Task准备完成
    - 执行阶段 执行Task中的行为
    - 结束构建

如果希望在Gradle的整个生命周期中的不同时段执行一些操作

可以使用官方提供的生命周期钩子函数

- 构建初始阶段
    - `gradle.settingsEvaluated`
        - 完成项目的配置阶段之后调用
        - 只能定义在setting.gradle或init.gradle脚本中
    - `gradle.projectsLoaded`
        - 所有项目加载之后调用
        - 只能定义在setting.gradle或init.gradle脚本中

- 配置阶段
    - `gradle.beforeProject`
        - 每个项目完成配置之前调用
        - 只能定义在setting.gradle或init.gradle脚本中
    - `gradle.afterProject` 每个项目完成配置之后调用
    - `gradle.projectsEvaluated` 所有项目全部完成配置之后调用
    - `gradle.taskGraph.whenReady` 全部任务图已经构建完成可以就绪后调用

- 执行阶段
    - 视频里面列举的几个函数已经废弃了，后面有空看文档补充吧

```kotlin
gradle.settingsEvaluated {
    println("开始构建")
}

// 已经废弃，不建议使用
gradle.buildFinished {
    println("构建结束")
}

// 下面只是演示生命周期钩子的使用
// beforeTask和afterTask已经废弃，不推荐使用了
var time: Long = 0
gradle.taskGraph.beforeTask {
    time = System.currentTimeMillis()
}
gradle.taskGraph.afterTask {
    val useTime = System.currentTimeMillis() - time
    println("任务： $name 执行耗时：${useTime}ms")
}
```

## 项目发布

可以将Gradle项目发布到Maven仓库中

- 插件

```kotlin
plugins {
    id("java")
    // 发布插件
    id("maven-publish")
}
```

- 发布配置

```kotlin
publishing {
    publications {
        create<MavenPublication>("myMavenPublication") {
            groupId = "com.handle"
            artifactId = "demo"
            version = "1.0.0"
            // 发布为jar包
            from(components["java"])

            // 版本映射
            versionMapping {
                // 对于 API 用途，从 runtimeClasspath 的解析结果中获取版本
                usage("java-api") {
                    fromResolutionOf("runtimeClasspath")
                }
                // 对于运行时用途，从解析结果中获取版本
                usage("java-runtime") {
                    fromResolutionResult()
                }
            }
        }
    }
    repositories {
        // 发布到Maven本地仓库
        mavenLocal()
    }
}
```

如果使用的是idea，就可以在右边gradle面板的publishing里面看到publishing任务，执行就可以了

## Gradle常用指令

- gradle指令要在含有build.gradle的目录执行

|指令|说明|
|:-|:-|
|gradle clean|删除build目录|
|gradle classes|编译业务代码和配置文件|
|gradle test|编译测试代码，生成测试报告|
|gradle build|构建项目，默认会执行gradle classes、gradle test，以及打包|
|gradle build -x test|跳过测试构建项目|

## Gradle单项目

### 创建Gradle项目

- Idea新建项目选择Gradle进行创建

- 命令行创建

```sh
gradle init
```

### settings.gradle

- Settings file to define build name and subprojects

```groovy
rootProject.name = 'hello-world'
```

### build.gradle

- Build script of the project

```groovy
plugins {
    id 'java'
    //  This plugin provides useful defaults and Gradle tasks
    id 'org.springframework.boot' version '3.2.0'
}

// 提供版本管理支持，填写依赖的时候可以省略版本号
apply plugin: 'io.spring.dependency-management'

// 项目名称和版本，项目名在settings.gradle中定义了
group = 'com.handle'
version = '1.0-SNAPSHOT'

// 依赖
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

## Gradle聚合项目

### 子项目之间的依赖写法

```kotlin
implementation(project(":subProjectName"))
```

### 子项目公共部分写在一处

```kotlin
// 对所有子项目生效
subprojects {
    apply(plugin = "java")
    
    group = "com.handle"
    version = "1.0-SNAPSHOT"
}
```

### groovy写法

- 新建gradle项目，删掉src目录

- build.gradle

```groovy
plugins {
    //  This plugin provides useful defaults and Gradle tasks
    // 包含了io.spring.dependency-management，其作用是提供版本管理支持，填写依赖的时候可以省略版本号
    // 在根项目中只声明不应用，在子项目再应用，这里起到的是插件版本统一管理的作用
    id 'org.springframework.boot' version '3.2.4' apply false
}

// 提供版本管理支持，填写依赖的时候可以省略版本号
apply plugin: 'io.spring.dependency-management'

subprojects {
    apply {
        // 如果不是核心插件，父项目的build.gradle声明过的插件才能应用到子项目
        plugin('java')
        plugin('org.springframework.boot')
        plugin('io.spring.dependency-management')
    }

    group = 'com.handle'
    version = '1.0-SNAPSHOT'

    ext {
        // 在此处指定依赖版本
        cglibVersion = '3.3.0'
    }

    // 子项目通用依赖都在这里声明
    dependencies {
        // 使用指定的依赖版本
        implementation "cglib:cglib:${cglibVersion}"

        compileOnly 'org.projectlombok:lombok'

        // Junit依赖
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
        testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }

    tasks.named('test') {
        // Use JUnit Platform for unit tests.
        useJUnitPlatform()
    }
}
```

- 子项目测试类写法

```java
// 需要指定主启动类，否则会报错
@SpringBootTest(classes = Application.class)
public class ApplicationTest {
    @Test
    public void test() {
        System.out.println("test success!!!!!!");
    }
}
```

## 发布Maven BOM

```groovy
plugins {
    // BOM插件
    id 'java-platform'
    // 发布插件
    id 'maven-publish'
}

group = 'com.handle'
version = '1.0-SNAPSHOT'

dependencies {
    constraints {
        // 这些依赖会生成到BOM的dependencyManagement里面
        api 'com.alibaba.cloud:spring-cloud-alibaba-dependencies:2023.0.0.0-RC1'
        api 'org.springframework.cloud:spring-cloud-dependencies:2023.0.0'
        api 'org.springframework.boot:spring-boot-dependencies:3.2.0'
    }
}

publishing {
    publications {
        // 自定义发布名称为bom
        bom(MavenPublication) {
            // 发布成BOM
            from components.javaPlatform
        }
    }
}
```

## 使用Maven BOM

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0' apply false
}

apply plugin: 'io.spring.dependency-management'

group = 'com.handle'
version = '1.0-SNAPSHOT'

dependencyManagement {
    imports {
        // 引入自定义BOM
        mavenBom 'com.handle:java-one-dependencies:1.0-SNAPSHOT'
    }
}

dependencies {
    // 不用写版本号
    implementation 'org.projectlombok:lombok'
}
```

## 打包部署

### 非模块打包

## 生成最小的jre

```groovy
// 非模块打包，不能有module-info.java文件
plugins {
    id("org.beryx.runtime") version "1.13.1"
}

application {
    mainClass = 'com.handle.package2exe.demo.Application'
}
runtime {
    // --strip-debug：去掉所有模块中的调试信息，从而减小生成的JRE的大小
    // --compress：压缩生成的JRE。2表示最高级别的压缩，但这也会增加构建时间
    // --no-header-files：不包含头文件。减少不必要的文件，进一步减小JRE的大小
    // --no-man-pages：不包含手册页。进一步减小JRE的大小
    options = ['--strip-debug', '--compress', 'zip-6', '--no-header-files', '--no-man-pages']
    // 依赖的模块，建议先创建module-info.java文件确认依赖哪些模块，然后再复制到这里来，最后删掉module-info.java文件
    modules = ['javafx.controls']
    // 指定生成的jre位置
    distDir = file("${projectDir}/build/${project.name}/jre")
}
```

## 打包成exe

- 1.build.gradle

```groovy
plugins {
    // 打包插件
    id "edu.sc.seis.launch4j" version "3.0.6"
}

launch4j {
    // 唯一需要指定的是mainClassName
    mainClassName = 'com.handle.package2exe.demo.Application'
    icon = "${projectDir}/icon/logo.ico"
    // 输出路径
    outputDir = "${projectDir}/build/${project.name}"
    // 生成的exe文件相同目录下，放置jre（文件夹），不配置Java运行环境也可以运行
    bundledJrePath = "./jre"
}
```

- 2.执行createExe任务

- 3.build/launch4j目录下就是打包生成的依赖和exe文件

## jre和exe整合

```groovy
plugins {
    // 生成exe文件和jar依赖文件夹
    id("edu.sc.seis.launch4j") version "3.0.6"
    // 非模块打包，不能有module-info.java文件
    id("org.beryx.runtime") version "1.13.1"
}

application {
    mainClass = 'com.handle.package2exe.demo.Application'
}

launch4j {
    // 唯一需要指定的是mainClassName
    mainClassName = 'com.handle.package2exe.demo.Application'
    icon = "${projectDir}/icon/logo.ico"
    // 输出路径
    outputDir = "${projectDir}/build/${project.name}"
    // 生成的exe文件相同目录下，放置jre（文件夹），不配置Java运行环境也可以运行
    bundledJrePath = "./jre"
}

runtime {
    options = ['--strip-debug', '--compress', 'zip-6', '--no-header-files', '--no-man-pages']
    // 依赖的模块，建议先创建module-info.java文件确认依赖哪些模块，然后再复制到这里来，最后删掉module-info.java文件
    modules = ['javafx.controls']
    // 指定生成的jre位置，不知道为什么不生效，只能通过复制任务复制到输出目录了
    distDir = file("${projectDir}/build/${project.name}/jre")
}

// 复制jre文件夹到输出目录
tasks.register('copyJre', Copy) {
    from "${projectDir}/build/jre"
    into "${projectDir}/build/${project.name}/jre"
    doLast {
        def sourceDirectory = file("${projectDir}/build/jre")
        sourceDirectory.deleteDir()
    }
}

tasks.copyJre.dependsOn('jre')
tasks.createExe.dependsOn('copyJre')
```

### 模块打包

执行jlink任务，最终会在build/image/bin目录下生成项目名对应的文件和bat文件，双击该bat文件即可运行程序

- 创建module-info.java文件

```java
// 模块的名称，它的命名规范与包一致
module com.handle.package2exe.demo {
    // 依赖的模块，按需导入
    requires javafx.controls;
    // 必须导出，否则运行报错
    exports com.handle.package2exe.demo;
}
```

- 配置buil.gradle文件

```groovy
plugins {
    // 模块打包，需要有module-info.java文件
    id("org.beryx.jlink") version "3.1.1"
}

application {
    // 指定主类和主模块
    mainClass = 'com.handle.package2exe.demo.Application'
    mainModule = 'com.handle.package2exe.demo'
}

jlink {
    // --strip-debug：去掉所有模块中的调试信息，从而减小生成的JRE的大小
    // --compress：压缩生成的JRE。2表示最高级别的压缩，但这也会增加构建时间
    // --no-header-files：不包含头文件。减少不必要的文件，进一步减小JRE的大小
    // --no-man-pages：不包含手册页。进一步减小JRE的大小
    options = ['--strip-debug', '--compress', '2', '--no-header-files', '--no-man-pages']
    mergedModule {
        // 主模块以来的模块都填写
        requires 'javafx.controls'
    }
    launcher {
        // 会在build/image/bin目录下生成项目名对应的文件和bat文件
        name = "${project.name}"
        // 双击运行bat文件后控制台窗口自动关闭
        noConsole = true
    }
}
```
