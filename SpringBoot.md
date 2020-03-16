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

## 事务管理

事务的四个属性：原子性，一致性，隔离和永久性。

两种类型的事务管理：

- 编程式事务管理：借助编程来管理事务
- 声明式事务管理：将事务管理与业务代码分开，只能使用注解和XML配置来管理事务

（Spring通过SpringAOP框架支持声明式事务管理）

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

通过注解：

- @Aspect声明切面类，放在类上面
- 建言类型：@Around,@Before,@After,@AfterReturing,@AfterThrowing
- 切入点，在建言的括号里
  - execution(* com.zdy..*(..))：com.zdy包下所有类的所有方法.
  - execution(* com.zdy.Dog.*(..)): Dog类下的所有方法.
- @Pointcut

通过XML配置

优先级问题：一个切面中的多个增强，不同切面的多个增强

> 在目标方法前面的增强@Before，优先级越高，越先执行。 在目标方法后面的增强@After,@AfterReturning，优先级越高，越后执行。
>
> 一个Aspect中定义多个增强。和定义的先后顺序有关，越先出现，优先级越高。
>
> 不同的Aspect中的多个增强。根据Aspect实现Ordered接口，方法getOrder返回值有关，返回值越低,优先级越高。
>
> 链接：https://juejin.im/post/5a56cdf06fb9a01cbe6539d2