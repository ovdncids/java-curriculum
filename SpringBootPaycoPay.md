# PAYCO 결제
## 준비 사항
* ❕ `sellerKey`: 가맹점 코드, 파트너 센터에서 발급 받은 가맹점 코드
* ❕ `cpId`: 상점 ID, PAYCO에서 발급
* ❕ `productID`: 상품 ID, PAYCO에서 확인 (간편결제형인지[PROD_EASY], 바로구매형인지 구분[PROD_CHK, DELIVERY_PROD])

## Ready (결제 요청)
* ❕ `sellerOrderReferenceKey`: 주문번호 (우리쪽에서 만든 주문번호)
* `payMode`: "PAY1" (요청과 동시에 완료), "PAY2" (요청후 콜백 페이지에서 승인 요청까지 해야 완료)
* `orderMethod`: "EASYPAY_F": 회원결제, "EASYPAY": 비회원결제
* ❕ `sellerOrderProductReferenceKey`: 상품번호 (우리쪽에서 만든 상품번호)
```json
{
  "sellerKey": "{sellerKey}",
  "sellerOrderReferenceKey": "ORDER_00000001",
  "totalDeliveryFeeAmt": "0",
  "totalPaymentAmt": "1500",
  "payMode": "PAY2",
  "returnUrl": "{콜백 URL}/approval_url",
  "orderMethod": "EASYPAY",
  "orderProducts": [
    {
    "cpId": "{cpId}",
    "productId": "PROD_EASY",
    "productAmt": "1500",
    "productPaymentAmt": "1500",
    "sortOrdering": 1,
    "productName": "BAG",
    "orderQuantity": 1,
    "sellerOrderProductReferenceKey": "PRODUCT_00000001"
    }
  ]
}
```
* 결제 요청
```sh
curl -v -X POST "https://alpha-api-bill.payco.com/outseller/order/reserve" -H "Content-Type: application/json" -d "{\"sellerKey\": \"DEMO\",\"sellerOrderReferenceKey\": \"ORDER_00000001\",\"totalDeliveryFeeAmt\": \"0\",\"totalPaymentAmt\": \"1500\",\"payMode\": \"PAY2\",\"returnUrl\": \"http://localhost:8080/api/v1/paycopay/approval_url\",\"orderMethod\": \"EASYPAY\",\"orderProducts\": [{\"cpId\": \"DEMO\",\"productId\": \"PROD_EASY\",\"productAmt\": \"1500\",\"productPaymentAmt\": \"1500\",\"sortOrdering\": 1,\"productName\": \"BAG\",\"orderQuantity\": 1,\"sellerOrderProductReferenceKey\": \"PRODUCT_00000001\"}]}"
```
* `허용되지 않은 IP 입니다.`: PAYCO 쪽에 서버 주소를 등록 해야 함. PAYCO 쪽에 전화 하는게 빠를 수 있음.

### 결과:
```json
{
   "result": {
      "reserveOrderNo": "202201010000000000",
      "orderSheetUrl": "https://alpha-bill.payco.com/easyLogin/202201010000000000"
   },
   "message": "success",
   "code": 0
}
```
* ❕ `reserveOrderNo`: 주문예약번호
* ❕ `orderSheetUrl`: PAYCO 결제창 URL

## 결제 진행
* `PAYCO 결제창 URL`에서 PAYCO 결제 절차 진행

## Approval (결제 승인)
* 결제 절차 진행 후 우리쪽 콜백 페이지에서 다시 PAYCO쪽으로 결제 승인 요청을 해야, 정상적으로 결제가 완료 된다.
* {콜백 URL}/approval_url?code=0&reserveOrderNo=202201010000000000&sellerOrderReferenceKey=ORDER_00000001&mainPgCode=31&totalPaymentAmt=1500&totalRemoteAreaDeliveryFeeAmt=0&discountAmt=0&pointAmt=0&paymentCertifyToken=xxxx-fY75vDM3d_VOajRB8_e0g7TB7vDbSK3Bdi0x4N-N-YRrOPCTJ5PHoUk39D9&bid=xxxx33I3UINV22A7ED5Q4WW7Y
