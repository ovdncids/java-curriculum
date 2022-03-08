# KakaoPay
* [결제 API 문서](https://developers.kakao.com/docs/latest/ko/kakaopay/single-payment)
* [카카오 개발자 콘솔](https://developers.kakao.com/console)
```sh
카카오 개발자 콘솔
  Admin 키 확인
  플랫폼 -> Web -> 사이트 도메인 (콜백 URL 설정)
    http://localhost:8080
```

## Ready (결제 요청)
```sh
curl -v -X POST "https://kapi.kakao.com/v1/payment/ready" \
-H "Authorization: KakaoAK {APP_ADMIN_KEY}" \
--data-urlencode "cid=TC0ONETIME" \
--data-urlencode "partner_order_id=partner_order_id" \
--data-urlencode "partner_user_id=partner_user_id" \
--data-urlencode "item_name=초코파이" \
--data-urlencode "quantity=1" \
--data-urlencode "total_amount=2200" \
--data-urlencode "vat_amount=200" \
--data-urlencode "tax_free_amount=0" \
--data-urlencode "approval_url={콜백 URL}/approval_url" \
--data-urlencode "fail_url={콜백 URL}/fail_url" \
--data-urlencode "cancel_url={콜백 URL}/cancel_url"
```
* `approval_url`: 카카오페이 결제 완료 후 우리쪽으로 콜백 받을 주소이다.

#### 결과: 
```json
{
   "tid": "T000000000xxxxxxxxxx",
   "tms_result": false,
   "next_redirect_app_url": "https://online-pay.kakao.com/mockup/v1/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63/aInfo",
   "next_redirect_mobile_url": "https://online-pay.kakao.com/mockup/v1/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63/mInfo",
   "next_redirect_pc_url": "https://online-pay.kakao.com/mockup/v1/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63/info",
   "android_app_scheme": "kakaotalk://kakaopay/pg?url=https://online-pay.kakao.com/pay/mockup/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63",
   "ios_app_scheme": "kakaotalk://kakaopay/pg?url=https://online-pay.kakao.com/pay/mockup/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63",
   "created_at": "2022-01-26T20:54:29"
}
```
* ❕ `tid`: 결제 고유 번호
* ❕ `next_redirect_pc_url`: 카카오페이 결제창(QR코드) URL
* ❕ `next_redirect_mobile_url`: 딤드된 배경에서 `다음` 버튼을 누르면, `android_app_scheme` 주소가 호출 되어 `카카오톡의 카카오페이`로 이동 된다.
* ❕ `android_app_scheme`: `카카오톡의 카카오페이`로 이동할때 사용 된다. (`ios_app_scheme` 동일한 문자)

## 결제 진행
* `QR코드` 등을 이용해서 카카오페이 결제 절차 진행

## Approval (결제 승인)
* 결제 절차 진행 후 우리쪽 콜백 페이지에서 다시 카카오페이쪽으로 결제 승인 요청을 해야, 정상적으로 결제가 완료 된다.
* {콜백 URL}/approval_url?pg_token=xxxxxxxxxxxxxxxxxxxx
* ❕ `pg_token`: 결제승인 요청을 인증하는 토큰
```sh
curl -v -X POST "https://kapi.kakao.com/v1/payment/approve" \
-H "Authorization: KakaoAK {APP_ADMIN_KEY}" \
--data-urlencode "cid=TC0ONETIME" \
--data-urlencode "tid=T000000000xxxxxxxxxx" \
--data-urlencode "partner_order_id=partner_order_id" \
--data-urlencode "partner_user_id=partner_user_id" \
--data-urlencode "pg_token=xxxxxxxxxxxxxxxxxxxx"
```

#### 결과
```json
{
   "aid": "A000000000xxxxxxxxxx",
   "tid": "T000000000xxxxxxxxxx",
   "cid": "TC0ONETIME",
   "partner_order_id": "partner_order_id",
   "partner_user_id": "partner_user_id",
   "payment_method_type": "MONEY",
   "item_name": "초코파이",
   "quantity": 1,
   "amount": {
      "total": 2200,
      "tax_free": 0,
      "vat": 200,
      "point": 0,
      "discount": 0
   },
   "created_at": "2022-01-27T17:38:20",
   "approved_at": "2022-01-27T17:43:56"
}
```
* ❕ `tid`: 요청 고유 번호
* 이제 결제가 완료 되었다.

## Cancel (결제 취소)
```sh
curl -v -X POST "https://kapi.kakao.com/v1/payment/cancel" \
-H "Authorization: KakaoAK {APP_ADMIN_KEY}" \
-d "cid=TC0ONETIME" \
-d "tid=T000000000xxxxxxxxxx" \
-d "cancel_amount=2200" \
-d "cancel_tax_free_amount=0" \
-d "cancel_vat_amount=200"
```

#### 결과
```json
{
   "aid": "A000000000xxxxxxxxxx",
   "tid": "T000000000xxxxxxxxxx",
   "cid": "TC0ONETIME",
   "status": "CANCEL_PAYMENT",
   "partner_order_id": "partner_order_id",
   "partner_user_id": "partner_user_id",
   "payment_method_type": "MONEY",
   "item_name": "초코파이",
   "quantity": 1,
   "amount": {
      "total": 2200,
      "tax_free": 0,
      "vat": 200,
      "point": 0,
      "discount": 0
   },
   "approved_cancel_amount": {
      "total": 2200,
      "tax_free": 0,
      "vat": 200,
      "point": 0,
      "discount": 0
   },
   "canceled_amount": {
      "total": 2200,
      "tax_free": 0,
      "vat": 200,
      "point": 0,
      "discount": 0
   },
   "cancel_available_amount": {
      "total": 0,
      "tax_free": 0,
      "vat": 0,
      "point": 0,
      "discount": 0
   },
   "created_at": "2022-01-27T17:50:40",
   "approved_at": "2022-01-27T17:51:56",
   "canceled_at": "2022-01-27T17:53:09"
}
```
