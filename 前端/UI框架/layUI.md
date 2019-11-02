# layUI

### 模块加载

```javascript
layui.use(['form', 'upload'], function(){  //如果只加载一个模块，可以不填数组。如：layui.use('form')
  var form = layui.form //获取form模块
  ,upload = layui.upload; //获取upload模块
  
  //监听提交按钮
  form.on('submit(test)', function(data){
    console.log(data);
  });
  
  //实例化一个上传控件
  upload({
    url: '上传接口url'
    ,success: function(data){
      console.log(data);
    }
  })
});
```

### 模板引擎的使用（layui.laytpl）

##### 快速使用

与一般的字符拼接不同的是，laytpl 的模板可与数据分离，集中把逻辑处理放在 View 层，提升代码可维护性，尤其是针对大量模板渲染的情况。

```javascript
layui.use('laytpl', function(){
  var laytpl = layui.laytpl;
  
  //直接解析字符
  laytpl('{{ d.name }}是一位公猿').render({
    name: '贤心'
  }, function(string){
    console.log(string); //贤心是一位公猿
  });
  
  //你也可以采用下述同步写法，将 render 方法的回调函数剔除，可直接返回渲染好的字符
  var string =  laytpl('{{ d.name }}是一位公猿').render({
    name: '贤心'
  });
  console.log(string);  //贤心是一位公猿
  
  //如果模板较大，你也可以采用数据的写法，这样会比较直观一些
  laytpl([
    '{{ d.name }}是一位公猿'
    ,'其它字符 {{ d.content }}  其它字符'
  ].join(''))
}); 
```

##### 你也可以将模板存储在页面或其它任意位置：

```html
//第一步：编写模版。你可以使用一个script标签存放模板，如：
<script id="demo" type="text/html">
  <h3>{{ d.title }}</h3>
  <ul>
  {{#  layui.each(d.list, function(index, item){ }}
    <li>
      <span>{{ item.modname }}</span>
      <span>{{ item.alias }}：</span>
      <span>{{ item.site || '' }}</span>
    </li>
  {{#  }); }}
  {{#  if(d.list.length === 0){ }}
    无数据
  {{#  } }} 
  </ul>
</script>
 
//第二步：建立视图。用于呈现渲染结果。
<div id="view"></div>
 
//第三步：渲染模版
var data = { //数据
  "title":"Layui常用模块"
  ,"list":[{"modname":"弹层","alias":"layer","site":"layer.layui.com"},{"modname":"表单","alias":"form"}]
}
var getTpl = demo.innerHTML
,view = document.getElementById('view');
laytpl(getTpl).render(data, function(html){
  view.innerHTML = html;
});  
```

##### 模版语法

| 语法                    | 说明                                                         | 示例                                                         |
| :---------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| {{ d.field }}           | 输出一个普通字段，不转义html                                 | `<div>{{ d.content }}</div>              `                   |
| {{= d.field }}          | 输出一个普通字段，并转义html                                 | `<h2>{{= d.title }}</h2>              `                      |
| {{# JavaScript表达式 }} | JS 语句。一般用于逻辑处理。用分隔符加 # 号开头。  注意：如果你是想输出一个函数，正确的写法是：{{ fn() }}，而不是：{{# fn() }} | `{{#    var fn = function(){    return '2017-08-18';  }; }} {{#  if(true){ }}  开始日期：{{ fn() }}{{#  } else { }}  已截止{{#  } }}                         ` |
| {{! template !}}        | 对一段指定的模板区域进行过滤，即不解析该区域的模板。注：layui 2.1.6 新增 | `<div> {{! 这里面的模板不会被解析  !}}</div>              `  |

##### 分隔符

如果模版默认的 {{ }} 分隔符与你的其它模板（一般是服务端模板）存在冲突，你也可以重新定义分隔符：

```javascript
laytpl.config({
  open: '<%',
  close: '%>'
});
 
//分割符将必须采用上述定义的
laytpl([
  '<%# var type = "公"; %>' //JS 表达式
  ,'<% d.name %>是一位<% type %>猿。'
].join('')).render({
  name: '贤心'
}, function(string){
  console.log(string); //贤心是一位公猿
});       
```

# Uncaught TypeError: s.parents is not a function

layer.msg();显示的信息为对象