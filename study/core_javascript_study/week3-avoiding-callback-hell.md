https://javascript.info/promise-basics

### 콜백 지옥의 발생과 해결
동기적 함수 호출이 아닌, 비동기적 함수 호출 시
함수의 인자로 콜백 함수를 받고, 그 콜백 함수의 인자로 또 콜백 함수를 받는 중첩이 일어나면 당연히 가독성이 나빠진다.

비동기 작업은 필연적이지만, 보다 코드를 컨트롤하기 편하려면 Promise 패턴을 이용하면 좋다. Promise 패턴은 쉽게 말해 어떤 상태일 때 어떤 행동을 할 것이라는 약속을 여러 개 걸어 놓음으로써 콜백 함수의 흐름을 제어하는 방식이다.

Promise는 자바스크립트 객체이기도 하다.
Promise 객체를 생성하는 구문은 다음과 같다.
```
let promise = new Promise(function(resolve, reject) {
    // executor 
});
```
new Promise로 전달되는 함수를 executor라고 한다. new Promise가 생성됐을 때 executor는 자동으로 실행된다. 그리고 인자인 resolv
e와 reject는 자바스크립트 그 자체가 제공하는 콜백 함수다. 
만약 executor가 결과를 갖고 있을 때, 반드시 둘 중 하나의 콜백 함수를 호출한다.
* resolve(value) --- 작업이 성공적으로 완료됐을 경우
* reject(error) --- 만약 에러가 발생하면, error는 error 객체를 의미한다.

요약하면, executor는 자동으로 실행되어 작업을 시도한다. 만약 잘 끝났다면 resolve를 호출하고, 에러가 있었다면 reject를 호출한다.

new Promise 생성자를 통해 생성한 promise 객체는 내부적으로 state, result 속성을 갖고 있다.

Promise의 state에는 크게 pending, fulfilled, rejected, (settled)가 있다. 
result는 초기에는 undefined지만, resolve(value)가 호출되거나 reject(error)가 호출되면 값이 value, error로 바뀐다.

..업데이트 중..
