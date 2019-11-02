# JSP

### 简介





### 使用

在html文件开头加上，使用`.jsp`后缀

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```



### 4中作用域

| 类型           | 范围       | 简称        |
| -------------- | ---------- | ----------- |
| ServletContext | 全局作用域 | application |
| HttpSession    | 会话作用域 | session     |
| ServletRequest | 请求作用域 | request     |
| PageContext    | 页面作用域 | page        |



### EL表达式

- #### 主要作用
  
  **获取数据**：EL表达式主要用于替换JSP页面中的脚本表达式，以从各种类型的Web作用域中检索Java对象，获取数据，包括访问JavaBean的属性、List集合、Map集合、数组等。
  **执行运算**：利用EL表达式可以在JSP页面中执行一些基本的关系运算、逻辑运算和算术运算，以在JSP页面中完成一些简单的逻辑操作。
**获取Web开发常用对**：EL表达式定义了一些隐式对象，利用这些隐式对象，开发人员可以很轻松获得对Web常用对象的引用，从而获得这些对象中的数据。
  

  
- #### 表达式的结构

```jsp
${expression}  //如果只是文本需要在前面添加转义符，如\${}
```



- #### 获取对象的属性

```jsp
  ${object.propertyName}
  ${object["propertyName"]}
```



- #### 关系运算符的语法

```jsp
${statement?A:B}
```



- #### empty运算符

  ​	示例：${empty username}

  1. empty首先判断JSP的作用域中是否有username变量，如果没有，则表达式返回true
  2. empty判断username变量是否为null或者一个长度为0的空字符串，如果是，则表达式返回true；如果username是一个空的集合或者数组，则表达式也返回true。否则表达式将返回false。

- #### EL表达式的隐式对象

| 隐式对象    | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| pageContext | JSP页面的上下文对象。它可以访问JSP隐式对象，如请求、响应、会话、输出、ServletContext等。 |
| cookie      | 包含了当前请求对象中所有Cookie对象的Map。Cookie名称就是key名称，每个key都映射到一个Cookie对象。表达式${cookie.name.value}返回带有特定名称的第一个Cookie值。 |


- #### 四个作用域对象

```jsp
${pageScope.username}
${requestScope.username}
${sessionScope.username}
${applicationScope.username}
```

EL表达式支持默认访问，即省略scope对象，直接访问username对象，默认范围**从小到大**取值。

${username}

默认先取pageScope中的username，如果为null，则取requestScope中的username，依次类推。

- #### 常用隐式对象的使用

```jsp
请求方式：${pageContext.request.method}
请求上下文路径：${pageContext.request.contextPath}
请求URL：${pageContext.request.requestURL}
```



## JSP指令



## JSP动作



### include指令和include动作的区别是什么

|             |      | 编译过程 |
| ----------- | ---- | -------- |
| @include    |      |          |
| jsp:include |      |          |

**55.JSP的动态include和静态include**

(1)动态include用jsp:include动作实现，如<jsp:include page="abc.jsp" flush="true" />，它总是会检查所含文件中的变化，适合用于包含动态页面，并且可以带参数。会先解析所要包含的页面，解析后和主页面合并一起显示，即先编译后包含。
(2)静态include用include伪码实现，不会检查所含文件的变化，适用于包含静态页面，如<%@
include file="qq.htm" %>，不会提前解析所要包含的页面，先把要显示的页面包含进来，然后统一编译，即先包含后编译。

## JSTL标签库

#### JSTL标签库分为五大类

| 标签库 | 作用                                    | URI                                    | 前缀 |
| ------ | --------------------------------------- | -------------------------------------- | ---- |
| 核心   | 包含Web应用的常见工作，循环、输入输出等 | http://java.sun.com/jsp/jstl/core      | c    |
| 国际化 | 语言区域、消息、数字和日期格式化        | http://java.sun.com/jsp/jstl/fmt       | fmt  |
| XML    | 访问XML文件                             | http://java.sun.com/jsp/jstl/xml       | x    |
| 数据库 | 访问数据库                              | http://java.sun.com/jsp/jstl/sql       | sql  |
| 函数   | 集合长度、字符串操作                    | http://java.sun.com/jsp/jstl/functions | fn   |

### Core核心库

该核心库中有14个标签，被分为4类。

**多用途核心标签**：\<c:out>、\<c:set>、\<c:remove>、\<c:catch>

**条件控制标签**：\<c:if>、\<c:choose>、\<c:when>、\<c:otherwise>

**循环控制标签**：\<c:forEach>、\<c:forTokens>

**URL相关标签**：\<c:import>、\<c:url>、\<c:redirect>、\<c:param>

| 标签           | 用途                                                         |
| -------------- | ------------------------------------------------------------ |
| \<c:out>       | 用于在JSP中显示数据                                          |
| \<c:set>       | 用于为变量或JavaBean中的变量属性赋值                         |
| \<c:remove>    | 删除存在scope中的变量                                        |
| \<c:choose>    | 作为父标签存在，子标签为\<c:when>，\<c:otherwise>            |
| \<c:forEach>   | 循环控制。可以遍历的对象包括java.util.Collection和java.util.Map的所有实现类和数组 |
| \<c:forTokens> | 可以使用某个分隔符分割指定的字符串                           |
| \<c:url>       | 用于组合一个正确的URL地址                                    |
