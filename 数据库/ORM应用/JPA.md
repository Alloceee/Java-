# JPA

### 简介

JPA的全称是Java Persistence API，即Java持久化API，是sun公司推出的一套基于ORM的规范，并不是ORM产品，**内部是由一系列接口和抽象类构成**。

JPA通过JDK5.0**注解描述对象 - 关系表的映射关系**，并将运行期的实体对象持久化到数据库中。

实现方式：

hibernate、toplink

### 优势

#### 标准化

JPA是JCP组织发布的Java EE标准之一，因此任何声称符合JPA标准的框架都遵循同样的架构，提供相同的访问API，这保证了基于API开发的企业应用能够经过少量的修改就能够在不同的JPA框架下运行。

#### 容器级特性的支持

JPA框架中支持大数据集、事务、并发等容器级事务，这使得JPA超越了简单持久化架构的局限，在企业应用发挥更大的作用。

#### 简单方便

JPA的主要目标之一就是提供更加简单的编程模型，在JPA框架下创建实体和创建Java类一样简单，没有任何的约束和限制，只需要使用javax.persistence.Entity进行注释，JPA的框架和接口也都非常简单，没有太多特别的规则和设计模式的要求，开发者可以很容易掌握。JPA基于非侵入式原则设计，因此可以很容易的和其他框架或者容器集成。

#### 查询能力

JPA的查询语句是面向对象而非面向数据库的，它以面向对象的自然语法结构查询语句，可以看成是Hibernate HQL的等价物。JPA定义了独特的JPQL（Java Persistence Query Language），JPQL是EJB QL的一种扩展，它是针对实体的一种查询语言，操作对象是实体，而不是关系数据库的表，而且能够支持批量更新和修改、JOIN、GROUP、BY、HAING等通常只有SQL才能够提供的高级查询特性，甚至还能够支持子查询。

#### 高级特性

JPA中能够支持面向对象的高级特性，如类之间的继承、多态和类之间的复杂关系，这样的这支持能够让开发者最大限度的使用面向对象的模型设计企业应用，而不需要自行处理这些特性在关系数据库的持久化。



JPA和Hibernate的关系就像JDBC和JDBC驱动的关系，JPA是规范，Hibernate除了作为ORM框架之外，它也是一种JPA实现。

##### JPA操作的基本步骤：

1. 加载配置文件创建实体管理器工厂

   persisitence:静态方法（根据持久化单元名称创建实体管理工厂）

   作用：创建实体管理工厂

2. 根据实体管理器工厂，创建实体管理器

   EntityManagerFactory：获取EntityManager对象

   方法：createEntityManager

   > 内部维护很多内容（数据库信息，缓存信息，所有的实体管理器对象）

   EntityManagerFactoy的创建过程比较浪费资源

   特点：线程安全的对象

   多个线程访问同一个EntityManagerFactory不会有线程安全问题

   如何解决EntityManagerFactory的创建过程浪费资源（耗时）的问题？

   思路：创建一个公共的EntityManagerFactory的对象

   静态代码块的形式创建EntityManagerFactory

3. 创建事务对象，开启事务
   
   - EntityManager对象：实体类管理器
- beginTransaction：创建事务
     - presist：保存
  - merge：更新
     - remove：删除
  - find/getReference：根据Id查询
  
- Transaction对象：事务
     - begin：开启事务
  - commit：提交事务
     - rollback：回滚

4. 增删查改操作

5. 提交事务

6. 释放资源

##### 搭建环境的过程：

1. 创建maven工程导入坐标

2. 需要配置jpa的核心配置文件

   1. 位置：配置到类路径下的一个叫做META-INF的文件夹下
   2. 命名：persistence.xml

3. 编写客户端的实体类

4. 配置实体类和表，类中的属性和表中的字段的映射关系

5. 保存客户端到数据库

   