

# Thymeleaf

## 标准语法：

| 语法 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| ${}  | 变量表达式，用于在展示变量的值，如果是对象，需要获取属性的值${object.attribute} |
| *{}  | 选择变量表达式，用于展示已选对象的属性值                     |
| #{}  | 文本信息表达式，用于展示资源文件中定义的信息，可以添加变量#{string(${})} |
| @{}  | 链接表达式，用于链接其他文件或者控制器                       |
| ~{}  | 片段表达式                                                   |



## ${}&*{}

**控制器**

```java
@RequestMapping(value = "/message/member_show", method = RequestMethod.GET)
public String memberShow(Model model) {
    User vo = new User();
    vo.setUid(12345678L);
    vo.setName("尼古拉丁.赵四");
    vo.setAge(59);
    vo.setSalary(1000.00);
    vo.setBirthday(new Date());
    model.addAttribute("member", vo);
    return "message/member_show";
}
```

**变量以及选择变量表达式**

```html
<div>
    <p th:text="'用户编号：' + ${member.uid}"/>
    <p th:text="'用户姓名：' + ${member.name}"/>
    <p th:text="'用户年龄：' + ${member.age}"/>
    <p th:text="'用户工资：' + ${member.salary}"/>
    <p th:text="'出生日期：' + ${member.birthday}"/>
    <p th:text="'出生日期：' + ${#dates.format(member.birthday,'yyyy-MM-dd')}"/>
</div>

<hr/>
<div th:object="${member}">
    <p th:text="'用户编号：' + *{uid}"/>
    <p th:text="'用户姓名：' + *{name}"/>
    <p th:text="'用户年龄：' + *{age}"/>
    <p th:text="'用户工资：' + *{salary}"/>
    <p th:text="'出生日期：' + *{birthday}"/>
    <p th:text="'出生日期：' + *{#dates.format(birthday,'yyyy-MM-dd')}"/>
</div>
```

**文本信息表达式**

如果我们要显示的信息是存在资源文件中的，同样可以在页面中显示，例如资源文件中定义了内容`welcome.msg=欢迎{0}光临！`

```html
<h2 th:text="#{welcome.msg('admin')}"/>
```

为了给我们的参数指定一个值，并给定一个调用的HTTP会话属性`user`，我们可以：

```html
<p th:utext="#{home.welcome(${session.user.name})}">
  Welcome to our grocery store, Sebastian Pepper!
</p>
```

>  请注意，使用`th:utext`此处意味着格式化的消息不会被转义。此示例假定`user.name`已经转义。

**链接表达式**

1. 在页面中可以通过“@{路径}”来引用。

```html
<script type="text/javascript" th:src="@{/js/main.js}"></script> 
```

2. 页面之间的跳转也能通过@{}来实现

```html
<a th:href="@{/show}">访问controller方法</a>
<a th:href="@{/static_index.html}">访问静态页面</a>
```

3. 参数的传递，通过（param1=value,param2=value）

```html
<div class="layui-card layui-col-md6" th:each=" project : ${projects}">
                        <div class="layui-card-header" th:text="${project.title}"></div>
                        <div class="layui-card-body">
                            完成进度：
                            <a th:href="@{/project/show(id=${project.id})}" th:class="layui-btn">查看详情</a>
                        </div>
                    </div>
```



**片段表达式**

表示标记片段并在模板周围移动它们的简单方法。这允许我们复制它们，将它们作为参数传递给其他模板，依此类推。

最常见的用途是使用`th:insert`或进行片段插入`th:replace`（在后面的部分中将详细介绍）：

```html
<div th:insert="~{commons :: main}">...</div>
```

但它们可以在任何地方使用，就像任何其他变量一样：

```html
<div th:with="frag=~{footer :: #main/text()}">
  <p th:insert="${frag}">
</div>
```



## 数据处理

```html
<p th:text="${#dates.format(mydate,'yyyy-MM-dd')}"/>
    <p th:text="${#dates.format(mydate,'yyyy-MM-dd HH:mm:ss.SSS')}"/>
    <hr/>
    <p th:text="${#strings.replace('www.baidu.cn','.','$')}"/>
    <p th:text="${#strings.toUpperCase('www.baidu.cn')}"/>
    <p th:text="${#strings.trim('www.baidu.cn')}"/>
    <hr/>
    <p th:text="${#sets.contains(names,'boot-0')}"/>
    <p th:text="${#sets.contains(names,'boot-9')}"/>
    <p th:text="${#sets.size(names)}"/>
    <hr/>
    <p th:text="${#sets.contains(ids,0)}"/>
    <p th:text="${ids[1]}"/>
    <p th:text="${names[1]}"/>
```



## 逻辑处理

所有的页面模版都存在各种基础逻辑处理，例如：判断、循环处理操作。在 Thymeleaf 之中对于逻辑可以使用如下的一些运算符来完成，例如：and、or、关系比较（>、<、>=、<=、==、!=、lt、gt、le、ge、eq、ne）。

通过控制器传递一些属性内容到页面之中：

```html
<span th:if="${member.age lt 18}">
未成年人！
</span>
<span th:if="${member.name eq '啊三'}">
欢迎啊三来访问！
</span>
```

不满足条件的判断

```html
<span th:unless="${member.age gt 18}">
你还不满18岁，不能够看电影！
</span>
```

```html
<div th:if="${variable.something} == null">
```

通过swith进行分支判断

```html
<span th:switch="${member.uid}">
<p th:case="100">uid为101的员工来了</p>
<p th:case="99">uid为102的员工来了</p>
<p th:case="*">没有匹配成功的数据！</p>
</span>
```



## 字面替换

文字替换允许轻松格式化包含变量值的字符串，而无需附加文字`'...' + '...'`。

这些替换必须用竖线（`|`）包围，如：

```html
<span th:text="|Welcome to our application, ${user.name}!|">
```

这相当于：

```html
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
```

文字替换可以与其他类型的表达相结合：

```html
<span th:text="${onevar} + ' ' + |${twovar}, ${threevar}|">
```

> 唯一的变量/消息表达式（`${...}`，`*{...}`，`#{...}`）被允许内部`|...|`字面取代。没有其他文字（`'...'`），布尔/数字标记，条件表达式等。



## 算术运算

一些算术运算也可用：`+`，`-`，`*`，`/`和`%`。

```html
<div th:with="isEven=(${prodStat.count} % 2 == 0)">
```

请注意，这些运算符也可以应用于OGNL变量表达式本身（在这种情况下，将由OGNL而不是Thymeleaf标准表达式引擎执行）：

```html
<div th:with="isEven=${prodStat.count % 2 == 0}">
```

> 请注意，其中一些运算符存在文本别名：`div`（`/`），`mod`（`%`）

三目运算

```html
<span th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')">
```



## 数据遍历

1. 普通遍历

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
  </tr>
  <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
  </tr>
</table>
```

2. list类型数据遍历

```html
<body>
    <table>
        <tr><td>No.</td><td>UID</td><td>姓名</td><td>年龄</td><td>偶数</td><td>奇数</td></tr>
        <tr th:each="user,memberStat:${allUsers}">
            <td th:text="${memberStat.index + 1}"/>
            <td th:text="${user.uid}"/>
            <td th:text="${user.name}"/>
            <td th:text="${user.age}"/>
            <td th:text="${memberStat.even}"/>
            <td th:text="${memberStat.odd}"/>
        </tr>
    </table>
</body>
```

3. map类型数据遍历

```html
<body>
    <table>
        <tr><td>No.</td><td>KEY</td><td>UID</td><td>姓名</td><td>年龄</td><td>偶数</td><td>奇数</td></tr>
        <tr th:each="memberEntry,memberStat:${allUsers}">
            <td th:text="${memberStat.index + 1}"/>
            <td th:text="${memberEntry.key}"/>
            <td th:text="${memberEntry.value.uid}"/>
            <td th:text="${memberEntry.value.name}"/>
            <td th:text="${memberEntry.value.age}"/>
            <td th:text="${memberStat.even}"/>
            <td th:text="${memberStat.odd}"/>
        </tr>
    </table>
</body>
```



## 页面引入

thymeleaf中提供了两种方式进行页面引入。

- th:replace
- th:include

1. 新建需要被引入的页面文件，路径为"src/main/view/templates/commons/footer.html"

```html
<meta http-equiv="Content-Type" content="text/html;charset=UTF-8"/>
<footer th:fragment="companyInfo">
    <p>设为首页 ©2018 SpringBoot 使用<span th:text="${projectName}"/>前必读
        意见反馈 京ICP证030173号 </p>
</footer>
```

可以看到页面当中还存在一个变量projectName，这个变量的值可以在引入页面中通过`th:with="projectName=百度"`传过来。

1. 引入页面中只需要添加如下代码即可

```html
<div th:include="@{/commons/footer} :: companyInfo" th:with="projectName=百度"/>
```



## 为特定属性设置值

`th:attr`为属性添加值，多个属性值用逗号隔开

```html
<input type="submit" value="Subscribe!" th:attr="value=#{subscribe.submit}"/>
```

但是通常，将使用`th:*`其任务设置特定标记属性的其他属性（而不仅仅是任何属性`th:attr`）。



例如，要设置`value`属性，请使用`th:value`：

```html
<input type="submit" value="Subscribe!" th:value="#{subscribe.submit}"/>
```

尝试`action`对`form`以及`href`标记中的属性执行相同的操作：

```html
<form action="subscribe.html" th:action="@{/subscribe}">
<li><a href="product/list.html" th:href="@{/product/list}">Product List</a></li>
```



## 固定值布尔属性

HTML具有*布尔属性*的概念，没有值的属性和值的前提意味着值为“true”。在XHTML中，这些属性只占用1个值，这本身就是一个值。

例如，`checked`：

```html
<input type="checkbox" name="option2" checked /> <!-- HTML -->
<input type="checkbox" name="option1" checked="checked" /> <!-- XHTML -->
```

标准方言包含允许您通过评估条件来设置这些属性的属性，因此，如果计算为true，则属性将设置为其固定值，如果计算为false，则不会设置该属性：

```html
<input type="checkbox" name="active" th:checked="${user.active}" />
```

标准方言中存在以下固定值布尔属性：

| `th:async`          | `th:autofocus` | `th:autoplay`   |
| ------------------- | -------------- | --------------- |
| `th:checked`        | `th:controls`  | `th:declare`    |
| `th:default`        | `th:defer`     | `th:disabled`   |
| `th:formnovalidate` | `th:hidden`    | `th:ismap`      |
| `th:loop`           | `th:multiple`  | `th:novalidate` |
| `th:nowrap`         | `th:open`      | `th:pubdate`    |
| `th:readonly`       | `th:required`  | `th:reversed`   |
| `th:scoped`         | `th:seamless`  | `th:selected`   |

# 模板布局

## 8.1包括模板片段

### 定义和引用片段

在我们的模板中，我们经常需要包含其他模板中的部分，页脚，标题，菜单等部分......

为了做到这一点，Thymeleaf需要我们定义这些部分，“片段”，以便包含，这可以使用`th:fragment`属性来完成。

假设我们要为所有杂货页面添加标准版权页脚，因此我们创建一个`/WEB-INF/templates/footer.html`包含以下代码的文件：

```html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <body>
  
    <div th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
    </div>
  
  </body>
  
</html>
```

上面的代码定义了一个名为的片段`copy`，我们可以使用其中一个`th:insert`或`th:replace`属性轻松地在我们的主页中包含这些片段（并且`th:include`，尽管自Thymeleaf 3.0以来不再推荐使用它）：

```html
<body>

  <div th:insert="~{footer :: copy}"></div>
  
</body>
```

请注意，`th:insert`需要一个*片段表达式*（`~{...}`），它是*一个导致片段的表达式*。在上面的例子中，这是一个非复杂的*片段表达式*，（`~{`，`}`）封闭是完全可选的，所以上面的代码相当于：

```html
<body>

  ...

  <div th:insert="footer :: copy"></div>
  
</body>
```

### 片段规范语法

*片段表达式*的语法非常简单。有三种不同的格式：

- `"~{templatename::selector}"`包括在名为的模板上应用指定标记选择器而产生的片段`templatename`。请注意，`selector`可以仅仅是一个片段的名字，所以你可以指定为简单的东西`~{templatename::fragmentname}`就像在`~{footer :: copy}`上面。

  > 标记选择器语法由底层AttoParser解析库定义，类似于XPath表达式或CSS选择器。有关详细信息，请参阅[附录C.](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#appendix-c-markup-selector-syntax)

- `"~{templatename}"`包含名为的完整模板`templatename`。

  > 请注意，您在`th:insert`/ `th:replace`tags中使用的模板名称必须由模板引擎当前使用的模板解析器解析。

- `~{::selector}"`或`"~{this::selector}"`插入来自同一模板的片段，进行匹配`selector`。如果在表达式出现的模板上找不到，则模板调用（插入）的堆栈将遍历最初处理的模板（*根*），直到`selector`在某个级别匹配。

双方`templatename`并`selector`在上面的例子可以是全功能的表达式（甚至条件语句！），如：

```html
<div th:insert="footer :: (${user.isAdmin}? #{footer.admin} : #{footer.normaluser})"></div>
```

再次注意周围的`~{...}`包络在`th:insert`/中是如何可选的`th:replace`。

片段可以包含任何`th:*`属性。一旦将片段包含在目标模板（具有`th:insert`/ `th:replace`attribute的模板）中，就会评估这些属性，并且它们将能够引用此目标模板中定义的任何上下文变量。

> 这种片段方法的一大优点是，您可以在浏览器完美显示的页面中编写片段，具有完整且*有效的*标记结构，同时仍保留使Thymeleaf将其包含在其他模板中的能力。

### 没有引用片段 `th:fragment`

由于标记选择器的强大功能，我们可以包含不使用任何`th:fragment`属性的片段。它甚至可以是来自不同应用程序的标记代码，完全不了解Thymeleaf：

```html
...
<div id="copy-section">
  &copy; 2011 The Good Thymes Virtual Grocery
</div>
...
```

我们可以使用上面的片段简单地通过其`id`属性引用它，类似于CSS选择器：

```html
<body>
  ...
  <div th:insert="~{footer :: #copy-section}"></div>
</body>
```

### `th:insert`和`th:replace`（和`th:include`）之间的区别

和之间有什么区别`th:insert`和`th:replace`（和`th:include`，因为3.0不推荐）？

- `th:insert` 是最简单的：它只是插入指定的片段作为其主机标签的主体。
- `th:replace`实际上用指定的片段**替换**它的主机标签。
- `th:include`类似于`th:insert`，但不是插入片段，它只插入此片段的*内容*。

所以像这样的HTML片段：

```html
<footer th:fragment="copy">
  &copy; 2011 The Good Thymes Virtual Grocery
</footer>
```

...在主机`<div>`标签中包含三次，如下所示：

```html
<body>

  <div th:insert="footer :: copy"></div>
  <div th:replace="footer :: copy"></div>
  <div th:include="footer :: copy"></div>
 
</body>
```

......将导致：

```html
<body>
  <div>
    <footer>
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
  </div>

  <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
  </footer>

  <div>
    &copy; 2011 The Good Thymes Virtual Grocery
  </div>
</body>
```



# 属性优先级

`th:*`在同一个标签中写入多个属性会发生什么？例如：

```html
<ul>
  <li th:each="item : ${items}" th:text="${item.description}">Item description here...</li>
</ul>
```

我们希望该`th:each`属性在之前执行，`th:text`以便我们得到我们想要的结果，但考虑到HTML / XML标准没有给标记中的属性编写顺序赋予任何意义，*优先级*必须在属性本身中建立机制，以确保它将按预期工作。

因此，所有Thymeleaf属性都定义了一个数字优先级，它确定了它们在标记中执行的顺序。这个顺序是：

| 优先级 | 特征                 | 属性                                       |
| :----- | :------------------- | :----------------------------------------- |
| 1      | 片段包含             | `th:insert` `th:replace`                   |
| 2      | 片段迭代             | `th:each`                                  |
| 3      | 条件评估             | `th:if` `th:unless` `th:switch` `th:case`  |
| 4      | 局部变量定义         | `th:object` `th:with`                      |
| 5      | 一般属性修改         | `th:attr` `th:attrprepend` `th:attrappend` |
| 6      | 具体属性修改         | `th:value` `th:href` `th:src` `...`        |
| 7      | 文字（标签正文修改） | `th:text` `th:utext`                       |
| 8      | 片段规范             | `th:fragment`                              |
| 9      | 片段删除             | `th:remove`                                |



# Thymeleaf + SpringBoot

首先创建工程时，选择thymeleaf

```xml
<dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

配置文件中配置thymeleaf

```properties
# thymeleaf配置
spring.thymeleaf.mode=HTML5
# 发开时关闭缓存，实时更新页面
spring.thymeleaf.cache=false
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.servlet.content-type=text/html
spring.mvc.static-path-pattern=/**
```

静态网页的头部：

```html
<html xmlns:th="http://www.thymeleaf.org">
```

