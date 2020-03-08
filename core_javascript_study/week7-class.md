### 클래스와 인스턴스의 개념 이해
정확히 말해서 클래스 개념도 없고 상속도 없다.
비슷하게 흉내낼 뿐이다. ---> ES6의 클래스. 내부적으로는 프로토타입을 활용

실생활에서의 클래스-인스턴스 비유:
클래스는 추상개념 = 상위개념 = 공통속성을 모았을 뿐 실재하지 않는다
인스턴스는 클래스의 속성을 물려받아서 상대적으로 구체적인 개념
인스턴스는 클래스의 속성을 지니는, 실존하는 개체 = 특정 조건에 부합하는 구체적인 예시

### 자바스크립트와 클래스
클래스도 상속도 존재하지 않지만, 비슷하게 해석될 수 있는 경우가 있다.
예를 들어, Array를 new 연산자와 함께 호출하면 인스턴스가 생성되는데,
Array의 prototype에 접근할 수 있게 되었으므로 상위객체의 속성을 상속했다고도 볼 수가 있음. 사실은 프로토타입 체이닝에 의한 참조지만.

인스턴스의 참조 여부에 따라 스태틱, 인스턴스 멤버로 나누는데
**인스턴스에서도 직접 메서드를 정의할 수 있기 때문에** 혼란을 야기.
인스턴스 메서드 대신 '프로토타입 메서드'라고 부르는 게 더 보편적이다.

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

### ES6의 클래스 및 클래스 상속
