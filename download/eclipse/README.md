# Eclipse for Java 8 (1.8)
* https://www.eclipse.org/downloads/packages/release/2020-06/r

#### Mac에서 Failed to create the Java Virtual 발생 할 경우
```sh
응용 프로그램 > Eclipse 아이콘 > 패키지 내용 보기 > Info.plist
```
```sh
# <string>-keyring</string> 밑에 추가
<string>-vm</string>
<string>/Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home/bin/java</string>
```
```sh
# Java 경로 찾기
/usr/libexec/java_home -V
```

## Spring 프로젝트 열기
```sh
File > Import... > Maven > Existing Maven Projects > Spring 프로젝트
```

## Spring Tools 3 설치
```sh
Help > Eclipse Marketplace > Search > spring > Spring Tools 3 (Standalone Edition) 3.9.14.RELEASE
```

## Spring 프로젝트 생성
* https://secure-key.tistory.com/47
```sh
File > New > Spring > Spring Legacy Project > Spring MVC Project > Project name: demo
  package: com.mycompany.myapp
```

### Tomcat 설치
* https://tomcat.apache.org/download-90.cgi
```sj
# Tomcat 9 버전 다운로드

Servers > create a new searver... > Apache > Tomcat v9.0 Server
  Tomcat installation directory: 다운 받은 경로 선택
  Next > demo 프로젝트 선택 > Add > Finish
```
* http://localhost:8080/myapp

# STS (Spring Tool Suite 4)
* https://spring.io/tools
```sh
다운로드 > spring-tool-suite-4-4.14.1.RELEASE-e4.23.0-win32.win32.x86_64.self-extracting.jar > 더블 클릭하면 압축 풀림
```
* ❕ `Spring Tool Suite 4` 부터는 일반 `Spring Legacy Project`를 생성 할 수 없고 `Spring Boot`만 생성 가능 하다.
