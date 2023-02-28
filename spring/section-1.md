# Spring 重新开始

## IoC 概念及原理

IoC 全称为 **控制反转(Inversion of Control)** ，所谓的控制反转，就是讲对象的创建与对象间调用过程委托给第三方进行管理，从而降低系统间的耦合度。

IoC 底层本质是基于：xml解析，工厂模式，反射。

1. 声明创建对象的 xml 配置文件

```xml
<bean id="userService" class="com.orkva.service.UserService"></bean>
```

2. 创建工厂类

```java
class BeanFactory {
    public UserService getUserService() {
        String classValue = xml.bean.class; // xml 解析
        Class clazz = Class.forName(classValue);
        return (UserService) clazz.newInstance(); // 反射创建对象
    }
}
```

## Spring 实现 IoC

1. 声明要托管至 Spring 的类

```java
public class UserService {
    public void sayHi() {
        System.out.println("hello world");
    }
}
```

2. 配置 xml 文件声明 Bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="userService" class="com.orkva.service.UserService">
    </bean>
</beans>
```

3. 使用 Spring 实例化 Bean

```java
public class Application {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-context.xml");
        UserService userService = context.getBean(UserService.class);
        userService.sayHi();
    }
}
```

Spring 提供 IoC 容器实现的两种方式：

- BeanFactory：IoC 容器的基本实现，是 Spring 内部使用的接口
  - 加载配置文件时不会创建对象，在获取对象时才创建
- ApplicationContext：BeanFactory 的子接口，提供更多更强大的功能
  - 加载配置文件时就会创建对象

ApplicationContext 的实现类

![image-20230222112601845](https://images.orkva.com/images/2023/02/22/image-20230222112601845.png)
