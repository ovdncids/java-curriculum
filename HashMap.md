# HashMap
## 키가 없을 경우 기본값 받기
```java
Map<String, Object> map = new HashMap<>();
// map.put("aB", null);
Integer aB = Integer.parseInt(map.getOrDefault("aB", "0").toString());
```
* ❕ `ab`키에 값`null`이 들어가 있으면 `getOrDefault`의 "0" 값이 적용 되지 않는다.
* ❕ `ab`키가 없을 경우만 적용
