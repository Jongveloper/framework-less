# Chapter 3 - Managing DOM Events

[![framework less](https://file-blyuofkggj.now.sh)](https://github.com/frameworkless-movement/manifesto)

To start the examples just run:
    
    npm start

### 이벤트 버블링

이벤트 버블링을 설명하려면 예시로 시작하는 것이 가장 좋다. 다음과 같은 코드를 살펴보자
```js
<div onclick="alert('div에 할당한 핸들러!')">
  <em><code>EM</code>을 클릭했는데도 <code>DIV</code>에 할당한 핸들러가 동작합니다.</em>
</div>
```
위 코드를 살펴보면 <em>에는 이벤트를 할당하지 않았음에도 <em>을 클릭했는데 <div>에 할당한 핸들러가 동작한다.

### 버블링
버블링의 원리는 간단하다.

**한 요소에 이벤트가 발생하면, 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작한다. 가장 최상단의 조상 요소를 만날때까지 이 과정이 반복되면서 요소 각각에 할당된 핸들러가 동작한다.**

3개의 요소가 `FORM > DIV > P` 형태로 중첩된 구조를 살펴보자.
요소 각각에 핸들러가 할당되어 있다.
```js
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```

가장 안쪽의 <p> 를 클릭하면 순서대로 다음과 같은 일이 벌어진다.
1. <p> 에 할당된 onClick 핸들러가 동작
2. 바깥의 <div>에 할당된 핸들러가 동작
3. 그 바깥의 <form>에 할당된 핸들러가 동작
4. document 객체를 만날 때까지, 각 요소에 할당된 onClick 핸들러가 동작

위의 동작방식 때문에 <p>요소를 클릭하면 p -> div -> form 순서로 3개의 얼럿 창이 뜨게 된다.
이런 흐름을 '이벤트 버블링'이라고 부른다.
이벤트가 제일 깊은 곳에 있는 요소에서 시작해 부모 요소를 거슬러 올라가며 발생하는 모양이 마치 거품이 올라가는 것 같다고 해서 붙여진 이름이다.
**거의 모든 이벤트는 버블링된다.**
키워드는 '거의' 이다.
`focus` 이벤트와 같이 버블링 되지 않는 이벤트도 존재한다.

### event.target
부모 요소의 핸들러는 이벤트가 정확히 어디서 발생했는지 등에 대한 자세한 정보를 얻을 수 있다.
이벤트가 ㅂ라생한 가장 안쪽의 요소는 target요소라고 불리고 event.target을 통해 접근할 수 있다.
`event.target`과 `this(=event.currentTarget)는 다음과 같은 차이점이 있다.`
- event.target은 실제 이벤트가 시작된 '타깃' 요소이다. 버블링이 진행되어도 변하지 않는다.
- this는 '현재'요소로, 현재 실행 중인 핸들러가 할당된 요소를 참조한다.

### 버블링 중단하기
이벤트 버블링은 타깃 이벤트에서 시작해서 <html> 요소를 거쳐 document 객체를 만날 때까지 각 노드에서 모두 발생한다.
몇몇 이벤트는 window 객체까지 거슬러 올라가기도 한다.
event.stopPropagation()을 호출하면 이벤트 버블링을 막을 수 있다.
```js
<body onclick="alert(`버블링은 여기까지 도달하지 못합니다.`)">
  <button onclick="event.stopPropagation()">클릭해 주세요.</button>
</body>
```

### 주의사항
버블링은 유용하기 때문에 버블링을 꼭 멈춰야하는 명백한 상황이 아니라면 버블링을 막지 않는게 좋다.
이벤트 버블링을 막아야 하는 경우는 거의 없다. 버블링을 막아야 해결되는 문제라면 커스텀 이벤트 등을 사용해 문제를 해결할 수 있다.

### 참고 사이트
[자바스크립트 튜토리얼](https://ko.javascript.info/bubbling-and-capturing)