
---

# 🌐 QuantiMizer – ML 기반 DB 최적화 퀀트 백테스팅 시스템

*25-2 KHU Capstone Design Project – ML-driven Database Optimization for Quantitative Backtesting*

---

## 📌 Overview

**QuantiMizer**는 복잡한 퀀트 투자 전략을 누구나 손쉽게 생성하고, 머신러닝 기반 DB 튜닝을 통해 **초고속 백테스팅**을 수행할 수 있도록 설계된 통합 플랫폼이다.
사용자는 **블록코딩(Blockly)** 기반 노코드 전략 빌더로 전략을 만들고, 백엔드는 이를 **고성능 SQL**로 자동 변환하여 PostgreSQL에서 실행한다. 또한 PilotScope 기반 ML 튜닝 엔진이 쿼리 워크로드를 학습해 **DB 성능을 자동 최적화**한다.

본 프로젝트는 DB 튜닝 연구와 실제 금융 도메인을 결합한 실증적 시스템으로,
특히 고회전율(High-turnover) 전략의 백테스팅 속도를 **24.8s → 1.8s (13.5배 향상)** 시켰다.


---

## 🧱 Key Features

### ✔️ 1) 노코드 전략 빌더 (React + Blockly)

* 드래그 앤 드롭으로 가치/모멘텀/기술적 지표 기반 전략 생성
* 유니버스 설정, 팩터 조합(가중치 적용), 포트폴리오 규칙을 시각적으로 구성
* 전략은 JSON 형태로 저장 → 백엔드가 SQL로 자동 변환


---

### ✔️ 2) 고성능 SQL 자동 변환 엔진 (FastAPI)

* 전략 JSON → Dynamic SQL Compiler
* 완전한 PIT(Look-ahead bias-free) 백테스팅 보장
* Window Functions, LATERAL JOIN, Z-score 정규화, Weighted Score 합성 자동 생성
* 백테스팅 결과를 Equity Curve / MDD / CAGR 형태로 제공


---

### ✔️ 3) ML 기반 DB 자율 튜닝 (PilotScope)

* 실제 금융 전략 워크로드 3종(Fundamental / Momentum / High-turnover)을 자동 생성
* PilotScope가 워크로드 실행 로그를 분석해 최적 Knob을 추천
* work_mem 증가, JIT 최적화, random_page_cost 조정 등 자동 적용
* 결과: High-turnover 전략에서 **13.5× 성능 향상**


---

### ✔️ 4) 시계열 주가 예측 TFT 모델 연동

* 300개+ 재무/기술적/거시 지표 기반
* 사용자가 Feature / Hyperparameter 설정 후 직접 학습 가능
* shap 기반 Feature Importance 시각화
* MAPE 6.82%, R² 0.94 성능 확보


---

### ✔️ 5) 커뮤니티 & 전략 공유 기능

* 백테스트한 전략을 게시글로 공유
* 다른 사용자는 Fork하여 전략을 재사용 및 확장
* 집단지성을 통한 전략 개선 흐름 제공


---

## 🏛 System Architecture

* **Frontend:** React + Blockly
* **Backend API:** FastAPI
* **DB:** PostgreSQL + PilotScope 튜닝 루프
* **Strategy Engine:** JSON → SQL 변환 및 시뮬레이션
* **ML Optimization Loop:** Workload Generator + PilotScope + Admin Service


---

## 🗄 Database Design

### Market Data (OLAP 최적화)

* `stocks_daily_info`: 약 850만 건 OHLCV + 기술적 지표
* `financials_quarterly`: LATERAL JOIN 기반 시점 매칭
* `fundamentals_daily`: PER/PBR 등 일별 밸류에이션


### Core Domain (JSONB 기반)

* `strategies`: 블록코딩 전략 트리(JSONB)
* `backtest_settings`: 동일 전략으로 기간/자본 조건을 다르게 실험
* `backtest_results`: Equity Curve, Metrics 저장(JSONB)


### System Logs (ML 학습용 데이터)

* 워크로드 실행 기록
* DB 설정 스냅샷
* PilotScope 튜닝 로그


---

## 🚀 Performance & Experiment Result

### 📌 High-turnover 전략 성능 (소형주 + 고회전)

| Type          | Before        | After        | Improvement         |
| ------------- | ------------- | ------------ | ------------------- |
| High-turnover | **24,841 ms** | **1,844 ms** | **+1,247% (13.5×)** |

핵심 원인:

* work_mem 증가 → Disk Sort 제거
* 비용 상수 조정 → 인덱스 활용 향상
* JIT 활성화


Momentum / Fundamental은 18~40ms 정도의 작은 지연 발생했으나 UX 영향 없음.
ML 튜닝의 목적 함수가 “전체 지연 최소화”이므로 High-turnover의 개선이 최적 전략.


---

## 🙋‍♂️ Team

| Name    | Role | Work                          |
| ------- | ---- | ----------------------------- |
| **이종규** | 팀장   | 프론트/백엔드 개발, 전략 엔진, 백오피스 구축    |
| **전현준** | 팀원   | PilotScope 기반 DB 튜닝, ML4DB 실험 |
| **정하규** | 팀원   | Selenium 크롤링, TFT 주가 예측 모델 개발 |
|         |      |                               |

---

## 🧪 Tech Stack

**Frontend:** React, TypeScript, Tailwind, Blockly
**Backend:** FastAPI, SQLAlchemy
**DB:** PostgreSQL 17, Parquet Staging
**ML/Optimization:** PilotScope, MLflow, TFT, LlamaTune/SMAC/Lero
**Infra:** Azure VM, Docker Compose

---

## 🎯 Contribution & Impact

본 프로젝트는 다음과 같은 중요한 기여를 달성하였다:

### 1) 도메인 특화 ML 기반 DB 튜닝 실증

금융 시계열·리밸런싱·대규모 윈도우 연산 환경에서 ML 튜닝의 효과 검증


### 2) 백테스팅 속도 혁신

24초 수준의 고복잡도 쿼리를 1초 내외로 단축하여 실시간 전략 실험 가능


### 3) 전략 실험 Coverage 확대

데이터 크기 제약 없이 고회전·대형 연산 전략까지 검증 가능

---

## 📬 Contact

([jklee3409@khu.ac.kr](mailto:jklee3409@khu.ac.kr))
