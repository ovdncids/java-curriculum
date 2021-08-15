# Java - Class
## Model형 Class
배열은 변수의 집합으로 숫자(index) 기준으로 변수에 접근 하지만, Model형 Class로 만든 객체(Object)는 문자를 기준으로 변수에 접근 한다.

src/com/company/model/Model.java
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
