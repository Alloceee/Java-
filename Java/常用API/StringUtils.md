```
isNotEmpty ：
判断某字符串是否非空
StringUtils.isNotEmpty(null) = false
StringUtils.isNotEmpty("") = false
StringUtils.isNotEmpty(" ") = true
StringUtils.isNotEmpty("bob") = true

isNotBlank：
判断某字符串是否不为空且长度不为0且不由空白符(whitespace)构成，
下面是示例：
StringUtils.isNotBlank(null) = false
StringUtils.isNotBlank("") = false
StringUtils.isNotBlank(" ") = false
StringUtils.isNotBlank("\t \n \f \r") = false
```

