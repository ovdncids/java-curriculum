# Spring Boot - HTTP 통신

## 설치
* 프로젝트명: SpringBootHttpStudy
* Spring Boot - Rest API의 Swagger UI 설정까지 동일

### 서버 포트 바꾸기
src/main/resources/application.properties
```properties
server.port=18080
```

또는

* Run -> Edit Configurations... -> Environment variables: `server.port=18080`

## 회원(Members) Service 만들기
* ❕ Service를 생성하는 이유 (여러 Controller에서 사용 되거나, 또는 Test(JUnit)에서 사용될 비지니스 로직(DB의 CRUD등등)을 담을때 사용한다.)

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```java
@Service
public class MembersService {
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

    public List<Member> read() {
        return members;
    }

    public Integer create(Member member) {
        members.add(member);
        return members.size();
    }

    public Integer delete(int index) {
        members.remove(index);
        return members.size();
    }

    public Integer update(int index, Member member) {
        members.set(index, member);
        return members.size();
    }
}
```

### 회원(Members) Controller에서 Service 사용
src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersController.java
```java
@Autowired
MembersService membersService;
```
```diff
# Create
- members.add(member);
+ membersService.create(member);

# Read
- return new MembersResponse("read", members);
+ return new MembersResponse("read", membersService.read());

# Delete
- members.remove(index);
+ membersService.delete(index);

# Update
- members.set(index, member);
+ membersService.update(index, member);
```

### 회원(Members) Service 테스트(JUnit)에서 호출 하기
* 테스트를 사용하는 이유 (테스트 하고 싶은 부분만 바로 실행 할 수 있다.)

src/test/java/com/example/SpringBootHttpStudy/SpringBootHttpStudyApplicationTests.java
```java
@Autowired
private MembersService membersService;

@Test
void members() {
    List<Member> members = membersService.read();
    membersService.create(new Member("semin", 20));
    membersService.delete(0);
    membersService.update(0, new Member("min", 30));
}
```
* ❕ 테스트에서도 Service를 자유롭게 사용 할 수 있다.
