# Spring Boot - Email

build.gradle.kts
```sh
implementation("org.springframework.boot:spring-boot-starter-mail")
```

src/main/resources/application.properties
```properties
spring.mail.host=smtp.gmail.com
spring.mail.port=25
spring.mail.username=개발자@gmail.com
spring.mail.password=앱 비밀번호
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

```java
@Value("${spring.mail.username}") private String username;

@Autowired
private JavaMailSender mailSender;

@GetMapping("/sendEmail")
public void sendEmail(
) {
    SimpleMailMessage message = new SimpleMailMessage();
    message.setFrom(username);
    message.setTo("받는자@naver.com");
    message.setSubject("subject");
    message.setText("body");
    mailSender.send(message);
}
```
* ❕ `message.setFrom` 수정하면 이메일이 안 나갈 수 있다.

## Google
### 535-5.7.8 Username and Password not accepted. For more information, go to
* [앱 비밀번호 생성](https://support.google.com/accounts/answer/185833#zippy=%2C%EC%95%B1-%EB%B9%84%EB%B0%80%EB%B2%88%ED%98%B8%EA%B0%80-%ED%95%84%EC%9A%94%ED%95%9C-%EC%9D%B4%EC%9C%A0)
후 16자리 비밀번호로 `spring.mail.password` 변경
* 메일이 안 보내질 경우: Gmail > 설정 > 모든 설정 보기 > 전달 및 POP/IMAP > IMAP 사용

## Naver
src/main/resources/application.properties
```properties
spring.mail.host=smtp.naver.com
spring.mail.port=25
spring.mail.username=개발자@naver.com
spring.mail.password=네이버 비밀번호
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

### 535 5.7.1 Username and Password not accepted RjnjJ+RHQW6J6CLomlluBA - nsmtp
* 네이버 메일 > 환경설정 > POP3/IMAP 설정선택됨 > POP3/SMTP 사용 > 사용함
