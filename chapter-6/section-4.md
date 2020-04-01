# Web

## QuickStart

* ```xml
  <!-- web.xml -->
  <servlet>
      <servlet-name>dispatcherServlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:dispatcherServlet.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
  </servlet>
  
  <servlet-mapping>
      <servlet-name>dispatcherServlet</servlet-name>
      <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```

* ```xml
  <!-- dispatcherServlet.xml -->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      <property name="prefix" value="/WEB-INF/jsp/"/>
      <property name="suffix" value=".jsp"/>
  </bean>
  ```

* ```xml
  <!-- pom.xml -->
  <!-- 编译插件 -->
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.7.0</version>
      <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
      </configuration>
  </plugin>
  <!-- Tomcat插件 -->
  <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
      <configuration>
          <port>8080</port>
          <path>/spring-mvc</path>
          <uriEncoding>UTF-8</uriEncoding>
          <warSourceDirectory>${basedir}/target/${project.artifactId}</warSourceDirectory>
      </configuration>
  </plugin>
  ```

## Annotation

* `@RequestMapping`

  * `@GetMapping`,`@PostMapping`,`@PutMapping`,`@DeleteMapping`,`@PatchMapping`
  * glob 模式和通配符映射请求,使用`@PathVariable`获取
    * `?` 匹配一个字符
    * `*` 匹配路径段中的零个或多个字符
    * `**` 匹配零个或多个路径段
  * `consumes`: 根据请求缩小请求映射范围`Content-Type`
    * 该`consumes`属性还支持否定表达式 - 例如，`!text/plain`表示除了以外的任何内容类型`text/plain`。
    * `MediaType`提供常用媒体类型的常量，例如 `APPLICATION_JSON_VALUE`和`APPLICATION_XML_VALUE`。
  * `produces`: 根据`Accept`请求标头和控制器方法生成的内容类型列表来缩小请求映射
    * 媒体类型可以指定字符集。支持否定表达式
  * `params`: 根据请求参数条件缩小请求映射
  * `headers`: 根据请求头条件缩小请求映射

* `@PathVariable`

  * 使用`{varName:regex}`进行正则匹配

* `@RequestParam`，`@RequestHeader`，`@RequestAttribute`

  * `@RequestParam` 用于获取从表单传递的参数
  * `@RequestAttribute` 用于获取请求转发时设置的属性

* `@SessionAttributes`,`@SessionAttribute`,`@CookieValue`

* POST乱码

  ```xml
  <!-- web.xml -->
  <filter>
      <filter-name>characterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
      </init-param>
      <init-param>
          <param-name>forceEncoding</param-name>
          <param-value>true</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>characterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

* GET乱码`<uriEncoding>UTF-8</uriEncoding>`

## RESTful

[**表现层状态转换**](https://zh.wikipedia.org/wiki/%E8%A1%A8%E7%8E%B0%E5%B1%82%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2)（REST，英文：Representational State Transfer）是Roy Thomas Fielding博士于2000年在他的博士论文中提出来的一种*万维网软件架构*风格，目的是便于不同软件/程序在网络（例如互联网）中互相传递信息。



需要注意的是，REST是设计风格而不是标准。REST通常基于使用HTTP，URI，和XML以及HTML这些现有的广泛流行的协议和标准。

* 资源是由URI来指定。
* 对资源的操作包括获取、创建、修改和删除资源，这些操作正好对应HTTP协议提供的GET、POST、PUT和DELETE方法。
* 通过操作资源的表现形式来操作资源。
* 资源的表现形式则是XML或者HTML，取决于读者是机器还是人，是消费web服务的客户软件还是web浏览器。当然也可以是任何其他的格式。

浏览器 form 表单只支持 GET 与 POST 请求，而 DELETE、PUT 等 method 并不支持，spring3.0 添加了一个过滤器，可以将这些请求转换为标准的 http 方法，使得支持 GET、POST、PUT 与 DELETE 请求，该过滤器为`HiddenHttpMethodFilter`。

`HiddenHttpMethodFilter` 只对 POST 请求的表单进行过滤，在表单内部添加 `name="_method"` 进行类型声明。

```xml
<form action="..." method="POST">
	<input type="hidden" name="_method" value="DELETE"/>
	......
</form>
```

## 排除静态资源

* 使用`<mvc:default-servlet-handler/>`指定默认处理器处理静态资源

  * 单独使用会让所有访问都进行默认处理，导致`@RequestMapping`注解失效，需要配合`<mvc:annotation-driven/>`使用

* 使用`<mvc:resources/>`指定静态资源匹配规则

  ```xml
  <mvc:resources mapping="/resources/**"
      location="/public, classpath:/static/"
      cache-period="31556926" />
  ```

* 在`web.xml`进行配置默认servlet

  ```xml
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.css</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.gif</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.mp4</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.png</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.jpg</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.js</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.html</url-pattern>
  </servlet-mapping>
  ```

## `url-pattern`

* `url-pattern` 中 `/` 与 `/*` 的区别，参考[Difference between / and /* in servlet mapping url pattern](https://stackoverflow.com/questions/4140448/difference-between-and-in-servlet-mapping-url-pattern)
  * `/` 表示匹配所有路径，例如 `/login` ，它覆盖容器的默认Servlet的映射
  * `/*` 表示匹配所有的路径及资源，例如 `/login` ， `*.jsp`

## `mvc:annotation-driven`

* `<mvc:annotation-driven/>` 是一种简写形式，其声明会自动注册 `DefaultAnnotationHandlerMapping`和`AnnotationMethodHandlerAdapter` 两个Bean，是Spring MVC为`@Controllers`分发请求所必需的
  * 同时它还提供了数据绑定支持，`@NumberFormatannotation`支持，`@DateTimeFormat`支持，`@Valid`支持，读写XML的支持（JAXB），读写JSON的支持（Jackson）
  * 使用其子标签`<mvc:path-matching>`为Spring MVC提供路径匹配模式，默认情况下`/index`和`/index.xxx`都可以到`@RequestMapping("/index")`，使用`<mvc:path-matching suffix-pattern="false"/>`强制进行路径匹配

> `DefaultAnnotationHandlerMapping`和`AnnotationMethodHandlerAdapter` 在Spring 3.2版本中已声明弃用，在Spring 3.2 及以上版本中建议使用 `RequestMappingHandlerMapping` 与 `RequestMappingHandlerAdapter`

## 类型转换

* 默认情况下Spring MVC会使用格式化程序将请求数据转换为对象，Spring MVC内部已经实现了 `Number` 与 `Date` 类型的转换规则，需要使用时可以在字段上添加 `@NumberFormat` 和 `@DateTimeFormat` 注解

  * 同时它也支持自定义格式化程序和转换器，实现 `Converter` 接口，并在 `SpringConfig.xml` 中进行配置

    ```xml
    <mvc:annotation-driven conversion-service="conversionService"/>
    
    <bean id="conversionService"
          class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="org.example.MyConverter"/>
            </set>
        </property>
        <property name="formatters">
            <set>
                <bean class="org.example.MyFormatter"/>
                <bean class="org.example.MyAnnotationFormatterFactory"/>
            </set>
        </property>
        <property name="formatterRegistrars">
            <set>
                <bean class="org.example.MyFormatterRegistrar"/>
            </set>
        </property>
    </bean>
    ```

## JSON请求

* 使用`@RequestBody`传递请求参数
* 使用`@ResponseBody`返回处理结果

## 异常处理

* `@ExceptionHandler`

  * 结合`@ControllerAdvice`使用

  > 参考[@ExceptionHandler方法](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-exceptionhandler)

## 拦截器

所有`HandlerMapping`实现都支持处理程序拦截器，这些拦截器在您要将特定功能应用于某些请求时很有用 - 例如，检查主体。拦截器必须`HandlerInterceptor`从 `org.springframework.web.servlet`包中实现三种方法，这些方法应该提供足够的灵活性来进行各种预处理和后处理：

* `preHandle(..)`：在执行实际处理程序之前
* `postHandle(..)`：处理程序执行后
* `afterCompletion(..)`：完成请求完成后

该`preHandle(..)`方法返回一个布尔值。您可以使用此方法来中断或继续执行链的处理。当此方法返回时`true`，处理程序执行链继续。当它返回false时，`DispatcherServlet` 假定拦截器本身已处理请求（并且，例如，呈现适当的视图）并且不继续执行执行链中的其他拦截器和实际处理程序。

见[拦截](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-config-interceptors)在MVC配置一节的如何配置拦截器的实例。您也可以通过在各个`HandlerMapping`实现上使用setter直接注册它们 。

请注意，`postHandle`对于在之前和之前 编写和提交响应的方法`@ResponseBody`和`ResponseEntity`方法不太有用。这意味着对响应进行任何更改都为时已晚，例如添加额外的标头。对于此类方案，您可以实现并将其声明为[Controller Advice](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-controller-advice) bean或直接对其进行配置 。`HandlerAdapter` `postHandle` `ResponseBodyAdvice` `RequestMappingHandlerAdapter`

## 文件上传

* ```xml
  <!-- pom.xml -->
  <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.2</version>
  </dependency>
  ```

* ```xml
  <!-- SpringConfig.xml -->
  <!-- 必须配置id -->
  <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
      <!--默认 ISO-8859-1-->
      <property name="defaultEncoding" value="UTF-8"/>
      <property name="maxInMemorySize" value="10240"/>
      <property name="uploadTempDir" value="/upload/"/>
      <property name="maxUploadSize" value="-1"/>
  </bean>
  ```

* ```jsp
  <form action="${pageContext.request.contextPath}/fileupload" method="post" enctype="multipart/form-data">
      <input type="file" name="file">
      ...
  </form>
  ```

* ```java
  @PostMapping("/form")
  public String handleFormUpload(@RequestParam("name") String name,
                                 @RequestParam("file") MultipartFile file) {
  
      if (!file.isEmpty()) {
          byte[] bytes = file.getBytes();
          // store the bytes somewhere
          return "redirect:uploadSuccess";
      }
  
      return "redirect:uploadFailure";
  }
  ```
