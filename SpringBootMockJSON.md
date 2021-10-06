# Spring Boot - Mock JSON

src/main/resources/json/member.json
```json
{
  "name": "홍길동",
  "age": 39
}
```

src/main/resources/json/members.json
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

src/test/java/패키지/{프로젝트명}Tests.java
```java
import java.lang.reflect.Type;
```
```java
@Test
void contextLoads() {
  Member member = getMockJSON("json/member.json", Member.class);
  System.out.println(member);

  List<Member> members = this.getMockJSON(
      "json/members.json",
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
