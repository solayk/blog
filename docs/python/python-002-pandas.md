---
layout: default
title: 판다스 분석
parent: Python
nav_order: 2
last_modified_date: 2021-02-04
---

# 판다스 분석 Pandas
{: .no_toc }

파이썬 판다스 분석에 대한 내용를 한 페이지에 모두 담았습니다. Ctrl + F 를 눌러 원하는 함수 이름, 이론 등을 검색하시면 빠르게 찾을 수 있습니다. 아래 목차는 삼각형을 누르면 숨겨집니다.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# 데이터 구조

## 시리즈 Series

데이터프레임(DataFrame)에서 하나의 열(Column) 데이터 구조체를 의미한다. 다시 말해 DataFrame은 Column(=Series)이 여러개인 데이타 구조체이다. DataFrame을 RDBMS의 테이블에 비유하면, Series는 테이블의 한 Column과 유사하다.

Series는 리스트, 튜플의 index와 dict 의 key 와 유사하고, 같은 값의 index가 가능(즉, 중복 가능)하다.

- List, Tuple index : 일련번호로 변경불가 / 순서 개념이 있다.
- Series Index : 중복 가능 / 순서 개념이 있다.
- Dict key : 중복 불가 / 순서 개념 없다.

```python
import pandas as pd
age_in = pd.Series([22,33,44])
print(age_in)							# 0 22						
   										# 1 33
   										# 2 44
print(age_in.index)					# RangeIndex(start=0, stop=5, step=1)
print(age_in.values)					# [22, 33, 44]
age_in.keys							# <bound method Series.keys of 0 22				
   										# 1 33
   										# 2 44
   										# dtype: int64>
age_in.keys()							# RangeIndex(start=0, stop=5, step=1)

age_in = pd.Series([22,33,44],index=['가','나','다'])
print(age_in)							# 가 22						
   										# 나 33
   										# 다 44
   										# dtype: int64
age_in['다'] = 100
print(age_in)							# 가 22						
   										# 나 33
   										# 다 100
   										# dtype: int64

srs = pd.Series(['가','나','다'],index=['a','b','a'])
print(srs['a'])							# a 가
										# a 다
										# dtype: object
print(srs[0])							# 가
print(srs['b'])							# 나
```

딕셔너리 dict를 Series로 변환할 수 있다.

```python
info_list = { 'kim': 25, 'park':22, 'lee':34 }
info_series = pd.Series(info_list)
print(info_series)						# kim 25
										# park 22
										# lee 34
										# dtype: int64
```

단, Series 변환 시 임의 지정할 index가 key 값과 다른 경우 변환할 수 없다.

```python
info_list = { 'kim': 25, 'park':22, 'lee':34 }
info_series = pd.Series(info_list, index=['kim',1,2])
print(info_series)						# kim 25.0
										# 1 NaN
										# 2 NaN
										# dtype: float64
```

DataFrame에서 하나의 컬럼만 추출하면 Series 이다.

```python
df.age									# Dataframe에서 age컬럼만 Series로 추출
```



## 데이터프레임 DataFrame

DataFrame은 2차원 데이터 구조체로 Numpy  보다 편리하게 데이타 핸들링한다. R의 DataFrame과 비슷하다.

```python
mydata = {
          'name':['홍길동','박길동','김길동'], 
          'age':[22,33,44], 
          'dept':['컴공','국어','산업']
         }

df = pd.DataFrame(mydata)
print(type(df))							# <class 'pandas.core.frame.DataFrame'>
print(df)
df
```

![1](https://user-images.githubusercontent.com/73984112/106235506-2862f380-623e-11eb-8fcd-2f85bf93acec.PNG)



### DataFrame 가져오기
{: .no_toc }

인덱스를 자동으로 만들지 않고, 데이터로 하고 싶다면 index_col 추가

```python
import pandas as pd

df = pd.read_excel('data/2016-01.xls', index_col='상품명')
df
```



### DataFrame 열, 행 추출
{: .no_toc }

df.컬럼명 또는 df['컬럼명'] : 열(컬럼) 추출

df.loc[ ] : 인덱스가 숫자라면 인덱스 순서, 인덱스 지정하면 인덱스명으로 행 추출

df.iloc[ ] : 인덱스 만으로 행 추출 (i: integer location)

```python
df.name								# 열 추출
df['name']								# 열 추출 (위 .name과 결과 같음)

df.loc[1]								# 행 추출
df.iloc[1]								# 행 추출 (순서로 필터링 할땐 차이 없음)

df2 = pd.DataFrame(mydata, index=['일','이','삼'])
df2.loc['이']							# 행 추출
df2
```

![2](https://user-images.githubusercontent.com/73984112/106235508-2a2cb700-623e-11eb-97d6-c5d77dc43110.PNG)

```python
df.loc[2, 3]								# 2 행의 3열 데이타
df.loc[1:3, 2:4]							# 1부터 3행전까지의 행에서 2부터 4전까지의 열의 데이타
df.iloc[-1]								# 마지막 행 데이타
df.iloc[[0,13,34]]						# 특정 행 데이타 동시 출력
```

마지막 행 데이터는 아래 방법으로도 추출할 수 있다.

```python
df.tail(1)
df.loc[df.shape[0]-1]					# shape은 tuple로 행, 열 값 반환 → 행 값 -1 = 마지막 행 인덱스
```



### DataFrame 2개 이상의 열 가져오기
{: .no_toc }

대괄호 2개 안에 칼럼 이름 표기

```python
df[['주문량','할인율']]						# type = DataFrame
```

바깥 괄호([ ])는 DataFrame, 안쪽 괄호는 List 이다.



### DataFrame 2개 이상의 행 가져오기
{: .no_toc }

```python
import pandas as pd
df = pd.read_excel('data/2016-01.xls', index_col='상품명')

df.loc[['야채','딸기','봉투']].sort_values(by='매출', ascending=False)
df.loc['야채':'딸기']

df[['President','College']].iloc[:10]		#특정 행 데이타 동시 출력 = df.iloc[:10,[0,12]]
```



### DataFrame 컬럼, 행 추가
{: .no_toc }
```python
df['gender'] = ['여자','남자','여자']		# 컬럼 추가
df.loc[9] = ['최길동',55,'기계','남자']		# 행 추가
```



### DataFrame 인덱스 순서 변경
{: .no_toc }
```python
df2 = df.reindex(index=[2,3,0,1,5])		# 없는 인덱스를 넣을 경우 해당 행 NaN 표기
```



### DataFrame 컬럼 연산
{: .no_toc }
```python
df['age+10']=df['age']+10
```



### DataFrame 값 변경
{: .no_toc }
아래 두가지 방법 외 여러가지가 있으나, 아래 명령어가 실용적이다.

```python
df.loc[9,'dept'] = '인문'
df.dept.iloc[9] = '인문'
```

![DataFrame](https://user-images.githubusercontent.com/73984112/106237656-96a9b500-6242-11eb-949b-8caaa82590b3.PNG)

### DataFrame 필터링
{: .no_toc }
```python
df.dept[2]									# 인덱스 2의 dept 컬럼값
df[df.age > 30]								# age 30 이상의 행 추출
```

정렬하거나 정렬한 결과를 바로 원본에 적용할 수도 있다.

```python
df.sort_index(ascending=0)							# 내림차순, 기본값 ascending=1 (오름차순)
df.sort_values('age',ascending=False) 					# 칼럼 정렬, ascending 기본값 True 오름
df.sort_values('age',ascending=False,inplace=True)	# 원본이 바귄다. 
```

Numpy와 유사해 혼동하기 쉬우나, 다른 부분도 있다.



### 기본 정보 확인
{: .no_toc }
info() : DataFrame 정보 보기

describe() : DataFrame 기본통계값 보기

```python
df.info()
df.describe()
```



### DataFrame 통계
{: .no_toc }
```python
data = pd.read_csv('./data/president_heights.csv')	# csv import

data.head()											# 데이터 일부 확인
data.tail()
data.tail(10)

data['height(cm)'].mean()

import matplotlib.pyplot as plt		# pyplot으로 시각화

data['height'].plot()					# 기본값 꺾은 선 그래프
data['height'].plot(kind='hist')			# histogram 히스토그램
plt.hist(data['height'])					# histogram 히스토그램 (위 결과와 같음)
data['height'].plot(kind='bar')			# bar 바
```



### 조건에 따라 값 순서대로 필터링
{: .no_toc }

```python
df.nlargest(10,'가격')					# '가격' 컬럼 높은 순 10개
df.nsmallest(10,'가격')					# '가격' 컬럼 낮은 순 10개
```



# 연산

## Series 연산
{: .no_toc }
Series 끼리 연산은 원본이 int 라도 결론은 float64로 변환

```python
from pandas import Series

s1 = Series([10, 15, 20], index=['일','이','삼'])
s2 = Series([2,4,6,8], index=['일','이','삼','사'])
	
s1 + 5									# 일 15, 이 20, 삼 25 (원본은 바꾸지 않음)
s1 + s1									# 같은 인덱스가 없으면 해당 인덱스 값만 NaN(Not a Number), 또한 int64 끼리 연산을 해도 결과는 float64

s3 = Series([1,2,3],index=['1줄','2줄','3줄'])

s1 + s3									# 모두 NaN, float64
```

## DataFrame 연산
{: .no_toc }
```python
from pandas import DataFrame
import numpy as np

df1 = DataFrame(np.zeros((3,3), dtype='int32'), index=['one','two','three'])

df2 = df1 + 3

df3 = df2 * 2

# 원본 변경 없이 기본 연산 가능

df2 + df3								# df2.add(df3)
df2 - df3								# df2.sub(df3)
df2 * df3								# df2.mul(df3)
df2 / df3								# df2.div(df3)
df2.mul(df3,fill_value=0)				# missing value를 지정한 값으로 바꿔 연산
```

### 문자열이 있는 DataFrame 연산
{: .no_toc }

```python
mydata = {
          'name':['홍길동','박길동','김길동'], 
          'age':[22,33,44], 
          'dept':['컴공','국어','산업']
         }

df = DataFrame(mydata)					# dict → DataFrame 변환

df + 100								# 에러
df * 2									# 글자, 숫자 모두 곱셈 (Python 특징)
```




# 통계

- count()
- value_counts(): 동일한 value 셈
- sum()
- mean(): 평균
- std(): 표준편차
- var(): 분산
- min()
- max()
- cumsum(): 누적 합 (단계별)
- comprod(): 누적 곱 (단계별)
- describe() : 요약통계량

```python
from pandas import DataFrame

df=DataFrame({'거래처':['A매장','B매장','C매장','A매장','C매장','C매장', 'C매장', 'C매장'],
             '주문량':[100,25, 40, 55, 137, 70, 80, 89],
             '할인율':[0.1, 0.3, 0.03, 0.07, 0.15, 0.2, 0.2, 0.2]})

df.주문량.cumprod()
```



### 조건 검색
{: .no_toc }

```python
df[df['가격'] > 10000]
df[df['가격'].between(10000,30000)]		# between 값 포함

df.sort_values(by='판매건수')				# by= 빼도 된다.
df[['판매건수','가격']].sort_values('가격')
```



### apply
{: .no_toc }

DataFrame 내 데이터를 함수에 인자로 넣을 때 쓴다.

```python
import pandas as pd

df = pd.DataFrame( {'a':[10,20,30], 'b':[2, 4, 6]})

def my_fun1(x):
    return x**2

def my_fun2(x, n):
    return x**n

df.apply(my_fun1)							# df.apply(lambda x :my_fun1(x))
df.apply(lambda x, y=3 :my_fun2(x,y))	# 매개변수가 2개 이상인 경우

df.apply(my_fun3, axis=0)					# 열 방향
df.apply(my_fun3, axis=1)					# 행 방향 (매개변수 series로 function에 들어감)

def func(score):
    grade = ''
    if( score <= 10) : grade='C'
    elif(score <= 20) : grade='B'
    elif(score <= 30) : grade='A'
    return grade

df.a.apply(func)							# df['result'] = df['a'].apply(func)
```

2개 이상 열 조건 apply 함수

```python
import pandas as pd

customer = pd.read_csv('./data/customer.csv', index_col = "Name")

def vip(series):
    grade=''
    if(series['state']=='confirmed' and series['price'] >= 500): grade='vip'
    else: grade='non-vip'
    return grade

customer['grade'] = customer.apply(vip, axis=1)
customer
```



### 결측치 null 데이터 처리
{: .no_toc }

결측치는 NaN로 표시한다.

- isnull()
- notnull()
- Numpy.NaN : 원본 데이터 결측치로 변경
- 제거: dropna() : Series에 결측치 하나라도 있으면 Series 제거
- 대치: fillna() → 삭제 또는 평균값

```python
import pandas as pd
import numpy as np 

df = pd.DataFrame(np.ones((4,4)),index=['A','B','C','D'])

df.iloc[1][1] = np.NaN
df.loc['B',1] = np.nan
df.iloc[1:][2] = np.NaN						# = df.loc[1:,2] = np.NaN

df.cumsum()								# 연산을 해도 NaN 은 변하지 않음

df.isnull()									# 결측치 True
df.notnull()									# 결측치 False

df.isnull().sum()							# 칼럼별 null count
df.isnull().count()							# 칼럼별 null count 아님

df.fillna({1:'NULL',2:-100})
```



# 데이터 분석

데이터 → 필터링 → 정제 → 시작화 → 분석

