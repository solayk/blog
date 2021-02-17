---
layout: default
title: 머신러닝
parent: Python
nav_order: 4
last_modified_date: 2021-02-08
---
# 머신러닝 Machine Learning
{: .no_toc }




<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# 절차

(1) 데이타 읽기

(2) 데이터와 레이블 분리 변수 선언

(3) 훈련데이터와 테스트 데이터로 분리하기 train_test_split()

(4) 알고리즘, 모델 학습 fit()

(5) 예측하기

(6) 검증하기

(7) 정확도 측정하기



# 회귀분석 Regression

독립변수(X)와 종속변수(Y)의 관계식에서 독립변수가 한 단위 증가할 때 종속변수가 얼마나 영향을 받는지 분석한다. 독립변수 개수에 따라 2가지 종류로 나뉜다.

- 단순회귀분석 : 독립변수가 1개
- 다중회귀분석 : 독립변수가 2개 이상

### 예시
{: .no_toc }
- 나이(X)가 많을 수록 소득(Y)가 높은지 분석
- 광고지불비(X)에 따라 상품 매출액(Y)이 영향을 받는지 분석
- 가계 수입(X)과 사교육비 지출(Y) 사이에 관계가 있는지 분석
- 어린이 연령(X1)과 하루 평균 학습시간(X2)를 이용하여 그 어린이의 성적(Y)을 예측하고자 할 때

### 종류
{: .no_toc }
- 단순 회귀분석 (선형회귀분석)
- 로지스틱 회귀분석



### 라이브러리
{: .no_toc }
- 싸이킷런 sklearn: scipy 기반 라이브러리. Anaconda에 내장되어 있다.



## 단순회귀분석(선형회귀분석)

### [예제] 전기생산량(독립변수)이 전기소비량(종속변수)에 미치는 영향

```python
%matplotlib inline

import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import statsmodels.api as sm

from scipy import stats
from matplotlib import font_manager, rc
from statsmodels.sandbox.regression.predstd import wls_prediction_std
from sklearn.datasets import make_regression

# 한글 폰트 설정
font_name = font_manager.FontProperties(fname="c:/Windows/Fonts/malgun.ttf").get_name()
rc('font', family=font_name)

# 월별 전기생산금액
x= [3.52, 2.51, 3.91, 4.07, 3.98, 4.29, 4.81, 3.73, 4.61, 3.39, 3.20]

# 월별 전기 사용량
y= [2.48, 2.27, 2.47, 2.37, 3.09, 3.18, 3.88, 3.03, 3.55, 2.99, 2.88]

result = stats.linregress(x,y)
result
```

실행결과 LinregressResult(slope=0.5546831443969521, intercept=0.8074740247672794, rvalue=0.7200942961345399, pvalue=0.012451312531534768, stderr=0.17816251936092412)

- 기울기(slope)
- 절편(intercept)
- 상관계수(rvalue) :상관관계의 정도를 파악하는 값 ( -1에서 1 )
  - 1에 근접하면 양의 상관관계
  - 1에 근접하면 음의 상관관계
  - 0에 근접하면 상관관계가 없다
- pvalue : 예측 불확실성의 정도를 나타내는 값
- 에러의 표준편차(stderr)
- 상관분석 : 두 변수 간에 선형적 관계가 있는지 분석
- 상관계수 : 상관관계의 정도를 파악하는 값 ( -1에서 1 )

1) pvalue가 0.05보다 작으므로 통계적으로 유의미하기에 귀무가설을 기각하고 대립가설을 채택하여 전기생랸량과 소비생산량 간에 상관관계가 있다.

2) rvalue(상관계수)가 1에 가까우므로 양의 상관관계가 있다고 볼 수 있다.

```python
slope, intercept, rvalue, pvalue, stderr = stats.linregress(x,y)
print('기울기 :',slope)
print('절편 :',intercept)
print('설명력(상관관계) :',rvalue)
print('p_value :',pvalue)

if pvalue < 0.05:
    print('유의미한 데이터')
else:
    print('유의미하지않은 데이터')

plt.scatter(x,y) # 산점도 그리기

x1 = np.array(x) # 회귀선 그리기
plt.plot(x1,x1 * slope + intercept, c='red')
plt.xlabel('전기생산량')
plt.ylabel('전기사용량')
```

전기생산량에 따른 예측 값을 구하려면

```python
4 * slope + intercept
```

### [예제] 오존 데이터셋에서 온도에 따른 오존량 예측

- 독립변수 : 온도(수치형),   종속변수 : 오존량(수치형)
- 독립변수가 하나이기에 단순회귀분석(선형회귀분석) 적용
- 귀무가설 : 온도가 오존량에 영향을 미치지 않는다
- 대립가설 : 온도가 오존량에 영향을 미친다

```python
df = pd.read_csv('../data/ozone/ozone.csv')
df.head()
```

1) 결측치 행 제거

2) 온도(Temp) 데이타 X와 오존(Ozone) 데이타 Y를 추출

3) 회귀분석 결과 출력



### [예제] 보스턴 주택가격

```python
from sklearn.datasets import load_boston # 보스턴 주택가격 데이터셋
from sklearn.linear_model import LinearRegression # 회귀분석을 위한 패키기
from sklearn.model_selection import train_test_split # 학습용, 검증용 데이터를 나누기 위한 패키기

boston = load_boston()
print(boston.DESCR)
```











회귀: 연속적인 데이터



- 문장에서 지명, 인명 찾기 : 분류
- 사진이나 녹음 음성으로 사람 알아보기 : 분류
- 울음소리 듣고 새 종류 알아내기 : 분류
- 구매력이 높은 VIP 고객 분류 : 회귀, 분류(가격대)
- 결함 발생 가능성이 높은 자동차 부품 사전 식별 : 회귀
- 종양 및 질병 식별 : 분류
- 특정 기간의 화산 분출 횟수 예측 : 회귀
- 회사의 연매출 예측 : 회귀
- 챔피언스리그 우승팀 예측 : 회귀(수치로 예측이라면), 분류



# sklearn 기본 라이브러리







## 의사결정나무 Desicion Tree





## 랜덤포레스트 Random Forest

결정트리(Decision Tree)의 앙상블(ensemble)





