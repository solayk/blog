---
layout: default
title: 파이썬 기초
parent: 파이썬
nav_order: 1
last_modified_date: 2021-01-28
---
# 파이썬 Python 기초
{: .no_toc }

파이썬에 대한 기초 지식, 함수를 한 페이지에 모두 담았습니다. Ctrl + F 를 눌러 원하는 함수 이름, 이론 등을 검색하시면 빠르게 찾을 수 있습니다. 아래 목차는 삼각형을 누르면 사라집니다.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>






# 넘파이 numpy

행렬, 선형대수, 통계 패키지. 

머신러닝의 이론적 백그라운드는 선형대수와 통계로 이루어져 있으며, 사이킷런 같은 머신러닝 패키지가 넘파이 기반으로 되어 있다. 머신러닝 알고리즘이나 사이파이와 같은 과학, 통계 지원용 패키지를 직접 만드는 개발이 아니라면 넘파이를 상세하기 알 필요는 없다지만, 넘파이를 이해하는 것이 파이썬 기반의 데이타분석과 머신러닝에 중요하다. 넘파이가 데이타 핸들링에 효율적으로 쉽고 편하고 할 수 없다.   그러나 데이타 핸들링에 주로 사용하는 판다스도 많은 부분이 넘파이를 기반으로 만들어져 있다.

아래 모든 예제는 import numpy as np를 실행한 상태로 가정한다.

## numpy 자료형(데이터형)

### dtype, astype

dtype으로 자료형 출력하고, astype으로 자료형 변경한다.

```python
arr = [[1,2,3],[9,100,7]]
print(arr.dtype)						# int32

arr2 = arr.astype(np.float64)
print(arr2)								# [[1., 2., 3. ],[9., 100., 7. ]]

arr = [1.1,2.2,3.3]
arr3 = np.array(arr,dtype='int32')
print(arr3)								# [1, 2, 3]

list = [9,2.2,'python']
arr = np.array(list)
print(type(arr))							# <class 'numpy.ndarray'>
print(arr.dtype)						# <U32
print(arr)								# ['9' '2.2' 'python']
```

ndarray에 다양한 자료형이 있을 경우 문자 > 실수 > 정수 순으로 전체 자료형을 결정한다.

<U는 Unicode를 의미하며, 숫자는 11, 32는 비트를 의미한다.

## numpy. array() = ndarray

list를 넘파이 기반 데이터 타입인 ndarray로 바꾸며, list 와 다르다.

### shape(), ndim()

shape은 타입과 형태, ndim은 차원을 출력한다.

```python
list_1 = [1,2,3]
list_2 = [9,8,7]

arr = np.array([list_1,list_2])
print(type(list_1))						# list
print(type(arr))							# numpy.ndarray

arr[1,1] = 100
print(arr)								# array([[1,2,3],[9,100,7]])

arr1 = np.array([1,2,3])
arr2 = np.array([[1,2,3]])

# 타입, 형태 출력
print(arr1.shape)						# (3,)
print(arr2.shape)						# (1, 3)

# 차원 출력
print(arr1.ndim)						# 1
print(arr2.ndim)						# 2
```

### arange(), zeros(), ones()

ndarray를 편하게 만들 수 있다.

```python
a = np.arange(10)
print(a)								# [0 1 2 3 4 5 6 7 8 9]

b = np.arange(10,20)
print(b)								# [10 11 12 13 14 15 16 17 18 19]

c = np.arange(100,120,3)
print(c)									# [100 103 106 109 112 115 118]

d = np.zeros((5,5), dtype='float')
print(d)								# 5 x 5 0.(실수) ndarray

e = np.ones((3,3), dtype='int32')
print(e)								# 3 x 3 1(정수) ndarray
```

### reshape()

ndarray 차원, 크기를 변경한다.

```python
arr = np.arange(20)
print(arr)								# [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]
print(arr.shape)						# (20,)

arr1 = arr.reshape(4,5)
print(arr1)								# arr이 4(행) x 5(열)로 변경
print(arr1.shape)						# (4,5)

arr2 = arr.reshape(5,-1)					# reshape에 하나만 -1 쓰면 알아서 계산
print(arr1)								# arr이 5(행) x 4(열)로 변경
print(arr1.shape)						# (5,4)
```

### ndarray indexing 인덱싱

```python
# 1부터 9까지 ndarray를 만들고 3행 3열 2차원 구조로 변경 후 두번째 행의 세번째 열의 값 추출
arr = np.arange(1,10)
arr2 = arr.reshape(3,3)
print(arr)								# [1 2 3 4 5 6 7 8 9]
print(arr2)
print(arr2[1][2])						# 6

arr = np.arange(1,10)
arr2 = arr.reshape(3,3)
print(arr2)								# [[1 2 3], [4 5 6], [7 8 9]]
    
## 행렬에서 1, 2, 4, 5 추출
print(arr2[:2,:2])

## 행렬에서 4, 5, 6, 7, 8, 9 추출
print(arr2[1:3])

## 행렬에서 2, 3, 5, 6 추출
print(arr2[:2,1:3])

## 행렬에서 1, 4 추출
print(arr2[:2,:1])
```

## numpy 연산

- abx / fabs : 절대값
- sqrt : 제곱근
- square : 제곱
- exp : 지수
- ceil / floor : 소수자릿수 올림/내림
- rint :소수자릿수 반올림
- modf : 원소의 몫과 나머지를 각 배열로 변환
- lsnan : 각 원소가 Nan인지 아닌지 불린 배열로 리턴
- add / subtract : 두 배열의 같은 위치의 요소끼리 덧셈/뺄셈
- multiply : 배열 원소끼리 곱셉
- divide : 첫번째 배열의 원소에서 두번째 배열 원소로 나눔
- power : 첫번째 배열의 원소에 두번째 배열의 원소만큼 제곱
- maximum : 비교하는 두 원소 중 큰 값
- mod : 첫번째 배열의 원소에서 두번째 배열 원소로 나눈 나머지

```python
arr1 = np.array([[1,2,3],[4,5,6]])
arr2 = np.array([[1,1,1],[2,2,2]])

print(arr1)								
print(arr2)								
print(arr1 + arr2)						# [[2, 3, 4], [6, 7, 8]]
print(arr1 - arr2)						# [[0, 1, 2], [2, 3, 4]] 
print(arr1 * arr2)						# [[1, 2, 3], [8, 10, 12]] 
print(arr1 / arr2)						# [[1., 2., 3.], [2., 2.5, 3.]]
```

곱하기, 나누기는 행렬의 곱이나 나눔이 아니라 요소의 곱, 나눔이다.

### 행렬 곱 dot(), matmul()

```python
A = np.array(
    [[1, 2, 3],
    [4, 5, 6]])
B = np.array(
    [[7,8],
     [9,10],
     [11, 12]])

print(np.dot(A,B))						# [[58, 64], [139, 154]]
```



dot()과 matmul차이는 

```python
a = np.ones([9, 5, 7, 4])
c = np.ones([9, 5, 4, 3])
print(np.dot(a, c).shape)				# (9, 5, 7, 9, 5, 3)
print(np.matmul(a, c).shape)			# (9, 5, 7, 3)
```





### 전치행렬 = 행, 열 위치 바꿈

```python
A = np.array(
    [[1, 2, 3],
    [4, 5, 6]])
print(np.transpose(A))					# [[1, 4], [2, 5], [3, 6]]
```

- sum : 배열 원소들의 합
- mean : 산술 평균
- std / var : 표준편차 / 분산
- min / max : 최소 값 / 최대 값
- cumsum : 각 원소의 누적값
- cumprod : 각 원소의 누적곱

```python
ar = np.array([1,3,5,7])
print(np.mean(ar))						# 4.0
print(np.sum(ar))						# 16
print(np.max(ar))						# 7
print(np.sum(np.square(ar)))			# 84
```

### 행렬 연산, unique()

```python
arr = np.array([[1,2,3],[4,5,6]])
print(np.sum(arr))
print(np.sum(arr[1]))
print(np.sum(arr,axis=0))				# [5, 7, 9] : axis=0 행방향 합
print(np.sum(arr,axis=1))				# [6, 15] : axis=1 열방향 합

arr2 = np.array([[12, 7, 22], [20,33, 44], [4,5,6]])
arr2[arr2>20]
print((arr2>20).sum())					# 3 : true 3개
print(arr2[arr2>20].sum())				# 99 : true 인 요소 합

region = np.array(['가산','서울','서울','판교','가산'])
print(np.unique(region))				# ['가산', '서울', '판교'] : 중복제거
```

### 행렬 정렬, sort(), array.sort(), argsort()

```python
arr = np.array([9,3,6,1,7,2,4,8,5,6])
np.sort(arr)[::-1]						# [9, 8, 7, 6, 6, 5, 4, 3, 2, 1]

arr2 = np.array([[9,3,6],[1,7,2],[4,8,5]])
print(np.sort(arr2,axis=0))				# [[1, 3, 2], [4, 7, 5], [9, 8, 6]]
print(np.sort(arr2,axis=1))				# [[3, 6, 9], [1, 2, 7], [4, 5, 8]]
print(np.sort(arr2,axis=None))			# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

sort() : 넘파이에서 sort() 호출

array. sort() : 행렬자체에서 sort() 호출, return 값 없이 정렬만 해 result로 받아도 None이다.

```python
origin = np.array([3,1,9,7])

result = np.sort(origin)					# sort()
print(origin)							# [3, 1, 9, 7]
print(result)							# [1, 3, 7, 9]

result = origin.sort()					# array.sort()
print(origin)							# [1, 3, 7, 9]
print(result)							# None
```

argsort() : 행렬 정렬 시 순서를 배열 인덱스로 반환. 다른 배열과 연계해 인덱스를 활용할 수 있다.

```python
arr = np.array([3, 8, 2, 5])
print(np.argsort(arr))					# [2, 0, 3, 1]

score = np.array([66,99,55,88,77])
name = np.array(['홍','김','박','최','이'])
idx = np.argsort(score)
idx

for i in idx:
    print(name[i],'-',score[i])
```

### 오름차순, 내림차순

```python
origin = np.array([3,1,7,9])

result = np.sort(origin)
print(result[::-1])						# [9, 7, 3, 1]

origin.sort()
print(origin[::-1])						# [9, 7, 3, 1]
```

### 2차원 이상 행렬 정렬

```python
arr = np.array([[9, 6, 3],[5, 11, 2]])

result = np.sort(arr, axis=0)
print(result)							# [[5, 6, 2], [9, 11, 3]]
result = np.sort(arr, axis=1)
print(result)							# [[3, 6, 9], [2, 5, 11]]
```



## numpy 난수

파이썬 내장 random 모듈보다 더 빠르게 많은 난수 표본 데이타를 생성한다. 적합한 난수는 시뮬레이션 결과에 큰 영향을 미친다.

### random(), normal(), randn(), rand(), randint

np.random.random([n, m]) 0.0에서 1.0 사이의 난수 추출

np.random.normal(size=( n, m)) 정규분포에서 표준추출

np.random.randn(n,m) 표준 정규 분포

np.random.rand(n,m) 0부터 1사이의 균일 분포

np.random.randint(n, m) 균일 분포의 정수 난수

shuffle 리스트나 배열의 순서를 뒤섞음

```python
arr = np.random.random([2,3])
arr = np.random.normal(size=(2,3))
arr = np.random.randn(2,3)
arr = np.random.rand(2,3)
arr = np.random.randint(2,3)
```

matplotlib pyplot으로 난수 분포를 확인할 수도 있다.

```python
import matplotlib.pyplot as plt

a = np.random.randn(100)
print(a)
```

![pyplot](https://user-images.githubusercontent.com/73984112/106227502-a1594f80-622c-11eb-82f5-9a540fdc22de.png)

주피터노트북에서 plot()이 실행이 안되면 앞에 %matplotlib inline 명령어를 추가하고 실행한다. %는 주피터노트북에서 매직명령어이다.



# 판다스 Pandas



## 시리즈 Series

데이터프레임 DataFrame의 한 컬럼 데이터 세트로 컬럼이 하나인 데이타 구조체이고, DataFrame은 컬럼이 여러개인 데이타 구조체이다. DataFrame을 RDBMS의 테이블과 유사하다면, Series는 테이블의 한 컬럼과 유사다하고 볼 수 있다. 데이터프레임에 대한 설명은 Ctrl + F 로 검색하세요.

Series는 리스트, 튜플의 index와 dict 의 key 와 유사하고, 같은 값의 index 가 가능( 즉, 중복 가능 )하다.

- List, Tuple index : 일련번호로 변경불가 / 순서 개념이 있다.
- Series Index : 중복 가능 / 순서 개념이 있다.
- Dict key : 중복 불가 / 순서 개념 없다.

아래 Pandas 예제는 import pandas as pd를 실행한 상태로 가정한다.

```python
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



