### 1.JDBC技术

#### 1.原生jdbc操作数据库流程

- class.forName加载数据库连接驱动
- DriverManager.getConnection()获取数据库连接对象
- 执行SQL处理结果集，执行SQL前如果有参数值就设置参数值setXXX()
- 关闭结果集、关闭会话、关闭连接
- 为什么要使用PreparedStatement
  - PrepareStatement接口继承Statement，PrepareStatement实例包含已编译的SQL语句，所以其执行速度要快于Statement对象
  - 作为Statement的子类，PrepareStatement继承了Statement的所有功能，三种方法execute、executeQuery和executeUpdate已被更改以使之不再需要参数
  - 在JDBC应用中，任何时候都不要使用Statement，原因如下
    - 代码的可读性和可维护性，Statement需要不断地拼接，而PreparedStatement不会
    - PreparedStatement尽最大可能提高性能，DB有缓存机制，相同的预编译语句不会再次需要编译
    - 最重要的一点是极大地提高了安全性，Statement容易被SOL注入，而PreparedStatement传入的内容不会和sql语句发生任何匹配关系