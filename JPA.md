# JPA

build.gradle
```gradle
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

src/main/resources/application.properties
```properties
database: testdb

spring.h2.console.path=/h2-console
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:${database};MODE=mysql;
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driverClassName=org.h2.Driver

spring.jpa.show-sql=true # Log에 SQL문 보기
spring.jpa.properties.hibernate.format_sql=true # Log에 SQL문 이쁘게 보기
spring.jpa.hibernate.ddl-auto=create # SessionFactory 시작시 스키마를 삭제하고 다시 생성 (개발시 유용)
spring.jpa.hibernate.connection.charSet=UTF-8
```
* https://haservi.github.io/posts/spring/hibernate-ddl-auto

src/main/java/{프로젝트}/models/Users.java
```java
import lombok.Data;
import javax.persistence.*;

@Entity
@Data
public class Users {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="user_pk")
    private long userPk;
    private String name;
    private Integer age;
}
```

src/main/java/{프로젝트}/repositories/UsersRepository.java
```java
@Repository
public interface UsersRepository extends JpaRepository<Users, Long> {
}
```
* `@Repository` 없어도 무방

src/main/test/{프로젝트}/DemoApplicationTests
```java
@SpringBootTest
// @DataJpaTest (스프링 설정과 상관없는 가상의 DB Table을 생성)
class DemoApplicationTests {
	@Autowired
	UsersRepository usersRepository;

	@Test
	void contextLoads() {
		Users users = new Users();
		users.setName("홍길동");
		users.setAge(39);
		usersRepository.save(users);

		List<Users> usersList = usersRepository.findAll();
		System.out.println(usersList);
	}
}
```
