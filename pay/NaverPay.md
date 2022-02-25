# NaverPay

## 준비 사항
* https://developer.pay.naver.com/docs/v2/api
* ❕ `merchantUserKey`: 가맹점 사용자 식별키, NaverPay에서 발급
* ❕ `clientId`: 클라이언트 아이디, NaverPay에서 발급

## Ready (결제 요청)
```html
<input type="button" id="naverPayBtn" value="네이버페이 결제 버튼">
<script src="https://nsp.pay.naver.com/sdk/js/naverpay.min.js"></script>
<script>
const oPay = Naver.Pay.create({
    "mode": "development", // development or production
    "clientId": "u86j4ripEt8LRfPGzQ8"
});
const elNaverPayBtn = document.getElementById("naverPayBtn");
elNaverPayBtn.addEventListener("click", function() {
    oPay.open({
        "merchantUserKey": "가맹점 사용자 식별키",
        "merchantPayKey": "ORDER_00000001",
        "productName": "상품1",
        "totalPayAmount": "1000",
        "taxScopeAmount": "1000",
        "taxExScopeAmount": "0",
        "returnUrl": "{콜백 URL}/approval_url"
    });
});
</script>
```
