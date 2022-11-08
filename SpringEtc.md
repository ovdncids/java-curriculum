# HashMap
## 키가 없을 경우 기본값 받기
```java
Map<String, Object> map = new HashMap<>();
// map.put("aB", null);
Integer aB = Integer.parseInt(map.getOrDefault("aB", "0").toString());
```
* ❕ `ab`키에 값`null`이 들어가 있으면 `getOrDefault`의 "0" 값이 적용 되지 않는다.
* ❕ `ab`키가 없을 경우만 적용

# LocalDateTime
## String 변환 LocalDateTime
```java
// LocalDateTime approvDatTime = LocalDateTime.parse("2022-01-01T00:00:01");
LocalDateTime localDateTime = LocalDateTime.parse("20220101000001", DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
String formatDateTime = LocalDateTime.from(localDateTime).format(DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
// String formatDateTimeNow = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
```

# Mybatis
## foreach
```java
@RequestParam String[] categories
```
```xml
<select id="read" resultType="Product">
    select * from products
    where 1 = 1
    <if test="categories != null and categories.length > 0">
        and category in
        <foreach collection="categories" item="category" index="index" separator=", " open="(" close=")">
            #{category}
        </foreach>
    </if>
</select>
```

# 오류들

## Http
### HttpMediaTypeNotSupportedException :: 415 status code
* https://blog.naver.com/writer0713/221853596497
```java
@RequestMapping(path = "", method = RequestMethod.POST)
public void membersCreate(@RequestBody MultiValueMap<String, Object> map) {
}
```
* `MultiValueMap`은 `Content type 'application/x-www-form-urlencoded;charset=UTF-8' not supported`에 유용 하다.

## java.lang.UnsupportedOperationException
* 배열, 인덱스에 관련된 오류

## Cannot construct instance of `java.time.LocalDateTime`
```java
@JsonDeserialize(using = LocalDateTimeDeserializer.class)
```

## SQL
### com.microsoft.sqlserver.jdbc.SQLServerException: 데이터 형식 varbinary을(를) date(으)로 암시적으로 변환할 수 없습니다.
```diff
- #{date}
+ #{date, mode=IN, jdbcType=DATE}
```

### Cannot determine value type from string ''
* 숫자형에 문자가 들어 올때 발생
* `select문`의 순서와 `Model 생성자`의 순서가 같은지 확인
