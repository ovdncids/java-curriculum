# Spring Boot - File Upload

* https://velog.io/@seokjun0915/Spring-Boot-%ED%8C%8C%EC%9D%BC-%EC%97%85%EB%A1%9C%EB%93%9C%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C

## File Upload
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
        factory.setMaxRequestSize(DataSize.ofMegabytes(100));
        factory.setMaxFileSize(DataSize.ofMegabytes(100));
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
* [React File Upload](https://github.com/ovdncids/react-curriculum/blob/master/FileUpload.md#react)

```diff
- return text;
```
```java
Arrays.stream(uploadFiles).forEach(multipartFile -> {
    File newFile = new File(Objects.requireNonNull(multipartFile.getOriginalFilename()));
    try {
        multipartFile.transferTo(newFile);
    }
    catch (IOException exception) {
        exception.printStackTrace();
    }
});
return text;
```
