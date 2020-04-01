# AOP

* Spring AOP代理机制
  * JDK动态代理: 针对实现了接口的类
  * CGLib动态代理: 针对没有实现接口的类，应用最底层的字节码增强技术生成当前类的子类对象
* 术语
  * Joinpoint(连接点): 被拦截的点，在spring中，这些点指的是方法，因为spring只支持方法类型的连接点（可以切入的点）
  * Pointcut(切入点): 对哪些Joinpoint进行拦截的定义。（已经被切入的点）
  * Advice(通知/增强): 拦截到Joinpoint之后所要做的事情就是通知。通知分为前置通知、后置通知、异常通知、最终通知、环绕通知（切面要完成的功能）
  * Introduction(引介): 一种特殊的通知，在不修改类代码的前提下，Introduction可以在运行期为类动态地添加一些方法或Field。
  * Aspect(切面): Pointcut和Advice|Introduction的结合
  * Target(目标对象): 代理的目标对象
  * Proxy(代理): 一个类被AOP织入增强后，就产生了一个结果代理类。
  * Weaving(织入): 将增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入
* aop:config, aop:pointcut, aop:aspect, aop:before|after|after-returning|after-throwing|around
* `<aop:aspectj-autoproxy/>`, @Aspect, @Pointcut, @Before, @After, @AfterReturning, @AfterThrowing, @Around
* pointcut
  * `public * *(..)`: 任何public方法
  * `* set*(..)`: 任何set方法
  * `* com.xyz.service.UserService.(..)`: com.xyz.service.UserService类的任何方法
  * `* com.xyz.service.*.(..)`: com.xyz.service包下任何方法
  * `* com.xyz.service..*.(..)`: com.xyz.service包及其子包下任何方法

