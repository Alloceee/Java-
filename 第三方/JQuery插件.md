# jquery.cookie.js 

Cookies

定义：让网站服务器把少量数据储存到客户端的硬盘或内存，从客户端的硬盘读取数据的一种技术；

下载与引入:jquery.cookie.js基于jquery；先引入jquery，再引入：jquery.cookie.js；下载：http://plugins.jquery.com/cookie/

```html
<script type="text/javascript" src="js/jquery.min.js"></script>
<script type="text/javascript" src="js/jquery.cookie.js"></script>
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)



使用：

**1.添加一个"会话cookie"**

```javascript
$.cookie('the_cookie', 'the_value');
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

这里没有指明 cookie有效时间，所创建的cookie有效期默认到用户关闭浏览器为止，所以被称为 “会话cookie（session cookie）”。

**2.创建一个cookie并设置有效时间为 7天**

```javascript
$.cookie('the_cookie', 'the_value', { expires: 7 });
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

在默认情况下，只有设置 cookie的网页才能读取该 cookie。如果想让一个页面读取另一个页面设置的cookie，必须设置cookie的路径。cookie的路径用于设置能够读取 cookie的顶级目录。将这个路径设置为网站的根目录，可以让所有网页都能互相读取 cookie （一般不要这样设置，防止出现冲突）。

**4.读取cookie**

```javascript
$.cookie('the_cookie');
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**5.删除cookie**

```javascript
$.cookie('the_cookie', null);   //通过传递null作为cookie的值即可
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**6.可选参数**

```javascript
$.cookie('the_cookie','the_value',{
    expires:7,  
    path:'/',
    domain:'jquery.com',
    secure:true
})
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**expires**：（Number|Date）有效期；设置一个整数时，单位是天；也可以设置一个日期对象作为Cookie的过期日期；
**path**：（String）创建该Cookie的页面路径；
**domain**：（String）创建该Cookie的页面域名；
**secure**：（Booblean）如果设为true，那么此Cookie的传输会要求一个安全协议，例如：HTTPS；

注意：cookie是基于域名来储存的。要放到测试服务器上或者本地localhost服务器上才会生效。cookie具有不同域名下储存不可共享的特性。单纯的本地一个html页面打开是无效的。

# jquery.validate.js 下载和使用方法

[下载地址](https://jqueryvalidation.org/)

```javascript
 $(function () {
// 在键盘按下并释放及提交后验证提交表单
            $("#password-form").validate({
                errorClass: "error",
                errorElement: "span",
                wrapper: "label",
                errorContainer: $(".error"),
                rules: {
                    password: {
                        required: true,
                        minlength: 5,
                        maxlength:25,
                    },
                    password2:{
                        required: true,
                        maxlength:25,
                        equalTo:"#password"
                    }

                },
                messages: {
                    password: {
                        required: "请输入密码",
                        minlength: "密码长度不能小于 5 个字母",
                        maxlength: "密码长度不能大于 25 个字母",
                    },
                    password2: {
                        required: "请输入密码",
                        minlength: "密码长度不能小于 5 个字母",
                        maxlength: "密码长度不能大于 25 个字母",
                        equalTo: "两次输入密码不一致"
                    }
                }
            })
        })
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

# jQuery-emoji  表情插件 

### 让文本框或div具备插入表情功能。

## 功能 features

- 支持给textarea或可编辑div加上表情功能，自动识别元素类型。
- 如果是textarea，则选择表情后插入表情代码，如果是可编辑div，则直接插入表情图片。
- 支持自定义表情代码的格式。
- 支持将表情代码转换为表情图片。
- 支持多组表情并提供tab切换。
- 示例已带有百度贴吧和qq高清2套表情。
- 同一页面支持多个表情实例。

