https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures<br>
https://www.youtube.com/watch?v=71AtaJpJHw0

### Closures
>A closure is **the combination of a function** bundled together (enclosed) with references to its surrounding state (the lexical environment).
In other words, a closure gives you **access to an outer function’s scope from an inner function.** 
In JavaScript, closures are created every time a function is created, at function creation time.
내부 함수에서 외부 함수 유효범위에 접근할 수 있게끔 한 함수 조합. 함수가 선언되는 시점에, 선언될 때마다 클로저가 생성된다.

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

아직 lexical environment와 closure의 개념적 차이가 뭔지 확실하게 모르겠음
..업데이트중..
