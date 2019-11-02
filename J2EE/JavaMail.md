# JavaMail

邮箱配置

```
接收邮件服务器：imap.qq.com
发送邮件服务器：smtp.qq.com
账户名：您的QQ邮箱账户名（如果您是VIP邮箱，账户名需要填写完整的邮件地址）
密码：您的QQ邮箱密码
电子邮件地址：您的QQ邮箱的完整邮件地址
```









## SpringBoot整合JavaMail

#### 1.pom.xml

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
```

#### 2.properties

```properties
#邮箱配置
spring.mail.host=smtp.qq.com
spring.mail.port=587
spring.mail.username=1184465220@qq.com
spring.mail.password=fxdufbhjcbrhidhc
```

#### 3.EmailServiceImpl

```java
@Service
public class EmailServiceImpl implements EmailService {
    @Autowired
    private JavaMailSender javaMailSender;

    @Override
    public String send() {
        try {
            String name = MimeUtility.decodeText("yewenshu");
            InternetAddress from = new InternetAddress(name + "<1184465220@qq.com>");
            MimeMessage mimeMessage  = javaMailSender.createMimeMessage();
            MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage,true);
            mimeMessageHelper.setTo("1252164978@qq.com");
            mimeMessageHelper.setFrom(from);
            mimeMessageHelper.setSubject("测试");
            mimeMessageHelper.setText("<h1>2121</h1>",true);
            javaMailSender.send(mimeMessage);
            return "发送成功";
        } catch (Exception e) {
            e.printStackTrace();
            return "发送失败";
        }
    }
}
```

