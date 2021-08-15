# Java - Class
## Model형 Class
배열은 변수의 집합으로 숫자(index) 기준으로 변수에 접근 하지만, Model형 Class로 만든 객체(Object)는 문자를 기준으로 변수에 접근 한다.

src/com/company/model/Model1.java
```java
package com.company.model;

public class Model1 {
    public boolean v1 = true;
    public int v2 = 100;
    public String v3 = "abc";
}
```

src/com/company/Class.java
```java
package com.company;
import com.company.model.Model1;

public class Class {
    public static void main(String[] args) {
        Model1 model1 = new Model1();
        System.out.println(model1);
        System.out.println(model1.v1);
        System.out.println(model1.v2);
        System.out.println(model1.v3);
        model1.v1 = false;
        System.out.println(model1.v1);
    }
}
```
* ❕ `new` 키워드 설명
* ❔`new` 키워드를 뺀다면
* `System.out.println(Model1);` 실행해 보기

## 캡슐화
```diff
- public boolean v1 = true;
+ private boolean v1 = true;
```
* ❕ 멤버변수의 접근권한을 `public`에서 `private`으로 바꾸면 `model1.v1` 접근이 막히게 된다. `get`, `set` 메서드를 만들어서 간접 접근 해야한다.

### get, set 메서드 만들기
* Model형 Class -> 멤버변수로 커서 이동 -> Generate... -> Getter and Setter -> 멤버변수 선택

src/com/company/model/Model1.java
```diff
- public int v2 = 100;
- public String v3 = "abc";
+ private int v2 = 100;
+ private String v3 = "abc";
```

src/com/company/Class.java
```diff
- model1.v1
+ model1.isV1()
```
```diff
- model1.v2
+ model1.getV2()
```
```diff
- model1.v3
+ model1.getV3()
```
```diff
- model1.v1 = false;
+ model1.setV1(false);
```

### set 메서드에서 Validation 적용
* 상황: `model1`객체의 멤버변수 `v2`는 양수만 받아야 하고, 음수를 받을 경우 0값을 넣으려 한다.
