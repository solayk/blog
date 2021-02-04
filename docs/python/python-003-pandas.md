---
layout: default
title: 판다스 분석
parent: 파이썬
nav_order: 3
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

### DataFrame 열, 행 추출

df.컬럼명 또는 df['컬럼명'] : 열(컬럼) 추출

df.loc[ ] : 인덱스를 지정하지 않으면 인덱스 순서로, 인덱스 지정하면 인덱스명으로 행 추출

df.iloc[ ] : 인덱스 만으로 행 추출

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
```

### DataFrame 컬럼, 행 추가

```python
df['gender'] = ['여자','남자','여자']		# 컬럼 추가
df.loc[9] = ['최길동',55,'기계','남자']		# 행 추가
```

### DataFrame 인덱스 순서 변경

```python
df2 = df.reindex(index=[2,3,0,1,5])		# 없는 인덱스를 넣을 경우 해당 행 NaN 표기
```

### DataFrame 컬럼 연산

```python
df['age+10']=df['age']+10
```

### DataFrame 값 변경

아래 2가지 방법 외 여러가지가 있으나, 실용적인 방법은 아래 명령어이다.

```python
df.loc[9,'dept'] = '인문'
df.dept.iloc[9] = '인문'
```

![DataFrame](https://user-images.githubusercontent.com/73984112/106237656-96a9b500-6242-11eb-949b-8caaa82590b3.PNG)

### DataFrame 필터링

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

info() : DataFrame 정보 보기

describe() : DataFrame 기본통계값 보기

```python
df.info()
df.describe()
```

### DataFrame 통계

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



# 판다스 데이터 연산

## Series 연산

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

```python
from pandas import DataFrame
import numpy as np

df1 = DataFrame(np.zeros((3,3), dtype='int32'), index=['one','two','three'])

df2 = df1 + 3

df3 = df2 * 2

# 원본 변경 없이 기본 연산 가능
```

