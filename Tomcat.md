# Tomcat 9 - Spring Boot 배포
## Spring boot war 파일 만들기
https://start.spring.io
```sh
Packaging > War
```

### Maven
pom.xml
```xml
<packaging>war</packaging>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<scope>provided</scope>
</dependency>
```
* ❕ 기존 프로젝트에서 `spring-boot-starter-tomcat provided` 없을 경우 Tomcat에서 Spring Boot가 실행 되지 않을 수 있다.
* ❕ 이 경우 프로젝트를 처음부터 만드는 방법이 빠를 수 있다.

#### .war 파일 생성
```sh
Maven 탭 > 프로젝트명 > Lifecycle > package > `target 디렉토리에 .war 파일 생성`
```

### Gradle
* https://hye0-log.tistory.com/29

build.gradle
```gradle
plugins {
	id 'war'
}

bootWar {
	archiveName("ROOT.war")
}
```

```sh
# war 파일 생성
./gradlew bootWar

# war 파일 실행
java -jar ./build/libs/ROOT.war

# ROOT 디렉토리명 변경
mv tomcat/webapps/ROOT tomcat/webapps/ROOT_ORI

# tomcat/webapps/ROOT.war 이동

# 현재 터미널에서 톰캣 실행 (로그를 바로 볼 수 있다. ctrl + c 바로 종료)
tomcat/bin/catalina.sh run

# tomcat 데몬으로 실행
tomcat/bin/startup.sh

# tomcat 데몬으로 종료
tomcat/bin/shutdown.sh

# tomcat 데몬 로그 보기
tail -f tomcat/logs/catalina.out
```

# Tomcat 10 - Spring Boot 배포
* https://adg0609.tistory.com/57
```sh
# ROOT 디렉토리명 변경
mv tomcat/webapps/ROOT tomcat/webapps/ROOT_ORI

# tomcat/webapps-javaee 폴더 생성
# tomcat/webapps-javaee/ROOT.war 이동
```

# CORS
```jsp
<%@ page contentType="application/json; charset=UTF-8" pageEncoding="UTF-8" trimDirectiveWhitespaces="true" %>
<%
 response.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
 response.setHeader("Access-Control-Allow-Headers", "Content-Type");

// Ajax 허용 도메인 목록
String[] allowList = {
    // 로컬 서버
    "https://localhost:3000",
    // 개발 서버
    "https://localhost:3001"
};
String origin = request.getHeader("Origin");
System.out.println("origin: " + origin);
for (String allow : allowList) {
    if (allow.equals(origin)) {
        response.setHeader("Access-Control-Allow-Origin", origin);
        response.setHeader("Access-Control-Allow-Credentials", "true");
    }
}
%>{
    "userId": "admin",
}
```

# HTTPS localhost 인증서
```cmd
keytool -genkeypair -alias tomcat -keyalg RSA -keysize 2048 -validity 3650 -storetype PKCS12 -keystore localhost.p12 -storepass changeit -dname "CN=localhost"
```
* 생성된 localhost.p12 파일을 conf/localhost.p12 이동

connf/server.xml
```xml
    <Connector port="8443"
               protocol="org.apache.coyote.http11.Http11NioProtocol"
               SSLEnabled="true"
               keystoreFile="conf/localhost.p12"
               keystorePass="changeit"
               keystoreType="PKCS12"
               sslProtocol="TLS"
               />
```
* https://localhost:8443

# Debugger
```cmd
set JPDA_ADDRESS=8000
set JPDA_TRANSPORT=dt_socket
bin/catalina.bat jpda start
```
* InteilliJ > 실행/디버그 구성 > 새 구성 추가 > 원격 JVM 디버그 > 포트: 8000

## InteilliJ Community 버전은 .jsp 파일로 Breakpoint를 찍을 수 없어, Servlet(.java)를 만들어서 Breakpoint를 사용한다.
```cmd
javac -cp C:\a.jar;C:\b.jar C:\a.java
```
