### 유닛 테스트에서 목 객체(Mock Object)를 사용하는 이유
>In simple English, Mocking is make a replica or imitation of something.
어떤 객체가 다른 객체에 대한 의존을 갖는 경우 -> 고립된 테스트를 하기가 어려워진다 = 테스트가 외부 요인으로 인해 오염된다 -> 그래서 실제 객체가 할 행위를 그대로 복제한 모조 객체를 만들어서, 
테스트 고립을 깨지 않고(다른 객체의 영향을 최소화한 상태에서) 의도대로 기능이 동작하는 지 테스트할 수 있다.

>In unit testing we want to test methods of one class in isolation. But classes are not isolated. They are using services and methods from other classes.
..... some mocking frameworks and use that mocked methods and services todo unit testing in isolation. There is where Mocking frameworks come into play.

비슷한 개념들
* A dummy object : 인스턴스화는 되지만 메소드는 호출되지 않는. 메소드 파라미터를 채워넣기 위한 객체같은.
* Fake object : 동작하도록 구현돼있지만, 단순화되어있는 것들. 실제 데이터베이스 대신 메모리 데이터베이스를 쓴다거나.
* A stub class : 테스트 중에 해당 클래스의 인스턴스를 사용할 목적으로, 부분적으로 인터페이스나 클래스를 구현한 것.
* A mock object : 특정 메소드 호출의 결과로 구현된 더미 인터페이스나 클래스. 목 객체는 테스트 동안 특정한 행위를 하도록 정해져있다.

### Using Mockito for mocking objects
Mockito는 외부 의존성과 함께 클래스를 테스트할 수 있도록 개발 절차를 매우 심플하게 만들어 줌.
테스트 중 Mockito를 사용하게 되면:
* 테스트 중 외부 의존성이 필요한 자리를 mock dependencies가 대체함
* 테스트 코드를 실행함
* 코드가 맞게 실행되었는지 검증 가능


https://medium.com/@piraveenaparalogarajah/what-is-mocking-in-testing-d4b0f2dbe20a
https://www.vogella.com/tutorials/Mockito/article.html#mock-object-generation


