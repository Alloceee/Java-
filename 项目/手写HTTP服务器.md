HTTP Protocol 实现了HTTP协议
Servlet
ServletContext
Request 封装HTTP请求报文
Response 封装HTTP响应报文
DispatcherServlet Servlet转发
Static Resources & File Download 静态资源的访问
Error Notification 错误页面显示
Get & Post & Put & Delete 支持各种HTTP方法
web.xml parse 解析web.xml
Forward 转发
Redirect 重定向
Simple TemplateEngine 简单的模板引擎

使用技术
基于Java BIO、多线程、Socket网络编程、XML解析、log4j/slf4j日志
只引入了junit,lombok(简化POJO开发),slf4j,log4j,dom4j(解析xml),mime-util(用于判断文件类型)依赖，与web相关内容全部自己完成。
session&cookie 会话管理
------------------------------------------------