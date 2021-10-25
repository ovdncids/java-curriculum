# Rest Template

## Service
MembersService.java
```java
@Service
public class MembersService {
    static String URL = "http://localhost:8080/api/v1/members";

    public MembersResponse read() {
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(URL, HttpMethod.GET, null, MembersResponse.class);
        return responseEntity.getBody();
    }

    public MembersResponse create(Member member) {
        RestTemplate restTemplate = new RestTemplate();
        HttpEntity<Member> httpEntity = new HttpEntity<>(member, null);
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(URL, HttpMethod.POST, httpEntity, MembersResponse.class);
        return responseEntity.getBody();
    }

    public MembersResponse delete(int index) {
        RestTemplate restTemplate = new RestTemplate();
        String url = URL + "/" + index;
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.DELETE, null, MembersResponse.class);
        return responseEntity.getBody();
    }

    public MembersResponse update(int index, Member member) {
        RestTemplate restTemplate = new RestTemplate();
        String url = URL + "/" + index;
        HttpEntity<Member> httpEntity = new HttpEntity<>(member, null);
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, httpEntity, MembersResponse.class);
        return responseEntity.getBody();
    }
}
```
* ❕ `HttpMethod.PATCH` 통신은 기본으로 제공 하지 않는다.

<!--
### Warning: Dereference of 'membersResponse' may produce 'NullPointerException'
```diff
- return membersResponse.members;
+ return membersResponse != null ? membersResponse.members : null;
```
* 위와 같이 수정하면 경고가 나오지 않지만, 소스가 지저분 해지므로 경고를 무시한다.
-->

## UriComponentsBuilder
```java
public static String uriBuilder(String url, Object query) {
    UriComponentsBuilder uriComponentsBuilder = UriComponentsBuilder.fromHttpUrl(url);
    try {
        ObjectMapper objectMapper = new ObjectMapper();
        String jsonString = objectMapper.writeValueAsString(query);
        Map<String, Object> map = objectMapper.readValue(jsonString, new TypeReference<Map<String, Object>>() {
        });
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            System.out.println("Key = " + entry.getKey() + " Value = " + entry.getValue());
            uriComponentsBuilder.queryParam(entry.getKey(), entry.getValue().toString());
        }
    } catch (JsonProcessingException e) {
        e.printStackTrace();
    }
    return uriComponentsBuilder.build().toUriString();
}
```
```diff
- ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(URL, HttpMethod.GET, null, MembersResponse.class);
+ String url = uriBuilder(URL, new Member("홍길동", 39));
+ ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.GET, null, MembersResponse.class);
```
