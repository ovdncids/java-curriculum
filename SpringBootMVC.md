# Spring Boot - MVC 패턴

## 설치
* https://start.spring.io
* https://sidepower.tistory.com/352
```sh
Project: Maven Project
Language: Java
Spring Boot: 2.5.4 (현재)
Project Metadata
  Artifact: SpringBootMvcStudy
Packaging: Jar
Java: 8
Dependencies: Spring Web

GENERATE <- SpringBootMvcStudy.zip 다운받기
압축 풀고 해당 경로를 Intellij에서 Open
```

## Spring Boot Tomcat 서버 실행
src/main/java/com/example/SpringBootMvcStudy/SpringBootMvcStudyApplication.java
```sh
public class SpringBootMvcStudyApplication <- RUN 버튼 누르기
```

## Controller 만들기
* Controller는 웹 URL 경로를 생성해 준다.

src/main/java/com/example/SpringBootMvcStudy/SpringBootMvcStudyApplication.java
```java
@Controller
public class SpringBootMvcStudyApplication {
  ...
  
  @RequestMapping("/")
	@ResponseBody
	String root() {
    return "Hello world!";
	}
```

```diff
- return "Hello world!";
+ return "<script>document.location.href = '/membersRead';</script>";
```

src/main/java/com/example/SpringBootMvcStudy/controllers/Members.java
```java
@Controller
public class Members {
    @RequestMapping("/membersRead")
    @ResponseBody
    String membersRead() {
        return "membersRead";
    }
}
```

src/main/java/com/example/SpringBootMvcStudy/controllers/Search.java
```java
@Controller
public class Search {
    @RequestMapping("/search")
    @ResponseBody
    String search() {
        return "search";
    }
}
```

## Model 만들기 - 회원(Members)
* Model은 데이터 구조 또는 스키마라고 한다.

src/main/java/com/example/SpringBootMvcStudy/models/Member.java
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
