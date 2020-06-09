참고 링크
https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%97%94%EC%A7%84
https://blog.sessionstack.com/how-javascript-works-the-rendering-engine-and-tips-to-optimize-its-performance-7b95553baeda


자바스크립트는 독립적으로 돌아가는 게 아니라, 주변 환경과 상호작용하므로 그 환경을 이해하는 것은 매우 중요
https://miro.medium.com/max/1311/1*lMBu87MtEsVFqqbfMum-kA.png

* 유저 인터페이스 <= 말단 유저와의 접점
address bar, back and forward buttons, bookmarking menu, etc
웹 페이지 그 자체에서 창을 제외하고 브라우저를 구성하는 모든 것

* 브라우저 엔진 <= UI와 렌더링 엔진 사이의 상호작용을 핸들링한다
* 렌더링 엔진 <= HTML과 CSS를 파싱해서 웹 페이지에 보여준다.
* networking <= XHR 요청과 같은 network call 등이 여기에 속한다. 
* UI 백엔드 <= 체크박스, 창과 같은 코어 위젯을 그리기 위해 사용되는 (플랫폼 종속저깅지 않은) 범용 인터페이스. 내부적으로는 OS의 UI 메소드들을 사용한다.
* 자바스크립트 엔진 <= 자바스크립트 코드가 실행되는 곳
* Data persistence <= 데이터를 로컬에 저장하고 싶을 경우, localStorage, indexDB, WebSQL 그리고 FileSystem과 같은 스토리지를 지원

이 글은 이 중 렌더링 엔진에 관한 글임.<br>

### Rendering engine
렌더링 엔진이 하는 일 = 요청받은 페이지를 브라우저에 보여주는 것
렌더링 엔진은 HTML과 XML 문서와 이미지를 보여줄 수 있다. 부가적인 플러그인을 사용한다면, PDF와 같은 다른 종류의 문서도 보여준다.

유명한 렌더링 엔진에는 Gecko(Firefox), Webkit(Safari), Blink(Chrome, Opera)가 있다.

### 렌더링 과정
HTML을 DOM 트리로 변환한다 -> 렌더 트리를 구성한다 -> 렌더 트리를 구조화한다 -> 렌더 트리를 그린다(paint)

#### Constructing the DOM tree
HTML 문서를 DOM 트리 내 DOM 노드로 변환한다.

#### Consturcting the CSSOM tree
CSS 객체 모델. 브라우저가 DOM을 구성하는 동안, 외부 CSS 스타일 시트를 참고하는 link나 head를 만난다. 왜 CSSOM도 트리 구조를 띌까? 페이지의 마지막 스타일 셋을 변환할 때, 브라우저는 노드에 적용 가능한 가장 일반적인 규칙부터 재귀적으로 좀 더 구체적인 규칙을 다듬는다.

#### Constructing the render tree
HTML 내부의 시각적인 지침이 CSSDOM 트리의 스타일링 데이터와 합쳐져 렌더 트리를 만드는 데 쓰인다.<br>
렌더 트리를 생성하기 위해서, 브라우저는 DOM 트리의 최상단으로부터 보이는 노드를 순차적으로 탐색한다. 결국 렌더 트리는 시각적인 요소를 화면해 표시할 순서에 맞게 구성해둔 트리. 컨텐츠를 순서에 맞게 페인팅하기 위함.

> To construct the render tree, the browser does roughly the following:
Starting at the root of the DOM tree, it traverses each visible node. Some nodes are not visible (for example, script tags, meta tags, and so on), and are omitted since they are not reflected in the rendered output. Some nodes are hidden via CSS and are also omitted from the render tree. For example, the span node — in the example above it’s not present in the render tree because we have an explicit rule that sets the display: none property on it.
For each visible node, the browser finds the appropriate matching CSSOM rules and applies them.
It emits visible nodes with content and their computed styles

#### Layout of the render tree
렌더러가 생성돼서 트리에 추가됐더라도, 위치와 사이즈는 갖고 있지 않다. 이러한 값들을 측정하는 것을 layout이라 부른다.<br>
HTML은 흐름 기반 레이아웃 모델을 사용하는데, 이는 대부분의 시간 동안 단일 경로 안에서 geometry를 변환한다는 걸 의미한다. <br>
Layout은 재귀적인 프로세스다. root renderer - <html>요소와 상응하는 - 에서 시작한다. Layout은 부분 혹은 전체 renderer 위계에 따라 재귀적으로 수행되며, 각 renderer에 대한 geometric 정보 변환이 일어난다. 
root renderer의 위치는 0, 0이고 이 dimensions는 브라우저 창 중 보이는 부분의 사이즈를 갖고 있다. (a.k.a view port)<br>
layout을 시작한다는 의미는 각각의 노드에 화면 위에서 어떻게 보여야 하는 지 정확한 coordinates를 제공한다는 것을 말한다.<br>


#### Painting the render tree
이 단계에서는 화면에 내용을 표시하기 위해 renderer 트리가 탐색되면서 renderer의 paint() 메소드가 호출된다.<br>

Painting은 global 혹은 incremental하게 이루어진다.<br>
* Global - the entire tree gets repainted<br>
* Incremental - 몇몇 renderer만 전체 트리에 영향을 주지 않는 방식으로 변경된다.<br>  
>The renderer invalidates its rectangle on the screen. This causes the OS to see it as a region that needs repainting and to generate a paint event. The OS does it in a smart way by merging several regions into one.<br>

#### Order of processing scripts and style sheets
태그를 만나면 스크립트가 즉시 변환되고 실행된다. document 변환은 스크립트 실행이 끝날 때까지 멈춘다. 이는 이 과정이 동기적임을 의미한다.
  
만약 스크립트가 외부 자원이면 먼저 네트워크에서 마찬가지로 동기적으로 내려받는다. 모든 변환 작업은 내려받기가 끝날 때까지 멈춘다.

HTML5는 스크립트를 비동기적으로 mark하는 옵션을 추가함으로써 변환과 실행을 다른 스레드에서 가능하게 했다.

#### Optimizing the rendering performance
만약 앱 최적화에 관심이 있다면, 다음의 다섯 가지 영역에 집중하라. 
1. JavaScript - 렌더링 시 자바스크립트 코드가 DOM 요소와 어떻게 상호작용하는 방법에 대해 생각해봐야 한다. 자바스크립트는 UI에 많은 변화를 줄 수 있다. 특히 SPA에선

2. Style calculations - 매칭되는 선택자에 어떤 CSS 규칙을 적용할 지 결정하는 과정을 말한다. 규칙이 정의될 때, 계산된 각각의 요소에 적용되며 가장 마지막 스타일도 계산된다.

3. Layout - 브라우저가 요소에 어떤 규칙을 적용할 지 아는 때에, 얼마나 많은 공간이 브라우저 스크린에서 필요한 지도 계산할 수 있다. 웹에서의 레이아웃 모델은 한 요소가 다른 요소들에 주는 영향을 정의한다. 예를 들어, <body>의 너비는 자식 요소의 너비에 영향을 미친다. 이는 모든 layout 과정이 computationally intensive함을 의미한다. 여러 레이어에 걸쳐 drawing이 이루어진다.
  
4. Paint - 실제 픽셀이 채워지는 단계다. 문자, 색, 이미지, 테두리, 그림자 등 각 요소의 모든 시각적 부분이 포함된다.

5. Compositing - 페이지가 여러 개의 layer로 구성되고 스크린 위에서 정확한 순서에 따라 놓여야 제대로 렌더링되기 때문이다. 이는 요소를 서로 겹칠 때 매우 중요하다.

### Optimizing your JavaScript
자바스크립트는 브라우저의 시각적 변화를 자주 유발한다. 특히 싱글 페이지 애플리케이션일 때.<br>

* 시각적 업데이트를 위해 setTimeout이나 setInterval을 호출하는 것을 피하라. 이는 frame 의 어느 시점에서(거의 끝에서) 콜백을 호출한다. 우리가 원하는 건 frmae의 시작 지점에서 시각적 변화가 일어나는 것이다.

* Web worker의 사용을 고려

* 여러 프레임에 걸쳐 일어나는 DOM 변화일 경우 작은 단위의 태스크로 쪼개는 게 좋다. 


### Optimize your CSS

요소의 추가 삭제, 속성 변경을 통한 DOM 조작은 그게 아주 작은 일부일 지라도 브라우저가 요소 스타일과, 전체 페이지의 레이아웃을 다시 계산하도록 만든다.

렌더링 과정을 최적화하려면,
* Selector complexity를 줄여라. 이는 요소의 스타일을 계산하는 데 걸리는 시간을 50% 이상 줄여준다. 스타일 자체를 생성하는 데 걸리는 남은 시간과 비교하면.

* 스타일 계산이 반드시 일어나야 할 요소를 줄인다. 결국 일부 요소에서 일어나는 스타일 변화는 곧 전체 페이지를 invalidate하는 것 이상이다.

### Optimize the layout
Layout을 재계산하는 것은 브라우저에게 매우 부담스러운 작업이다.
* layout 수를 가능한 줄인다. style을 변경 시 브라우저는 layout이 재계산돼야 하는 변화인지 확인해야 한다. 너비, 높이, 좌우, 등 geometry에 관련된 속성값의 변화는 layout 작업을 필요로 한다. 그러므로 가능한 변화시키지 않는 게 좋다.

* old layout model에 대한 flexbox 사용은 더 빠르고 성능 개선의 이점이 있다.

* 강제 동기 layout을 피하기. 자바스크립트 코드가 실행되는 동안 이전 프레임의 old layout 값에 접근할 수 있다. 만약 접근할 수 있게 되기 전에 스타일이 변경된다면(요소에 동적으로 CSS 클래스를 추가하는 등의) 브라우저는 먼저 스타일을 변경한 다음에 layout을 한다. 이는 매우 시간이 걸리고 리소스를 소모하므로 가능한 피하는 게 좋다.

### Optimize the paint
이는 가장 오래 걸리는 일이므로 빈도를 낮출 수록 좋다.

* Changing any property other than transforms or opacity triggers a paint. 

* layout을 유발했다면, 당연히 paint도 일어날 것. geometry 정보에 변화가 있을 경우 결과적으로 요소에 시각적 변화가 일어난다.

* layer promotion과 animation orchestration을 통해 paint되는 범위를 줄일 것


