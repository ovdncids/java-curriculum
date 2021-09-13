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

## .jsp 파일 읽기
src/main/webapp/test.jsp
```jsp
<%="test"%>
```
* http://localhost:8080/test.jsp
* pom.xml 설정 없이도 webapp 경로의 파일은 접근 가능하다. 하지만 jsp를 컴파일 하지 못한다.

### .jsp 파일 컴파일 하기
pom.xml
```xml
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>
```
* pom.xml <- Maven <- Download Sources
* IntelliJ 재시작
* http://localhost:8080/test.jsp

## View 만들기
src/main/webapp/WEB-INF/jsp/members.jsp
```jsp
<%="members.jsp"%>
```

src/main/java/com/example/SpringBootMvcStudy/controllers/Members.java
```diff
- String membersRead() {
-     return "membersRead";
- }
```
```java
ModelAndView membersRead() {
    return new ModelAndView("members");
}
```

src/main/resources/application.properties
```properties
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```
<!--
# 컨테이너에 기본 서블릭을 등록한다. Spring boot 2.4 버전 부터는 기본이 false이다. (무슨 차이인지 ...)
server.servlet.register-default-servlet=true
-->
* ❔ `Search.java` 적용해 보기

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
