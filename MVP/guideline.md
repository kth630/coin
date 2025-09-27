# 📌 MVP Guideline

## 1. 프로젝트 목표
- **비트코인 일봉 데이터**를 기반으로, **다음날 종가가 상승(1)인지 하락(0)인지 예측**하는 모델 개발  
- **MVP 범위**:  
  - pandas_ta를 활용한 주요 지표 생성 (EMA, RSI, MACD, Bollinger Bands, ATR, SuperTrend, SMA 등)  
  - 단순 등락 예측 (조건부 예측은 추후 확장)  
  - ML 모델: XGBoost / LightGBM / RandomForest  

---

## 2. 데이터 파이프라인
### 📂 data.ipynb
1. **데이터 수집**  
   - 원본: OHLCV (open, high, low, close, volume)  
   - 단위: 일봉  

2. **전처리**  
   - 결측치 확인 및 제거  
   - 데이터 타입(datetime 변환 등) 정리  

3. **타겟 생성**  
   ```python
   df['target'] = (df['close'].shift(-1) > df['close']).astype(int)
   df = df.dropna(subset=['target'])
   ```

---

### 📂 indicators.ipynb
1. **기술적 지표 생성**  
   - 이동평균: SMA, EMA  
   - 변동성 지표: Bollinger Bands, ATR  
   - 모멘텀 지표: RSI, MACD  
   - 추세 지표: SuperTrend  

2. **병합**  
   - 원본 데이터프레임에 지표 컬럼 추가  
   - 불필요하거나 상관관계가 너무 높은 지표는 제거 후보  

---

## 3. EDA (최소화)
- **NaN 체크** → 지표 계산 후 초기 구간 제거  
- **타겟 분포 확인**  
  ```python
  df['target'].value_counts(normalize=True)
  ```
- **지표 간 상관관계 (heatmap)**  
  - 중복 변수 제거에 참고  

---

## 4. 모델링
1. **Train/Test Split**  
   - 시계열 데이터 → 시간순으로 분리 (shuffle 금지)  

2. **Baseline 모델**  
   - XGBoost / LightGBM / RandomForest  

3. **평가 지표**  
   - Accuracy (기본)  
   - F1-score (불균형 대응용)  

---

## 5. 검증 및 개선
- **Baseline 성능 비교**: 랜덤 예측(≈50%) vs 모델  
- **Feature Importance** → 어떤 지표가 유효한지 확인  
- **EDA 확장** → 필요시 지표/타겟 관계 시각화  

---

## 6. 차후 확장
- 조건부 예측 (예: +1% 이상 상승 시만 1)  
- Hyperparameter Tuning  
- 다른 자산군 확장 (알트코인, 주식)  
- 백테스트 및 전략 시뮬레이션  
