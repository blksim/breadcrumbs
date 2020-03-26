개발한 웹 애플리케이션에 대한 유닛 테스트를 작성 중인데
유닛테스트, 목 객체의 사용 목적에 대해 충분히 인지하지 못한 채 일단 신택스, 테스트를 짜고 있는 느낌적인 느낌에 치중하고 있는 느낌이라 다시 개념 정리

https://stackoverflow.com/questions/48711883/what-is-the-point-in-unit-testing-mock-returned-data

> Goal of unit-testing is to test the functionality without connecting to any external systems. 
If you are connecting to any external system, that is considered **integration testing**.
**Mocking an object** helps in creating mock objects that cannot be created during unit-testing, 
and testing behavior/logic based on what the mocked object (or real object when connecting to external system) data is returned.

> Mocks are structures that simulate behaviour of external dependencies that you don't/can't have or which can't operate properly in the context of your test, 
because they depend on other external systems themselves (e.g. a connection to a server). 
Therefore a test like you've described is indeed not very helpful, 
because you basically try to verify the simulated behaviour of your mocks and nothing else.

https://www.brandonsavage.net/dont-write-useless-unit-tests/
테스트 샘플은 링크 안에

> This test will in fact provide us with 100% code coverage of the getAllUsers() method. 
But unfortunately, for any practical purpose, this unit test is completely useless.

> This unit test is useless because instead of testing the interworkings of the getAllUsers() method, 
it’s actually testing Mockery. From the first lines of the test, we’re creating mocks of PDO and PDOStatement, 
and we’re passing those mocks into the Users class. What we’re really testing is that Mockery properly returns an array; 
we never touch the database.

> If getAllUsers() actually *did* something, like processed the users in some way, 
it would be worth mocking and testing appropriately for the various conditions of the algorithm. That is not the case here.

> Too often, in the search for 100% unit test code coverage, I see tests like this get written. 
They don’t serve a practical purpose, except to meet the test coverage goal. 
**Worse, they don’t actually improve the quality of the application.**

> Instead of writing a unit test here, we would be better served by writing an integration test, or a functional test. 
These tests would require us to interact directly with the database, but would provide far more valuable information about the health and status of our application. 
A useless unit test provides us with little if any benefit; a useful functional test provides us with tremendous advantages.

persistence layer 접근 메소드 하나밖에 없고 verify를 이용한 **호출 여부나 횟수** 한 가지밖에 확인할 수 없는 서비스 메소드에 대한 유닛 테스트를 작성하다가 뼈맞았다.
뭘 테스트하는 지(해야될 지), 더 나은 방법이 있는 지 고민하고 실행하기

읽어보기
https://www.slideshare.net/homespothq/unit-testing-concepts-and-best-practices
