# JAVA
## 설치
* https://www.oracle.com/kr/java/technologies/javase-downloads.html
```sh
# Java SE 8
# JDK Download
java -version
```

* https://www.jetbrains.com/idea

## 프로젝트 생성
```
New Project > Java
  Create project from template(Command Line App 선택)
  프로젝트명, 프로젝트 경로, 패키지명
```

Main.java
```java
package com.company;

public class Main {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```
* ❕ `Project JDK is not defined` 이렇게 나오면 `Setup SDK` 설정 `Add JDK`
```
# Mac
/Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk

# Windows
C:\Program Files\Java\jdk1.8.0_291
```
