# Rest Template


MembersService.java
```java
@Service
public class MembersService {
    public MembersResponse read() {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://localhost:8080/api/v1/members";
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.GET, null, MembersResponse.class);
        return responseEntity.getBody();
    }

    public MembersResponse create(Member member) {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://localhost:8080/api/v1/members";
        HttpEntity<Member> httpEntity = new HttpEntity<>(member, null);
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.POST, httpEntity, MembersResponse.class);
        return responseEntity.getBody();
    }

    public MembersResponse delete(int index) {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://localhost:8080/api/v1/members/" + index;
        ResponseEntity<MembersResponse> responseEntity = restTemplate.exchange(url, HttpMethod.DELETE, null, MembersResponse.class);
        return responseEntity.getBody();
    }

    public MembersResponse update(int index, Member member) {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://localhost:8080/api/v1/members/" + index;
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
