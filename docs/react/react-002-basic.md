---
layout: default
title: 기초
parent: React
nav_order: 1
last_modified_date: 2021-02-07
---
# React 리액트 기초
{: .no_toc }



<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# React는 무엇일까

React는 페이스북이 만든 프론트엔드 라이브러리입니다. React가 무엇인지 인터넷을 찾아보면 "성능" 측면에서 Virtual DOM을 통한 업데이트로 연산을 최소화하고, "코딩" 측면에선 Component로 분리해 작성과 관리가 쉽게 만들었다는 얘기를 볼 수 있습니다. MVC Framework가 아닌 User Interface(View)만 만듭니다.

사실 무엇인지, 왜 쓰는지 잘 와닿지 않습니다. 기존 [DOM](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model)과 어떤 차이가 있는지 가장 명확하게 표현한 그림을 보겠습니다. 출처는 [서비큐라 기술블로그](https://subicura.com/2016/06/20/server-side-rendering-with-react.html) 입니다. 기존 DOM은 메모리에 HTML을 띄우고 JavaScript를 읽어 내려가며 붙여가는 방식이라면, React의 Server Side Rendering은 이 과정을 서버에서 수행하고, Client Side에서 이벤트 속성만 추가한 후 사용자에게 보여주어 속도가 빠릅니다. 이 단계가 성능에 미치는 차이는 데이터가 많을 때, 사용자 인터렉션이 많을 때 더 크게 나타납니다.

![client-side-vs-server-side](https://user-images.githubusercontent.com/73984112/107143695-cf9c0500-6979-11eb-95ba-9974edf8b650.png)

이 글에선 리액트에 대한 탄생 배경에 대해 깊게 다루진 않겠습니다. 자세한 설명이 궁금하신 분은 아래 글을 참고하시기 바랍니다.

[https://velopert.com/3612](https://velopert.com/3612)

[https://velopert.com/3236](https://velopert.com/3236)

[https://velog.io/@youthfulhps/React-React를-사용하는-이유](https://velog.io/@youthfulhps/React-React를-사용하는-이유)

[https://ryublock.tistory.com/41](https://ryublock.tistory.com/41)

[https://www.youtube.com/watch?v=muc2ZF0QIO4&feature=youtu.be](https://www.youtube.com/watch?v=muc2ZF0QIO4&feature=youtu.be)



# React 설치

아래 설명은 Windows 10 Pro, VS Code (Visual Studio Code) 기준으로 작성했다.

1) Node.js 설치: [https://nodejs.org/ko/](https://nodejs.org/ko/)에서 최신 버전을 받아 설치한다.

2) React 프로젝트를 저장할 폴더를 만들고, VS Code 실행 후 아래 사진과 같이 폴더 경로를 열어준다.

![1](https://user-images.githubusercontent.com/73984112/107110079-58d30f00-6888-11eb-8f8a-c5ab04d13614.PNG)

3)  yarn 설치

VS Code에서 Ctrl + ` 를 누르면 하단에 터미널이 보인다. 

![2](https://user-images.githubusercontent.com/73984112/107110285-0a267480-688a-11eb-9264-560824174b0c.PNG)

여기에 아래 명령어 기입 후 엔터로 node.js 설치가 잘 되었는지 확인한다.

```bash
npm --version
```

버전이 나오면, 아래 명령어로 yarn을 설치한다.

```bash
npm install --global yarn
```

설치가 완료되면 yarn 버전도 확인한다.

```bash
yarn --version
```

4) React 프로젝트 설치

먼저 create-react-app 라이브러리를 설치한다. 리액트 세팅이 된 boilerplate를 만들기 쉽게 돕는 라이브러리다.

```bash
yarn global add create-react-app
create-react-app -V
```

그리고 리액트 프로젝트를 생성한다. 점(.)은 이 경로에 설치한다는 의미이다.

```bash
create-react-app .
```

완료되면 아래 사진처럼 리액트 프로젝트 파일이 나타난다.

![3](https://user-images.githubusercontent.com/73984112/107110553-127faf00-688c-11eb-81f7-d1a2844b1a83.PNG)

리액트 웹 페이지를 실행해보자.

```bash
yarn start
```

아래 화면이 나오면 성공이다.

![4](https://user-images.githubusercontent.com/73984112/107110593-6e4a3800-688c-11eb-8590-2cb07e575584.PNG)



# React 폴더 구조와 작동 원리



## index.js

React의 진입 파일이다. React로 실행한 웹 페이지는 index.html 파일이고, 이 파일의 "root" 태그에 React로 만든 ReactDOM.render() 안에 있는 요소를 붙인다.

```react
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

reportWebVitals();
```

reportWebVitals()은 create-react-app 라이브러리에 있는 것으로 분석에 사용한다. 여기서 다루진 않는다. (참고: [https://create-react-app.dev/docs/measuring-performance/](https://create-react-app.dev/docs/measuring-performance/))



## JSX(Javascript + xml)

Javascript를 확장한 문법으로 간단하게 말하면 HTML과 Javascript를 같이 쓸 수 있다. 기본적인 특징은 아래와 같으며, 자세한 내용은 [공식 문서](https://ko.reactjs.org/docs/introducing-jsx.html)를 참고한다.

- create-react-app이 해석
- return() 에 감싸져 있는 HTML 문법과 매우 유사한 코드
- data binding 쉬움
- 중괄호{ }로 html에 Javascript 문법 작성



# React Error 에러 모음

### npm start 시 "react-scripts 은(는) 내부 또는 외부 명령 실행할 수 있는 프로그램 또는 배치 파일이 아닙니다"

커맨드에 npm update 또는 yarn update 입력해 해결한다.

