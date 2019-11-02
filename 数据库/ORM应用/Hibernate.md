# Hibernate

Hibernate就是应用ORM思想建立的一个框架，一般我们把它称之为全自动的ORM框架，程序员在使用Hibernate时几乎不要编写sql语句，而是通过编写操作对象即可完成对数据库的增删查改。

> 它对JDBC进行了非常轻量级的对象封装。
>
> 它将POJO与数据库表建立映射关系，是一个全自动的ORM框架。

Hibernate

- hibernate的inverse属性
  - 解决方案一：按照Object[]数据取出数据，然后自己组Bean
  - 解决方案二：对每个表的bean写构造函数，比如表一要查出field1，field2两个字段，那么一个构造函数就是bean(type field1,type field2),然后再hql里面直接生成这个Bean
- 简述iBatis框架与Hibernate的框架的区别及应用场景
  - 不用写sql语句，可以以面向对象的方式设计和访问，方便理解。可以自动HQL转化为SQL语句，跨平台，其实hibernate底层也是调用的jdbc，它吃屎对jdbc进行了封装
  - hibernate缺点：处理复杂业务时，灵活度差，复杂的hql难写
  - ibatis优点：是在结果集与实体类之间进行映射，效率高，学习成本低
  - ibatis缺点：需要我们自己写SQL语句，不能够跨平台
  - 使用场景：
    - ibatis可以做大型项目，但开发量会比hibernate多，hibernate只适合做中小型项目，因为其性能是个大问题，当然能把hibernate性能优化的很好是个例外

### 操作案例

1. 编写配置文件，默认hibernate.cfg.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-configuration SYSTEM 
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
   <session-factory>
   <!-- 数据库配置 -->
   <property name="hibernate.connection.url">
      jdbc:mysql://localhost/test
   </property>
   <property name="hibernate.connection.driver_class">
      com.mysql.jdbc.Driver
   </property>
   <property name="hibernate.connection.username">
      root
   </property>
   <property name="hibernate.connection.password">
      root123
   </property>
       
   <!-- 选项配置 -->
   <!-- 根据实体类去创建表 -->
   <property name="hbm2ddl.auto">
      update
   </property>
   <!-- 告诉hibernate应该以哪个数据的语法去生成sql语句 -->
   <property name="hibernate.dialect">
      org.hibernate.dialect.MySQLDialect
   </property>
   <!-- 可以不设置，把生成的sql语句输出到后台 -->
   <property name="show_sql">
      true
   </property>
   <!-- List of XML mapping files -->
   <mapping resource="Employee.hbm.xml"/>

</session-factory>
</hibernate-configuration> 
```



2.

