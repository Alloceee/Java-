## 常用注解

| 注解                                 | 描述                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| @EnableAutoConfiguration             | SpringBoot自动配置（auto-configuration）：尝试根据你添加的Jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上类选择自动配置。如果发现应用了你不下纲要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用他们。 |
| @ComponentScan                       |                                                              |
| @Configuration                       |                                                              |
| @EnableCaching                       | 注解是spring framework中的注解驱动的缓存管理功能。自spring版本3.1起加入了该注解。如果你使用了这个注解，那么你就不需要在XML文件中配置cache manager了，等价于 \<cache:annotation-driven/> 。能够在服务类方法上标注@Cacheable |
| @Import                              |                                                              |
| @ImportResource                      |                                                              |
| @Resource(name = “name”,type=“type”) |                                                              |
| @Value                               |                                                              |
| @Bean                                |                                                              |
| @Component                           |                                                              |
| @Service                             |                                                              |
| @Repository                          |                                                              |
| @AutoWired                           |                                                              |
| @Inject                              |                                                              |
| @Qualifier                           |                                                              |





## SpringBoot有哪些优点

- 减少开发、测试时间
- 使用javaConfig有助于避免使用xml
- 避免大量的maven导入和各种版本的冲突

### 特点：
创建独立的Spring应用程序
嵌入的Tomcat，无需部署WAR文件
简化Maven配置
自动配置Spring
提供生产就绪型功能，如指标，健康检查和外部配置
绝对没有代码生成和对XML没有要求配置

## 异常处理

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(value = RuntimeException.class)
    @ResponseBody
    public Object defaultErrorHandler(HttpServletRequest req, Exception e) throws Exception {
        e.printStackTrace();   
        return "我是个异常处理类";
    }
    //404处理
    @Bean
    public WebServerFactoryCustomizer<ConfigurableWebServerFactory> webServerFactoryCustomizer(){
        return (factory->{
            ErrorPage error404Page = new ErrorPage(HttpStatus.NOT_FOUND, "/404.do");
            factory.addErrorPages( error404Page);
        });
    }
}
```

controller

```java
@RestController
public class BaseController {

    @RequestMapping("/404.do")
    public Object error_404() {
        return "你要找的页面，被lison偷吃了！";
    }
}
```

注意：WebServerFactoryCustomizer这种配置方式是在SpringBoot2之后才这样配置的，在1.X的版本需要用到的是EmbeddedServletContainerCustomize

```java
  @Bean
   public EmbeddedServletContainerCustomizer containerCustomizer() {
       return (container -> {
           ErrorPage error404Page = new ErrorPage(HttpStatus.NOT_FOUND, "/404.do");
           container.addErrorPages( error404Page);
        });
   }
```



## SpringBoot添加拦截器

### 1.创建注解

```java
/**
 * 用于验证权限
 * @author AlmostLover
 */
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Authority {
}
```

### 2.创建拦截器(判断用户是否已经登录)

```java
/**
 * @author AlmostLover
 */
public class AuthorityInterceptor extends HandlerInterceptorAdapter {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //flag变量用于判断用户是否登录，默认为false
        boolean flag = false;
        //拦截请求
        //1.获取session中的用户
        TdUser user = (TdUser) request.getSession().getAttribute("user");
        //2.判断用户是否已经登录
        if (user == null) {
            //如果用户没有登录，则设置提示信息，跳转到登录界面
            request.setAttribute("message", "请先登录再访问网站");
            response.sendRedirect("/login");
        } else {
            //如果用户已经登录，则验证通过，放行
            flag = true;
        }
        return flag;
    }
}
```

### 3.编写配置类

```java
/**
 * 和springmvc的webmvc拦截配置一样
 *
 * @author Lunacy
 */
@Configuration
public class WebConfigurer implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authorityInterceptor())
                .addPathPatterns("/*")
                .excludePathPatterns("/login");
    }

    @Bean
    public AuthorityInterceptor authorityInterceptor() {
        return new AuthorityInterceptor();
    }
}
```

> 一定要加@Configuration  这个注解，在启动的时候才会被加载





## SringBoot添加过滤器

### 1.过滤器类

```java
/**
 * 用于过滤群聊消息中的敏感字符
 *
 * @author AlmostLover
 */
@WebFilter(urlPatterns = "/message/send", filterName = "MessageFilter")
public class MessageFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("用于过滤敏感字");
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {

    }
}
```

### 2.配置Configuration

```java
@Configuration
@ConditionalOnWebApplication
public class WebConfig implements WebMvcConfigurer {
    /**
     * 配置过滤器
     */
    @Bean
    public FilterRegistrationBean<MessageFilter> registrationBean(){
        FilterRegistrationBean<MessageFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new MessageFilter());
        registrationBean.setOrder(1);
        return registrationBean;
    }
}
```







## SpringBoot中的监视器

SpringBoot actuator是spring启动框架中的重要功能之一。SpringBoot监视器可帮助您访问生产环境中正在运行的应用程序的当前状态。有几个指标必须在生产环境中进行检查和监控。即使一些外部应用程序可能正在使用这些服务来向相关人员触发警告信息。监视器模块公开了一组可以直接作为HTTP URL访问的REST端点来检查状态







## AOP切面编程



1. pom.xml

```xml
 <!--  切面编程    -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

2. class类

```java
@Aspect
@Component
public class LogAspect {
    private static final Logger LOGGER = LoggerFactory.getLogger(LogAspect.class);
    @Pointcut("execution(public * com.springboot.demo.controller.*.*(..))")
    public void webLog(){}

    @Before("webLog()")
    public void doBefore(JoinPoint joinPoint){
        //接收到请求，记录请求内容
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        //记录下请求内容
        LOGGER.info("URL : "+request.getRequestURL().toString());
        LOGGER.info("IP : "+request.getRemoteAddr());
        LOGGER.info("请求类方法 : "+joinPoint.getSignature());
        LOGGER.info("请求类方法参数 : "+ Arrays.toString(joinPoint.getArgs()));
        LOGGER.info("==============================");
    }

    @AfterReturning(returning = "ret" , pointcut = "webLog()")
    public void doAfterReturning(Object ret){
        //处理完请求，返回内容
        LOGGER.info("REQUEST : "+ret);
    }
}
```

| 注解                                                       | 作用                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| @Aspect                                                    | 标注这个类是切面编程的类                                     |
| @Component                                                 | 将该类添加到spring容器中交给spring管理                       |
| @Pointcut()                                                | @Pointcut("execution(public * com.springboot.demo.controller.*.*(..))") |
| @Before("webLog()")                                        |                                                              |
| @AfterReturning(returning = "ret" , pointcut = "webLog()") |                                                              |





## 整合MyBatis

1. pom.xml

```xml
<!--myBaits起步依赖-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.1.1</version>
        </dependency>
        <!--MYSQL连接驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```

2. application.properties

```properties
#DB Configuration
spring.datasource.driveClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/chat_room?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=ljwlloveyou980

#spring集成Mybatis环境
#pojo别名扫描包
mybatis.type-aliases-package=com.chat.demo.entity
#加载Mybatis映射文件
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
```

> 新版本连接数据库要添加时区serverTimezone=UTC

3. 编写PO类和mapper类





4. 开启二级缓存

它的默认配置就是启用二级缓存的，所以这个不用我们操心。我们需要做的是在mapper.xml文件里添加二级缓存的属性配置：

> <cache />

加上这个标签，二级缓存就会启用，它的默认属性如下：

> - 映射语句文件中的所有 select 语句将会被缓存。
> - 映射语句文件中的所有 insert,update 和 delete 语句会刷新缓存。
> - 缓存会使用 Least Recently Used(LRU,最近最少使用的)算法来收回。
> - 根据时间表(比如 no Flush Interval,没有刷新间隔), 缓存不会以任何时间顺序来刷新。
> - 缓存会存储列表集合或对象(无论查询方法返回什么)的 1024 个引用。
> - 缓存会被视为是 read/write(可读/可写)的缓存,意味着对象检索不是共享的,而且可以安全地被调用者修改,而不干扰其他调用者或线程所做的潜在修改。



## 整合Redis

随着Spring Boot2.x的到来，支持的组件越来越丰富，也越来越成熟，其中对Redis的支持不仅仅是丰富了它的API，更是替换掉底层Jedis的依赖，取而代之换成了Lettuce(生菜)

Lettuce和Jedis都是连接Redis Server的客户端程序。Jedis在实现上是直连redis server，多线程环境下非线程安全，除非使用线程池，为每个Jedis实例添加物理连接。Lettuce基于Netty的连接实例（StatefulRedisConnection），可以在多个线程间并发访问，且线程安全，满足多线程环境下的并发访问，同时它是可伸缩的设计，一个而连接实例不够的情况也可以按需求添加连接实例。

#### 1.pom.xml

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

#### 2.application.properties

```properties
# Redis配置
spring.redis.host=localhost
spring.redis.port=6380
spring.redis.password=12345
# redis超时时间（毫秒），如果不设置，取默认值2000
spring.redis.timeout=10000
# 最大空闲数
spring.redis.lettuce.pool.max-idle=300
# 连接池最大数据库连接数
spring.redis.lettuce.pool.max-active=600
# 最大建立连接等待时间。如果超过此事件将接到异常，设-1表示无限制
spring.redis.lettuce.pool.max-wait=1000
#连接的最小空闲连接数
spring.redis.lettuce.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.lettuce.shutdown-timeout=300
```

#### 3.config

```java
/**
 * redis配置类
 * @program: springbootdemo
 * @Date: 2019/2/22 15:20
 * @Author: zjjlive
 * @Description:
 */
@Configuration
@EnableCaching //开启注解
public class RedisConfig extends CachingConfigurerSupport {

    /**
     * retemplate相关配置
     * @param factory
     * @return
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {

        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(factory);

        //使用Jackson2JsonRedisSerializer来序列化和反序列化redis的value值（默认使用JDK的序列化方式）
        Jackson2JsonRedisSerializer jacksonSeial = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper om = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会跑出异常
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jacksonSeial.setObjectMapper(om);

        // 值采用json序列化
        template.setValueSerializer(jacksonSeial);
        //使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());

        // 设置hash key 和value序列化模式
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(jacksonSeial);
        template.afterPropertiesSet();

        return template;
    }

    /**
     * 对hash类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public HashOperations<String, String, Object> hashOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForHash();
    }

    /**
     * 对redis字符串类型数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ValueOperations<String, Object> valueOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForValue();
    }

    /**
     * 对链表类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ListOperations<String, Object> listOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForList();
    }

    /**
     * 对无序集合类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public SetOperations<String, Object> setOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForSet();
    }

    /**
     * 对有序集合类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ZSetOperations<String, Object> zSetOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForZSet();
    }

}

```

#### 4.封装Redis工具类

```java
/**
 * @author AlmostLover
 */
@Component
public class RedisUtil {
    @Autowired
    private RedisTemplate<String,Object> redisTemplate;

    public RedisUtil(RedisTemplate<String ,Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    /**
     * 指定缓存失效时间
     * @param key
     * @param time
     * @return
     */
    public boolean expire(String key,long time){
        try {
            if(time>0){
                redisTemplate.expire(key,time, TimeUnit.SECONDS);
            }
            return true;
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     *
     * @param key
     * @return
     */
    public long getExpire(String key){
        return redisTemplate.getExpire(key,TimeUnit.SECONDS);
    }

    /**
     * 判断key是否存在
     * @param key
     * @return
     */
    public boolean hasKey(String key){
        try {
            return  redisTemplate.hasKey(key);
        }catch (Exception e){
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String ...key){
        if(key!=null&&key.length>0){
            if(key.length==1){
                redisTemplate.delete(key[0]);
            }else{
                redisTemplate.delete(CollectionUtils.arrayToList(key));
            }
        }
    }
    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public Object get(String key){
        return key==null?null:redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key 键
     * @param value 值
     * @return true成功 false失败
     */
    public boolean set(String key,Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 普通缓存放入并设置时间
     * @param key 键
     * @param value 值
     * @param time 时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */
    public boolean set(String key,Object value,long time){
        try {
            if(time>0){
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            }else{
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 递增
     * @param key 键
     * @param delta 要增加几(大于0)
     * @return
     */
    public long incr(String key, long delta){
        if(delta<0){
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }

    /**
     * 递减
     * @param key 键
     * @param delta 要减少几(小于0)
     * @return
     */
    public long decr(String key, long delta){
        if(delta<0){
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }

    //================================Map=================================
    /**
     * HashGet
     * @param key 键 不能为null
     * @param item 项 不能为null
     * @return 值
     */
    public Object hget(String key,String item){
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object,Object> hmget(String key){
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     * @return true 成功 false 失败
     */
    public boolean hmset(String key, Map<String,Object> map){
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * HashSet 并设置时间
     * @param key 键
     * @param map 对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String,Object> map, long time){
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if(time>0){
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     * @param key 键
     * @param item 项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key,String item,Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     * @param key 键
     * @param item 项
     * @param value 值
     * @param time 时间(秒)  注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key,String item,Object value,long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if(time>0){
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除hash表中的值
     * @param key 键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item){
        redisTemplate.opsForHash().delete(key,item);
    }

    /**
     * 判断hash表中是否有该项的值
     * @param key 键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item){
        return redisTemplate.opsForHash().hasKey(key, item);
    }

    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     * @param key 键
     * @param item 项
     * @param by 要增加几(大于0)
     * @return
     */
    public double hincr(String key, String item,double by){
        return redisTemplate.opsForHash().increment(key, item, by);
    }

    /**
     * hash递减
     * @param key 键
     * @param item 项
     * @param by 要减少记(小于0)
     * @return
     */
    public double hdecr(String key, String item,double by){
        return redisTemplate.opsForHash().increment(key, item,-by);
    }

    //============================set=============================
    /**
     * 根据key获取Set中的所有值
     * @param key 键
     * @return
     */
    public Set<Object> sGet(String key){
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 根据value从一个set中查询,是否存在
     * @param key 键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key,Object value){
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将数据放入set缓存
     * @param key 键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object...values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 将set数据放入缓存
     * @param key 键
     * @param time 时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key,long time,Object...values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if(time>0) {
                expire(key, time);
            }
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 获取set缓存的长度
     * @param key 键
     * @return
     */
    public long sGetSetSize(String key){
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 移除值为value的
     * @param key 键
     * @param values 值 可以是多个
     * @return 移除的个数
     */
    public long setRemove(String key, Object ...values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    //===============================list=================================

    /**
     * 获取list缓存的内容
     * @param key 键
     * @param start 开始
     * @param end 结束  0 到 -1代表所有值
     * @return
     */
    public List<Object> lGet(String key, long start, long end){
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 获取list缓存的长度
     * @param key 键
     * @return
     */
    public long lGetListSize(String key){
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 通过索引 获取list中的值
     * @param key 键
     * @param index 索引  index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     * @return
     */
    public Object lGetIndex(String key,long index){
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @param time 时间(秒)
     * @return
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @param time 时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据索引修改list中的某条数据
     * @param key 键
     * @param index 索引
     * @param value 值
     * @return
     */
    public boolean lUpdateIndex(String key, long index,Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 移除N个值为value
     * @param key 键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */
    public long lRemove(String key,long count,Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


}
```

#### 5.控制器类操作redis数据库

```java
@Controller
public class RedisController {
    @Resource
    private RedisUtil redisUtil;

    @GetMapping("/set")
    @ResponseBody
    public String set() {
        boolean b = redisUtil.set("username",12345);
        return b+"";
    }

    @GetMapping("/get")
    @ResponseBody
    public String get(){
        String username = (String) redisUtil.get("username");
        return username;
    }
}
```

## 整合JUnit4

```java
@SpringBootTest(classes = {App.class})
@RunWith(SpringRunner.class)
public class UserTest {

    @Resource
    private UsersMapper usersMapper;

    @Test
    public void testAdd() {
        Users user = new Users() ;
        user.setPasswd("123");
        user.setUsername("enjoy");
        usersMapper.insertSelective(user);
    }

    @Test
    public void testFindUser() {
        Users enjoy = usersMapper.findByUsernameAndPasswd("enjoy", "123");
        System.out.println(enjoy);
    }

}
```





## 整合thymeleaf

#### 1.pom.xml

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```



#### 2.application.properties

```properties
# thymeleaf配置
spring.thymeleaf.prefix=classpath:/templates
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
# 热部署文件，页面不产生缓存，及时更新
spring.thymeleaf.cache=false
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```



## 整合SpringData JPA

#### 1.idea创建工程时，勾选SpringData JPA模板

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
 </dependency>
<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
</dependency>
```

#### 2.application.properties

省略数据库配置

```properties
# spring Data JPA 配置
spring.jpa.database=MySQL
spring.jpa.show-sql=true
spring.jpa.generate-ddl=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.hibernate.naming-strategy=org.hibernate.cfg.ImprovedNamingStrategy
```

#### 3.User.java

```java
@Entity(name = "User")
@Table(name = "user")
public class User implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
    private String username;
    private String password;
    //省略getter/setter
}
```



#### 4.Repository.java

```java
public interface IndexRepository extends JpaRepository<User, Long> {

}
```



## SpringBoot开启热部署

### 1.pom.xml

```xml
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
```



```xml
		 <plugin>
                <!--热部署配置-->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <!--fork:如果没有该项配置,整个devtools不会起作用-->
                    <fork>true</fork>
                </configuration>
            </plugin>
```



## JSR303参数校验

1. pom.xml

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

#### 常用注解

### 4.1、空检查

```
@Null 验证对象是否为null
@NotNull 验证对象是否不为null, 无法查检长度为0的字符串
@NotBlank 检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
@NotEmpty 检查约束元素是否为NULL或者是EMPTY.
```

### 4.2、Booelan检查

```
@AssertTrue 验证 Boolean 对象是否为 true
@AssertFalse 验证 Boolean 对象是否为 false
```

### 4.3、长度检查

```
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内
@Length(min=, max=) Validates that the annotated string is between min and max included.
```

### 4.4、日期检查

```
@Past 验证 Date 和 Calendar 对象是否在当前时间之前，验证成立的话被注释的元素一定是一个过去的日期
@Future 验证 Date 和 Calendar 对象是否在当前时间之后 ，验证成立的话被注释的元素一定是一个将来的日期
@Pattern 验证 String 对象是否符合正则表达式的规则，被注释的元素符合制定的正则表达式，regexp:正则表达式 flags: 指定 Pattern.Flag 的数组，表示正则表达式的相关选项。
```

### 4.5、数值检查

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
建议使用在Stirng,Integer类型，不建议使用在int类型上，因为表单值为“”时无法转换为int，但可以转换为Stirng为”“,Integer为null
@Min 验证 Number 和 String 对象是否大等于指定的值
@Max 验证 Number 和 String 对象是否小等于指定的值
@DecimalMax 被标注的值必须不大于约束中指定的最大值. 这个约束的参数是一个通过BigDecimal定义的最大值的字符串表示.小数存在精度
@DecimalMin 被标注的值必须不小于约束中指定的最小值. 这个约束的参数是一个通过BigDecimal定义的最小值的字符串表示.小数存在精度
@Digits 验证 Number 和 String 的构成是否合法
@Digits(integer=,fraction=) 验证字符串是否是符合指定格式的数字，interger指定整数精度，fraction指定小数精度。
@Range(min=, max=) 被指定的元素必须在合适的范围内
@Range(min=10000,max=50000,message=”range.bean.wage”)
@Valid 递归的对关联对象进行校验, 如果关联对象是个集合或者数组,那么对其中的元素进行递归校验,如果是一个map,则对其中的值部分进行校验.(是否进行递归验证)
@CreditCardNumber信用卡验证
@Email 验证是否是邮件地址，如果为null,不进行验证，算通过验证。
@ScriptAssert(lang= ,script=, alias=)
@URL(protocol=,host=, port=,regexp=, flags=)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

## 5、案例分析

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```java
//5.1、实体 Bean
public class ValidateTestClass{
 
    @NotNull(message = "reason信息不可以为空")
    private String reason;//订单取消原因
 
    //get、set方法、有参构造方法、无参构造方法、toString方法省略
  ......
}
```



```java
//5.2、control
public class ValidateTestClassValidateTest{
 
   @RequestBody
   @RequestMapping(value = "/test")
   public void addBook(@Valid ValidateTestClass validateTestClass，BindingResult result) {
 
       //备注：这里一个@Valid的参数后必须紧挨着一个BindingResult 参数，否则spring会在校验不通过时直接抛出异常
       if (result.hasErrors()){
            List<ObjectError> errorList = result.getAllErrors();
            for(ObjectError error : errorList){
                System.out.println(error.getDefaultMessage());
            }
        }
 
    }
}
```



## 注意

配置类只能写一个，

否则其他的不会执行



# 面试

### SpringBoot启动原理

springboot采用maven工程，在pom.xml文件中继承了spring-boot-starter-parent，他继承了spring-boot-dependencies，集成了spring的所有依赖

springboot有一个启动类

```java
@SpringBootApplication
public class OaApplication {

    public static void main(String[] args) {
        SpringApplication.run(OaApplication.class, args);
    }

}
```

@SpringBootApplication中

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
}
```

@EnableAutoConfiguration中

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)//将这个类交给spring去管理
public @interface EnableAutoConfiguration {
}
```

AutoConfigurationImportSelector.java中可以看到自动配置的类在**META-INF/spring.factories**中

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
}
```

![1567494100491](C:\Users\AlmostLover\Desktop\MarkDown笔记\Spring\1567494100491.png)

打开spring.factories

```
org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\  //嵌入式的tomcat
```

```
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
```



##### 怎么整合第三方依赖

父集成

##### 怎么做到无配置集成springmvc

使用@EnableWebMvc集成springmvc的功能

##### Tomcat怎么启动

```
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
```

```java
@ConditionalOnClass({ Tomcat.class, UpgradeProtocol.class })
```

嵌入式的Tomcat

## **SpringBoot核心功能**

1、独立运行Spring项目
Spring boot 可以以jar包形式独立运行，运行一个Spring Boot项目只需要通过java -jar xx.jar来运行。

2、内嵌servlet容器
Spring Boot可以选择内嵌Tomcat、jetty或者Undertow,这样我们无须以war包形式部署项目。
3、提供starter简化Maven配置
spring提供了一系列的start pom来简化Maven的依赖加载，例如，当你使用了spring-boot-starter-web，会自动加入如图5-1所示的依赖包。
4、自动装配Spring 
SpringBoot会根据在类路径中的jar包，类、为jar包里面的类自动配置Bean，这样会极大地减少我们要使用的配置。当然，SpringBoot只考虑大多数的开发场景，并不是所有的场景，若在实际开发中我们需要配置Bean，而SpringBoot没有提供支持，则可以自定义自动配置。
5、准生产的应用监控
SpringBoot提供基于http ssh telnet对运行时的项目进行监控。
6、无代码生产和xml配置　　
SpringBoot不是借助与代码生成来实现的，而是通过条件注解来实现的，这是Spring4.x提供的新特性。