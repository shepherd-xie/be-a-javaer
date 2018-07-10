## 动态代理

---

所谓动态代理，即通过代理类：Proxy的代理，接口和实现类之间可以不直接发生联系，而可以在运行期（Runtime）实现动态关联。

java动态代理主要是使用java.lang.reflect包中的两个类。

**InvocationHandler类**

* public Object invoke(Object proxy, Method method, Object[] args) throws Throwable

proxy是代理类，method是被代理的方法，args是被代理方法的参数。此方法由代理类实现。

**Proxy类**

* protected Proxy(InvocationHandler h)
* public static Class<?> getProxyClass(ClassLoader loader, Class<?>... interfaces) throws IllegalArgumentException
* public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) throws IllegalArgumentException

动态代理其实是在运行时生成class，所以，必须为其提供一组interface，然后告诉他class已经实现了这些interface，而且在生成Proxy的时候，必须给他提供一个handler，让他来接管实际的工作。

``java
/**
 * 用于动态生成一个代理对象
 */
public class CreateProxy implements InvocationHandler {
    private Object target; // 被代理的对象

    // 用于创建代理对象的方法
    public Object create(Object target) {
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    /**
     * 代理对象要执行的方法
     * @param proxy 代理类对象
     * @param method 被代理对象的方法
     * @param args 被代理对象方法的参数
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("==> 开始代理 <==");
        Object invoke = method.invoke(target, args);
        System.out.println("==> 结束代理 <==");
        return invoke;
    }
}
``

``java
/**
 * 接口实现类
 */
public class Person implements Runnable {
    @Override
    public void run() {
        System.out.println("核心业务");
    }
}
``

``java
public class MainClass {
    public static void main(String[] args) {
        CreateProxy createProxy = new CreateProxy(); // 用来创建代理对象的对象
        Runnable person = new Person();
        Runnable proxy = (Runnable) createProxy.create(person);
        proxy.run(); // inovke
    }
}
``

``java
/**
 * 接口实现类
 */
public class Person implements Runnable, Consumer<String> {
    @Override
    public void run() {
        System.out.println("核心业务");
    }

    @Override
    public void accept(String s) {
        System.out.println("另一个核心业务:" + s);
    }
}
``

``java
public class MainClass {
    public static void main(String[] args) {
        CreateProxy createProxy = new CreateProxy(); // 用来创建代理对象的对象
        Person person = new Person();
        Object proxy = createProxy.create(person);
        Runnable runnableProxy = (Runnable) proxy;
        runnableProxy.run(); // inovke
        Consumer consumerProxy = (Consumer) proxy;
        consumerProxy.accept("核心业务");
    }
}
``

### 动态代理应用：AOP框架的简单实现

```java
public class Message implements Consumer<String> {
    private List<String> list = new ArrayList<>();
    @Override
    public void accept(String s) {
        list.add(s);
    }
}
```

```java
public interface Advice {
    void beforeAdvice();
    void afterAdvice();
}
```

```java
/**
 * 切面的实现类
 */
public class LogAdvice implements Advice {
    @Override
    public void beforeAdvice() {
        System.out.println("start time:" + System.currentTimeMillis());
    }

    @Override
    public void afterAdvice() {
        System.out.println("end time:" + System.currentTimeMillis());
    }
}
```

```properties
bean.target=Message
bean.advice=LogAdvice
bean=ProxyFactoryBean
```

```java
public class ProxyFactoryBean implements InvocationHandler {
    private Object target;
    private Advice advice;
    public Object getProxy() {
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        advice.beforeAdvice();
        Object obj = method.invoke(target, args);
        advice.afterAdvice();
        return obj;
    }
    public Object getTarget() {
        return target;
    }
    public void setTarget(Object target) {
        this.target = target;
    }
    public Advice getAdvice() {
        return advice;
    }
    public void setAdvice(Advice advice) {
        this.advice = advice;
    }
}
```

```java
public class BeanFactory {
    private Properties prop = new Properties();
    public BeanFactory(InputStream in) {
        try {
            prop.load(in);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public Object getBean(String name) {
        String className = prop.getProperty(name);
        Object bean = null;
        try {
            // 获取ProxyFactoryBean的class对象
            Class<?> aClass = Class.forName(className);
            bean = aClass.newInstance(); // 实例化对象
            // 根据配置文件实例化target和advice对象
            Object target = Class.forName(prop.getProperty(name.concat(".target"))).newInstance();
            Object advice = Class.forName(prop.getProperty(name.concat(".advice"))).newInstance();
            // 通过内省实现对ProxyFactoryBean的属性赋值
            BeanInfo beanInfo = Introspector.getBeanInfo(aClass);
            PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();
            for (PropertyDescriptor propertyDescriptor : propertyDescriptors) {
                String propertyName = propertyDescriptor.getName();
                Method writeMethod = propertyDescriptor.getWriteMethod();
                if ("target".equals(propertyName)) {
                    writeMethod.invoke(bean, target);
                } else if ("advice".equals(propertyName)) {
                    writeMethod.invoke(bean, advice);
                }
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IntrospectionException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
        return bean;
    }
}
```

```java
public class MainClass {
    public static void main(String[] args) {
        // 1. 读取配置文件
        InputStream in = ClassLoader.getSystemResourceAsStream("bean.properties");
        // 2. 创建bean的工厂对象
        BeanFactory beanFactory = new BeanFactory(in);
        // 3. 获取代理对象
        ProxyFactoryBean proxyFactoryBean = (ProxyFactoryBean) beanFactory.getBean("bean");
        Consumer<String> proxy = (Consumer<String>) proxyFactoryBean.getProxy();
        proxy.accept("Tom");
    }
}
```

```java
```