# 注解

- 概念

  - 注解（Annotation），也叫元数据。一种代码级别的说明，与类、接口、枚举在同一个层次，它可以声明在包、类、字段、方法、局部变量、方法参数等方面，用来对这些元素进行说明、注释。
  - 概念描述：
    - JDK1.5之后的新特性
    - 说明程序的
    - 使用注解：@注解名称
  - 作用分类：
    - 编写文档：通过代码里标识的元数据生成文档【生成文档doc文档】 javadoc命令生成doc文档
    - 代码分析：通过代码里标识的元数据对代码进行分析【使用反射】
    - 编译检查：通过代码里标识的元数据让编译器能够实现基本的编译检查【override】

- JDK内置注解

  - @override ：检测被该注解标注的方法是否是继承自父类或者父接口
  - @Deprecated：将该注解标注的内容，表示已过时
  - @SuppressWarnings：压制警告，可以传参数，一般是all

- 自定义注解

  - 格式：
    - 元注解
    - public @interface 注解名称{属性列表；}
    
  - 本质
    
    - 注解本质上是一个接口，该接口默认继承Annotation接口
    
  - ```java
    import java.lang.annotation.*;
    
    /**
     * @author AlmostLover
     */
    @Target(ElementType.TYPE)//作用在方法上
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface MyAnnotation {
        //给属性默认赋值
        String className() default "";
        String methodName() default "";
    }
    ```

    

- 属性定义

  - 属性：接口中的抽象方法
    - 要求：
      - 属性的返回值类型
        - 基本数据类型
        - 字符串
        - 枚举
      - 定义了属性，在使用时需要给属性赋值
        - 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时可以不进行属性的赋值
        - 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可
        - 数组赋值时，值使用{}包裹，如果数组中只有一个值，则{}可以省略

- 元注解

  - 定义：用于描述注解的注解

  - @Target：描述注解能够作用的位置

    - 

      ![img](https://img.mubu.com/document_image/a9bcf69c-290e-4e49-bd4d-43d724070a50-3816968.jpg)

    - ElementType 取值：

      - TYPE：可以作用于类上
      - METHOD：可以作用于方法上
      - FIELD：可以作用于成员变量上

  - @Retention：描述注解被保留的阶段

    - @Retention(RetentionPolicy.SOURCE)
    - @Retention(RetentionPolicy.RUNTIME)：一般自定义注解都是runtime，当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
    - @Retention(RetentionPolicy.CLASS)

  - @Documented：描述注解是否被抽取到api文档中

  - @Inherited：描述注解是否被子类继承

- 解析注解

  - 在程序使用（解析）注解：获取注解中定义的属性值

    1.获取注解定义位置的对象

    2.获取指定的注解 getAnnotation(Class)

    3.调用注解中的抽象方法获取配置的属性值

```java
/**
 * @author AlmostLover
 */
@MyAnnotation(className = "con",methodName = "show")
public class ReflectTest {
    public static void main(String[] args) {
        //1.获取该类的字节码文件对象
        Class<ReflectTest> reflectTestClass = ReflectTest.class;
        //2.获取上边的注解对象，其实就是在内存中生产一个该注解接口的子类对象
        MyAnnotation myAnnotation = reflectTestClass.getAnnotation(MyAnnotation.class);
        //3.调用注解对象中定义的抽象方法获取返回值
        myAnnotation.methodName();
        myAnnotation.className();
    }
}

```


- 常用方法：

```java
    //用于判断该类上是否存在该注解
    boolean b = reflectTestClass.isAnnotationPresent(MyAnnotation.class);
```

- 案例：简单的测试框架

- 总结

  - 注解给谁用
    - 编译器
    - 给解析程序用
  - 注解不是程序的一部分，可以理解为注解就是一个标签