참고자료
https://en.wikipedia.org/wiki/Dependency_injection#Types_of_dependency_injection<br>
https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/<br>
https://www.edureka.co/blog/what-is-dependency-injection/<br>
https://medium.com/better-programming/the-3-types-of-dependency-injection-141b40d2cebc


### Dependency Injection
> In software engineering, dependency injection is a technique whereby one object supplies the dependencies of another object. 
> A "dependency" is an object that can be used, for example as a service. Instead of a client specifying which service it will use, something tells the client what service to use. The "injection" refers to the passing of a dependency (a service) into the object (a client) that would use it. 
> The service is made part of the client's state.[1] Passing the service to the client, 
> rather than allowing a client to build or find the service, is the fundamental requirement of the pattern.

### 기본 컨셉
class A가 class B의 기능 일부를 사용한다 = class A가 class B에 dependency를 갖고 있다고 한다.
자바로 예를 들면, 이 경우 위의 기능을 사용하기 위해서는 class A가 class B의 인스턴스도 생성해야 한다.
이렇게 class A가 의존 관계에 있는 class B 객체의 인스턴스를 생성하는 일을 누군가에게 대신 시키고 바로 기능을 사용할 수 있게끔 하는
작업을 'dependency injection'이라고 한다. 의존 주입. '누가' '뭘 통해서' 주입하는 지가 중요.
![alt text](https://cdn-media-1.freecodecamp.org/images/1*TF-VdAgPfcD497kAW77Ukg.png)

### 3종류의 DI
1. Constructor Injection
의존성들이 클래스 생성자를 통해 전달된다.
```
class BankingService {}

class PayrollSystem {
  private _BankingService: BankingService;

  constructor(aBankingService: BankingService) {
    this._BankingService = aBankingService;
  }
}
```
PayrollSystem의 객체의 인스턴스를 초기화하려면 BankingService 인스턴스도 필요하다.

> You should use constructor injection when your class has a dependency that the class requires in order to work properly. **If your class cannot work without a dependency, then inject it via the constructor.** If your class needs three dependencies, then demand all three in the constructor.

> Additionally, you should use constructor injection when the dependency in question has a lifetime longer than a single method. Dependencies passed into the constructor should be useful to the class in a general way, **with its use spanning multiple methods in the class. If a dependency is used in only one spot, method injection (covered below) might be a better choice.**

2. Setter Injection
클라이언트가 setter 메소드를 노출함으로써 주입하는 쪽이 그곳으로 전달할 수 있도록 한다.

3. Interface Injection
의존성이 주입자 역할을 하는 메소드를 전달해서, 어떤 클라이언트든 이 메소드를 거치도록 한다. 
클라이언트는 반드시 인터페이스를 구현하고 의존을 받기 위한 setter 메소드를 노출시켜야 한다.
```
interface IFoodPreparer {
    prepareFood(aRecipe: Recipe);
  }

  class Baker implements IFoodPreparer {
    prepareFood(aRecipe: Recipe) {
      console.log('Use baking skills to do the following: ' + aRecipe.Text);
    }
  }

  class ShortOrderCook implements IFoodPreparer {
    prepareFood(aRecipe: Recipe) {
      console.log('Use the grill to do the following: ' + aRecipe.Text);
    }
  }

  class Chef implements IFoodPreparer {
    prepareFood(aRecipe: Recipe) {
      'Use well-trained culinary skills to prepare the following: ' +
        aRecipe.Text;
    }
  }

```

### 장점
- unit 테스트 용이
- 보일러플레이트 코드 감소한다. 의존 초기화를 주입전용 컴포넌트에 맡기면 되니까.
- 애플리케이션 확장 용이
- 코드간 결합도를 낮춰준다.

### 참고
https://www.martinfowler.com/articles/injection.html<br>
https://www.journaldev.com/2394/java-dependency-injection-design-pattern-example-tutorial
