# Spring Tool Suite 4 (4.31.0)

## Maven 설정
```sh
# JRE 설정
Window > Java > Installed JREs > jre 폴더 선택

# Maven 설정
프로젝트 > Run As
  > Maven clean
  > Maven test > Edit configuration and launch
    > Main > User settings: ~/.m2/settings.xml (Nexus 설정 파일)
    > JRE > Execution environment: JRE 설정에서 등록한 JRE 선택
프로젝트 > Maven > Update Project...
프로젝트 > Run As > Maven install
main() 함수가 있는 Java 파일 > Run As Java Application
```

### Failed to connect to MBean server at port 9001: Could not invoke shutdown operation: Spring application did not start before the configured timeout (30000ms
```powershell
./mvnw.cmd spring-boot:start -X `
"-s" "C:\maven\settings.xml" `
"-Dspring-boot.run.profiles=windows" `
-f pom.xml
```
```log
[DEBUG] Connected to local MBeanServer at port 9001
[DEBUG] Waiting for spring application to start...
[DEBUG] Spring application is not ready yet, waiting 500ms (attempt 1)
...
[DEBUG] Spring application is not ready yet, waiting 500ms (attempt 59)
[DEBUG] Spring application is not ready yet, waiting 500ms (attempt 60)
# spring-boot:start가 0.5초씩 attempt 60안에 완료 되지 않으면 MBeanServer가 강제 종료 시킴
```
pom.xml
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <wait>1000</wait>
    </configuration>
</plugin>
```
* 기본 0.5초에서 1초로 변경 시킴

```sh
InteilliJ > Maven > 프로젝트 > 플러그인 > spring-boot > spring-boot:start > 실행 구성 수정 > 실행
실행: spring-boot:start -X -s C:\maven\settings.xml -Dspring-boot.run.profiles=windows -f pom.xml
# -X: DEBUG값 출력
# pom.xml에 wait값인 1초가 적용 된다.
```

## Gradle 설정
```sh
Import projects... > Gradle > Existing Gradle Project >
  Project root directory: 선택
  Override workspace settings: 체크
    Gradle wrapper
    Java home: 선택
    Show Console View: 체크
    Show Executions View: 체크
Gradle Tasks 탭 > build
```
* ❕ STS4 재실행 후에 `Gradle Tasks 탭`에서 에러 표시가 나온다면 `새로 고침` 버튼을 누른다.
* ❕[gradle build 중 gradle-8.14.3-bin.zip 파일을 Nexus에서 못 받을때](https://github.com/ovdncids/tools/blob/master/NexusYarn.md#gradle-build-%EC%A4%91-gradle-8143-binzip-%ED%8C%8C%EC%9D%BC%EC%9D%84-nexus%EC%97%90%EC%84%9C-%EB%AA%BB-%EB%B0%9B%EC%9D%84%EB%95%8C)

### 로컬과 Docker에서 사용가능한 설정파일 위치
```java
public File getTempFile(String resourcesPath, String tempName, String tempExtension) {
    File file = null;
    try {
        InputStream ssoAgentconfigInputStream = PlatformUtil.class.getResourceAsStream(resourcesPath);
        Path tempFile = Files.createTempFile(tempName, tempExtension);
        Files.copy(ssoAgentconfigInputStream, tempFile, StandardCopyOption.REPLACE_EXISTING);
        file = tempFile.toFile();
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
    return file;
}

// src/main/resources/sso/agentconfig-LOCAL.xml
File file = PlatformUtil.get().getTempFile(
        "/sso/agentconfig-LOCAL.xml",
        "agentconfig-LOCAL",
        ".xml"
);
```
* `System.getProperty("catalina.base")` 경로는 jsp와 Spring에서 Tomcat이 실행된 이후에 접근 가능하다.
