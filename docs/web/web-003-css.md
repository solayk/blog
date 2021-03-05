---
layout: default
title: CSS
parent: Web
nav_order: 3
last_modified_date: 2021-03-05
---
# CSS
{: .no_toc }



<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# vh, vw, % 차이

폭과 높이(width, height) 설정 시 vw, vh는 view port 기준이며 %는 창 기준이다. 따라서 100vw, 100vh를 쓰면 보이지 않는 영역이 있으면 스크롤 바가 생긴다. 화면에 스크롤이 발생하면 %로 포지셔닝한 컴포넌트가 흔들리는 문제가 발생할 수 있다.

