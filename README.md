## DSP Project – iPhone 15 Resale Price Analysis

본 프로젝트는 중고 거래 플랫폼(중고나라/중고마켓 등)에서 수집한 **iPhone 15 시리즈 판매 완료/예약중 상품 데이터**를 기반으로,
시간이 지남에 따라 중고 시세가 어떻게 감가(depreciation)되는지, 그리고 **모델·용량·등급·배터리 정보**가 가격에 어떤 영향을 주는지 정량적으로 분석하는 데 목적이 있습니다.

### Project Pipeline
- **1. Crawling & Raw Data Collection**
  - Selenium 기반 크롤러로 iPhone 15 관련 상품 페이지를 대량 수집
  - 제목·설명·가격·상태·날짜·모델/용량 등 상세 정보 스크래핑
  - 중복/중단 방지를 위해 resume용 임시 CSV를 주기적으로 저장

- **2. Data Cleaning & Feature Engineering**
  - 스팸/매입·교환·세트 상품 등 노이즈 데이터 필터링
  - 상대 시점 표현(“n일 전”, “n시간 전”)을 실제 날짜로 변환 후, 출시일 기준 `days/weeks_since_launch` 계산
  - 텍스트에서 **모델(`Normal/Plus/Pro/Pro Max`), 용량(128/256/512/1024GB), 배터리 효율(%)**을 규칙 기반으로 추출
  - 제목·설명 패턴을 이용해 **상태 등급(prompt_grade: S/A/B/C)** 를 휴리스틱으로 분류

- **3. Exploratory Data Analysis (EDA)**
  - 주차별·등급별·모델별 평균 가격 추이 및 분포 시각화
  - 이상치(outlier) 탐지(IQR 기준) 및 요약, 별도 CSV로 저장
  - Feature 분포(모델×용량, 모델×등급, 배터리 효율 vs 가격 등)를 그림으로 정리

- **4. Statistical Modeling**
  - `price ~ weeks_since_launch` / `log_price ~ weeks_since_launch` 기본 감가 모델 적합
  - 등급·모델·용량을 포함한 확장 회귀모델로 **각 요인의 마진 효과와 감가율 차이** 추정
  - Pro 모델에 대해 ANOVA 및 Tukey HSD로 **등급 간 평균 가격 차이** 검정
  - R² 기여도 분석으로 **시간 vs 모델 vs 용량 vs 등급 vs 배터리**의 상대적 중요도 비교

- **5. Strategy & Visualizations**
  - 주차별 가격 경향과 회귀선, 등급별 감가율, Feature Importance 등을 개별 플롯으로 생성
  - 단순/공격적 마크다운(가격 인하) 전략을 가정하고, **판매 소요 일수·평균 판매가**를 Monte Carlo로 시뮬레이션
  - Price Stability Index(PSI) 등 지표를 통해 **가격 안정성/변동성**을 시각적으로 요약

### Why This Process?
- **실제 중고 거래 환경을 최대한 반영**하기 위해, 원천 데이터 수집부터 텍스트 파싱, 결측치/노이즈 처리까지 직접 설계했습니다.
- 단순 평균 비교가 아니라, **시간·모델·용량·등급을 동시에 통제하는 회귀/ANOVA 분석** 으로 보다 인과적으로 해석하려는 목적이 있습니다.
- 마지막으로, 분석 결과를 **가격 전략·마케팅/구매 의사결정에 바로 쓸 수 있도록** 그래프와 시나리오 시뮬레이션까지 한 번에 만드는 구조로 설계했습니다.
