# Java - Class

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

#### 언제 원시자료형을 쓰고, 언제 래퍼클래스를 사용하는가?
* ❕ `null`을 사용해야 한다면 `래퍼클래스`, 필요 없다면 `원시자료형`을 사용한다.
```java
int int3 = null;
Integer int4 = null;
```

Main.java
```java
class PrimitiveTest {
    int a;
}

public class Main {
    public static void main(String[] args) {
        PrimitiveTest primitiveTest = new PrimitiveTest();
        System.out.println(primitiveTest.a);
    }
}
```
* ❔ `primitiveTest.a`값은?
* ❔ `int a;`를 `Integer a;`로 바꾼다면?

## Model형 Class
`배열`은 변수의 집합으로 `숫자(index)` 기준으로 변수에 접근 하지만, Model형 Class로 만든 `객체(Object)`는 `문자`를 기준으로 변수에 접근 한다.

src/com/company/models/Model1.java
```java
package com.company.models;

public class Model1 {
    public Boolean v1 = true;
    public Integer v2 = 100;
    public String v3 = "abc";
}
```

src/com/company/Class.java
```java
package com.company;
import com.company.models.Model1;

public class Class {
    public static void main(String[] args) {
        // 오브젝트 Create
        Model1 model1 = new Model1();
        System.out.println(model1);

        // 오브젝트 Read
        boolean v1 = model1.v1;
        int v2 = model1.v2;
        String v3 = model1.v3;

        // 오브젝트 Update
        model1.v1 = false;
        model1.v2 = -10;
        model1.v3 = "def";

        // 오브젝트 Delete
        model1.v1 = null;
        model1.v2 = null;
        model1.v3 = null;
    }
}
```
* ❕ boolean(기본 자료형)과 Boolean(참조형) 차이 설명
* `new` 키워드 설명
* ❔`new` 키워드를 뺀다면
* `System.out.println(Model1);` 실행해 보기
* `System.out.println(Model1.class);` 실행해 보기

## 캡슐화
```diff
- public Boolean v1 = true;
+ private Boolean v1 = true;
```
* ❕ 멤버변수의 접근권한을 `public`에서 `private`으로 바꾸면 `model1.v1` 접근이 막히게 된다. `get`, `set` 메서드를 만들어서 간접 접근 해야한다.

### get, set 메서드 만들기
* Model형 Class -> 멤버변수로 커서 이동 -> Generate... -> Getter and Setter -> 멤버변수 선택

src/com/company/models/Model1.java
```diff
- public Integer v2 = 100;
- public String v3 = "abc";
+ private Integer v2 = 100;
+ private String v3 = "abc";
```

src/com/company/Class.java
```diff
// 오브젝트 Read
boolean v1 = model1.getV1();
int v2 = model1.getV2();
String v3 = model1.getV3();

// 오브젝트 Update
model1.setV1(false);
model1.setV2(-10);
model1.setV3("def");

// 오브젝트 Delete
model1.setV1(null);
model1.setV2(null);
model1.setV3(null);
```

### set 메서드에서 Validation 적용
* 상황: `model1`객체의 멤버변수 `v2`는 양수만 받아야 하고, 음수를 받을 경우 0으로 대치 하려한다.
```java
public void setV2(Integer v2) {
    if (v2 < 0) v2 = 0;
    this.v2 = v2;
}
```

* ❔ `model1`객체의 멤버변수 `v2`는 음수만 받아야 하고, 양수를 받을 경우 0으로 대치 하려면

## static 멤버
* Class의 `static 멤버`는 `new 연산자`로 객체를 만들지 않고도, 바로 사용할 수 있는 변(상)수 또는 함수

src/com/company/models/Model1.java
```java
public static String s1 = "Static user";
public static void s2() {
    System.out.println(s1);
}
```

src/com/company/Class.java
```java
Model1.s2();
Model1 m1 = new Model1();
m1.s1 = "Static m1";
m1.s2();
Model1 m2 = new Model1();
m2.s1 = "Static m2";
m2.s2();
Model1.s2();
```

## 외부 라이브러리 불러오기
### Gson
* Object를 JSON 형식으로 쉽게 변경 가능
* https://search.maven.org/artifact/com.google.code.gson/gson/2.8.8/jar
* `lib/gson-2.8.8.jar` 이동하기
* File -> Project Structure -> Project Settings -> Libraies -> + -> `lib/gson-2.8.8.jar` 선택

```java
// Gson 객체 생성
Gson gson = new Gson();

// Object에서 JSON문자형으로 변경
String model1String = gson.toJson(model1);
System.out.println(model1String);

// JSON문자형에서 Object로 변경
Model1 model1New = gson.fromJson(model1String, Model1.class);
System.out.println(model1New);
```
* API에서 JSON 설명

#### pom.xml 설정
```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
</dependency>
```
