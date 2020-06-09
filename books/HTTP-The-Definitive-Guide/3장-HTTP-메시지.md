### 3.1 메시지의 흐름
- 업스트림, 다운스트림, 인바운드, 아웃바운드
- HTTP 메시지가 서버를 향한다 : 인바운드
- 처리 끝난 메시지가 클라이언트(에이전트)로 돌아온다 : 아웃바운드
- 모든 메시지는 다운스트림으로 흐른다

### 3.2 메시지의 각 부분
- 시작줄, 헤더 블록, 본문으로 구성
- 메서드 : 서버가 리소스에 대해 수행해주길 기대하는 동작이다.
- 요청 URL : 리소스의 경로 
- 버전 : 사용중인 HTTP 버전
- 상태코드 : 요청 중 상태를 나타내는 코드
- 사유 구절(reason-phase) : 상태 코드부터 줄바꿈 문자열까지가 사유 구절이다. 상태코드와 일대 일로 대응되는, 사람의 이해를 돕기 위한 설명
- 헤더들 : 이름,  콜론, 선택적인 공백 값, CRLF가 순서대로 나타나는 0개 이상의 헤더들.
- 엔터티 본문 : 임의의 데이터 블록을 포함하거나, CRLF로 끝나는 블록. 헤더든 엔터티든 내용이 없어도 CRLF(빈 줄)로 끝나야 한다.
- 요청 메시지의 시작줄 : 공백으로 구분되며, 클라이언트가 어떤 메소드로, 어떤 리소스에 대한 경로를, 어떤 HTTP 버전으로 요청하고 있는 지를 의미
- 응답 메시지의 시작줄 : HTTP버전과 상태코드, 사유 구절로 구성된다.

### 3.3 메서드
- HTTP 메서드의 지원 여부는 서버마다 다르다.
- GET과 HEAD 메서드는 '안전한 메서드'에 속한다. 서버에 별다른 작용을 일으키지 않는다는 말.
- GET은 가장 흔히 쓰이는 메서드로, 서버에게 리소스를 요청한다.
- HEAD는 GET처럼 동작하지만, 엔터티 본문 없이 헤더만을 반환한다. HEAD를 사용하면,
> 리소스를 가져오지 않고도 그에 대해 무엇인가(타입이라거나)를 알아낼 수 있다.
> 응답의 상태 코드를 통해, 개체가 존재하는지 확인할 수 있다.
> 헤더를 확인하여 리소스가 변경되었는지 검사할 수 있다. (p62)
- PUT은 요청의 본문을 이용하여 새 문서를 만들거나, 본문을 사용해서 교체할 때 사용한다.
- POST는 서버에 입력 데이터를 전송한다. 흔히 HTML 폼에 채워진 데이터를 모아서 서버 게이트웨이 프로그램에 전달한다.
- TRACE는 목적지 서버에 도달하기 전까지 거치는 여러 애플리케이션들(방화벽, 프락시, 게이트웨이 등)이 요청에 어떤 영향을 미치는 지 진단할 때 사용한다. 
- OPTIONS는 웹 서버로부터 지원 메서드 목록을 요청할 수 있다.
- DELETE는 서버에게 해당 리소스를 삭제할 것을 요청하지만, HTTP 명세는 서버가 클라이언트에게 알리지 않고 요청을 무시하는 것을 허용하므로, 반드시 그 처리를 보장하지는 못한다. 
- 확장 메서드(p66)

### 3.4 상태코드 (p67~74)
> 상태 코드는 클라이언트에게 그들의 트랜잭션을 이해할 수 있는 쉬운 방법을 제공한다.(p67)
- 100-199: 정보성 상태 코드. 
- 200-299: 성공 상태 코드
- 300-399: 리다이렉션 상태 코드
- 400-499: 클라이언트 에러 상태 코드
- 500-599: 서버 에러 상태 코드

### 3.5 헤더
- 헤더는 클라이언트와 서버가 무엇을 하는지 결정하는 데 사용되며, 다섯 가지 종류가 있다.
- 일반 헤더(General Headers) : 클라이언트와 서버 양쪽에서, 클라이언트/서버/다른 애플리케이션 등에 메시지를 보내기 위해 공통적으로 사용하는 헤더.
- Connection, Date, MIME-Version, Trailer chuncked transfer, Transfer-Encoding, Upgrade, Via
- 캐시 관련 헤더 : Cache-Control, Pragma

- 요청 헤더(Request Headers) : 클라이언트가 서버로부터 응답받고자 하는 데이터의 타입 등의 부가 정보를 포함한다. 예) Accept: */*
- Client-IP, From, Host, Referer, UA-Color, UA-CPU, UA-Disp, UA-OS, UA-Pixels, User-Agent

*Accept 관련 헤더*
- 클라이언트는 서버로부터 원하는 미디어 종류, 문자 집합, 인코딩, 언어, 확장 전송 코딩을 미리 헤더에 포함시킬 수 있다.
- Accept, Accept-Charset, Accept-Encoding, Accept-Language, TE

*조건부 요청 헤더*
- 조건부 요청 헤더는 서버에게 응답 전 제약 조건 확인을 먼저 요구한다.
- Expect, If-Match, If-Modifed-Since, If-None-Match, If-Range, If-Unmodified-Since, Range

*요청 보안 헤더*
- 인증 정보를 헤더에 포함하므로써 리소스 접근 전 인증할 수 있도록 한다.
- Authorization, Cookie, Cookie2

*프락시 요청 헤더*
- Max-Forwards, Proxy-Authorization, Proxy-Connection

- 응답 헤더(Response Headers) : 서버가 클라이언트에게 제공하는 자신만의 헤더. 
- Age, Public, Retry-After, Server, Title, Warning
*협상 헤더*
- Accept-Ranges, Vary

*응답 보안 헤더*
- Proxy-Authenticate, Set-Cookie, Set-Cookie2, WWW-Authenticate

- 엔터티 헤더(Entity Headers) : 엔터티 본문의 데이터 타입에 대한 정보를 제공한다. 예) Content-Type: text/html; charset=iso-latin-1
Allow(해당 엔터티에 대해 수행될 수 있는 요청 메서드 목록), Location(엔터티의 실제 위치, 즉 리소스에 대한 새로운 URL을 알려줌)

*콘텐츠 헤더*
Content-Base, Content-Encoding, Content-Languate, Content-Length, Content-Location, Content-MD5, Content-Range, Content-Type

*엔터티 캐싱 헤더*
> 예를 들면, 리소스에 대해 캐시된 사본이 아직 유효한지에 대한 정보와, 캐시된 리소스가 더 이상 유효하지 않게 되는 시점을 더 잘 추정하기 위한 단서같은 것이다.
- ETag, Expires, Last-Modifed

- 확장 헤더(Extension Headers) : *애플리케이션 개발자들에 의해 만들어졌지만 아직 승인된 HTTP에는 추가되지 않은 비표준 헤더, p77*

상태코드, 헤더 별 설명을 열거하지 않았음. 책 참고하기.
