# 📌 기술적 지표 설명서 (수식·예시 포함)

본 문서는 본 프로젝트에서 활용되는 주요 기술적 지표에 대한 **정의, 핵심 수식, 시각 예시**를 제공합니다.  
예시 그림은 임의의 샘플 데이터로 생성되었으며 지표 개념 설명을 목적으로 합니다.

---

## 1) SMA (Simple Moving Average, 단순 이동평균선)
**정의**: 일정 기간 \(n\) 동안의 종가 평균.  
**수식**:
\[
SMA_t = \frac{1}{n} \sum_{i=0}^{n-1} P_{t-i}
\]
**예시**:  
![SMA vs EMA](sma_ema_example.png)

---

## 2) EMA (Exponential Moving Average, 지수 이동평균선)
**정의**: 최근 가격에 더 높은 가중치를 부여한 이동평균.  
**수식**:
\[
EMA_t = \alpha \cdot P_t + (1-\alpha)\cdot EMA_{t-1}, \quad \alpha = \frac{2}{n+1}
\]
**비고**: EMA는 SMA 대비 신호 반응이 빠름.  
**예시**:  
![SMA vs EMA](sma_ema_example.png)

---

## 3) RSI (Relative Strength Index, 상대강도지수)
**정의**: 일정 기간 상승폭과 하락폭의 비율로 과매수/과매도 상태를 나타내는 모멘텀 지표.  
**수식**:
\[
RS = \frac{\text{평균 상승폭}}{\text{평균 하락폭}},\quad
RSI = 100 - \frac{100}{1+RS}
\]
**범위 기준**: 일반적으로 70 이상 과매수, 30 이하 과매도로 해석.  
**예시**:  
![RSI](rsi_example.png)

---

## 4) MACD (Moving Average Convergence Divergence)
**정의**: 장단기 EMA 차이를 이용한 추세 지표.  
**수식**:
\[
MACD_t = EMA_{12}(P)_t - EMA_{26}(P)_t,\quad
Signal_t = EMA_9(MACD)_t
\]
**비고**: MACD와 시그널선 교차, 0선 상/하 여부가 해석 포인트.

---

## 5) Bollinger Bands (볼린저 밴드)
**정의**: 이동평균선을 중심으로 표준편차를 활용해 상·하단 밴드를 형성하는 변동성 지표.  
**수식**:
\[
\text{Upper} = SMA_n + k \cdot \sigma,\quad
\text{Lower} = SMA_n - k \cdot \sigma
\]
여기서 \(\sigma\)는 최근 \(n\)기간 표준편차, \(k\)는 보정계수(일반적으로 2).  
**예시**:  
![Bollinger Bands](bollinger_example.png)

---

## 6) ATR (Average True Range, 평균 진폭)
**정의**: 순수 변동성(진폭) 크기를 측정하는 지표.  
**수식**:
\[
TR_t = \max\big(High_t - Low_t,\ |High_t - Close_{t-1}|,\ |Low_t - Close_{t-1}|\big)
\]
\[
ATR_t = \frac{1}{n}\sum_{i=0}^{n-1} TR_{t-i}
\]
**예시**:  
![ATR](atr_example.png)

---

## 7) SuperTrend (슈퍼트렌드)
**정의**: ATR을 기반으로 추세 전환 시점을 시각화하는 추세 추종형 지표.  
**핵심 개념식**:
\[
\text{Basic Upper/Lower Band} = \frac{High + Low}{2} \pm (Multiplier \times ATR)
\]
\[
\text{SuperTrend} = 
\begin{cases}
\text{Upper Band 업데이트 규칙 적용} & \text{(하락 추세)} \\
\text{Lower Band 업데이트 규칙 적용} & \text{(상승 추세)}
\end{cases}
\]
**비고**: 실제 구현은 캔들 종가가 밴드를 돌파/유지하는지에 따라 상·하 밴드를 갱신하는 절차적 로직을 포함.

---


