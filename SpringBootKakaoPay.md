# KakaoPay
* 결제 API 문서
* https://developers.kakao.com/docs/latest/ko/kakaopay/single-payment
* 개발자 콘솔
* https://developers.kakao.com/console
```sh
개발자 콘솔
  Admin 키 확인
  플랫폼 -> Web -> 사이트 도메인	(콜백 URL 설정)
    http://localhost:8080
```

## ready
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
* 결과: 
```json
{
   "tid":"T0000000001425491179",
   "tms_result":false,
   "next_redirect_app_url":"https://online-pay.kakao.com/mockup/v1/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63/aInfo",
   "next_redirect_mobile_url":"https://online-pay.kakao.com/mockup/v1/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63/mInfo",
   "next_redirect_pc_url":"https://online-pay.kakao.com/mockup/v1/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63/info",
   "android_app_scheme":"kakaotalk://kakaopay/pg?url=https://online-pay.kakao.com/pay/mockup/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63",
   "ios_app_scheme":"kakaotalk://kakaopay/pg?url=https://online-pay.kakao.com/pay/mockup/0c64fa4a17947ae4d2d554a3e31564fe29893f8c2daf91d32a0269b8bde36e63",
   "created_at":"2022-01-26T20:54:29"
}
```

## approval_url
