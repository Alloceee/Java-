# ElasticSearch

### Windows下安装elasticsearch

在安装Elasticsearch引擎之前，必须安装ES需要的软件环境

注意：安装elasticsearch7.4运行需要jdk11及以上

elasticsearch与jdk有依赖关系

1. [下载地址](https://www.elastic.co/cn/downloads/)

2. 配置环境变量

![img](https://img-blog.csdn.net/20180921174644360?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3. 测试运行

点击/bin目录下的elasticsearch.bat开始安装

### 安装elasticsearch的head插件

 es5以上版本安装head需要安装node和grunt(之前的直接用plugin命令即可安装) 

 (一)从地址：https://nodejs.org/en/download/ 下载相应系统的msi，双击安装。
![img](https://img-blog.csdn.net/20180921174729508?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 



 （二）安装完成用cmd进入安装目录执行 node -v可查看版本号
![img](https://img-blog.csdn.net/20180921174744921?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

（三）执行 npm install -g grunt-cli 安装grunt ，安装完成后执行grunt -version查看是否安装成功，会显示安装的版本号
![img](https://img-blog.csdn.net/20180921174758791?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

（四）开始安装head
① 进入安装目录下的config目录，修改elasticsearch.yml文件.在文件的末尾加入以下代码

http.cors.enabled: true

http.cors.allow-origin: "*"

node.master: true

node.data: true

然后去掉network.host: 192.168.0.1的注释并改为network.host: 0.0.0.0，去掉cluster.name；node.name；http.port的注释（也就是去掉#）

 elasticsearch-7.1.1 版本 yml文件需要这个 cluster.initial_master_nodes: ["node-1"] 

②双击elasticsearch.bat重启es
③在https://github.com/mobz/elasticsearch-head中下载head插件，选择下载zip
![img](https://img-blog.csdn.net/20180921174815652?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

④解压到指定文件夹下，G:\elasticsearch-6.4.1\elasticsearch-head-master 进入该文件夹，修改G:\elasticsearch-6.4.1\elasticsearch-head-master\Gruntfile.js 在对应的位置加上hostname:'*'
![img](https://img-blog.csdn.net/20180921174843354?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

⑤在G:\elasticsearch-6.6.2\elasticsearch-head-master 下执行npm install 安装完成后执行grunt server 或者npm run start 运行head插件，如果不成功重新安装grunt。成功如下
![img](https://img-blog.csdn.net/2018092117485988?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

⑥浏览器下访问http://localhost:9100/
![img](https://img-blog.csdn.net/20180921174914941?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmtrYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 整合springboot

参考博客 https://segmentfault.com/a/1190000018625101?utm_source=tag-newest 

 https://blog.csdn.net/chen_2890/article/details/83895646 

1. properties

```properties
spring.data.elasticsearch.cluster-name=elasticsearch
spring.data.elasticsearch.cluster-nodes=127.0.0.1:9300
spring.data.elasticsearch.repositories.enabled=true
```

2. pom.xml

```xml
 		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
        <!--集合工具包-->
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>19.0</version>
        </dependency>
```

3. ```java
   	/**
        *
        * @Description:matchQuery
        *@Author: https://blog.csdn.net/chen_2890
        */
       @Test
       public void testMathQuery(){
           // 创建对象
           NativeSearchQueryBuilder queryBuilder = new NativeSearchQueryBuilder();
           // 在queryBuilder对象中自定义查询
           //matchQuery:底层就是使用的termQuery
           queryBuilder.withQuery(QueryBuilders.matchQuery("title","坚果"));
           //查询，search 默认就是分页查找
           Page<Item> page = this.itemRepository.search(queryBuilder.build());
           //获取数据
           long totalElements = page.getTotalElements();
           System.out.println("获取的总条数:"+totalElements);
      
           for(Item item:page){
               System.out.println(item);
           }
    }
    ```

   


```java
   /**
    * @Description:
    * termQuery:功能更强大，除了匹配字符串以外，还可以匹配
    * int/long/double/float/....	
    * @Author: https://blog.csdn.net/chen_2890			
    */
   @Test
   public void testTermQuery(){
       NativeSearchQueryBuilder builder = new NativeSearchQueryBuilder();
       builder.withQuery(QueryBuilders.termQuery("price",998.0));
       // 查找
       Page<Item> page = this.itemRepository.search(builder.build());
   
       for(Item item:page){
           System.out.println(item);
       }
   }
```
   
```java
/**
     * @Description:布尔查询
     * @author: https://blog.csdn.net/chen_2890
     */
    @Test
    public void testBooleanQuery() {
        NativeSearchQueryBuilder builder = new NativeSearchQueryBuilder();    builder.withQuery(QueryBuilders.boolQuery().must(QueryBuilders.matchQuery("title","华为")).must(QueryBuilders.matchQuery("brand", "华为")));
        // 查找
        Page<Item> page = this.itemRepository.search(builder.build());
        for (Item item : page) {
            System.out.println(item);
        }
    }
```

  ```java
/**

   * @Description:模糊查询
     uthor: https://blog.csdn.net/chen_2890			
             */
            @Test
            public void testFuzzyQuery(){
     NativeSearchQueryBuilder builder = new NativeSearchQueryBuilder();
     builder.withQuery(QueryBuilders.fuzzyQuery("title","faceoooo"));
     Page<Item> page = this.itemRepository.search(builder.build());
     for(Item item:page){
         System.out.println(item);
     }

   } 
  ```

