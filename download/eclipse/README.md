# Eclipse for Java 8 (1.8)
* https://www.eclipse.org/downloads/packages/release/2020-06/r
```sh
# 설치
eclipseinstaller > Eclipse IDE for Enterprise Java and Web Developers
```

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

# Spring Tools 3 Add-On For Spring Tools 4 3.9.22.RELEASE < Spring Legacy Project 생성 가능
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
Tomcat 시작
```
* http://localhost:8080/myapp
* `pom.xml`의 `<artifactId>myapp</artifactId>` 경로를 사용한다.

### 한글 깨짐
```sh
# 프로젝트 Text file encoding
프로젝트 > Properties > Resource > Text file encoding > Other > UTF-8
```

Tomcat/server.xml
```diff
- <Connector connectionTimeout="20000" port="8081" protocol="HTTP/1.1" redirectPort="8443"/>
+ <Connector uriencoding="utf-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
```

Tomcat/web.xml
```xml
<filter>
    <filter-name>setCharacterEncodingFilter</filter-name>
    <filter-class>org.apache.catalina.filters.SetCharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <async-supported>true</async-supported>
</filter>
<!-- 주석 해제 -->
```

모든 .jsp 파일
```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8" %>
<!-- 최상단에 추가 -->
```

## MyBatis
src/webapp/WEB-INF/spring/root-context.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://127.0.0.1:3306/DB명" />
        <property name="username" value="계정" />
        <property name="password" value="비밀번호" />
    </bean>
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="mapperLocations">
            <list>
                <value>classpath:mappers/*.xml</value>
            </list>
        </property>
        <property name="typeAliasesPackage" value="com.example.SpringBootRestApiStudy.models" />
    </bean>
</beans>
```

# STS (Spring Tool Suite 4)
* https://spring.io/tools
```sh
다운로드 > spring-tool-suite-4-4.14.1.RELEASE-e4.23.0-win32.win32.x86_64.self-extracting.jar > 더블 클릭하면 압축 풀림
```
* ❕ `Spring Tool Suite 4` 부터는 일반 `Spring Legacy Project`를 생성 할 수 없고 `Spring Boot`만 생성 가능 하다.
