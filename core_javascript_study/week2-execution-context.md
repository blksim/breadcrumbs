참고 링크</br>
https://hackernoon.com/javascript-v8-engine-explained-3f940148d4ef
https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec
https://blog.kevinchisholm.com/javascript/difference-between-scope-and-context/
https://towardsdatascience.com/javascript-context-this-keyword-9a78a19d5786
http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/
https://stackoverflow.com/questions/23948198/variable-environment-vs-lexical-environment


### 실행 컨텍스트란?
- 쉽게 말해, 코드가 실행되는 앞뒤 맥락이자 코드가 실행되기 위한 주변 환경 정보를 모아 놓은 객체
- 아. 키와 값으로 뭔가 정보가 저장돼있고, 값이 바뀔 수 있고, 여러 개가 존재하겠구나
- 자바스크립트 엔진이 내가 짠 코드를 순서에 맞게 실행하기 위해서 만들어 둔 거구나
- 이 객체가 1) 언제 생성되는지 / 2) 어떤 정보 담고 있는지 /  3) 코드 실행 순서에 어떤 영향을 주는 지 알면 되겠군?

이 객체를 콜 스택에 쌓아 올린 다음, 맨 위에 있는 컨텍스트와 관련 있는 코드부터 실행
### 콜 스택의 위치
		- 자바스크립트 엔진(V8)의 메모리 구조
				- Memory Heap(메모리 힙) / Call Stack(호출 스택)
				- 메모리 힙에서 메모리 공간 관리를 한다. 
				  변수와 객체에 대한 공간 할당이 여기에서 일어남.
				- 호출 스택에서는 호출 순서에 따라 실행될 함수 순서를 갖고 있다.

### 콜 스택은 그래서 뭘까?
엔진에서 함수 실행 순서를 내부적으로 결정하기 위해, 앞으로 실행해야 할 함수를 스택 구조로(아래->위 순서) 쌓아둔 목록이다.
그러니까, 어떤 함수a를 호출하려는데 그 함수 내부를 봤더니 안에 또 다른 함수b가 호출돼야 한다면, a의 실행이 완료되기 위해 b의 실행이 선행되어야 하므로, 스택 맨 위에 b를 먼저 실행하기 위한 실행 컨텍스트 객체를 올리는 식이다. -> 실행 컨텍스트 수 : 전역 포함 세개

### 왜 큐가 아니고 스택일까?
큐는 선입선출, 스택은 선입후출
엔진에서 큐도 함께 사용한다 : 처리될 메시지 목록 + 관련 콜백 함수들 목록 담겨있음
스택에 충분한 공간만 있다면, 함수가 실행될 때 메시지큐에 담긴 콜백 함수에 대한 실행 컨텍스트도 마찬가지로 스택에 쌓이고 실행이 완료되면 스택, 큐에서 제거된다.


1) 언제 생성되는지 : 
- 엔진이 처음 자바스크립트 파일 읽어들일 때 전역 컨텍스트가 생성됨 
- 이후 코드 흐름에 의해 함수 호출 시 실행 컨텍스트가 생성됨
- 내부 함수 호출 시 새로운 실행 컨텍스트가 생성됨
- 항상 최근의 실행 컨텍스트부터 실행하며, 함수 실행 완료 시 컨텍스트 객체를 스택에서 제거하고, 제어권을 바로 밑에 깔린 컨텍스트에 반환한다.

2) 어떤 정보 담고 있는지 : VariableEnvironment, LexicalEnvironment, ThisBinding
- 앞의 두 Environment는 각각 environmentRecord, outerEnvironmentReference를 담고 있음 (뒤에서 계속)

3) 코드 실행 순서에 언제 어떻게 영향을 주나 : 
- 엔진에서 코드 한 줄 읽는다 -> 함수를 호출하는 코드를 발견한다 -> 코드를 실행하기 전에, 실행하는 데 필요한 정보를 담기 위해 실행 컨텍스트 객체를 생성해 스택에 쌓는다(Creation Stage : VariableEnvironment, LexicalEnvironment 생성) -> 함수가 실행된다(Activation / Code Execution Stage)<br>
**Creation Stage**
= 함수 호출은 됐는데, 아직 함수 내부의 어떤 코드도 실행하지 않은 단계
- 스코프 체인 생성
- 변수, 함수, 인자 생성
- “this” 값 결정
**Activation / Code Execution Stage**
= 변수 값, 함수에 대한 참조값을 할당하고, 코드를 해석 / 실행한다.

### VariableEnvironment vs LexicalEnvironment
공통점 : 둘 다 같은 정보를 갖고 있음(environmentRecord, outerEnvironmentReference)<br>
차이점 : VariableEnvironment는 LexicalEnvironment 중 한 타입에 가깝다. 실행 컨텍스트의 특정한 시점에 대한 정보만 가지고 있음

### environmentRecord 
environmentRecord에는 현재 컨텍스트와 관련된 코드의 식별자 정보가 들어있음(매개변수 이름, 함수 선언, 변수명 등)
실행 컨텍스트 생성 시 내부를 처음부터 끝까지 훑어나가며 순서대로 식별자 정보를 VariableEnvironment에 먼저 담은 다음 복사해서 LexicalEnvironment를 만든다

**호이스팅**
- 코드 실행하기 전에 엔진이 변수명을 모두 알고 있는 상태를 ‘끌어올림’이라는 가상적 개념으로 설명
- 변수는 선언부만(할당 부분은 그대로 둠), 선언된 함수도 호이스팅 대상.

**함수 선언문 vs 함수 표현식**
- 함수 선언문은 선언부 / 구현부만 있고, 표현식은 그것을 변수에 할당한 식. 익명함수라고도 함
- 전역 컨텍스트가 활성화될 경우 전역에 선언한 함수들이 호이스팅되는데, 이 때 함수명이 동일할 경우 맨 마지막에 선언한 함수에 값이 할당된다. 
- 전역 스코프에 함수 선언하거나, 동명의 함수를 중복 선언하지 않아야 하는 이유.

### outerEnvironmentReference
#### 스코프체인
- 식별자에 대한 스코프(유효범위)를 안에서 바깥으로 식별자 검색해 나가기 위한 상호 의존 관계에 있는 사슬
- 검색을 위한 근거 자료가 LexicalEnvironment의 **outerEnvironmentReference**이다.
- 즉 현재 호출 함수가 선언될 당시의 LexicalEnvironment를 참조한다.
- 무슨 말이냐면, 변수나 함수 선언 시  environmentRecord에서 그 식별자 정보를 갖고 있으니, 이를 담고 있는 LexcicalEnvironment가 어디 있는지 알기만 하면(outerEnvironmentReference) 현재 함수 내부의 식별자를 검색할 수 있다는 말<br>
**변수 은닉화** : 전역 공간, 함수 내부에 같은 이름의 변수가 선언된 상황에서, 스코프체인에 따라 가장 좁은 스코프(함수 내부)의 LexicalEnvironment부터 검색하기에 동명의 ‘전역’ 변수에는 접근할 수 없게 되는 것<br>
**전역변수와 지역변수**
함수 내부에서 선언하면 지역변수, 전역 공간에서 선언하면 전역변수

...업데이트 중..
