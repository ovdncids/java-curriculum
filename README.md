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
### 1. 사칙 연산자 (`+, -, *, /`)

#### 숫자형에 대한 사칙 연산자
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

#### 문자형에 대한 사칙 연산자 (`+, -, *, /`)
```java
String string1 = "a";
String string2 = "b";
String result5 = string1 + string2;
```
* ❔ `+` 연산자를 `-`, `*`, `/`로 바꾼다면
* ❔ 문제: 출력할 문구중에 일정 부분만 바꾸어서 출력하고 싶다. 다음을 완성 하시오.
  ```java
  String stringFirst = "올해 총 카드 사용 금액은 ";
  String stringLast = " 입니다.";
  String stringChange = ??;
  System.out.println(stringFirst + stringLast);

  출력: 올해 총 카드 사용 금액은 오백만원 입니다.
  출력: 올해 총 카드 사용 금액은 천만원 입니다.
  출력: 올해 총 카드 사용 금액은 이천만원 입니다.
  ```
* <details><summary>정답</summary>

  ```java
  String string3 = "올해 총 카드 사용 금액은 ";
  String string4 = " 입니다.";
  String string5 = " 오백만원";
  System.out.println(string3 + string5 + string4);
  ```
</details>

#### boolean형에 대한 사칙 연산자 (`+, -, *, /`)
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
* ❕ `소괄호 연산자`는 `사칙 연산자`보다 우선 순위를 갖는다.
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
* 조건은 주로 연산자를 사용해서 `boolean` 형식으로 받는다.
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

<!--
## 원시(Primitive)자료형과 래퍼(Wrapper)클래스
### int와 Integer형 비교
```java
int int1 = 1;
Integer int2 = new Integer(1);
System.out.println(int1 == int2);
System.out.println(int2.toString() + 1);
System.out.println(int2.equals(int1));
// System.out.println(int1.toString());
// System.out.println(int1.equals(int1));
```

### boolean과 Boolean형 비교
```java
boolean bool1 = true;
Boolean bool2 = new Boolean(true);
System.out.println(bool1 == bool2);
System.out.println(bool2.toString() + 1);
System.out.println(bool2.equals(bool1));
// System.out.println(bool1.toString());
// System.out.println(bool1.equals(int1));
```
-->

## 기본형(Primitive type) 배열
Array.java

### 배열을 사용하는 이유
1. 순차적인 반복 작업에 사용한다. (주로 동일한 데이터 타입으로 묶인 경우가 많다.)
2. 숫자(index)를 바탕으로 해당 데이터에 접근 한다.

* https://t1.daumcdn.net/blogfile/fs8/27_25_21_25_0O7Ul_IMAGE_0_42.jpg?original&filename=42.jpg

### 배열의 CRUD
```java
// 배열 Create
int[] array1 = {};
int[] array2 = {0, 1, 2};
int[] array3 = new int[10];

// 배열 Read
int a1 = array2[0];
int a2 = array2[1];
System.out.println(array2[2]);

// 배열 Update
array3[0] = 3;
array3[1] = array2[1];
array3[2] = array3[1];

// 배열 Delete
```

### 배열의 크기
```java
int length1 = array1.length;
int length2 = array2.length;
int lastIndex = array2.length - 1;
int lastValue = array2[lastIndex];
```
* ❕ `lastValue`는 `array2` 배열의 마지막 요소의 값을 받는다.

### 배열의 성격
```java
int[] arr1 = {};
int[] arr2 = {};
// quiz1
if (arr1 == arr2) {
    String result = "참";
    System.out.println(result);
} else {
    String result = "거짓";
    System.out.println(result);
}
int quiz2 = arr1[5];
// quiz3
arr1[5] = 10;
```
* ❔ 문제: `arr1`와 `arr2`는 같을까?
* <details><summary>정답</summary>

  https://ovdncids.github.io/javascript-curriculum/images/memory.png
  ```
  배열은 선언과 동시에 별도의 `메모리 공간`에 존재하고, 변수는 단지 해당 배열이 있는 `메모리 주소`를 가지고 있다.
  따라서 `arr1`과 `arr2`는 서로 다른 배열의 주소를 가지므로 같지 않다.
  실제 값을 가지고 있지 않고, 메모리 주소를 가지고 있는 변수를 참조형 변수(Reference variable)라고 한다.
  ```
</details>

* ❔ 해당 배열이 가진 `length`보다 큰 `index`를 `Read` 한다면?
* ❔ 해당 배열이 가진 `length`보다 큰 `index`를 `Update` 한다면?
* ❕ 런타임 에러(RunTime Error), 컴파일 에러(Compile Error) 차이점 설명

### 배열 실습
* 1 부터 5까지 더하기(total 변수를 만들어서 한번씩 더해서 만듬)
```java
int[] testArray1 = {1, 2, 3, 4, 5};
int total1 = testArray1[0];
total1 = total1 + testArray1[1];
total1 = total1 + testArray1[2];
total1 += testArray1[3];
total1 += testArray1[4];
```

* `testArray1` 평균 구하기
```java
int avg = total1 / testArray1.length;
```

* `testArray1` 홀수만 더하기
```java
int odd1 = testArray1[0] + testArray1[2] + testArray1[4];
```

* `testArray1` 짝수만 더하기
```java
int even1 = testArray1[1] + testArray1[3];
```

* ❔ 문제: `testArray1`의 `index` 번호 `0, 1, 4`번을 모두 곱해서 int 변수 `multiply1`에 넣기
* <details><summary>정답</summary>

  ```java
  int multiply1 = testArray1[0] * testArray1[1] * testArray1[4];
  ```
</details>

## for문(제어문 > 반복문)
For.java

### for문을 사용하는 이유
1. 반복 작업을 한곳으로 묶기 위해 사용함 (주로 배열이 사용 된다.)
2. 주로 게시판에 목록을 보여줄때 주로 사용함

### for문 문법
1. 기본 구조
```java
for (초기문; 조건문; 증감문) {
  실행문;
  ...
}
```
* 예제
```java
for (int index1 = 0; index1 < 3; index1++) {
  System.out.println(index1);
}
```

2. break
```java
for (int index2 = 1; index2 <= 3; index2++) {
  System.out.println(index2);
  break;
}
```

3. continue
```java
for (int index3 = 1; index3 <= 3; index3 += 1) {
  if (index3 == 2) {
    continue;
  }
  System.out.println(index3);
}
```

* ❔ 문제: `초기문`, `조건문`, `증감문`을 이용하여 `1`부터 `10` 사이에 `홀수`만 `실행문`에서 `System.out.println`로 찍어 보기
* <details><summary>정답</summary>

  ```java
  for (int index4 = 1; index4 <= 10; index4 += 2) {
    System.out.println(index4);
  }
   ```
</details>

* ❔ 문제: `초기문`, `조건문`, `증감문`을 이용하여 `2`부터 `10` 사이에 `짝수`만 `실행문`에서 `System.out.println`로 찍어 보기
* <details><summary>정답</summary>

  ```java
  for (int index5 = 2; index5 <= 10; index5 += 2) {
    System.out.println(index5);
  }
  ```
</details>

### for문의 범위(Scope), 로컬(Local) 변수와 블록(Block) 변수의 차이
1. 초기문 사용하지 않기
```java
int index6 = 0;
for (; index6 < 3; index6++) {
  final int blockInt = index6;
  System.out.println(blockInt);
}
System.out.println(index6);
```
* ❕ 결과적으로 `로컬 변수 index6`은 for문이 반복된 횟수가 된다.
* ❔ `int index6 = 0;`을 잘라서 `초기문`에 붙여넣어 보기 (에러가 발생할지 생각해 보기)
* Ctrl(또는 command) 키를 눌러서 해당 변수 이동
* 블록 변수 설명
* ❔ 문제: `로컬 변수 total1`에 `0`을 넣고, `for문`을 이용해 `total1`에 1부터 5까지 더하고, `total1`을 `for문` 밖에서 `console.log`로 찍어 보기
* <details><summary>정답</summary>

  ```java
  int total1 = 0;
  for (int index7 = 1; index7 <= 5; index7++) {
    total1 += index7;
  }
  System.out.println(total1);
  ```
</details>

* ❔ 문제: `total1`의 `평균` 값을 구해 `avg1` 상수에 넣고, `avg1`을 `console.log`로 찍어 보기
* ❕ 힌트: 평균으로 나눌 `5`값을 얻는 과정이 중요 (변수 또는 상수를 여러개 사용해도 무관, 사직연산 가능)
* <details><summary>정답</summary>

  ```java
  int total1 = 0;
  int index7 = 1;
  for (; index7 <= 5; index7++) {
    total1 += index7;
  }
  int count = index7 - 1;
  int avg1 = total1 / count;
  System.out.println(avg1);
  ```
  `total1 / 5 ` 이렇게 바로 나누었다면, 나중에 프로그램이 1에서 10까지로 변한다면, `5`값을 `2군데`에서 수정 해야 한다.
</details>

### for문에서 배열 사용하기
```java
int[] array1 = {1, 2, 3};
for (int index8 = 0; index8 < array1.length; index8++) {
  System.out.println(array1[index8]);
}
```
* ❔ 문제: `array2` 배열 변수에 `new int[3]`을 이용해 3개짜리 배열을 만들고, 위에 for문을 이용해 `array2` 배열을 `[1, 2, 3]`으로 만들고, `array2`를 for문 밖에서 `System.out.println`로 찍어 보기
* <details><summary>정답</summary>

  ```java
  int[] array1 = {1, 2, 3};
  int[] array2 = new int[3];
  for (int index8 = 0; index8 < array1.length; index8++) {
    array2[index8] = array1[index8];
  }
  System.out.println(array2);
  ```
</details>

* ❕ 결과적으로 `array2`는 `array1`을 복사하였다.
* ❔ `array1 === array2` 참일까요?
* ❕ 메모리 설명
```java
int[] array3 = {1, 2, 3};
int[] array4 = array3;
```
* ❔ `array3 === array4` 참일까?
```java
array3 = new int[3];
array4 = new int[3];
```
* ❔ 문제: `array3`에서 사용하던 배열에 다시 접근할 수 있을까?
* <details><summary>정답</summary>

  없다. (따라서 배열은 `상수(final)`로 사용 해야한다.)
</details>

### index++와 ++index의 차이
```java
int index = 0;
int diff1 = index++;
System.out.println(index);
int diff2 = ++index;
System.out.println(index);
```
