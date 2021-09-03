# Spring Boot

## 설치
* https://start.spring.io
* https://sidepower.tistory.com/352
```sh
Project: Maven Project
Language: Java
Spring Boot: 2.5.4 (현재)
Project Metadata
  Artifact: SpringBootStudy
Packaging: Jar
Java: 8
Dependencies: Spring Web

GENERATE <- SpringBootStudy.zip 다운받기
압축 풀고 해당 경로를 Intellij에서 Open
```

## Spring Boot Tomcat 서버 실행
src/main/java/com/example/SpringBootStudy/SpringBootStudyApplication.java
```sh
public class SpringBootStudyApplication <- RUN 버튼 누르기
```

## 회원(Members) Rest API
src/main/java/com/example/SpringBootStudy/models/Member.java
```java
public class Member {
    private String name;
    private Integer age;

    public Member(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

src/main/java/com/example/SpringBootStudy/models/MembersResponse.java
```java
public class MembersResponse {
    public String result;
    public ArrayList<Member> members;

    public MembersResponse(String result) {
        this.result = result;
    }

    public MembersResponse(String result, ArrayList<Member> members) {
        this.result = result;
        this.members = members;
    }
}
```

* ❕ 접근권한을 private로 만든 경우는 `Getter and Setter`가 필수 이지만, 접근권한을 public으로 만든 경우는 없어도 된다.

src/main/java/com/example/SpringBootStudy/api/v1/Members.java
```java
@RestController
@CrossOrigin(origins = "*")
@RequestMapping("api/v1/members")
public class Members {
    private static ArrayList<Member> init() {
        ArrayList<Member> members = new ArrayList<>();
        members.add(new Member("홍길동", 39));
        members.add(new Member("김삼순", 33));
        members.add(new Member("홍명보", 44));
        members.add(new Member("박지삼", 22));
        members.add(new Member("권명순", 10));
        return members;
    }
    private static final ArrayList<Member> members = init();

    @RequestMapping(path = "", method = RequestMethod.POST)
    public MembersResponse membersCreate(@RequestBody Member member) {
        members.add(member);
        return new MembersResponse("created");
    }

    @RequestMapping(path = "", method = RequestMethod.GET)
    public MembersResponse membersRead() {
        return new MembersResponse("read", members);
    }

    @RequestMapping(path = "/{index}", method = RequestMethod.DELETE)
    public MembersResponse membersDelete(@PathVariable("index") int index) {
        members.remove(index);
        return new MembersResponse("deleted");
    }

    @RequestMapping(path = "/{index}", method = RequestMethod.PATCH)
    public MembersResponse membersUpdate(
            @PathVariable("index") int index,
            @RequestBody Member member
    ) {
        members.set(index, member);
        return new MembersResponse("updated");
    }
}
```

<!--
### Global CORS configuration
https://spring.io/guides/gs/rest-service-cors/#global-cors-configuration

src/main/java/com/example/SpringBootStudy/SpringBootStudyApplication.java
```java
  @Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/api/v1/*").allowedOrigins("*");
			}
		};
	}
```
-->

## Swagger UI 설정
pom.xml
```xml
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.9.2</version>
</dependency>
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.9.2</version>
</dependency>
```

src/main/java/com/example/SpringBootStudy/SpringBootStudyApplication.java
```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
	@Bean
	public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2).select().apis(RequestHandlerSelectors.any())
				.paths(PathSelectors.any()).build();
	}
}
```
* http://localhost:8080/swagger-ui.html

<!-- pom.xml 파일에서 문제가 있을경우 2.9.2 -> 2.6.1 변경 후 다시 2.9.2 버전으로 돌아 온다. -->
