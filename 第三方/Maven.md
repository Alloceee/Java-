# Maven



Jar

主要用于存放.class文件，要求目录结构完整，因为目录结构就是包名，也可以存放其他类型的文件，.json、.xml、.properties、.yml

classpath

类路径，告诉当前JVM要运行的类在哪些位置



### 依赖

#### 声明依赖

使用<dependency>标签声明，嵌套在<dependencies>中

```xml
<dependencies>
    <dependecy></dependecy>
</dependencies>
```

#### 依赖范围

> <scope></scope>

| 范围     | 编译有效 | 测试有效 | 运行有效 | 例子        |
| -------- | -------- | -------- | -------- | ----------- |
| compile  | √        | √        | √        | Spring-core |
| provited | √        | √        | —        |             |
| test     | —        | √        | —        | JUnit       |
| runtime  | —        | √        | √        | JDBC驱动    |

#### 依赖传递



#### 排除依赖

```xml
  <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <!-- <scope>provided</scope>-->
            <exclusions>
                <exclusion>
                    <artifactId></artifactId>
                    <groupId>org.apache</groupId>
                </exclusion>
            </exclusions>
        </dependency>

```

#### 生命周期

构建过程：

清除、编译、检测、校验、测试、打包

三个独立的生命周期：

clean生命周期：

pre-clean

clean

post-clean

default生命周期

site生命周期

#### 插件



#### 测试

mvn test背后默认执行操作

只有 src/main/java下的文件才会被执行

以Test结尾，开头或者TestCase结尾的java类

加上@Test注解

#### 聚合和继承

##### 聚合

###### (1) 父模块的创建.

父模块一般承担聚合模块和统一管理依赖的作用，没有实际代码和资源文件.

父模块就是创建一个普通的 Maven Project , 此处省略.

但是需要注意的是: 父模块的打包方式必须是 pom.



![img](https://upload-images.jianshu.io/upload_images/13884592-6d66c08b393f4281.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/303/format/webp)

###### 继承

**Maven** **私服**

 

# **一 仓库分类**

 

Maven 的仓库分为两种1 本地仓库

2 远程仓库

---中央仓库

由maven 提供的。收录了很多**开源技术****和框架**的 jar 包和插件。如：junit、spring、strust2、freemarker、qiniuyun、alibaba.. 

---私服

一般在局域网中搭建，例如公司局域网。也可以搭建在外网。

 私服的作用：减少对中央仓库的访问。提高效率。

在maven 中添加依赖时查找过程：

本地仓库---->私服---->中央仓库

# 二	**window** **系统中** **nexus** **搭建私服**

 

nexus 有两种版本的：一个是带有 jetty 容器可以运行的。nexus-bundle 版本。

另一个是不带有运行容器的。是个war 包需要部署tomcat 中。

 

nexus-bundle 版本的安装过程：下载 nexus-版本-bundle.zip，例如 nexus-2.8.0-bundle.zip。解压进入 bin 目录，双击 nexus.bat,安装 window 服务。按操作提示完成安装。然后将私服的bin 目录加入 path 环境变量即可。安装完成后，默认端口是：**8081**。可以在控制台通过**nexus start** 启动该服务，从而启动私服。默认管理员账号密码分别是：**admin** 和 **admin123**。访问地址是：[**http://localhost:8081/nexus**](http://localhost:8081/nexus)

 

**3.** **x 版本：解压、将bin目录加入Path环境变量。执行nexus /run 或nexus -run** 

**配置 ，打开 nexus-3.13.0-01\etc\nexus-default.properties**

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\第三方插件\wps1.jpg) 

 

 

 

 

不带运行容器的 war 包版本安装过程：下载 nexus-版本号.war，例如 nexus-2.9.0.war。下载之后，将 war 包拷贝到 tomcat 的 webapps 目录底下即可完成安装。此时：nexus 是以tomcat 的一个应用存在的。因此 nexus 随着 tomcat 的启动而启动（第一次启动时 tomcat 会将 war 包解压），端口也取决于 tomcat 的端口，访问路径也是取决于解压后的根目录的名称。 默认账号和密码分别是 admin 和 admin123。例如：端口是 8081，解压后根目录是： nexus-2.9.0 那么访问路径就是：**http://localhost:8081/nexus-2.9.0**

 

注意：①这里的访问路径是针对本机而言。如果你的本机是一台云主机，例如阿里云。那

么外网访问的实际地址只需要将 localhost 改为实际的公网 ip 或者可以解析得到的域名即可。② 私服中的 jar 包仓库保存目录默认在，C:\Users\当前用户\sonatype-work\nexus 。

第一次启动时创建 sonatype-work 目录。



# **三 如何使用私服**

 

### **3.1** **从私服中下载** **jar** **包（不用权限）**

在本地仓库的 setting.xml 中配置。

<mirrors>

<mirror>

<id>nexus</id>

<mirrorOf>*</mirrorOf>

<url>仓库组的 url </url>

</mirror>

</mirrors>

说明： mirrorOf 的值可以是某个仓库组的名称，也可以是*。表示从哪个仓库下载 jar 包。为*时表示从所有仓库组下载。

仓库组的 url，例如：http://localhost:8080/nexus/content/groups/public/

 

### **3.2** **将某个 jar 包上传私服中（需要权限）**

3.2.1 在用户 setting.xml 中配置

<server>

<id>仓库名</id>

<username>用户名</username>

<password>密码</password>



</server>

例如：

<server>

 

 

 

 

</server>



 

 

 

<id>3rd_party</id>

<username>admin</username>

<password>admin123</password>



 

3.2.2 打开cmd 输入指令mvn deploy:deploy-file

-DgroupId=g 坐标

–DartifactId=a 坐标

-Dversion=v 坐标

-Dfile=jar 包位置

-DrepositoryId=仓库名（也叫仓库 id）

-Durl=私服的仓库地址

 

实际写时，请写成一行指令。



例如：

mvn deploy:deploy-file -DgroupId=json-lib	– DartifactId=json-lib	-Dversion=2.2.2

-Dfile=json-lib-2.2.2-jdk15.jar	-DrepositoryId=3rd_party	- Durl=http://dev.qiyeclass.com:8081/repository/3rd_party/

 

 

### **3.3** **将当前工程打包后部署到私服中（需要权限）**

3.3.1 在用户 setting.xml 中配置上传仓库的 id，用户名，密码。同 3.2.1 

3.3.2 在当前工程的 pom.xml 文件中添加私服位置

第一个表示策略为release 的仓库地址，第二个表示策略为 snapshots 时的仓库地址。

```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <name>Internal Releases</name>
        <url>http://localhost:8080/nexus-2.7.0-06/content/repositories/ releases/</url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <name>Internal Snapshots</name>
        <url>http://localhost:8080/nexus-2.7.0-06/content/repositories/ snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```



 

3.3.3 然后执行mvn deploy 命令 进行发布。

 

# **四 常见问题注意事项**

 

1 在 nexus 上搜索搜不出 jar 包，但是仓库中确实有 jar 包。索引失效，覆盖即可。解决：覆盖掉~/sonatype-work\nexus\indexer\central-ctx 这个目录。

2 使用 nexus-boudl 版本搭建私服时，在cmd 窗口执行 nexus install 报错： wrapper | OpenSCManager failed - 拒绝访问。 (0x5)

解决：用管理员身份运行cmd 窗口再安装。3 在本地仓库找不到包或者在私服中找不到包提示。

发生此种原因，有可能是包真的不存在。也有可能是包存在了，但是使用不了。最好的解决办法。把本地仓库对应的该包清空，重新从中央仓库下载。或者

将私服中的对应包清空，从中央仓库下载。

4 如何清除本地仓库对应的某个包？ 直接删除。



5 明确仓库的 url，不要写成仓库组的 url。

组地址一般有个groups: nexus/content/groups/public/

仓库地址一般有个repositories： nexus/content/repositories/thirdparty/

仓库组的 url 一般是作为私服地址下载 jar 包使用。在 setting.xml 的通过

<mirror>

<id>nexus</id>

<mirrorOf>*</mirrorOf>

<name>Human Readable Name for this Mirror.</name>

<url>http://localhost:8081/nexus/content/groups/public/</url>

</mirror>

 

6 确保 setting.xml 的 server 配置正确。尤其是 id。

id 标签配置的是仓库 id。

在 web 页面上一般有：新建仓库组，和新建仓库等操作。在新建仓库的时候，一般需要指定仓库名和仓库 id。像 3.0 如果不指定仓库 id 的话， 

仓库名就是仓库 id。因此配置文件中的 id 要和页面上的仓库 id 对应。 

 

7 maven 无法从私服上下载更新 jar 包。有可能是.lastUpdate 结尾的文件导致，改文件记录的是最后更新时间，maven 就是通过它来判断一个 jar 是否需要重新下载。要是出问题， 可以删掉它尝试看能否解决问题。这个操作不会带来任何破坏性的影响。

 

8 上传的 jar 包的 策略 要正确

如果一个仓库的策略为 snapshot :意味着你的verson 命名必须是-snapshot 结尾的如果一个仓库的策略为release :	你的 verson 可以自由命名。比如：1.0

 

9 确定仓库组已经将仓库作为一个成员包含进去了。

假设你的在 settimg.xml 中配置的私服地址是 public 组。

你当前的私服中有一个 public 组和两个仓库分别是：test1 和test2 。你将 jar 包上传到test1 仓库中，但是你的 public 却没有引用test1 仓库。

那么你是无法在你工程引用到你刚刚上传的 jar 包的。即使你的gav 没有任何错误。