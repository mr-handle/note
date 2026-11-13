# Spring MVC

## 组件

- 1.DispatcherServlet，所有请求都经过其处理分发，通过web.xml配置使其生效

- 2.HandlerMapping，内部缓存handler（controller方法）和handler访问路径数据，被DispatcherServlet调用，用于查找路径对应的handler，需要加入到IOC容器

- 3.HandlerAdapter，处理转换请求参数和响应数据，DispatcherServlet通过HandlerAdapter间接调用handler，HandlerAdapter是DispatcherServlet和handler之间的适配器，需要加入到IOC容器

- handler，是Controller的方法，用来接收请求参数，调用业务，最终返回响应

- ViewResolver，通过配置前缀和后缀，简化模板视图页面查找，对于前后端分离项目，后端只返回json数据，不返回页面，因此它不是必须的

## 初始化

### 核心配置类

```java
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration {
    @Bean
    public RequestMappingHandlerMapping handlerMapping() {
        return new RequestMappingHandlerMapping();
    }
    
    @Bean
    public RequestMappingHandlerAdapter handlerAdapter() {
        return new RequestMappingHandlerAdapter();
    }
}
```

### @EnableWebMvc

- 等同于xml配置中的<mvc:annotation-driven>

- 相当于添加了HandlerMapping、HandlerAdapter和json转换器

- 最终走MvcNamespaceHandler中的AnnotationDrivenBeanDefinitionParser的parse方法
    - 自动添加HandlerMapping
    - 自动添加HandlerAdapter
        - 在addRequestBodyAdvice方法中添加json处理器JsonViewRequestBodyAdvice
        - 在addResponseBodyAdvice方法中添加json处理器JsonViewResponseBodyAdvice

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration {}
```

### WebMvcConfigurer

Spring MVC标准化配置类

- 除了handlerMapping、handlerAdapter、json转换器可以用@EnableWebMvc注解添加外，如果想要设置其它的mvc组件（如视图解析器），不用一个个写@Bean来添加，可以实现WebMvcConfigurer，里面提供了很多组件的默认实现方法，根据需要覆写相应方法即可添加

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration implements WebMvcConfigurer{
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/views/", ".jsp");
    }
}
```

### 初始化类

- 初始化两个容器（所以至少两个配置类）
    - root容器是父容器，放service、mapper、mybatis等组件
    - web容器是子容器，放controller、web相关组件，web容器在FrameworkServlet中的createWebApplicationContext方法创建，通过setParent方法维护容器的父子关系
- 每当web项目启动，就会自动调用WebApplicationInitializer接口的onStartup方法，因此可以在这个方法里面定义一些初始化工作，如初始化IOC容器、DispatcherServlet

- AbstractAnnotationConfigDispatcherServletInitializer间接实现了WebApplicationInitializer

- 因此可以定义一个初始化类，继承AbstractAnnotationConfigDispatcherServletInitializer，做一些初始化工作

```java
public class SpringMvcInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    // 指定Root IOC容器配置类，设置service层、mapper层的IOC容器的配置
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }

    // 指定Web IOC容器配置类，设置springmvc相关的如HandlerMapping、HandlerAdapter、Controller层的IOC容器的配置
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] {SpringMvcConfiguration.class};
    }
    
    // 配置SpringMVC内部自带servlet的访问地址，处理所有请求
    @Override
    protected String[] getServletMappings() {
        return new String[] {"/"};
    }
}
```

## 路径设置

### @RequestMapping

- 将路径注册到HandlerMapping，最终建立请求url和处理请求的方法之间的对应关系

- method，请求方式，常用RequMethod.GET/RequMethod.POST，请求方式不对报405异常

```java
// 一级请求路径
@RequestMapping("/application")
public class ApplicationController { 
    // 二级请求路径
    @RequestMapping("/hello")
    public String hello() {
        return "hello world";
    }
}
```

### @GetMapping

只能用在方法上，同@RequestMapping("/application", method=RequMethod.GET)

### @PostMapping

只能用在方法上，同@RequestMapping("/application", method=RequMethod.POST)

### @CrossOrigin

同源策略：比较访问方和被访问方的域名（协议、IP、端口）是否一样，不一样的话浏览器会阻止访问，除非被访问方设置了允许跨域访问

```java
// 允许其它源访问这个controller，浏览器不拦截，也可加在handler上
@CrossOrigin
@RequestMapping("/application")
@RestController
public class ApplicationController { 
}
```

## 参数接收

### 直接接收

- 形参列表参数名设置为和请求参数名一样

- 使用实体对象接收时，定义一个和请求参数名称对应属性的实体类，并且定义get/set方法即可

- 一个请求参数名包含多个值时，必须用集合接收，并且加注解@RequestParam

- 前端不传值也不会报错

```java
@GetMapping("/getAccount")
@ResponseBody
// url：localhost:8080/application/getAccount?name=handle&age=18
public String getAccount(String name, int age) {
    return "name = " + name + ", age = " + age;
}
```

### @RequestParam

获取请求参数，把请求中指定名称的参数的值赋给控制器方法中的形参

- name/value，设置为和请求参数名一样，如果形参名和请求参数名一样，可以不写此属性

- required，前端是否必须传递此参数，为true时不传值报400异常

- defaultValue，可指定默认值

- 一个请求参数名包含多个值时，用集合接收即可

```java
 @GetMapping("/user/getRequestParams")
 public Map<String, Object> getRequestParams(
    @RequestParam("age") Integer age,
    @RequestParam("interest") List<String> interests,
    @RequestParam Map<String, String> allRequestParams) {
        Map<String, Object> map = new HashMap<>();
        map.put("age", age);
        map.put("interest", interests);
        map.put("allRequestParams", allRequestParams);
        return map;
    }
```

请求url：`localhost:8080/application/getRequestParams?age=18&interest=basketball&interest=movie`

方法返回：`{"interest": ["basketball","movie"],"allRequestParams": {"age": "18", "interest": "basketball"},"age": 18}`

### @PathVariable

获取路径变量

```java
 @GetMapping("/user/{id}/{name}/getPathVariables")
 public Map<String, Object> getPathVariables(
    @PathVariable("id") Integer id,
    @PathVariable("name") String name,
    @PathVariable Map<String, String> allPathVariables) {
        Map<String, Object> map = new HashMap<>();
        map.put("id", id);
        map.put("name", name);
        map.put("allPathVariables", allPathVariables);
        return map;
    }
```

### @RequestBody

获取请求体

系统会使用HttpMessageConverter或者自定义的HttpMessageConverter将请求的body中的json字符串转换为java对象

- 由于java原生api只支持路径参数和param参数（request.getParameter("key")），不支持json，所以用@RequestBody接收json数据时，需要导入json处理的依赖，并且在HandlerAdapter配置json转换器

```java
// json数据处理，必须用此注解，它会加入json处理器
// 相当于它给HandlerAdapter配置了json转换器
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration {}

@PostMapping("/user/getRequestBody")
public Map<String, Object> getRequestBody(@RequestBody String content) {
     Map<String, Object> map = new HashMap<>();
     map.put("content", content);
     return map;
}
```

### @RequestHeader

获取请求头

```java
@PostMapping("/user/getHeaders")
public Map<String, Object> getHeaders(
    @RequestHeader("Host") String host,
    @RequestHeader Map<String, String> allHeaders) {
     Map<String, Object> map = new HashMap<>();
     map.put("host", host);
     map.put("allHeaders", allHeaders);
     return map;
}
```

### @CookieValue

获取 cookie 值

```java
// 1.设置cookie
@PostMapping("/setCookies")
public String setCookies(HttpServletResponse response) {
    Cookie cookie = new Cookie("userName", "handle");
    // set to -1 will be treated as a session cookie by the browser
    // expires in 7 days
    cookie.setMaxAge(7 * 24 * 60 * 60); 
    response.addCookie(cookie);
    // 加密传输到server，https连接有效
    cookie.setSecure(true);
    // it is not accessible to the client scripts
    // For example, you can not use the Document.cookie property to access HttpOnly cookies in JavaScript
    // This is one way to secure a cookie from being changed by malicious code or cross-site scripting (XSS) attacks
    cookie.setHttpOnly(true);
    // The Path attribute specifies a URL path for which the cookie should be sent to the server. 
    // By explicitly setting the Path directive, the cookie will be delivered to the specified URL and all of its subdirectories
    // By default, if no path is specified, 
    // a cookie is only sent to the server for the URL that was used to set it in the browser.
    // global cookie accessible every where for the current domain
    cookie.setPath("/"); 
    return "set cookie success";
}

// 2.获取cookie
@PostMapping("/getCookies")
public Map<String, Object> getCookies(
    @CookieValue(value = "id", defaultValue = "426353") id,
    @CookieValue Cookie cookie,
    HttpServletRequest request) {
    Cookie[] cookies = request.getCookies();
    if (cookies != null) {
        return Arrays.stream(cookies)
                .map(c -> c.getName() + "=" + c.getValue()).collect(Collectors.joining(", "));
    }

    Map<String, Object> map = new HashMap<>();
    map.put("id", id);
    map.put("cookie", cookie);
    map.put("cookies", cookies);
    return map;
}

// 3.删除cookie
@PostMapping("/deleteCookies")
public String deleteCookies(HttpServletResponse response) {
    // To delete a cookie, you need to create a new instance of the Cookie class with the same name 
    // and the Max-Age directive to 0
    // and add it again to the response
    Cookie cookie = new Cookie("userName", null);
    cookie.setMaxAge(0); 
    response.addCookie(cookie);
    cookie.setSecure(true);
    cookie.setHttpOnly(true);
    cookie.setPath("/"); 
    return "delete cookie success";
}
```

### @RequestAttribute

- 获取 request 的属性的值

```java
@Controller
@RequestMapping("/application")
public class ApplicationController {
    @GetMapping("/gotoDestination")
    public String gotoDestination(HttpServletRequest request) {
        // 设置request属性值
        request.setAttribute("code", "200");
        request.setAttribute("message", "success");

        // 转发到/destination
        return "forward:/application/destination";
    }

    @ResponseBody
    @GetMapping("/destination")
    public Map<String, Object> getDestination(
        @RequestAttribute("message") String message, HttpServletRequest request) {
        Map<String, Object> map = new HashMap<>();
        map.put("code", request.getAttribute("code"));
        map.put("message", message);

        // {"code":"200", "message":"success"}
        return map;
    }
}
```

### @MatrixVariable

- 获取矩阵变量的值

```java
/**
 * 路径变量配置
 */
@Configuration(proxyBeanMethods = false)
public class ApplicationConfiguration {
    @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMath(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                // 不移除路径变量中分号后面的内容（矩阵变量）
                urlPathHelper.setRemoveSemicolonContent(false);

                configurer.setUrlPathHelper(urlPathHelper);
            }
        };
    }
}
```

```java
/**
 *  获取矩阵变量的值
 */
@GetMapping("/user/{bossAge}/{empAge}/getMatrixVariables")
public Map<String, Object> getMatrixVariables(
    @MatrixVariable(value = "age", pathVar = "bossAge") Integer bossAge,
    @MatrixVariable(value = "age", pathVar = "empAge") List<Integer> empAge
) {
    Map<String, Object> map = new HashMap<>();
    map.put("bossAge", bossAge);
    map.put("empAge", empAge);
    return map;
}
```

请求：localhost:8080/application/user/boss;age=23/emp;age=18;age=19,20,21/getMatrixVariables
响应：

```json
{
    "bossAge":23,
    "empAge":[18,19,20,21]
}
```

### Spring MVC 原生对象获取

- 只要在对应的控制器方法添加形参即可获取到

```java
@RequestMapping("/hello")
public String hello(HttpServletRequest request, 
                    HttpServletResponse response,
                    HttpSession session,
                    InputStream inputStream,
                    OutputStream outputStream,
                    Reader reader,
                    Writer writer
                    ) {
    return "hello world";
}
```

- ServletContext获取

ServletContext是最大的配置文件，全局最大共享域，核心api为getRealPath

```java
// 1.DI获取
@Autowired
private ServletContext servletContext;

// 2.request和session获取
@RequestMapping("/hello")
public String hello(HttpServletRequest request, 
                    HttpSession session
                    ) {
    ServletContext servletContext = request.getServletContext();
    ServletContext servletContext2 = session.getServletContext();                      
    return "hello world";
}
```

### 共享域对象操作

- 原生共享域：request、session、servletContext

- springmvc提供的request级别的共享域：model、modelMap、map、modelAndView

```java
@Autowired
private ServletContext servletContext;

@RequestMapping("/hello")
public ModelAndView hello(HttpServletRequest request, 
                    HttpSession session,
                    Model model, 
                    ModelMap modelMap,
                    Map<String, Object> map
                    ) {
    request.setAttribute("key", "value");
    model.addAttribute("key", "value");
    modelMap.addAttribute("key", "value");
    map.put("key", "value");

    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("key", "value");
    modelAndView.setViewName("视图（页面）名称");
    return modelAndView;
}
```

## 结果响应

### @ResponseBody

- 结果（json数据）放入响应体直接返回，不走视图解析器，转发和重定向都失效

- 在类上注解

```Java
@ResponseBody
public class ApplicationController {}
```

- 在方法上加注解

```java
public class ApplicationController {
    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "hello world";
    }
}
```

### @RestController

@RestController = @Controller + @ResponseBody

### 转发和重定向

- 转发只能转发到项目下的资源

- 重定向可以重定向到项目下的资源，也可以重定向到项目外的资源

```java
@Controller
@RequestMapping("/application")
public class ApplicationController {
    @ResponseBody
    @GetMapping("/destination")
    public String destination() {
        return "destination";
    }

    @GetMapping("/forward")
    public String forward() {
        // 转发到/destination
        return "forward:/application/destination";
    }

    @GetMapping("/redirect")
    public String redirect() {
        // 重定向到/destination，项目内资源
        return "redirect:/application/destination";
        // 重定向到百度，项目外资源
        return "redirect:http://www.baidu.com";
    }
}
```

### 静态资源

- MvcNamespaceHandler解析default-servlet-handler，由DefaultServletHandlerBeanDefinitionParser的parse方法处理
    - 往IOC容器添加了DefaultServletHttpRequestHandler（可以把它当成另一个专门处理静态资源的HandlerMapping）
        - handleRequest方法中，当没有找到handler时，对请求进行了转发，去找静态资源

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration implements WebMvcConfigurer{
    /**
     * 开启静态资源查找，同<mvc:default-servlet-handler />
     * dispatcherServlet根据请求路径调用handlerMapping查找有没有对应的handler，如果没有，再找静态资源
     */
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
}
```

## 全局异常处理

- @ControllerAdvice，异常处理器，全局异常发生，会走此类的handler逻辑，可以返回视图、转发和重定向

- @RestControllerAdvice = @ControllerAdvice + @ResponseBody，返回json

```java
// 1.声明全局异常处理类
@RestControllerAdvice
public class GlobalExceptionHandler {
    // 2.声明异常处理方法
    @ExceptionHandler(RuntimeException.class)
    public ResultVo<String> runtimeExceptionHandler(RuntimeException exception) {
        String errorMessage = exception.getLocalizedMessage();
        if (StringUtils.isBlank(errorMessage)) {
            errorMessage = getErrorMessage(exception);
        }
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), errorMessage);
    }
 
    @ExceptionHandler(Exception.class)
    public ResultVo<String> runtimeExceptionHandler(Exception exception) {
        String errorMessage = exception.getLocalizedMessage();
        if (StringUtils.isBlank(errorMessage)) {
            errorMessage = getErrorMessage(exception);
        }
        return ResultVo.failure(ResultCodeEnum.BAD_REQUEST.getCode(), errorMessage);
    }
 
    public static String getErrorMessage(Exception exception){
        String errorMessage = exception.getLocalizedMessage();
        if (StringUtils.isBlank(errorMessage)) {
            StringWriter stringWriter = new StringWriter();
            PrintWriter printWriter = new PrintWriter(stringWriter);
            exception.printStackTrace(printWriter);
            errorMessage = stringWriter.toString();
            if (errorMessage.length() > 500) {
                errorMessage = errorMessage.substring(0, 500);
            }
        }
        return errorMessage;
    }
}
```

## Spring MVC拦截器

- DispatcherServlet的doDispatch方法内
    - getHandler，获取handlerMapping
    - getHandlerAdapter，获取handlerAdapter
    - applyPreHandle，执行拦截器的preHandle
        - 正序遍历拦截器集合，依次调用preHandle
    - handle，调用handler
    - applyPostHandle，执行拦截器的postHandle
        - 倒序遍历拦截器集合，依次调用postHandle
    - processDispatchResult
        - triggerAfterCompletion，执行拦截器的afterCompletion
            - 倒序遍历拦截器集合，依次调用afterCompletion
- 1.定义拦截器

```java
public class LoginInterceptor implements HandlerInterceptor {
    // 在执行控制器的handler方法前执行
    // 形参中的handler就是控制器的handler方法
    // 可用来编码格式设置，登录保护，权限处理
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) 
        throws Exception {
        // 返回true，放行，返回false，不放行
        return true;
    }
    
    // 在控制器的handler方法执行完毕后执行
    // preHandle返回false或handler报错不执行
    // 可用来处理结果，敏感词检查
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, 
                ModelAndView modelAndView) {
        // todo
    }
    
    // 整体处理完毕后执行
    // 可用来做响应计数
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception e) {
        // todo
    }
}
```

- 2.将拦截器加入spring mvc的IOC容器

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration implements WebMvcConfigurer{
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                // 可设置只拦截指定地址，用通配符*表示任意一层，**表示任意多层
                .addPathPatterns("/account/**")
                // 可设置排除拦截地址，排除拦截地址应该在拦截地址范围内
                .excludePathPatterns("/account/getAccountById");
        // 可添加多个拦截器，先添加的优先级高
        registry.addInterceptor(new LogInterceptor());
    }
}
```

## 参数校验

- Bean Validation 是一套定义 JavaBean 参数校验标准的规范 (JSR 303, 349, 380)，它提供了一系列注解，可以直接用于 JavaBean 的属性上，从而实现便捷的参数校验
    - JSR 303 (Bean Validation 1.0): 奠定了基础，引入了核心校验注解（如 @NotNull、@Size、@Min、@Max 等），定义了如何通过注解的方式对 JavaBean 的属性进行校验，并支持嵌套对象校验和自定义校验器。
    - JSR 349 (Bean Validation 1.1): 在 1.0 基础上进行扩展，例如引入了对方法参数和返回值校验的支持、增强了对分组校验（Group Validation）的处理。
    - JSR 380 (Bean Validation 2.0): 拥抱 Java 8 的新特性，并进行了一些改进，例如支持 java.time 包中的日期和时间类型、引入了一些新的校验注解（如 @NotEmpty, @NotBlank等）。

Bean Validation 本身只是一套规范（接口和注解），我们需要一个实现了这套规范的具体框架来执行校验逻辑

目前，Hibernate Validator 是 Bean Validation 规范最权威、使用最广泛的参考实现

需要注意的是：所有的注解，推荐使用 JSR 注解，即`javax.validation.constraints`，而不是`org.hibernate.validator.constraints`

- springboot依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

- 非 SpringBoot 项目需要自行引入相关依赖包
    - Hibernate Validator 6.x 及更高版本实现了 Bean Validation 2.0 (JSR 380)

```xml
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>8.0.1.Final</version>
</dependency>
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator-annotation-processor</artifactId>
    <version>8.0.1.Final</version>
</dependency>
```

### 验证请求体

当 Controller 方法使用 @RequestBody 注解来接收请求体并将其绑定到一个对象时，可以在该参数前添加 @Valid 注解来触发对该对象的校验。

如果验证失败，它将抛出MethodArgumentNotValidException。

#### 定义实体类

```java
@Getter
@Setter
@ToString
public class User {
    private Long id;

    @NotBlank
    private String name;

    @Length(min = 6, max = 16)
    private String password;

    @Min(1)
    private int age;

    @Email
    private String email;

    @Past
    private LocateDate birthday;
}
```

#### 定义控制器

```java
@RequestMapping("/user")
@RestController
public class UserController {
    @PostMapping("/updateUser")
    public void updateUser(@RequestBody @Valid User user) {
        // todo
    }
}
```

```java
// 下面的@Validated注解是之前的笔记，应该是不规范的，没有验证过，先留着吧
@RequestMapping("/user")
@RestController
public class UserController {
    // 还可以捕捉校验错误信息，BindingResult必须紧挨着校验对象
    @PostMapping("/updateUser")
    public void updateUser(@Validated @RequestBody User user, BindingResult result) {
        if (result.hasErrors()) {
            // todo
        }
    }
}
```

### 验证请求参数

- 对于直接映射到方法参数的简单类型数据（如路径变量 @PathVariable 或请求参数 @RequestParam），校验方式略有不同：
    - 在 Controller 类上添加 `@Validated` 注解：这个注解是 Spring 提供的（非 JSR 标准），它使得 Spring 能够处理方法级别的参数校验注解。这是必需步骤。
    - 将校验注解直接放在方法参数上：将 @Min, @Max, @Size, @Pattern 等校验注解直接应用于对应的 @PathVariable 或 @RequestParam 参数。

```java
// 在类上添加@Validated注解
@Validated
@RequestMapping("/user")
@RestController
public class UserController {
    @PostMapping("/getUserByName")
    public void getUserByName(
        @RequestParam("name")
        @NotBlank(message = "姓名不能为空")
        @Size(max = 10, message = "姓名长度不能超过 10")
        String name) {
        // todo
    }
}
```
