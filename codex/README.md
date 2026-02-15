# 배차통 현행 시스템 분석 문서

작성 기준
- 기준 코드베이스: `/Users/kimtj/github/bctong-migration`
- 분석 범위:
  - 웹 대시보드: `/Users/kimtj/github/bctong-migration/bctalk-web`
  - API 서버(Strapi): `/Users/kimtj/github/bctong-migration/bctalk-api`
- 대상 독자: 기획자/운영자(비개발자 중심)
- 목적: 현행 파악, 운영 로직 명확화, v2 로드맵 준비

## 문서 구성
1. `/Users/kimtj/github/bctong-migration/codex/01-서비스-개요.md`
2. `/Users/kimtj/github/bctong-migration/codex/02-역할별-기능맵.md`
3. `/Users/kimtj/github/bctong-migration/codex/03-핵심-업무플로우.md`
4. `/Users/kimtj/github/bctong-migration/codex/04-상태전이-비즈니스규칙.md`
5. `/Users/kimtj/github/bctong-migration/codex/05-외부연동-자동화.md`
6. `/Users/kimtj/github/bctong-migration/codex/06-데이터모델-용어사전.md`
7. `/Users/kimtj/github/bctong-migration/codex/07-v2-준비-관찰사항-개선백로그.md`
8. `/Users/kimtj/github/bctong-migration/codex/08-확인필요사항-질문리스트.md`
9. `/Users/kimtj/github/bctong-migration/codex/appendix/화면-API-매핑.md`
10. `/Users/kimtj/github/bctong-migration/codex/appendix/주요-엔드포인트-목록.md`

## 빠른 읽기 가이드
- 전체 비즈니스 한 번에 파악: 01 → 03 → 04
- 역할별 화면/권한 파악: 02
- 외부 연동·자동화·스케줄 파악: 05
- 데이터 항목 정의/용어 정리: 06
- v2 우선순위·리스크 확인: 07, 08
- 실무 추적(어떤 화면이 어떤 API를 쓰는지): appendix 2종

## 해석 원칙
- 구현 코드가 우선 기준이며, 화면 문구/주석은 보조 근거로 사용함
- 일부 기능은 "현재 코드상 존재"와 "운영 사용 여부"가 다를 수 있어, 별도 확인 항목으로 분리함
- 기술 설명보다 업무/정책 중심으로 번역하되, 운영에 필요한 정량 규칙(계산식/상태조건/스케줄)은 누락 없이 기록함
