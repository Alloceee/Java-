# SpringData JPA

Spring Data JPA是spring基于ORM框架、JPA规范的基础上封装的一套JPA应用框架，可使开发者用极简的代码即可实现数据库的访问和操作。它提供了包括增删查改等在内的常用功能，且易于拓展，学习并使用Spring Data JPA可以极大提高开发效率。

> Spring Data JPA让我们摆脱了DAO层的操作，基本上所有CURD都可以依赖于它来实现，在实际的工作工程中，推荐使用Spring Data JPA+ORM(如：hibernate)完成操作，这样在切换不同的ORM框架时提供了极大的方便，同时也使数据库层操作更加简单，方便解耦。

Spring Data JPA是JPA规范的再次封装对象，底层还是使用了Hibernate的JPA技术实现，引用JPQL（Java Presistence Query Lanaguage）查询语句，属于Spring整个生态体系的一部分。

#### JPA包括一下3方面的内容

- 一套API规范
- 面向对象的查询语句：JPQL
- ORM元数据的映射

> JPA的宗旨是为POJO提供持久化标准规范

## 使用Spring Data Repositories

Spring Data存储库抽象中的中央接口是Repository。它将域类以及域类的ID类型作为类型参数进行管理。此接口主要用作标记接口，用于捕获要使用的类型，并帮助您发现扩展此接口的接口。该CrudRepository规定对于正在管理的实体类复杂的CRUD功能。



*User页面大小为20 的第二页*

```java
PagingAndSortingRepository<User, Long> repository = // … get access to a bean
Page<User> users = repository.findAll(PageRequest.of(1, 20));
```





### 查询注解

@Query

@NameQuery





#### 总结

SpringJPA里面的优先级：@Query>@NameQuery>方法查询

推荐使用的优先级：@Query>方法查询>@NameQuery

相同点：都不支持动态查询条件



### @Entity实例常用注解

| 注解     | 作用                                                         |
| -------- | ------------------------------------------------------------ |
| @Entity  | 定义对象将成为JPA管理的实体，将映射到指定的数据库表，作用于实体对象类上 |
| @Table   | 指定数据库的表名                                             |
| @Id      | 定义属性为数据库的主键，一个实体必须有一个                   |
| @IdClass | 利用外部类的联合主键                                         |
|          |                                                              |
|          |                                                              |

> #### 作为符合主键类，需要满足一下几种要求：
>
> - 必须实现Serializable接口
> - 必须有默认的public无参数的构造方法
> - 必须覆盖equals和hashCode方法。equals方法用于判断两个对象是否相同，EntityManager通过find方法来查找Entity时是根据equals的返回值来判断的。



### 关系注解

@JoinColumn

用法：配合@OneToOne、@ManyToOne、@OneToMany使用

@OneToOne

```java
@OneToOne

```



@ManyToOne

```java
@Entity
@Table(name = "project")
public class Project implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
    private String title;
    @ManyToOne(cascade = CascadeType.ALL,fetch = FetchType.EAGER)
    @JoinColumn(name = "user_id")//user_id作为外键
    private User user;
    private String createTime;
    private String overTime;
}
```





# SpringData JPA + SpringBoot

### 基础实例

1. pom.xml

```xml
 <!-- springBoot JPA的起步依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
```

2. application.properties

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/db_example
spring.datasource.username=root
spring.datasource.password=123456
```

3. 创建一个@Entity

```java
@Entity
public class TdNote implements Serializable {

  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  private long id;
  private long userId;
  private String title;
  private String content;
  private String path;
  private java.sql.Timestamp ctime;
```

> **使用时一定要实现Serializable接口**

4. 编写一个NoteRepository

```java
public interface NoteRepository extends CrudRepository<TdNote,Long> {
    
}
```

5. 编写一个控制类

```java
@GetMapping("/all")
    @ResponseBody
    public Iterable<TdNote> all(){
        return noteRepository.findAll();
    }
```

6. 运行效果

```java
[{"id":1,"userId":0,"title":"111","content":"# eieh\n## ehi2eh2i","path":null,"ctime":"2019-08-22 04:55:37"},{"id":2,"userId":0,"title":"12","content":"212","path":null,"ctime":"2019-08-22 04:57:49"},{"id":3,"userId":0,"title":"3","content":"2121","path":null,"ctime":"2019-08-22 05:24:05"},{"id":4,"userId":0,"title":"3月31","content":"weqqwe","path":null,"ctime":"2019-08-22 05:25:05"}]
```

