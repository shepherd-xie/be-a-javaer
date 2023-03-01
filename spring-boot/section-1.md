# run

## 一切的开始

```java
@SpringBootApplication
public class HelloSpringBootApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloSpringBootApplication.class, args);
    }
}
```

首先可以看到 SpringBootApplication 的注解，声明了几个比较关键的配置类。

```java
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
public @interface SpringBootApplication
```

```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration
```

```java
@Import({AutoConfigurationPackages.Registrar.class})
public @interface AutoConfigurationPackage
```

接下来看核心的部分。

```java
SpringApplication.run(HelloSpringBootApplication.class, args);
```

```java
/**
 * Static helper that can be used to run a {@link SpringApplication} from the
 * specified sources using default settings and user supplied arguments.
 * @param primarySources the primary sources to load
 * @param args the application arguments (usually passed from a Java main method)
 * @return the running {@link ApplicationContext}
 */
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
   return new SpringApplication(primarySources).run(args);
}
```

这里可以看到实际创建了一个 SpringApplication 实例，类注解声明了它的作用。

> Class that can be used to bootstrap and launch a Spring application from a Java main method. By default class will perform the following steps to bootstrap your application:
>
> - Create an appropriate ApplicationContext instance (depending on your classpath)
> - Register a CommandLinePropertySource to expose command line arguments as Spring properties
> - Refresh the application context, loading all singleton beans
> - Trigger any CommandLineRunner beans

>可用于引导和从Java Main方法启动Spring应用程序的类。 默认类将执行以下步骤来引导您的应用程序：
>
> - 创建适当的应用程序context实例（取决于您的classPath）
> - 注册Commandline PropertySource以将命令行参数视为Spring属性
> - 刷新应用程序上下文，加载所有Singleton Bean
> - 触发任何Commandlinerunner bean

```java
/**
 * Run the Spring application, creating and refreshing a new
 * {@link ApplicationContext}.
 * @param args the application arguments (usually passed from a Java main method)
 * @return a running {@link ApplicationContext}
 */
public ConfigurableApplicationContext run(String... args) {
   long startTime = System.nanoTime();
   DefaultBootstrapContext bootstrapContext = createBootstrapContext();
   ConfigurableApplicationContext context = null;
   configureHeadlessProperty();
   SpringApplicationRunListeners listeners = getRunListeners(args);
   listeners.starting(bootstrapContext, this.mainApplicationClass);
   try {
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
      ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);
      Banner printedBanner = printBanner(environment);
      context = createApplicationContext();
      context.setApplicationStartup(this.applicationStartup);
      prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
       // 刷新 Bean 实例
      refreshContext(context);
      afterRefresh(context, applicationArguments);
      Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
      // ... 省略日志处理
      listeners.started(context, timeTakenToStartup);
       // 调用 ApplicationRunner CommandLineRunner
      callRunners(context, applicationArguments);
   }
   catch (Throwable ex) {
      // ... 省略异常处理
   }
   try {
      if (context.isRunning()) {
         Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
         listeners.ready(context, timeTakenToReady);
      }
   }
   catch (Throwable ex) {
      // ... 省略异常处理
   }
   return context;
}
```

## 自动装载

在执行 run 方法之前会有一步新建 SpringApplication 实例，自动装配的绝大多数操作都是在这里进行的。

```java
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
   return new SpringApplication(primarySources).run(args);
}
```

新建时会调用以下重载方法。

```java
/**
 * Create a new {@link SpringApplication} instance. The application context will load
 * beans from the specified primary sources (see {@link SpringApplication class-level}
 * documentation for details). The instance can be customized before calling
 * {@link #run(String...)}.
 * @param resourceLoader the resource loader to use
 * @param primarySources the primary bean sources
 * @see #run(Class, String[])
 * @see #setSources(Set)
 */
@SuppressWarnings({ "unchecked", "rawtypes" })
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
   this.resourceLoader = resourceLoader;
   Assert.notNull(primarySources, "PrimarySources must not be null");
   this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
   this.webApplicationType = WebApplicationType.deduceFromClasspath();
   this.bootstrapRegistryInitializers = new ArrayList<>(getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
   setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
   setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
   this.mainApplicationClass = deduceMainApplicationClass();
}
```

可以看其中频繁调用了 `getSpringFactoriesInstances` 方法，实际会进入以下重载方法。

```java
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
   ClassLoader classLoader = getClassLoader();
   // Use names and ensure unique to protect against duplicates
   Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
   List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
   AnnotationAwareOrderComparator.sort(instances);
   return instances;
}
```

在 loadFactoryNames 注解上可以清楚的看到从 FACTORIES_RESOURCE_LOCATION 也就是 "META-INF/spring.factories" 中加载所需的 FactoryName。

```java
/**
 * Load the fully qualified class names of factory implementations of the
 * given type from {@value #FACTORIES_RESOURCE_LOCATION}, using the given
 * class loader.
 * <p>As of Spring Framework 5.3, if a particular implementation class name
 * is discovered more than once for the given factory type, duplicates will
 * be ignored.
 * @param factoryType the interface or abstract class representing the factory
 * @param classLoader the ClassLoader to use for loading resources; can be
 * {@code null} to use the default
 * @throws IllegalArgumentException if an error occurs while loading factory names
 * @see #loadFactories
 */
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
   ClassLoader classLoaderToUse = classLoader;
   if (classLoaderToUse == null) {
      classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
   }
   String factoryTypeName = factoryType.getName();
   return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

然后再调用 createSpringFactoriesInstances 去实例化获取的 FactoryName，这就是 SpringBoot 自动装配的真相。