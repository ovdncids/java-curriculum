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
