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
Terminal <- git init
```
* `2.5.14 (SNAPSHOT)` SNAPSHOT 버전으로 생성해야 `Swagger UI`에서 오류가 발생 하지 않는다.

## Spring Boot Tomcat 서버 실행
src/main/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplication.java
```sh
public class SpringBootRestApiStudyApplication <- RUN 버튼 누르기
```

## 회원(Members) Rest API
src/main/java/com/example/SpringBootRestApiStudy/models/Member.java
* Model, DTO(Data Transfer Object) 상황에 따라서 달리 불려진다.
* VO(Value Object)는 주로 Request, Response 객체로 주로 사용 된다.
```java
public class Member {
    private String name;
    private Integer age;
}
```
* `Generate...`, `Constructor`,  `Getter and Setter` 설명

src/main/java/com/example/SpringBootRestApiStudy/models/MembersResponse.java
```java
public class MembersResponse {
    public String result;
    public List<Member> members;

    public MembersResponse() {}

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

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```java
@RestController
@CrossOrigin(origins = "*")
@RequestMapping("api/v1/members")
public class MembersController {
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
    // public MembersResponse membersRead(@ModelAttribute Member member) {
    // public MembersResponse membersRead(@RequestParam("name") String title, @RequestParam(required=false, defaultValue="1") int age) {
    public MembersResponse membersRead() {
        return new MembersResponse("read", members);
    }

    @RequestMapping(path = "/{index}", method = RequestMethod.DELETE)
    public MembersResponse membersDelete(@PathVariable("index") int index) {
        members.remove(index);
        return new MembersResponse("deleted");
    }

    @RequestMapping(path = "/{index}", method = {RequestMethod.PUT, RequestMethod.PATCH})
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
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
}
```
* pom.xml <- Maven <- Reload project
* Spring Boot(Tomcat) 재시작
* http://localhost:8080/swagger-ui.html
* ❕ 안될 경우: IntelliJ 재시작
* ❕ Spring Boot 2.6버전 이후 `documentationPluginsBootstrapper` 오류 발생 할 경우 `application.properties` 파일에 추가
```sh
spring.mvc.pathmatch.matching-strategy=ANT_PATH_MATCHER
```

<!-- pom.xml 파일에서 문제가 있을경우 2.9.2 -> 2.6.1 변경 후 다시 2.9.2 버전으로 돌아 온다. -->

<!--
### Gradle
* https://mvnrepository.com/artifact/io.springfox/springfox-swagger2/2.9.2

build.gradle
```gradle
implementation group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2'
```
* IntelliJ -> View -> Tool Windows -> Gradle -> Reload Gradle Project
-->

# MySQL 연동
* [mysql-curriculum](https://github.com/ovdncids/mysql-curriculum)
* Spring Boot 2.0 부터 HikariCP가 기본 Datasource로 사용 된다.

src/main/resources/application.properties
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/DB명
spring.datasource.username=계정
spring.datasource.password=비밀번호
```
```properties
# spring.datasource.url=jdbc:mysql://127.0.0.1:3306/DB명?useUnicode=true&characterEncoding=utf8
# spring.datasource.url=jdbc:mysql://127.0.0.1:3306/DB명?useUnicode=true&amp;characterEncoding=utf8
```

pom.xml
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.31</version>
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
* ❕ `select * from members`의 경우는 생성자의 파라미터 순서대로 DB에서 내려 받는다.
* ❕ `select name, age, member_pk from members`의 경우는 생성자의 파라미터 순서와 같이 않아도 받을 수 있다.
* ❕ 생성자를 사용하지 않으면 `select * from members`, `select name, age, member_pk from members` 모두 받을 수 있다.

## 1. JDBC(Java Database Connectivity) 연동 모듈
pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

### 회원(Members) Read
src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
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
mybatis.configuration.map-underscore-to-camel-case=true
mybatis.type-aliases-package=com.example.SpringBootRestApiStudy.models
mybatis.mapper-locations=classpath:mappers/*.xml
logging.level.com.example.SpringBootRestApiStudy=TRACE
```
* ❕ `com.example.SpringBootRestApiStudy` 패키지가 동일한지 확인
* ❕ SQL문 로깅이 잘 안 될 경우 `logging.level.com=TRACE` 해보자

### MySQL 접속 테스트 (JUnit)
#### 회원(Members) Read
src/main/resources/mappers/members.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "https://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.SpringBootRestApiStudy.repositories.MembersRepository">
    <select id="read" resultType="Member">
        select * from members
    </select>
</mapper>
```

src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
private final Logger logger = LoggerFactory.getLogger(this.getClass());

@Autowired
private SqlSessionFactory sqlSessionFactory;

@Test
void membersRead() {
    SqlSession sqlSession = sqlSessionFactory.openSession();
    List<Member> members = sqlSession.selectList("com.example.SpringBootRestApiStudy.repositories.MembersRepository.read");
    logger.info("Done: MembersRepository.read");
}
```
* 테스트를 사용하는 이유 (테스트 하고 싶은 부분만 바로 실행 할 수 있다.)
* `org.slf4j.Logger` 선택

#### 회원(Members) Create
src/main/resources/mappers/members.xml
```xml
<insert id="create" parameterType="Member">
    insert into members(name, age)
    values(#{name}, #{age})
</insert>
```

src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@Test
void membersCreate() {
    SqlSession sqlSession = sqlSessionFactory.openSession();
    Integer count = sqlSession.insert(
        "com.example.SpringBootRestApiStudy.repositories.MembersRepository.create",
        new Member(0, "홍길동", 39)
    );
    logger.info("Done: MembersRepository.create");
}
```

#### 회원(Members) Delete
src/main/resources/mappers/members.xml
```xml
<delete id="delete" parameterType="Integer">
    delete from members
    where member_pk = #{memberPk}
</delete>
```

src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@Test
void membersDelete() {
    SqlSession sqlSession = sqlSessionFactory.openSession();
    Integer count = sqlSession.delete(
        "com.example.SpringBootRestApiStudy.repositories.MembersRepository.delete",
        1
    );
    logger.info("Done: MembersRepository.delete");
}
```

#### 회원(Members) Update
src/main/resources/mappers/members.xml
```xml
<update id="update">
    update members set name = #{name}, age = #{age}
    where member_pk = #{memberPk}
</update>
```

src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@Test
void membersUpdate() {
    SqlSession sqlSession = sqlSessionFactory.openSession();
    Integer count = sqlSession.update(
        "com.example.SpringBootRestApiStudy.repositories.MembersRepository.update",
        new Member(2, "이순신", 33)
    );
    logger.info("Done: MembersRepository.update");
}
```
* `Controller`에서 `SqlSession` 적용해 보기

### Repository 인터페이스 매핑
#### 회원(Members) Read
src/main/java/com/example/SpringBootRestApiStudy/repositories/MembersRepository.java
```java
@Mapper
public interface MembersRepository {
    // @Select("select * from members")
    List<Member> read();
}
```
* ❕ Repository 인터페이스 생성하는 이유 (Controller나 Service 마다 SqlSessionFactory, SqlSession을 호출할 필요 없이, 바로 매핑까지 해준다.)

#### MembersController에서 Repository 사용하기
src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```java
@Autowired
private MembersRepository membersRepository;
```
```diff
- public MembersResponse membersRead() {
```
```java
public MembersResponse membersRead() {
    List<Member> members = membersRepository.read();
```

#### 회원(Members) Create
src/main/java/com/example/SpringBootRestApiStudy/repositories/MembersRepository.java
```java
Integer create(Member member);
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```diff
- members.add(member);
```
```java
membersRepository.create(member);
```
* `membersRepository.create(member);` 생성된 Member의 수를 반환한다.

##### (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator) 발생 한다면
src/main/java/com/example/SpringBootRestApiStudy/models/Member.java
```java
public Member() {}
```
* 또는 `Swagger` 새로고침

#### 회원(Members) Delete
src/main/java/com/example/SpringBootRestApiStudy/repositories/MembersRepository.java
```java
Integer delete(Integer memberPk);
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```diff
- members.remove(index);
```
```java
membersRepository.delete(memberPk);
```
+ index <- memberPk 수정
* `membersRepository.delete(memberPk);` 삭제된 Member의 수를 반환한다.

### 회원(Members) Update
src/main/resources/mappers/members.xml
```diff
- <update id="update">
-     update members set name = #{name}, age = #{age}
-     where member_pk = #{memberPk}
- </update>
```
```xml
<update id="update">
    update members set name = #{member.name}, age = #{member.age}
    where member_pk = #{memberPk}
</update>
```

src/main/java/com/example/SpringBootRestApiStudy/repositories/MembersRepository.java
```java
Integer update(@Param("memberPk") Integer memberPk, @Param("member") Member member);
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```diff
- members.set(index, member);
```
```java
membersRepository.update(memberPk, member);
```
+ index <- memberPk 수정
* `membersRepository.update(memberPk, member);` 수정된 Member의 수를 반환한다.

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```diff
- private static List<Member> init() {
- public static final List<Member> members = init();
```
src/main/java/com/example/SpringBootRestApiStudy/models/Member.java
```diff
- public Member(String name, Integer age) {
```

### 회원(Members) Service 만들기
* ❕ Service를 생성하는 이유 (여러 Controller에서 사용 되거나, 또는 Test(JUnit)에서 사용될 비지니스 로직(DB의 CRUD등등)을 담을때 사용한다.)

src/main/java/com/example/SpringBootRestApiStudy/services/MembersService.java
```java
@Service
public class MembersService {
    @Autowired
    private MembersRepository membersRepository;

    public List<Member> read() {
        return membersRepository.read();
    }

    public Integer create(Member member) {
        return membersRepository.create(member);
    }

    public Integer delete(Integer memberPk) {
        return membersRepository.delete(memberPk);
    }

    public Integer update(@Param("memberPk") Integer memberPk, @Param("member") Member member) {
        return membersRepository.update(memberPk, member);
    }
}
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```diff
- private MembersRepository membersRepository;
+ private MembersService membersService;
```
* membersRepository <- membersService

#### 회원(Members) Service 테스트(JUnit)에서 호출 하기
src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@Autowired
private MembersService membersService;

@Test
void members() {
    List<Member> members = membersService.read();
    membersService.create(new Member(null, "홍길동", 39));
    membersService.delete(0);
    membersService.update(0, new Member(null, "이순신", 33));
}
```
* ❕ 테스트에서도 Service를 자유롭게 사용 할 수 있다.

<!--
#### Repository에서 바로 쿼리문 사용하기
src/main/java/com/example/SpringBootRestApiStudy/repositories/MembersRepository.java
```java
@Select("select * from members")
List<Member> select();
```

src/main/java/com/example/SpringBootRestApiStudy/services/MembersService.java
```java
public List<Member> select() {
    return membersRepository.select();
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

src/main/java/com/example/SpringBootRestApiStudy/services/MembersService.java
```diff
- @Service
- 밑으로 모두
```
```java
public interface MembersService {
    List<Member> read();
    Integer create(Member member);
    Integer delete(Integer memberPk);
    Integer update(@Param("memberPk") Integer memberPk, @Param("member") Member member);
}
```

src/main/java/com/example/SpringBootRestApiStudy/services/MembersServiceImpl.java
```diff
- @Service
- public class MembersService {
```
```java
@Service("MembersService")
public class MembersServiceImpl implements MembersService {
```

* ❕ 결론 Service와 ServiceImpl의 관계가 1:1인 경우는 service와 serviceImpl로 따로 나눌 필요 없다.
* 1:N 관계가 된다면 그때 생각해 보자 (하지만 필자는 아직 1:N 상황을 만난적 없음)
* 하지만 회사에서 `Service와 ServiceImpl`을 나누어 사용한다고 하면, 회사의 룰에 따른다.
* https://elvis-note.tistory.com/entry/9-Spring-MVC-2-Service%EC%99%80-ServiceImpl
* https://multifrontgarden.tistory.com/97

<!-- APO: 쉽게 미들웨어라고 생각하면 쉽다. -->

## 특정 컬럼 추가/삭제 후 Response 하기
src/main/java/com/example/SpringBootRestApiStudy/models/Member.java
```java
@JsonIgnoreProperties({"memberPk", "age"})
class MemberRead extends Member {
    public String add;
}
```

src/main/resources/mappers/members.xml
```diff
- <select id="read" resultType="Member">
+ <select id="read" resultType="MemberRead">
```

# 기타 라이브러리
## @Slf4j log
src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
@SpringBootTest
class SpringBootRestApiStudyApplicationTests {
    @Test
    void contextLoads() {
        log.info("@Slf4j");
    }
}
```
* ❕ `@Slf4j`는 `log` 함수를 바로 사용하게 해준다.
* `@Slf4j` 임포트가 잘 안되면 (Add library 'Maven ... lombok' 선택)

### STS4
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <!-- <version>1.18.24</version> -->
    <scope>compile</scope>
</dependency>
```
* Maven Dependencies > lombok-1.18.24.jar > Run As > Java Application > Install / Update > Project Clear... > STS4 종료
* /Applications/SpringToolSuite4.app/Contents/Eclipse/SpringToolSuite4.ini
```ini
-javaagent:/Applications/SpringToolSuite4.app/Contents/Eclipse/lombok-1.18.24.jar
```
* STS4 시작

## Properties
### 커스텀 Properties 읽기
src/main/resources/custom.properties
```properties
a1=123
b1.b2=한글
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```java
@Slf4j
@PropertySource(value = "classpath:custom.properties", encoding="UTF-8")
```
```java
@Value("${a1}") private Integer a1;
@Value("${b1.b2}") private String b2;
```
```java
log.info(a1.toString());
log.info(b2);
```

<!--
#### @TestPropertySource(value = "classpath:custom.properties") encoding 없는 문제
src/test/java/com/example/SpringBootRestApiStudy/SpringBootRestApiStudyApplicationTests.java
```java
@TestPropertySource(value = "classpath:custom.properties")
@Value("${b1.b2}") private String b2;
log.info(b2);
```
* Run -> Edit Configurations... -> Environment variables: `file.encoding=UTF-8`
-->

### local, production Properties 나누기
src/main/resources/application-local.properties
```properties
property.c1=local
```

src/main/resources/application-production.properties
```properties
property.c1=production
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```java
@Value("${property.c1}") private String c1;
```
```java
log.info(c1);
```
* Run -> Edit Configurations... -> Environment variables: `spring.profiles.active=local`
* Command line인 경우: spring-boot:run -Dspring.profiles.active=local,db-dev
* 잘 안되면 `@Value("${spring.profiles.active}") private String active;` 확인

#### Environment variables 여러개 사용
src/main/resources/application-db-local.properties
```properties
property.d1=db-local
```

src/main/resources/application-db-production.properties
```properties
property.d1=db-production
```

src/main/java/com/example/SpringBootRestApiStudy/controllers/MembersController.java
```java
@Value("${property.d1}") private String d1;
```
```java
log.info(d1);
```
* Run -> Edit Configurations... -> Environment variables: `spring.profiles.active=local,db-local`

#### properties 우선 순위
src/main/resources/application.properties
```properties
a1=1
b1.b2=b2
property.c1=c1 
property.d1=d1
```
* https://www.whiteship.me/spring-boot-external-config

#### static으로 프로퍼티 받기
* https://github.com/ovdncids/java-curriculum/blob/master/SpringBootHttp.md#%EC%BB%A4%EC%8A%A4%ED%85%80-properties-model%EB%A1%9C-%EB%B0%9B%EA%B8%B0
