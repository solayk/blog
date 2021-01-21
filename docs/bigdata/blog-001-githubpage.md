---
layout: default
title: 깃허브페이지
nav_order: 3
has_children: false
permalink: docs/githubpage
---





# VSCode, Typora, Jekyll, GitHub Pages를 이용한 Static Web 블로그 구축

VSCode

Typora

[Typora]: https://typora.io/

GitHub.io, GitHub Pages를 통해 Git Repository 내용을 변환기(Generator)를 이용해 저장된 데이터만 사이트로 보여주는 정적인 웹(Static Web)이다. 별도의 호스팅이 필요 없고, CSS로 디자인을 자유롭게 할 수 있다.

내가 쓴 글을 로컬에서 관리할 수 있다. 블로그는 호스팅 업체가 사라지면 별도 백업을 하지 않으면 사라질 수 있지만, GitHub Pages는 Local, Remote Git을 이용해 백업이 쉽다. 

마크다운으로 작성한 글을 다른 사이트에 쉽게 이식할 수 있다.



윈도우 10 리눅스 설치

WSL 에 CentOS 7 설치



rvm 설치

관리자 권한으로 terminal 실행 후 root 계정에서 아래 순서대로 명령어를 실행한다.

https://rvm.io/rvm/install

```bash
$ gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
$ curl -sSL https://get.rvm.io | bash -s stable --ruby
```

터미널 안내대로 source 명령어를 실행한다.

터미널을 재 기동하고 GitHub Page가 사용하는 ruby version 2.1.7 설치한다.





![image](https://user-images.githubusercontent.com/5028400/34940910-c77e4516-fa34-11e7-9ba6-57a841f56271.png)



localhost:4000에서는 메인, 서브 둘 다 css가 제대로 연결되어서 잘 나왔는데,

master 브랜치로 push 시키고 github pages에서 화면을 확인해보니,

서브페이지들(_posts폴더 내에 있는 소스)이 다 깨져서 나왔다. 

 

메인은 제대로 나오고, 서브페이지만 css가 연결 안된거 같았다 

이렇게도 해보고 저렇게도 해보고 다 해봤으나, 계속 안되는 듯해서 포기하려던 찰나, 해결이 됐다!!! 

(참고: https://stackoverflow.com/questions/25466166/stylesheets-not-working-for-jekyll-theme-freelancer-bootstrap)

 

1) _config.yml 파일에서 baseurl 변경

baseurl: "/repository명/ (Error. '/' not found 오류현상 생김)

baseurl: "" 



![img](https://blog.kakaocdn.net/dn/xfj8A/btqBxAyf2D4/NAWLBbgXkvBUTp8TyTLV20/img.png)



\* url도 지정해주도록 하자.

url을 따로 따로 지정안해주면, github pages에서 username.github.io/index.html이라고 뒤에 붙어야

메인이 뜨게 되는거 같다. url을 지정해주면 저 링크를 입력했을때 바로 메인페이지가 나온다 

 

 

2) default.html <head></head>태그 내에서 링크 지정

<link rel="stylesheet" href="{{ site.baseurl }}/all.css">

<script defer src="{{ site.baseurl }}/all.js">



default.html에서 css, js 각각 경로 앞에 '/'를 추가해주었다. github pages에서도 제대로 나오는거 확인 완료.





# 2. 블로그 꾸미기



jeckyll 과 bundle 설치

```BASH
$ bundle install
```

BASH 에 들어가 아래 명령어 실행 후

```BASH
$ bundle exec jeckyll serve
```

서버가 돌아가면 http://127.0.0.1:4000에 접속해 결과물을 확인한다.

변경사항 발생 후 F5를 누르면 반영 결과를 바로 확인할 수 있다.







sass

$hero-3: 배경화면



검색

Gibhub Blog는 Static Web으로 동적인 검색을 구현하려면 추가 라이브러리를 써야한다. 선택한 테마에서 

[lunr]: https://lunrjs.com/

 라는 라이브러리를 사용해 동일한 것을 적용했다. 아직 lunr 에서 한글 지원을 하지 않고 있어 아래와 같이 just-the-docks.js를 수정해 해결했다.

