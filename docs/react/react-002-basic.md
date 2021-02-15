---
layout: default
title: 기초
parent: React
nav_order: 1
last_modified_date: 2021-02-07
---
# React 리액트 기초
{: .no_toc }

React는 페이스북이 만든 프론트엔드 라이브러리입니다. React에 대한 기본적인 설명과 작성 방법을 한페이지에 정리했습니다. 원하는 내용을 Ctrl + F 로 검색해보세요. 편의상 경어체로 작성하지 않았으니 양해 부탁 드립니다.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# React는 무엇일까

React가 무엇인지 인터넷을 찾아보면 "성능" 측면에서 Virtual DOM을 통한 업데이트로 연산을 최소화하고, "코딩" 측면에선 Component로 분리해 작성과 관리가 쉽게 만들었다는 얘기를 볼 수 있습니다. MVC Framework가 아닌 User Interface(View)만 만듭니다.

사실 무엇인지, 왜 쓰는지 잘 와닿지 않습니다. 기존 [DOM](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model)과 어떤 차이가 있는지 가장 명확하게 표현한 그림을 보겠습니다. 출처는 [서비큐라 기술블로그](https://subicura.com/2016/06/20/server-side-rendering-with-react.html) 입니다. 기존 DOM은 메모리에 HTML을 띄우고 JavaScript를 읽어 내려가며 붙여가는 방식이라면, React의 Server Side Rendering은 이 과정을 서버에서 수행하고, Client Side에서 이벤트 속성만 추가한 후 사용자에게 보여주어 속도가 빠릅니다. 이 단계가 성능에 미치는 차이는 데이터가 많을 때, 사용자 인터렉션이 많을 때 더 크게 나타납니다.

![client-side-vs-server-side](https://user-images.githubusercontent.com/73984112/107143695-cf9c0500-6979-11eb-95ba-9974edf8b650.png)

이 글에선 리액트에 대한 탄생 배경에 대해 깊게 다루진 않겠습니다. 자세한 설명이 궁금하신 분은 아래 글을 참고하시기 바랍니다.

[VELOPERT 리액트는 무엇인가](https://velopert.com/3612)

[VELOPERT Virtual DOM](https://velopert.com/3236)

[Youthfulhps React를 사용하는 이유](https://velog.io/@youthfulhps/React-React를-사용하는-이유)

[DOM과 Virtual DOM](https://ryublock.tistory.com/41)

[영상 React and the Virtual DOM](https://www.youtube.com/watch?v=muc2ZF0QIO4&feature=youtu.be)



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



## import

React는 외부라이브러리로 Component를 제공하는 경우가 많다. 외부 라이브러리의 Component 또는 별도 파일로 만든 Component를 쓰려면 import 해야만 한다.

1) 기본적으로 React를 쓰려면 아래 코드가 반드시 있어야 한다.

```react
import React from 'react';
```

2) 외부 라이브러리를 import 하되 수정해서 쓰려면 import 시 { } 안에 import 대상을 쓴다.

```react
import { Table } from 'react-bootstrap';
```



## Lifecycle Hook

Component의 Mount 여부에 따라 실행

```react
class Detail extends React.Component {
    componentDidMount(){        
    }
    componentWillUnmount(){
    }
}
```

componentDidMount : Detail이란 Component가 Mount 할때 실행

componentWillUnmount : Detail이란 Component가 Unmount 할때 실행



## UseEffect(() => {}) Hook

Lifecycle Hook과 동일한 역할. UseEffect 자체를 여러번 쓸 수 있다.

```react
useEffect(()=>{
    setTimeout(()=>{alert('ok')},2000)		// Component Mount 할때 실행
    return function test(){}					// Component Unmount 할때 return 실행
});
```

단, 다시 Rendering 할때마다 useEffect가 실행되므로 useEffect에 조건을 지정해 한번만 실행하도록 조건 지정 처리가 필요한 상황이 있을 수 있다. useEffect(()=> {},[조건]);

### 예시: useEffect로 Component의 Mount 2초 후 div 숨김
{: .no_toc }

만일 useEffect에 [alert] 조건이 빠지면 inputData 값을 입력할때마다 useEffect가 실행된다.

```react
function Detail(props) {
	
    let [alert, alertChange] = useState(true);
    let [inputData,inputDataChange] = useState('');
    
    useEffect(() => {
        let timer = setTimeout(() => { alertChange(false) }, 2000)
        return () => {clearTimeout(timer)}
    },[alert]);

    return (
	        { inputData }
            <input onChange={(e)=>{inputDataChange(e.target.value)}}></input>
            {
                alert
                ? (<div className="my-alert">
                       <p>재고가 얼마 남지 않았습니다</p>
                   </div>)
                : null
            }
	)
}
```

추가로 setTimeout은 실행할 코드가 복잡하면 문제가 발생할 수 있어 useEffect의 Unmount 시 clearTimeout 으로 제거한다.



# Props

HTML을 Component로 나누다보면 다른 Component의 State에 접근할 방법이 필요하다. 이때 사용하는 것이 Props 이다.

```react
function App(){
	let [shoes, shoesChange] = useState(Data);
    
    return(
    	<div>
        	<Products shoes={shoes}></Products>
        </div>
    )
}

function Products(props) {
  return (
    props.shoes.map(function (i, idx) {
      return (
        <div className="col-md-4" key={idx}>
          <img src={'URL' + (idx + 1) + '.jpg'} width="100%" />
          <h4>{i.title}</h4>
          <p>{i.content}</p>
          <p>{i.price}원</p>
        </div>
      )
    })
  )
}
```



## Context API

페이지가 복잡해지면 Props로 넘기는 것이 복잡해진다. 이런 경우 Context API를 쓴다.

1) useContext 를 import 한다.

2) Component 메인 함수 전에 Context 생성한다.

3) Context를 사용하고 싶은 HTML 영역을 Context 태그로 감싼다. 아래 예제에선 <stockContext.Provider></stockContext.Provider> 이다.

4) 태그 안에 넘길 값을 정의한다. 아래 예제에선 <stockContext.Provider value={stock}>에서 value에 해당하는 부분이다.

```react
import React, { useContext, useState } from 'react';

let stockContext = React.createContext();

function App() {

  let [stock, stockChange] = useState([10,11,12])

  return (
	<div>
		<stockContext.Provider value={stock}>
        <div className="row">
        	<Products shoes={shoes}></Products>
        </div>
        </stockContext.Provider>    
    </div>
  )
 
  function Products(props) {

  let stock = useContext(stockContext);

  return (
    props.shoes.map(function (i, idx) {
      return (
        <div className="col-md-4" key={idx}>
          <p>재고: {stock[idx]}</p>
        </div>
      )
    })
  )
}
```

만일 Context를 다른 Component로 넘기길 원한다면

1) Context 변수 앞에 export를 붙이고

```react
export let stockContext = React.createContext();
```

2) 넘길 Component를 Context 태그로 감싼다. (*주의 Route 태그를 감쌀 경우 다른 path가 작동하지 않는다)

```react

<Route path="/detail/:id">
	<stockContext.Provider value={stock}>            
		<Detail shoes={shoes} stock={stock} stockChange={stockChange}></Detail>
	</stockContext.Provider>
</Route>
```

3) 받아올 Component에 import 한다.

```react
import {stockContext} from './App';
```

3) 위 예제와 마찬가지로 Context를 쓰길 원하는 HTML 영역을 Context 태그로 감싼다.



# AJAX

서버에 요청하는 방식은 크게 세가지가 있다.

- GET 요청: 서버에 URL 표기로 정보를 전달해 요청
- POST 요청: 서버에 URL에 표기하지 않고 정보를 전달해 요청
- AJAX: 비동기(Asynchronous) 방식으로 브라우저 새로고침 없이 서버에 정보를 전달해 요청

Javascript에서 Ajax를 쓰려면

- JQuery 설치 ⇒ $.ajax()
- Axios 설치 ⇒ axios.get()
- Javascript ⇒ fetch()

React에서 axios를 쓰기 위해 먼저 터미널에서 아래 명령어로 axios를 설치한다.

```bash
yarn add axios
```

### axios.get() ⇒ 버튼 클릭(onClick)으로 데이터 받기
{: .no_toc }

axios.get('url').then().catch()

```react
import axios from 'axios';

function App() {
    
    let [shoes, shoesChange] = useState(Data);
    
    return(
        <div>
        	<button className="btn btn-primary" onClick={()=>{
              axios.get('URL')
              .then((result)=>{console.log(result.data)})
              .catch(()=>{})
            }}>더보기</button>
        </div>
    )
}
```

then: 성공 시 실행할 코드 작성 (Callback 콜백 함수 () => {}로 작성), console 창에 정보가 뜨면 정상이다.

catch: 실패 시 실행할 코드 작성

useState로 변수를 선언했다면 "더보기" 버튼을 눌렀을때 상품 목록을 가져오기는 아래와 같이 변경함수를 쓸 수 있다. State는 직접 변경이 안되므로 깊은 복사(...)를 쓴다.

```react
axios.get('URL')
              .then((result)=>{
                shoesChange([...shoes,...result.data])
              })
              .catch(()=>{})
```

### 로딩 표시
{: .no_toc }

"더보기" 버튼 누르면 데이터를 받아오기 전까지 진행 표시를 할 수도 있다.

```react
let [load, loadChange] = useState(false);

{
	load
	? (
		<div className="my-alert">
			<p>로딩중입니다</p>
		</div>)
	: null
}

<button className="btn btn-primary" onClick={()=>{
	loadChange(true);
	axios.get('URL')
	.then((result)=>{
	shoesChange([...shoes,...result.data]);
	loadChange(false);
	})
	.catch(()=>{loadChange(false);})
}}>더보기</button>

             
```



### axios.post() 서버에 데이터 보내기
{: .no_toc }

```react
axios.post('url',{id:'admin',pw:'1111'}).then().catch();
```



# Redux

- Props를 쓰지 않고 Component에 State 공유 (ContextAPI와 유사한 역할)

- State 데이터 관리 편리

## Redux 설치

```react
yarn add redux react-redux
```

1) react-redux에서 Provider를 import 한다.

```react
import { Provider } from 'react-redux';
```

2) State를 공유하고 싶은 상위 Component를 Provider 태그로 감싼다.

```react
<Provider>
	<App />
</Provider>
```

전체 적용을 원하면 index.js에서 App 태그를 감싼다.

3) State를 Provider를 import 한 파일에 정의한다.

```react
let store = createStore(()=>{ 
  return [
    { id : 0, name : 'nike', qty : 2 },
    { id : 1, name : 'adidas', qty : 2 }
  ] 
});
```

3) State를 부르고 싶은 Component에 

​	(1) import { connect } 추가

​	(2) export connect 추가

​	(3) function 추가

​	(4) Component 함수 내부 파라미터로 props 설정

```react
import React from 'react';
import { Table } from 'react-bootstrap';
import { connect } from 'react-redux';

function Cart(props) {
    return (
        <div>
            <Table responsive>
                <thead>
                    <tr>
                        <th>#</th>
                        <th>상품명</th>
                        <th>수량</th>
                        <th>변경</th>
                    </tr>
                </thead>
                <tbody>
                    {
                        props.state.map((a, i) => {
                            return (
                                <tr>
                                    <td>{props.state[i].id}</td>
                                    <td>{props.state[i].name}</td>
                                    <td>{props.state[i].qty}</td>
                                    <td>변경</td>
                                </tr>
                            )
                        })
                    }

                </tbody>
            </Table>
        </div>
    );
}

function test(state) {
    return {
        state: state
    }
}

export default connect(test)(Cart)
```



## Redux State 변경

Redux로 정의한 State의 값을 변경하려면 reduce 함수를 정의하고, dispatch로 연동한다. 이런 방식으로 State 정의와 State 변경 함수를 모아서 관리할 수 있다.

1) State를 정의한 파일에 reducer 함수 정의

```react
let defaultValue = [
  { id : 0, name : 'nike', qty : 2 },
  { id : 1, name : 'adidas', qty : 2 }
];

function reducer(state = defaultValue, condition) {
  if (condition.type === 'addQty'){
    let tmp = [...defaultValue];
    tmp[0].qty++;
    return tmp;
  }
  else if (condition.type === 'removeQty'){
    let tmp = [...defaultValue];
    if (tmp[0].qty == 1) alert('수량은 최소 1이어야 합니다');
    else tmp[0].qty--;
    return tmp;
  }
  else {
    return state;
  }
}

let store = createStore(reducer);
```

2) State 값 변경 액션 위치에 props.dispatch로 연결한다.

```react
<td>
	<button onClick={()=>{ props.dispatch({ type : 'addQty' })}}>+</button>
	<button onClick={()=>{ props.dispatch({ type : 'removeQty' })}}>-</button>
</td>
```



# Local Storage

State 데이터 저장은 (1) 서버 DB (2) 브라우저 임시 저장공간에 할 수 있다. 대부분의 데이터는 DB에 저장하지만 캐시, history 등 임시 저장 목적으로 브라우저 임시 저장공간을 쓸 수 있다. 브라우저 임시 저장공간은 Session Storage와 Local Storage가 있다. Session Storage는 브라우저를 끄거나 일정 시간이 지나면 사라지고, Local Storage는 임시 저장공간을 청소하지 않는 이상 반 영구적으로 저장할 수 있다. 단, 크롬 기준 5MB 이하 텍스트 저장만 가능하다.

아래는 브라우저 임시 저장공간을 쓰는 예이다.

- 글 작성 중간 실수로 데이터를 잃어버리지 않기 위한 임시 저장
- 장바구니, 좋아하는 콘텐츠 등 수시로 변경하는 데이터
- 방문자가 봤던 상품 팝업에 띄우기 위해 저장
- 서버에 반드시 저장할 필요가 없는 데이터

함수형 Component로 만든 페이지 접속 시 Local Storage에 페이지 url 기록을 원하면 아래와 같이 useEffect() 안에 localStorage getItem, setItem으로 불러오고 저장할 수 있다.

```react
useEffect(()=>{
	var arr = localStorage.getItem('watched');
	if(arr == null){ arr = []; } else { arr = JSON.parse(arr); }
    arr.push(id);
    arr = new Set(arr);
    arr = [...arr];
    localStorage.setItem('watched',JSON.stringify(arr));
},[]);
```



# React 성능 개선

React의 성능은 크롬 확장 프로그램인 [React-developer-tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=ko)를 통해 랜더링 시간을 체크하는 방식으로 확인할 수 있다.

## 1. Lazy Loading : Component가 필요한 시점에 import

파일 상단에 lazy, Suspense를 import 하고, import 했던 Component를 아래와 같이 바꾼다.

```react
import React, { useContext, useState, lazy, Suspense } from 'react';

// import Detail from "./Detail.js";						// 기존 방식
let Detail = lazy(()=>{ return import('./Detail.js') });
```

Component 태그를 아래와 같이 Suspense 태그로 감싼다.

```react
<Route path="/detail/:id">
	<stockContext.Provider value={stock}>
		<Suspense fallback={<div>로딩중이에요</div>}>
			<Detail shoes={shoes} stock={stock} stockChange={stockChange}></Detail>
		</Suspense>
	</stockContext.Provider>
</Route>
```

## 2. memo( ) : 불필요한 재랜더링 방지

Component 크기가 클때 쓸 수 있다.



## 3. 기타 React 성능 개선 방법

1) 콜백함수나 스타일 Object는 컴포넌트 밖 별도 변수로 쓴다. 재 랜더링 시 메모리 할당 작업을 줄일 수 있다.

2) CSS 애니메이션 효과를 줄때 margin, width, padding 등 레이아웃 속성에 애니메이션 효과를 주면 느려진다. 가급적 transform, opacity 같은 속성을 주는 것이 좋다.



# 기타

## PWA, Progressive Web App

아이폰 앱스토어, 안드로이드 구글플레이에 등록하지 않고 배포할 수 있게 만드는 리액트 기반 웹앱이다.

( 추후 업데이트 예정 )



## 리액트 애니메이션 효과 React CSS Animation

1) 커맨드에서 react-transition-group 설치한다.

```bash
yarn add react-transition-group
```

2) 코드에 라이브러리를 import 한다. 추가로 CSS를 작성할 파일도 import 한다.

```react
import { CSSTransition } from 'react-transition-group'
import './Detail.scss';
```

3) 애니메이션을 추가할 Component를 CSSTransition 태그로 감싼다.

```react
<CSSTransition in={tabSwitch} classNames="ani" timeout={500}>
	<TabContent tab={tab} tabSwitchChange={tabSwitchChange}/>
</CSSTransition>
```

	- in : 효과 ON, OFF 스위치 역할
	- classNames : CSS 파일에 효과 부여를 위한 클래스 이름 지정
	- timout : CSS 효과 작동하기까지의 시간

4) 효과를 ON, OFF 할 수 있는 스위치 State를 만든다.

```react
let [tabSwitch, tabSwitchChange] = useState(false);
```

5) CSS 파일에 애니메이션 시작과 동작 시 적용할 CSS를 기입한다. 아래 예제는 투명도와 Transition 시간으로 천천히 나타나는 효과를 만든 것이다.

```css
.ani-enter {    // 애니메이션 시작할때 적용할 CSS
    opacity: 0;
}

.ani-active {   // 애니메이션 동작할때 적용할 CSS
    opacity: 1;
    transition: all 500ms;
}
```

이외에도 react-transition-group에서 제공하는 애니메이션 효과와 예제는 아래 링크에서 확인한다.

[https://reactcommunity.org/react-transition-group/](https://reactcommunity.org/react-transition-group/)



## Node.js 서버에 React 웹 올리기

React는 새로고침 없는 부드러운 페이지 전환이 가능하고, 서버의 역할이 줄어든다.






# React Error 에러 모음

### npm start 시 "react-scripts 은(는) 내부 또는 외부 명령 실행할 수 있는 프로그램 또는 배치 파일이 아닙니다"
{: .no_toc }
커맨드에 npm update 또는 yarn update 입력해 해결한다.

