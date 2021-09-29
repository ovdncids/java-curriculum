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

## 모든 서버 요청에 JwtRequestFilter 먼저 실행 시키기
* 필터(Filter), 인터셉터(Interceptor), 미들웨어(Middleware) 상황에 따라서 달리 불려진다.

src/main/java/패키지/common/JwtRequestFilter.java
```java
@Component
public class JwtRequestFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain
    ) throws IOException, ServletException {
        filterChain.doFilter(request,response);
    }
}
```

src/main/java/패키지/WebSecurityConfig
```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private JwtRequestFilter jwtRequestFilter;

    @Override
    protected void configure(HttpSecurity http) {
        http.addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
* `디버깅 모드`에서 어떻게 동작 하는지 확인
