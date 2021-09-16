# Spring Boot - Rest API

## 설치
* https://start.spring.io
* https://sidepower.tistory.com/352
```sh
Project: Maven Project
Language: Java
Spring Boot: 2.5.4 (현재)
Project Metadata
  Artifact: SpringBootRestApiStudy
Packaging: Jar
Java: 8
Dependencies: Spring Web

GENERATE <- SpringBootRestApiStudy.zip 다운받기
압축 풀고 해당 경로를 Intellij에서 Open
```

## Spring Boot Tomcat 서버 실행
src/main/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplication.java
```sh
public class SpringBootRestApiStudyApplication <- RUN 버튼 누르기
```

## 회원(Members) Rest API
src/main/java/com/example/SpringBootRestApiStudy/models/Member.java
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

src/main/java/com/example/SpringBootRestApiStudy/models/MembersResponse.java
```java
public class MembersResponse {
    public String result;
    public List<Member> members;

    public MembersResponse(String result) {
        this.result = result;
    }

    public MembersResponse(String result, List<Member> members) {
        this.result = result;
        this.members = members;
    }
}
```

* ❕ 접근권한을 private로 만든 경우는 `Getter and Setter`가 필수 이지만, 접근권한을 public으로 만든 경우는 없어도 된다.

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```java
@RestController
@CrossOrigin(origins = "*")
@RequestMapping("api/v1/members")
public class Members {
    private static List<Member> init() {
        List<Member> members = new ArrayList<>();
        members.add(new Member("홍길동", 39));
        members.add(new Member("김삼순", 33));
        members.add(new Member("홍명보", 44));
        members.add(new Member("박지삼", 22));
        members.add(new Member("권명순", 10));
        return members;
    }
    public static final List<Member> members = init();

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
* http://localhost:8080/api/v1/members

<!--
### Global CORS configuration
https://spring.io/guides/gs/rest-service-cors/#global-cors-configuration

src/main/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplication.java
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

src/main/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplication.java
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
* pom.xml <- Maven <- Reload project
* Spring Boot(Tomcat) 재시작
* http://localhost:8080/swagger-ui.html
* ❕ 안될 경우: IntelliJ 재시작

<!-- pom.xml 파일에서 문제가 있을경우 2.9.2 -> 2.6.1 변경 후 다시 2.9.2 버전으로 돌아 온다. -->

# MySQL 연동
* Spring Boot 2.0 부터 HikariCP가 기본 Datasource로 사용 된다.

src/main/resources/application.properties
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/DB명
spring.datasource.username=계정
spring.datasource.password=비밀번호
```

pom.xml
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

src/main/java/com/example/SpringBootRestApiStudy/models/Member.java
```diff
- public Member(String name, Integer age) {
-     this.name = name;
-     this.age = age;
- }
```

```java
private Integer memberPk;

public Member(Integer memberPk, String name, Integer age) {
    this.memberPk = memberPk;
    this.name = name;
    this.age = age;
}

public Integer getMemberPk() {
    return memberPk;
}

public void setMemberPk(Integer memberPk) {
    this.memberPk = memberPk;
}
```

## 1. JDBC 연동 모듈
pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

### 회원(Members) Read
src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```java
@Autowired
JdbcTemplate jdbcTemplate;
```
```diff
- public MembersResponse membersRead() {
```
```java
public MembersResponse membersRead() {
    String query = "select * from members";
    List<Member> members = jdbcTemplate.query(query, (rs, rowNum) ->
        new Member(rs.getInt("member_pk"), rs.getString("name"), (Integer) rs.getObject("age"))
    );
```

* @Autowired 관련 문서
* https://laycoder.tistory.com/109
* https://medium.com/@jang.wangsu/di-dependency-injection-%EC%9D%B4%EB%9E%80-1b12fdefec4f

### 회원(Members) Create
```diff
- members.add(member);
```
```java
String query = "insert into members(name, age) values(?, ?)";
jdbcTemplate.update(query, member.getName(),member.getAge());
```

### 회원(Members) Delete
```diff
- members.remove(index);
```
```java
String query = "delete from members where member_pk = ?";
jdbcTemplate.update(query, index);
```

### 회원(Members) Update
```diff
- members.set(index, member);
```
```java
String query = "update members set name = ?, age = ? where member_pk = ?";
jdbcTemplate.update(query, member.getName(),member.getAge(), index);
```

## 2. MyBatis 모듈
https://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure

pom.xml
```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.0</version>
</dependency>
```
* ❕ Spring Boot 버전에 맞는 MyBatis를 넣어야 한다.

src/main/resources/application.properties
```properties
# mybatis.configuration.map-underscore-to-camel-case=true
mybatis.type-aliases-package=com.example.SpringBootRestApiStudy.models
mybatis.mapper-locations=classpath:mappers/*.xml
```

### 회원(Members) Read
src/main/resources/mappers/members.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "mybatis-3-mapper.dtd">

<mapper namespace="com.example.SpringBootRestApiStudy.api.v1.MembersMapper.read">
    <select id="read" resultType="Member">
        select * from members
    </select>
</mapper>
```

#### MySQL 접속 테스트
src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@Autowired
private SqlSessionFactory sqlSessionFactory;

@Test
void membersRead() {
    SqlSession sqlSession = sqlSessionFactory.openSession();
    List<Member> members = sqlSession.selectList("com.example.SpringBootRestApiStudy.api.v1.MembersMapper.read");
    System.out.println(members);
}
```
* 테스트를 사용하는 이유 (테스트 하고 싶은 부분만 바로 실행 할 수 있다.)

#### Mapper 인터페이스 생성
src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersMapper.java
```java
@Mapper
public interface MembersMapper {
    List<Member> read();
}
```
* ❕ Mapper 인터페이스 생성하는 이유 (Controller 마다 SqlSessionFactory, SqlSession을 호출할 필요 없고, 바로 매핑까지 해준다.)

#### MembersController에서 Mapper 사용하기
src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```java
@Autowired
private MembersMapper membersMapper;
```
```diff
- public MembersResponse membersRead() {
```
```java
public MembersResponse membersRead() {
    List<Member> members = membersMapper.read();
```

### 회원(Members) Create
src/main/resources/mappers/members.xml
```xml
<insert id="create" parameterType="Member">
    insert into members(name, age) values(#{name}, #{age})
</insert>
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersMapper.java
```java
Integer create(Member member);
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```diff
- members.add(member);
```
```java
membersMapper.create(member);
```
* `membersMapper.create(member);` 생성된 Member의 수를 반환한다.

### 회원(Members) Delete
src/main/resources/mappers/members.xml
```xml
<delete id="delete" parameterType="Integer">
    delete from members where member_pk = #{memberPk}
</delete>
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersMapper.java
```java
Integer delete(Integer memberPk);
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```diff
- members.remove(index);
```
```java
membersMapper.delete(memberPk);
```
+ index <- memberPk 수정
* `membersMapper.delete(memberPk);` 삭제된 Member의 수를 반환한다.

### 회원(Members) Update
src/main/resources/mappers/members.xml
```xml
<update id="update">
    update members set name = #{member.name}, age = #{member.age} where member_pk = #{memberPk}
</update>
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersMapper.java
```java
Integer update(@Param("memberPk") Integer memberPk, @Param("member") Member member);
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```diff
- members.set(index, member);
```
```java
membersMapper.update(memberPk, member);
```
+ index <- memberPk 수정
* `membersMapper.update(memberPk, member);` 수정된 Member의 수를 반환한다.

<!--
DAO, VO
# Log
https://atoz-develop.tistory.com/entry/Spring-Boot-MyBatis-%EC%84%A4%EC%A0%95-%EB%B0%A9%EB%B2%95
-->

### 회원(Members) Service 만들기
* ❕ Service를 생성하는 이유 (여러 Controller에서 사용 되거나, 또는 Test에서 사용될 비지니스 로직을 담을때 사용한다.)

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersService.java
```java
@Service
public class MembersService {
    @Autowired
    private MembersMapper membersMapper;

    public List<Member> read() {
        return membersMapper.read();
    }

    public Integer create(Member member) {
        return membersMapper.create(member);
    }

    public Integer delete(Integer memberPk) {
        return membersMapper.delete(memberPk);
    }

    public Integer update(@Param("memberPk") Integer memberPk, @Param("member") Member member) {
        return membersMapper.update(memberPk, member);
    }
}
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersController.java
```diff
- private MembersMapper membersMapper;
+ private MembersService membersService;
```
* membersMapper <- membersService

#### 회원(Members) Service 테스트에서 호출 하기
src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@Autowired
private MembersService membersService;

@Test
void members() {
    List<Member> members = membersService.read();
    membersService.create(new Member(null, "semin", 20));
    membersService.delete(0);
    membersService.update(0, new Member(null, "min", 30));
}
```
* ❕ 테스트에서도 Service를 자유롭게 사용 할 수 있다.

<!--
#### Mapper에서 바로 쿼리문 사용하기
src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersMapper.java
```java
@Select("select * from members")
List<Member> select();
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersService.java
```java
public List<Member> select() {
    return membersMapper.select();
}
```

src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
members = membersService.select();
```
-->

### Service와 ServiceImpl
* 관습적으로 Service를 만들때 Service와 ServiceImpl로 나누어서 개발하는 곳이 많다.

MembersService.java 복사해서 MembersServiceImpl.java

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersService.java
```diff
- @Service
```
```java
public interface MembersService {
    List<Member> read();
    Integer create(Member member);
    Integer delete(Integer memberPk);
    Integer update(@Param("memberPk") Integer memberPk, @Param("member") Member member);
}
```

src/main/java/com/example/SpringBootRestApiStudy/api/v1/MembersServiceImpl.java
```diff
- @Service
- public class MembersService {
```
```java
@Service("MembersService")
public class MembersServiceImpl implements MembersService {
```

* ❕ 결론 Service와 ServiceImpl의 관계가 1:1인 경우는 service와 serviceImpl로 따로 나눌 필요 없다.
* 1:N 관계가 된다면 생각해 보자 (하지만 아직 1:N 상황을 만난적 없음)
* https://elvis-note.tistory.com/entry/9-Spring-MVC-2-Service%EC%99%80-ServiceImpl
* https://multifrontgarden.tistory.com/97
