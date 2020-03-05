https://developer.mozilla.org/en-US/docs/Web/API/Event

https://medium.com/@bretdoucette/part-4-what-is-event-delegation-in-javascript-f5c8c0de2983 를 번역

### Event
`Event` 인터페이스는 DOM의 어느 곳에서 이벤트가 시작되는 지를 나타낸다.

이벤트는 마우스 버튼을 클릭하거나 키보드를 두드리거나, 비동기 태스크의 진행을 나타내는 API에 의해 호출되거나 발생한다.

`HTMLElement.click` 처럼 요소에 속한 메소드를 호출함으로써 programmatically하게 호출할 수도 있고, `EventTarget.dispatchEvent()` 처럼 이벤트를 정의하고, 특정한 타깃에 보낼 수도 있다.<br>

여러 종류의 이벤트가 있는데, 몇몇은 `Event` 인터페이스에 기반한다. `Event`는 그 자체로 모든 이벤트에 해당하는 프로퍼티와 메소드를 갖고 있다.

많은 DOM 요소는 이러한 이벤트들을 받아들이거나 "듣고 있도록", 코드를 처리("핸들")하도록 설정할 수 있다.
이벤트 핸들러들은 `EventTarget.addEventListner()`를 통해 여러 HTML elements들에 연결("attatched")되어 있고, 이는 오래된 HTML의 이벤트 핸들러 속성을 대체한다.

더 나아가, 적당히 덧붙여지기만 하면 `removeEventListener()`를 통해 이벤트 핸들러와의 연결을 끊을 수도 있다.

**참고**
하나의 요소는 여러 핸들러를 가질 수 있다. 심지어 완전히 같은 이벤트일지라도, 특별히 별개의 독립적인 코드 모듈들이 핸들러에 붙어 있다면 이는 각각의 목적을 위해서다.
(예를 들어, 웹 페이지의 광고 모듈과 통계 모듈은 영상 시청을 모니터링한다)<br>

중첩된 요소가 많을 경우, 각각이 지닌 핸들러들과 이벤트 처리는 매우 복잡해진다. 특히 부모 요소가 자식 요소와 매우 비슷한 이벤트를 받았을 경우 오버랩되어 동시적으로 일어나기 때문이다. 

그리고 이런 이벤트들의 처리 순서는 이벤트 버블링과 캡처 설정에 의존한다.

### Event Delegation이란
Event Delegation을 이해하려면 먼저 이벤트 리스너를 이해해야 한다.
자바스크립트에서의 이벤트는 **HTML 요소에 일어나는 것들**이라고 정의된다.

흔히 쓰이는 자바스크립트 이벤트들:
1. change:: HTML 요소가 바뀌었을 때
2. click:: HTML 요소 클릭했을 때
3. mouseover:: 마우스를 HTML 요소에 갖다댔을 때
4. mouseout:: 마우스를 HTML 요소로부터 뗐을 때
5. keydown:: 키보드 키를 뗐을 때
6. load:: 브라우저가 페이지를 완전히 로드한 다음

`addEventListener()`
이벤트 리스너를 HTML 요소에 등록하기 위해서 이 메소드를 사용한다.

```
addEventListener() 메소드 예시

const character = document.getElementById("disney-character");
chracter.addEventListener('click', showCharactersName);
```

`document.getElementById`의 첫 번째 인자는 **event target**이다. 여기서 이벤트 타겟은 HTML 요소가 된다. 하지만 자바스크립트에선, 어떤 것도 이벤트 타겟이 될 수 있다. document 내의 HTML 요소는 물론 document 자체가 될 수도 있다(예. 브라우저에 로드된 웹페이지), 혹은 윈도우 창-클라이언트 사이드 자바스크립트에서 모든 것을 둘러싸는 최상위 객체-가 되기도 한다. 하지만 대부분의 유즈 케이스에선 HTML 요소다. 

두 번째 인자가 실제 이벤트 리스너이다.

### 이벤트 리스너는 이렇게 동작한다
유저가 `disney-character`를 id로 갖는 HTML 요소를 클릭하면 이벤트 리스너가 실행되고 `showCharactersName` 함수를 호출한다.

이벤트 리스너는 페이지 로드 시 설정된다. 그러므로 처음에 웹사이트를 열었을 때, 브라우저는 다운로드하고, 읽어들이고, 자바스크립트 코드를 실행한다.

```
const character = document.getElementById("disney-character");
character.addEventListener('click', showCharactersName);
```
위 코드에서, **페이지 로드 시**, 이벤트 리스너는 `disney-character`를 id로 갖는 HTML 요소를 찾아 **클릭 이벤트 리스너**를 해당 HTML 요소에 등록한다.

이는 페이지가 로드되었을 때 해당 요소가 페이지에 존재할 경우 잘 동작한다.
하지만 초기 페이지 로드 이후에 요소가 DOM에 추가된다면 이벤트 리스너에 어떤 일이 발생할까?

## Event Delegation
Event Delegation은 이 문제를 해결한다. Event Delegation을 이해하기 위해서, 우리는 아래 Disney Characters 목록을 볼 필요가 있다.
![alt text](https://miro.medium.com/max/558/1*e8wwVTidL-Wt_4-eN_PY4Q.png)

이 목록은 간단한 기능을 갖고 있다. 캐릭터를 목록에 추가할 수 있고, 이름 옆의 체크박스에 체크할 수 있다.
목록은 동적이기도 하다. input들(Mickey, Minnie, Goofy)는 **초기 페이지 로드 이후**에 추가되고, 그렇기에 등록된 이벤트 리스너도 갖고 있지 않다.

다음의 코드를 살펴보자.
```
const checkBoxes = document.querySelectorAll('input');
checkBoxes.forEach(input => input.addEventListener('click', () => alert('hi!'))
// 알림창은 input을 클릭했을 때 실행된다.
```

하지만 페이지 로드 시의 HTML 마크업을 보자.
```
<ul class="characters">
</ul>
```

이제 페이지 로드 이후의 마크업을 보자. (로컬 스토리지나, api 호출 이후의)
```
ul class=”characters”>
 <li>
   <input type=”checkbox” data-index=”0" id=”item0">
   <label for=”item0">Mickey</label>
 </li>
 
 <li>
   <input type=”checkbox” data-index=”1" id=”item1">
   <label for=”item1">Minnie</label>
 </li>
 
 <li>
   <input type=”checkbox” data-index=”2" id=”item2">
   <label for=”item2">Goofy</label>
 </li>
</ul>
** input들은 페이지가 로드된 이후에 DOM에 위치하게 됐으며 그에 묶인 이벤트 리스너가 없다 
```

input들을 클릭했을 때, 알림창이 뜰 것 같지만 이들은 페이지 로드 시에 존재하지 않았기 때문에, <br>
이벤트 리스너가 바운드되지 않았기 때문에, 결과적으로 아무 일도 일어나지 않는다.

## 그럼... 어떻게 해결할까?
Event Delegation.

input의 변화를 직접 리스닝하고 있는 대신, **페이지가 초기 로드될 때 나타날 HTML 요소를 찾아야 한다**
이 예시에서는 `characters`라는 클래스 이름의 `unordered` 리스트가 페이지 로드시 존재한다.<br>
여기에 이벤트 리스너를 등록하면 된다!
```
<ul class=”characters”> // PARENT - ALWAYS ON THE PAGE
 <li>
   <input type=”checkbox” data-index=”0" id=”char0"> //CHILD 1
   <label for=”char0">Mickey</label>
 </li>
 
 <li>
   <input type=”checkbox” data-index=”1" id=”char1"> //CHILD 2
   <label for=”char1">Minnie</label>
 </li>
 
 <li>
   <input type=”checkbox” data-index=”2" id=”char2"> //CHILD 3
   <label for=”char2">Goofy</label>
 </li>
</ul>
```

```
<ul class="characters">
</ul>

<script>
    function toggleDone (event) {
        console.log(event.target)
    }
    
    const characterList = document.querySelector('.characters')
    characterList.addEventListener('click', toggleDone);
</script>
```

### Console.log(event.target)
이제 input을 페이지 로드 뒤에 클릭했을 때 어떤 일이 일어날까?
![alt text](https://miro.medium.com/max/1650/1*zEg2lVlISrmkA1pjJzW6Lg.png)

event.target은 이벤트가 디스패치된 객체에 대한 참조다. 달리 말하면, 이는 이벤트가 발생한 HTML 요소를 정의한다.
이 케이스에서 발생한 이벤트는 클릭이다. 이벤트가 일어난 객체는 `<input/>`이다.

### Console.log(event.currentTarget)
![alt text](https://miro.medium.com/max/928/1*Dr7TDideY1Tp4KQUiWxbjg.png)

**event.currentTarget**은 DOM을 탐색하면서 이벤트의 현재 타겟을 정의한다. 이는 항상 이벤트 리스너가 붙은 요소를 말한다. 예시에서 이벤트 리스너는 unordered list, `characters`에 붙었으므로,
콘솔에서 확인할 수 있는 내용은 위와 같다.

## 자바스크립트에서 Event Delegation 사용하기
이제 우리는 event.target이 이벤트가 일어난 HTML 요소를 정의한다는 것, 듣고자 하는 요소(input)을 알고 있기 때문에, 이를 자바스크립트로 해결하는 것은 상대적으로 쉽다.

```
// Event Delegation

function toggleDone (event) {
  if (!event.target.matches('input')) return;
  console.log(event.target)
  // 이제 맞는 input을 갖고 있다 - 여기서 노드를 수정하면 된다
}
```
기본적으로 위 코드는 클릭된 event target이 `input` 요소가 아닐 시 함수를 빠져나간다.
이제는 초기 페이지 로드 이후 DOM에 추가된 input에 대해 유저가 맞는 자식 노드를 클릭했다는 데 자신감을 가질 수 있다.

## Event Bubbling
왜 event delegation이 동작하는지 깊이 이해하려면, **Event Bubbling**을 이해할 필요가 있다.

### 클릭했을 때 실제로 어떤 일이 일어나는가?
유저가 클릭하면, DOM의 최상단까지 거슬러 올라가며 클릭한 해당 요소의 모든 부모 요소에 등록된 클릭 이벤트를 트리거한다. 항상 이 클릭을 보는 건 아니다, 항상 이 요소에 붙은 이벤트 리스너가 클릭을 리스닝하고 있지는 않기 때문에. 하지만 버블링은 일어난다.

## event bubbling 혹은 event propagation
이러한 버블링 특성으로 인해, event propagation은 언제라도 DOM의 input들 중 하나를 클릭했을 때, 효과적으로 전체 document body를 클릭하는 것을 의미하게 된다.

```
<div class="one">
    <div class="two">
        <div class="three">
        </div>
    </div>
</div>

<script>
    const divs =document.querySelectorAll('div')
    
    function logClassName(event) {
        console.log(this.classList.value);
    }
    
    divs.forEach(div => div.addEventListener('click', logClassName));
</script>
```

위에서 세 개의 div는 각기 이벤트 리스너를 갖고 있다. 브라우저에서 div를 클릭하면, `console.log`에
`logClassName()`을 실행한 클래스 이름을 알 수 있다.

![alt text](https://miro.medium.com/max/1219/1*LVpJlIinlF3tx3S7O3EPTA.gif)

위에서 보듯, div3을 클릭했는데도 div2와 div1도 콘솔에 찍히는 것을 확인할 수 있는데, 이것이 이벤트 버블링이다.

![alt text](https://miro.medium.com/max/490/1*ImxlC10hU2YCx3A8XKla7w.png)

### Event Delegation 예시로 돌아가기
```
<ul class=”characters”> // PARENT -- This is where the listener is!
 <li>
   <input type=”checkbox” data-index=”0" id=”char0"> //CHILD 1
   <label for=”char0">Mickey</label>
 </li>
 
 <li>
   <input type=”checkbox” data-index=”1" id=”char1"> //CHILD 2
   <label for=”char1">Minnie</label>
 </li>
 
 <li>
   <input type=”checkbox” data-index=”2" id=”char2"> //CHILD 3
   <label for=”char2">Goofy</label>
 </li>
</ul>
<script>
  const characterList = document.querySelector('.characters');
  characterList.addEventListener('click', toggleDone);
</script>
```
예제 코드로 돌아가 보면, `characters`라는 unordered list에 오직 한 개의 이벤트 리스너가 등록돼있다.
하지만 부모 HTML 요소를 클릭할 때, 그 요소가 `input`이면, 이벤트 리스너가 호출된다.

이벤트 버블링 때문에 자식 HTML 요소 상위의 부모 요소에도 이벤트 리스너를 등록할 수 있다. 그리고 이벤트 리스너는 *어떤 자식 노드에라도 이벤트가 일어날 때마다 실행된다. 초기 페이지 로드 이후에 추가된 자식 노드일 지라도!*

### 결론, 왜 Event Delegation을 사용할까?
Event delegation(이벤트 위임)하지 않으면 새로이 로드된 각각의 input에 `click` 이벤트를 다시 바인드해줘야 한다. 이를 일일히 코딩하는 것은 복잡하고 번거롭다.<br>

첫째로, 이는 엄청나게 많은 이벤트 리스너를 양산할 것이고, 이벤트 리스너들은 전체 메모리 footprint를 증가시킬 것이다. 메모리 footprint가 커질 수록 성능은 감소한다

둘째로, DOM에서 요소를 제거하면서 이벤트 리스너들을 바인드하고 다시 바인드하는 행위는 메모리 누수와 연관이 있다. 

