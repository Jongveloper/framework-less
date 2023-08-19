# Chapter 2 - Rendering

[![framework less](https://file-blyuofkggj.now.sh)](https://github.com/frameworkless-movement/manifesto)

To start the examples just run:
    
    npm start

# Diffing Algorithm
리액트는 두 가지 가정을 기반하여 O(n) 복잡도의 휴리스틱 알고리즘을 구현했다.
1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.
2. 개발자가 key prop을 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄 수 있다.

두개의 트리를 비교할 때, 리액트는 두 엘리먼트의 루트 엘리먼트부터 비교한다.
이후의 동작은 루트 엘리먼트의 타입에 따라 달라진다.

### 엘리먼트의 타입이 다른 경우
두 루트 엘리먼트의 타입이 다르면, 리액트는 이전 트리를 버리고 완전히 새로운 트리를 구축한다.
<a> 에서 <img>로, <Article>에서 <Comment>로, 혹은 <Button>에서 <div> 로 바뀌는 것 모두 트리 전체를 재구축하는 경우이다.

트리를 버릴 때 이전 DOM 노드들은 모두 파괴된다. 컴포넌트 인스턴스는 componentWillUnmount()가 실행된다. 새로운 트리가 만들어질 때, 새로운 DOM 노드들이 DOM에 삽입된다.
그에 따라 컴포넌트 인스턴스는 UNSAFE_componentWillMount()가 실행되고 componentDidMount()가 이어서 실행된다.
이전 트리와 연관된 모든 state는 사라진다.

루트 엘리먼트 아래의 모든 컴포넌트도 언마운트되고 그 state도 사라진다.
```jsx
<div>
    <Counter/>
</div>

<span>
    <Counter/>
</span>
```

위의 예시에서, Counter 컴포넌트는 언마운트되고 새로운 Counter 컴포넌트가 마운트된다.

### DOM 엘리먼트의 타입이 같은 경우
같은 타입의 두 REACT DOM 엘리먼트를 비교할 때, React는 두 엘리먼트의 속성을 확인하여, 동일한 내역은
유지하고 변경된 속성들만 갱신한다.
```jsx
<div className="before" title="stuff"/>

<div className="after" title="stuff"/>
```

이 두 엘리먼트를 비교하면, React는 현재 DOM 노드 상에 className만 수정한다.
style이 갱신될 때, React는 또한 변경된 속성만을 갱신한다.

```jsx
<div style={{color:'red', fontWeight: 'bold'}}/>

<div style={{color:'green', fontWeight: 'bold'}}/>
```

위 두 엘리먼트 사이에서 변경될 때, React는 fontWeight는 수정하지 않고 color 속성만을 수정한다.

DOM 노드의 처리가 끝나면, React는 이어서 해당 노드의 자식들을 재귀적으로 처리한다.

### 같은 타입의 컴포넌트 엘리먼트
컴포넌트가 갱신되면 인스턴스는 동일하게 유지되어 렌더링 간 state가 유지된다. React는
새로운 엘리먼트의 내용을 반영하기 위해 현재 컴포넌트 인스턴스의 props를 갱신한다.
이때 해당 인스턴스의 UNSAFE_componentWillReceiveProps(), UNSAFE_componentWillUpdate(), componentWillUpdate()가 실행된다.

다음으로 render() 메서드가 호출되고 비교 알고리즘이 이전 결과와 새로운 결과를 재귀적으로 처리한다.

### 자식에 대한 재귀적 처리
DOM 노드의 자식들을 재귀적으로 처리할 때, React는 기본적으로 동시에 두 리스트를 순회하고 차이점이 있으면 변경을 생성한다.
예를들어 자식의 끝에 엘리먼트를 추가하면, 두 트리 사이의 변경은 잘 작동할 것이다.
```jsx
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```
리액트는 두 트리에서 first가 일치하는 것을 확인하고 second가 일치하는 것을 확인한다.
그리고 마지막으로 third를 트리에 추가한다.

하지만 위와 같이 단순하게 구현하면, 리스트의 맨 앞에 엘리먼트를 추가하는 경우 성능이 좋지 않다.
예를 들어, 아래의 두 트리 변환은 형편없이 작동한다.

```jsx
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```
리액트는 Duke와 Villanova 종속트리를 그대로 유지하는 대신 모든 자식을 변경한다.
이런 비효율은 문제가 될 수 있다.

### Keys
위의 문제를 해결하기 위해, React는 key 속성을 지원한다.
자식들이 key를 가지고 있다면, React는 key를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인한다.
예를 들어, 위 비효율적인 예시에 key를 추가하여 트리의 변환 작업이 효율적으로 수행되도록 수정할 수 있다.
```jsx
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```
이제 React는 2014 key를 가진 엘리먼트가 새로 추가되었고, 2015와 2016 key를 가진 엘리먼트는 그저 이동만 하면 되는 것을 알 수 있게 되었다.
실제로, key로 사용할 값을 정하는 것은 어렵지 않다. 그리려고 하는 엘리먼트는 일반적으로 식별자를 가지고 있을 것이고, 그대로 해당 데이터를 key로 사용할 수 있다.

```jsx
<li key={item.id}>{item.name}</li>
```

이러한 상황에 해당하지 않는다면, 데이터 구조에 ID라는 속성을 추가해주거나 데이터 일부에 해시를 적용해서 key를 생성할 수 있다.
해당 key는 오로지 형제 사이에서만 유일하면 되고, 전역에서 유일할 필요는 없다.

최후의 수단으로 배열의 인덱스를 key로 사용할 수 있다. 항목들이 재배열되지 않는다면 이방법도 잘 동작하겠지만 재배열되는 경우 비효율적으로 동작할 것 이다.

인덱스를 key로 사용 중 배열이 재배열되면 컴포넌트의 state와 관련된 문제가 발생할 수 있다.
컴포넌트 인스턴스는 key를 기반으로 갱신되고 재사용된다. 인덱스를 key로 사용하면, 항목의 순서가 바뀌었을 때 key 또한 바뀌게 된다.
그 결과로 컴포넌트의 state가 엉망이 되거나 의도하지 않은 방식으로 바뀔 수 있다.

### 참고 문서
[REACT 공식 문서](https://ko.legacy.reactjs.org/docs/reconciliation.html)