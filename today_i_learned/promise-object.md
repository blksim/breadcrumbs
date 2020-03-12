https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise의 번역.

> A Promise is a proxy for a value not necessarily known when the promise is created. 
It allows you to associate handlers with an asynchronous action's eventual success value or failure reason. 
This lets asynchronous methods return values like synchronous methods: instead of immediately returning the final value, 
the asynchronous method returns a promise to supply the value at some point in the future.

사용 목적
비동기 메서드를 상태별로 - 동기적으로 컨트롤하기 위해 사용되는 매개체(프록시)가 프로미스 객체다.

>A Promise is in one of these states:

pending: initial state, neither fulfilled nor rejected.
fulfilled: meaning that the operation completed successfully.
rejected: meaning that the operation failed.
A pending promise can either be fulfilled with a value, or rejected with a reason (error). 
When either of these options happens, the associated handlers queued up by a promise's then method are called. 
(If the promise has already been fulfilled or rejected when a corresponding handler is attached, the handler will be called, 
so there is no race condition between an asynchronous operation completing and its handlers being attached.)

As the Promise.prototype.then() and Promise.prototype.catch() methods return promises, they can be chained.

프로미스 객체는 세 가지 상태 중 한 번에 하나만을 지니기 때문에 경합할 일이 없다
pending, fullfilled, rejected 세 가지 상태에 따라 큐에 대기하고 있다던 핸들러 메소드가 호출됨

> Not to be confused with: Several other languages have mechanisms for lazy evaluation and deferring a computation, 
> which they also call "promises", e.g. Scheme. Promises in JavaScript represent processes which are already happening, 
> which can be chained with callback functions. If you are looking to lazily evaluate an expression, 
> consider the arrow function with no arguments: f = () => expression to create the lazily-evaluated expression, and f() to evaluate.

> Note: A promise is said to be settled if it is either fulfilled or rejected, but not pending. 
> You will also hear the term resolved used with promises — this means that the promise is settled or “locked in” to match the state of another promise. 
> States and fates contains more details about promise terminology.

프로미스는 fullfilled나 rejected이면서 pending 상태가 아닐 때 완수되었다고 본다.
해결되었다는 용어도 접하게 될 건데 - 이는 프로미스가 완수되었거나, 다른 프로미스의 상태를 확인하기 위해 락이 걸렸다는 의미다

.... 단점은 ....
