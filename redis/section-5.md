# Spring Boot 整合 Redis

## 基本操作

1. 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

> 自 Spring Boot 2.0 以后，Redis Client 默认实现由 Jedis 变为 lettuce。
>
> 关于两者之间的比较可以参考：[Jedis vs. Lettuce: An Exploration](https://redis.com/blog/jedis-vs-lettuce-an-exploration/)

2. 配置参数

```yaml
spring:
  redis:
    host: redis.local
    port: 6379
    database: 0
```

3. Redis Bean 参考

```java
@Bean
@ConditionalOnMissingBean(
    name = {"redisTemplate"}
)
@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate<Object, Object> template = new RedisTemplate();
    template.setConnectionFactory(redisConnectionFactory);
    return template;
}

@Bean
@ConditionalOnMissingBean
@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
    return new StringRedisTemplate(redisConnectionFactory);
}
```

4. 使用测试

```java
@SpringBootTest
class RedisApplicationTests {

	@Autowired
	private StringRedisTemplate redisTemplate;

	@Test
	void redisTest() {
		redisTemplate.opsForValue().set("name", "hello,world");
		System.out.println(redisTemplate.opsForValue().get("name"));
	}

}
```

## 存储对象

```java
User user = new User("bob", 18);
redisTemplate.opsForValue().set("user", user);
System.out.println(redisTemplate.opsForValue().get("user"));
```

```
org.springframework.data.redis.serializer.SerializationException: Cannot serialize; nested exception is org.springframework.core.serializer.support.SerializationFailedException: Failed to serialize object using DefaultSerializer; nested exception is java.lang.IllegalArgumentException: DefaultSerializer requires a Serializable payload but received an object of type [com.orkva.User]
```

配置对象解析

1. 使用 JDK 序列化

```java
public class User implements Serializable {
    private String name;
    private Integer age;
}
```

2. 在 RedisTemplate 中设置序列化方式

```java
@Bean
@Primary
public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
    redisTemplate.setConnectionFactory(redisConnectionFactory);

    Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL);
    jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

    StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
    redisTemplate.setKeySerializer(stringRedisSerializer);
    redisTemplate.setHashKeySerializer(stringRedisSerializer);
    redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
    redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
    redisTemplate.afterPropertiesSet();

    return redisTemplate;
}
```

当然也可以使用 Spring 提供的通用序列化工具

```java
@Bean
@Primary
public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
    redisTemplate.setConnectionFactory(redisConnectionFactory);
    redisTemplate.setDefaultSerializer(new GenericJackson2JsonRedisSerializer());
    StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
    redisTemplate.setKeySerializer(stringRedisSerializer);
    redisTemplate.setHashKeySerializer(stringRedisSerializer);
    redisTemplate.afterPropertiesSet();
    return redisTemplate;
}
```

绝大多数的情况下我们在 Java 中使用 Redis 方法时都会有成熟的工具类，并不会直接去使用 RedisTemplate 等工具，工具类的方法在各类博客都有详述，就不再赘述。
