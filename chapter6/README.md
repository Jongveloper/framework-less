#  Chapter 6 - Routing

[![framework less](https://file-blyuofkggj.now.sh)](https://github.com/frameworkless-movement/manifesto)

To start the examples just run:
    
    npm start

### History API

DOM의 window객체는 history 객체를 통해 브라우저의 세션 기록에 접근할 수 있는 방법을 제공한다.
history는 사용자를 자신의 방문 기록 앞과 뒤로 보내고 기록 스택의 콘텐츠도 조작할 수 있는, 유용한 메서드와 속성을 가진다.

### back();
세션 기록의 바로 뒤 페이지로 이동하는 비동기 메서드이다.
방문 기록의 뒤로 이동하려면 다음과 같이 사용한다.
```js
history.back();
```
위의 코드는 사용자가 브라우저 도구 모음의 뒤로 가기 버튼을 누른 것과 같다.

### forward()
세션 기록의 바로 앞 페이지로 이동하는 비동기 메서드이다. 세션 기록의 제일 마지막 페이지에서 호출해도 오류가 발생하지 않는다.
기록의 앞으로 갈 때 사용한다.
```js
history.forward();
```
위의 코드는 사용자가 브라우저 도구 모음의 앞으로 가기 버튼을 누른 것과 같다.

### 기록의 특정 지점으로 이동
go() 메서드를 사용하면 세션 기록에서 현재 페이지의 위치를 기준으로, 상대적인 거리에 위치한 특정 지점까지 이동할 수 있다.
```js
history.go(-1);
```
한 페이지 앞으로 이동하려면 다음과 같이 사용한다.
```js
history.go(1);
```

### pushState()
주어진 데이터를 지정한 제목으로 세션 기록 스택에 넣는다.
데이터는 DOM이 불투명하게 취급하므로, 직렬화 간으한 모든 자바스크립트 객체를 사용할 수 있다.
`history.pushState(state, title, url)`
state: 브라우저 이동시 넘겨 줄 데이터
title: 변경할 브라우저 제목 (원치 않으면 null)
Url: 변경할 주소

### replaceState()

세션 기록 스택의 제일 최근 항목을 주어진 데이터, 지정한 제목 및 URL로 대체한다. 데이터는 DOM이 불투명하게 취급하므로,
직렬화 가능한 모든 자바스크립트 객체를 사용할 수 있다.

### 인터페이스
History는 브라우저의 세션 기록에 접근할 수 있는 방법을 제공하는 인터페이스다.
다음은 history객체를 사용해 브라우저 방문 기록을 추가하거나 대체한 후 탐색하는 코드이다.
```js
window.onpopstate = (event) => {
    alert (
        `location: ${document.location}, state: ${JSON.stringify(event.state)}`,
    );
};

history.pushState({page: 1}, "title 1", "?page=1");
history.pushState({page: 2}, "title 2", "?page=2");
history.replaceState({page: 3}, "title 3", "?page=3");
history.back(); // alerts "location: http://example.com/example.html?page=1, state: {"page: '1"}
history.go(2); //alerts "location: http://example.com/example.html?page=3, state: {"page":3}"
```