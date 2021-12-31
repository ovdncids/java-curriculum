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

# LocalDateTime
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

## String 변환 LocalDateTime
```java
LocalDateTime localDateTime = LocalDateTime.parse("20220101000001", DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
String formatDateTime = LocalDateTime.from(localDateTime).format(DateTimeFormatter.ofPattern("yyyyMMddHHmmss"));
```
