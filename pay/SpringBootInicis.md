# INCIS 결제
## 준비 사항
* https://manual.inicis.com/main
* [연동문서](./CA_INIpayMobile_WEB_manual_v.4.15.pdf)
* ❕ `P_MID`: 상점아이디, Inicis에서 발급
* ❕ `INIAPI key`: 취소할때 필요, Inicis에서 발급
* ❕ `P_RESERVED`: 결제옵션 복합필드
```
// 앱_스키마 (하이브리드 앱인 경우 앱에서 결제 완료 후 응답 받을 스키마값)
app_scheme=앱_스키마://
// 신용카드필수옵션
twotrs_isp=Y&block_isp=Y&twotrs_isp_noti=N
// 30만원 이상 결제
ismart_use_sign=Y
// 모바일버전, 백신앱 제외
apprun_check=Y
// 천원 이하 결제 허용
below1000=Y
```

## Ready (결제 요청)
```html
<form ref="formINIPay" name="ini" method="post" action="https://mobile.inicis.com/smart/wcard" accept-charset="euc-kr">
  <!-- 캐릭터셋 설정 -->
  <input type="hidden" name="P_CHARSET" value="utf8" />
  <!-- 이니시스에서 발급받은 mid 값 -->
  <input type="hidden" name="P_MID" value="INIpayTest" />
  <!-- 상점에서 생성한 주문번호 -->
  <input type="hidden" name="P_OID" value="ORDER_00000001" />
  <!-- 상품이름 -->
  <input type="hidden" name="P_GOODS" value="상품1" />
  <!-- 상품결제요청 가격 -->
  <input type="hidden" name="P_AMT" value="100" />
  <!-- 결제요청 회원 이름 -->
  <input type="hidden" name="P_UNAME" value="홍길동" />
  <!-- 결제옵션 복합필드 -->
  <input type="hidden" name="P_RESERVED" value="app_scheme=앱_스키마://&twotrs_isp=Y&block_isp=Y&twotrs_isp_noti=N&ismart_use_sign=Y&apprun_check=Y&disable_kpay=Y&below1000=Y" />
  <!-- 결제요청 처리 approval_url -->
  <!-- <input type="hidden" name="P_NEXT_URL" value="{콜백 URL}/approval_url" /> -->
  <input type="hidden" name="P_NEXT_URL" value="http://localhost:8080/approval_url/ORDER_00000001" />
  <!-- 결제버튼 -->
  <button type="button" onclick="inicisPay(this.form)">결제하기</button>
</form>

<script>
const inicisPay = function(form) {
  const popup = window.open('', 'popupInicisPay');
  form.target = 'popupInicisPay';
  form.submit();
  const popupCheck = window.setInterval(function() {
    if (!popup) {
      clearInterval(popupCheck);
      alert('팝업창을 허용 해주세요.');
    }
    console.log(popup);
    if (!popup.closed) return;
    clearInterval(popupCheck);
    console.log('TODO: 결제 결과 확인, 완료 페이지로 이동.');
  }, 1000);
};
</script>
```

#### 결과:
```sh
P_STATUS: 00
P_RMESG1: 정상 처리 되었습니다.
P_TID: INIMX_AUTHINIpayTest2022020914081539****
P_REQ_URL: https://fcmobile.inicis.com/smart/payReq.ini
P_NOTI: 
P_AMT: 100
```
* ❕ `P_TID`: INCIS 인증거래번호
* ❕ `P_REQ_URL`: INCIS 승인요청 URL

## Approval (결제 승인)
* 결제 절차 진행 후 우리쪽 콜백 페이지에서 다시 INCIS쪽으로 결제 승인 요청을 해야, 정상적으로 결제가 완료 된다.

#### 결제 승인
```sh
curl -v -X POST "https://fcmobile.inicis.com/smart/payReq.ini?P_MID=INIpayTest&P_TID={P_TID}"
```

#### 결과:
```sh
P_STATUS=00
P_AUTH_DT=2022020914****
P_AUTH_NO=3005****
P_RMESG1=성공적으로 처리 하였습니다.
P_RMESG2=00
P_TID=INIMX_AUTHINIpayTest2022020914081539****
P_FN_CD1=06
P_AMT=100
P_TYPE=CARD
P_UNAME=홍길동
P_MID=INIpayTest
P_OID=ORDER_00000001
P_NOTI=
P_NEXT_URL=http://localhost:8080/approval_url/ORDER_00000001
P_MNAME=
P_NOTEURL=
P_CARD_MEMBER_NUM=
P_CARD_NUM=527289**********
P_CARD_ISSUER_CODE=04
P_CARD_PURCHASE_CODE=06
P_CARD_PRTC_CODE=1
P_CARD_INTEREST=0
P_CARD_CHECKFLAG=1
P_CARD_ISSUER_NAME=국민카드
P_CARD_PURCHASE_NAME=국민계열
P_FN_NM=국민계열
CARD_CorpFlag=0
P_SRC_CODE=K
P_MERCHANT_RESERVED=dXNlcG9pbnQ****%3D
P_CARD_APPLPRICE=100
EventCode=
```

* ❕❕❕ `P_TID`: `INCIS 인증거래번호`에서 `INCIS 승인거래번호`로 변경 됨

## Cancel (결제 취소)
* https://manual.inicis.com/iniapi

#### INICIS에 넘길 정보 만들기
```java
public String cancel() {
    Date date_now = new Date(System.currentTimeMillis());
    SimpleDateFormat fourteen_format = new SimpleDateFormat("yyyyMMddHHmmss");
    String key = "ItEQKi3rY7uvDS8l"; // INIAPI key
    String type = "Refund";
    String paymethod = "Card";
    String timestamp = fourteen_format.format(date_now);
    String clientIp = "127.0.0.1";
    String mid = "INIpayTest";
    String tid = "{P_TID}";
    String msg = "취소사유";
    String hashData = key+type+paymethod+timestamp+clientIp+mid+tid;
    hashData = encodeSHA512(hashData);
    String apiURL = "https://iniapi.inicis.com/api/v1/refund";
    apiURL += "?type=" + type;
    apiURL += "&paymethod=" + paymethod;
    apiURL += "&timestamp=" + timestamp;
    apiURL += "&clientIp=" + clientIp;
    apiURL += "&mid=" + mid;
    apiURL += "&tid=" + tid;
    apiURL += "&msg=" + msg;
    apiURL += "&hashData=" + hashData;
    return apiURL;
}

public static String encodeSHA512(String str) {
    String hashData = "";
    try {
        MessageDigest digest = MessageDigest.getInstance("SHA-512");
        digest.reset();
        digest.update(str.getBytes());
        hashData = String.format("%0128x", new BigInteger(1, digest.digest()));
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
    }
    return hashData;
}
```
```sh
curl -v -X POST "https://iniapi.inicis.com/api/v1/refund?type=Refund&paymethod=Card&timestamp=20220210003552&clientIp=127.0.0.1&mid=INIpayTest&tid={P_TID}&msg=취소사유&hashData=56a199c0cea0bfaa631b310ff40b8af075b428351ce3a0a7a24baa961351c7cab474a36d07dd9d43885a0d0473138b1df84b6153ca9d7dfd2987c1411a8e****"
```

#### 결과
```json
{
   "resultCode":"00",
   "resultMsg":"정상처리되었습니다.",
   "cancelDate":"20220210",
   "cancelTime":"004139",
   "receiptInfo":"",
   "cshrCancelNum":"",
   "detailResultCode":""
}
```
