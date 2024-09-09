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
* [React-Native File Upload](https://github.com/ovdncids/react-native-curriculum/blob/master/FileUpload.md#react-native)

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

## File Download
```java
@GetMapping(value="/downloadFile/{fileName}")
public ResponseEntity<?> downloadFile(
        @PathVariable("fileName") String fileName
) throws IOException {
    Path path = Paths.get("이미지 업로드할 절대 경로" + "/" + fileName);
    String contentType = Files.probeContentType(path);
    HttpHeaders httpHeaders = new HttpHeaders();
    httpHeaders.setContentDisposition(
            ContentDisposition.builder("inline")
                    .filename(fileName, StandardCharsets.UTF_8)
                    .build()
    );
    httpHeaders.add(HttpHeaders.CONTENT_TYPE, contentType);
    Resource resource = new InputStreamResource(Files.newInputStream(path));
    return new ResponseEntity<>(resource, httpHeaders, HttpStatus.OK);
}
```
* ❕ `ContentDisposition.builder` 값이 `inline`이면 브라우저에서 이미지를 볼 수 있고 `attachment`이면 브라우저에서 이미지를 다운로드 한다.
