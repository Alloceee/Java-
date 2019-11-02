# JDK8新特性

- 常用函数接口

- 方法引用

- Optional类

  - Optional 主要用作返回类型。在获取到这个类型的实例后，如果它有值，你可以取得这个值，否则可以进行一些替代行为。

  - Optional 类是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。

  - Optional 是个容器：它可以保存类型T的值，或者仅仅保存null。Optional提供很多有用的方法，这样我们就不用显式进行空值检测。

  - Optional 类的引入很好的解决空指针异常。

  - 

    ![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\资料\45414b7c-2741-4f20-a656-ec3ac2d44d64-3816968.jpg)

    ![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\资料\01acbdd2-ec4e-4501-b894-1495f20aa1a9-3816968.jpg)

  - https://www.cnblogs.com/zhangboyu/p/7580262.html