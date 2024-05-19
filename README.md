# Stock EDA
시장에 신규로 상장되는 주식 종목들의 시계열 패턴을 분석하기 위함입니다. 
프로젝트 목적으로는 시장에 신규로 상장한 종목들이 일정한 패턴이 있다는것을 트레이딩으로 경험적 증거 (Empirical Evidence)를 바탕으로하여 이를 검증하기 위해서 여러가지 통계적 검증을 통해 가설을 검증하는 것을 목표로합니다.

## 데이터 구조

2020~2024 년 동안 상장된 신규주들의 데이터를 수집하였고, 수집된 데이터는 각 종목이 상장 날로 부터 81(약 4달) 기간동안의 데이터를 수집하였습니다. 스팩상장 주들은 분석에서 제외를 하였고, IPO 신규상장 주식만을 수집하여 수집 된 종목의 수는 320개가 됩니다.

데이터 특성으로는 현재가, 저가, 고가, 시가, 거래량, 거래대금 을 수집하였습니다. 추가적으로 공모가 대비 가격(현재가, 저가, 고가, 시가) 4개의 특성을 추가 해주었습니다. 그래서 각 종목의 데이터는 최대 길이는 81, 특성은 10개인 
형태를 가지게 됩니다. 추가해준 공모가대비 가격 특성은 신규 상장 주식들이 처음 상장되는 기준이 되는 가격 대비 상승을 알기 위해 추가해준 특성이라고 생각하시면 되겠습니다. 


## 시계열 패턴 식별

추세 (Trend): 시간이 지남에 따라 시계열 데이터가 증가하거나 감소하는 경향이 있을 때 나타납니다. ACF와 PACF분석을 통해서 지속적으로 +,-를 값을 확인하여 추세가 존재할 가능성을 확인합니다.

계절성 (Seasonality): 일정한 주기마다 데이터가 반복되는 경향이 있을 때 나타납니다. ACF 분석을 통해 주기적인 패턴이 보이면, 계절성이 존재할 가능성이 있습니다.

자기회귀 패턴 (Autoregressive Pattern): 데이터가 과거 값에 의존하는 경우입니다. PACF분서에서 초기 몇몇 지연에서 높은 값을 보이면, AR(1~2) 모델이 적합할 수 있습니다.

이동평균 패턴 (Moving Average Pattern): 데이터가 과거의 오차 항에 의존하는 경우입니다. ACF에서 초기 몇몇 지연에서 높은 값을 보이면, MA(1~2) 모델이 적합할 수 있습니다.

아래의 그림은 ACF, PACF 분석을 한 결과입니다.

### Mean ACF (자기상관함수)
ACF 그래프는 시간 지연(lag)에 따른 시계열 데이터의 자기상관성을 보여줍니다. 첫 번째 시차(lag 1)에서의 자기상관이 매우 높으면 데이터가 강하게 자기상관되어 있음을 의미합니다. 시차가 증가할수록 자기상관값은 감소하며, 대략 5 시차 이후로는 0에 가까워집니다. 이는 장기적인 시차에서는 변수나 데이터의 값에 대한 영향이 작아진다는 것을 의미합니다. 이러한 자기상관 감소 패턴은 데이터에 일정한 패턴이나 주기성이 있을 가능성을 시사합니다.

예를 들어, 한 해 동안의 월별 판매량 데이터를 생각해보겠습니다.

첫 번째 시차(lag 1): 만약 한 달 전의 판매량과 현재 월의 판매량 간에 높은 자기상관이 있다면, 이는 한 달 전의 판매량이 현재 월의 판매량에 강한 영향을 미친다는 것을 의미합니다. 예를 들어, 여름철 에어컨 판매량이 증가하는 계절적 패턴이 있을 수 있습니다.

시차 증가: 시차가 증가할수록 자기상관값이 감소한다면, 이는 과거 데이터와 현재 데이터 간의 관련성이 점차 약화된다는 것을 의미합니다. 대략 5 시차 이후에는 자기상관이 거의 0에 가깝기 때문에, 5개월 전의 판매량이 현재 월의 판매량에 미치는 영향은 거의 없다고 볼 수 있습니다. 이는 장기적인 시간에 따른 패턴이나 추세가 일정해진다는 것을 시사합니다.

### Mean PACF (부분자기상관함수)
PACF 그래프는 시간 지연(lag)에 시계열 데이터의 부분자기상관성을 보여줍니다. 부분자기상관성이란 특정 시차(lag)에서 두 시점 간의 상관관계를 설명할 때, 그 사이에 있는 다른 시점들의 영향을 배제하고 순수한 상관관계를 측정한 값을 의미합니다.
아래그림을 확인해보면, 첫 번째 lag와 두번 째 lag만이 높은 갚을 나타내고 나머지는 거의 0에 수렴하는 값을 보이고 있습니다.

이것 또한 예를 들어, 한 해 동안의 월별 판매량 데이터로 생각해보겠습니다.
 
첫 번째 시차(lag 1): 만약 우리가 1달 전의 판매량과 현재 월의 판매량을 비교했을 때, 높은 부분자기상관을 보인다면 이것은 1달 전의 판매량이 현재 월의 판매량에 미치는 영향이 크다는 것을 의미합니다. 이것은 예를 들어, 이전 달의 판매량이 이번 달의 재고 수량에 영향을 준다거나, 이전 달의 광고 효과가 이번 달의 판매량에 영향을 준다는 것을 의미할 수 있습니다.

시차 2: 시차가 2인 경우, 2달 전의 판매량과 현재 월의 판매량 사이의 자기상관을 살펴봅니다. 이 값이 높다면, 2달 전의 판매량이 현재 월의 판매량에 영향을 미치고 있다는 것을 의미합니다. 하지만 시차가 1일 때보다 이 영향은 약하다는 것을 의미합니다. 그리고 이후의 시차들에서는 시차가 커질수록 부분자기상관값이 급격히 감소합니다. 이는 그 이후의 달들의 판매량은 현재 월의 판매량에 대해 덜한 영향을 미친다는 것을 의미합니다.

시차 3 이후: 시차가 3 이후로는 대부분의 부분자기상관값이 0에 가까워지거나 음수 값을 보인다고 가정합니다. 이는 해당 시차 이후의 데이터들은 이전 달들의 판매량과는 뚜렷한 관련성이 없다는 것을 의미합니다. 이 경우에는 해당 시차 이후의 데이터들이 현재 월의 판매량에 대해 거의 예측할 수 없다고 생각할 수 있습니다.


### 결론
 ACF 그래프가 높은 값에서 점진적으로 하락하여 시차 5에서 0에 가까운 값을 가지는 경우, 이는 데이터에 일정한 패턴이 존재할 가능성을 시사합니다. 이 패턴은 단기적인 자기상관과 장기적인 독립성을 반영하며, 이를 통해 적절한 시계열 모델을 선택하고
 패턴을 분석할 수 있습니다.

PACF의 lag 1,2가 높다는 것은 단기적 의존성 데이터가 단기적으로 자기상관되어 있음을 의미합니다. 즉, 현재 값이 직전 두 시점의 값에 의해 설명되는 경향이 강하다는 것을 나타냅니다. 이것을 통해 단기적인 패턴이 존재 할 수 있는것을 암시합니다.






<p align='center'>
  <img src = https://github.com/sangse/Stock_EDA/assets/145996429/2769b362-7cc1-4d3a-8efb-7e28d9c1a014>
</p>
