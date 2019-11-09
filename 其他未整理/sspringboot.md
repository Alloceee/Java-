今天我看到项目中用到了  @PostConstruct 这个注解，之前没看到过，特地查了一下，

1.@PostConstruct说明

     被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器调用一次，类似于Serclet的inti()方法。被@PostConstruct修饰的方法会在构造函数之后，init()方法之前运行。

2.@PreDestroy说明

     被@PreDestroy修饰的方法会在服务器卸载Servlet的时候运行，并且只会被服务器调用一次，类似于Servlet的destroy()方法。被@PreDestroy修饰的方法会在destroy()方法之后运行，在Servlet被彻底卸载之前。

 




可以看出来这个注解是用来项目启动时，进行加载参数的初始化参数的操作

然后我就总结了下springboot中几种项目启动时，可以初始化加载参数的方法。

第一种：注解@PostConstruct

项目启动之后，可以看到这行代码在项目启动的时候已经执行了



 

第二种：实现CommandLineRunner接口，@Order注解里面的参数是类执行的顺序，由小到大顺序





类中的输出语句都打印出来了



第三种：springboot的启动类,

最简单的方法，直接在springboot的启动类中加上需要初始化的类就行了~











springboot中@GeneratedValue作用：
（1）、@GeneratedValue注解存在的意义主要就是为一个实体生成一个唯一标识的主键、@GeneratedValue提供了主键的生成策略。

（2）、@GeneratedValue注解有两个属性,分别是strategy和generator,

generator属性：

generator属性的值是一个字符串,默认为"",其声明了主键生成器的名称
(对应于同名的主键生成器@SequenceGenerator和@TableGenerator)。


strategy属性：提供四种值:

-AUTO主键由程序控制, 是默认选项 ,不设置就是这个

-IDENTITY 主键由数据库生成, 采用数据库自增长, Oracle不支持这种方式

-SEQUENCE 通过数据库的序列产生主键, MYSQL  不支持

-Table 提供特定的数据库产生主键, 该方式更有利于数据库的移植

 **mysql> SET @@global.sql_mode= ‘’;** 



### java对象转成JSON字符串，避免 $ref

2016-08-05 15:29:59 更多

版权声明：本文为博主原创文章，遵循[ CC 4.0 BY-SA ](http://creativecommons.org/licenses/by-sa/4.0/)版权协议，转载请附上原文出处链接和本声明。本文链接：https://blog.csdn.net/fengzhihen2007/article/details/52129576

如果使用Hibernate, 查询出重复的数据或者使用类似下面的数据

User s = new User();
s.setAccount("2121");
List<User> list = new ArrayList<User>();
list.add(s);
list.add(s);
System.out.println(JSON.toJSONString(list));

运行结果是：

[{"account":"2121"},{"$ref":"$[0]"}]



如果接口返回上面的数据， 客户端解析数据时会出现问题， 为了避免 $ref出现， 可以使用下面的代码：

JSON.toJSONString(list, SerializerFeature.DisableCircularReferenceDetect)