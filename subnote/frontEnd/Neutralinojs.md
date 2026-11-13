# Neutralinojs

相比于Electron，Neutralinojs的打包体积更小，运行的内存更少，运行速度看官方的对比也是更少的运行时间

- 安装neu命令行工具

```sh
npm install -g @neutralinojs/neu
```

- 也可以不安装neu命令行工具，用npx执行对应的neu命令就行了

```sh
# 例：npx @neutralinojs/neu create myapp
npx @neutralinojs/neu <command>
```

## 使用neu

### 使用普通js构建Neutralinojs应用

```sh
# 创建新项目，默认使用 neutralinojs-minimal 模板
neu create 项目名

# 使用指定模板：neutralinojs/neutralinojs-zero，可能需要魔法
# 这个模板比neutralinojs-minimal更加精简，适合从零搭建，或者用它配置前端框架
neu create myapp --template neutralinojs/neutralinojs-zero

cd 项目名

# 运行项目
neu run

# 构建项目
# --release：将二进制文件打包到.zip文件
neu build --release
```

### 使用前端框架构建Neutralinojs应用

#### 使用neu命令行工具已有的模板创建Neutralinojs应用

```sh
# 例：创建react应用
neu create myapp --template codezri/neutralinojs-react

cd myapp

# Start the React development server with Neutralinojs
neu run

# Build the React and Neutralinojs app
neu build
```

#### 使用任意前端框架构建Neutralinojs应用

以react前端框架为例

```sh
# 首先用 neutralinojs/neutralinojs-zero 模板，创建一个空的Neutralinojs项目
# 可能需要魔法
neu create myapp --template neutralinojs/neutralinojs-zero

# 然后进入项目
cd myapp

# 然后使用你最喜欢的前端框架创建一个新的项目
# 例1：创建react项目
npx create-react-app react-src

# 例2：创建vue项目，默认的项目名为vue-project
npm create vue@latest

# 然后配置 Neutralinojs 项目
# 由于neutralinojs-zero 创建了一个目前不需要的www目录，把它删了
rm -rf www

# 配置Neutralinojs项目以支持前端框架
# 打开neutralino.config.json
# 设置documentRoot的目录为你的前端框架的构建输出目录
# 这样Neutralinojs应用就知道前端框架的资源位置了
# 例1：react通常生成build输出到build目录，因此设置如下
"documentRoot": "/react-src/build/"

# 例2:vue的构建输出目录
"documentRoot": "/vue-project/dist/"

# 从前端框架的默认资源目录下加载一个图标
# 例1：react
"modes": {
    "window": {
        // --- other options
        "icon": "/react-src/public/logo192.png"
    }
}

# 例2：vue
"modes": {
    "window": {
        // --- other options
        "icon": "/vue-project/public/favicon.ico"
    }
}
```

默认情况下，neutralinojs-zero模板配置请求neu命令行工具从github发布中下载Neutralinojs客户端（即neutralino.js）

然后neu命令行工具通过复制neutralino.js来创建你的应用包

然而，你可以从npm仓库下载neutralino.js并和你的应用前端捆绑在一起

```json
//  例1：react
"cli": {
    // --- other options
    "resourcesPath": "/react-src/build/",
    // ---
    "clientLibrary": "/www/neutralino.js", // <--- 删除此属性以避免从github发布中下载neutralino.js
    // ---
}

//  例2：vue
"cli": {
    // --- other options
    "resourcesPath": "/vue-project/dist/",
    // ---
    "clientLibrary": "/www/neutralino.js", // <--- 删除此属性以避免从github发布中下载neutralino.js
    // ---
}
```

至此，你可以像Neutralinojs应用那样构建和运行react应用了

- 例1：构建和运行react应用

```sh
# 进入react项目
cd react-src

# 构建react应用
npm run build

# 进入Neutralinojs项目
cd ..

# 运行Neutralinojs应用
neu run
```

- 例2：构建和运行vue应用

```sh
# 进入react项目
cd vue-project

# 安装依赖包
npm i

# 构建应用
npm run build

# 进入Neutralinojs项目
cd ..

# 运行Neutralinojs应用
neu run
```

### 用@neutralinojs/lib初始化本地API

你可以通过`neu run`运行应用，但你还不能使用本地API，因为它还没初始化

可以使用如下命令安装Neutralinojs客户端来初始化本地API

```sh
# 例1：react
cd react-src

# 例2：vue
cd vue-project

# 安装
npm install @neutralinojs/lib
```

接下来是加载Neutralinojs的全局变量

你可以用你选择的框架，通过在根html文件中包含JavaScript脚本来实现

react的根html文件通常在`./public/index.html`，可以添加如下代码来加载这个客户端库

vue的根html文件通常在`./index.html`，可以添加如下代码来加载这个客户端库

```html
<script src="%PUBLIC_URL%/__neutralino_globals.js"></script>
```

接下来是确保这个客户端库从你的前端应用入口文件那里初始化了

react的应用入口文件通常是`./src/index.js`

vue的应用入口文件通常是`./src/main.js`

因此，初始化过程可以在该文件，通过调用@neutralinojs/lib包的init方法进行

- 例1:react

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

// Import init function from "@neutralinojs/lib"
import { init } from "@neutralinojs/lib"

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

init(); // Add this function call
```

- 例2:vue

```js
import './assets/main.css'

import { createApp } from 'vue'
import App from './App.vue'

// Import init function from "@neutralinojs/lib"
import { init } from "@neutralinojs/lib"

createApp(App).mount('#app')

init(); // Add this function call
```

然后验证客户端库是否正确加载了

咱们通过用filesystem API读取Neutralinojs应用的当前目录来进行验证

首先在neutralino.config.json中更新允许Neutralinojs应用调用的api

可以允许filesystem整个命名空间的api调用（filesystem.*）

或者只允许指定命名空间的单个api调用，如下

```json
    "nativeAllowList": [
        "app.*",
        "filesystem.readDirectory"
    ],
```

对于react，接下来在`./src/App.js`添加如下代码

```js
import { useEffect } from 'react'
import './App.css';

// Import filesystem namespace
import { filesystem } from "@neutralinojs/lib"

function App() {

  // Log current directory or error after component is mounted
  useEffect(() => {
    filesystem.readDirectory('./').then((data) => {
        // 打印当前目录
      console.log(data)
    }).catch((err) => {
      console.log(err)
    })
  }, [])

  return (
    <div className="App">
      My Neutralinojs App
    </div>
  );
}

export default App;
```

对于vue，接下来在`./src/App.vue`添加如下代码

```vue
<script setup>
  // 其它内容省略

  // Import filesystem namespace
  import { filesystem } from "@neutralinojs/lib"

  import { onMounted } from 'vue'

  onMounted(() => {
    filesystem.readDirectory('./').then((data) => {
      console.log(data)
    }).catch((err) => {
      console.error(err)
    })
  })
</script>
```

最后运行Neutralinojs应用

```sh
# 例1：react
cd react-src

# 例2：vue
cd vue-project


npm run build

cd ..

# --window-enable-inspector：允许你打开开发者工具
# 在Neutralinojs应用的任意位置鼠标右键然后`inspect element`就可以打开开发者工具了
neu run -- --window-enable-inspector
```

### 热重载

前端框架可以使用HMR (Hot Module Replacement)特性来提高开发效率

但是对于使用了前端框架的Neutralinojs应用，有两个http服务被启动

一个是Neutralinojs资源服务，另一个是前端框架的开发服务

neu命令行工具提供了内置特性来启用HMR，通过修补根html文件来实现

修改配置文件来激活热重载

```json
// react项目设置
"cli": {
    // --- other options
    "frontendLibrary": {
        // 1.告诉neu命令行工具根html文件和开发服务的地址
        "patchFile": "/react-src/public/index.html",
        "devUrl": "http://localhost:3000",
        // 2.添加特定前端库开发命令
        "projectPath": "/react-src/",
        "initCommand": "npm install",
        "devCommand": "BROWSER=none npm start",
        "buildCommand": "npm run build"
    }
}

// vue项目设置
"cli": {
    // --- other options
    "frontendLibrary": {
        // 1.告诉neu命令行工具根html文件和开发服务的地址
        "patchFile": "/vue-project/index.html",
        "devUrl": "http://localhost:5173",
        // 2.添加特定前端库开发命令
        "projectPath": "/vue-project/",
        "initCommand": "npm install",
        "devCommand": "BROWSER=none npm run dev",
        "buildCommand": "npm run build"
    }
}
```

最后，通过如下命令运行Neutralinojs应用

```sh
# 执行devCommand
# 以开发模式启动react开发服务并运行Neutralinojs应用
neu run
```

### 打包

```sh
# 首先会执行buildCommand
# 因此应用程序包会使用当前react项目的源代码
neu build --release
```

## Neutralinojs应用配置

在项目目录下的`neutralino.config.json`文件，包含了应用配置的详情

每个Neutralinojs应用都会从配置文件加载三个基础属性：applicationId、url和defaultMode

但是配置文件不是Neutralinojs必须的，因为Neutralinojs框架通常会为所有配置选项加载合理的默认值

你可以使用一个结构良好的配置文件开发Neutralinojs应用

也可以不需要配置文件，而是通过命令行参数来启动一个Neutralinojs应用

```json
{
    // 标识应用程序的唯一字符串
    "applicationId": "js.neutralino.sample",
    // 应用程序版本
    "version": "1.0.0",
    // 应用程序的默认运行模式，接受的值为：window、browser、cloud和chrome
    "defaultMode": "window",
    // 静态服务器的文档根目录
    // 如果使用resources目录作为文档根目录，可以设置如下
    // documentRoot和url的配置要合适
    // 如："documentRoot": "/resources/","url": "/"
    // 或："documentRoot": "/","url": "/resources/"
    "documentRoot": "/resources/",
    // 应用程序的入口URL，Neutralinojs将首先加载这个URL
    // 接受相对和绝对URL
    // "/"：表示初始加载 "http://localhost:<port>/" URL（内部即加载"/index.html"）
    // 也可以使用远程url，如："http://example.com"
    "url": "/",
    // 启用/禁用后台服务（禁用静态文件服务功能和本地接口消息功能）
    // 如果加载一个远程url到浏览器组件，你可以设置为false
    // 如果从本地源文件加载资源，确保设置为true
    // 默认值是false
    "enableServer": true,
    // 启用或禁用本地接口
    // 需要用到本地接口功能就设置为true
    // 默认值是false
    "enableNativeAPI": true,
    "nativeAllowList": [
        "app.*"
    ],
    "modes": {
        "window": {
        "title": "easy-cut",
        "width": 800,
        "height": 500,
        "minWidth": 400,
        "minHeight": 200,
        "icon": "/www/icon.png"
        }
    },
    "cli": {
        "binaryName": "easy-cut",
        "resourcesPath": "/www/",
        "extensionsPath": "/extensions/",
        "clientLibrary": "/www/neutralino.js",
        "binaryVersion": "6.9.0",
        "clientVersion": "6.9.0"
    }
}
```

## 本地接口

为了通过Neutralinojs服务，与本地操作进行交互，Neutralinojs为开发人员提供了一个JavaScript客户端库（即 Neutralino.js，一下称客户端库）

Neutralinojs的JavaScript客户端实现在Neutralino.js文件中

这是Neutralinojs项目中必须有这个客户端库的原因

客户端库将其JavaScript接口暴露给浏览器的window作用域

你可以用普通js，通过Neutralino或window.Neutralino访问这些接口

如果你使用了前端框架来构建你的应用前端，可以从`@neutralinojs/lib`这个npm模块导入Neutralinojs命名空间来使用这些接口

```js
import { app } from '@neutralinojs/lib';

const conf = await app.getConfig();  // Vanilla Js: await Neutralino.app.getConfig()
```

- 假设你需要从操作系统获取一个环境变量值，本地接口工作流程为
    - 调用 Neutralino.os.getEnv这个js方法
    - 客户端库发送WebSocket信息给Neutralinojs服务
    - Neutralinojs服务执行本地操作，获取给定的环境变量值
    - Neutralinojs服务发送包含该环境变量值的WebSocket信息给客户端库
    - 客户端库解析从服务器得到的promise，最终得到结果

客户端库维护一个任务池，来映射Neutralinojs服务的消息，通过请求的uuid来进行匹配

Neutralinojs所有模式使用都这种通信机制，为开发人员提供了许多本地操作

在Neutralinojs应用配置文件中，可以使用 nativeAllowList和nativeBlockList配置允许的和不允许的本地接口

```json
{
  "nativeAllowList": [
    "app.*",
    "window.*",
    "os.execCommand"
  ],
  "nativeBlockList": [
    "filesystem.remove",
    "extensions.*"
  ]
}
```

## 发布应用

构建的时候会生成各个平台特定的二进制文件+资源文件resources.neu

打包的时候只需包含平台特定的二进制文件和resources.neu就可以了

如为linux_x64平台打包，则只需包含myapp-linux_x64和resources.neu这两个文件就行了

```sh
# 生成各个平台特定的二进制文件+资源文件resources.neu
# --release：将"neu build"生成的dist目录下以应用名为名称的整个文件夹，打包为"应用名-release.zip"文件,然后放到dist目录下
# 笔者认为还不如不加这个参数
neu build [--release]

# 生成各个平台特定的二进制文件（内置了资源文件）
neu build --embed-resources
```

内置资源文件的方式使得每次更新应用的资源文件必须重新构建

如果你想在各个发布版本之间独立更新资源文件，而不重新执行构建

使用标准的平台特定的二进制文件和resources.neu这种方式会更合适

此外还可以使用hschneider/neutralino-build-scripts这个社区项目打包安装脚本，具体请参考Neutralinojs官网教程

## 扩展

### 扩展作为子进程

- 在`neutralinojs.config.json`文件中定义扩展

```json
"nativeAllowList": [
    "app.*",
    // 记得加上
    "extensions.*"
],
"extensions": [
    {
        // id：标识扩展的唯一键
        "id": "js.neutralino.sampleextension",
        // 跨平台的启动扩展的命令
        "command": "node ${NL_PATH}/extensions/binary/main.js",
        // Linux系统的启动扩展的命令
        "commandLinux": "${NL_PATH}/extensions/binary/linux/ext_bin",
        // macOS系统的启动扩展的命令
        "commandDarwin": "${NL_PATH}/extensions/binary/mac/ext_bin",
        // Windows系统的启动扩展的命令
        "commandWindows": "${NL_PATH}/extensions/binary/win/ext_bin.exe"
    },
    {
        "id": "js.neutralino.binaryextension",
        "command": "node ${NL_PATH}/extensions/binary/main.js",
    },
    {
        "id": "js.neutralino.javaExtension",
        "command": "java -jar ${NL_PATH}/extension/neutralinojsExtension.jar"
    }
],
// 启用扩展
"enableExtensions": true,
// 开发阶段建议导出${NL_PATH}/.tmp/auth_info.json文件
// 扩展读取这个文件连接neutralinojs服务器
"exportAuthInfo": true
```

#### 以java做扩展为例

- 依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-websocket</artifactId>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <scope>provided</scope>
    </dependency>
</dependencies>
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <finalName>app</finalName>
            </configuration>
        </plugin>
    </plugins>
</build>
```

- 配置bean

```java
@Configuration
public class WebSocketConfig {
    @Bean
    public StandardWebSocketClient standardWebSocketClient() {
        return new StandardWebSocketClient();
    }

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper();
    }
}
```

- websocketHandler

```java
@Component
public class NeutralinoWebSocketHandler extends TextWebSocketHandler {
    private static ConnectionInfo connectionInfo;

    @Autowired
    private ObjectMapper objectMapper;

    @Autowired
    private WebSocketClient webSocketClient;

    @PostConstruct
    public void init() {
        connectToNeutralinoServer();
    }

    private void connectToNeutralinoServer() {
        File file = new File("/home/handle/Applications/code/web/neutralinojs/extension-demo/.tmp/auth_info.json");
        connectionInfo = null;
        connectionInfo = objectMapper.readValue(file, ConnectionInfo.class);
        String url = String.format("ws://localhost:%d?extensionId=%s&connectToken=%s", connectionInfo.getNlPort(), connectionInfo.getExtensionId(),
                                   connectionInfo.getNlConnectToken());
        webSocketClient.execute(this, url);
        System.out.println("正在连接 Neutralinojs Server：" + url);
    }

    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        System.out.println("已连接到 Neutralinojs Server！");
    }

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        JsonNode jsonNode = objectMapper.readTree(payload);

        if (!jsonNode.has("event")) {
            return;
        }
        String event = Optional.ofNullable(jsonNode.get("event")).map(JsonNode::asText).orElse("");

        if (event.isBlank()) {
            return;
        }
        switch (event) {
            case "helloExtension" -> {
                System.out.println("收到消息：" + payload);

                EventInfo eventInfo = new EventInfo();
                eventInfo.setEvent(event);
                eventInfo.setData("java已收到helloExtension消息");

                MessageInfo messageInfo = new MessageInfo();
                messageInfo.setId(session.getId());
                messageInfo.setMethod("app.broadcast");
                messageInfo.setAccessToken(connectionInfo.getNlToken());
                messageInfo.setData(eventInfo);
                try {
                    String json = objectMapper.writeValueAsString(messageInfo);
                    session.sendMessage(new TextMessage(json));
                    System.out.println("发送消息给neu：" + json);
                } catch (IOException e) {
                    throw new RuntimeException("发送消息异常", e);
                }
            }
        }
    }

    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        System.out.println("Connection closed: " + status);
        System.exit(0);
    }
}
```

- 实体类

```java
@Getter
@Setter
@ToString
public class ConnectionInfo {
    private String nlConnectToken;

    private String nlToken;

    private String extensionId = "js.neutralino.javaExtension";

    private Integer nlPort;
}

@Getter
@Setter
@ToString
public class MessageInfo {
    private String id;

    //  Native method name
    private String method;

    // Access token generated by the Neutralinojs server
    private String accessToken;

    // Parameters for the native method (optional)
    private EventInfo data;
}

@Getter
@Setter
@ToString
public class EventInfo {
    private String event;

    private Object data;
}
```

- 前端UI

```html
<h1>Neutralino Extension Demo</h1>
<button id="sendButton" onclick="onSendButtonClick()">发消息</button>
<p id="reply"></p>
</div>
<script src="/js/neutralino.js"></script>
<script src="/js/main.js"></script>
<script>
    // 发送消息给扩展
    async function onSendButtonClick() {
        let extension = "js.neutralino.javaExtension";
        let event = 'helloExtension';
        let data = {
            testValue: "helloworld",
        };

        await Neutralino.extensions.dispatch(extension, event, data);
    }

    // 接收扩展发来的消息
    Neutralino.events.on('helloExtension', function (event) {
        console.log('扩展回复:', event, new Date().getTime());
        const p = document.querySelector("#reply");
        p.innerHTML = JSON.stringify(event.detail);
    });
</script>
```

### 扩展作为主进程

不建议这么搞，demo已经做出来了但是暂时不想写到笔记，累了
