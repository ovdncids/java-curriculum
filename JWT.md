# JSON Web Token(JWT)

## 설치
pom.xml
```xml
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt</artifactId>
  <version>0.9.1</version>
</dependency>
```

## JWT 테스트
src/main/java/패키지/common/JwtAuth.java
```java
public class JwtAuth {
    private static String privateKey = "privateKey";

    public static String tokenCreate(Map<String, Object> member) {
        Date now = new Date();
        return Jwts.builder()
            .setHeaderParam(Header.TYPE, Header.JWT_TYPE)
            .setExpiration(new Date(now.getTime() + Duration.ofMinutes(60 * 24).toMillis()))
            .setSubject("login")
            .setClaims(member)
            .signWith(SignatureAlgorithm.HS256, privateKey)
            .compact();
    }

    public static Map<String, Object> tokenCheck(String token) {
        return Jwts.parser().setSigningKey(privateKey).parseClaimsJws(token).getBody();
    }
}
```

src/test/java/패키지/{프로젝트명}Tests.java
```java
@Test
void tokenCreate() {
    Map<String, Object> member = new HashMap<>();
    member.put("name", "홍길동");
    member.put("age", 39);
    String token = JwtAuth.tokenCreate(member);
    System.out.println(token);
}

@Test
void tokenCheck() {
    String token = "tokenCreate 함수에서 생성한 token";
    Map<String, Object> member = JwtAuth.tokenCheck(token);
    System.out.println(member);
}
```
