https://developer.mozilla.org/en-US/docs/Web/API/Event
https://medium.com/@bretdoucette/part-4-what-is-event-delegation-in-javascript-f5c8c0de2983 를 번역<br>

### Event
`Event` 인터페이스는 DOM의 어느 곳에서 이벤트가 시작되는 지를 나타낸다.

이벤트는 마우스 버튼을 클릭하거나 키보드를 두드리거나, 비동기 태스크의 진행을 나타내는 API에 의해 호출되거나 발생한다.
`HTMLElement.click` 처럼 요소에 속한 메소드를 호출함으로써 programmatically하게 호출할 수도 있고, `EventTarget.dispatchEvent()` 처럼 이벤트를 정의하고, 특정한 타깃에 보낼 수도 있다.<br>

여러 종류의 이벤트가 있는데, 몇몇은 `Event` 인터페이스에 기반한다. `Event`는 그 자체로 모든 이벤트에 해당하는 프로퍼티와 메소드를 갖고 있다.<br>
많은 DOM 요소는 이러한 이벤트들을 받아들이거나 "듣고 있도록", 코드를 처리("핸들")하도록 설정할 수 있다.
이벤트 핸들러들은 `EventTarget.addEventListner()`를 통해 여러 HTML elements들에 연결("attatched")되어 있고, 이는 오래된 HTML의 이벤트 핸들러 속성을 대체한다.
더 나아가, 적당히 덧붙여지기만 하면 `removeEventListener()`를 통해 이벤트 핸들러와의 연결을 끊을 수도 있다.

**참고**
하나의 요소는 여러 핸들러를 가질 수 있다. 심지어 완전히 같은 이벤트일지라도, 특별히 별개의 독립적인 코드 모듈들이 핸들러에 붙어 있다면 이는 각각의 목적을 위해서다.
(예를 들어, 웹 페이지의 광고 모듈과 통계 모듈은 영상 시청을 모니터링한다)<br>

중첩된 요소가 많을 경우, 각각이 지닌 핸들러들과 이벤트 처리는 매우 복잡해진다. 특히 부모 요소가 자식 요소와 매우 비슷한 이벤트를 받았을 경우 
오버랩되어 동시적으로 일어나기 때문이다. 그리고 이런 이벤트들의 처리 순서는 이벤트 버블링과 캡처 설정에 의존한다.

......업데이트중....
### Event Delegation이란?

### 왜 Event Delegation을 사용할까?
event delegation(이벤트 위임)하지 않으면 새로이 로드된 각각의 input에 `click` 이벤트를 다시 바인드해줘야 한다. 이를 일일히 코딩하는 것은 복잡하고 번거롭다.<br>
첫째로, 이는 엄청나게 많은 이벤트 리스너를 양산할 것이고, 이벤트 리스너들은 전체 메모리 footprint를 증가시킬 것이다. 메모리 footprint가 커질 수록 성능은 감소한다
둘째로, DOM에서 요소를 제거하면서 이벤트 리스너들을 바인드하고 다시 바인드하는 행위는 메모리 누수와 연관이 있다. 
