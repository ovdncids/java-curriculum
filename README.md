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
```java
int a, b, c;
```
* ❔ 문제: System.out.println(a); 한다면
* ❔ 문제: 한줄로 변수 `a, b, c`에 각각 `1, 2, 3` 넣어 보기
* <details><summary>정답</summary>

  ```java
  int a = 1, b = 2, c = 3;
  ```
</details>

## 상수 (Constant)
Constant.java

### 상수를 사용하는 이유
1. 한번 선언된 값의 변경을 막기 위해 사용 한다.
```java
final boolean c1 = true;
final int c2 = 100;
final String c3 = "abc";
System.out.println(c1);
System.out.println(c2);
System.out.println(c3);
```
* ❔ c1 상수에 final을 2번 선언 한다면
* ❔ c1 상수에 값을 변경해 보기
* 상수에 대한 CRUD 설명

## 연산자 (Operator)
Operator.java
### 1. 4칙 연산자 (`+, -, *, /`)

#### 숫자형에 대한 4칙 연산자
```java
int int1 = 1;
int int2 = 2;
int result1 = int1 - int2;
int result2 = int1 * int2;
int result3 = int1 / int2;
int result4 = int1 + int2;
```
* ❔ `int1` 값을 문자 `"1"`로 바꾼다면
* ❔ `int1` 변수의 형식을 `String`으로 바꾼다면
* ❔ 문제: 프리랜서 개발자가 월 500만원을 받고 있다. 3.3% 원천징수를 때고 받는 실수령액과 세금을 계산하라.
  ```java
  int salary = 5000000;
  double rate = 3.3;
  double tax = ??;
  double realSalary = ??;

  힌트: 세금 계산식 = 급여 * 원천징수 / 100
  ```
* <details><summary>정답</summary>

  ```java
  int salary = 5000000;
  double rate = 3.3;
  double tax = salary * rate / 100;
  double realSalary = salary - tax;
  ```
</details>

#### 문자형에 대한 4칙 연산자 (`+, -, *, /`)
```java
String string1 = "a";
String string2 = "b";
String result5 = string1 + string2;
```
* ❔ `+` 연산자를 `-`, `*`, `/`로 바꾼다면
* ❔ 문제: 출력할 문구중에 일정 부분만 바꾸어서 출력하고 싶다. 다음을 완성 하시오.
  ```java
  String string3 = "올해 총 카드 사용 금액은 ";
  String string4 = " 입니다.";
  String string5 = ??;
  System.out.println(string3 + string4);

  출력: 올해 총 카드 사용 금액은 오백만원 입니다.
  ```
* <details><summary>정답</summary>

  ```java
  String string3 = "올해 총 카드 사용 금액은 ";
  String string4 = " 입니다.";
  String string5 = " 오백만원";
  System.out.println(string3 + string5 + string4);
  ```
</details>

#### boolean형에 대한 4칙 연산자 (`+, -, *, /`)
```java
boolean boolean1 = true;
boolean boolean2 = true;
boolean result6 = boolean1 + boolean2;
```
* ❔ `+` 연산자를 `-`, `*`, `/`로 바꾼다면

### 2. 동등 비교 연산자 (==)
```java
boolean equalBoolean1 = true == true;
boolean equalBoolean2 = true == false;
boolean equalInt1 = 1 == 1;
boolean equalInt2 = 1 == 2;
boolean equalString1 = "a" == "a";
boolean equalString2 = "a" == "b";
```
* ❕ `true == true`를 `true == 1`으로 바꾼다면
* ❕ `true == true`를 `true == "true"`으로 바꾼다면

### 3. 비교 연산자 (<, <=, >, >=)
```java
boolean compareInt1 = 1 <= 1;
boolean compareInt2 = 1 > 2;
```
* ❕ `1 <= 1`를 `"a" >= "a"`으로 바꾼다면
* ❕ `1 > 2`를 `true > true"`으로 바꾼다면

### 4. 논리 연산자 (&&, ||)
```java
boolean logical1 = true && true;
boolean logical2 = false || false;
```
* `&&`를 사용하는 상황: 로그인이 되어 있고, 글수정 권한이 있는 아이디인 경우, 글수정 버튼 활성화
* `||`를 사용하는 상황: 프리미엄 회원이거나 광고를 본 경우, 영상 시청 가능
* ❕ `true && true`를 `true && 1`으로 바꾼다면
* ❕ `false || false`를 `false || "a"`으로 바꾼다면

### 5. 소괄호() 연산자
```java
int roundBracket1 = 1 + 2 * 3;
int roundBracket2 = (1 + 2) * 3;
int roundBracket3 = ((1 + 2) * 3);
```
* ❕ `소괄호 연산자`는 `4칙 연산자`보다 우선 순위를 갖는다.
* ❔ 문제: `소괄호 연산자` 안에서 `true`와 `false`를 `동등 연산자`로 비교 후에 `boolean`형 변수 `y`에 넣고, `y`를 `System.out.println`로 찍어 보기
* <details><summary>정답</summary>

  ```java
  boolean y = (true == false);
  System.out.println(y);
  ```
</details>

## if문(제어문 > 조건문)
1. 기본 구조
```java
if (조건1) {
  // 조건1이 참인 경우 실행
} else if (조건2) {
  // 조건2가 참인 경우 실행
} else if (조건3) {
  // 조건3이 참인 경우 실행
  // else if는 여러게 사용 가능
} else {
  // 해당 되는 조건이 없을 경우 실행
}
```
* 예제
```java
int if1 = 1;
if (if1 == 1) {
    System.out.println("참1");
} else if (if1 == 2 || if1 == 3) {
    System.out.println("참2 또는 참3");
} else if (if1 == 4 && true) {
    System.out.println("참4");
} else {
    System.out.println("거짓");
}
```
* 조건은 주로 연산자에서 `boolean` 형식으로 받는다.
* `if1` 값을 수정하여 `참2 또는 참3`이 나오게 만들기
* `if1` 값을 수정하여 `참4`이 나오게 만들기
* `if1` 값을 수정하여 `거짓`이 나오게 만들기

* ❔ 문제: 조건이 `1 == 1`인 `if`문을 만들고, 참인 경우 `System.out.println("참");`을 찍어 보기
* <details><summary>정답</summary>

  ```java
  if (1 == 1) {
    System.out.println("참");
  }
  ```
</details>

### 3항 연산자
```java
String condition3 = 1 == 1 ? "a" : "b";
```
* ❔ 문제: 조건이 `2 == 3`인 `3항 연산자`문을 만들고, 참인 경우 `"c"` 거짓인 경우 `"d"`를 String `condition4` 변수에 넣어 보기
* <details><summary>정답</summary>

  ```java
  String condition4 = 2 == 3 ? "c" : "d";
  ```
</details>

## 배열
