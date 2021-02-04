---
layout: default
title: 주피터노트북
parent: 파이썬
nav_order: 4
last_modified_date: 2021-01-28
---
# 주피터노트북 Jupyter Notebook
{: .no_toc }

주피터 노트북은 코드 실행 뿐만 아니라 문서화가 쉽다.


<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>




### 작업 폴더 설정

실행파일 우 클릭 > 대상(T): 칸 변경

(변경 전) C:\ProgramData\Anaconda3\python.exe C:\ProgramData\Anaconda3\cwp.py C:\ProgramData\Anaconda3 C:\ProgramData\Anaconda3\python.exe C:\ProgramData\Anaconda3\Scripts\jupyter-notebook-script.py "%USERPROFILE%/"

(변경 후) C:\ProgramData\Anaconda3\python.exe C:\ProgramData\Anaconda3\cwp.py C:\ProgramData\Anaconda3 C:\ProgramData\Anaconda3\python.exe C:\ProgramData\Anaconda3\Scripts\jupyter-notebook-script.py "D:\python"



### 단축키

*esc ⇒ m : markdown

esc ⇒  y : code 

Shift + Enter ⇒ 실행





## Markdown 글 작성 기호

별표 하나 * : 번호 없는 목록

별표 감싸기 * (띄어쓰기 X) * : *기울이기 효과*

별표 두개 사이 감싸기 * * (띄어쓰기 X) * * : **굵게**

별표 세개 * * * (띄어쓰기 X)  * * *  : ***기울이기 + 굵게***

인용문 : 첫 문자 '>' 이후 내용

```
<div align='center'> Hello World! </div>
<img src='dataset/puppy.jpg'>
[링크걸기](https://www.daum.net)
[![다음](https://t1.daumcdn.net/daumtop_chanel/op/20200723055344399.png)](https://www.daum.net)
```
### 실행결과

<div align='center'> Hello World! </div>

저장한 사진(puppy.jpg)이 뜬다

[링크걸기](https://www.daum.net)

[<img src="https://t1.daumcdn.net/daumtop_chanel/op/20200723055344399.png" alt="다음" style="zoom: 50%;" />](https://www.daum.net)

###  파일 읽고 바로 띄우기

```
import pandas

p1 = pandas.read_csv('dataset/subway_data1.csv',encoding='utf-8')
p1
```

![파일 읽고 바로 띄우기](https://user-images.githubusercontent.com/73984112/106087387-3a259780-6167-11eb-9c41-dfee3824fca9.PNG)

### 표 만들기

```
<table border = '1'>
    <tr><td>인덱스</td><td>값</td></tr>
    <tr><td>1</td><td>데이타</td></tr>
<table>
    
## <font color = 'green'> 서울의 지하철 </font>

<img src = 'dataset/map_subway.jpg'>
```

