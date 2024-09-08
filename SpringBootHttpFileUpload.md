# Spring Boot - File Upload

src/main/java/패키지/config/MultipartConfig.java
```java
@Configuration
public class MultipartConfig {
    @Bean
    public MultipartResolver multipartResolver() {
        return new StandardServletMultipartResolver();
    }

    @Bean
    public MultipartConfigElement multipartConfigElement() {
        MultipartConfigFactory factory = new MultipartConfigFactory();
        factory.setLocation("이미지 업로드할 절대 경로");
        factory.setMaxRequestSize(DataSize.ofMegabytes(100L));
        factory.setMaxFileSize(DataSize.ofMegabytes(100L));
        return factory.createMultipartConfig();
    }
}
```

Controller.java
```java
@PostMapping(
        value="/uploadFiles",
        consumes=MediaType.MULTIPART_FORM_DATA_VALUE
)
public String uploadFiles(
        @RequestPart("text") String text,
        @RequestPart("uploadFiles") MultipartFile[] uploadFiles
) {
    return text;
}
```
* http://localhost:8080/swagger-ui/index.html
