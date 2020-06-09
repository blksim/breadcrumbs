### 클래스와 인스턴스의 개념 이해
- 정확히 말해서 클래스 개념도 없고 상속도 없다.
- 비슷하게 흉내낼 뿐이다. ---> ES6의 클래스. 내부적으로는 프로토타입을 활용

실생활에서의 클래스-인스턴스 비유:
- 클래스는 추상개념 = 상위개념 = 공통속성을 모았을 뿐 실재하지 않는다
- 인스턴스는 클래스의 속성을 물려받아서 상대적으로 구체적인 개념
- 인스턴스는 클래스의 속성을 지니는, 실존하는 개체 = 특정 조건에 부합하는 구체적인 예시

### 자바스크립트와 클래스
- 클래스도 상속도 존재하지 않지만, 비슷하게 해석될 수 있는 경우가 있다.
- 예를 들어, Array를 new 연산자와 함께 호출하면 인스턴스가 생성되는데,
- Array의 prototype에 접근할 수 있게 되었으므로 상위객체의 속성을 상속했다고도 볼 수가 있음. 사실은 프로토타입 체이닝에 의한 참조지만.

- 인스턴스의 참조 여부에 따라 스태틱, 인스턴스 멤버로 나누는데
- **인스턴스에서도 직접 메서드를 정의할 수 있기 때문에** 혼란을 야기.
- 인스턴스 메서드 대신 '프로토타입 메서드'라고 부르는 게 더 보편적이다.

```
var Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
}; // 생성자
Rectangle.prototype.getArea = function () { 
  return this.width * this.height;
}; // 프로토타입 메서드
Rectangle.isRectangle = function (instance) {
  return instanceof Rectangle &&
    instance.width > 0 && instance.height > 0;
}; // 스태틱 메서드

var rect1 = new Rectangle(3, 4)
console.log(rect1.getArea()); // 12 (0) 실제로는 rect1.__proto__.getArea에 접근 --> 인스턴스에서 직접 호출할 수 있는 프로토타입 메서드
console.log(rect1.isRectangle(rect1)); // Error isRectangle은 스태틱 메서드이므로 인스턴스에서 직접 접근 불가
console.log(Rectangle.isRectangle(rect1)); // true 생성자 함수 자체를 this로 해야만 호출 가능
```
(p180)

### 클래스 상속
역시나 프로토타입을 기반으로 한 것으로, 자바스크립트에서의 클래스 상속은 결국 프로토타입 체이닝을 잘 연결한 것으로 이해하면 된다.

### ES6의 클래스 및 클래스 상속
```
var Grade = function () {
    var args = Array.prototype.slice.call(arguments);
    for (var i = 0; i < args.length; i++) {
        this[i] = args[i];
    }
    this.length = args.length;
};

Grade.prototype = [];
var g = new Grade(100, 80);
```
> 위 예제의 문제는 length 프로퍼티가 삭제 가능하다는 것과, Grade의 프로토타입에 빈 배열을 연결시켰다는 것.
> 내장 객체인 배열 인스턴스의 length 프로퍼티는 configurable 속성이 false라서 삭제가 불가능하지만,
> Grade 클래스의 인스턴스는 일반 객체의 성질을 지니므로 삭제가 가능해서 문제 
빈 배열 대신 요소 있는 배열을 할당할 경우에도, 인스턴스 메소드 호출 시 그대로 영향을 받게 되어 의도와 다르게 동작하게 된다.

```
var Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
};

Rectangle.prototype.getArea = function () {
  return this.width * this.height;
};

var rect = new Rectangle(3, 4);
console.log(rect.getArea());

var Square = function (width) {
  this.width = width;
};

Square.prototype.getArea = function () {
  return this.width * this.width;
};

var sq = new Square(5);
console.log(sq.getArea());
////
var Square = function (width) {
  this.width = width;
  this.height = width;
};
Square.prototype.getArea = function () {
  return this.width * this.height;
};
///
var Square = function (width) {
  Rectangle.call(this, width, width);
};
Square.prototype = new Rectangle();
```
- Square의 생성자 함수 내부에서 Rectangle의 생성자 함수를 호출하면서 height 자리에 width를 전달.
- 메서드를 상속하기 위해 Square의 프로토타입 객체에 Rectangle의 인스턴스를 할당.
- 이렇게 하위 클래스로 삼을 생성자 함수의 prototype에 상위 클래스의 인스턴스를 부여하는 것만으로도 기본적인 메소드 상속은 가능하지만 여전히 구조적으로 안전성 떨어짐

### 클래스가 구체적인 데이터 지니지 않게 하는 방법
= 앞선 문제 : 상위 클래스의 인스턴스를 하위 클래스의 프로토타입에서 참조함으로써 상속 효과를 낼 수 있게 됐지만,
여전히 인스턴스를 통해 값을 조작할 수 있다는 안전성 문제가 있다.
=> 인스턴스 멤버에 대한 접근 권한을 없애기 위해서 가장 쉬운 방법은 인스턴스 생성 이후에 프로퍼티 지우고 새로 추가하지 못하게 하는 것.
```
delete Square.prototype.width;
delete Square.prototype.height;
Object.freeze(Square.prototype);
```
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/delete
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze
```
// 목적 : 부모 클래스의 것을 포함한 메소드를 프로토타입에서 참조하는 인스턴스를 반환.
// 부모클래스 인스턴스에서 메소드만 옮기고 나머지 프로퍼티는 삭제 후 불변객체로 리턴하는 함수
var extendClass1 = function (SuperClass, SubClass, subMethods) {
    SubClass.prototype = new SuperClass(); // 부모클래스 인스턴스를 자식클래스의 프로토타입이 참조.
    for (var prop in SubClass.prototype) { // 자식 클래스의 프로토타입을 순회
        if (SubClass.prototype.hasOwnProperty(prop)) { // 그 요소가 속성이면
            delete SubClass.prototype[prop]; // 해당 속성을 삭제
        }
    }
    if (subMethods) { // 인자로 받은 subMethod(subClass에 추가할 메소드) 객체
        for (var method in subMethods) { // subMethod 객체를 순회
            SubClass.prototype[method] = subMethods[method];
            // SubClass prototype 객체의 속성에 subMethod의 속성을 대입.
        }
    };

    Object.freeze(SubClass.prototype); // SubClass.prototype을 불변객체로 만든다.
    return SubClass;
};

// Square의 생성자 함수
var Square = extendClass1(Rectangle, function (width) { // 부모 클래스 Rectangle, 자식 메소드
    console.log(this);
    Rectangle.call(this, width, width); // Rectangle 생성자 함수 호출 시 this, width, width를 인자로 전달
    // 여기서 this는 생성자 함수 호출 시 전달할 값. 따로 지정하지 않을 시 null이나 undefined
});
```
상속받을 상위 클래스의 프로퍼티는 지우고, 메서드만 할당한 다음 고정하기

```
var Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
};
Rectangle.prototype.getArea = function () {
  return this.width * this.height;
};
var Square = function (width) {
  Rectangle.call(this, width, width);
};
var Bridge = function () {};
Bridge.prototype = Rectangle.prototype;
Square.prototype = new Bridge();
Object.freeze(Square.prototype);
```
빈 생성자 함수를 만들어 그 프로토타입이 상위 클래스의 프로토타입을 바라보게 한 다음,
하위 클래스의 프로토타입에는 빈 생성자 함수의 인스턴스를 할당.

```
// 빈 생성자 함수(Bridge)를 이용하는 방법 = 생성자 함수는 인스턴스 프로퍼티 초기화를 위한 것이므로.
// var Square = function (width) { // width를 인자로 받아 Rectangle의 생성자 함수를 호출한 다음, 생성자 함수에 대한 인자로 Square 인스턴스와 width를 대입
//     Rectangle.call(this, width, width);
// };
// var Bridge = function () {}; // 빈 생성자 함수
// Bridge.prototype = Rectangle.prototype; // Bridge 프로토타입이 Rectangle 프로토타입을 참조
// Square.prototype = new Bridge(); // Square 프로토타입이 Bridge 인스턴스를 참조
// Object.freeze(Square.prototype); // Square 프로토타입을 불변객체로 만든다.

// // 위를 참고하면
// var extendClass2 = (function () {
//     var Bridge = function () {}; // 생성자 함수로 빈 함수 먼저 할당
//     return function (SuperClass, SubClass, subMethods) {
//         Bridge.prototype = SuperClass.prototype; // 부모 클래스의 프로토타입을 참조
//         SubClass.prototype = new Bridge(); // 자식클래스는 브릿지 인스턴스를 참조
//         if (subMethods) { // 자식 클래스에 추가할 메서드들이 있으면
//             for (var method in subMethods) { // 객체 속성에 대입된 메소드들을
//                 SubClass.prototype[method] = subMethods[method]; // 자식 클래스 프로토타입에 저장
//             }
//         Object.freeze(SubClass.prototype); // 불변객체화
//         return SubClass;
//         }
//     }
// })();
```
즉시실행함수 내부에서 Bridge를 선언한 다음, 이를 클로저로 활용한 경우.

```
// Square.prototype = Object.create(Rectangle.prototype); // Rectangle의 프로토타입을 참조하는 새로운 객체 생성하여 인스턴스를 Sqaure 프로토타입이 참조
// Object.freeze(Square.prototype); // Square 프로토타입을 불변객체화
```
Object.create을 활용한 추상화


### constructor 복구하기
```
var extnedClass1 = function (SuperClass, subClass, subMethods) {
  SubClass.prototype = new SuperClass();
  for (var prop in SubClass.prototype) {
    if (SubClass.prototype.hasOwnProperty(prop)) {
      delete SubClass.prototype[prop];
    }
  }
  SubClass.prototype.constructor = SubClass;
  if (subMethods) {
    for (var method in subMethods) {
      SubClass.prototype[method] = subMethods[method];
    }
}
Object.freeze(SubClass.prototype);
return SubClass;

var extendClass3 = function (SuperClass, SubClass, subMethods) {
  SubClass.prototype = Object.create(Superclass.prototype);
  SubClass.prototype.constructor = SubClass;
  if (subMethods) {
    for (var method in subMethods) {
      SubClass.prototype[method] = subMethods[method];
    }
  }
  Object.freeze(SubClass.prototype);
  return SubClass;
};
```

빈 함수를 이용
```
var extendClass2 = (function () {
  var Bridge = function () {};
  return function (SuperClass, SubClass, subMethods) {
    Bridge.prototype = SuperClass.prototype;
    SubClass.prototype = new Bridge();
    SubClass.prototype.constructor = SubClass;
    Bridge.prototype.constructor = SuperClass;
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
  };
})();
```

Object.create 활용
```
var extendClass3 = function (SuperClass, SubClass, subMethods) {
  SubClass.prototype = Object.create(SuperClass.prototype);
  SubClass.prototype.constructor = SubClass;
  if (subMethods) {
    for (var methods in subMethods) {
      SubClass.prototype[method] = subMethods[method];
    }
  }
  Object.freeze(SubClass.prototype);
  return SubClass;
}
```

