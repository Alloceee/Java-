# layui 时间框选择一闪就消失，打不开问题解决办法

置顶  更多

版权声明：本文为博主原创文章，遵循[ CC 4.0 BY-SA ](http://creativecommons.org/licenses/by-sa/4.0/)版权协议，转载请附上原文出处链接和本声明。本文链接：https://blog.csdn.net/lanhaileihen/article/details/90043905

```
layui.use('laydate', function() {
    var laydate = layui.laydate;
    $(this).removeAttr("lay-key");
    laydate.render({
        elem: '#startTime'
        ,trigger : 'click'
    });
    laydate.render({
        elem: '#reserveTime'
         ,trigger : 'click'
    });
});
```

 

加上 $(this).removeAttr("lay-key");和,trigger : 'click'，问题就解决了。




1.toggle方法

```js
$("button").toggle(function(){
   alert(1);
},function(){
   alert(2);
});
```



在JQuery 1.9 以上的版本将不会出现点击的toggle方法!!

2.自定义

```javascript
var counter=0;
$('button').bind("click", function() {
	counter++ % 2 ? 
		(function() { alert(1); }()) :
		(function() { alert(2); }()); 
});
```



jquery 

str.startsWith(“”);

以。。开头



 应用方式如下:
    var url = location.href;
    if (url.startWith('http://www.baidu.com'))
    {
       //如果当前url是以 http://www.baidu.com 开头(以下是处理代码)
    }

jquery 的这俩个方法全部是用选择器实现的. 





## jQuery实时头像预览

```javascript
 $('#hd_photo').change(function () {
            var file = this.files[0];
            var fr = new FileReader();
            fr.readAsDataURL(file);
            fr.onload = function(){
                $('#hd_img').attr('src',fr.result)

            }
        })
```



判断上传的图片后缀

```javascript
if(!/.(gif|jpg|png)$/.test(file.name)){
                console.log("ssss");
            }
```





## 清除文件域的内容

```javascript
var file = $(":file");
            file.after(file.clone().val(""));
            file.remove();
```



## 获取ip地址

### 1. 第三方接口

1) 这里提供一个搜狐接口的地址：http://pv.sohu.com/cityjson?ie=utf-8 ，将这个js引入到页面即可得到returnCitySN。

![img](https://images2017.cnblogs.com/blog/449809/201708/449809-20170808150407089-1567557118.png)

2) api.ipify.org

https://api.ipify.org/?format=jsonp&callback=getIP

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```javascript
1 <script type="application/javascript">
2   function getIP(json) {
3     document.write("My public IP address is: ", json.ip);
4   }
5 </script>
6 
7 <script type="application/javascript" src="https://api.ipify.org?format=jsonp&callback=getIP"></script>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

或者

```javascript
1 $.getJSON('https://api.ipify.org?format=json', function(data){
2     console.log(data.ip);
3 });
```

 

### 2. 向服务器发送一个ajax请求，该请求包中会包含客户端的相关信息，当然也包括IP。

### 3. 使用webRTC，获取私有IP

The **`RTCPeerConnection()`** constructor returns a newly-created [`RTCPeerConnection`](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection), which represents a connection between the local device and a remote peer.（http://ourcodeworld.com/articles/read/257/how-to-get-the-client-ip-address-with-javascript-only）

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```javascript
 1 /**
 2  * Get the user IP throught the webkitRTCPeerConnection
 3  * @param onNewIP {Function} listener function to expose the IP locally
 4  * @return undefined
 5  */
 6 function getUserIP(onNewIP) { //  onNewIp - your listener function for new IPs
 7     //compatibility for firefox and chrome
 8     var myPeerConnection = window.RTCPeerConnection || window.mozRTCPeerConnection || window.webkitRTCPeerConnection;
 9     var pc = new myPeerConnection({
10         iceServers: []
11     }),
12     noop = function() {},
13     localIPs = {},
14     ipRegex = /([0-9]{1,3}(\.[0-9]{1,3}){3}|[a-f0-9]{1,4}(:[a-f0-9]{1,4}){7})/g,
15     key;
16 
17     function iterateIP(ip) {
18         if (!localIPs[ip]) onNewIP(ip);
19         localIPs[ip] = true;
20     }
21 
22      //create a bogus data channel
23     pc.createDataChannel("");
24 
25     // create offer and set local description
26     pc.createOffer().then(function(sdp) {
27         sdp.sdp.split('\n').forEach(function(line) {
28             if (line.indexOf('candidate') < 0) return;
29             line.match(ipRegex).forEach(iterateIP);
30         });
31         
32         pc.setLocalDescription(sdp, noop, noop);
33     }).catch(function(reason) {
34         // An error occurred, so handle the failure to connect
35     });
36 
37     //listen for candidate events
38     pc.onicecandidate = function(ice) {
39         if (!ice || !ice.candidate || !ice.candidate.candidate || !ice.candidate.candidate.match(ipRegex)) return;
40         ice.candidate.candidate.match(ipRegex).forEach(iterateIP);
41     };
42 }
43 
44 // Usage
45 
46 getUserIP(function(ip){
47     alert("Got IP! :" + ip);
48 });
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```javascript
 //获取城市ajax
        $.ajax({
            url: 'http://api.map.baidu.com/location/ip?ak=ia6HfFL660Bvh43exmH9LrI6',
            type: 'POST',
            dataType: 'jsonp',
            success:function(data) {
                $('#city').html(data.content.address_detail.province + "," + data.content.address_detail.city)
            }
        });
    })
</script>
</body>
</html>
```









####  [jquery validate如何不提交表单就做验证（ajax提交数据）](https://www.cnblogs.com/shinima/p/4171190.html)

```javascript
if($("#FromID").valid()){
}
```



### 事件委托

```javascript
$(function(){
$(document).on('click','绑定点击事件的元素',function(){
/*需要注意的就是事件里边的$(this)指的就是被点击的元素而不是$(document)*/
})
})
```



Jquery、Java解析html字符串成dom对象，遍历dom对象获取图片元素（或者其子元素）
使用 Jquery解析html:
.parseHTML方法：将html字符串解析成dom对象，他返回的是一个array数组（包含其所有子元素）.parseHTML方法：将html字符串解析成dom对象，他返回的是一个array数组（包含其所有子元素）(doms).children(“img”)：获取doms对象节点下的所有img子元素；
var content = item.neiRong;//爬取内容
var doms = $.parseHTML(content);//解析Html串
var imgs = $(doms).children("img");//children()方法：查找img元素
var length = imgs.length;//获取img的个数
1
2
3
4
使用Java解析Html串：
使用Jsoup解析Html串，原理和Jquery是一样的，都是解析成dom对象；
String str = "";
str += "<div>";
str += "<img src='http://a86d90395a7f8f486.jpeg' />";
str += "<img src='http://a9c575a8d1763f889.jpeg' />";
str += "<img src='http://de55fe7697ccb203e.jpeg' />";
str += "<img src='http://weec82c7cb50b0496.jpeg' />";
str += "</div>";
//注意：Jsoup只能爬取静态内容，js无法获取；
org.jsoup.nodes.Document document = org.jsoup.Jsoup.parse(str);
Elements select = document.select("img");
————————————————
版权声明：本文为CSDN博主「ZixiangLi」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zixiangli/article/details/78689878





#### ajax上传文件

```javascript
 $.ajax({
            data: formData,
            url: '/admin/company/import',
            type: 'POST',
            dataType: 'json',
            processData : false, // 使数据不做处理
            contentType : false, // 不要设置Content-Type请求头
            success: function (res) {
                console.log(res);
                // if (res.code > 0) {
                //     layer.msg(res.msg);
                // } else {
                //     layer.msg(res.msg);
                //     location.reload();
                // }
            }
        })
```

>  processData : false, // 使数据不做处理
>  contentType : false, // 不要设置Content-Type请求头



#### 获取url参数

```javascript
 function GetQueryString(name)
        {
            var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)");
            var r = window.location.search.substr(1).match(reg);
            if(r!=null)return  unescape(r[2]); return null;
        }
```



#### ajax上传文件进度条

```html
<div class="progress" hidden>
    <div class="progress-bar p_bar" role="progressbar" aria-valuenow="60"
         aria-valuemin="0" aria-valuemax="100">
        <span class="uploadFile_rate">0%</span>
    </div>
</div>
```



```javascript
  var sendFile = function (_this, files) {
            var formData = new FormData();
            if (files.length === 1) {
                formData.append("file", files[0]);
            } else {
                //循环添加到formData中
                for (var i = 0; i < files.length; i++) {
                    formData.append("file" + i, files[i]);
                }
            }
            $(_this).siblings(".progress").show();
            var rate = $(_this).siblings(".progress").find('.uploadFile_rate');
            var bar = $(_this).siblings(".progress").children('.p_bar');
            $.ajax({
                data: formData,
                type: 'post',
                dataType: "json",
                url: '/admin/upload/fileUpload',
                // async: false,
                processData: false, // 使数据不做处理
                contentType: false, // 不要设置Content-Type请求头
                success: function (data) {
                    $(_this).siblings("input:hidden").val(JSON.stringify(data));
                    alert("上传文件成功！");
                },
                error: function (e) {
                    console.log("error")
                },
                xhr: function () {  // custom xhr
                    myXhr = $.ajaxSettings.xhr();
                    if (myXhr.upload) { // if upload property exists
                        myXhr.upload.addEventListener('progress', function (e) {
                            var done = e.position || e.loaded;
                            var total = e.totalSize || e.total;
                            var percent = Math.round(done / total * 100);
                            rate.html(percent + '%');
                            bar.animate({
                                width: percent + "%"
                            }, {
                                duration: 5
                            });
                        }, false);
                        myXhr.addEventListener('load', function (e) {
                            //upload done
                        });
                    }
                    return myXhr;
                }
            })
        };
```

```css
.progress {
    width: 200px;
    height: 20px;
    border: 1px solid #ccc;
    border-radius: 10px;
    margin: 10px 20px;
    overflow: hidden;
}
/* 初始状态设置进度条宽度为0px */
.progress > div {
    width: 0;
    height: 100%;
    background-color: yellowgreen;
    transition: all .3s ease;
}
```

