# jQuery

[中文API](http://jquery.cuishifeng.cn/)

### ajax

| 函数                                            | 用法                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| \$.post("url",null, function(data) { },"json"); | 参数一：ajax post请求url;参数二：需要传递的数据；参数三：处理返回数据的函数；参数四：要求返回数据的类型 |
| $.each(data,function() {}                       | jQuery遍历函数，遍历数组或者集合                             |



### Json对象与Json字符串的转化

| 方法                   | 用法                       |
| ---------------------- | -------------------------- |
| JSON.parse(jsonString) | 将json字符串转化为json对象 |
| JSON.stringify(object) | 将对象转化为json字符串     |



## validate插件使用

```javascript
$(function() {
					
					$('form').validate({
						rules: {
							username: {
								required: true,
							},
							password: {
								required: true,
								remote: {
									url: '',
									dataType: 'json',
									data: {
										username: function() {
											return $('#username').val();
										},
										password: function() {
											return $("#password").val()
										}
									}
								}
							}
						},
						messages: {
							username: {
								required: "请输入用户名"
							},
							password: {
								required: "请输入密码",
								remote: "用户名或密码不正确"
							}
						}
					});
				})
```

