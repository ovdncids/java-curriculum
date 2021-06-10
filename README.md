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

## 변수 (Variable)
### 변수를 사용하는 이유
1. 자료형 데이터를 보관 할 수 있고, 자유롭게 값을 수정 할 수 있다.
Variable.java
```java
public class Variable {
    public static void main(String[] args) {
        boolean v1 = true;
        int v2 = 100;
        String v3 = "abc";
        System.out.println(v1);
        System.out.println(v2);
        System.out.println(v3);
        v1 = false;
        v2 = -10;
        v3 = "def";
        System.out.println(v1);
        System.out.println(v2);
        System.out.println(v3);
    }
}
```
* 자료형에는 `boolean`(true, false), `int`(숫자), `String`(문자) 3가지가 주로 쓰인다.
* `boolean` 선언문 설명
* `System.out.println();` 설명
* ❔ `v1` 변수에 boolean을 2번 선언 한다면
* ❔ 선언 하지 않은 `v4` 변수를 `System.out.println(v4);`로 찍는다면
* `변수`에 대한 `CRUD` 설명
* ❕ `변수명`에 대한 규칙
  ```
  제어문 이름으로 사용 불가 (if, for, switch, while, ...)
  연산자 이름으로 사용 불가 (+, -, *, /, ==, !, <, >, this, ...)
  자료형 또는 예약어 사용 불가 (true, false, null, NaN, delete, ...)
  숫자를 앞으로 사용 불가 (1a, 2b, ...)
  대소문자 구분 (lowUP, LowUp, LOWUP)
  주로 `Camel(낙타) 표기법`으로 사용 (carUse, busTake, ...)
  ```

2. `디버그 모드`에서 연산의 과정을 볼 수 있다.
```java
System.out.println(1 + 2 + 3);
int num1 = 1;
int num2 = 2;
int num3 = 3;
int sum1 = num1 + num2;
int sum2 = sum1 + num3;
System.out.println(sum2);
```
* breakpoint 설명

3. 변수 수정으로 프로그램 전체를 수정 가능하다.
```java
int calc = 100;
System.out.println(calc + 10);
System.out.println(calc - 10);
System.out.println(calc * 10);
System.out.println(calc / 10);
```
* ❔ 변수 `calc` 값 수정해 보기

### 한줄에 변수 여러개 선언하기 (선언문)
```js
let a, b, c;
```
* ❔ 문제: System.out.println(a); 한다면
* ❔ 문제: 한줄로 변수 `a, b, c`에 각각 `1, 2, 3` 넣어 보기
* <details><summary>정답</summary>

  ```js
  int a = 1, b = 2, c = 3;
  ```
</details>

## 상수 (Constant)
