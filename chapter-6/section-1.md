---
title: Spring
date: 2018-12-16 17:23:00
tags: 
categories: 
top:

---

![Spring Overview](../../shepherd-xie.github.io/source/_posts/assets/spring-overview.png)

<!-- more -->

[TOC]



# [Spring Framework Documentation Version 5.1.0.RELEASE](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)

# Core

## QuickStart

* applicationContext.xml
* BeanFactory/ApplicationContext
  * BeanFactory负责读取bean配置文档，管理bean的加载，实例化，维护bean之间的依赖关系，负责bean的声明周期。
  * ApplicationContext除了提供上述BeanFactory所能提供的功能之外，还提供了更完整的框架功能

## Bean

* `<bean id="..." class="..."/>`
* `scope` : singleton|prototype|request|session|application|websocket
* `lazy-init` : 默认支持singleton 不支持prototype
* 构造方法: 无参/有参
* 静态工厂: `<bean id="..." class="..." factory-method="..."/>`
* 实例工厂: `<bean id="..." factory-bean="..." factory-method="..."/>`

## Dependency injection - 依赖注入

* property: `<property name="..." value|ref="..."/>`
* constructor: `<constructor-arg index|name|type="..." value|ref="..."/>`
* p-namespace(property): `<bean id="..." class="..." p:driverClassName="..." p:url="..." p:username="..." p:password="..."/>`
* c-namespace(constructor): `<bean id="..." class="..." c:_0="..." c:_1-ref="..." c:name-ref="..." c:email="..."/>`
* EL(Expression Language): `<property name="..." value="#{<expression>}"/>`
  * `#` 是spring提供的EL表达式，用于获取对象的属性与方法
  * `$` 用于获取引入的properties文件中key的对应变量的值
* 复合类型: `<property name="dataSource.driverClassName" value|ref="..."/>`

## Annotation

* @Component|@Repository|@Service|@Controller
* @Autowired|@Inject|@Resource|@Value
* @Scope
* @Required|@Primary|@Qualifier
* @PostConstruct|@PreDestroy
* @ComponentScan
* JUnit4
  * @RunWith(SpringJunit4ClassRunner.class)
  * @ContextConfiguration

## `context:annotation-config`

在基于主机方式配置Spring时,Spring配置文件applicationContext.xml,你可能会见 `<context:annotation-config/>` 这样一条配置，它的作用是隐式的向Spring容器注册

* `AutowiredAnnotationBeanPostProcessor`
* `CommonAnnotationBeanPostProcessor`
* `PersistenceAnnotationBeanPostProcessor`
* `RequiredAnnotationBeanPostProcessor `

 这4个`BeanPostProcessor`。注册这4个bean处理器主要的作用是为了你的系统能够识别相应的注解。

1. 使用`@Autowired`注解，需声明`AutowiredAnnotationBeanPostProcessor`。
2. 使用`@PersistenceContext`注解，需声明`PersistenceAnnotationBeanPostProcessor`。
3. 使用`@Required`注解，需声明`RequiredAnnotationBeanPostProcessor`。
4. 使用`@Resource`、`@PostConstruct`、`@PreDestroy`等注解须声明`CommonAnnotationBeanPostProcessor`。

## `context:component-scan`

它的`base-package`属性指定了需要扫描的类包，类包及其递归子包中所有的类都会被处理。

> 使用 `<context：component-scan>` 隐式启用 `<context：annotation-config>` 的功能。使用 `<context：component-scan>` 时，通常不需要包含 `<context：annotation-config>` 元素。

