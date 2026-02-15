# 시스템 구조

배차통 시스템의 전체 구조와 각 구성요소를 설명합니다.

---

## 전체 시스템 구성

배차통은 **웹 대시보드**와 **API 서버**, 그리고 **외부 연동 시스템**으로 구성됩니다.

```mermaid
flowchart TB
    subgraph Users["사용자"]
        User["화주 (업체)"]
        Admin["관리자"]
        Driver["기사"]
    end

    subgraph Web["웹 대시보드"]
        Dashboard["대시보드"]
        OrderMgmt["배차 관리"]
        Billing["청구/정산"]
    end

    subgraph API["API 서버"]
        Logic["비즈니스 로직"]
        DB[(데이터베이스)]
    end

    subgraph External["외부 시스템"]
        NaverMap["네이버 지도"]
        Call24["24시콜"]
        Popbill["세금계산서"]
        Aligo["SMS/카카오"]
        Slack["Slack"]
    end

    User --> Dashboard
    Admin --> OrderMgmt
    Admin --> Billing
    Driver --> Dashboard

    Web --> API
    API --> External
```

---

## 구성 요소 설명

### 1. 웹 대시보드 (Frontend)

사용자가 직접 접하는 화면입니다.

| 항목 | 설명 |
|------|------|
| **역할** | 사용자 인터페이스 제공 |
| **사용자** | 화주, 관리자, 기사 |
| **접근 방식** | 웹 브라우저 |

**주요 화면:**
- 배차 요청/관리
- 배차 내역 조회
- 청구서/정산 관리
- 거래처/기사 관리
- 각종 설정

### 2. API 서버 (Backend)

모든 데이터 처리와 비즈니스 로직을 담당합니다.

| 항목 | 설명 |
|------|------|
| **역할** | 데이터 처리, 외부 연동 |
| **데이터베이스** | PostgreSQL |
| **기반 기술** | Strapi CMS |

**주요 기능:**
- 배차 정보 저장/조회
- 운임 계산
- 청구서 생성
- 외부 시스템 연동
- 알림 발송

### 3. 데이터베이스

모든 정보가 저장되는 곳입니다.

**저장 정보:**
- 배차 정보 (주문)
- 거래처 정보
- 사용자 정보
- 기사 정보
- 청구/정산 내역
- 가격표

---

## 외부 시스템 연동

배차통은 다양한 외부 시스템과 연동하여 기능을 확장합니다.

### 연동 시스템 목록

| 시스템 | 연동 목적 | 사용 기능 |
|--------|----------|----------|
| **네이버 지도** | 거리/경로 계산 | 운임 산정, 예상 시간 |
| **24시콜** | 외부 배차 플랫폼 | 기사 매칭 확대 |
| **Popbill** | 세금계산서 | 발행, 조회, 웹훅 수신 |
| **알리고** | 알림 발송 | SMS, 카카오 알림톡 |
| **유니패스** | 통관 정보 | 수출입 화물 정보 조회 |
| **Slack** | 내부 알림 | 신규 배차, 취소 알림 |
| **AWS S3** | 파일 저장 | 이미지, 서류 저장 |

### 연동 흐름도

```mermaid
flowchart LR
    subgraph 배차통["배차통 시스템"]
        API["API 서버"]
    end

    subgraph 지도["거리 계산"]
        Naver["네이버 지도 API"]
    end

    subgraph 배차["기사 매칭"]
        Call24["24시콜"]
    end

    subgraph 세금["세금계산서"]
        Popbill["Popbill"]
    end

    subgraph 알림["알림 발송"]
        Aligo["알리고"]
        Slack["Slack"]
    end

    API -->|"거리 조회"| Naver
    Naver -->|"거리/시간/요금"| API

    API -->|"배차 등록"| Call24
    Call24 -->|"기사 배정"| API

    API -->|"세금계산서 발행"| Popbill
    Popbill -->|"발행 결과"| API

    API -->|"SMS/카카오"| Aligo
    API -->|"팀 알림"| Slack
```

---

## 데이터 흐름

### 1. 배차 요청 흐름

```mermaid
sequenceDiagram
    participant 화주
    participant 웹
    participant API
    participant 지도
    participant DB

    화주->>웹: 배차 요청 입력
    웹->>API: 배차 정보 전송
    API->>지도: 거리 계산 요청
    지도-->>API: 거리/시간/통행료
    API->>API: 운임 계산
    API->>DB: 배차 정보 저장
    API-->>웹: 배차 생성 완료
    웹-->>화주: 결과 표시
```

### 2. 기사 배정 흐름

```mermaid
sequenceDiagram
    participant 관리자
    participant 웹
    participant API
    participant 24시콜
    participant Slack

    관리자->>웹: 배차 확인
    웹->>API: 24시콜 등록 요청
    API->>24시콜: 배차 정보 전송
    24시콜-->>API: 등록 완료

    Note over 24시콜: 기사 매칭 진행

    24시콜->>API: 기사 배정 알림
    API->>API: 기사 정보 저장
    API->>Slack: 배정 완료 알림
```

### 3. 청구/정산 흐름

```mermaid
sequenceDiagram
    participant 관리자
    participant 웹
    participant API
    participant Popbill
    participant DB

    관리자->>웹: 청구서 생성 요청
    웹->>API: 대상 주문 조회
    API->>DB: 청구 대상 조회
    DB-->>API: 주문 목록
    API-->>웹: 청구 대상 표시

    관리자->>웹: 세금계산서 발행
    웹->>API: 발행 요청
    API->>Popbill: 세금계산서 발행
    Popbill-->>API: 발행 완료
    API->>DB: 청구서 정보 저장
    API-->>웹: 발행 완료
```

---

## 시스템 특징

### 보안
- 사용자 인증 (JWT 토큰)
- 권한별 접근 제어
- 민감 정보 암호화

### 확장성
- 외부 API 연동 구조
- 모듈화된 설계

### 안정성
- 에러 추적 (Sentry)
- 자동 백업

---

## 기술 요약

| 영역 | 기술 |
|------|------|
| **프론트엔드** | React, Vite, Mantine UI |
| **백엔드** | Strapi CMS, Node.js |
| **데이터베이스** | PostgreSQL |
| **파일 저장** | AWS S3 |
| **에러 추적** | Sentry |

---

## 관련 문서

- [서비스 소개](./system-introduction.md) - 배차통이 무엇인지
- [외부 연동 기능](../04-features/integration-features.md) - 상세한 연동 설명
- [핵심 데이터 모델](../02-domain/entities.md) - 데이터 구조
