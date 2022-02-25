# PAYCO 결제
## 준비 사항
* [연동문서](./CA_PAYCO_연동_가이드_v1.1.9.36.pdf)
* ❕ `sellerKey`: 가맹점 코드, 파트너 센터에서 발급 받은 가맹점 코드
* ❕ `cpId`: 상점 ID, PAYCO에서 발급
* ❕ `productID`: 상품 ID, PAYCO에서 확인 (간편결제형인지[PROD_EASY], 바로구매형인지 구분[PROD_CHK, DELIVERY_PROD])

## Ready (결제 요청)
* ❕ `sellerOrderReferenceKey`: 주문번호 (우리쪽에서 만든 주문번호)
* `payMode`: "PAY1" (요청과 동시에 완료), "PAY2" (요청후 콜백 페이지에서 승인 요청까지 해야 완료)
* `orderMethod`: "EASYPAY_F": 회원결제, "EASYPAY": 비회원결제
* ❕ `sellerOrderProductReferenceKey`: 상품번호 (우리쪽에서 만든 상품번호)
#### PAYCO에 넘길 정보
```json
{
  "sellerKey": "{sellerKey}",
  "sellerOrderReferenceKey": "ORDER_00000001",
  "totalDeliveryFeeAmt": "0",
  "totalPaymentAmt": 1500,
  "payMode": "PAY2",
  "returnUrl": "{콜백 URL}/approval_url",
  "orderMethod": "EASYPAY",
  "orderProducts": [
    {
    "cpId": "{cpId}",
    "productId": "PROD_EASY",
    "productAmt": 1500,
    "productPaymentAmt": 1500,
    "productName": "BAG",
    "orderQuantity": 1,
    "sellerOrderProductReferenceKey": "PRODUCT_00000001"
    }
  ]
}
```
#### 결제 요청
```sh
curl -v -X POST "https://alpha-api-bill.payco.com/outseller/order/reserve" -H "Content-Type: application/json" -d "{\"sellerKey\": \"DEMO\", \"sellerOrderReferenceKey\": \"ORDER_00000001\", \"totalPaymentAmt\": 1500, \"payMode\": \"PAY2\", \"returnUrl\": \"http://localhost:8080/api/v1/paycopay/approval_url\", \"orderMethod\": \"EASYPAY\", \"orderProducts\": [{\"cpId\": \"DEMO\", \"productId\": \"PROD_EASY\", \"productAmt\": 1500, \"productPaymentAmt\": 1500, \"productName\": \"BAG\", \"orderQuantity\": 1, \"sellerOrderProductReferenceKey\": \"PRODUCT_00000001\"}]}"
```
* `허용되지 않은 IP 입니다.`: PAYCO 쪽에 서버 주소를 등록 해야 함. PAYCO 쪽에 전화 하는게 빠를 수 있음.

#### 결과:
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
* ❕ `reserveOrderNo`: PAYCO 주문예약번호
* ❕ `orderSheetUrl`: PAYCO 결제창 URL

## 결제 진행
* `PAYCO 결제창 URL`에서 PAYCO 결제 절차 진행

## Approval (결제 승인)
* 결제 절차 진행 후 우리쪽 콜백 페이지에서 다시 PAYCO쪽으로 결제 승인 요청을 해야, 정상적으로 결제가 완료 된다.
* {콜백 URL}/approval_url?code=0&reserveOrderNo=202201010000000000&sellerOrderReferenceKey=ORDER_00000001&mainPgCode=31&totalPaymentAmt=1500&totalRemoteAreaDeliveryFeeAmt=0&discountAmt=0&pointAmt=0&paymentCertifyToken=xxxx-fY75vDM3d_VOajRB8_e0g7TB7vDbSK3Bdi0x4N-N-YRrOPCTJ5PHoUk39D9&bid=xxxx33I3UINV22A7ED5Q4WW7Y
* ❕ `paymentCertifyToken`: 결제 인증 토큰

#### PAYCO에 넘길 정보
```json
{
  "sellerKey": "{sellerKey}",
  "reserveOrderNo": "202201010000000000",
  "sellerOrderReferenceKey": "ORDER_00000001",
  "paymentCertifyToken": "xxxx-fY75vDM3d_VOajRB8_e0g7TB7vDbSK3Bdi0x4N-N-YRrOPCTJ5PHoUk39D9",
  "totalPaymentAmt": 1500
}
```

#### 결제 승인
```sh
curl -v -X POST "https://alpha-api-bill.payco.com/outseller/payment/approval" -H "Content-Type: application/json" -d "{\"sellerKey\": \"DEMO\", \"reserveOrderNo\": \"202201010000000000\", \"sellerOrderReferenceKey\": \"ORDER_00000001\", \"paymentCertifyToken\": \"xxxx-fY75vDM3d_VOajRB8_e0g7TB7vDbSK3Bdi0x4N-N-YRrOPCTJ5PHoUk39D9\", \"totalPaymentAmt\": 1500}"
```

#### 결과:
```json
{
   "result":{
      "sellerOrderReferenceKey":"ORDER_00000001",
      "reserveOrderNo":"202201010000000000",
      "orderNo":"202201010000000001",
      "memberName":"최**",
      "memberEmail":"ov****",
      "orderChannel":"PC",
      "totalOrderAmt":1500,
      "totalDeliveryFeeAmt":0,
      "totalRemoteAreaDeliveryFeeAmt":0,
      "totalPaymentAmt":1500,
      "receiptPaycoPointAmt":0,
      "receiptPaycoPointTaxfreeAmt":0,
      "receiptPaycoPointTaxableAmt":0,
      "receiptPaycoPointVatAmt":0,
      "receiptPaycoPointServiceAmt":0,
      "orderProducts":[
         {
            "orderProductNo":"202201010000000002",
            "sellerOrderProductReferenceKey":"PRODUCT_00000001",
            "orderProductStatusCode":"OPSPAED",
            "orderProductStatusName":"결제완료",
            "cpId":"DEMO",
            "productId":"PROD_EASY",
            "productKindCode":"GENERAL_PRODUCT",
            "productPaymentAmt":1500,
            "originalProductPaymentAmt":1500
         }
      ],
      "paymentDetails":[
         {
            "cardSettleInfo":{
               "cardCompanyName":"씨티카드",
               "cardCompanyCode":"CCCT",
               "cardNo":"000000******0000",
               "cardInstallmentMonthNumber":"00",
               "cardAdmissionNo":"25604815",
               "cardInterestFreeYn":"N",
               "corporateCardYn":"N",
               "partCancelPossibleYn":"Y",
               "acquirersCorporationCode":"CCBC"
            },
            "paymentTradeNo":"202201010000000003",
            "paymentMethodCode":"31",
            "paymentMethodName":"신용카드",
            "paymentAmt":1500,
            "taxfreeAmt":0,
            "taxableAmt":1363,
            "vatAmt":137,
            "serviceAmt":0,
            "tradeYmdt":"20220128131737",
            "pgAdmissionNo":"22658971042446",
            "pgAdmissionYmdt":"20220128131737",
            "easyPaymentYn":"Y"
         }
      ],
      "orderCertifyKey":"xxxxtSAAs7xPSaBtZddXjAAOtpmBg2PegtO6XoZJQmQh4CC",
      "paymentCompletionYn":"Y",
      "paymentCompleteYmdt":"20220128131737"
   },
   "message":"success",
   "code":0
}
```
* ❕ `orderNo`: PAYCO 주문번호
* ❕ `orderCertifyKey`: PAYCO 주문인증키

## Cancel (결제 취소)
#### PAYCO에 넘길 정보
```json
{
  "sellerKey": "{sellerKey}",
  "orderNo": "202201010000000001",
  "orderCertifyKey": "xxxxtSAAs7xPSaBtZddXjAAOtpmBg2PegtO6XoZJQmQh4CC",
  "cancelTotalAmt": 1500,
  "requestMemo": "Cancel reason",
  "orderProducts": [
    {
      "sellerOrderProductReferenceKey": "PRODUCT_00000001",
      "cpId": "{cpId}",
      "productId": "PROD_EASY",
      "productAmt": 1500,
      "cancelDetailContent": "Cancel reason for product"
    }
  ]
}
```
```sh
curl -v -X POST "https://alpha-api-bill.payco.com/outseller/order/cancel" -H "Content-Type: application/json" -d "{\"sellerKey\": \"DEMO\", \"orderNo\": \"202201010000000001\", \"orderCertifyKey\": \"xxxxtSAAs7xPSaBtZddXjAAOtpmBg2PegtO6XoZJQmQh4CC\", \"cancelTotalAmt\": 1500, \"requestMemo\": \"Cancel reason\", \"orderProducts\": [{\"sellerOrderProductReferenceKey\": \"PRODUCT_00000001\", \"cpId\": \"DEMO\", \"productId\": \"PROD_EASY\", \"productAmt\": 1500, \"cancelDetailContent\": \"Cancel reason for product\"}]}"
```

#### 결과:
```json
{
   "result":{
      "orderNo":"202201010000000001",
      "cancelTradeSeq":2000601560,
      "totalCancelPaymentAmt":1500.0,
      "remainCancelPossibleAmt":0.0,
      "cancelPaymentDetails":[
         {
            "paymentTradeNo":"202201010000000004",
            "paymentMethodCode":"31",
            "paymentMethodName":"신용카드",
            "cancelPaymentAmt":1500.0,
            "tradeYmdt":"20220128140438",
            "cancelPaymentTradeNo":"202201010000000005",
            "cancelTaxableAmt":1363.0,
            "cancelTaxfreeAmt":0.0,
            "cancelVatAmt":137.0,
            "cancelServiceAmt":0.0
         }
      ],
      "cancelReceiptPaycoPointAmt":0.0,
      "cancelReceiptPaycoPointTaxfreeAmt":0.0,
      "cancelReceiptPaycoPointTaxableAmt":0.0,
      "cancelReceiptPaycoPointVatAmt":0.0,
      "cancelReceiptPaycoPointServiceAmt":0.0,
      "cancelYmdt":"20220128140438"
   },
   "message":"success",
   "code":0
}
```
