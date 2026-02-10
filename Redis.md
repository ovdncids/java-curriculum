# Redis

## redis-cli 연결
```sh
redis-cli -h a.amazonaws.com -p 16579 --tls --cacert "C:\AmazonRootCA1.pem" --sni a.amazonaws.com
```

## 필요한 정보로 SQL문 만들기
```sh
EVAL "
local docinfoList = redis.call('HVALS','A:B')
local result = {}
for i = 1, #list do
    local ok, sheet = pcall(cjson.decode, list[i])
    if ok and sheet and sheet.DOC_ID then
      local DOC_NAME = string.gsub(sheet.DOC_NAME, \"'\", \"''\")
      result[i] = (i == 1) and 'SELECT ' or 'UNION ALL SELECT '
      result[i] = result[i] .. \"'\" .. sheet.DOC_ID .. \"' AS \\\"시트 아이디\\\", \"
      result[i] = result[i] .. \"'\" .. DOC_NAME .. \"' AS \\\"시트명\\\", \"
      result[i] = result[i] .. \"(SELECT NAME FROM ACCOUNT WHERE EMP_NO = '\" .. sheet.CREATE_USER .. \"') AS \\\"생성자\\\", \"
      result[i] = result[i] .. \"'\" .. sheet.CREATE_USER .. \"' AS \\\"사번\\\", \"
      result[i] = result[i] .. \"'\" .. sheet.CREATE_DATE .. \"' AS \\\"생성일\\\" FROM DUAL\"
    end
end
return result
" 0
```
* `EVAL문`을 `Redis Insight > CLI`에서 실행 시키면 한줄로 바꾸어준다.

```sh
redis-cli -h a.amazonaws.com -p 16579 --tls --cacert "C:\AmazonRootCA1.pem" --sni a.amazonaws.com --raw EVAL " local docinfoList = redis.call('HVALS','GOODOCS:DOCINFO') local result = {} for i = 1, #docinfoList do local ok, sheet = pcall(cjson.decode, docinfoList[i]) if ok and sheet and sheet.DOC_ID then local DOC_NAME = string.gsub(sheet.DOC_NAME, \"'\", \"''\") result[i] = (i == 1) and 'SELECT ' or 'UNION ALL SELECT ' result[i] = result[i] .. \"'\" .. sheet.DOC_ID .. \"' AS \\\"시트 아이디\\\", \" result[i] = result[i] .. \"'\" .. DOC_NAME .. \"' AS \\\"시트명\\\", \" result[i] = result[i] .. \"(SELECT NAME FROM ACCOUNT WHERE EMP_NO = '\" .. sheet.CREATE_USER .. \"') AS \\\"생성자\\\", \" result[i] = result[i] .. \"'\" .. sheet.CREATE_USER .. \"' AS \\\"사번\\\", \" result[i] = result[i] .. \"'\" .. sheet.CREATE_DATE .. \"' AS \\\"생성일\\\" FROM DUAL\" end end return result " 0
```
* `Redis Insight`는 한글이 깨지므로 `터미널`에서 `--raw` 옵션을 넣어서 실행 해야 한다.
