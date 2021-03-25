---
layout: default
title: Python
parent: Algorithm
nav_order: 2
last_modified_date: 2021-03-23
---
# Python 알고리즘, 자료구조
{: .no_toc }


<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# 자주 쓰는 코드

### basic
{: .no_toc }

- 아스키코드: ord(문자), chr(숫자)
- for ~ else: for 문 break 여부, break 되지 않으면 else 실행

### list
{: .no_toc }

- 이중배열 sort

  data.sort(key=lambda x: x[1])
  
- 배열 데이터 타입 변환

  numbers = list(map(str,numbers))

  numbers = [str(x) for x in numbers]

- map

  - 파이썬 3.x: list(map(calc, ex)) 반드시 list 붙여야 리스트 형식 반환
  - 파이썬 2.x: list 없이도 리스트 형식 반환



### dict
{: .no_toc }

from collections import Counter

cnt = Counter([kind for name, kind in clothes])

from functools import reduce

answer = reduce(lambda x, y: x*(y+1), cnt.values(), 1) - 1

### comprehension
{: .no_toc }

dnf = [k for k, v in d.items() if v > 0]

### zip
{: .no_toc }

- 전후비교

  for p1, p2 in zip(phoneBook, phoneBook[1:]):

- 배열 결합 시 index 추가

  for e in zip(genres, plays, range(len(plays))):

  d[e[0]].append([e[1] , e[2]])

### set
{: .no_toc }

dict는 key에 대해 value가 map 되어 있으나, set은 원소가 집합에 속해 있는지 아닌지만 상관하는 자료 구조.




# 자료구조, 알고리즘 선택 

사용하려는 알고리즘에 따라 자료구조를 선택한다.



## 1. Hash 해시

문자열을 Key로 상수시간 O(1)에 읽고 쓸 수 있다.

![해시](https://user-images.githubusercontent.com/73984112/112093721-4fee9080-8bdd-11eb-8596-99ca4ceb3506.PNG)



Hash Collision(충돌): 만일 키가 같은 hash bucket 가리키면

![Hash Collision](https://user-images.githubusercontent.com/73984112/112093900-9348ff00-8bdd-11eb-9521-d52f71f12f56.png)

Python의 Dictionary는 Hash 구조라 사전의 원소들을 O(1) 상수시간에 접근 가능하다.



### 예제: 완주하지 못한 선수
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42576?language=python3)

["leo", "kiki", "eden"] 와 같이 이름 배열에서 일치 원소 검색. 최대 100,000개 이름이 가능하다면, 가능한 모든 조합의 수를 따지면 많아져 선형 배열 (linear array) 처리에 문제가 있다.

- 이름 대신 번호가 주어졌다면 → 선형 배열로 처리
- 이름이 주어졌다면 → 해시 Hash

```python
def solution(participant,completion):
    d = {}
    for x in participant:
        d[x] = d.get(x,0) + 1
    for x in completion:
        d[x] -= 1
    dnf = [k for k, v in d.items() if v > 0] # did not finish
    answer = dnf[0]
    return answer
```

### 복잡도
{: .no_toc }
3, 4행 순환문: dict 자료구조 사용해 O(1) 상수시간에 접근, participant 배열 길이에 비례

5, 6행 순환문: 역시 completion 배열 길이에 비례

7행: dict

결론: Linear Time O(n) 복잡도를 가지는 알고리즘

![완주(1)](https://user-images.githubusercontent.com/73984112/112097305-1c166980-8be3-11eb-83d1-145585705ccc.png)

### 정렬을 이용한다면
{: .no_toc }
```python
def solution(participant,completion):
    participant.sort()
    completion.sort()
    for i in range(len(completion)):
        if participant[i] != completion[i]:
            return participant[i]
    return participant[len(participant)-1]
```

배열 정렬을 쓴다면 sort 최적 알고리즘도 O(N Log N)에 비례하는 복잡도를 가진다.

![완주(2)](https://user-images.githubusercontent.com/73984112/112096399-89c19600-8be1-11eb-86d4-5bdd09bbdf59.png)



### 예제: 전화번호 목록
{: .no_toc }

[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42577)

```python
def solution(phone_book):
    answer = True
    d = {}
    phone_book.sort()
    for x in phone_book:
        d[x] = d.get(x,0)
    temp = ' '
    for k,v in d.items():
        if k.startswith(temp):
            answer = False
            break
        temp = k
    return answer
```

- 배열 sort: O(N Log N)
- 문자열비교: O(N Log N)
- 이중 반복문: O(N^2)



(참고) 배열 sorted 하고 전, 후 비교를 위해 zip

```python
def solution(phone_book):
    phoneBook = sorted(phone_book)
    for p1, p2 in zip(phoneBook, phoneBook[1:]):
        print(p1,p2)
        if p2.startswith(p1):
            return False
    return True
```



### 예제: 위장
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42578)

```python
def solution(clothes):
    answer = 1

    dic = dict()
    for cloth, Type in clothes:
        if (dic.get(Type) == None):
            dic[Type] = [cloth]
            continue
        dic[Type].append(cloth)

    for Type in dic:
        answer *= len(dic[Type]) + 1

    return answer-1
```



(참고) Dict 변환 시 개수만 필요하므로 Collections.Counter 클래스 사용, reduce 함수로 iterator & function 반복

```python
def solution(clothes):
    from collections import Counter
    from functools import reduce
    cnt = Counter([kind for name, kind in clothes])
    answer = reduce(lambda x, y: x*(y+1), cnt.values(), 1) - 1
    return answer
```



### 예제: 베스트앨범
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42579)

```python
def solution(genres, plays):
    sum = {}
    dat = {}
    idx = 0
    for g, p in zip(genres,plays):
        sum[g] = sum.get(g,0) + p
        if dat.get(g) is None: dat[g] = [[idx, g, p]]
        else: dat[g].append([idx, g, p])
        idx += 1

    result = []
    for itm in dat.values():
        itm.sort(key=lambda x: (x[2]))
        itm.reverse()
        result += itm[:2]

    for n in result:
        n.append(sum.get(n[1]))

    result.sort(key=lambda x: (x[3],x[2]))
    result.reverse()

    answer = [x[0] for x in result]

    return answer
```



(참고)

```python
def solution(genres, plays):
    answer = []
    d = {e:[] for e in set(genres)}
    for e in zip(genres, plays, range(len(plays))):
        d[e[0]].append([e[1] , e[2]])
    genreSort =sorted(list(d.keys()), key= lambda x: sum( map(lambda y: y[0],d[x])), reverse = True)
    for g in genreSort:
        temp = [e[1] for e in sorted(d[g],key= lambda x: (x[0], -x[1]), reverse = True)]
        answer += temp[:min(len(temp),2)]
    return answer
```



## 3. 힙 Heap

우선 순위 큐를 위해 만들어진 완전 이진 트리(complete binary tree) 자료 구조로 상수시간에 최대/최소 원소를 빠르게 찾을 수 있다. 완전 이진 트리이므로 배열에 넣어 구현할 수 있다.

<img src = "https://user-images.githubusercontent.com/73984112/112424890-87457480-8d78-11eb-839c-65821546d57d.png" width="400px">

- 힙 구성 heapify O(N log N)
- 삽입 insert O(log N)
- 삭제 remove O(log N)
- 정렬 heapsort O(N log N)
- 우선 순위 큐 priority queue

```python
import heapq

heapq.heapify(list) 배열 list로 부터 min heap 구성
m = heapq.heappop(list)  min heap list에서 최소값 삭제(반환)
heapq.heappush(list,x) min heap list에 원소 x 삽입
```





### 예제: 더 맵게 Scoville 지수
{: .no_toc }

[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42626)

수가 하나 남을때까지 섞고(N-1) 결과 재정렬(N) 한다면 전체 복잡도는 O(N^2)에 비례한다. 만일 최소, 최대 원소를 빠르게 꺼낼 수 있는 구조라면 힙 Heap을 쓴다.

(참고) 최소 힙 min heap 사용. 

```python
import heapq

def solution(scoville, K):
    answer = 0
    heapq.heapify(scoville)

    while True:
        min1 = heapq.heappop(scoville)
        if min1 >= K:
            break
        elif len(scoville) == 0:
            answer = -1
            break
        min2 = heapq.heappop(scoville)
        new_scoville = min1 + 2 * min2
        heapq.heappush(scoville,new_scoville)
        answer += 1

    return answer
```

만일 순서대로 정렬된 배열을 이용하면, 한 음식과 다른 한 음식 (가장 scoville 지수가 낮은 두 개) 을 섞어서 새로운 음식이 만들어졌을 때 (새로운 원소가 생겨서 이것을 배열에 삽입해야 하므로) 두 가지 중 하나를 해야 한다. (1) 새로운 음식까지 포함해서 배열을 만들고 다시 정렬하거나 (2) 이미 정렬된 배열이 있으므로 이것을 순서대로 (또는 이진 탐색하여) 알맞은 위치를 결정하여 새 scoville 지수를 그 자리에 삽입하거나. 두 경우 모두, 배열을 재정비하는 데에는 배열의 길이에 비례하는 시간이 적어도 소요된다. 따라서 이 경우는 정렬된 배열을 이용하는 것보다는 "동적인 특성이 보다 우수한" heap 을 이용하는 것이 유리하다.

"동적인 특성이 보다 우수한" 이라는 말은, heap 은 원소들의 삽입과 삭제 (삭제는 min 또는 max 값을 가지는 원소에 한하여) 연산이 효과적 (그리고 효율적) 으로 이루어질 수 있다는 뜻이다. 새로운 음식의 scoville 지수를 heap 에 삽입하는 데 걸리는 시간은, 원소의 수를 n 이라고 할 때 log(n) 에 비례한다. 선형으로 원소들을 늘어놓지 않고 트리의 형태 (2차원) 로 늘어놓음으로써 부가의 정보를 담을 수 있어서 이와 같이 우수한 성능 특성을 얻을 수 있다.

하지만 heap 은 정렬된 상태의 배열보다 항상 우수하진 않다. Heap 에서는 최소 (또는 최대, heap 을 어떻게 구성하느냐에 따라) 원소를 꺼내는 것은 매우 효과적으로 할 수 있지만, 원소들 사이에 total ordering 이 매겨져 있지 않다. ("느슨한 ordering" 이라고 말할 수 있다.) 

예를 들어, "주어진 n 개의 자연수들 중 k 번째로 작은 것을 찾아내시오."

여러 가지 방법을 생각해 볼 수 있습니다:
(1) 주어진 원소들 중 가장 작은 것을 선택하는 연산을 k 회 반복한다.
(2) n 개의 원소들로 이루어진 배열을 정렬하고 그 중에서 k 번째에 해당하는 것을 고른다.
(3) min heap 을 구성하고, 최소값을 꺼내는 동작을 k 번 반복한다.

어떤 방식이 가장 효율적일까? 또, 위와 같은 종류의 문제에서 heap 을 구성한다고 했을 때 이 구성에 걸리는 시간 (점근식에 따른 복잡도 외에 코드의 동작에서 어떤 일들이 이루어지는지를 곰곰 따져 본다면) 및 최소 원소를 꺼내는 동작(이 때에도 트리의 구조를 조정하면서 heap의 성질을 만족하기 위하여 뭔가 해야 할 일이 있지요)에 걸리는 시간, 그와 비교하여 배열에 담아 정렬하는 데 소요되는 시간을 비교해봐야 한다.

(추가) 최대 힙 max

9행 print(heap) 출력 결과는 [(-8, 8), (-7, 7), (-5, 5), (-1, 1), (-3, 3), (-4, 4)]로 힙에 튜플(tuple)를 원소로 추가하거나 삭제하면, 튜플 내에서 맨 앞에 있는 값을 기준으로 최소 힙이 구성되는 원리를 이용한다. 최대 힙을 만들려면 각 값에 대한 우선 순위를 구한 후, `(우선 순위, 값)` 구조의 튜플(tuple)을 힙에 추가하거나 삭제한다.

```python
import heapq

nums = [4, 1, 7, 3, 8, 5]
heap = []

for num in nums:
  heapq.heappush(heap, (-num, num))  # (우선 순위, 값)
print(heap)
while heap:
  print(heapq.heappop(heap)[1])  # index 1
```







## 4. 정렬

```python
numbers.sort(key= lambda x: (x*4)[:4], reverse=True)
temp = sorted(numbers,key= lambda x: (x*4)[:4],reverse=True)
```



### 예제: 가장 큰 수
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42746)

<img src = "https://user-images.githubusercontent.com/73984112/112261247-d030f680-8cae-11eb-934e-ef50001045fe.png" width="500px">

built-in function인 sort 함수는 문자열은 사전(=알파벳) 순서로 정렬하며 숫자는 오름차순으로 정렬한다.

```python
def solution(numbers):
    numbers = list(map(str,numbers))
    numbers.sort(key= lambda x: (x*3)[:4], reverse=True)
    if numbers[0] == '0':
        answer = '0'
    else:
        answer = ''.join(numbers)
    return answer
```

3행 sort N Log N에 비례, 2, 4~7행 N에 비례 → 정렬을 하지 않고, 해결이 어려우므로 3행이 알고리즘 복잡도 결정 O(N Log N)

(참고)

```python
import functools

def comparator(a,b):
    t1 = a+b
    t2 = b+a
    return (int(t1) > int(t2)) - (int(t1) < int(t2)) #  t1이 크면 1, t2가 크면 -1, 같으면 0

def solution(numbers):
    n = [str(x) for x in numbers]
    n = sorted(n, key=functools.cmp_to_key(comparator),reverse=True)
    answer = str(int(''.join(n)))
    return answer
```



### 예제: K번째 수
{: .no_toc }

[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42748)

```python
def solution(array, commands):
    answer = []
    for itm in commands:
        temp = sorted(array[(itm[0]-1):itm[1]])
        answer.append(temp[itm[2]-1])
    return answer
```



(참고)

```python
def solution(array, commands):
    return list(map(lambda x:sorted(array[x[0]-1:x[1]])[x[2]-1], commands))
```



### 예제: H-Index
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42747)

```python
from collections import Counter
import math

def solution(citations):

    n = len(citations)
    avg = math.ceil(sum(citations)/n)
    temp = Counter(citations)

    cnt = sum([v for k, v in temp.items() if k > avg])

    d = {}
    for i in range(avg + 1):
        d[i] = 0

    for k, v in temp.items():
        if k <= avg:
            d[k] = v
        else: continue

    temp = {k:v for k, v in temp.items() if k <= avg}

    for key, val in sorted(d.items(), reverse=True):
        cnt += val
        if key <= cnt and n - cnt <= cnt:
            answer = key
            break
        else:
            continue
    return answer
```



(참고)

```python
def solution(citations):
    citations = sorted(citations)
    l = len(citations)
    for i in range(l):
        if citations[i] >= l-i:
            return l-i
    return 0
```

(참고)

```python
def solution(citations):
    citations.sort(reverse=True)
    answer = max(map(min, enumerate(citations, start=1)))
    return answer
```





## 6. 탐욕법 Greedy Algorithm

- 알고리즘 각 단계에서 그 순간에 최적이라고 생각하는 것 선택
- 현재의 선택이 마지막 해답의 최적성을 해치지 않을 때
- *모든 케이스를 순환하지 않고 복잡도를 N개로 낮출 수 있다.



### 예제: 체육복
{: .no_toc }

[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42862)

확인 대상이 작다면 배열로 처리하는 O(n) 복잡도 알고리즘으로 해결할 수 있다.

하지만 확인 대상이 크다면, 표본만 정렬해 O(klogk) 확인 대상 중 확인이 필요한 대상만 해시를 적용해 상수 시간에 처리한다. 즉 n과 klogk 차이가 커지면 klogk 복잡도 알고리즘으로 방향을 잡는다. 주의할 것은 if 조건문의 순서가 중요하다.

1) 맨 앞, 뒤에 편의상 값 1 추가 후 반복 + 조건문으로 값 교체

반복문 n번 반복, 알고리즘 복잡도 n에 비례 → O(n)

```python
def solution(n, lost, reserve):
    u = [1] * (n+2)
    for x in reserve:
        u[x] += 1
    for l in lost:
        u[l] -= 1
    for i in range(1,n+1):
        if u[i-1] == 0 and u[i] == 2:
            u[i-1:i+1] = [1,1]
        elif u[i] == 2 and u[i+1] == 0:
            u[i:i+2] = [1,1]

    return len([x for x in u[1:-1] if x > 0 ])
```

2) 확인 대상 n이 클 경우

built-in function sorted는 K log K 복잡도에 비례, set은 배열의 길이에 비례한다.

```python
def solution(n, lost, reserve):
    s = set(lost) & set(reserve)
    l = set(lost) - s
    r = set(reserve) - s
    for x in sorted(r):
        if x - 1 in l:
            l.remove(x - 1)
        elif x + 1 in l:
            l.remove(x + 1)
    return n - len(l)
```

(참고)

```python
def solution(n, lost, reserve):
    _reserve = [r for r in reserve if r not in lost]
    _lost = [l for l in lost if l not in reserve]
    for r in _reserve:
        f = r - 1
        b = r + 1
        if f in _lost:
            _lost.remove(f)
        elif b in _lost:
            _lost.remove(b)
    return n - len(_lost)
```



### 예제: 조이스틱
{: .no_toc }

[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42860)

```python
def solution(name):
    na = list(name)
    group = [idx for idx,x in enumerate(name) if x != 'A']
    cnt = 0
    for p in group:
        if na[p] <= 'M':
            cnt += ord(na[p]) - ord('A')
        else:
            cnt += ord('Z') - ord(na[p]) + 1
    temp = []
    temp.append(len(na) - 1)
    for idx, p in enumerate(group):
        if idx == len(group) - 1:
            break
        temp.append(int(p)*2 + (len(na) - int(group[idx+1])))
    cnt += min(temp)
    return cnt
```

(참고) A가 아닌 문자 기준 좌/우 minimum distance를 아래와 같이 구할 수도 있다.

```python
	min_dist=99
	min_idx=0
	for it in indexes:
    	min_dist2=min(abs(it-current_idx),n-abs(it-current_idx))
        if min_dist2 < min_dist:
        	min_dist=min_dist2
            min_idx=it
    count+=min_dist
```



### 예제: 큰 수 만들기
{: .no_toc }

[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42883)

```python
def solution(number, k):
    result = []
    for i, n in enumerate(number):
        while len(result) > 0 and result[-1] < n and k > 0:
            result.pop()
            k-= 1
        if k == 0:
            result += list(number[i:])
            break
        result.append(n)
    result = result[:-k] if k > 0 else result
    return ''.join(result)
```

11행이 필요한 이유는 test case "999"와 같이 같은 수가 반복될 경우 k에 2를 넣어도 999를 return 하므로 while 조건에서 확인하지 않는 result[-1] < n에 대한 처리가 때문이다.

복잡도 O(N)

문자열 number의 길이를 N이라고 하면, 이 알고리즘의 실행 시간은 N에 비례. 알고리즘 실행 시간이 무엇에 비례하는지는 while 순환문 안의 몸체가 얼마나 여러 번 실행되느냐에 달려 있고, for 문이 한번 돌때 while 문이 여러번 돌 수 있지만 그 합은 N 보다 클 수 없다. 

```python
def solution(number, k):
    stack = [number[0]]
    for num in number[1:]:
        while len(stack) > 0 and stack[-1] < num and k > 0:
            k -= 1
            stack.pop()
        stack.append(num)
    if k != 0:
        stack = stack[:-k]
    return ''.join(stack)
```



## 7. 동적계획법 Dynamic Programming

재귀로 작은 부분 문제로 나누어 해를 조합하여 전체 답에 이르는 방식 (ex. Knapsack Problem)

문제의 성질에 따라 탐색해야 하는 범위를 효과적으로 줄일 때 필요하다.



### 예: 피보나치 수열
{: .no_toc }

(1) 재귀함수로 구현하면 복잡도가 지수 함수 형태 → 함수가 한번 호출되면 다시 두번 호출, O(2^N)

```python
def fibo(n):
    return fibo(n-1) + fibo(n-2) if n >= 2 else n

for n in range(1, 11):
    print(n, fibo(n))		# f(0) = 0, f(1) = 1
```

(2) f(2), f(3), ... 계산을 해나가며 이전에 구한 해를 그대로 쓴다. → 복잡도 선형 함수 형태, O(N)



### 예제: N으로 표현
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/42895)




<img src = "https://user-images.githubusercontent.com/73984112/112440889-2f196d00-8d8e-11eb-8a51-053692da0819.png" width="400px">

<img src = "https://user-images.githubusercontent.com/73984112/112440969-45272d80-8d8e-11eb-84de-2f94addce377.png" width="400px">

<img src = "https://user-images.githubusercontent.com/73984112/112441049-5708d080-8d8e-11eb-8971-a52be10b1cd6.png" width="500px">

<img src = "https://user-images.githubusercontent.com/73984112/112442066-c67ec000-8d8e-11eb-8eba-1bbcd58883da.png" width="500px">

<img src = "https://user-images.githubusercontent.com/73984112/112442271-02b22080-8d8f-11eb-8249-69ffb2db0c7c.png" width="500px">

중요한 것은 이미 계산한 결과를 해로 가지고 있으므로 괄호를 하고 연산한 것과 동일하며, 괄호가 없이 연산한 것도 포함되어 있도록 완전하게 나열했다.

<img src = "https://user-images.githubusercontent.com/73984112/112442960-ba473280-8d8f-11eb-88ce-e3d6911d9430.png" width="500px">

(참고) for 순환문이 4겹 이지만, 중복된 것은 제외하고 답을 찾아간다. 경우의 수는 폭발적으로 증가하지만, 불필요한 연산을 줄이고 끝날 수 있다.

```python
def solution(N, number):
    s = [set() for x in range(8)]
    for i, x in enumerate(s, start=1):
        x.add(int(str(N)*i))
    for i in range(1, len(s)):
        for j in range(i):
            for op1 in s[j]:
                for op2 in s[i-j-1]:
                    s[i].add(op1 + op2)
                    s[i].add(op1 - op2)
                    s[i].add(op1 * op2)
                    if op2 != 0: s[i].add(op1 // op2)
        if number in s[i]:
            answer = i + 1
            break
    else:
        answer = -1
    return answer
```

만일 N = 1, number = 1 케이스를 통과하려면 answer는 1이지만, 위 알고리즘은 2가 나온다.

이를 방지하려면 5행 뒤에 아래 구문을 추가해야 하지만, 연산이나 조합을 전혀 하지 않는 케이스라 논란의 여지가 있다.

```python
if number in s[i-1]:
    return i
```



만일 위 2행에서 s = [set()]*8 로 생성하면 set()으로 한 객체 생성 후 8번 복사한 것이므로 아래와 같이 실행하면 전체 요소에 동일하게 반영된다.

```python
s = [set()] * 8
print(s)
# [set([]), set([]), set([]), set([]), set([]), set([]), set([]), set([])]
s[0].add(1)
print(s)
# [set([1]), set([1]), set([1]), set([1]), set([1]), set([1]), set([1]), set([1])]
```



(참고)

```python
def solution(N, number):
    S = [{N}]
    for i in range(2, 9):
        lst = [int(str(N)*i)]
        for X_i in range(0, int(i / 2)):
            for x in S[X_i]:
                for y in S[i - X_i - 2]:
                    lst.append(x + y)
                    lst.append(x - y)
                    lst.append(y - x)
                    lst.append(x * y)
                    if x != 0: lst.append(y // x)
                    if y != 0: lst.append(x // y)
        if number in set(lst):
            return i
        S.append(lst)
    return -1def solution(N, number):
    S = [{N}]
    for i in range(2, 9):
        lst = [int(str(N)*i)]
        for X_i in range(0, int(i / 2)):
            for x in S[X_i]:
                for y in S[i - X_i - 2]:
                    lst.append(x + y)
                    lst.append(x - y)
                    lst.append(y - x)
                    lst.append(x * y)
                    if x != 0: lst.append(y // x)
                    if y != 0: lst.append(x // y)
        if number in set(lst):
            return i
        S.append(lst)
    return -1
```



## 8. 깊이/너비 우선 탐색 DFS/BFS

- 그래프(graphs)
  - 정점(vertex, node)과 간선(edge, link)
  - 유향(directed) 그래프와 무향(undirected) 그래프
- 스택(stack), 큐(queue)



### (1) 깊이 우선 탐색(DFS; Depth-First Search)

한 정점에서 인접한 모든(아직 방문하지 않은) 정점을 방문하되, 각 인접 정점을 기준으로 깊이 우선 탐색을 끝낸 후 다음 정점으로 진행

<img src = "https://user-images.githubusercontent.com/73984112/112478973-df4f9b80-8db7-11eb-86da-56abf2c99c75.png" width="300px">

스택으로 어느 정점에서 DFS 하고 있는지 기억하고 되돌아감



### (2) 너비 우선 탐색(BFS; Breadth-First Search)

한 정점에서 인접한 모든(아직 방문하지 않은) 정점을 방문하고, 방문한 각 인접 정점을 기준으로(방문한 순서에 따라) 또 다시 너비 우선 탐색 진행

<img src = "https://user-images.githubusercontent.com/73984112/112479431-613fc480-8db8-11eb-857f-e53c85fdced5.png" width="300px">

큐로 어느 정점에서 HFS 해야 하는지 기억하고 되돌아감





### 예: 여행경로
{: .no_toc }
[[링크]](https://programmers.co.kr/learn/courses/30/lessons/43164)

재귀적인 성질을 가진 한번에 그리기 → 깊이 우선 탐색 DFS = 스택

*실패하는 경우 없다고 언급

** 스택에서 꺼낸 것의 역순이 우리가 원하는 순서



(참고) 정렬(sort)해 stack에서 마지막 데이터만 건드리므로 5행 sort가 전체 복잡도를 결정한다. → O(N log N)

```python
def solution(tickets):
    routes = {}
    for t in tickets:
        routes[t[0]] = routes.get(t[0],[]) + [t[1]]
    for r in routes:
        routes[r].sort(reverse=True)
    stack = ["ICN"]
    path = []
    while len(stack) > 0:
        top = stack[-1]
        if top not in routes or len(routes[top]) == 0:
            path.append(stack.pop())
        else:
            stack.append(routes[top][-1])
            routes[top] = routes[top][:-1]  # pop also available
    return path[::-1]
```

