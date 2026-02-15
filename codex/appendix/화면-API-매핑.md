# 부록 A. 화면-API 매핑

주의
- 아래는 핵심 흐름 위주의 매핑입니다.
- Strapi 기본 CRUD(`/orders`, `/companies` 등)는 공통적으로 list/get/create/update/delete 패턴을 사용합니다.

## 1) 사용자 영역

| 화면 | 주요 목적 | 주요 API |
|---|---|---|
| 신규 배차(`/new`, `/quick`) | 오더 생성/수정 | `/orders`, `/notifications`, `/companies`, `/partners`, `/items` |
| 배차내역(`/history`) | 상태별 이력 조회 | `/orders?filters...` |
| 거래명세서(`/invoice`) | 청구/입금 현황 조회 | `/invoices`, `/tax/detail` |
| 거래처(`/partners`) | 상/하차지 관리 | `/partners` |
| 상차품(`/items`) | 품목 관리 | `/items` |
| 마일리지(`/points`) | 적립/차감 조회 | `/point-logs` |
| 내 회사(`/company`) | 회사 정보 수정 | `/companies/:id`, `/users/:id` |
| 내 정보(`/mypage`) | 프로필/비밀번호 | `/users/:id` |

## 2) 배차 운영 영역

| 화면 | 주요 목적 | 주요 API |
|---|---|---|
| 배차상황실(`/admin/orders`) | 일반오더 배차 운영 | `/orders`, `/notifications`, `/call/post`, `/unipass`, `/distance-price` |
| 선착불 배차(`/admin/orders/fee`) | 선착불 배차 운영 | `/orders`, `/notifications`, `/call/post`, `/unipass` |
| 오더상세 모달 | 오더 조회/취소/거리계산 | `/orders/:id`, `/notifications` |
| 기사매칭 모달 | 기사/운임 입력 | `/orders/:id`, `/notifications` |
| 정산입력 모달 | 계산서/계좌 입력 | `/orders/:id`, `/point-logs` |
| 콜연동 모달 | 24시콜 등록/수정/조회/취소 | `/call/post` |

## 3) 정산/세금/청구 영역

| 화면 | 주요 목적 | 주요 API |
|---|---|---|
| 업체별 거래내역(`/admin/bills`) | 회사별 건수/매출/매입 집계 | `/bills` |
| 청구생성(`/admin/invoice`) | 미청구 주문 청구 변환 | `/unclaimed-bills`, `/invoices`, `/batch-billmonth` |
| 청구/수금(`/admin/invoicehistory`) | 발행/수금/미수 통합관리 | `/invoices`, `/invoice-logs`, `/tax/issue`, `/tax/detail` |
| 미수금(`/admin/invoice/unpaid`) | overdue 현황 | `/invoices` |
| 매출세금계산서(`/admin/outgoingtax`) | 팝빌 발행문서 조회 | `/tax/list`, `/tax/detail` |
| 매입세금계산서(`/admin/incomingtax`) | 매칭 검증/승인/거절 | `/incoming-taxes`, `/orders`, `/tax/htdetail` |
| 지급관리(`/admin/accounting`) | 지급대상 조회/지급완료 | `/orders`, `/batch-finish`, `/tax/htdetail` |
| 즉시지급(`/admin/accounting/quick`) | 즉시지급 정산 | `/orders`, `/batch-finish` |

## 4) 관리/마스터 영역

| 화면 | 주요 목적 | 주요 API |
|---|---|---|
| 계약처 관리(`/admin/companies`) | 회사/정책/담당자 관리 | `/companies`, `/users` |
| 상하차지 관리(`/admin/partners`) | 거래처 마스터 | `/partners` |
| 상차품 관리(`/admin/items`) | 품목 마스터 | `/items` |
| 알림센터(`/admin/notifications`) | 변경 이력 추적 | `/notifications` |
| 마일리지(`/admin/points`) | 포인트 이력/출금 | `/point-logs`, `/users` |
| 상품권(`/admin/giftcards`) | 템플릿/정책 관리 | `/giftcards`, `/gift/balance`, `/gift/send` |
| 사용자 계정(`/admin/users`) | 계정/권한/소속 관리 | `/users`, `/users/count` |
| 기사 블랙리스트(`/admin/driver-blacklist`) | 기사 위험관리 | `/drivers` |

## 5) 분석/실험 영역

| 화면 | 주요 목적 | 주요 API |
|---|---|---|
| 기존 내역 분석(`/admin/price-analytics`) | 이력 분석(실험실) | `/orders` 중심 조회 |
| 거리별 운임단가(`/admin/distance-price`) | 거리x톤수 평균매입 매트릭스 | `/distance-price` |
| 거리계산(`/admin/distance-calc`) | 대량 거리/통행료 계산 | 외부 지도 API(클라이언트 측 호출) |
| 배차추이(`/admin/dispatch-trend`) | 담당자별 추이/지분 | `/dispatch-analytics/trend`, `/dispatch-analytics/share` |

## 6) 매핑상 확인 필요 항목
- 웹 코드에 `/payment-logs`, `/hubs` 호출 흔적이 있으나, 현재 API 코드 트리(`bctalk-api/src/api`)에서는 해당 리소스를 확인하지 못함
- 운영 중 별도 API를 참조하는지, 미사용 잔존 코드인지 확인 필요
