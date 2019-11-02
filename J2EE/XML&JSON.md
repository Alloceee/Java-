### json和xml区别

XML:
(1)应用广泛，可扩展性强，被广泛应用各种场合；
(2)读取、解析没有JSON快；
(3)可读性强，可描述复杂结构。
JSON:
(1)结构简单，都是键值对；
(2)读取、解析速度快，很多语言支持；
(3)传输数据量小，传输速率大大提高；
(4)描述复杂结构能力较弱。

XML技术

- 优点：用户配置文件，格式统一，符合标准；用于在互不兼容的系统间交互数据，共享数据方便；
- 缺点：xml文件格式复杂，数据传输占流量，服务端和客户端解析xml文件占用大量资源且不易维护
- xml常用解析器有两种:DOM和SAX
- 主要区别在与它们解析xml文档的方式不同。使用DOM解析，xml文档以DOM树形结构加载如内存，而SAX采用的是时间模型



SAX是什么？
也是用来解析XML的
SAX解析工具- 内置在jdk中。org.xml.sax.*

SAX运用场景？
DOM解析原理：一次性把xml文档加载进内存，然后在内存中构建Document树。
对内存要求比较要高。   
缺点： 不适合读取大容量的xml文件，容易导致内存溢出。

SAX解析原理： 加载一点，读取一点，处理一点。对内存要求比较低。

SAX解析工具核心：
核心的API：

SAXParser类： 用于读取和解析xml文件对象
parse（File f, DefaultHandler dh）方法： 解析xml文件



参数一： File：表示 读取的xml文件。
参数二： DefaultHandler： SAX事件处理程序。使用DefaultHandler的子类













# 资源文件的获取

```java
public static Map<String, String> getResource() {
        // ①创建Properties对象:用于读取文件
        Properties properties = new Properties();
        // ②通过线程获得类加载器
        ClassLoader contextClassLoader = Thread.currentThread().getContextClassLoader();
        // ③类加载器获得获得流资源
        // 资源文件在src目录下面：直接写文件路径   不需要/        如果写在其他的包路径==》包路径/子包路径/子包路径...../资源文件的名字
        InputStream is = contextClassLoader.getResourceAsStream("application.properties");
        //④properties加载流资源
        try {
            properties.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
        // ⑤读取数据：getProperty(key)：返回资源文件中对应key中的值(字符串)，若key不存在则返回null
        property.put("appName", properties.getProperty("appName"));
        property.put("res_path", properties.getProperty("res_path"));
        return property;
    }
```
