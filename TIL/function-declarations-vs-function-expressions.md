https://medium.com/@mandeep1012/function-declarations-vs-function-expressions-b43646042052 를 번역한 글<br>

### 자바스크립트에서 Function Statement/Declarations란
#### Function Statement 함수 선언문
- function statement(함수 선언문)는 function을 선언한다.
- 선언된 함수는 나중을 위해 저장되고, 나중에 실행될 것이다. *호출됐을 때*
- 변수 선언이 var로 시작해야 되는 것과는 달리, 그냥 함수 그 자체로 시작한다.
- 예를 들어 `function` `bar() { return 3; }` 이렇게.
- 함수는 오직 여기서만 선언할 수 있다. *사용하려면,* 반드시 함수명에 소괄호를 붙여 호출해야 한다.

#### Function Expression 함수 표현식
- 자바스크립트 함수는 **표현식**에 의해서도 정의될 수 있다.
- 함수 표현식은 변수에 할당된다.
- `var x = function (a, b) { return a * b};`
- 함수 표현식이 변수에 할당되고 나면, 변수는 함수로 사용될 수 있다. 
- 변수에 할당된 함수들은 함수명이 필요하지 않다.
- 항상 변수명으로 호출된다.

#### Function Expression vs. Function Statement
```
// Function Declaration
alert(foo()); // 선언문을 어떤 코드 전에도 로드할 수 있음
can run.
function foo() { return 5; }
// Function Expression
alert(foo()); //Error! foo를 아직 로드하지 못했음
var foo = function() { return 5; }
```
* 함수 선언문은 어떤 코드가 실행 중일 때도 로드되고,
* 함수 표현식은 인터프리터가 그 코드에 도달했을 때만 로드된다.
* `var` 구문과 비슷하게, 함수 선언문은 다른 코드보다 최상단으로 끌어올려진다(hoisting)
* 함수 표현식은 정의된 함수 스코프 내 지역 변수들의 복사본을 유지하도록 하는 끌어올림이 발생하지 않음.

### Function Expressions의 장점
- closure로서
- 다른 함수에 대한 인자로서
- 즉시실행함수로서(IIFE)
