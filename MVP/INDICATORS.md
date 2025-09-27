# 기술적 지표 (Technical Indicators)

## 1. ATR (Average True Range)
**평균 참 변동폭**: 가격의 변동성을 측정하는 지표로, 일정 기간 동안의 참 변동폭의 평균값입니다.

**수식:**
- True Range (TR): $TR = \max(H - L, |H - C_{prev}|, |L - C_{prev}|)$
- ATR: $ATR_n = \frac{1}{n} \sum_{i=1}^{n} TR_i$ 또는 $ATR_n = \frac{ATR_{n-1} \times (n-1) + TR_n}{n}$

여기서:
- H: 고가 (High)
- L: 저가 (Low)  
- C_prev: 전일 종가 (Previous Close)
- n: 기간 (보통 14일)

**활용 / 확인 가능한 것:**
- 변동성 증가/감소 추세 파악 (ATR 상승 = 변동성 확대)
- 포지션 사이징(리스크 관리) 근거: ATR 기반 스탑로스 설정 (예: 엔트리 ± 1~2 ATR)
- 추세 전환 초기 구간에서 ATR이 급증하는지 모니터링
- 전략 필터: ATR이 너무 낮으면(비변동 구간) 매매 회피

---
## 2. Bollinger Band
**볼린저 밴드**: 이동평균선을 중심으로 표준편차를 이용해 상하한선을 그은 기술적 지표입니다.

**수식:**
- 중심선 (Middle Band): $MB = SMA_n$
- 상단선 (Upper Band): $UB = SMA_n + (k \times \sigma_n)$
- 하단선 (Lower Band): $LB = SMA_n - (k \times \sigma_n)$

여기서:
- SMA_n: n기간 단순이동평균
- k: 표준편차 배수 (보통 2)
- $\sigma_n$: n기간 표준편차

**활용 / 확인 가능한 것:**
- 밴드 수축(Squeeze): 향후 큰 변동(확장) 가능성 신호
- 상단/하단선 반복 터치: 추세 강도 or 과열/과매도 단서 (단, 단독 신호는 아님)
- 밴드 밖 종가 마감 후 재진입: 단기 반전 시그널로 활용 가능
- 변동성 국면 비교: 밴드 폭(Width = (UB-LB)/MB) 시계열화하여 모멘텀 필터로 사용

---
## 3. Supertrend
**슈퍼트렌드**: ATR을 기반으로 한 추세 추종 지표로, 매수/매도 신호를 제공합니다.

**수식:**
- Basic Upper Band: $BUB = \frac{H + L}{2} + (multiplier \times ATR)$
- Basic Lower Band: $BLB = \frac{H + L}{2} - (multiplier \times ATR)$

**Final Bands:**
- $FUB = \begin{cases} 
BUB & \text{if } BUB < FUB_{prev} \text{ or } C_{prev} > FUB_{prev} \\
FUB_{prev} & \text{otherwise}
\end{cases}$

- $FLB = \begin{cases} 
BLB & \text{if } BLB > FLB_{prev} \text{ or } C_{prev} < FLB_{prev} \\
FLB_{prev} & \text{otherwise}
\end{cases}$

**Supertrend:**
$Supertrend = \begin{cases} 
FLB & \text{if } C \leq FLB \text{ (상승 추세)} \\
FUB & \text{if } C \geq FUB \text{ (하락 추세)}
\end{cases}$

**활용 / 확인 가능한 것:**
- 추세 구간 vs 횡보 구간 구분 (Supertrend 방향 전환 횟수로 노이즈 측정)
- 트레일링 스탑(추세 추종 전략) 자동화 근거로 사용
- 파라미터 (기간, multiplier) 변화에 따른 민감도 튜닝 → 전략 최적화 대상
- 다른 모멘텀 지표(MACD, RSI 등)와 교차 확인으로 거짓 신호 필터링

---
## 4. MACD (Moving Average Convergence Divergence)
**이동평균 수렴확산**: 두 이동평균선의 차이를 이용한 모멘텀 지표입니다.

**수식:**
- MACD Line: $MACD = EMA_{12} - EMA_{26}$
- Signal Line: $Signal = EMA_9(MACD)$
- Histogram: $Histogram = MACD - Signal$

여기서:
- $EMA_{12}$: 12일 지수이동평균
- $EMA_{26}$: 26일 지수이동평균
- $EMA_{9}$: MACD의 9일 지수이동평균

**활용 / 확인 가능한 것:**
- 0선(Zero Line) 돌파: 추세 방향 전환 가능성
- MACD-시그널 교차: 진입/청산 타이밍 후보 (역추세 장에서 속임 많음)
- 히스토그램 수축 → 재확장: 모멘텀 재가속 신호
- 가격 vs MACD 다이버전스: 추세 피로/전환 가능성 조기 포착
- 고변동 자산(BTC)에서는 파라미터 조정(예: 8,21,5)으로 민감도 튜닝

---
## 지표 결합 활용 아이디어
- Supertrend 방향 + MACD 0선 필터: 추세 정합 진입만 허용
- Bollinger Band Squeeze + ATR 확장 시작: 변동성 돌파 전략 트리거
- ATR 기반 포지션 사이징 + Supertrend 트레일링 스탑: 리스크 일관화
- MACD 다이버전스 발생 시 Bollinger Band 외측 확장 실패 여부 확인 → 반전 신뢰도 강화

> 추후: 각 지표를 함수화하여 신호 컬럼(예: `signal_long`, `signal_exit`) 생성 후 백테스트 구조 확장 가능.

## pandas_ta로 각 지표 계산 & 사용 방법

아래 예시는 `df`가 다음 컬럼을 갖고 있다고 가정합니다: `date, open, high, low, close, volume`
`pandas_ta`는 `DataFrame.ta` 액세서를 통해 쉽게 다양한 기술적 지표를 추가할 수 있습니다.

### 1. ATR (Average True Range)
```python
# 기본: 기간 14 (default length=14)
df.ta.atr(length=14, append=True)
```
생성 컬럼 예: `ATRr_14` (혹은 버전에 따라 `ATR_14`)

옵션:
- `mamode`: 이동평균 방식 ( 기본 RMA/EMA 등 )
- `drift`: 변화 계산 간격 (기본 1)

활용 팁:
```python
# ATR 기반 변동성 스탑
df['atr_stop_long'] = df['close'] - 2 * df['ATRr_14']
```

### 2. Bollinger Bands (BBANDS)
```python
# 기본: 길이 20, 표준편차 2
df.ta.bbands(length=20, std=2, append=True)
```
생성 컬럼 (접두: `BBL`, `BBM`, `BBU`, `BBB`, `BBP`):
- `BBL_20_2.0`: 하단선 (Lower)
- `BBM_20_2.0`: 중심선 (Middle)
- `BBU_20_2.0`: 상단선 (Upper)
- `BBB_20_2.0`: 밴드폭(%) 또는 밴드 너비(Bandwidth)
- `BBP_20_2.0`: %B (가격이 밴드 내 위치)

활용 예:
```python
# 밴드폭 수축 (변동성 압축 구간 탐지)
df['bb_squeeze'] = df['BBB_20_2.0'] < df['BBB_20_2.0'].rolling(120).quantile(0.1)
```

### 3. Supertrend
```python
# length = ATR 길이, multiplier = ATR 배수
df.ta.supertrend(length=7, multiplier=3.0, append=True)
```
생성 컬럼 예:
- `SUPERT_7_3.0`: Supertrend 값 (현재 추세선)
- `SUPERTd_7_3.0`: 방향 (1 = 상승, -1 = 하락)
- `SUPERTl_7_3.0`, `SUPERTs_7_3.0`: 밑/위 밴드 (long/short 기반)

활용 예:
```python
# 방향 신호를 이용한 추세 필터
trend_col = 'SUPERTd_7_3.0'
df['long_signal'] = (df[trend_col] == 1) & (df[trend_col].shift(1) == -1)
df['short_signal'] = (df[trend_col] == -1) & (df[trend_col].shift(1) == 1)
```

### 4. MACD
```python
# fast=12, slow=26, signal=9 (전통 기본값)
df.ta.macd(fast=12, slow=26, signal=9, append=True)
```
생성 컬럼 예:
- `MACD_12_26_9`: MACD Line
- `MACDs_12_26_9`: Signal Line
- `MACDh_12_26_9`: Histogram

활용 예:
```python
# 골든/데드 크로스 탐지
macd = 'MACD_12_26_9'
signal = 'MACDs_12_26_9'
df['macd_cross_up'] = (df[macd] > df[signal]) & (df[macd].shift(1) <= df[signal].shift(1))
df['macd_cross_dn'] = (df[macd] < df[signal]) & (df[macd].shift(1) >= df[signal].shift(1))
```

### 5. 공통 패턴 & 체이닝
`pandas_ta`는 여러 지표를 한 번에 추가할 수도 있습니다.
```python
# 원하는 지표 사전 정의
ta_list = [
    {'kind': 'atr', 'length': 14},
    {'kind': 'bbands', 'length': 20, 'std': 2},
    {'kind': 'macd', 'fast': 12, 'slow': 26, 'signal': 9},
]

# run 메서드 활용
df.ta.strategy(ta_list)
```
또는:
```python
df.ta.atr(length=14, append=True)
df.ta.bbands(length=20, append=True)
df.ta.macd(append=True)
```

### 6. 컬럼 네이밍 주의
지표마다 파라미터가 접미사로 붙어 다수 생성되므로 `print([c for c in df.columns if 'MACD' in c])` 처럼 필터링해서 확인.

### 7. NaN 처리
- 초기 구간(rolling window 미충족) 지표는 NaN 발생 → `df.dropna()` 또는 `df.fillna(method='bfill')` 사용 여부 전략에 따라 결정
- Supertrend, MACD 크로스 신호 계산 시 `shift()` 사용 전 NaN 제거 체크

### 8. 성능 & 최적화
- 너무 많은 지표를 한 번에 붙이면 메모리 증가 → 필요한 컬럼만 유지
- 백테스트 전 `df = df.loc[df['date'] >= '2021']` 등 기간 슬라이싱
- MultiIndex 사용(여러 자산 결합 시) → `groupby` + `apply`로 개별 지표 계산

### 9. 간단 통합 예시
```python
# 핵심 지표 생성
df.ta.atr(length=14, append=True)
df.ta.supertrend(length=7, multiplier=3, append=True)
df.ta.macd(append=True)

# 조건 기반 진입/청산 예시
trend = 'SUPERTd_7_3.0'
macd_line = 'MACD_12_26_9'
macd_sig = 'MACDs_12_26_9'
atr_col = 'ATRr_14'

# 롱 진입: Supertrend 상승 + MACD > Signal
df['enter_long'] = (df[trend] == 1) & (df[macd_line] > df[macd_sig])
# 청산: MACD 크로스다운 또는 Supertrend 하락 전환
df['exit_long'] = ((df[macd_line] < df[macd_sig]) & (df[macd_line].shift(1) >= df[macd_sig].shift(1))) | \
                  ((df[trend] == -1) & (df[trend].shift(1) == 1))

# ATR 기반 스탑 레벨 (예시)
df['stop_long'] = df['close'] - 2 * df[atr_col]
```

### 10. 다음 확장 아이디어
- 포지션 컬럼(`position`) 생성 후 누적 수익 곡선 산출
- 파라미터 스윕(Grid Search) → 승률, MDD, CAGR 비교
- 슬리피지/수수료 모델 적용

> 필요하면: 백테스트 뼈대 코드/포지션 로직/성과지표 계산 셋업도 추가해 드릴 수 있습니다. 요청 주세요.
