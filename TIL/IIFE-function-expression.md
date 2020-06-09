참고링크<br>
https://en.wikipedia.org/wiki/Immediately_invoked_function_expression<br>
https://developer.mozilla.org/en-US/docs/Glossary/IIFE<br>
https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-stop-feeling-iffy-about-using-an-iife-7b0292aba174<br>
http://gregfranko.com/blog/i-love-my-iife/

### IIFE(Immediately invoked function expression) : 즉시 호출되는 함수
>An immediately invoked function expression (or IIFE, pronounced "iffy")[1] is a JavaScript programming language idiom which produces a lexical scope using JavaScript's function scoping.<br>
>Immediately invoked function expressions can be used **to avoid variable hoisting from within blocks**, 
**protect against polluting the global environment and simultaneously allow public access to methods** 
while retaining privacy for variables defined within the function.
자바스크립트 함수 스코프 규칙을 이용해서 렉시컬 스코프를 생성하기 위한 함수 표현식을 일컫는 이디엄.

>You will often (but not always) see IIFEs used in frameworks/libraries such as jQuery. 
>They will often wrap all of the code for their framework/library inside of an IIFE in order to protect the scope of its variables 
>and also to make sure that everything is executed without the user having to do anything.

#### 왜 쓸까
1. 블럭 스코프 내에서 변수 호이스팅(끌어올림)되는 것을 방지하기 위해
2. 전역 환경 오염 방지 & 메소드에 아무데서나 접근하지 못하도록 방지하기 위해 ---> 코드 모듈화에 도움이 된다

#### 사용법
```
(function () { /* ... */ }(); // 여는 소괄호로 시작하는 함수 선언문 뒤에 소괄호 한 쌍
(function () { /* ... */ }()); // 소괄호로 함수 선언문을 한 번 감싸거나
(() => { /* ... */})(); // 화살표 함수를 소괄호로 감싼 다음 뒤에 소괄호 한 쌍
```
>By wrapping the anonymous function inside of parentheses, the JavaScript parser knows to treat the anonymous function as a function expression instead of a function declaration. 
>A function expression can be called (invoked) immediately by using a set of parentheses, but a function declaration cannot be.

```
(function () {
  var aName = "Barry";
})();
console.log(aName) // throws "Uncaught ReferenceError: aName is not defined"
```

```
var result = (function () {
  var name = "Barry";
  return name;
})();
console.log(result); // "Barry" IIFE를 변수에 할당 시 함수 자체가 아니라 실행 결과만 저장된다
```

#### 장점
1. 스코프에서 식별자 탐색에 걸리는 시간 감소
>A small performance benefit of using IIFE’s is the ability to pass commonly used global objects (window, document, jQuery, etc) to an IIFE’s anonymous function, 
>and then reference these global objects within the IIFE via a local scope.
>Note: JavaScript first looks for a property in the local scope, and then it goes all the way up the chain, last stopping at the global scope. 
>Being able to place global objects in the local scope provides faster internal lookup speeds and performance.
전역 객체의 레퍼런스를 지역 변수에다 저장한 다음 쓸 수 있음 <- 자바스크립트 엔진은 가장 안쪽(좁은) 스코프부터 식별자를 탐색하므로, 당연히 속도와 성능이 향상될 수 있다.

2.minification optimization
```
function($) {
// library codes
}(jQuery)
```
>Note: Another added benefit (when using jQuery), is that you can freely use the $ without worrying about other library conflicts, 
>since you passed in the global jQuery object and scoped it to the $ as a local parameter.
특히 제이쿼리 개발자한테 편리한 것은 다른 라이브러리와의 충돌 없이 전역 제이쿼리 객체에 지역 매개변수인 $를 통해 접근할 수 있다는 것

3. ....업데이트중...


#### 참고할 만한 개념
