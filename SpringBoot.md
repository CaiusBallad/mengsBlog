[TOC]

# 项目结构

- （java）工程代码
- （resources）静态文件
- （test）测试文件

# Spring核心概念

## IoC控制反转

IoC容器读取配置元数据（XML，JAVA注解或者JAVA代码）来对对象进行配置，组装和实例化。

Spring中有两种类型的容器：

- BeanFactory容器：资源有限情况如移动设备使用
- ApplicationContext容器：包含前者的所有功能

## Bean

由IoC容器容器管理的实例化对象，创建时需要配置元属性信息：创建方式，生命周期信息，依赖关系。

**Bean的范围（scope）包括**：singleton（默认值，每个容器单一实例），prototype（需要时返回新的实例），request，session和global-session

**生命周期**：init-method和destory-method分别为初始化回调函数参数和销毁回调函数参数。

## DI依赖注入

> Dependency Injection

DI可以讲多个独立的Java类粘合起来，同时又保持他们的独立性，达到解耦的目的。

1. 基于构造函数的DI
2. 基于Setter的DI

## 自动装配

自动装配Auto-Wiring类型有：

- byName
- byType
- constructor

# 事务管理

事务的四个属性：原子性，一致性，隔离和永久性。

两种类型的事务管理：

- 编程式事务管理：借助编程来管理事务
- 声明式事务管理：将事务管理与业务代码分开，只能使用注解和XML配置来管理事务



SpringBoot中使用@Transactional开启事务，指定方法中的操作要么都成功，要么都回滚。

需要注意的是：

1. @Transactional注解只能用在public方法上，其余的不会起作用
2. 默认情况，Spring会对unchecked异常（空指针）进行事务回滚，如果是checked异常（文件读写，网络问题）则不回滚
3. 数据库的引擎需要支持事务

在之前的demo中的Service中选一个方法开启事务：

```java
@Transactional
public void changePassword(){
    userDao.updateUser("333", 4);
    int errorValue = 1/0;
    userDao.updateUser("111111", 1);
}
```

会发现由于出错，两者都不会对数据库进行操作



# 常用注解

## 启动注解

- @SpringBootApplication启动注解

> 可以看出大概可以把 `@SpringBootApplication`看作是 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。根据 SpringBoot官网，这三个注解的作用分别是：
>
> 1. @EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制`
> 2. `@ComponentScan`： 扫描被`@Component` (`@Service`,`@Controller`)注解的bean，注解默认会扫描该类所在的包下所有的类。
> 3. @Configuration：表面该类可以被IoC容器用作Bean定义的来源，允许在上下文中注册额外的bean或导入其他配置类。



## Controller中常用注解

> 1. `@RestController`  **将返回的对象数据直接以 JSON 或 XML 形式写入 HTTP 响应(Response)中。**绝大部分情况下都是直接以  JSON 形式返回给客户端，很少的情况下才会以 XML 形式返回。转换成 XML 形式还需要额为的工作，上面代码中演示的直接就是将对象数据直接以 JSON 形式写入 HTTP 响应(Response)中。关于`@Controller`和`@RestController` 的对比，我会在下一篇文章中单独介绍到（`@Controller` +`@ResponseBody`= `@RestController`）。
>    1. @RestController和@Controller，前者是新的注解，综合了2个注解，用来返回json数据
> 2. `@RequestMapping` :上面的示例中没有指定 GET 与 PUT、POST 等，因为**`@RequestMapping`默认映射所有HTTP Action**，你可以使用`@RequestMapping(method=ActionType)`来缩小这个映射。
> 3. `@PostMapping`实际上就等价于 `@RequestMapping(method = RequestMethod.POST)`，同样的 `@DeleteMapping` ,`@GetMapping`也都一样，常用的 HTTP Action 都有一个这种形式的注解所对应。
> 4. `@PathVariable` :取url地址中的参数。`@RequestParam` url的查询参数值。
> 5. `@RequestBody`:可以**将 \*HttpRequest\* body 中的 JSON 类型数据反序列化为合适的 Java 类型。**
> 6. `ResponseEntity`: **表示整个HTTP Response：状态码，标头和正文内容**。我们可以使用它来自定义HTTP Response 的内容。

其余的关键注解：

1. @Component：对类进行标注，泛指组建Bean，在应用启动的时候会被容器加载并加入容器管理，其中@Controller，@Service，@Repository是它的细化，分别对应了控制层，服务层和持久层的Bean。
2. @Autowired：通过byType的方式，自动注入，使得对象直接定义而不需要new
3. @Required：适用于Bean属性的Setter方法，该属性必须在XML配置文件中填充
4. @Resource：通过byName的方式，自动注入
5. @Qualifier：多个相同类型的Bean存在，可以指定装配哪一个
6. @Configuration：专门进行依赖关系配置

# 读取配置文件

- @Value("${property}")可以为成员变量注入属性值
- @ConfigurationProperties读取属性值，并与Bean（需要使用@Component）绑定，在prefix=""中来指定具体的配置项
  - 如果Bean没有添加@Component，则需要@EnableConfigurationProperties注册配置的Bean
  - 可以使用注解@Validated来校验属性，在字段上添加如@NotNull，@Email等
- @PropertySource方在配置类上，用来读取指定的配置文件

# 异常处理

全局异常处理，ResponseStatusException转状态码处理：

- @ControllerAdvice：可以指定特定的类
- @ExceptionHandler处理Controller级别的异常



# AOP

面向切面编程，需要了解的概念有：

- Poincut切点：定位到具体方法的表达式，一般都是类中的方法
- Advice增强/建言：定位到方法后要做的事情，如事务，日志等
- Aspect切面：切点+建言

通过注解实现：

- @Aspect声明切面类，放在类上面
- 建言类型：@Around,@Before,@After,@AfterReturing,@AfterThrowing
- 切入点，在建言的括号里
  - execution(* com.zdy..*(..))：com.zdy包下所有类的所有方法.
  - execution(* com.zdy.Dog.*(..)): Dog类下的所有方法.
- @Pointcut

通过XML配置实现

优先级问题：一个切面中的多个增强，不同切面的多个增强

> 在目标方法前面的增强@Before，优先级越高，越先执行。 在目标方法后面的增强@After,@AfterReturning，优先级越高，越后执行。
>
> 一个Aspect中定义多个增强。和定义的先后顺序有关，越先出现，优先级越高。
>
> 不同的Aspect中的多个增强。根据Aspect实现Ordered接口，方法getOrder返回值有关，返回值越低,优先级越高。
>
> 链接：https://juejin.im/post/5a56cdf06fb9a01cbe6539d2

# 缓存

SpringBoot框架中可以使用不同的缓存技术，同时也可以不使用第三方的缓存依赖，使用Spring的ConcurrenMapCacheManager作为缓存管理器。

使用SpringBoot集成Cache时，需要注册实现CacheManager的Bean。

@EnableCaching开启缓存技术，放在Application上；然后在需要缓存的方法上加入@Cacheable注解

先在pom文件中引入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

在Application函数上添加：@EnableCaching

在查询方法上添加@Cacheable注解：

```java
@Cacheable("user1")
public User selectUserById(Integer id){
    return userXMLDao.findUserById(id);
}
```

注解中的“user1”是缓存的值（value）的意思，即调用该方法时，会从“user1”中查找值，而我们传递的参数是key。

修改数据库的值，再查询时，可以发现查询的是缓存中的旧值。

# 异步编程

@EnableAsync开启异步任务，放在Application上。

@Async放在具体方法上，表面是一个异步任务

需要继承AsyncConfigurerSupport接口来进行相关配置，如设置线程池大小等。

如果不开启异步，多个请求都在Main线程池里面进行

# 日志

默认使用Logback来生成日志，使用INFO输出到控制台。

# Swagger

Swagger是一个api框架，能提供在线文档查阅和在线文档测试。

需要引入依赖，新建一个配置类，并在类上开启@EnableSwagger2，指定包来生成文档。

具体的接口描述通过@Api，@ApiOperation等来标注

先引入依赖：

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

然后创建一个配置类：

```java
@Configuration
@EnableSwagger2
public class Swagger2 {

    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                //加载配置信息
                .apiInfo(apiInfo())
                //设置全局响应参数
                .select()
                //加载swagger扫描包
                .apis(RequestHandlerSelectors.basePackage("com.example.demo"))
                .paths(PathSelectors.any())
                .build();
    }
    /**
     * 配置基本的API文档信息
     * @return
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Demo的API文档")
                .description("这是一个快速生成的API文档")
                .build();
    }
}
```

@Configuration表面这是一个配置类，@EnableSwagger2开启Swagger

之后在Controller中的接口上添加描述：

```java
@ApiOperation(value = "查询用户",notes = "查询单个用户的所有信息")
@RequestMapping("/query")
public User query(){
    return userService.selectUserById(1);
}

@ApiOperation(value = "创建用户",notes = "创建一个新的用户")
@ApiImplicitParam(name = "user",value = "用户的详细信息",required = true, dataType = "User")
@RequestMapping("/insert")
public List<User> insertUser(){
    String before ="Before: "+ userService.selectAllUser() + "]";
    System.out.println(before);
    userService.insertUser();
    return userService.selectAllUser();
}
```

```java
@ApiIgnore
@RequestMapping("delete")
public List<User> testDelete(){
    userService.deleteUser(2);
    return userService.selectAllUser();
}
```

还可使用@ApiModel来描述实体，@ApiModelProperty描述属性。

然后访问：http://localhost:8080/swagger-ui.html#/地址即可

# 邮件服务

在pom中添加依赖：spring-boot-starter-mail

在application.properties中添加邮箱配置

1. 可通过实现MailService接口来发送邮件：简单消息，HTML邮件，带附件的邮件，带图片的邮件
2. 邮件系统：固定模板，其中能存在能改变的参数；发送失败的处理方式

# 定时任务

启动类上添加@EnableScheduling，然后在具体实现上使用@Scheduled注解

- cron="*/6****?"
- fixedRate = 6000

以上2种都表示每隔6秒运行一次

# 整合Docker

需要做的内容包括：

1. 创建Dockerfile文件
2. 在pom中添加docker插件
3. 使用maven命令打包
   1. mvn clean
   2. mvn package docker:build
4. 构建完毕后可以查看镜像：docker images
5. 启动镜像：docker run -p 8080:8080 -t <image>



