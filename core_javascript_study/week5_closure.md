참고 링크<br>
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures<br>
https://www.youtube.com/watch?v=71AtaJpJHw0<br>
https://medium.com/@LindaVivah/javascript-closures-a-beginner-s-guide-3d5eee63cb6f<br>
https://www.codingame.com/playgrounds/6516/closures-in-javascript-for-beginners<br>
https://itnext.io/a-beginners-guide-to-understanding-javascript-closures-b5eb57fdbeb4

### Closures
> A closure is **the combination of a function** bundled together (enclosed) with references to its surrounding state (the lexical environment).
In other words, a closure gives you **access to an outer function’s scope from an inner function.** 
In JavaScript, closures are created every time a function is created, at function creation time.
> A closure is a function which has access to the variable from another function’s scope. This is accomplished by creating a function inside a function. Of course, the outer function does not have access to the inner scope.
- 내부 함수에서 외부 함수 유효범위에 접근할 수 있게끔 한 함수 조합. 함수가 선언되는 시점에, 선언될 때마다 클로저가 생성된다.
- 다른 함수 스코프에서 접근할 수 있는 함수. 함수 안에 함수를 생성할 때 생김. 물론, 바깥 함수에서 안쪽 스코프에는 접근할 수 없음.

> Closure are nested function which has access to the outer scope</br>
> After the outer function is returned, by keeping a reference to the inner function (the closures) we prevent the outer scope to be destroyed.
> Another extremely important thing to understand is that a closure is created at every function call. Whenever I’m using the closure, it will reference the same outer scope. If any variable is change in the outer scope, than the change will be visible in the next call as well.
- 클로저는 외부 스코프에 접근할 수 있는 중첩된 함수다.
- 외부 함수가 리턴(실행 종료)되고 나서도, 내부 함수에 대한 참조를 유지함으로써 외부 스코프가 사라지는 것을 막는다.
- 가장 중요한 것은 closure가 모든 함수 호출 시에 생성된다는 것이다. 내가 closure를 사용할 때마다, 같은 외부 스코프를 참조한다는 것이다. 만약 외부 스코프에 수정 사항이 생기면, 다음 함수 호출 시 수정 사항이 그대로 반영될 것이다.

> Things to remember:
> - A closure is an inner function which has access to the outer function scope
> - Every call of the outer function creates a new closure
> - Every call of the closure interacts with the same outer scope – which is persistent
- 클로저는 외부 함수 스코프에 접근할 수 있는 내부 함수다
- 외부 함수 호출할 때마다 새 클로저 생성된다(외부 함수라는 명명이 내부 함수를 전제하므로)
- 클로저 호출할 때마다 같은 외부 스코프와 상호작용한다

> A function inside another function that: 
> 1) uses variables from its parent scope and 2) is exposed to the outside world.
> That “exposed” part is a big reason why we even use closures. 
> By only exposing the function, and not the variables it uses, we essentially make them private.
- 1) 부모 스코프의 변수를 사용하고 
- 2) 바깥으로 노출된 
- 함수 안의 함수 
- '노출됐다'는 게 closure를 사용하는 이유임
- 사용할 변수들이 아닌 함수를 노출하는 것만으로, 변수들에 대한 접근을 프라이빗하게 만들 수 있다.

--> **그래서, closure는 개념적으로 함수인가 현상인가?** 
> 클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상을 말합니다.
> ... 통상적으로 클로저 현상이 발견되는 함수 자체를 클로저라고 칭하더라도 ..... 개념적으로 클로저는 어떤 상황에서만 발생하는 특수한 '현상'을 의미합니다. 함수는 이 현상이 나타나기 위한 '조건'에는 해당하지만, 그 현상을 구체화한 '대상'으로는 볼 수 없습니다. 따라서 실제적인 클로저는 '클로저 현상에 의해 메모리에 남겨진 변수들의 집합'을 지칭하는 것으로 이해하는 것이 좀 더 정확할 것입니다. (121p)


#### Lexical Scoping
```
function init() {
  var name = 'Mozilla'; // 함수 init에 의해 생성된 지역변수
  function displayName() { // displayName()은 내부 함수, 클로저
    alert(name); // 부모 함수 내부에 선언된 변수를 사용
  }
}
```
- `init()`은 지역변수 `name`과 `displayName()`이라는 함수를 생성한다.
- `displayName()`는 `init()` 내부에 정의된 함수로서 `init()` 구현부 내에서만 접근 가능하다. (init 외부에서 접근 불가)
- `displayName()`은 자기가 따로 갖고 있는 지역 변수가 없는 상태.
- 하지만, 내부 함수는 외부 함수의 변수에 접근할 수 있으므로, `displayName()`는 부모 함수 호출 시 선언된 `name` 변수에 접근할 수 있다.
- 이것은 lexical scoping의 예시로, 어떻게 parser가 함수 중첩 시 변수명을 처리하는 지 보여준다. 
- "lexical"이라는 말 자체가
- lexical scoping이 해당 변수가 사용 가능한지 여부를 결정하기 위해, 
- **소스 코드 안에서 변수가 선언된 위치**를 이용한다는 사실을 의미.


### Closure
```
function makeFunc() {
  var name = 'Mozilla';
  function displayName() {
    alert(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```

언뜻 보기엔 이 코드가 작동한다는 게 반직관적으로 보이긴 한다. 몇몇 프로그래밍 언어에서, 함수 내 지역 변수는 
해당 함수가 실행되는 동안 만 존재한다. `makeFunc()`가 실행 완료되면, `name` 변수에 더 이상 접근할 수 없다고 생각할 지 모른다.
하지만, 코드는 기대한 대로 동작하기 때문에 이는 자바스크립트만의 현상이 아닌 것이다.

이렇게 동작하는 이유는 자바스크립트 함수가 closures를 형성하기 때문이다.
- **closure은 함수와 함수가 선언된 lexical environment의 조합이다**<br>
- **이 environment는 해당 closure가 생성되었을 당시의 모든 in-scope 지역 변수로 구성돼있다**
- 위 코드에서, `myFunc`는 makeFunc이 실행 중일 때 **`displayName`의 인스턴스**를 참조하는 변수다.
- `displayName`의 인스턴스는 변수 `name`의 존재에서 보듯, lexical environment에 대한 참조를 유지하고 있다.
- 이런 이유로, `myFunc`가 호출되면, 변수 `name`은 사용 가능한 상태가 되고, "Mozilla"는 `alert`로 전달되는 것이다.

```
function makeAdder(x) {
  return function(y) {
    return x + y;
  }
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  //7
console.log(add10(2)); //12
```

- x라는 인자를 받아서 새로운 함수를 리턴하는 makeAdder라는 함수를 선언
- makeAdder는 **function factory**임. 인자에 새로운 값을 더해 줄 수 있는 함수를 생성한다.
- 위 예시에서는 function factory가 5를 인자로 받는 함수, 10을 인자로 받는 함수 두 개를 새로 생성함
- add5와 add10은 둘다 closures다. 같은 함수 정의를 공유하지만, 다른 lexical environments를 공유한다. 
- add5의 lexical environment에 속한 x는 5이고, add10의 lexical environment에 속한 x는 10이다.

https://www.quora.com/Is-lexical-scope-the-same-definition-as-closure-in-JavaScript-If-not-what-are-the-differences-in-meaning-and-example


### Closure와 메모리 관리
- 메모리 관리 = 메모리 누수(개발자의 의도와 달리 참조 카운트가 0이 되지 않아 GC되지 않는 상황)를 방지한다
- 최근의 자바스크립트 엔진에서는 '의도한' 메모리 소모에 대한 관리법만 잘 파악하면 충분하다.
- 즉 필요하지 않을 때는 메모리를 쓰지 않도록 해주면 된다.
- 참조 카운트를 0으로 만드는 방법 : null이나 undefined를 할당한다.

```
// (1) return에 의한 클로저의 메모리 헤제
var outer = (function () {
  var a = 1;
  var inner = function () {
    return ++a;
  };
})();
console.log(outer());
console.log(outer());
outer = null; // outer 식별자의 inner 함수 참조를 끊음

// (2) setInterval에 의한 클로저의 메모리 해제
(function () {
  var a = 0;
  var intervalId = null;
  var inner = function () {
    if (++a >= 10) {
      clearInterval(intervalId);
      inner = null; // inner 식별자의 함수 참조를 끊음.
    }
  console.log(a);
  };
  intervalId = setInterval(inner, 1000);
})();

// (3) eventListener에 의한 클로저 메모리의 해제
(function () {
  var count = 0;
  var button = document.createElement('button');
  button.innerText = 'click';
  
  var clickHandler = function() {
    console.log(++count, 'times clicked');
    if (count >= 10) {
      button.removeEventListener('click', clickHandler);
      clickHandler = null; // clickHandler 식별자의 함수 참조를 끊음
    }
};
button.addEventListner('click', clickHandler);
document.body.appendChild(button);
})();
```

### Closure 활용 사례
#### 1. 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때
#### 2. 접근 권한 제어
- return을 활용한 접근 권한 부여
> 외부에 제공하고자 하는 정보들을 모아서 return하고, 내부에서만 사용할 정보들은 return하지 않는 것으로 접근 권한 제어가 가능한 것입니다. return한 변수들은 공개 멤버(public member)가 되고, 그렇지 않은 변수들은 비공개 멤버(private member)가 되는 것이죠.
> 1. 함수에서 지역변수 및 내부함수 등을 생성합니다.
> 2. 외부에 접근권한을 주고자 하는 대상들로 구성된 참조형 데이터(대상이 여럿일 때는 객체 또는 배열, 하나일 때는 함수)를 return합니다.
> -> return한 변수들은 공개 멤버가 되고, 그렇지 않은 변수들은 비공개 멤버가 됩니다.
이 때 return되는 함수에 대한 덮어씌우기가 가능 하지만, 그것도 변경되지 않게 하려면 return 전에 객체를 freeze한다. 
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze

#### 3. 부분 적용 함수(partially applied function)
#### 4. 커링 함수(currying function)
https://edykim.com/ko/post/writing-a-curling-currying-function-in-javascript/<br>
https://javascript.info/currying-partials<br>
https://www.zerocho.com/category/JavaScript/post/579236d08241b6f43951af18<br>
https://blog.bitsrc.io/understanding-currying-in-javascript-ceb2188c339<br>
> Currying is a process in functional programming in which we can transform a function with multiple arguments into a sequence of nesting functions. It returns a new function that expects the next argument inline.<br>
> It keeps returning a new function (that expects the current argument, like we said earlier) until all the arguments are exhausted. The arguments are kept "alive"(via closure) and all are used in execution when the final function in the currying chain is returned and executed.<br>
> 여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 것을 말합니다
--> 자주 쓰이는 함수의 매개변수 대부분이 비슷하고 일부만 바뀌는 경우 활용 가능.
```
var getInformation = function (baseUrl) {
  return function (path) {
    return function (id) {
      return fetch(baseUrl + path + '/' + id);
    };
  };
};
//ES6
var getInformation = baseUrl => path => id => fetch(baseUrl + path + '/' + id);
```



