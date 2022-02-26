# NaverPay
## 준비 사항
* https://developer.pay.naver.com/docs/v2/api
* ❕ `clientId`: 클라이언트 아이디, NaverPay에서 발급

## Ready (결제 요청)
```html
<input type="button" id="naverPayBtn" value="네이버페이 결제 버튼">
<script src="https://nsp.pay.naver.com/sdk/js/naverpay.min.js"></script>
<script>
const oPay = Naver.Pay.create({
    "mode": "production", // development or production
    "clientId": "u86j4ripEt8LRfPGzQ8"
});
const elNaverPayBtn = document.getElementById("naverPayBtn");
elNaverPayBtn.addEventListener("click", function() {
    oPay.open({
        "merchantUserKey": "USER_00000001",
        "merchantPayKey": "ORDER_00000001",
        "productName": "상품1",
        "totalPayAmount": "100",
        "taxScopeAmount": "100",
        "taxExScopeAmount": "0",
        "returnUrl": "{콜백 URL}/approval_url"
    });
});
</script>
```

#### 결과:
```sh
resultCode: Success
paymentId: 20220226NP364178****
```
* ❕ `paymentId`: NaverPay 결제번호

## Approval (결제 승인)
* 결제 절차 진행 후 우리쪽 콜백 페이지에서 다시 NaverPay쪽으로 결제 승인 요청을 해야, 정상적으로 결제가 완료 된다.

#### 결제 승인
```sh
curl -X POST https://dev.apis.naver.com/naverpay-partner/naverpay/payments/v2/apply/payment -H X-Naver-Client-Id:{clientId} -H X-Naver-Client-Secret:{clientSecret} -d paymentId={paymentId}
```
