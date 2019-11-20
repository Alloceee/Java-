#### thymeleaf+layui加载页面渲染时TemplateProcessingException: Could not parse as expression: "

解决方式

1.也就是把cols后的[[ ]]变为

```html
[
    [
    
    ]
]
```

因为[[…]]之间的表达式在thymeleaf被认为是内联表达式,所以渲染错误

2.或者在<script type="text/javascript" >  加上 th:inline="none"

```html
<script type="text/javascript"  th:inline="none">
```

