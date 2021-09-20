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

# httpclient5
## 설치
pom.xml
```xml
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
</dependency>
```

## 회원(Members) Read
src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```java
// @SuppressWarnings("unchecked")
public List<Member> read() throws Exception {
    HttpGet httpGet = new HttpGet("http://localhost:8080/api/v1/members");
    httpGet.addHeader("Content-Type", "application/json");
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpGet);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("members"));
    httpClient.close();
    return (List<Member>) linkedHashMap.get("members");
}
```

## 회원(Members) Create
```java
public Integer create(Member member) throws Exception {
    HttpPost httpPost = new HttpPost("http://localhost:8080/api/v1/members");
    httpPost.addHeader("Content-Type", "application/json");
    Gson gson = new Gson();
    StringEntity stringEntity = new StringEntity(gson.toJson(member), StandardCharsets.UTF_8);
    httpPost.setEntity(stringEntity);
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpPost);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

## 회원(Members) Delete
```java
public Integer delete(int index) throws Exception {
    HttpDelete httpDelete = new HttpDelete("http://localhost:8080/api/v1/members/" + index);
    httpDelete.addHeader("Content-Type", "application/json");
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpDelete);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

## 회원(Members) Update
```java
public Integer update(int index, Member member) throws Exception {
    HttpPatch httpPatch = new HttpPatch("http://localhost:8080/api/v1/members/" + index);
    httpPatch.addHeader("Content-Type", "application/json");
    Gson gson = new Gson();
    StringEntity stringEntity = new StringEntity(gson.toJson(member), StandardCharsets.UTF_8);
    httpPatch.setEntity(stringEntity);
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpPatch);
    System.out.println(httpResponse.getCode());
    String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(jsonString);
    JSONParser jsonParser = new JSONParser(jsonString);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

## Httpclient5 Service 또는 공용 함수 만들기
src/main/java/com/example/SpringBootHttpStudy/api/v1/common/Httpclient5.java
```java
public class Httpclient5 {
    public static void connect(String method, String url) throws Exception {
        HttpUriRequestBase httpUriRequestBase;
        if ("POST".equals(method)) {
            httpUriRequestBase = new HttpPost(url);
        } else if ("PATCH".equals(method)) {
            httpUriRequestBase = new HttpPatch(url);
        } else if ("DELETE".equals(method)) {
            httpUriRequestBase = new HttpDelete(url);
        } else {
            httpUriRequestBase = new HttpGet(url);
        }
        httpUriRequestBase.addHeader("Content-Type", "application/json");
        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse httpResponse = httpClient.execute(httpUriRequestBase);
        System.out.println(httpResponse.getCode());
        String jsonString = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
        System.out.println(jsonString);
        JSONParser jsonParser = new JSONParser(jsonString);
        LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
        httpClient.close();
    }
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```java
public List<Member> read() throws Exception {
    Httpclient5.connect("GET", "http://localhost:8080/api/v1/members");
    return null;
}
```

### 통신 후에 결과를 담을 model 생성 
src/main/java/com/example/SpringBootHttpStudy/api/v1/models/Httpclient5Response.java
```java
public class Httpclient5Response {
    private int code = 0;
    private String responseJsonString;
    private LinkedHashMap<String, Object> responseMap;
}
```
* Generate... -> Getter and Setter -> 생성

src/main/java/com/example/SpringBootHttpStudy/api/v1/common/Httpclient5.java
```java
public static Httpclient5Response connect(String method, String url) throws Exception {
    Httpclient5Response httpclient5Response = new Httpclient5Response();
    ...
    httpclient5Response.setCode(httpResponse.getCode());
    httpclient5Response.setResponseJsonString(jsonString);
    httpclient5Response.setResponseMap(linkedHashMap);
    return httpclient5Response;
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```diff
- Httpclient5.connect("GET", "http://localhost:8080/api/v1/members");
- return null;
```
```java
String url = "http://localhost:8080/api/v1/members";
Httpclient5Response httpclient5Response = Httpclient5.connect("GET", url);
return (List<Member>) httpclient5Response.getResponseMap().get("members");
```

### 회원(Members) Service Delete
```java
public Integer delete(int index) throws Exception {
    String url = "http://localhost:8080/api/v1/members/" + index;
    Httpclient5.connect("DELETE", url);
    return null;
}
```

### 회원(Members) Service Create, Update
```java
public Integer create(Member member) throws Exception {
    String url = "http://localhost:8080/api/v1/members";
    Httpclient5.connect("POST", url, member);
    return null;
}
```
* ❔ 아래 부분을 `Httpclient5.connect` 함수에 적용해 보기
```java
Gson gson = new Gson();
StringEntity stringEntity = new StringEntity(gson.toJson(member), StandardCharsets.UTF_8);
httpPost.setEntity(stringEntity);
```
* <details><summary>정답</summary>

  ```diff
  - public static Httpclient5Response connect(String method, String url) throws Exception {
  + public static Httpclient5Response connect(String method, String url, Object entity) throws Exception {
  ```
  ```java
  if (entity != null) {
      Gson gson = new Gson();
      StringEntity stringEntity = new StringEntity(gson.toJson(entity), StandardCharsets.UTF_8);
      httpUriRequestBase.setEntity(stringEntity);
  }
  ```
</details>

* ❔ `update` 함수도 적용해 보기
* ❔ `String url` 멤버 변수로 빼기

## Httpclient5 get, post, patch, delete 함수 만들기
src/main/java/com/example/SpringBootHttpStudy/api/v1/models/Httpclient5Response.java
```java
public static Httpclient5Response get(String url) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpGet(url);
    return connect(httpUriRequestBase, null);
}

public static Httpclient5Response post(String url, Object entity) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpPost(url);
    return connect(httpUriRequestBase, entity);
}

public static Httpclient5Response delete(String url) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpDelete(url);
    return connect(httpUriRequestBase, null);
}

public static Httpclient5Response patch(String url, Object entity) throws Exception {
    HttpUriRequestBase httpUriRequestBase = new HttpPatch(url);
    return connect(httpUriRequestBase, entity);
}
```
* ❔ Httpclient5Response 함수 수정 하기
* ❔ Service 수정 하기

## Query string 받아서 넘기기
src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersController.java
```java
public MembersResponse membersRead(@ModelAttribute Member member) throws Exception {
    return new MembersResponse("read", membersService.read(member));
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```diff
- public List<Member> read() throws Exception {
+ public List<Member> read(Member member) throws Exception {
```
* ❕ Problem이 발생 하면 Problems 탭에서 확인
* Swagger에서 확인

### Httpclient5에서 Query string을 받을 수 있는 함수 만들기
```diff
- Httpclient5Response httpclient5Response = Httpclient5.get(url);
+ Httpclient5Response httpclient5Response = Httpclient5.getQuery(url);
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/models/Httpclient5Response.java
```java
public static String uriBuilder(String url, Object query) throws Exception {
    URIBuilder uriBuilder = new URIBuilder(url);
    Gson gson = new Gson();
    JsonObject jsonObject = gson.toJsonTree(query).getAsJsonObject();
    for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
        System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue() );
        uriBuilder.addParameter(entry.getKey(), entry.getValue().toString());
    }
    url = uriBuilder.build().toString();
    System.out.println(url);
    return url;
}

public static Httpclient5Response getQuery(String url, Object query) throws Exception {
    url = uriBuilder(url, query);
    return get(url);
}
```

* ❔ postQuery, patchQuery, deleteQuery 함수 만들기

## 커스텀 Properties Model로 받기
src/main/resources/custom.properties
```properties
a1=123
b1.b2=한글
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/models/CustomProperties.java
```java
@Component
@PropertySource(value="classpath:custom.properties", encoding="UTF-8")
public class CustomProperties {
    @Value("${a1}")
    private Integer a1;
    @Value("${b1.b2}")
    private String b2;

    private static Integer _a1;
    private static String _b2;

    private CustomProperties() {}

    @PostConstruct
    private void init() {
        _a1 = this.a1;
        _b2 = this.b2;
    }

    public static CustomProperties getAll() {
        CustomProperties properties = new CustomProperties();
        properties.a1 = _a1;
        properties.b2 = _b2;
        return properties;
    }
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```diff
- Httpclient5Response httpclient5Response = Httpclient5.getQuery(url, member);
+ Httpclient5Response httpclient5Response = Httpclient5.getQuery(url, CustomProperties.getAll());
```
* `@Component`와 `@PostConstruct` 동작 설명

## 객체 Merge 해주는 함수 만들기
src/main/java/com/example/SpringBootHttpStudy/api/v1/models/Httpclient5Response.java
```java
public static Map<String, Object> gsonMerge(Object[] objects) {
    Map<String, Object> map = new HashMap<>();
    for (Object object : objects) {
        Gson gson = new Gson();
        JsonObject jsonObject = gson.toJsonTree(object).getAsJsonObject();
        for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
            map.put(entry.getKey(), entry.getValue());
        }
    }
    return map;
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```java
Object[] object = {member, CustomProperties.getAll()};
```
```diff
- Httpclient5Response httpclient5Response = Httpclient5.getQuery(url, CustomProperties.getAll());
+ Httpclient5Response httpclient5Response = Httpclient5.getQuery(url, Httpclient5.gsonMerge(object));
```
* ❔ `member.name`만 빼고 넘기려면

## 예외 처리
* https://cheese10yun.github.io/spring-guide-exception
* https://supawer0728.github.io/2019/04/04/spring-error-handling
* https://www.baeldung.com/exception-handling-for-rest-with-spring

src/main/resources/application.properties
```properties
server.error.include-message=ALWAYS
server.error.include-exception=TRUE
#server.error.include-stacktrace=ALWAYS
```
