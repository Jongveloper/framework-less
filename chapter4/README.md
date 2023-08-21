# Chapter 4 - Web Components

[![framework less](https://file-blyuofkggj.now.sh)](https://github.com/frameworkless-movement/manifesto)

To start the examples just run:
    
    npm start

# Custom Event And Dispatch
자바스크립트를 사용하면 핸들러를 할당할 수 있을 뿐만 아니라 이벤트를 직접 만들수도 있다.

### Event의 생성자
내장 이벤트 클래스는 DOM 요소 클래스같이 계층 구조를 형성한다.
내장 이벤트 클래스 계층의 꼭대기엔 Event 클래스가 있다.
```js
let event = new Event(type[, options]);
```
인수는 다음과 같다.
- type : 이벤트 타입을 나타내는 문자열로 "my-event" 같은 커스텀 이벤트가 올 수 있고 "click" 같은 내장 이벤트가 올 수 있다.
- options - 두 개의 선택 프로퍼티가 있는 객체가 온다.
    - bubbles : true이면 이벤트가 버블링된다.
    - cancelable : true이면 브라우저 기본 동작이 실행되지 않는다.
```js
defaultValue = {
    bubbles: false,
    cancelable: false
}
```

### DispatchEvent
이벤트 객체를 생성한 다음엔 elem.dispatchEvent(event)를 호출해 요소에 있는 이벤트를 반드시 실행시켜줘야 한다.
이벤트를 실행시켜줘야 핸들러가 일반 브라우저 이벤트처럼 이벤트에 반응할 수 있다.
```js
<button id="elem" onclick="alert('클릭');">자동 클릭 버튼</button>

<script>
    let event = new Event("click");
    elem.dispatchEvent(event);
</script>
```

`event.isTrusted`를 사용하면 이벤트가 스크립트를 통해 생성한 이벤트인지 진짜 사용자가 만든 이벤트인지 알 수 있다.

### CustomEvent
제대로 된 커스텀 이벤트를 만들려면 new CustomEvent를 사용해야 한다.
CustomEvent는 Event와 거의 유사하지만 한 가지 다른점이 있다.
CustomEvent의 두 번째 인수는 객체가 들어갈 수 있는데, 개발자는 이 객체에 detail이라는 프로퍼티를 추가해 커스텀 이벤트 관련 정보를 명시하고, 정보를 이벤트에 전달할 수 있다.
```js
<h1 id="elem">우종혁님, 환영합니다!</h1>

<script>
  elem.addEventListener("hello", function(event) {
    alert(event.detail.name);
  });

  elem.dispatchEvent(new CustomEvent("hello", {
    detail: { name: "종혁" }
  }));
</script>
``` ㅜ
detail 프로퍼티엔 어떤 데이터도 들어갈 수 있다.
사실 new Event로 일반 이벤트를 생성한 다음 추가 정보가 담긴 프로퍼티를 이벤트 객체에 추가해주면 되기 때문에 detail 프로퍼티 없이도 충분히 이벤트에 원하는 정보를 추가할 수 있다.
그런데도 detail이라는 특별한 프로퍼티를 사용하는 이유는 다른 이벤트 프로퍼티와 충돌을 피하기 위해서이다.
이 외에도 new CustomEvent를 사용하면 코드 자체만으로 커스텀 이벤트라고 설명해주는 효과가 있다.
