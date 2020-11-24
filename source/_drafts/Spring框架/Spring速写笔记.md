---
title: Spring框架/Spring速写笔记
tags:
---

# Spring框架
## 1.1、Spring基础
### 依赖注入
Spring中控制反转（Inversion of Control,IOC）是通过依赖注入（Dependeney Injection,DI）实现的。
申明Bean注解
- @Component 通用场景
- @Service 适用于业务逻辑层场景
- @Repository 适用于数据访问层（DAO）
- @Controller 适用于Mvc中的表现层
```java
@Component  //默认场景
public class AppConfig {
}

@Controller //适用于Mvc控制层
public class UserController {
}

@Service    //适用于业务逻辑层
public class UserService {
}

@Repository //适用于数据访问层
public class UserDAO {
}
```
注入Bean注解
- @Autowrited Spring提供的注解
- @Inject JSR-330提供的注解
- @Resource JSR-250提供的注解
```java
public class BeanTest{
     @Autowired
     AppConfig appConfig;

     @Resource
     UserController userController;

     @Inject
     UserService userService;
 }
```

### JAVA配置
Spring推荐的配置方式，可以极大的减少Spring框架和代码的耦合。
- @Configuration 申明为Java配置类
- @Bean 申请配置类中的方法创建一个Bean

## AOP
面向切面编程，目的是为了解耦，是业务代码和框架代码进行去耦合。
1. 使用@Aspect声明一个切面。
2. 使用@After、@Before、@Around声明一个通知（advice）。
3. 通知的拦截规则为切点（pointcut），使用@Pointcut。
4. 满足切点条件的代码处称为连接点（JoinPoint）。

需要将切面（Aspect）申明为Bean交给Spring托管，并且Spring要开启AOP（@EnableAspectJAutoProxy）

## 1.2、Spring常用配置
### Scope
@Scope 用于指定Spring容器如何创建Bean的实例。
- Singletion：单例模式，Spring创建Bean的默认配置。
- Prototype：原型模式，每次调用均创建新实例。
- Request：Web模式下每个http request共享一个实例。
- Session：Web模式下每个http session共享一个实例。

### SpEL和资源协调
使用Spring表达式语言将资源注入，通过@Value中参数使用表达式：
```java
@Component
@Configuration
@PropertySource("classpath:app.properties")
public class ValueJavaConfig {
    //1. 注入普通常量：
    @Value("abc")
    private String abc;

    //2. 注入操作系统属性：
    @Value("#{systemProperties['os.name']}")
    private String osName;

    //3. 注入表达式运算结果：
    @Value("#{T(java.lang.Math).random()*100.0}")
    private Double randomValue;

    //4. 注入其他Bean的属性：
    @Value("#{otherBean.name}")
    private String otherName;

    //5. 注入文件内容：
    @Value("classpath:db.properties")
    private Resource jdbcFile;

    //6. 注入网址内容：
    @Value("http://www.baidu.com")
    private Resource baiduUrl;

    //7. 注入属性文件：
    @Value("${book.name}")
    private String bookName;
}

```

### Bean初始化和销毁
1. 配置方式：在JavaConfig中使用@Bean的initMethod和destroyMethod属性。
2. 注解方式：使用JSR250定义的@PostConstruct、@PreDestroy注解。
```java
//利用@Bean的initMethod和destroyMethod方法指定初始化
  @Bean(initMethod = "init", destroyMethod = "destroy")
  public PropertySourcesPlaceholderConfigurer propertyConfigure() {
      return new PropertySourcesPlaceholderConfigurer();
  }

  //利用JSR250的@PostConstruct和@PreDestroy注解
  public class MyBean {
      @PostConstruct
      public void init() {
      }
      @PreDestroy
      public void destory() {
      }
  }

```

### Profile环境设置
通过Profile的定义提供不同环境下的配置支持。
1、通过设定Environment的ActiveProfiles来设定当前context需要使用的配置环境，并且通过@Profile可以达到不同环境下实例化不同的Bean。
```java
 ctx.getEnvironment().setActiveProfiles("dev");
```
2、通过设定jvm的spring.profile.active参数来设置配置环境。
3、Web项目设置在Servlet的context parameter中。
```xml
<servlet>
		<servlet-name>SpringMvcDispatcher</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>spring.profile.active</param-name>
			<param-value>dev</param-value>
		</init-param>
	</servlet>
```
```Java
public class WebInit implements WebApplicationInitializer {
     @Override
     public void onStartup(ServletContext servletContext) throws ServletException {
         servletContext.setInitParameter("spring.profile.active","dev")
     }
 }
```

### Application Event
Spring事件模型为Bean之间的消息通信提供了支持，当一个Bean任务处理完成后，另一个Bean需要知道并同步执行，这样就需要另外一个Bean监听当前Bean发起的事件。
Spring事件遵循以下流程：
（1）自定义事件，继承ApplicationEvent
（2）定义事件监听器，实现ApplicationListener
（3）定义事件发布者，使用容器发布事件
```java
@Component
public class AppPublisher{
    @Autowired
    ApplicationContext applicationContext;

    public void publish(String msg){
        applicationContext.publishEvent(new AppCoustomerEvent(msg));
    }
}
```

## 1.3、Spring高级话题
### Spring Aware
Spring Aware的目的是为了让Bean获得Spring容器的服务，但这会导致Bean和Spring框架强耦合。
- BeanNameAware：获得容器中Bean的名称
- BeanFactoryAware：获得当前Bean Factory，获取使用容器的服务
- ApplicationContextAware：获取当前Application Context，获取使用容器的服务
- MessageSourceAware：获取Message source，获取文本信息
- ApplicationEventPublisherAware：应用事件发布器
- ResourceLoaderAware：获取资源加载器，获取外部资源文件

定义Bean继承上面的接口，即可获取相关容器的服务，继承ApplicationContextAware后可获得Spring容器所有服务。
```java
@Service
public class AwareService implements BeanNameAware, ResourceLoaderAware {

    private String beanName;
    private ResourceLoader resourceLoader;

    @Override
    public void setBeanName(String s) {
        beanName = s;
    }

    @Override
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }

    public void print() {
        System.out.println("Bean名称为" + beanName);
        Resource resource = resourceLoader.getResource("classpath:app.properties");
        System.out.println(resource.getFilename());
    }
}
```

### 多线程
Spring通过执行器（TaskExecutor）实现多线程和并发编程，使用ThreadPoolTaskExecutor实现一个基于线程池的TaskExecutor。通过@EnableAsync开启对异步任务的支持，通过@Async注解声明异步方法。
```java
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer {
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(5);
        taskExecutor.setMaxPoolSize(10);
        taskExecutor.setQueueCapacity(100);
        taskExecutor.initialize();
        return taskExecutor;
    }

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return null;
    }
}
```

### 计划任务
在配置类中申请注解@EnableScheduling开启计划任务的支持，在方法上申明@Scheduled申明方法为计划任务，支持cron、fixDelay、fixRate等类型的计划任务。
```java
@Service
public class ScheduledTaskService {
    //每5秒执行一次
    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
    }

    //每天11.28分执行
    @Scheduled(cron = "0 28 11 ? * *")
    public void fixTimeExecution() {
    }
}
```

### 条件注解
@Conditional提供了更加通用的基于条件Bean的创建，@Conditional根据满足某个特定场景来创建特定Bean，通过这个可以实现自动配置。
```java
@Configuration
public class ConditionConfig {
    @Bean
    @Conditional(WindowsConditional.class)
    public ListService windowsListService(){
        return new WindowsListService();
    }
    @Bean
    @Conditional(LinuxConditional.class)
    public ListService linuxListService(){
        return new LinuxListService();
    }
}

public class WindowsConditional implements Condition {
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        return conditionContext.getEnvironment().getProperty("os.name").contains("Windows");
    }
}
```

### 组合注解与元注解
@SpringBootApplication注解便是一个组合注解，组合注解省去了大量注解使用，消除了样板代码。
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

### @Enable注解工作原理
第一种，通过@Import注解导入JavaConfig，在导入的JavaConfig中申请Bean。
第二种，通过条件选择配置类，接口为ImportSelector，重写SelectImport方法，加载对应的JavaConfig。
第三种，通过动态注册Bean，通过实现ImportBeanDefinitionRegistrar接口，在运行时自动添加Bean到JavaConfig中。
```java
public class AutoProxyRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

    }
}
```

### 单元测试
Spring通过Spring TestContext FrameWork对集成测试提供支持，他不依赖于特定的测试框架（Junit、TestNG）
通过@SpringJuint4ClassRunner提供Spring TestContext FrameWork的功能，通过@ContextConfiguration配置Application Context（Java配置类），通过@ActiveProfiles制定运行环境。


# SpringMVC
## SpringMvc概述
MVC：Model(数据模型)+View（视图）+Controller（控制器）
三层架构：Presentation tier（展示层）+Application tier（应用服务层）+Data tier（数据访问层）
MVC只存在于展示层，Model是数据模型，SpingMvc中的Model类用于和View进行数据交互、传值，View视图包含JSP、Thymeleaf等，Controller则是和Application tier进行交互的。

### 快速搭建SpringMvc
Servlet2.5及以下需要配置web.xml，而Servlet3.0则可以通过WebApplicationInitializer接口注册DispatcherServlet。
