1、前言
对于Long 类型的数据，如果我们在Controller层通过@ResponseBody将返回数据自动转换成json时，不做任何处理，而直接传给前端的话，在Long长度大于17位时会出现精度丢失的问题。

至于为啥丢失，我们在此处不探讨。

如图所示：后端返回数据如下：



 

而前端接收的数据时就丢失了精度



 

2、简单分析
首先，我们分析一下@ResponseBody是怎样将一个普通的对象转换成Json对象返回。

@responseBody注解的作用是将controller的方法返回的对象通过适当的转换器（默认使用MappingJackson2HttpMessageConverte（Spring 4.x以下使用的是MappingJackson2HttpMessageConverte））转换为指定的格式之后，写入到response对象的body区，需要注意的是，在使用此注解之后不会再走试图处理器，而是直接将数据写入到输入流中，他的效果等同于通过response对象输出指定格式的数据。

作用等同于response.getWriter.write(JSONObject.fromObject(user).toString());

3、怎么处理
总的来说主要有两种处理方式

如何避免精度丢失呢？最常用的办法就是待转化的字段统一转成String类型

那么怎样转化呢？

一般有两种方式：

首先我们要在maven中添加必须的依赖

复制代码
  <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.8.6</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.6</version>
        </dependency>
复制代码
方式一.

1、在待转化的字段之上加上@JsonSerialize(using=ToStringSerializer.class)注解，如图所示：

复制代码
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ProductVo {

    @JsonSerialize(using=ToStringSerializer.class)
    private Long productId

    private String productName;
    get,set省略

复制代码
Controller方法不需要特殊处理,但是使用这种时，如果需要转换的字段较多，就显得比较繁琐。

让我们看看效果



 

方法二.

所以，我们可以采用配置spring的消息转换器的ObjectMapper为自定义的类

复制代码
public class CustomObjectMapper extends ObjectMapper {

    public CustomObjectMapper() {
        super();
        SimpleModule simpleModule = new SimpleModule();
        simpleModule.addSerializer(Long.class, ToStringSerializer.instance);
        simpleModule.addSerializer(Long.TYPE, ToStringSerializer.instance);
        registerModule(simpleModule);
    }
}
复制代码
然后，我们还需要在SpringMVC的配置文件中加上如下配置

复制代码
<mvc:annotation-driven >
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg index="0" value="utf-8" />
                <property name="supportedMediaTypes">
                    <list>
                        <value>application/json;charset=UTF-8</value>
                        <value>text/plain;charset=UTF-8</value>
                    </list>
                </property>
            </bean>
           <-对日期进行转化的->
            <bean
                    class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="com.jay.jackson.util.CustomObjectMapper">
                        <property name="dateFormat">
                            <bean class="java.text.SimpleDateFormat">
                                <constructor-arg type="java.lang.String" value="yyyy-MM-dd HH:mm:ss" />
                            </bean>
                        </property>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
复制代码
 至此就大功告成了，Controller方法不需要特殊处理，代码如下：

复制代码
/**
     * 跳转到首页
     * @param request
     * @param response
     */
    @RequestMapping(value="/index", method = RequestMethod.GET)
    public String page(HttpServletRequest request, HttpServletResponse response){
        return "/index";
    }

    @RequestMapping(value = "/getProducts",method = RequestMethod.POST)
    @ResponseBody
    public List getProducts(HttpServletRequest request,HttpServletResponse response){
        List<ProductVo> productVos=new ArrayList<>();
        for (int i=1;i<=2;i++){
            ProductVo productVo= new ProductVo();
            productVo.setProductId(20170720125047233L+i);
            productVo.setProductName("测试商品"+i);
            productVos.add(productVo);
        }
        return productVos;
    }

    @RequestMapping(value = "/getUsers",method = RequestMethod.POST)
    @ResponseBody
    public List<UserVo> getUsers(){
        List<UserVo> userVos=new ArrayList<>();
        for (int i=1;i<=2;i++){
            UserVo userVo=new UserVo();
            userVo.setUserid((long)i);
            userVo.setUserName("测试用户"+i);
            userVo.setCreateTime(new Date());
            userVos.add(userVo);
        }
        return userVos;
    }
复制代码
 

 

我们来看效果。