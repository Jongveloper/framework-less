# 선언형 프로그래밍과 명령형 프로그래밍
명령형 프로그래밍은 어떤 일을 **어떻게** 할 것인가에 관한 것이고,
선언적 프로그래밍은 **무엇을** 할 것인가에 관한 것이다.
### example
식당에 갔다.
- 명령형 접근 (HOW) : 17번이라고 적힌 표지판 아래에 있는 테이블이 비어있네요. 우리는 저기로 걸어가서 테이블에 앉도록 하겠습니다.
- 선언형 접근 : 2명 자리주세요.

명령형 방식은 내가 실제로 자리에 **어떻게** 앉을지에 관심이 있다.
이를 위해 나는 내가 어떻게 테이블을 잡아서 자리에 앉을지에 관해, 필요한 단계들을 하나하나 나열해야 한다.
반면, 선언형 방식은 오로지 내가 **무엇을** 원하는지에 관심이 있다.

수동 스틱은 명령형 방식이고 오토 스틱은 선언적 방식이다.

---

실제 코드로 예시를 들기 전에, 자리에 앉는 방법은 누가 알지?와 같은 의문이 생길 수 있을 것이다.
이에 대한 대답은, 선언적 방식의 접근을 위해서는 명령형 방식으로 **어떻게 접근하는가**에 관한 내용이 먼저 추상화 되어있어야 한다.
- 직원에게 사용했던 선언형 접근에는 직원이 테이블에 어떻게 앉는가에 관한 모든 명령형 단계들을 알고 있다는 가정이 뒷받침 되어있다.
- 오토 스틱 자동차는 변속 기어에 대해 일종의 추상화 계층을 가지고 있다.
```
많은 선언적 접근 방식들의 기반에는 일종의 명령적 추상화가 존재한다.
```
### 명령적 코드
```js
function double (arr) {
  const results = []
  for (let i = 0; i < arr.length; i++) {
    results.push(arr[i] * 2)
  }
  return results
}
function add (arr) {
  let result = 0
  for (let i = 0; i < arr.length; i++) {
    result += arr[i]
  }
  return result
}

$('#btn').click(function() {
  $(this).toggleClass('highlight')
  $(this).text() === 'Add Highlight'
    ? $(this).text('Remove Highlight')
    : $(this).text('Add Highlight')
})
```
1. 위 코드는 **어떻게** 처리하는지에 관해 묘사하고 있다.
각각의 예시에서, 명시적으로 배열을 반복하거나, 우리가 원하는 기능을 수행하기 위한 단계들을 명시적으로 나열하고 있다.
2. 각각의 예시에서 상태의 일부를 변경하고 있다.
처음 두 예시에서는 result라는 변수를 만들어 그것을 계속해서 수정하고 있다.
세 번째 예시에서는 아무런 변수도 없지만, 여전히 DOM 자체에 state가 존재하고 있다.
그리고 해당 코드는 DOM의 state를 수정하고 있다.

### 선언적 코드
```js
function double (arr) {
  return arr.map((item) => item * 2)
}

function add (arr) {
  return arr.reduce((prev, current) => prev + current, 0)
}

<Btn
  onToggleHighlight={this.handleToggleHighlight}
  highlight={this.state.highlight}
>
  {this.state.buttonText}
</Btn>
```
처음 두 예제에서, 자바스크립트의 내장 함수인 map과 reduce를 활용한 레버리징에 주목해보면, 우리는 명령적인 코드를 작성하지 않고도 명령적인 코드를 작성한 것과 같은 결과를 얻을 수 있다.
**가장 효율적인 선언적 프로그래밍 방법은 명령적으로 작성된 코드를 추상화하는 것이다.**

해당 예제들은 우리가 **어떻게** 처리할지보다, **무엇을** 원하는지를 설명한다.
우리는 map과 reduce가 어떻게 구현되어있는지는 전혀 알지도 못하고, 관심도 없다.
아무런 state도 변경하지 않는다. 모든 변경들은 map과 reduce 내부에 추상화되어있다.