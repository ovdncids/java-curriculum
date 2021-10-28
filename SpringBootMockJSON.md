# Spring Boot - Mock JSON

src/main/resources/json/Member.json
```json
{
    "name": "홍길동",
    "age": 39
}
```

src/main/resources/json/Members.json
```json
[
    {
        "name": "홍길동",
        "age": 39
    },
    {
        "name": "김삼순",
        "age": 33
    },
    {
        "name": "홍명보",
        "age": 44
    },
    {
        "name": "박지삼",
        "age": 22
    },
    {
        "name": "권명순",
        "age": 10
    }
]
```

<!--
## Gson
src/test/java/패키지/{프로젝트명}Tests.java
```java
import java.lang.reflect.Type;
```
```java
@Test
void contextLoads() {
    Member member = getMockJSON("json/Member.json", Member.class);
    System.out.println(member);

    List<Member> members = getMockJSON(
            "json/Members.json",
            new TypeToken<List<Member>>(){}.getType()
    );
    System.out.println(members);
}

static <T>T getMockJSON(String filePath, Type type) {
    try {
        ClassPathResource resource = new ClassPathResource(filePath);
        Path path = Paths.get(resource.getURI());
        Reader reader = new FileReader(path.toString());
        Gson gson = new Gson();
        return gson.fromJson(reader, type);
    } catch (Exception exception) {
        exception.printStackTrace();
        return null;
    }
}
```
-->

## Jackson
src/test/java/패키지/{프로젝트명}Tests.java
```java
static Path getResource(String filePath) {
    try {
        ClassPathResource resource = new ClassPathResource(filePath);
        return Paths.get(resource.getURI());
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

static String getMockJSON(String filePath) {
    try {
        return new String(Files.readAllBytes(getResource(filePath)));
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

static <T>T getMockJSON(String filePath, Class<T> valueType) {
    ObjectMapper mapper = new ObjectMapper();
    try {
        return mapper.readValue(new File(getResource(filePath).toUri()), valueType);
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

static <T>T getMockJSON(String filePath, TypeReference<T> valueTypeRef) {
    ObjectMapper mapper = new ObjectMapper();
    try {
        return mapper.readValue(new File(getResource(filePath).toUri()), valueTypeRef);
    } catch (IOException ioException) {
        ioException.printStackTrace();
        return null;
    }
}

@Test
void testMember() {
    String jsonMember = getMockJSON("json/Member.json");
    System.out.println(jsonMember);

    Member member = getMockJSON("json/Member.json", Member.class);
    System.out.println(member);

    List<Member> members = getMockJSON(
            "json/Members.json",
            new TypeReference<List<Member>>(){}
    );
    System.out.println(members);
}
```
* ❕ `Model`의 `멤버 변수`들이 `private`일 경우, `public 빈 생성자` 또는 `get`, `set` 메서드가 있어야 한다.

### Java Lambda Expression
```java
members.forEach((Member member) ->
    System.out.println(member)
);

members.forEach(member -> {
    System.out.println(member);
});
```
* `Python`의 `lambda` 형식으로 실행 가능

## Lombok 어노테이션
* `@ToString(exclude = "password")` toString()에서 password만 제외 시킨다.
* `@NoArgsConstructor` 파라미터가 없는 기본 생성자를 만들어 준다.
* `@AllArgsConstructor` 모든 필드를 파라미터로 받는 생성자를 만들어 준다.
* `@RequiredArgsConstructor` final 또는 @NonNull 필드만 파라미터로 받는 생성자를 만들어 준다.
* `@EqualsAndHashCode` equals(내용이 동일), hashCode(동일 객체) 함수를 만들어준다.
* `@EqualsAndHashCode(callSuper = true)` 부모 클래스 필드도 동일한지 비교한다.
* `@Data` @Getter, @Setter, @RequiredArgsConstructor, @ToString, @EqualsAndHashCode 한번에 설정 해준다.

### @Builder
```java
@Data
@Builder
public static class User {
    private String name;
    @Builder.Default
    private int age = 39;
}

@Test
void testBuilder() {
    User user = User.builder().name("홍길동").build();
    System.out.println(user);
}
```

### @SuperBuilder
```java
@SuperBuilder
public static class Parent {
    private String p1;
}

@SuperBuilder
public static class Child extends Parent {
    private String c1;
}

@Test
void testBuilder() {
    Child child = Child.builder().p1("p1").c1("c1").build();
    System.out.println(child);
}
```
* ❕ `@SuperBuilder` 대신 `@Builder`를 사용하면 .p1("p1") 사용할 수 없다.

### 예시: UsersResult
src/main/resources/json/UsersResult.json
```json
{
    "RESULT": {
        "USERS": [
            {
                "NAME": "홍길동",
                "AGE": 39
            },
            {
                "NAME": "김삼순",
                "AGE": 33
            }
        ]
    }
}
```

src/test/java/패키지/{프로젝트명}Tests.java
```java
@Data
public static class UsersResult {
    @JsonAlias("RESULT")
    private UserList result;

    @Data
    public static class UserList {
        @JsonAlias("USERS")
        private List<User> users;
    }

    @Data
    public static class User {
        @JsonAlias("NAME")
        private String name;
        @JsonAlias("AGE")
        private int age;
        private String from;
    }
}

@Test
void testUsersResult() {
    UsersResult users = getMockJSON("json/UsersResult.json", UsersResult.class);
    users.getResult().getUsers().stream().forEach(user -> user.setFrom("Korea"));
    System.out.println(users);
}
```

## 예외
### Cannot construct instance of `java.time.LocalDateTime`
```java
@JsonDeserialize(using = LocalDateTimeDeserializer.class)
```

