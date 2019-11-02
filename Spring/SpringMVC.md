# springMVC

### 开发步骤

1. 在web.xml文件中定义前端控制器DispatcherServlet来拦截与用户请求。

2. 如果需要以POST方式提交请求，则定义包含表单数据的JSP页面。如果仅仅只是以GET方式发送请求，则无需这一步。

3. 定义处理用户请求的Handle类，可以实现Controller接口或使用@Controller注解。

   DispatcherServlet也是前端控制器，该控制器负责接收请求，并将请求分发给对应的Handler，即实现Controller接口的Java类，而该Java类负责调用后台业务逻辑代码来处理请求。

| 问题                                                      | 解答                                                         |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| Controller并未接收到用户请求，如何处理                    | MVC框架的底层机制是：前端Servlet接收到用户请求后，通常会对用户请求进行简单预处理，例如解析、封装参数等，然后通过反射创建Controller实例，并调用Controller的制定方法（基于注解可以是任意方法）来处理用户请求。 |
| Servlet拦截请求后，如何知道创建那个Controller接口的实例呢 | 通过xml配置或者@RequestMapping(value=“/index”)配置           |

4. 配置handler，即编写对应controller类的业务逻辑代码。
5. 编写视图资源。



### 流程说明（原理）

Spring MVC 请求->响应的完整工作流程：

- 客户端（浏览器）发送请求，直接请求到DispatcherServlet截获
- DispatcherServlet对请求URI进行解析，得到URI。然后根据URI，调用HandlerMapping获得该Handler配置的所有相关的对象，包括Handler对象以及Handler对象对应的拦截器，这些对象会被封装到一个HandlerExecutionChain对象中返回
- DispatcherServlet根据获取的Handler，选择一个合适的HandlerAdapter。HandlerAdapter的设计符合面向对象中的单一职责原则，代码架构请求，便于维护，最重要的是，代码可复用性高。HandlerAdapter会被用于处理多种Handler，调用Handler实际处理请求的方法。
- 提取请求中的数据模型，开始执行Handler(Controller)。在填充Handler的入参过程中，根据配置，Spring将做一些额外的工作。

| 工作       | 具体内容                                                     |
| ---------- | ------------------------------------------------------------ |
| 消息转换   | 将请求消息（如json、xml等数据）转换成一个对象，将对象转换成指定的响应信息 |
| 数据转换   | 对请求信息进行数据转换，如spring转换成Integer、Double等      |
| 数据格式化 | 对请求信息进行数据格式化，如将字符串转化成数字或格式化日期等 |
| 数据验证   | 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中 |

- Handler执行完成后，向DispatcherServlet返回一个ModelAndView对象，ModelAndView对象中包含视图名或视图名和模型
- 根据返回的ModelAndView对象，选择一个合适的ViewResolver（视图解析器）返回给DispatcherServlet。
- ViewResolver结合Model和View来渲染视图
- 将视图渲染结果返回给客户端



### 特点

- SpringMVC拥有强大的灵活性、非侵入性和可配置性
- SpringMVC提供一个前端控制器DispatcherServlet，开发者无需额外开发控制对象
- SpringMVC分工明确，包括控制器、验证器、命令对象、模型对象、处理程序映射视图解析器等等，每个功能实现由一个专门的对象负责完成
- SpringMVC分工明确，包括控制器、验证器、命令对象、模型对象、处理程序映射视图解析器等等，每一个功能实现由一个专门的对象负责完成
- SpringMVC可以自动绑定用户输入，并正确地转换数据类型。 例如，SpringMVC能自动解析字符串，并将其设量为模型的int 或float 类型的属性。
- SpringMVC使用一个名称／值的Map对象实现更加灵活的模型数据传输。 
-  Spring MVC内置了常见的校验器， 可以校验用户输入，如果校验不通过， 则重定向回输入表单。 输入校验是可选的，并且支持编程方式及声明方式。 
-  Spring MVC支持国际化，支持根据用户区域显示多国语言，并且国际化的配置非常简 单。 
- Spring MVC支持多种视图技术， 最常见的有 JSP技术以及其他技术，包括Velocity 和 Freemarker 。 
- Spring 提供了一个简单而强大的JSP标签库，支持数据绑定功能 ， 使得编写JSP页面更加容易。 



### 注解开发

#### 常用注解

| 注解              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| @CrossOrigin      | 解决跨域问题                                                 |
| @Controller       | 用于定义控制器类                                             |
| @RestController   | 用于标注控制层组件（如struts中的action），@Controller和@ResponseBoby的合集 |
| @RequestMapping   | 提供路由信息，负责URL到Controller中的具体函数的映射          |
| @ResponseBody     | 该注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区 |
| @ModelAttribute   | 该方法定义使用@ModelAttribute注解：SpringMVC在调用目标 处理方法前 |
| @RequestParam     | 在处理方法入参处使用@RequestParam可以把请求参数传递给请求方法 |
| @PathVariable     | 绑定URL占位符到入参                                          |
| @ExceptionHandler | 注解到方法上，出现异常时会执行方法                           |
| @ControllerAdivce | 使一个Controller成为全局的异常处理类，类中用@ExceptionHandler注解的方法可以处理所有Controller发生的异常 |



#### @Controller

标记一个类是Controller类，分发器会扫描有该注解的类，然后检测该方法上是否有注解@RequestMapping注解，有才是真正处理请求的处理器，其中为业务逻辑代码。

配置文件配置自动扫描包

```xml
<context:component-scan base-package="com.demo.controller" />
```

##### 用于参数绑定的注解

| 分类                         | 注解                                   |
| ---------------------------- | -------------------------------------- |
| 处理request body部分的注解   | ＠RequestParam、 ＠RequestBody         |
| 处理request uri部分的注解    | ＠PathVariable                         |
| 处理request header部分的注解 | ＠RequestHeader、 ＠CookieValue        |
| 处理attribute类型的注解      | ＠SessionAttributes、 ＠ModelAttribute |

> 用于参数绑定的注解，如果没有参数会报空指针异常，可以给defaultValue=“”

###### ＠RequestParam、＠RequestHeader、＠CookieValue

通用支持的属性

| 属性         | 类型    | 是否必要 | 说明                           |
| ------------ | ------- | -------- | ------------------------------ |
| name         | String  | 否       | 指定请求头绑定的名称           |
| value        | String  | 否       | name属性的别名                 |
| required     | boolean | 否       | 只是参数是否必须绑定           |
| defaultValue | String  | 否       | 如果没有传递参数而使用的默认值 |

> 只用value时可以省略。@CookieValue(“userId”)

```java
@Controller
public class SessionAttributesController{
    @RequestMapping(value="/login")
    public String login(@RequestParam("username")String username,
                        @RequestHeader("User-Agent")String userAgent,
                        @CookieValue(value="JSESSIONID", defauleValue="") 
                        String sessionId,
                        @PathVariable Integer userid,
                        Model model){
        User user = new User();
        user.setUsername(username);
        model.setAttribute("user",user);
        return "welcome";
    }
}
```



###### @SessionAttributes

注解类型允许我们有选择地指定 Model中的哪些属性需要转存到HttpSession对象当中。

 注解支持的属性

| 属性  | 类型       | 是否必要 | 说明                                               |
| ----- | ---------- | -------- | -------------------------------------------------- |
| names | String[]   | 否       | Model中属性的名称，即存储在HttpSession中属性的名称 |
| value | String[]   | 否       | names属性的别名                                    |
| types | Class<?>[] | 否       | 指定绑定参数的类型                                 |

> @SessionAttributes只能声明在类上 ， 而不能声明在方法上。

```java
@Controller
／／将Model中的属性名为user的属性放入HttpSession对象当中
@SessionAttributes ("user") 
public class SessionAttributesController{
    @RequestMapping(value="/login")
    public String login(@RequestParam("username")String username,Model model){
        User user = new User();
        user.setUsername(username);
        //"user"中的user对象被加入HttpSession中
        model.setAttribute("user",user);
        return "welcome";
    }
}
```

```java
//添加对象的类型为User.class
@SessionAttributes(types={User.class}, value="user")
@SessionAttributes(types={User.class,Dept.class}, value={"user","dept"})
```



###### @ModelAttributes

该注解类型将请求参数绑定到Model对象

只支持一个value属性，表示被绑定对象的属性名称

> 被＠ModelAttribute注释的方法会在Controller每个方法执行前被执行， 因此 在一个 Controller映射到多个URL时， 要谨慎使用。

测试＠ModelAttribute(value＝“ ”）注释返回具体类的方法

```java
@Controller
public class ModelAttributeController{
    //使用＠ModelAttribute注释的value属性，来指定model属性的名称,model属性的值就是方法的返回值
    @ModelAttribute("loginname") 
    public String userModel1(@RequestParam("loginname") String loginname){
         return loginname; 
    }
    @RequestMapping(value="/login1") {
    public String loginl() { 
           return "result1";
    }

```

###### ＠ModelAttribute注释一个方法的参数

```java
@Controller
public class ModelAttribute5Controller{
//model 属性名称就是value 值, 即"user",model属性对象就是方法的返回值
    @ModelAt℃ribute("user") 
    public User userModel5(@RequestParam("loginname")String loginname,
                           @RequestParam("password") S℃ring password) { 
        User user = new User(); 
        user.setLoginname(loginname); 
        user.setPassword(password); 
        return user;
    }
     //@ModelAttribute ("user") User user注释方法参数，参数user的值就是userModel5() 
     //方法中的model属性。
     @RequestMapping(value="llogin5") 
     public String login5（@ModelAttribute("user") User user) { 
         user.setUsername("管理员");
         return "result5";
    }
}
```



##### @RequestMapping 注解支持的属性

| 属性     | 类型            | 是否必要 | 说明                                                         |
| -------- | --------------- | -------- | ------------------------------------------------------------ |
| value    | String[]        | 否       | 用于将指定请求的实际地址映射到方法上                         |
| name     | String          | 否       | 给映射地址指定一个别名                                       |
| method   | RequestMethod[] | 否       | 映射指定请求的方法类型，包括 GET、 POST、 HEAD、 OPTIONS、 PUT、 PATCH、DELETE、 TRACE |
| consumes | String[]        | 否       | 指定处理请求的提交内容类型（Content-Type），例如 application/ison、 text/html等 |
| produces | String[]        | 否       | 指定返回的内容类型，返回的内容类型必须是 request 请求头（Accept）中所包含的类型 |
| params   | String[]        | 否       | 指定 request 中必须包含某些参数值时，才让该方法处理          |
| headers  | String[]        | 否       | 指定 request 中必须包含某些指定的 header 值，才能让该方法处理请求 |
| Path     | String[]        | 否       | 在 Servlet 环境中只有：uri 路径映射（例如 “/myPath.do”。也支持如 ant 的基于路径模式（例如 “/myPath/ ＊ ，”）。在方法层面上，支持相对路径（例如“ editor.do“ |



##### 返回数据JSON数据类型

```java
@Controller
@RequestMapping("/json")
public class BookController { 
         @RequestMapping(value＝"/testRequestBody"
         //@ResponseBody会将集合数据转换为json格式并将其返回客户端
         @ResponseBody 
         public Objece getJson() { 
             List<Book> list = new ArrayList＜Book>(); 
             list.add(new Book(l,"Spring MVC企业应用实战","肖文吉");
             list.add(new Book(2,"轻量级JavaEE企业应用实战","李刚"));
             return list; 
         }
   }
```

```javascript
$(document) .ready(function(){
    testResponseBody(); 
}); 
function testResponseBody() {
   $.post("${pageContext.request.contextPath}/json/testRequestBody",null,
           function(data) { 
        $.each(data,function() {
            var tr = $("<tr align＝'center'/>"); 
            $("<td/>").html(this.id).appeηdTo(tr); 
            $("<td/>").html(this.name).appeηdTo(tr); 
            $("<td/>").html(this.author).appeηdTo(tr); 
            $ ("#booktable") .append(tr); 
        })
    },"json");
```

| 函数                                            | 用法                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| \$.post("url",null, function(data) { },"json"); | 参数一：ajax post请求url;参数二：需要传递的数据；参数三：处理返回数据的函数；参数四：要求返回数据的类型 |
| $.each(data,function() {}                       | jQuery遍历函数，遍历数组或者集合                             |

>  数据转换接口HttpMessageConverter
>
> json默认采用jackjson，如果改为fastjson需要配置数据转换接口
>
> xml解析采用JAXB

```java
public void parseXML(){
    JAXBContext context = JAXBContext.newInstance(Book.class);
    InputStream is = this.getClass().getResouceAsStream("/web.xml");
    Unmarshaller unm = context.createUnmarshaller();
    Book book = num.unmarshal(is);
}
```

##### 异常处理Controller

```java
@ControllerAdvice
public class ExceptionController {

    @ExceptionHandler(Exception.class)
    public String error(Exception e, Model model){
        System.out.println(e.getMessage());
        model.addAttribute("message",e.getMessage());
        return "/error";
    }

    @ModelAttribute
    //应用到所有@RequestMapping注解方法
    //此处将键值对添加到全局，注解了@RequestMapping的方法都可以获得此键值对
    public void addUser(Model model) {
        model.addAttribute("user", null);
    }

}
```

> @ControllerAdvice注解在类上，表明这个类为增强类
>
>  @ExceptionHandler(Exception.class)注解在方法上，这是一个出现异常会执行的方法



### SpringMVC提供JSP标签库

##### form标签

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
 <form:form cssClass="layui-form" action="">
        <div class="layui-form-item">
          <div class="layui-inline">
            <label class="layui-form-label">用户名</label>
            <div class="layui-input-inline">
              <form:input lay-verify="required" autocomplete="off" cssClass="layui-input" path="username" />
            </div>
          </div>
        </div>
        <div class="layui-form-item">
          <div class="layui-inline">
            <label class="layui-form-label">密码</label>
            <div class="layui-input-inline">
              <form:input lay-verify="required" autocomplete="off" cssClass="layui-input" path="password"/>
            </div>
          </div>
        </div>
        <div class="layui-form-item">
          <div class="layui-input-block">
            <button class="layui-btn" lay-submit="" lay-filter="demo1">立即提交</button>
            <button type="reset" class="layui-btn layui-btn-primary">重置</button>
          </div>
        </div>
 </form:form>
```

| 标签             | 属性（必选）                                                 | 其他属性                     |
| ---------------- | ------------------------------------------------------------ | ---------------------------- |
| \<form:form>     | modelAttribute,commandName：form绑定的模型属性名称，默认为command | cssClass,cssStyle,htmlEscape |
| \<form:input>    | path：要绑定的属性路径                                       |                              |
| \<form:password> | path：要绑定的属性路径                                       |                              |



#### SpringMVC的文件上传与下载

##### 文件上传

前端表单类型

```html
<form action="" method="" enctype="multipart/form-data"></form>
```

后端处理

```java
//上传文件会自动绑定到MultipartFile中
@RequestMapping("/upload",method=RequestMethod.POST)
public String upload(HttpServletRequest request,
                    @RequestParam("description")String description,
                    @RequestParam("file")MultipartFile file) throws Excetion{
    //如果文件不为空，写入上传路径
    if(!file.isEmpty()){
        String path = request.getServletContext().getRealPath("/images");
        String filename = file.getOriginalFilename();
        File filepath = new File(path,filename);
        //判断路径是否存在，如果不存在就创建一个
        if(!filepath.getParentFile().exists()){
            filepath.getParentFile().mkdirs();
        }
        //将上传文件保存到一个目标文件当中
        file.transferTo(new File(path+File.separator+filename));
        return "success";
    }else{
        return "error";
    }
}
```

###### MultipartResolver常用方法（MultipartFile file中file的方法）

| 方法                             | 用途                              |
| -------------------------------- | --------------------------------- |
| byte[] getBytes()                | 获取文件数据                      |
| String getContentType()          | 获取文件MIME类型，如images/jpeg等 |
| InputStream getInputStream()     | 获取文件流                        |
| String getName()                 | 获取表单中文件组件的名字          |
| **String getOriginalFilename()** | 获取上传文件的原名                |
| long getSize()                   | 获取文件的字节大小，单位为byte    |
| boolean isEmpty()                | 是否有上传的文件                  |
| **void transferTo(File dest)**   | 将上传文件保存到一个目标文件中    |



##### 文件下载

```java
@RequestMapping("/download")
public ResponseEntity<byte[]> download(HttpServletRequest request,
                                      @RequestParam("filename")String filename,
                                      Model model)throws Exception{
    //下载文件路径
    String path = request.getServletContext().getRealPath("/images");
    File file = new File(path+File.separator+filename);
    HttpHeader headers = new HttpHeader();
    //下载显示的文件名，解决中文名称乱码问题
    String downloadFileName = new String(filename.getBytes("UTF-8"),"ISO8859-1");
    //通知浏览器以attachment方式下载
    headers.setContentDispositionFormData("attachment",downloadFileName);
    //application/octet-stream；二进制流数据（最常见的文件下载）
    headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
    //201 HttpStatus.CREATED
    return new ResponseEntity<byte[]>(FileUtils.readFileToByteArray(file),headers,HttpStatus.CREATED);
}
```

> 使用ResponseEntity可以很方便的返回HttpHeaders和HttpStatus.
>
> MediaType即互联网媒体类型，也叫MIME类型



### 拦截器

主要作用是拦截用户的请求并进行相应的处理。比如通过拦截器来进行用户权限验证，或者用来判断用户是否已经登录等。

SpringMVC中的Interceptor拦截器拦截请求是通过实现HandlerInterceptor接口来完成的或者继承HandlerInterceptorAdater。

#### 编写拦截器

```java
/**
 * 拦截器必须实现HandlerInterceptor
 *
 * @author AlmostLover
 */
public class AuthorityInterceptor implements HandlerInterceptor {
    private static final String[] IGNORE_URI = {"/login"};

    /**
     * 该方法在整个请求完成之后执行，主要作用是用于清理资源
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("AuthorityInterceptor afterCompletion --> ");
    }

    /**
     * 该方法在controller的方法调用之后执行，方法中可以对ModelAndView进行操作
     * 该方法也能在当前Interceptor的preHandle方法的返回值为true时才会执行
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("AuthorityInterceptor postHandler --> ");
    }

    /**
     * preHandler方法是进行处理器拦截用的，该方法将在controller处理之前进行调用，
     * 该方法的返回值为true拦截器才会继续往下执行，该方法的返回值为false的时候整个请求就结束了
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("AuthorityInterceptor preHandler --> ");
        //flag变量用于判断用户是否登录，默认为false
        boolean flag = false;
        //获取请求的路径进行判断
        String servletPath = request.getServletPath();
        //判断请求是否需要拦截
        for (String s : IGNORE_URI) {
            if(servletPath.contains(s)){
                flag = true;
                break;
            }
        }
        //拦截请求
        if(!flag){
            //1.获取session中的用户
            TdUser user = (TdUser) request.getSession().getAttribute("user");
            //2.判断用户是否已经登录
            if(user==null){
                //如果用户没有登录，则设置提示信息，跳转到登录界面
                System.out.println("AuthorityInterceptor 拦截请求");
                request.setAttribute("message","请先登录再访问网站");
                request.getRequestDispatcher("login").forward(request,response);
            }else {
                //如果用户已经登录，则验证通过，放行
                System.out.println("AuthorityInterceptor 放心请求：");
                flag = true;
            }
        }
        return flag;
    }
}
```

#### springmvc-config.xml文件配置

```xml
<!-- SpringMVC 拦截器定义 -->
<mvc:interceptors>
    <mvc:interceptor>
        <!-- 拦截所有请求 -->
        <mvc:mapping path="/*" />
        <!-- 使用bean定义一个Interceptor 自定义Interceptor的路径 -->
        <bean class="org.fkit.interceptor.AuthorizationInterceptor" />
    </mvc:interceptor>
</mvc:interceptors>
```

### 过滤器







### springMVC拦截器（过滤器、监听器的区别）

- 拦截器是指通过统一拦截从浏览器发往服务器的请求来完成功能的增强
- 使用场景：解决请求的共性问题（乱码问题、权限验证问题）
- 过滤器
  - Servlet中的过滤器Filter是实现了javax.servlet.Filter接口的服务器端程序，主要用途是过滤字符编码、做一些业务逻辑判断等。其工作原理是，只是你在web.xml文件配置好要拦截的客户端请求，他会帮你拦截到请求，此时你就可以对请求或响应（Request、Response）统一设置编码，简化操作；同时还可以进行逻辑判断，如用户是否已经登录、有没有权限访问该网页等等工作。他是随着你的web启动而启动的，只初始化一次，以后就可以拦截相关请求，只是当你的web应用停止或重新部署的时候才销毁
- 监听器
  - Listener，它是实现了javax.servlet.ServletContextListener接口的服务器端程序，它也是随着web应用的启动而启动，只初始化一次，随web应用的停止而销毁。主要作用是：做一些初始化的内容添加工作、设置一些基本的内容，比如一些参数或者一些固定的对象等等。
- 拦截器
  - 拦截器是面向切面编程中应用的，就是你的service或者一个方法前调用一个方法，或者在方法后调用一个方法。是基于JAVA的反射机制。拦截器不是在web.xml

### springMVC request接收设置是线程安全的吗

- 是线程安全的，request、response以及requestcontext在使用时不需要进行同步，而根据psirng的默认规则，controller对于beanfactory而言是单例的，即controller只有一个，controller中的request等实例对象也只有一个

