참고링크
https://codeburst.io/javascript-what-the-heck-is-a-callback-aba4da2deced<br>

### 컨셉
> Simply put: A callback is a function that is to be executed after another function has finished executing — hence the name ‘call back’.
> More complexly put: In JavaScript, functions are objects. Because of this, functions can take functions as arguments, and can be returned by other functions. Functions that do this are called higher-order functions. Any function that is passed as an argument is called a callback function.

어떤 함수가 실행 완료되고 난 **다음에** 실행되는 함수라서 call back
자바스크립트에서는 함수도 객체다. 그래서 함수도 함수를 인자로 가질 수 있고, 이는 다른 함수의 인자로 또다시 전달될 수 있다.
이렇게 다음에 실행될 함수를 인자로 전달해주는 역할을 하는 함수들을 higher-order functions라고 하고(선순위 함수), 
**인자로 전달되는 함수**를 callback function(콜백 함수)라고 한다.

### 사용 이유
자바스크립트는 event driven language = 다른 이벤트를 기다리는 동안에도 다음 코드가 실행될 수 있음
```
function first() {
    // Simulate a code delay
    setTimeout( function() {
      console.log(1);
    }, 500);
}

function second() {
  console.log(2);
}

first();
second();
//2
//1
```

first()를 먼저 호출했음에도, 로그에 찍힌 값은 second()가 먼저다.
이는 자바스크립트가 우리가 원하는 순서대로 함수를 호출하지 않는다는 말이라기 보다는,
second()를 호출하기 전에, first() 호출 시의 응답을 기다리지 않는다는 얘기다.
<br>
그렇다면 이는 콜백 함수랑 무슨 상관일까?
콜백함수 사용은 다른 코드가 실행 완료될 때까지, 
특정 코드를 실행하지 않기로 정하기 위한 방법 중 하나이기 때문이다.

### 사용 방법
```
    function doHomework(subject, callback) {
        alert('Starting my ${subject} homework.');
        callback();
    }
    doHomework('math');
    //Alerts: Starting my math homework.
    doHomework('math', function() {
      alert('Finished my homework');
    });
```
doHomework 함수의 두 번째 인자로 콜백 함수를 바로 익명 함수로 선언해도 되고,

혹은
```
function alertFinished() {
  alert('Finished my homework');
}
doHomework('math', alertFinished);
```
이렇게 미리 정의한 함수선언문의 함수명을 표기해도 된다.
두 가지는 완전히 동일하게 동작하지만, 방식은 약간 다른데 전자는 doHomework() 함수가 실행되는 동안 
함수선언문을 인자로 전달하는 방식이다.

### 사용 예시
트위터 봇 개발하기 예시에서, 트위터 api를 사용해 어떤 응답에 대한 반응을 하기 전 일단 그 응답을 기다려야 하는 상황을 생각해보자.
api 요청할 때, 응답이 와야 그 다음 동작을 할 수 있다. 
```
T.get('search/tweets', params, function(err, data, response) {
  if(!err) {
    // This is where the magic will happen
  } else {
    console.log(err);
  }
}
```
이 요청에는 세 개의 매개변수가 들어간다. 'search/tweets' 요청 경로, 'params' 검색할 파라미터, 그리고 익명으로 선언된 콜백 함수
콜백 함수는 서버로부터의 응답을 기다려야 하는 상황에 코드 실행을 더 진행시키지 않게 해주므로 중요하다.
우리의 api 요청이 성공적일지 아닐 지 모르기 때문에 일단 기다려야 하니까.
트위터 api 서버가 응답해오면, err나 response에 응답 객체를 할당한다.
위 콜백 함수에서는 요청 성공 여부에 따라 새로운 데이터에 대한 다음 동작을 결정할 수 있다!

