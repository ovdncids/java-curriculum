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

## Jackson
src/test/java/패키지/{프로젝트명}Tests.java
```java
@Test
void contextLoads() {
  Member member = getMockJSON("json/Member.json", Member.class);
  System.out.println(member);

  List<Member> members = getMockJSON(
      "json/Members.json",
      new TypeReference<List<Member>>(){}
  );
  System.out.println(members);
}

static <T>T getMockJSON(String filePath, Class<T> valueType) {
  try {
    ClassPathResource resource = new ClassPathResource(filePath);
    Path path = Paths.get(resource.getURI());
    ObjectMapper mapper = new ObjectMapper();
    return mapper.readValue(new File(path.toString()), valueType);
  } catch (Exception exception) {
    exception.printStackTrace();
    return null;
  }
}

static <T>T getMockJSON(String filePath, TypeReference<T> valueTypeRef) {
  try {
    ClassPathResource resource = new ClassPathResource(filePath);
    Path path = Paths.get(resource.getURI());
    ObjectMapper mapper = new ObjectMapper();
    return mapper.readValue(new File(path.toString()), valueTypeRef);
  } catch (Exception exception) {
    exception.printStackTrace();
    return null;
  }
}
```
* ❕ `Model`의 `멤버 변수`들이 `private`일 경우 `빈 생성자`가 있어야 한다.
