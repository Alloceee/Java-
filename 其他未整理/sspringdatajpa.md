springdatajpa

```

findTop12ByStartCityAndEndCityOrderByStartTime
```

Top12取前12条数据





### 更新局部数据

springdatajpa直接更新原本对象如果新对象为null则会覆盖原有数据

1. 使用spring的BeanUtils

```java
BeanUtils.copyProperties(company, company1, getNullPropertyNames(company)); //使用更新对象的非空值去覆盖待更新对象
```

```java
public class BeanUtils {

    public static String[] getNullPropertyNames(Object source) {
        final BeanWrapper src = new BeanWrapperImpl(source);
        java.beans.PropertyDescriptor[] pds = src.getPropertyDescriptors();

        Set<String> emptyNames = new HashSet<String>();
        for (java.beans.PropertyDescriptor pd : pds) {
            Object srcValue = src.getPropertyValue(pd.getName());
            if (srcValue == null) emptyNames.add(pd.getName());
        }
        String[] result = new String[emptyNames.size()];
        return emptyNames.toArray(result);
    }

}
```

