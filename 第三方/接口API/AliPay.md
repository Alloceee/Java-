# AliPay

1. 申请账号





2. 引入阿里支付的sdk

```xml
<!-- https://mvnrepository.com/artifact/com.alipay.sdk/alipay-sdk-java -->
        <dependency>
            <groupId>com.alipay.sdk</groupId>
            <artifactId>alipay-sdk-java</artifactId>
            <version>3.4.27.ALL</version>
        </dependency>
```

3. 支付宝参数配置类

```java
public class AlipayConfig {

    // APPID
    public static String app_id = "你应用的APPID";

    // 生成公钥时对应的私钥（填自己的）
    public static String private_key = "你的秘钥";

    //异步回调接口:得放到服务器上，且使用域名解析 IP
    public static String notify_url = "回调函数接口";


    //支付宝网关（注意沙箱alipaydev，正式则为 alipay）不需要修改
    public static String url = "https://openapi.alipay.com/gateway.do";

    //编码类型
    public static String charset = "UTF-8";

    //数据类型
    public static String format = "json";

    // 公钥
    public static String public_key = "你的公钥";

    //签名类型
    public static String signtype = "RSA2";

}
```

