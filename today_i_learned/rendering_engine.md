참고 링크
https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%97%94%EC%A7%84
https://blog.sessionstack.com/how-javascript-works-the-rendering-engine-and-tips-to-optimize-its-performance-7b95553baeda

항상 js 엔진을 빠삭하게 파야 겠다고 생각했는데 좋은 글을 발견...<풀심기 강박>
글은 밝을 때 마저 업데이트.

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

> o construct the render tree, the browser does roughly the following:
Starting at the root of the DOM tree, it traverses each visible node. Some nodes are not visible (for example, script tags, meta tags, and so on), and are omitted since they are not reflected in the rendered output. Some nodes are hidden via CSS and are also omitted from the render tree. For example, the span node — in the example above it’s not present in the render tree because we have an explicit rule that sets the display: none property on it.
For each visible node, the browser finds the appropriate matching CSSOM rules and applies them.
It emits visible nodes with content and their computed styles

#### Layout of the render tree
렌더러가 생성돼서 트리에 추가됐더라도, 위치와 사이즈는 갖고 있지 않다. 이러한 값들을 측정하는 것을 layout이라 부른다.





