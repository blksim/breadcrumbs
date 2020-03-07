### constructor, prototype, instance
```
var instance = new Constructor();
```
> 어떤 생성자 함수를 new 연산자와 함께 호출하면
> Constructor에서 정의된 내용을 바탕으로 새로운 인스턴스가 생성됩니다.
> 이 때 instance에는 __proto__라는 프로퍼티가 자동으로 부여되는데,
> 이 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조합니다.
(148p)

```
var Person = function (name) { // 변수 Person에 함수 선언문을 할당. 
  this._name = name;
};
Person.prototype.getName = function () { 
// Person의 prototype이라는 객체 내 getName이라는 속성에 익명 함수 선언문을 할당. 
// 현재 객체의 _name이라는 속성 값을 리턴 
  return this._name;
};

var suzi = new Person('Suzi');
suzi.__proto__.getName(); // undefined ---> 여기서 this가 가리키는 것은 suzi가 아니라 suzi.__proto__라는 객체라서
```
만약 `__proto__` 객체에 name 프로퍼티가 있다면
```
var suzi = new Person('suzi');
suzi.__proto__.name = 'SUZI__proto__';
suzi.__proto__.getName; // SUZI__proto__
```
`__proto__` 없이 인스턴스에서 곧바로 메서드를 쓸 수도 있다.
```
var suzi = new Person('Suzi', 28);
suzi.getName(); //Suzi
var iu = new Person('Jieun' 28);
iu.getName(); // Jieun
`__proto__` 없이 메서드가 호출된다? 
```
`__proto__`는 **생략 가능한 프로퍼티**라서 그렇다.
> 생략하지 않으면 this는 suzi.__proto__를 가리키지만, 이를 생략하면 suzi를 가리킵니다.
> suzi.__proto__에 있는 메서드인 getName을 실행하지만 this는 suzi를 바라보게 할 수 있게 된 것이죠.
(152p)

> 자바스크립트는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓는데, 해당 함수를 생성자 함수로서 사용할 경우,
> 즉 new 연산자와 함께 호출할 경우, 그로부터 생성된 인스턴스에는 숨겨진 프로퍼티인 `__proto__`가 자동으로 생성되며,
> 이 프로퍼티는 생성자 함수의 prototype 프로퍼티를 참조합니다. `__proto__` 프로퍼티는 생략 가능하도록 구현돼 있기 때문에
> **생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있게 됩니다.
(152p)

> 한편 property 내부에 있지 않은 메서드들은 인스턴스가 직접 호출할 수 없고, 생성자 함수에서 직접 접근해야 실행이 가능합니다.

**constructor**
생성자 함수의 프로퍼티인 prototype 객체 내에는 constructor라는 프로퍼티가 있다. 인스턴스의 __proto__ 객체 내부에도 마찬가지다.
이 프로퍼티는 단어 그대로 원래의 생성자 함수(자기 자신)을 참조
```
var arr = [1, 2];
Array.prototype.constructor === Array // true
arr.__proto__.constructor === Array // true
arr.constructor === Array // true

var arr2 = new arr.constructor(3, 4);
console.log(arr2); // [3, 4];
```
> 인스턴스의 __proto__가 생성자 함수의 prototype 프로퍼티를 참조하며 __proto__가 생략 가능하기 때문에 
인스턴스에서 직접 constructor에 접근할 수 있는 수단이 생긴 것입니다.

[Constructor]
[instance].__proto__.constructor
[instance].constructor
Object.getPrototypeOf([instance]).constructor
[Constructor].prototype.constructor
---> 모두 동일한 대상 가리킴

[Constructor].prototype
[instance].__proto__
[instance]
Object.getPrototypeOf([instance])
---> 모두 동일한 객체(prototype)에 접근 가능

### 메서드 오버라이드
- 인스턴스가 이미 동일한 프로퍼티나 메소드를 가지고 있을 경우 프로토타입의 메소드가 새로 정의한 메소드로 덮어씌워진다.
- 여기서 완전히 원본을 교체한다는 의미가 아니라, 원본 위에 얹는다는 데 가까움.
- this가 바라보는 대상 바꾸려면 ---> call이나 apply 사용 ---> 오버라이드됐더라도 원본 메소드에 접근 가능

### 프로토타입 체인
모든 객체의 __proto__에는 Object.prototype이 연결돼있다. prototype 객체도 마찬가지.
__proto__가 생략가능한 이유는 결국 __proto__의 __proto__ .... 식으로 거슬러 올라가면 결국 최상단의 Object.prototype이 나오기 때문.
이 체인을 프로토타입 체인이라 하고, 이를 거슬러 올라가며 검색하는 것을 프로토타입 체이닝이라 한다.
가장 가까운 __proto__를 먼저 검색 후, 해당 메소드가 없으면 __proto__가 참조했던 prototype 객체가 참조했던 __proto__를 검색하는 식

### 객체 전용 메서드의 예외사항
객체에 한해서만 동작하게 하고 싶은 메소드라면, 프로토타입 객체 안에 정의할 경우 다른 데이터 타입도 해당 메서드를 사용할 수 있어 의도한 대로 
동작하지 않을 수 있다.
따라서 이 경우에는 메소드를 Object.prototype이 아닌 스태틱 메소드, 해당 인스턴스를 인자로 주입받는 방식으로 정의할 수밖에 없다. 

그래서 Object.prototype의 메소드에는 toString, hasOwnProperty, valueOf, isPrototypeOf처럼 범용적인 메서드들만 존재한다.

*참고
Object.create(null)은 __proto__가 없는 객체를 생성한다.
그러면 범용적으로 존재하던 내장 메서드와 프로퍼티가 제거됨으로써 객체 자체의 무게가 가벼워짐으로써 성능상 이점이 있다.


### 다중 프로토타입 체인
__proto__를 아래로 체이닝하는 방법은 현재 인스턴스에서 __proto__에 __proto__가 참조하는 상위 생성자 함수의 인스턴스를 연결하면 된다.
```
var Grade = function () {
  var args = Array.prototype.slice.call(arguments);
  for (var i = 0; i < args.length; i++) {
    this[i] = args[i];
  }
  this.length = args.length;
};
var g = new Grade(100, 80);
```
위 객체는 인덱스 붙은 요소는 갖지만 배열은 아닌 유사배열 객체라 배열 메서드를 사용할 수 없음.
call/apply를 사용하는 대신 인스턴스에서 직접 배열 메서드를 사용하고 싶다면 
g.__proto__에 배열 인스턴스를 할당하면 된다.
```
Grade.prototype = [];
```
그러면 결과적으로 Grade.prototype, Array.prototype의 메서드는 물론 최상단의 Object.prototype에 있는 메서드에도 접근할 수 있게 된다.
