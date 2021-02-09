---
layout: default
title: 파이썬 기초
parent: Python
nav_order: 1
last_modified_date: 2021-01-28
---
# 파이썬 Python 기초
{: .no_toc }

파이썬에 대한 기초 지식, 함수를 한 페이지에 모두 담았습니다. Ctrl + F 를 눌러 원하는 함수 이름, 이론 등을 검색하시면 빠르게 찾을 수 있습니다. 아래 목차는 삼각형을 누르면 숨겨집니다.

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



## numpy 자료형(데이터형)

Numpy는 배열 안에 동일한 데이터 타입만 저장한다. 저장할때 필요한 저장 공간을 일정하게 유지해 원하는 위치에 바로 접근해 데이터를 읽어 계산을 쉽고, 빠르게 처리한다.



### dtype, astype
{: .no_toc }
dtype으로 자료형 출력하고, astype으로 자료형 변경한다.

```python
import numpy as np

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

```python
import numpy as np

ar = np.array([1, 2, 3])                 
print('자료형 type:', ar.dtype)  			# int32
ar = np.array([1, 2.9, 3])                 
print('자료형 type:', ar.dtype)  			# float64
```

문자가 있을 경우 Unicode로 변환한다.

```python
import numpy as np

ar = np.array(['1', '2', '3','4'])   
print('자료형 type:', ar.dtype)			# <U1

ar = np.array(['가', '나', '다','라'])   
print('자료형 type:', ar.dtype)			# <U1

ar = np.array(['hello', 'hi', 'hola','bye'])   
print('자료형 type:', ar.dtype)			# <U5 (제일 긴 문자 기준)

ar = np.array(['iloveyou', 'hi', 'hola','bye'])   
print('자료형 type:', ar.dtype)			# <U8

ar = np.array(['abcdefghijklmnopqrstuvwxyz', 'hi', 'hola','bye'])   
print('자료형 type:', ar.dtype)			# <U26

ar = np.array([1, 2, 3,'4'])   
print('자료형 type:', ar.dtype)			# <U11

ar = np.array([11111111111, 2, 3,'4'])   
print('자료형 type:', ar.dtype)			# <U21

ar = np.array([1, 2.9, 3,'4'])   
print('자료형 type:', ar.dtype)			# <U32 (실수가 있을 경우 32비트)

ar = np.array([1, 222222222222222222222222222222222.999999999999999999999999, 3,'4'])   
print('자료형 type:', ar.dtype)			# <U32 (출력할때 2.2222222222222223e+43 이런식으로 표현)
```

### 참고) 리틀 인디언, 빅 인디언
{: .no_toc }

리틀 vs 빅 엔디언 컴퓨터는 데이터를 메모리나 디스크에 저장할 때 바이트 단위로 나누어 저장한다. 저장하는 데이터는 대게 4바이트나 8바이트로 구성되므로,연속되는 바이트를 어떤 순서로 저장해하는데 이를 바이트 저장 순서(byte order)라고 한다. 두가지가 있다.

- 빅 엔디언(big endian) : 높은 바이트부터 낮은 바이트로 저장. 예를 들어 0x12345678인 경우 0x12, 0x34, 0x56, 0x78 순서로 저장
- 리틀 엔디안(little endian) : 낮은 바이트부터 낮은 바이트로 저장. 예를 들어 0x12345678인 경우 0x78, 0x56, , 0x34, 0x12 순서로 저장

인텔 계열 CPU(인텔이나 AMD)는 리틀 엔디안을 사용한다. 다만 네트워크를 통해 데이터를 전송할 때는 빅엔디안을 사용한다.




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




