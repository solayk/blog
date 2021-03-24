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

### list
{: .no_toc }

- 이중배열 sort

  data.sort(key=lambda x: x[1])

### dict
{: .no_toc }

from collections import Counter

cnt = Counter([kind for name, kind in clothes])er

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



## 예제: 완주하지 못한 선수
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

배열 정렬을 쓴다면 sort 최적 알고리즘도 O(nlogn)에 비례하는 복잡도를 가진다.

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

- 배열 sort: O(n log n)
- 문자열 비교: O(nlogn)
- 이중 반복문: O(n^2)



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



## 2. 탐욕법 Greedy Algorithm

- 알고리즘 각 단계에서 그 순간에 최적이라고 생각하는 것 선택

- 현재의 선택이 마지막 해답의 최적성을 해치지 않을 때



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

built-in function sorted는 klogk 복잡도에 비례. set은 배열의 길이에 비례한다.

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










