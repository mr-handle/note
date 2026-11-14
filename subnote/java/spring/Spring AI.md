# Spring AI

Spring AI的版本选择也是要考虑跟Spring Boot版本兼容的

## ChatModel 和 ChatClient

ChatModel 和 ChatClient 是两个核心的抽象，它们分别代表了底层交互接口和高级应用封装

简单来说，ChatModel 是直接与底层大语言模型（LLM）通信的基础设施

而 ChatClient 则是基于 ChatModel 构建的高级 API，旨在简化复杂 AI 应用的开发

- ChatModel（底层对话模型）：
    - 它是直接与具体大语言模型交互的底层接口，提供基础的 call() 和 stream() 方法
    - 工作原理是接收 Prompt 作为输入，发送给后端大模型，并接收 ChatResponse 作为输出
    - 适合简单的模型交互场景或需要精细控制底层参数的模型实验

- ChatClient（高级聊天客户端）：
    - 它是基于 ChatModel 构建的高级封装，提供了流畅的链式 API（Fluent API）
    - 类似于应用程序开发中的“服务层”，它将与 LLM 及其他组件（如提示词模板、聊天记忆、RAG 组件等）交互的复杂性隐藏在背后，开发者可以快速组装一整套 AI 交互流程
    - 适合快速构建标准化的复杂 AI 服务和业务接口

- bom

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-bom</artifactId>
    <version>${spring.ai.bom.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

### 以Ollama的chat模型为例使用Spring AI

- 依赖

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-ollama</artifactId>
</dependency>
```

- 配置

```properties
# 解决调用大模型返回中文乱码问题
# 保证大模型返回响应的时候不会中文乱码
# 推荐配置文件的方式进行设置，否则就要在控制器类声明
# @RequestMapping(produces = "text/html;charset=utf-8") public class MyController {}
# 或声明 
# @GetMapping(value = "/myRequest", produces = "text/html;charset=utf-8") public String myMethodName(){ return xxx}

# 启用字符编码过滤器
# 只有设置为 true，Spring Boot 才会注册并激活 CharacterEncodingFilter
spring.servlet.encoding.enabled=true
# 强制请求和响应都使用指定的字符集
spring.servlet.encoding.force=true
# 告诉 Spring Boot 内部的 CharacterEncodingFilter 过滤器
# 在处理 HTTP 请求参数（如表单提交、URL 参数）和生成 HTTP 响应内容时，默认使用 UTF-8 进行解码和编码
spring.servlet.encoding.charset=UTF-8

# ollama的本地服务器地址
spring.ai.ollama.base-url=http://localhost:11434
# 使用的模型名称，ollma list命令列出的模型名称复制过来就行了
spring.ai.ollama.chat.model=gemma4:e4b-it-qat
```

- 控制器，使用ChatClient

```java
@RestController
class MyController {

    private final ChatClient chatClient;

    //  Spring AI自动装配了一个ChatClient.Builder
    public MyController(ChatClient.Builder chatClientBuilder) {
        this.chatClient = chatClientBuilder.build();
    }

    @GetMapping("/ai")
    String generation(String userInput) {
        return this.chatClient.prompt()
            .user(userInput)
            // 发送一个请求到AI模型
            .call()
            // 将AI模型的响应以String返回
            .content();
    }
}
```

- 控制器，使用ChatModel

```java
@RestController
public class ApplicationController {
    @Autowired
    private OllamaChatModel chatModel;

    @GetMapping("/ai/generate")
    public Map<String, String> generate(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return Map.of("generation", this.chatModel.call(message));
    }

    //  produces = "text/html;charset=utf-8"将返回的数据用utf-8编码
    @GetMapping(value = "/ai/generateStream", produces = "text/html;charset=utf-8")
    public Flux<String> generateStream(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return chatModel.stream(message);
        // 下面这种写法是等价的
        // Prompt prompt = new Prompt(new UserMessage(message));
        // return chatModel.stream(prompt).map(response -> response.getResult().getOutput().getText());
    }
}
```

### 以OpenAI API为例使用Ollama的chat模型

Ollama还兼容OpenAI接口，使用demo如下

- maven依赖

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
```

- 配置

```properties
# ollama本身没有apiKey，但是又不能为空，随便填个值上去就行了
# 对于其它模型供应商的apiKey，应该设置到vault或者环境变量中，然后通过占位符获取如${API_KEY}
# 这里springai有个问题，官方文档里面说spring.ai.openai.chat.api-key是会覆盖spring.ai.openai.api-key的
# 但是启动的时候，创建openAiSdkAudioSpeechModel bean时，还是会读spring.ai.openai.api-key，启动报错
#spring.ai.openai.chat.api-key=ollama
spring.ai.openai.api-key=ollama
# ollama的本地服务器地址，只要不是openai的，貌似都要加v1
spring.ai.openai.chat.base-url=http://localhost:11434/v1
# 使用的模型名称，ollma list命令列出的模型名称复制过来就行了
spring.ai.openai.chat.model=gemma4:e2b-it-qat
```

```java
@RestController
public class ApplicationController {
    @Autowired
    private OpenAiChatModel chatModel;

    private final ChatClient chatClient;

    @Autowired
    public ApplicationController(OpenAiChatModel chatModel) {
        // this.chatClient = ChatClient.create(chatModel);
        this.chatClient = ChatClient.builder(chatModel)
                                    .build();
    }

    @GetMapping("/chatModelCall")
    public Map<String, String> chatModelCall(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") 
        String message) {
        // Use the model with thinking-capable models
        ChatResponse response = chatModel.call(new Prompt(message));

        AssistantMessage assistantMessage = response.getResult().getOutput();

        // Access the reasoning process from metadata
        String reasoning = Optional.ofNullable(assistantMessage)
                                   .map(AbstractMessage::getMetadata)
                                   .map(map -> map.get("reasoningContent"))
                                   .map(reasoningContent -> {
                                       if (reasoningContent instanceof String s) {
                                           return s;
                                       }
                                       return null;
                                   })
                                   .orElse("");

        // Get the final answer
        String answer = Optional.ofNullable(assistantMessage).map(AssistantMessage::getText).orElse("");

        Map<String, String> map = new HashMap<>();
        map.put("Reasoning", reasoning);
        map.put("Answer", answer);
        map.put("Timestamp", String.valueOf(Instant.now().toEpochMilli()));
        return map;
    }

    @GetMapping(value = "/chatModelStream", produces = "text/html;charset=utf-8")
    public Flux<String> chatModelStream(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return chatModel.stream(message);
    }

    @GetMapping("/chatClientCall")
    public Map<String, String> chatClientCall(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") 
        String message) {
        // Use the model with thinking-capable models
        ChatResponse response = chatClient.prompt().user(message).call().chatResponse();

        AssistantMessage assistantMessage = response.getResult().getOutput();

        // Access the reasoning process from metadata
        String reasoning = Optional.ofNullable(assistantMessage)
                                   .map(AbstractMessage::getMetadata)
                                   .map(map -> map.get("reasoningContent"))
                                   .map(reasoningContent -> {
                                       if (reasoningContent instanceof String s) {
                                           return s;
                                       }
                                       return null;
                                   })
                                   .orElse("");

        // Get the final answer
        String answer = Optional.ofNullable(assistantMessage).map(AssistantMessage::getText).orElse("");

        Map<String, String> map = new HashMap<>();
        map.put("Reasoning", reasoning);
        map.put("Answer", answer);
        map.put("Timestamp", String.valueOf(Instant.now().toEpochMilli()));
        return map;
    }

    @GetMapping(value = "/chatClientStream", produces = "text/html;charset=utf-8")
    public Flux<String> chatClientStream(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return chatClient.prompt().user(message).stream().content();
    }
}
```

## chat模型和embedding模型

spring的官方文档只说了ollama的chat 模型是openai api兼容的

因此如果要使用ollama的embedding模型，目前最好还是使用ollama api吧

## 提示词

prompt > message > 字符串

- message是一个MessageType枚举，包含四种角色
    - SYSTEM，设定AI行为边界/角色/定位。指导AI的行为和响应方式，设置AI如何解释和回复输入的
    - USER，用户原始提问输入。代表用户向AI提出的问题、命令或陈述
    - ASSISTANT，AI返回的响应信息。用它确保上下文能够连贯交互（记忆对话，积累回答）
    - TOOL，桥接外部服务，可以进行方法调用，如支付/数据查询等，类似调用第三方工具栏

```java
@GetMapping(value = "/chatClientStream")
public Flux<String> chatClientStream(
        @RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {

    // 使用system和user
    return chatClient.prompt()
                         .system("你是一个说书大师")
                         .user(message)
                         .stream()
                         .content();
    // 使用字符串
    return chatClient.prompt()
                         .user(message)
                         .stream()
                         .content();        
    // 使用message
    UserMessage userMessage = new UserMessage(message);
    return chatClient.prompt()
                        .messages(userMessage)
                        .stream()
                        .content();
    // 使用prompt
    SystemMessage systemMessage = new SystemMessage("你是一个说书大师");
    UserMessage userMessage = new UserMessage(message);
    Prompt prompt = new Prompt(systemMessage, userMessage);
    // promt也可以这么创建
    // Prompt prompt = new Prompt(List.of(systemMessage, userMessage));

    return chatClient.prompt(prompt)
                         .stream()
                         .content();
}
```

### 提示词模板

```java
String stringTemplate = """
            讲一个关于{topic}的故事，
            并以{outputFormat}格式输出，
            数字在{wordCount}左右                                                           
        """;
PromptTemplate promptTemplate = new PromptTemplate(stringTemplate);
Prompt prompt = promptTemplate.create(Map.of("topic", message, "outputFormat", "html", "wordCount", 300));
return chatClient.prompt(prompt)
        .stream()
        .content();
```

- 从资源文件加载提示词模板

```java
@Value("classpath:/prompts/message.st")
private Resource stringTemplate;

PromptTemplate promptTemplate = new PromptTemplate(stringTemplate);
```

## 结构化输出

```java
public record PersonRecord(String name, Integer age) {
}

@GetMapping("/chatClientStructuredOutput")
public PersonRecord chatClientStructuredOutput(@RequestParam(value = "name") String name,
        @RequestParam(value = "age") Integer age) {
    String stringTemplate = """
            我叫：{name}，今年{age}岁！
            """;
    // 写法1，param
    return chatClient.prompt()
        .user(promptUserSpec -> promptUserSpec.text(stringTemplate)
                                            .param("name", name)
                                            .param("age", age))
        .call()
        .entity(PersonRecord.class);
    // 写法2，params
    return chatClient.prompt()
        .user(promptUserSpec -> promptUserSpec.text(stringTemplate)
                                            .params(Map.of("name", name, "age", age)))
        .call()
        .entity(PersonRecord.class);
}
```

## 聊天记忆（Chat Memory）

```java
@GetMapping("/chatMemory")
public String interactChatModel() {
    // Create a memory instance
    ChatMemory chatMemory = MessageWindowChatMemory.builder()
                                                    .build();
    String conversationId = "007";

    // First interaction
    UserMessage userMessage1 = new UserMessage("My name is James Bond");
    chatMemory.add(conversationId, userMessage1);
    ChatResponse chatResponse1 = chatModel.call(new Prompt(chatMemory.get(conversationId)));
    chatMemory.add(conversationId, chatResponse1.getResult()
                                                .getOutput());

    // Second interaction
    UserMessage userMessage2 = new UserMessage("What is my name?");
    chatMemory.add(conversationId, userMessage2);
    ChatResponse chatResponse2 = chatModel.call(new Prompt(chatMemory.get(conversationId)));
    AssistantMessage assistantMessage = chatResponse2.getResult()
                                                        .getOutput();
    chatMemory.add(conversationId, assistantMessage);

    // The response will contain "James Bond"
    return assistantMessage.getText();
}
```

### MessageWindowChatMemory

MessageWindowChatMemory维护一个滑动窗口的消息到指定的最大大小

当消息的数量超过最大值时，旧的消息将被清除，同时始终保留SystemMessage实例

默认窗口大小为20条消息

### MessageChatMemoryAdvisor

该顾问使用提供的ChatMemory实现管理会话内存

在每次交互时，它从内存中检索会话历史记录，并将其作为消息集合包含在提示词中

- 依赖

```sh
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-chat-memory-repository-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

- 配置

```properties
# 解决调用大模型返回中文乱码问题
# 保证大模型返回响应的时候不会中文乱码
# 推荐配置文件的方式进行设置，否则就要在控制器类声明
# @RequestMapping(produces = "text/html;charset=utf-8") public class MyController {}
# 或声明
# @GetMapping(value = "/myRequest", produces = "text/html;charset=utf-8") public String myMethodName(){ return xxx}

# 启用字符编码过滤器
# 只有设置为 true，Spring Boot 才会注册并激活 CharacterEncodingFilter
spring.servlet.encoding.enabled=true
# 强制请求和响应都使用指定的字符集
spring.servlet.encoding.force=true
# 告诉 Spring Boot 内部的 CharacterEncodingFilter 过滤器
# 在处理 HTTP 请求参数（如表单提交、URL 参数）和生成 HTTP 响应内容时，默认使用 UTF-8 进行解码和编码
spring.servlet.encoding.charset=UTF-8

# llama.cpp或ollama的apiKey随便填一个就可以了
# 对于其它模型供应商的apiKey，应该设置到vault或者环境变量中，然后通过占位符获取如${API_KEY}
# 这里springai有个问题，官方文档里面说spring.ai.openai.chat.api-key是会覆盖spring.ai.openai.api-key的
# 但是启动的时候，创建openAiSdkAudioSpeechModel bean时，还是会读spring.ai.openai.api-key，启动报错
spring.ai.openai.api-key=llama.cpp
# llama.cpp的本地服务器地址，要改成模型启动时的具体ip和端口，并且加/v1
spring.ai.openai.chat.base-url=http://localhost:8888/v1
# llama.cpp启动的模型的模型名就是文件名，可以去掉后缀
spring.ai.openai.chat.model=Ornith-1.5-9B-Q4_K_M

logging.level.org.springframework.ai.chat.client.advisor=DEBUG

# 根据具体情况设置
spring.ai.chat.memory.repository.redis.host=
spring.ai.chat.memory.repository.redis.port=
spring.ai.chat.memory.repository.redis.username=
spring.ai.chat.memory.repository.redis.password=
# Name of the Redis search index
spring.ai.chat.memory.repository.redis.index-name=chat-memory-idx
# Key prefix for chat memory entries
spring.ai.chat.memory.repository.redis.key-prefix=chat-memory:
# 默认不过期，例：24h, 30d
#spring.ai.chat.memory.repository.redis.time-to-live=24h
# Whether to initialize the Redis schema on startup
spring.ai.chat.memory.repository.redis.initialize-schema=true
# Maximum number of conversation IDs to return
spring.ai.chat.memory.repository.redis.max-conversation-ids=1000
# Maximum number of messages to return per conversation
spring.ai.chat.memory.repository.redis.max-messages-per-conversation=1000
```

- 控制器

```java
@Autowired
private RedisChatMemoryRepository chatMemoryRepository;

private final ChatClient chatClient;

public ApplicationController(RedisChatMemoryRepository chatMemoryRepository, ChatModel chatModel) {
    ChatMemory chatMemory = MessageWindowChatMemory.builder()
                                                    .chatMemoryRepository(chatMemoryRepository)
                                                    .maxMessages(10)
                                                    .build();

    this.chatClient = ChatClient.builder(chatModel)
                                .defaultAdvisors(MessageChatMemoryAdvisor.builder(chatMemory)
                                                                            .build())
                                .build();
}

@GetMapping("/redisChatMemory")
public Flux<String> redisChatMemory(
        @RequestParam(name = "conversationId", defaultValue = "1001") String conversationId,
        @RequestParam(name = "message") String message) {
    return chatClient.prompt()
                        .user(message)
                        // 设置会话id
                        .advisors(advisorSpec -> advisorSpec.param(ChatMemory.CONVERSATION_ID, conversationId))
                        .stream()
                        .content();
}
```

### 查询历史记录

```java
// Cast to access advanced features
AdvancedRedisChatMemoryRepository advancedRedisChatMemoryRepository =
        (AdvancedRedisChatMemoryRepository) chatMemoryRepository;

// Find messages by type across all conversations
List<AdvancedRedisChatMemoryRepository.MessageWithConversation> results =
        advancedRedisChatMemoryRepository.findByContent("Spring AI", 10);

// 根据会话id查询
List<Message> results = advancedRedisChatMemoryRepository.findByConversationId(conversationId);
```

## 工具调用（Tool Calling）

LLM本身不能调用工具，它会在响应中表达调用特定工具的意图，然后，应用程序执行这个工具，并将结果返回给模型

工具调用分为两种：信息检索和采取行动

信息检索：如获取当前日期时间和天气，然后模型润色后返回

采取行动：执行模型生成的计划，如发送邮件

有两种工具声明的方式，注解方式和编程方式（一步一步创建必要对象）

下面只介绍注解的方式

### `@Tool`注解

- 属性
    - name：工具名称，如果不指定，会使用方法名
    - description：工具描述，让模型了解该工具是干嘛的和如何使用该工具
        - 如果不指定，会使用方法名，设置详细的描述是非常有必要并且至关重要
    - returnDirect：工具调用结果是直接返回给客户端还是传给模型
    - resultConverter：ToolCallResultConverter实现，用来转换工具调用结果为String类型的对象返回给模型

- 注解的方法可以是静态方法或实例方法

- 注解的方法的可以是任何可见性（public, protected, package-private, or private）

- 注解的方法可以有0个或任意参数，参数类型可以是绝大多数类型

- 注解的方法可以没有返回类型（void）或绝大多数返回类型
    - 如果有返回类型，则该返回类型必须是可序列化的，因为返回值会被序列化传给模型

- 注解的方法的所属类可以是顶级类或内部类，这个类可以是任何可见性，只要实例化的地方是可访问的就行

- 注解的方法的所属类只要它是一个Spring bean，它就支持AOT 编译，不用额外为GraalVM编译器做配置
    - 否则，你需要在这个类加上注解`@RegisterReflection(memberCategories = MemberCategory.INVOKE_DECLARED_METHODS)`
    - 可以根据需要决定是否将其定义为Spring bean

- 注解的方法的参数类型和返回值类型限制，不可以是如下类型
    - Optional
    - 异步类型 (e.g. CompletableFuture, Future)
    - 响应式类型 (e.g. Flow, Mono, Flux)
    - 函数式类型(e.g. Function, Supplier, Consumer)
        - 其实通过使用基于函数式的工具规范来实现，是可以支持函数式类型的

```java
@Component
publci class DateTimeTools {
    @Tool(description = "Get the current date and time in the user's timezone")
    public String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }
}
```

### `@ToolParam`注解

`@Tool`注解的方法的参数还可以添加`@ToolParam`注解

用于提供额外的参数信息，如参数描述，是可选还是必须，默认是必须的

- 属性
    - description：参数描述，可以让模型更好地了解如何去使用
    - required：参数是可选还是必须，默认是必须的

- 参数如果注解了`@Nullable`，它将被认为是可选的，除非用`@ToolParam`标识为必须了

```java
class DateTimeTools {
    @Tool(description = "Set a user alarm for the given time")
    void setAlarm(@ToolParam(description = "Time in ISO-8601 format") String time) {
        LocalDateTime alarmTime = LocalDateTime.parse(time, DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("Alarm set for " + alarmTime);
    }
}
```

### 将工具添加到ChatClient

如果使用的是`@Tool`注解方式声明的工具

当调用ChatClient时，给tools()方法传一个该工具类的实例就可以了

```java
ChatClient.create(chatModel)
    .prompt("What day is tomorrow?")
    .tools(new DateTimeTools())
    .call()
    .content();
```

#### ToolCallback

在底层，ChatClient会为工具类实例的每一个`@Tool`注解的方法生成一个ToolCallback并传给模型

当然你也可以使用ToolCallbacks工具类手动完成这一步

```java
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());

ChatClient.create(chatModel)
    .prompt("What day is tomorrow?")
    .tools(dateTimeTools)
    .call()
    .content();
```

除此之外tools()方法还接收ToolCallbackProvider实例

#### 将默认工具添加到ChatClient

defaultTools()指定的默认工具，对于同一个ChatClient.Builder创建的ChatClient是通用的，因此使用要特别小心

当同时用defaultTools()和tools()指定时，后者会覆盖前者

```java
ChatModel chatModel = ...
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(new DateTimeTools())
    .build();
```

### 将工具添加到ChatModel

```java
ChatModel chatModel = ...
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(dateTimeTools)
    .build();
Prompt prompt = new Prompt("What day is tomorrow?", chatOptions);
chatModel.call(prompt);
```

#### 将默认工具添加到ChatModel

```java
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
ChatModel chatModel = OllamaChatModel.builder()
    .ollamaApi(OllamaApi.builder().build())
    .options(ToolCallingChatOptions.builder()
            .toolCallbacks(dateTimeTools)
            .build())
    .build();
```

## 函数式作为工具来调用

- 限制，以下类型不支持
    - 基本数据类型
    - Optional
    - 集合类型(e.g. List, Map, Array, Set)
    - 异步类型(e.g. CompletableFuture, Future)
    - 响应式类型 (e.g. Flow, Mono, Flux)

- WeatherService

```java
public class WeatherService implements Function<WeatherRequest, WeatherResponse> {
    public WeatherResponse apply(WeatherRequest request) {
        return new WeatherResponse(30.0, Unit.C);
    }
}

public enum Unit { C, F }
public record WeatherRequest(String location, Unit unit) {}
public record WeatherResponse(double temp, Unit unit) {}
```

- 定义ToolCallback bean

```java
@Configuration(proxyBeanMethods = false)
class WeatherTools {
    WeatherService weatherService = new WeatherService();

    @Bean
    ToolCallback currentWeather() {
        return FunctionToolCallback.builder("currentWeather", weatherService::getWeather)
        .description("Get the weather in location")
        .inputType(WeatherRequest.class)
        .build();
    }
}
```

- 使用ToolCallback bean

```java
@Autowired
ToolCallback currentWeather;

// Pass it to ChatClient at request time
ChatClient.create(chatModel)
    .prompt("What's the weather like in Copenhagen?")
    .tools(currentWeather)
    .call()
    .content();

// Or register as a default tool for all requests via the builder
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(currentWeather)
    .build();
```

## Tool Callback

ToolCallback接口提供一种方式，来定义一个可以被ai模型调用的工具，它包括了定义和执行的逻辑

你可以从 MCP Client (using the Model Context Protocol)或ChatClient (to build a modular agentic application)定义一个ToolCallback

Spring AI提供了两个内置实现：MethodToolCallback和FunctionToolCallback

ToolCallback提供的方法如下：

```java
public interface ToolCallback {
    /**
     * Definition used by the AI model to determine when and how to call the tool.
     */
    ToolDefinition getToolDefinition();

    /**
     * Metadata providing additional information on how to handle the tool.
     */
    ToolMetadata getToolMetadata();

    /**
     * Execute tool with the given input and return the result to send back to the AI model.
     */
    String call(String toolInput);

    /**
     * Execute tool with the given input and context, and return the result to send back to the AI model.
     */
    String call(String toolInput, ToolContext tooContext);
}
```

## Tool Definition

ToolDefinition接口提供必要的信息以便于ai模型去了解工具的能力，这些必要信息包括工具名称，描述，入参结构

每个ToolCallback实现类必须提供一个ToolDefinition实例来定义工具

ToolDefinition包含以下方法

```java
public interface ToolDefinition {
    /**
     * The tool name. Unique within the tool set provided to a model.
     */
    String name();

    /**
     * The tool description, used by the AI model to determine what the tool does.
     */
    String description();

    /**
     * The schema of the parameters used to call the tool.
     */
    String inputSchema();
}
```

ToolDefinition.Builder可以让你使用默认的实现DefaultToolDefinition构建一个ToolDefinition

```java
ToolDefinition toolDefinition = ToolDefinition.builder()
    .name("currentWeather")
    .description("Get the weather in location")
    .inputSchema("""
        {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string"
                },
                "unit": {
                    "type": "string",
                    "enum": ["C", "F"]
                }
            },
            "required": ["location", "unit"]
        }
    """)
    .build();
```

### Method Tool Definition

从方法构建工具时，会自动生成ToolDefinition

当然也可以手动完成，代码如下：

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolDefinition toolDefinition = ToolDefinitions.from(method);
```

从方法生成的ToolDefinition包含了方法名和工具名

方法名作为工具描述，json结构作为方法入参

如果方法有`@Tool`注解，则工具名和工具描述会从注解里面提取（如果设置了）

如果你想要显式声明一部分或全部属性，可以使用ToolDefinition.Builder构建一个自定义的ToolDefinition实例

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolDefinition toolDefinition = ToolDefinitions.builder(method)
    .name("currentDateTime")
    .description("Get the current date and time in the user's timezone")
    .inputSchema(JsonSchemaGenerator.generateForMethodInput(method))
    .build();
```

### Function Tool Definition

由于编程式的写法没有记笔记，可以参考spring ai的 Functions as Tools

### Result Conversion

- ToolCallResultConverter接口

```java
@FunctionalInterface
public interface ToolCallResultConverter {
    /**
     * Given an Object returned by a tool, convert it to a String compatible with the
     * given class type.
     */
    String convert(@Nullable Object result, @Nullable Type returnType);
}
```

- 使用自定义ToolCallResultConverter

```java
class CustomerTools {
    @Tool(description = "Retrieve customer information", resultConverter = CustomToolCallResultConverter.class)
    Customer getCustomerInfo(Long id) {
        return customerRepository.findById(id);
    }
}
```

### Tool Context

toolContext选项如果同时出现在默认选项和运行时选项中，则会将其合并，并且运行时选项优先级更高

- 在工具方法中声明ToolContext参数

```java
class CustomerTools {
    @Tool(description = "Retrieve customer information")
    Customer getCustomerInfo(Long id, ToolContext toolContext) {
        return customerRepository.findById(id, toolContext.getContext().get("tenantId"));
    }
}
```

- 在toolContext()方法里面设置ToolContext

```java
// 使用ChatModel
ChatModel chatModel = ...

String response = ChatClient.create(chatModel)
        .prompt("Tell me more about the customer with ID 42")
        .tools(new CustomerTools())
        .toolContext(Map.of("tenantId", "acme"))
        .call()
        .content();

// 使用ChatModel
ChatModel chatModel = ...
ToolCallback[] customerTools = ToolCallbacks.from(new CustomerTools());
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(customerTools)
    .toolContext(Map.of("tenantId", "acme"))
    .build();
Prompt prompt = new Prompt("Tell me more about the customer with ID 42", chatOptions);
chatModel.call(prompt);
```

### Return Direct

returnDirect设置为true，工具调用后直接返回，而不是返回给模型

```java
class CustomerTools {
    @Tool(description = "Retrieve customer information", returnDirect = true)
    Customer getCustomerInfo(Long id) {
        return customerRepository.findById(id);
    }
}
```

### Tool Execution

工具执行是使用提供的入参调用工具并返回结果的一个流程

ToolCallingManager负责工具执行的生命周期

```java
public interface ToolCallingManager {
    /**
     * Resolve the tool definitions from the model's tool calling options.
     */
    List<ToolDefinition> resolveToolDefinitions(ToolCallingChatOptions chatOptions);

    /**
     * Execute the tool calls requested by the model.
     */
    ToolExecutionResult executeToolCalls(Prompt prompt, ChatResponse chatResponse);

}
```

如果使用了任意的Spring AI Spring Boot Starters，DefaultToolCallingManager是自动配置好的ToolCallingManager实现

当然你可以自己定制ToolCallingManager bean

```java
@Bean
ToolCallingManager toolCallingManager() {
    return ToolCallingManager.builder().build();
}
```

- Spring AI支持3种管理工具执行生命周期的方式
    - 推荐的方式是通过ChatClient管理的Framework-Controlled Tool Execution
    - 需要自定义工具调用循环的时候，用Advisor-Controlled Tool Execution
    - 需要全程手动管理的时候，用User-Controlled Tool Execution

### Exception Handling

当工具调用失败时，可以捕获ToolExecutionException来处理错误

- ToolExecutionExceptionProcessor可以用来处理ToolExecutionException，有两种处理方式
    - 生成错误信息返回给AI模型
    - 抛出一次给调用者处理

```java
@FunctionalInterface
public interface ToolExecutionExceptionProcessor {
    /**
     * Convert an exception thrown by a tool to a String that can be sent back to the AI
     * model or throw an exception to be handled by the caller.
     */
    String process(ToolExecutionException exception);
}
```

如果你使用任何的Spring AI Spring Boot Starters, DefaultToolExecutionExceptionProcessor是自动配置好的默认的ToolExecutionExceptionProcessor实现

- 有两种方式设置DefaultToolExecutionExceptionProcessor的错误处理方式

```properties
spring.ai.tools.throw-exception-on-error=false
```

```java
@Bean
ToolExecutionExceptionProcessor toolExecutionExceptionProcessor() {
    return new DefaultToolExecutionExceptionProcessor(true);
}
```

如果定义了自己的ToolCallback实现，取保错误发生时抛出ToolExecutionException

## 文本向量化

Embedding（嵌入）的工作原理是将文本、图像和视频转换为称为向量的浮点数数组

嵌入数组的长度称为向量的维度

Embedding Model（嵌入模型）是嵌入过程中采用的模型

嵌入模型是一种机器学习模型，旨在在连续的低维向量空间中表示数据（例如文本、图像或其它形式的信息）

这些嵌入可以捕获数据之间的语义或上下文相似性，使机器能够更有效地执行比较、聚类或分类等任务

先将用户的查询发送到向量数据库，得到相似文档，然后将这些文档作为用户问题的上下文，与用户的查询一起发送到AI模型

这种技术称为检索增强生成

### 嵌入模型

使用嵌入模型完成文本向量化例子

- 依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>4.1.2</version>
</parent>

<artifactId>spring-ai-openai-embedding-demo</artifactId>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>2.0.2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-model-openai</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- 配置

```yaml
spring:
    servlet:
        encoding:
            enabled: true
            force: true
            charset: UTF-8
    ai:
        openai:
            api-key: yourApiKey
            base-url: http://yourIp:yourPort/v1
            embedding:
                model: yourEmbeddingModel
```

- 控制器

```java
@Autowired
private EmbeddingModel embeddingModel;

@GetMapping("/test")
public Map<String, EmbeddingResponse> test(
        @RequestParam(value = "message", defaultValue = "玲珑骰子安红豆") String message) {
    // 文本向量化的两种写法
    // EmbeddingResponse embeddingResponse = embeddingModel.call(new EmbeddingRequest(List.of(message), null));
    // EmbeddingResponse embeddingResponse = embeddingModel.call(new EmbeddingRequest(List.of(message),
    //                                                                                EmbeddingOptions.builder()
    //                                                                                                .model("embeddinggemma-2-Q8_0")
    //                                                                                                .build()));
    EmbeddingResponse embeddingResponse = embeddingModel.embedForResponse(List.of(message));
    // 向量数组
    float[] outputs = embeddingResponse.getResult()
                                        .getOutput();
    System.out.println(Arrays.toString(outputs));
    return Map.of("embeddingResponse", embeddingResponse);
}
```

### 向量数据库

使用向量数据库例子

- 依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>4.1.2</version>
</parent>

<artifactId>spring-ai-redis-vector-store</artifactId>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>2.0.2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-vector-store-redis</artifactId>
        <exclusions>
            <exclusion>
                <groupId>io.lettuce</groupId>
                <artifactId>lettuce-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-model-openai</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- 配置

```yaml
spring:
    servlet:
        encoding:
            enabled: true
            force: true
            charset: UTF-8
    ai:
        openai:
            api-key: yourApiKey
            base-url: http://yourIp:yourPort/v1
            embedding:
                model: yourEmbeddingModel
        vectorstore:
            redis:
                # 是否初始化必要的schema
                initialize-schema: true
                # index-name和prefix要一起改，否则会检索不到，修改例子：
                # index-name: 自定义名称-index
                # prefix: "自定义名称:"
                index-name: vector-index
                prefix: "vector:"
    data:
        redis:
            host: yourIp
            port: yourPort
            username: yourUserName
            password: yourPassword
```

- 控制器

```java
@Autowired
private VectorStore vectorStore;

@GetMapping("/test")
public String test() {
    List<Document>
            documents = List.of(
                    new Document("Spring AI rocks!! Spring AI rocks!! Spring AI rocks!! Spring AI rocks!! Spring AI rocks!!",
                        Map.of("meta1", "meta1")),
                    new Document("The World is Big and Salvation Lurks Around the Corner"),
                    new Document("You walk forward facing the past and you turn back toward the future.",
                        Map.of("meta2", "meta2")));

    // 隐式调用了嵌入模型
    // Add the documents to Redis
    vectorStore.add(documents);

    // 相似度查找
    // Retrieve documents similar to a query
    List<Document> results = this.vectorStore.similaritySearch(SearchRequest.builder().query("Spring").topK(5).build());
    return "result size: " +  results.size() + " timestamp: " + System.currentTimeMillis();
}
```

### 检索增强生成（Retrieval Augmented Generation）

RAG是一种从你的数据中（特定知识库）查找相关信息，并在将提示词发送给LLM之前，将其注入到提示词中的方法

这样一来，LLM就能获得相关的信息，并基于这些信息进行回答，从而降低幻觉的概率

由于LLM的知识仅限于它所接受的训练数据

如果想让一个LLM了解特定领域的知识或专有数据，可以：使用RAG、用你的数据微调LLM，RAG和微调混合使用

RAG分为先后两个阶段：索引和检索

索引：知识库文档->文本分割->分割后得到的分段->文本向量化->向量数据库

检索：用户查询-> 查询内容文本向量化-> 查询向量数据库->用户查询+数据库检索出来的分段->LLM

工具调用和RAG都是增强LLM的手段

- 依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>4.1.2</version>
</parent>

<artifactId>spring-ai-rag-demo</artifactId>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>2.0.2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-rag</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-vector-store-redis</artifactId>
        <exclusions>
            <exclusion>
                <groupId>io.lettuce</groupId>
                <artifactId>lettuce-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.ai</groupId>
        <artifactId>spring-ai-starter-model-openai</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

- 配置

```yaml
spring:
    servlet:
        encoding:
            enabled: true
            force: true
            charset: UTF-8
    ai:
        openai:
            api-key: llama.cpp
            base-url: http://yourIp:yourPort/v1
            chat:
                model: Ornith-1.5-9B-Q4_K_M
                base-url: http://yourIp:yourPort/v1
            embedding:
                model: embeddinggemma-2-Q8_0
                base-url: http://yourIp:yourPort/v1
        vectorstore:
            redis:
                # 是否初始化必要的schema
                initialize-schema: true
                # index-name和prefix要一起改，否则会检索不到，修改例子：
                # index-name: 自定义名称-index
                # prefix: "自定义名称:"
                index-name: vector-index
                prefix: "vector:"
    data:
        redis:
            host: yourIp
            port: yourPort
            username: yourUserName
            password: yourPassword
```

- resources下的知识库文件StatusCode.txt

```txt
00000 系统正常返回
A0001 客户端请求错误
A0002 客户端跳转错误
B0001 服务器内部错误
B0002 服务器响应错误
```

- 配置类

```java
@Configuration
public class InitVectorStoreConfiguration {
    @Autowired
    private VectorStore vectorStore;

    @Value("classpath:StatusCode.txt")
    private Resource statusCodeFile;

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @PostConstruct
    public void init() throws NoSuchAlgorithmException {
        TextReader textReader = new TextReader(statusCodeFile);
        textReader.setCharset(StandardCharsets.UTF_8);

        List<Document> documents = textReader.get();
        List<Document> splitDocuments = TokenTextSplitter.builder()
                                                         .build()
                                                         .apply(documents);
        String metadata = (String) textReader.getCustomMetadata()
                                             .get("source");
        MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
        messageDigest.update(metadata.getBytes(StandardCharsets.UTF_8));
        byte[] hash = messageDigest.digest();
        String redisKey = "statusCode:" + Base64.getUrlEncoder()
                                                .encodeToString(hash);
        Boolean isAbsent = stringRedisTemplate.opsForValue()
                                              .setIfAbsent(redisKey, "1");
        if (Boolean.TRUE.equals(isAbsent)) {
            vectorStore.add(splitDocuments);
        }
    }
}
```

- 控制器

```java
@Autowired
private VectorStore vectorStore;

private final ChatClient chatClient;

public MainController(ChatModel chatModel) {
    this.chatClient = ChatClient.builder(chatModel)
                                .build();
}

@GetMapping("/test")
public Flux<String> test(@RequestParam(value = "message", defaultValue = "00000") String message) {
    VectorStoreDocumentRetriever retriever = VectorStoreDocumentRetriever.builder()
                                                                            .vectorStore(vectorStore)
                                                                            .build();
    RetrievalAugmentationAdvisor advisor = RetrievalAugmentationAdvisor.builder()
                                                                        .documentRetriever(retriever)
                                                                        .build();
    String systemPrompt = "你是一个运维工程师，按照我给出的状态码给出对应的解释，否则回复找不到信息。";
    return chatClient.prompt()
                        .system(systemPrompt)
                        .user(message)
                        .advisors(advisor)
                        .stream()
                        .content();
}
```
