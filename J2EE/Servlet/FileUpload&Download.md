# 文件上传

### 前端文件上传方案

- form表单post同步提交

```html
<form method="post" >
    <input type="file" name="filename">
    <input type="text" name="username">
</form>
```



- 用XHR对象的post提交

可以选择原生的XmlHttpServlet对象，不过为了简化以及解决浏览器兼容性问题。一般选择jQuery的Ajax实现，之所以选择Ajax函数而不选择post方法在于Ajax方法可以灵活定制请求头。

```html
<script>
$(document).ready(function(){
		var uploading = false;
		$("#ctn-input-file").on("change", function(e){
		    if(uploading){
		        alert("文件正在上传中，请稍候");
		        return false;
		    }
		    var formdata = new FormData();//form表单。
		    var file = e.target.files[0];//e.target == document.getElementById("ctn-input-file")
		    //
		    formdata.append("file",file );
		    //formdata.append("file2","file2");//多文件上传。
		    $.ajax({
		        url: "fileupload4",
		        type: 'POST',
		        cache: false,
		        data: formdata,//传到请求体的数据。如果是文件，浏览器会自动设置：
		        				//Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryfxmGQVueQFHdZmT2
		        processData: false, //默认：{age:20,password:123} ==> age=20&password=123
		        contentType: false,//一定不要设置。因为默认是application/x-www-form-urlencoded。文件上传必须是multipart/form-data
		        beforeSend: function(){
		            uploading = true;
		        },
		        success : function(data) {
		            uploading = false;
		        }
		    });
		});
	});
</script>
```





### 后台三种方案

| 方案                                                         | 优点     | 缺点 |
| ------------------------------------------------------------ | -------- | ---- |
| 使用字符串解析，手动解析                                     | 定制性强 | 麻烦 |
| 使用第三方文件上传工具包，Apache Commons-FileUpload 工具包   |          |      |
| Servlet3.0 规范中已经加入了对文件上传的支持。具体是Request.getParts()方法。所以Servlet容器只要支持3.0规范，就可以支持文件上传的解析。 |          |      |



#### 请求报文

##### 请求头

```
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryDZUHI8tCmSJYkmw2
```

| Content-Type                                    |                  |
| ----------------------------------------------- | ---------------- |
| multipart/form-data                             | 文件上传表单类型 |
| boundary=----WebKitFormBoundaryDZUHI8tCmSJYkmw2 | 边界分隔符       |



##### 请求体

```
------WebKitFormBoundaryDZUHI8tCmSJYkmw2
Content-Disposition: form-data; name="file"; filename="生日蛋糕订购子系统.mp4"
Content-Type: video/mp4


------WebKitFormBoundaryDZUHI8tCmSJYkmw2
Content-Disposition: form-data; name="md5"

d20c1f0b2ec64c96c7d57a54d751ce8b
------WebKitFormBoundaryDZUHI8tCmSJYkmw2--
```

| 解析                                                         | 作用                         |
| ------------------------------------------------------------ | ---------------------------- |
| ------WebKitFormBoundaryDZUHI8tCmSJYkmw2                     | 分隔符                       |
| ------WebKitFormBoundaryDZUHI8tCmSJYkmw2--                   | 结束分隔符                   |
| Content-Disposition: form-data; name="file"; filename="生日蛋糕订购子系统.mp4"<br/>Content-Type: video/mp4 | 文件域，二进制数据内容不展示 |
| Content-Disposition: form-data; name="md5"<br/>d20c1f0b2ec64c96c7d57a54d751ce8b | 表单域，显示内容             |
| <br />                                                       | 每个域之间存在空行           |



#### 文件域

```http
------WebKitFormBoundaryDZUHI8tCmSJYkmw2
Content-Disopsition:form-data; name="file"; filename="生日蛋糕订购子系统.mp4"
Content-Type: video/mp4

```

#### 表单域

```http
------WebKitFormBoundaryDZUHI8tCmSJYkmw2
Content-Disposition:form-data;name="password"

d20c1f0b2ec64c96c7d57a54d751ce8b
```

   

### Apache Comments-FileUpload工具包

[官网下载]()

使用方法

**FileItem相当于Servlet中的Part**

3 获取表单

获取Name ： FileItem.getFieldName()

获取value  :   FileItem.getString(“Charset”);

 

4 获取文件

获取文件Input输入框的Name属性： FileItem.getFieldName()

获取文件上传时的文件名：

request.setCharacterEncoding(“charset”);//没有办法设置文件内容本身的编码。

FileItem.getName();

获取文件内容：

当文件是文本文件时，可以按照一定的编码来**直接内容。**

Fileitem.getString(“charset”);

​    也可以通过流的方式来读取文件内容：

​    FileItem.getInputStream();

5 使用临时目录

现有如下代码：

​		rq.setCharacterEncoding("UTF-8");

​		DiskFileItemFactory factory = new DiskFileItemFactory(); 

​		File repository = new File("D:/upload_temp/");

​		factory.setRepository(repository);

获取文件属性

| 方法                    | 作用                             |
| ----------------------- | -------------------------------- |
| FileItem.getFieldName() | 获取文件域的名称                 |
| FileItem.getString()    | 获取文件内容或者表单域中的内容   |
| FileItem.getSize()      | 获取文件域内容或表单域内容的大小 |
| FileItem.isFormFile()   | 判断是否为表单域，返回boolean    |

> 编码格式转化问题：
>
> 1. re.setCharacterEncoding(“charset”)设置头部信息编码
>2. FileItem.getString(“charset”)设置文件内容编码



| 实验描述                                     | 结果                                                  |
| -------------------------------------------- | ----------------------------------------------------- |
| 上传两个小文件。分别为289B   68B             | 临时目录没有被创建出来                                |
| 稍微大一点的文件。都是分别是808B，一个是3.6M | 3.6M的文件，在临时目录中生成了一个文件。后缀名：.temp |



使用临时目录和不使用临时目录的对比：

| 不使用临时目录 | 文件从 前端---->后台。所有文件存入后台服务器内存。           | 优点：快速。缺点：耗内存。                         |
| -------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 使用临时目录   | 文件从 前端---->后台内存--->硬盘临时目录。 后台内存只是一个中转站，可以用很小的内存来中转。 | 优点：不用担心空间不够的问题。缺点：需要二次拷贝。 |

正确使用临时目录的姿势：

![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps5.jpg) 

结论：大文件会在临时目录生成临时文件，小文件不会生成。可以通过方法设定threshold 的值

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\web开发\Servlet\wps6.jpg)



### Servlet3.0文件上传

1. 添加注解**@MultipartConfig**

2. @MultipartConfig的location属性指定的是工作目录。

   - 如果有4个域（表单域 + 文件域），在工作目录就会生成。
   - 对应的4个临时文件。这些临时文件默认不会被删除。
   - 以下情况会被删除：
     -  调用了part.write()
     -  Part.delete(); //不起作用。                              

3. HttpServletRequest对象中获取Part

   Rq.getParts();                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       

### 总结

| 问题                                                         | 结论                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 文件选择框，能否多选？                                       | 不能。如果要选择多个，进行浏览器插件开发                     |
| 文件内容如果出现了和boundary一样的内容怎么办？               | 不可能。浏览器在设置boundary之前，需要读取整个请求体中要上传的所有文件的内容。既然知道了文件的内容，可以通过算法来规避这个问题。基本思路：额外处理（ base64( md5(file)) ） |
| 不能通过rq.getParameter(“name”);来获取表单域的值，那么怎么获取？ | 只有解析了整个message-body中的每个part。才能获取             |
| 文件上传是不是只有表单域和文件域？                           | 是的。                                                       |
| Content-Length是不是文件的总大小？                           | 不是。Content-Length只表示，Message-body的总字节数。每个文件大小，需要解析了之后才能知道有多大。所以这也是HTTP协议的一个弊端。所以HTTP协议不应当用于传输大文件。大文件传输，请考虑其它协议。例如：FTP，TCP。 |





# 文件下载

Step1：输入两个响应头：

Content-Type: application/octet-stream;

Content-Disposition: attachment;filename=ABC.txt;

Step2： 将文件的数据写到响应体中。

> 温馨提醒：
>
> 文件下载只支持单文件下载，如需下载多文件。可以选择用ZIPOutputStream将多文件或目录压缩为一个最终的压缩文件。
>
>  文件下载也只是一次正常的HTTP响应。与其它响应不同的是，由于添加了上述头部。浏览器一般会弹出文件下载框，提示用于是否下载，以及选择要下载的保存路径。





# Javascript计算文件MD5

### 需求

在前端上传文件到服务端时，如果有必要（例如网盘系统），需要实现秒传功能。也就是提前算一下MD5，拿到数据库对比。很明显，不管是B/S端还是C/S端系统，为了提高性能和效率，计算文件MD5值的这一步，必须在B端或者C端实现。对于B/S结构项目，需要使用javascript计算文件上传时的MD5值。

由于ECMAScript草案和H5标准目前都没有直接支持计算文件的MD5，所以只能手动计算。

###### **思路**

计算md5就是经过如下过程：

文件====》byte [ ] ===> MD5计算 ====》128位MD5三列码或32位ASCII字符串

###### **Javascript 文件支持**

对于前端而言，H5提供了FileReader可以将文件转换为byte类型的操作。详情参考：

JavaScript高级程序设计（第3版）

![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps7.jpg) 

以上方法用于读取一个文件的全部内容。但对于计算MD5值而言，假设文件有500M，需要耗费500M的内存空间。由于浏览器的内存分配比较苛刻，不大可能分配大内存。所以可以采用分段的方式读取。也就是通过循环来读取部分文件内容。

参考：25.4.2

![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps8.jpg) 

 

###### **MD5值的计算方式**

各个编程语言提供的一般都支持两种方式：整体文件计算、流式计算。

整体计算的弊端，就是需要将所有的数据一次性通过转为字节数组的方式提供。

流式计算的方式则支持，分块传输。这是利用MD5算法的支持实现的。

推荐使用流式计算。

例如：java的MessageDigest

byte[] digest(byte []) 实现的就是整体计算。必须要传入一个大数组。

而另一个方法

 public void update(byte[] input, int offset, int len)  提供的就是一种流式计算。

 再结合无参的 public byte[] digest() 方法 算出最终三列码。

 

###### **Javascript两种方案计算MD5**

由于javascript的H5的FileReader接口，均实现了读取全部文件和部分内容的功能。

因此javascript也可以使用两种方式来计算MD5。

**方案一（了解）**

对于采用整体计算的方式来计算文件MD5，步骤如下：

Step1：  先获取File对象。一般是通过dom对象的.files属性获取。该属性表示一个type=file		的input表单的文件对象。

var file = document.getElementById(“fileinput”).files[0]

Step2：  创建FileReader对象。var fr= new FileReader();

Step3： 转为字节。fr.readAdBinaryString(file);  

Step4:   var file_md5 = md5(fr.result);

其中：md5表示一个javascript函数实现，用于传入一个大字节字符串。返回一个md5的32位ASCII码。对于次种方案，可以参考百度百科 给出的javascript计算MD5方案：

https://baike.baidu.com/item/MD5/212708?fr=aladdin

> 由于整体计算比较消耗内存，不推荐使用。所以简单介绍。

方案二：使用开源的js-spark-md5**（推荐，掌握）**

[下载地址](https://github.com/satazor/js-spark-md5#readme)

```javascript
document.getElementById('file').addEventListener('change', function () {
    var blobSlice = File.prototype.slice || File.prototype.mozSlice || File.prototype.webkitSlice,
        file = this.files[0],
        chunkSize = 2097152,                             // Read in chunks of 2MB
        chunks = Math.ceil(file.size / chunkSize),
        currentChunk = 0,
        spark = new SparkMD5.ArrayBuffer(),
        fileReader = new FileReader();
 
    fileReader.onload = function (e) {
        console.log('read chunk nr', currentChunk + 1, 'of', chunks);
        spark.append(e.target.result);                   // Append array buffer
        currentChunk++;
 
        if (currentChunk < chunks) {
            loadNext();
        } else {
            console.log('finished loading');
            console.info('computed hash', spark.end());  // Compute hash
        }
    };
 
    fileReader.onerror = function () {
        console.warn('oops, something went wrong.');
    };
 
    function loadNext() {
        var start = currentChunk * chunkSize,
            end = ((start + chunkSize) >= file.size) ? file.size : start + chunkSize;
 
        fileReader.readAsArrayBuffer(blobSlice.call(file, start, end));
    }
 
    loadNext();
});
```



# SpringBoot文件上传与下载

设置默认上传大小

在配置文件application.properties中添加

```properties
# 修改默认文件上传大小
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=100MB
```

前端先采用js-spark-md5计算文件的md5值，如果相同则不进行第二次上传

1. 上传文件控制器

```java
@PostMapping("/upload")
    public String upload(@RequestParam("file") MultipartFile file, @RequestParam("md5") String md5) {
        if (file.isEmpty()) {
            return "文件上传失败";
        }
        //获取文件名和后缀
        String filename = file.getOriginalFilename();
        assert filename != null;
        String suffix = filename.substring(filename.indexOf("."));
        String filePath = "C:\\Users\\AlmostLover\\Desktop\\fileUpload\\";
        String newPath = filePath + md5 + suffix;
        //判断文件是否已经上传
        List<TdFile> file1 = fileMapper.findByMD5(md5);
        if (file1.size() == 0) {
            File dest = new File(newPath);
            try {
                file.transferTo(dest);
                fileMapper.upload(new TdFile(md5, newPath, filename));
                return "上传成功";
            } catch (IOException e) {
                e.printStackTrace();
            }
            return "上传失败";
        } else {
            fileMapper.upload(new TdFile(md5, file1.get(0).getPath(), filename));
            return "文件已经存在，秒传成功";
        }
    }
```

2. 下载文件控制

```java
@GetMapping("/download")
    public ResponseEntity<Resource> download(String md5) throws IOException {
        if (md5 != null) {
            List<TdFile> file = fileMapper.findByMD5(md5);
            // 获取文件名称，中文可能被URL编码
            String filename = URLDecoder.decode(file.get(0).getFilename(), "UTF-8");
            // 获取本地文件系统中的文件资源
            FileSystemResource resource = new FileSystemResource(file.get(0).getPath());
            // 解析文件的 mime 类型
            String mediaTypeStr = URLConnection.getFileNameMap().getContentTypeFor(filename);
            // 无法判断MIME类型时，作为流类型
            mediaTypeStr = (mediaTypeStr == null) ? MediaType.APPLICATION_OCTET_STREAM_VALUE : mediaTypeStr;
            // 实例化MIME
            MediaType mediaType = MediaType.parseMediaType(mediaTypeStr);

            /*
             * 构造响应的头
             */
            HttpHeaders headers = new HttpHeaders();
            headers.setContentDispositionFormData("attachment", URL.encode(filename));
            headers.setContentType(mediaType);
            return ResponseEntity.ok()
                    .headers(headers)
                    .contentLength(resource.getInputStream().available())
                    .body(resource);
        }
        return null;
    }
```

>注意： `@GetMapping("/download/{fileName:.*}")`中的`{fileName:.*}`可以这么写，是因为SpringMVC会忽略`.`后面的内容，这样就可以完整的得到整个文件。
>
>URL.encode(filename)下载时响应头的文件名需要编码，否则中文文件名下载时显示不出来。