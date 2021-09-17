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

## httpclient5
### 설치
pom.xml
```xml
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
</dependency>
```

### 회원(Members) Read
src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```java
// @SuppressWarnings("unchecked")
public List<Member> read() throws Exception {
    HttpGet httpGet = new HttpGet("http://localhost:8080/api/v1/members");
    httpGet.addHeader("Content-Type", "application/json");
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpGet);
    System.out.println(httpResponse.getCode());
    String json = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(json);
    JSONParser jsonParser = new JSONParser(json);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("members"));
    httpClient.close();
    return (List<Member>)linkedHashMap.get("members");
}
```

### 회원(Members) Create
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
    String json = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(json);
    JSONParser jsonParser = new JSONParser(json);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

### 회원(Members) Delete
```java
public Integer delete(int index) throws Exception {
    HttpDelete httpDelete = new HttpDelete("http://localhost:8080/api/v1/members/" + index);
    httpDelete.addHeader("Content-Type", "application/json");
    CloseableHttpClient httpClient = HttpClients.createDefault();
    CloseableHttpResponse httpResponse = httpClient.execute(httpDelete);
    System.out.println(httpResponse.getCode());
    String json = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(json);
    JSONParser jsonParser = new JSONParser(json);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

### 회원(Members) Update
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
    String json = EntityUtils.toString(httpResponse.getEntity(), StandardCharsets.UTF_8);
    System.out.println(json);
    JSONParser jsonParser = new JSONParser(json);
    LinkedHashMap<String, Object> linkedHashMap = jsonParser.object();
    System.out.println(linkedHashMap.get("result"));
    httpClient.close();
    return null;
}
```

<!--
### Query string 받아서 넘기기
src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersController.java
```java
public MembersResponse membersRead(@ModelAttribute Member member) throws Exception {
    return new MembersResponse("read", membersService.read(member));
}
```

src/main/java/com/example/SpringBootHttpStudy/api/v1/MembersService.java
```diff
- public List<Member> read() throws Exception {
-   HttpGet httpGet = new HttpGet("http://localhost:8080/api/v1/members");
```
```java
public List<Member> read(Member member) throws Exception {
    URIBuilder uriBuilder = new URIBuilder("http://localhost:8080/api/v1/members");
    Gson gson = new Gson();
    JsonObject jsonObject = gson.toJsonTree(member).getAsJsonObject();
    for(Map.Entry<String, JsonElement> entry : jsonObject.entrySet()) {
        System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue() );
        uriBuilder.addParameter(entry.getKey(), entry.getValue().toString());
    }
    HttpGet httpGet = new HttpGet(uriBuilder.build());
```
-->
