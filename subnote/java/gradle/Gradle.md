# Gradle

- 官网：<https://gradle.org/>

## 安装Gradle

- 参考Spring Boot支持的Gradle版本

- 参考Idea支持的Gradle版本：`idea_home\plugins\gradle\lib`

- 参考Gradle支持的jdk版本，需要先安装jdk并配置好

- 下载完整版（包含了文档和源码）

- 修改maven下载源
    - 在`GRADLE_HOME/init.d`目录下创建init.gradle文件，内容如下

- gradle-8.x版本这样写

```groovy
allprojects {
    // 项目依赖的下载地址
    repositories {
        mavenLocal()
        maven {
            name "aliyun";
            url "https://maven.aliyun.com/repository/public"
        }
        mavenCentral()
    }
    // build.gradle构建脚本使用的依赖的下载地址，一般是各种插件的下载地址
    buildscript {
        repositories {
            mavenLocal()
            maven {
                name "aliyun";
                url "https://maven.aliyun.com/repository/public"
            }
            mavenCentral()
        }
    }
}
```

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

- 配置环境变量
    - `GRADLE_HOME`
    - `GRADLE_USER_HOME`
        - gradle的本地仓库路径和gradle wrapper的缓存路径、缓存的路径
        - 下载的依赖会存放在`GRADLE_USER_HOME/caches/modules-2/files-2.1`目录下
        - 除了依赖目录还有别的目录，建议不要指定为maven的repository路径，避免目录混乱
    - `M2_HOME`，maven家目录，gradle默认会查找`~/.m2/repository`作为maven本地仓库路径，修改了maven本地仓库路径的话才需要定义

- 配置Path：`GRADLE_HOME\bin`

- 测试：`gradle -v`

## IDEA配置Gradle

- Settings->Build,Execution,Deployment->Gradle
    - Gradle user home，指定一个路径作为gradle的本地仓，gradle和maven的结构不一样，不要设置为maven本地仓库的路径
    - Build and run using，设置为IntelliJ IDEA，IDEA会对纯Java项目做优化，启动更快，如果构建有问题再改回默认Gradle
    - Run test using，设置为IntelliJ IDEA，不然用默认Gradle的话初始化测试都要等好几秒了，浪费时间
    - Distribution，设置为Local installation，并选择本地安装的gradle家目录
    - Gradle JVM，设置为本地安装的JDK即可

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

## Groovy

- 官网：<https://groovy-lang.org/>

- Groovy兼容Java语法

- 当Groovy的源文件定义了类的时候，这个类实际上实现了GroovyObject

- 当Groovy的源文件没有定义类的时候，这个源文件会被转换为扩展了Script的类
    - 这个类将作为脚本使用
    - 这个类使用源文件名作为类名
    - 源文件的内容被打包进run方法
    - 这个类会加入一个main方法使得可以外部执行该脚本

- 在Groovy的源文件中可以混合类的定义和脚本定义，此时不能定义一个和文件同名的类

- Groovy默认类、方法和字段是public修饰的

- Groovy使用def定义变量和方法，不建议使用具体的数据类型

- 无论变量、属性、方法、闭包的参数还是方法的返回值，类型都是可有可无的，都是在给变量赋值的时候才决定它们的类型

- Groovy基本类型也是对象，可以直接调用对象对应的方法

### Groovy属性

- 属性赋值
    - 对象.属性名=属性值
    - 对象的setter方法
    - 具名构造器
- 属性读取
    - 对象.属性名
    - 对象["属性名"]
    - 对象的getter方法
- 对类的属性的操作本质还是通过getter、setter方法完成的

### Groovy方法

- 方法声明
    - 参数类型、返回值类型可以省略
    - return关键字可以省略，默认使用方法最后一句的返回值作为方法的返回值
- 方法调用，在不导致二义性的时候，`()`可以省略

### Groovy字符串

- 单引号，作为字符串常量使用，没有运算能力

- 双引号，可通过`${}`引用变量，有运算能力

- 三引号，模板字符串，支持字符串换行
