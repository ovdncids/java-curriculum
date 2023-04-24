# Tomcat 9
## Spring boot war 파일 만들기
* https://hye0-log.tistory.com/29
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

# Tomcat 10
* https://adg0609.tistory.com/57
```sh
# tomcat/webapps-javaee 폴더 생성
# tomcat/webapps-javaee/ROOT.war 이동
```
