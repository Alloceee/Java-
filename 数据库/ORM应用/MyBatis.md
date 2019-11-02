# MyBatis

### 什么是 MyBatis ？

  - MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以对配置和原生Map使用简单的 XML 或注解，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。
  - MyBatis作为持久层的框架，其主要思想是将程序中的大量SQL语句剥离出来，配置在配置文件中，实现SQL的灵活配置。这样做的好处是将SQL与程序代码分离，可以在不修改程序代码的情况下，直接在配置文件中修改SQL。

### 基本映射关系

数据表映射类

数据表的行映射对象（即实例）

数据表的列（字段）映射对象的属性

### MyBatis如何安装？

  - 要使用 MyBatis， 只需将 mybatis-x.x.x.jar 文件置于 classpath 中即可。

  - 如果使用 Maven 来构建项目，则需将下面的 dependency 代码置于 pom.xml 文件中：

    ```xml
    <dependency> 
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>x.x.x</version>
    </dependency>
    ```

#### MyBatis用到的设计模式

简单工厂、Builder、动态代理（JDK提供）

#### MyBatis推荐采用数据源方式连接数据库

数据源是一种用来提高数据库连接性能的常规手段，数据源会负责维持一个数据库连接池，当程序创建数据源实例时，系统会一次性地创建多个数据库连接，并把这些数据库连接保存在连接池中。当程序需要进行数据库访问时，无需重新获得数据库连接，而是从连接池中取出一个空闲的数据库连接，当程序使用数据库连接访问数据库结束后，无需关闭数据库连接，而是将数据库连接归还给连接池即可，通过这种方式，就可以避免频繁地获取数据库连接、关闭数据库连接所导致的性能下降。



### MyBatis的功能架构：

我们把Mybatis的功能架构分为三层：

- **API接口层**：提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理。
- **数据处理层**：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。
- **基础支撑层**：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑。



### MyBatis的使用

1. 开发持久化类PO和编写持久化操作的Mapper.xml，在其中定义要执行的SQL语句

   ```
   useGeneratedKeys="true"
   ```

   > 表示使用数据库的自增长策略，需要底层数据库的支持

2. 获取SqlSessionFactroy

3. 获取SqlSession

4. 用面向对象的方式操作数据库

5. 关闭事务，关闭SqlSession

```java
public class MyBatisTest{
    public static void main(String[] args)throws Exception{
        //读取mybatis-config.xml文件
        InputStream is = Resources.getResourceAstream("/mybatis-config.xml");
        //初始化mybatis，创建SqlSessionFactory类的实例
        SqlSessionFactory sqlsessionFactory = new SqlSessionFactoryBuilder().builde(is);
        //创建session实例
        SqlSession session = sqlsessionFactory.openSession();
        //创建user对象
        User user = new User("admin","男"，26);
        session.insert("org.fkit.mapper.UserMapper.save",user);
        //提交事务
        session.commit();
        //关闭session
        session.close();
    }
}
```



### 常用对象

#### SqlSessionFactory

常用方法：SqlSession openSession().创建SqlSession对象

#### SqlSession

关键对象，是持久化操作的对象，类似于JDBC中的connection，它是应用程序额持久存储层之间执行交互操作的一个单线程对象。

每个线程都应该有它自己的SqlSession实例。SqlSession的实例不能被共享，也是线程不安全的，绝对不能将SqlSession实例的引用放在一个类的静态字段甚至是实例字段中，也绝对不能将SqlSession实例的引用放在任何类型的管理范围中，比如Servlet当中的HttpSession对象中。使用完之后要关闭Session，放在finally中。

底层封装了DataSource。

#### 通过注解添加别名

```java
@Alias("TdMessage")
public class TdMessage {

    private Integer id;
    private String content;
    private Integer uid;
    private Integer gid;
    ...
    }
```

mapper.xml中resultType可以直接填写别名

```xml
<mapper namespace="com.chat.demo.mapper.MessageMapper">
    <select id="findByGroupId" resultType="TdMessage" parameterType="Integer">
        select *
        from td_message
        where gid = #{gid}
        order by createTime desc
        limit 5
    </select>
</mapper>
```

#### environments环境配置

environments环境配置实际就是数据源的配置。Mybatis可以配置多种环境，这种机制使得Mybatis可以将SQL映射应用于多种数据库中。例如，开发、测试和生产环境需要有不同的配置；多个生产数据库想使用相同的SQL映射等等。

尽管可以配置多个环境，但是每个SqlSessionFactroy实例只能选择一个环境，即每个数据对应一个SqlSessionFctory实例，所以，如果想要连接两个数据库就要创建两个SqlSessionFactory实例，每个数据库对应一个。

#### XML映射文件

resultMap,resultType：结果集的映射，不能同时使用

头部声明：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
```

举例：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.chat.demo.mapper.FileMapper">
    <select id="findAll" resultType="TdFile">
        select *
        from td_file
    </select>
    <select id="findByFilename" resultType="String" parameterType="String">
        select path
        from td_file
        where filename = #{filename}
    </select>
    <insert id="upload" parameterType="TdFile">
        insert into td_file(md5, path, filename)
        values (#{md5}, #{path}, #{filename})
    </insert>
     <update id="modify" parameterType="TdFile">
        update td_file
        set md5 = #{md5}, path = #{path},filename = #{filename}
        where id = #{id}
    </update>
     <delete id="delete" parameterType="TdFile">
        delete
        from td_file
        where id = #{id}
    </delete>
</mapper>
```



### MyBatis的优缺点

  - 优点：
    - 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
    - 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql基本上可以实现我们不使用数据访问框架可以实现的所有功能，或许更多。
    - 解除sql与程序代码的耦合：通过提供DAL层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
    - 提供映射标签，支持对象与数据库的orm字段关系映射
    - 提供对象关系映射标签，支持对象关系组建维护
    - 提供xml标签，支持编写动态sql。
  - 缺点：
    - 编写SQL语句时工作量很大，尤其是字段多、关联表多时，更是如此。
    - SQL语句依赖于数据库，导致数据库移植性差，不能更换数据库。
    - 框架还是比较简陋，功能尚有缺失，虽然简化了数据绑定代码，但是整个底层数据库查询实际还是要自己写的，工作量也比较大，而且不太容易适应快速数据库修改。
    - 二级缓存机制不佳

### MyBatis中#和$的区别
  - \#相当于对数据加上双引号，$相当于直接显示数据
  - \#能够很大程度上防止SQL注入，$不能防止SQL注入
  - $一般用于传入数据库对象，例如表名
      - 应用场景：报表的实现，动态选取表名，表名为${}
      - Orderby、in操作都可以考虑${}
  - 一般能用#就不要用$

### myBatis拦截器

- 只能拦截四种类型的接口:Executor、StatementHandler、ParameterHandler和ResultSetHandler.
- 如果要支持拦截其他接口要重写MyBatis的Configuration
- myBatis拦截器常常被用来进行分页处理

### 配置文件中的大于小于号需要转义

- 特殊字符

  原符号  <   <=   >   >=    &     '      "替换符号<<=>>=&'"

  更多见下表

  ![img](https://img-blog.csdnimg.cn/20190611112002702.png)

  

#### 如果为PO类添加有参的构造函数，默认要添加一个无参的构造函数给mybatis进行初始化操作

#### 在Insert之后获取自增的主键

###### 1.mapper.xml

```xml
  <insert id="insert" parameterType="TdGroup"  useGeneratedKeys="true"  keyProperty="id" >
        insert into td_group(name,createBy,roomKey)
        values (#{name},#{createBy},#{roomKey})
    </insert>
```

> useGeneratedKeys="true"    keyProperty="id"  
>
> 设置后会将自增的主键Id放入传过来的实体类的id中，keyProperty设置的是存放id的实体类字段名称

###### 2.controller.java获取返回的Id

```java
 @PostMapping("/create")
    public String insert(TdGroup group, @SessionAttribute("user") TdUser user) {
        group.setCreateBy(user.getId());
        groupMapper.insert(group);
        //通过mybatis获取自增主键
        Integer id = group.getId();
        if (id != null) {
            return JSONObject.toJSONString(new ResultData(200, Integer.toString(id)));
        } else {
            return JSONObject.toJSONString(new ResultData(500, "创建群聊失败"));
        }
    }
```

>通过实体类的get方法获取

#### 实现关联映射

##### 一对一映射

一个person对应一个card

Person.java

```java
public class Person implements Serializable{
    private Integer id;
    private String name;
    private String sex;
    private Integer age;
    private Card card;//一对一
}
```

CradMapper.xml

```xml
<mapper namespace="com.mapper.CardMapper">
    <select id="findById" parameter="int" resultType="com.entity.card">
        select * from td_card where id = #{id}
    </select>
</mapper>
```



PersonMapper.xml

```xml
<mapper namespace="com.mapper.PersonMapper">
    <select id="findById" parameter="int" resultMap="personMapper">
        select * from td_person where id = #{id}
    </select>
    <resultMap type="com.entity.Person" id="personMapper">
        <id property="id" column="id" />
        <result property="name" column="name" />
        <result property="sex" column="sex" />
        <result property="age" column="age" />
        <!-- 一对一关联映射：association -->
        <association property="card" column="crad_id" select="com.mapper.CardMapper.findById" javaType="com.entity.Card" />
    </resultMap>
</mapper>
```

##### 一对多映射

多个学生属于一个班级

Clazz.class

```java
public class Clazz implements Serializable{
    private Integer id;
    private String code;
    private String name;
    private List<Student> students;
}
```

Student.class

```java
public class Student implements Serializable{
    private Integer id;
    private String name;
    private String sex;
    private Clazz clazz;
}
```

ClazzMapper.xml

```xml
<mapper namespace="com.mapper.ClazzMapper">
    <select id="findById" parameter="int" resultMap="clazzMapper">
        select * from td_clazz where id = #{id}
    </select>
    <resultMap type="com.entity.Clazz" id="clazzMapper">
        <id property="id" column="id" />
        <result property="code" column="code" />
        <result property="name" column="name" />
        <!-- 一对多关联映射：collection fetchType="lazy"表示懒加载-->
        <collection property="students" select="com.mapper.StudentMapper.findByClazzId" javaType="ArrayList" column="id" ofType="com.entity.Student" fetchType="lazy"/>
    </resultMap>
</mapper>
```

> fetchType属性，取值有eager和lazy，eager表示立即加载，lazy表示懒加载，懒加载还需要在mybatis-config.xml中添加如下配置：

```xml
<settings>
    <!-- 要是延迟加载生效必须配置下面两个属性 -->
    <setting name="lazyLoadingEnabled" value="true" />
    <setting name="aggressiveLazyLoading" value="false" />
</settings>
```

>注意：一对多使用的都是懒加载  
>
>如果两个表中都有id属性，需要设置别名，否则默认选取第一个id，设定别名o.id AS oid，column中的oid就是td_order中的id

#### 多对多映射

查询语句嵌套子查询

```xml
<mapper namespace="org.fkit.mapper.ArticleMapper"> 
    <select id="findByOrderId" parameter="int" resultType="org.fkit.domain.Article"> 
    SELECT * FROM tb_article WHERE id IN ( 
    SELECT article_id FROM tb_item WHERE order_id = #{id}
    </select> 
</mapper>
```

### 动态sql

#### if

动态SQL通常会做的事情是有条件的包含where子句的一部分

```xml
<mapper namespace="com.mapper.EmployeeMapper">
    <select id="findByIdLike" resultType="com.entity.Employee">
        select * from td_employee where state = "ACTIVE"
        <!-- 可选条件，如果传进来的参数有id苏醒，则加上id查询条件 -->
        <if test="id != null">
            and id = #{id}
        </if>
    </select>
</mapper>
```

```java
public interface EmployeeMapper{
    List<Employee> findByIdLike(HashMap<String,Object> params);
}
```

> 在myBatis中，获取参数有两种方式：一是从**HashMap**中获取集合property对象，二是从**javabean**中获取property对象

#### choose(when、otherwise)

```xml
<select id="findByEmploeeChoose" parameterType="hashmap" resultType="com.entity.Emploee">
    select * from td_emploee where status="ACTIVE"
    <!-- 如果传入id，就根据id查询，没有传入就根据username和password查询，否则查询sex=男 -->
    <choose>
        <when test="id !=null">
        	and id = #{id}
        </when>
        <when test="loginname != null and password != null">
        	and loginname = #{loginname} and password = #{password}
        </when>
        <otherwise>
            and sex = "男"
        </otherwise>
    </choose>
</select>
```

#### where

```xml
<select id = "findByEmployeeLike" reusltType="com.entity.employee">
 	select * from td_emploee
    <where>
        <if test="status !=null">
        	status = #{status}
        </if>
        <if test="id !=null">
        	and id = #{id}
        </if>
        <if test="loginname != null and password != null">
        	and loginname = #{loginname} and password = #{password}
        </if>
    </where>
</select>
```

> where元素知道只有在一个以上的if条件有值的情况下才去插入where子句。而且，若最后的内容是“AND”或”OR“开头，则where元素也知道如何将它们去除

#### set

```xml
<select id = "findByEmployeeWithIn" reusltType="com.entity.employee" parameterType="int">
 	select * from td_emploee where id=#{id}
</select>
<!-- 动态更新员工信息 -->
<update id = "findByEmployeeIfNecessary" parameterType="com.entity.employee">
 	update td_employee
    <set>
    	<if test="loginname != null">loginname = #{loginname},</if>
    	<if test="password != null">password = #{password},</if>
        <if test="sex != null">sex = #{sex},</if>
        <if test="status != null">status = #{status}</if>
    </set>
    where id=#{id}
</select>
```

> set元素会动态前置SET关键字，同时也会消除无关的逗号，因为使用了条件语句之后很可能就会在生成的赋值语句的后面留下这些逗号

#### trim

```xml
<trim prefix="前缀" suffix="后缀" >

</trim>
```



#### foreach

##### IN遍历

```xml
<select id = "findByEmployeeIn" reusltType="com.entity.employee">
 	select * from td_emploee where id IN
    <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
    	#{item}
    </foreach>
</select>
```

> foreach允许指定一个集合，声明可以用在元素体内的集合项和索引变量。它也允许指定开闭匹配的字符串以及在迭代中间放置分隔符。



```java
List<Employee> findByEmployeeIn(List<Integer> ids)
```

##### 批量插入

通过foreach动态拼装SQL语句

```xml
<insert id="insertAuthor" useGeneratedKeys="true"
    keyProperty="id">
  insert into Author (username, password, email, bio) values
  <foreach item="item" collection="list" separator=",">
    (#{item.username}, #{item.password}, #{item.email}, #{item.bio})
  </foreach>
</insert>
```

>  useGeneratedKeys="true"
>     keyProperty="id"获取每次添加生成的主键id

使用BATCH类型的executor（可以手动更改batch提交队列的大小，jdbc批量提交就是由此实现）





#### bind

bind元素可以从OGNL表达式中创建一个变量并将其绑定到上下文

```xml
<select id = "findByEmployeeLikeName" reusltType="com.entity.employee">
    <bind name="pattern" value="'%' + parameter.getName() +'%'" />
    select * from td_emploee
    where loginname like #{pattern}
</select>
```

```java
List<Employee> findByEmployeeLikeName(Employee employee);
```





### 缓存机制

#### 一级缓存（SqlSession级别）

在创建数据库时需要创建一个SqlSession对象 ，在对象中有一个HashMap用于存储缓存数据，不同的SqlSession之间的缓存数据区域（HashMap）是互相不影响的。

一级缓存的作用域是SqlSession范围的，当在一个SqlSession中执行两次相同的sql语句时，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次查询时会总缓存中获取数据，不再去底层数据库查询，从而提高查询效率。

> 需要注意，如果SqlSession执行了DML操作（insert，update，delete），并提交到数据库，MyBatis就会清空SqlSession中的一级缓存，这样做的目的是为了保证缓存中存储的是最新的消息，避免出现脏读现象。

当一个SqlSession结束后该SqlSession中的一级缓存也就不存在了。MyBatis默认开启一级缓存，不需要任何配置。

#### 二级缓存（mapper级别）

二级缓存是mapper级别的缓存，使用二级缓存时，多个SqlSession使用同一个Mapper的sql语句去操作数据库，得到的数据会存在二级缓存区域，它同样是使用HashMap进行数据存储。相比一级缓存SqlSession，二级缓存的范围更大，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的。

二级缓存是多个SqlSession共享的，其作用域是mapper的同一个namespace。不同的SqlSession两次执行相同的namespace下的sql语句，且向sql中传递的参数也相同，即最终执行相同的sql语句，则第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次查询时会从缓存中获取数据，不再去底层数据库查询，从而提高查询效率。

MyBatis默认没有开启二级缓存，需要手动在setting全局配置参数中开启二级缓存。

| 配置属性      | 属性值                                                       |
| ------------- | ------------------------------------------------------------ |
| flushInterval | 刷新间隔（毫秒，默认不设置，调用语句时刷新）                 |
| size          | 缓存数目（默认 1024）                                        |
| readOnly      | 只读。只读的缓存会给所有调用者返回缓存对象的相同实例，因此这些对象不能被修改，提供性能优势。可读写的缓存会返回缓存对象的拷贝（通过序列化），慢但是安全，默认false。 |
| eviction      | 回收策略（默认LRU：最近最少使用）FIFO、SOFT、WEAK            |

> 使用二级缓存时，与查询结果映射的Java对象必须实现java.io.Serializable接口的序列化和反序列化操作，如果存在父类，其成员都需要实现序列化接口。实现序列化接口是为了缓存数据进行序列化和反序列化操作，因为二级缓存数据存储介质多种多样，不一定在内存，有可能是硬盘或者远程服务器。

MyBatis缓存的实现是基于Map的，从缓存里面读写数据是缓存模块的核心基础功能；

除核心功能之外，有很多额外的附加功能，如：防止缓存击穿，添加缓存情况策略（fifo,lru）、序列化功能、日志能力、定时清空能力等

附加功能可以以任意的组合附加到核心基础之上

### MyBatis的缓存功能使用HashMap实现会不会出现并发安全的问题





### 注解开发

#### 一对多映射

```java
 @Select("SELECT * FROM `td_message` where gid = #{gid} limit 10")
    @Results({
            @Result(property = "content", column = "content"),
            @Result(property = "createTime", column = "createTime"),
            @Result(property = "user", column = "uid",
                    one = @One(select = "com.chat.demo.mapper.UserMapper.findById"),
                   fetchType=FetchType.EAGER)
 })
List<TdMessage> findByGroupId(Integer gid);
```

> fetchType=FetchType.EAGER表示加载的类型，是懒加载还是立即加载

#### 多对多映射

```java
@Select("SELECT * FROM `td_message` where gid = #{gid} limit 10")
    @Results({
            @Result(property = "content", column = "content"),
            @Result(property = "createTime", column = "createTime"),
            @Result(property = "user", column = "uid",
                    many = @many(select = "com.chat.demo.mapper.UserMapper.findById"),
                   fetchType=FetchType.LAZY)
 })
List<TdMessage> findByGroupId(Integer gid);
```

> 一对多关系用@one，多对多关系用@many



### 代码生成器(MGB)

1. pom.xml文件添加以来和mybatis-generator插件

```xml
			<plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.5</version>
                <configuration>
                    <configurationFile>src/main/resources/mybatis-generator/generatorConfig.xml</configurationFile>
                    <overwrite>true</overwrite>
                </configuration>
                <!--此处必须添加mysql驱动包-->
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <scope>runtime</scope>
                        <version>5.1.46</version>
                    </dependency>
                </dependencies>
            </plugin>
```

##### 必须添加mysql驱动包

```xml
		<dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.5</version>
        </dependency>
```

2. 创建对应的generatorConfig.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE generatorConfiguration PUBLIC
        "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd" >
<generatorConfiguration>
	<!--mysql-connector-java-8.0.17.jar所在的位置-->
    <classPathEntry
            location="C:\Users\AlmostLover\.m2\repository\mysql\mysql-connector-java\8.0.17\mysql-connector-java-8.0.17.jar"/>

    <context id="context" targetRuntime="MyBatis3Simple">
        <!--是否取消注释-->
        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="false"/>
        </commentGenerator>

        <!--连接信息-->
        <jdbcConnection userId="root" password="ljwlloveyou980" driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/vote?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=UTC&amp;useSSL=true"/>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

         <!--实体类生成配置-->
        <javaModelGenerator targetPackage="com.yewenshu.vote.demo.model" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

         <!--映射xml配置类生成配置-->
        <sqlMapGenerator targetPackage="com.yewenshu.vote.demo.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>

         <!--接口mapper类生成配置-->
        <javaClientGenerator targetPackage="com.yewenshu.vote.demo.dao" type="XMLMAPPER"
                             targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>

         <!--数据库表配置，%表示所有表-->
        <table tableName="%" enableCountByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" enableUpdateByExample="false"/>
    </context>
</generatorConfiguration>
```

> 文件路径和pom.xml中 <configurationFile>src/main/resources/mybatis-generator/generatorConfig.xml</configurationFile>的路径保持一致

3. 点击Maven中

![1566820454627](C:\Users\AlmostLover\Desktop\MarkDown笔记\数据库\ORM应用\1566820454627.png)

表示运行成功

![1566820493937](C:\Users\AlmostLover\Desktop\MarkDown笔记\数据库\ORM应用\1566820493937.png)



## 案例

### 实现模糊查询

##### 方法一：把'%#{name}%'改为”%”#{name}”%”

```xml
 <select id="findByLike" resultType="TdFile" parameterType="TdFile">
        select *
        from td_file
        <where>
            <if test="filename !=null">
                filename like "%"#{filename}"%"
            </if>
            <if test="des !=null">
                and des like "%"#{des}"%"
            </if>
            <if test="type !=null">
                and type like "%"#{type}"%"
            </if>
        </where>
</select>
```

##### 方法二：使用sql中的字符串拼接函数（推荐使用）

```xml
<select id="findByLike" resultType="TdFile" parameterType="TdFile">
        select *
        from td_file
        <where>
			<if test="filename !=null">
            	filename like CONCAT(CONCAT('%',#{filename}),'%')
      		</if>
          	<if test="des !=null">
             	and des like CONCAT(CONCAT('%',#{des}),'%')
       		</if>
  			<if test="type !=null">
                and type like CONCAT(CONCAT('%',#{type}),'%')
       		</if>
		</where>
</select>
```



### 实体类为什么实现序列化

Hibernate的实体类中为什么要继承Serializable？

hibernate有二级缓存，缓存会将对象写进硬盘，就必须序列化，以及兼容对象在网络中的传输 等等。

java中常见的几个类（如：Interger、String等），都实现了java.io.Serializable接口。

实现 java.io.Serializable 接口的类是可序列化的。没有实现此接口的类将不能使它们的任一状态被序列化或逆序列化。序列化类的所有子类本身都是可序列化的。这个序列化接口没有任何方法和域，仅用于标识序列化的语意。

确切的说应该是对象的序列化，一般程序在运行时，产生对象，这些对象随着程序的停止运行而消失，但如果我们想把某些对象（因为是对象，所以有各自 不同的特性）保存下来，在程序终止运行后，这些对象仍然存在，可以在程序再次运行时读取这些对象的值，或者在其他程序中利用这些保存下来的对象。这种情况 下就要用到对象的序列化。

只有序列化的对象才可以存储在存储设备上。为了对象的序列化而需要继承的接口也只是一个象征性的接口而已，也就是说继承这个接口说明这个对象可以 被序列化了，没有其他的目的。**之所以需要对象序列化，是因为有时候对象需要在网络上传输，传输的时候需要这种序列化处理，从服务器硬盘上把序列化的对象取 出，然后通过网络传到客户端，再由客户端把序列化的对象读入内存，执行相应的处理。**

**将二级缓存中的内容持久化保存下来，便于恢复缓存的信息，hibernate的缓存机制通过使用序列化，断定应该是基于序列化的缓存，如没有 serializable接口，在序列化时，使用objectOutputStream的write（object）方法将对象保存到文件时将会出现异常。**

**Hibernate并不要求持久化类必须实现java.io.Serializable接口，但是对于采用分布式结构的Java应用，当Java对象在不同的进程节点之间传输时，这个对象所属的类必须实现Serializable接口，此外，在Java Web应用中，如果希望对HttpSession中存放的Java对象进行持久化，那么这个Java对象所属的类也必须实现**



