packet

https://en.wikipedia.org/wiki/Network_packet<br>
https://computer.howstuffworks.com/question525.htm<br>
https://techterms.com/definition/packet<br>
https://searchnetworking.techtarget.com/definition/packet<br>
https://kb.iu.edu/d/anyq

패킷은 TCP/IP 통신 시 어디서 왔는지, 목적지는 어디인지 등의 특정한 형식 정보와 함께 데이터를 실어보내는 단위. 예를 들어, 어떤 리소스가 한 곳에서 다른 곳으로 보내질 때 TCP 레이어의 TCP/IP는 효율적인 크기로 파일을 쪼갠다. 이 패킷들에는 번호가 매겨지고, 목적지에 대한 인터넷 주소도 포함한다.

이렇게 정보를 교환할 때 패킷이 교환되는 네트워크를 packet-switching network라고 하는데, 이는 연결상태가 유지되지 않는 인터넷과 같은 네트워크에서 전송을 통제하기 위한 효율적인 방법이다.

패킷은 헤더, 페이로드, 트레일러로 이루어져 있고, 
헤더에는 프로토콜, 헤더와 페이로드 크기, 출발지와 목적지의 아이피 주소 등이,
페이로드에는 실제 전송할 데이터
트레일러에는 패킷에 대한 signature, 에러 체크용 체크섬이 포함된다.

router

https://en.wikipedia.org/wiki/Router_(computing)<br>
https://www.lifewire.com/what-is-a-router-2618162<br>
https://www.computerhope.com/jargon/r/router.htm<br>
https://searchnetworking.techtarget.com/definition/router

일종의 하드웨어다. 로컬 머신과 네트워크를 연결해주는.
외부 침입으로부터 지켜주는 첫 번째 방어 라인이다. 가장 높은 수준의 보안은 방화벽이고, 이는 시스템과 정보를 공격으로부터 보호해준다. 라우터는 제조사에서 공급하는 펌웨어를 포함할 수 있다.
대다수의 라우터는 오직 네트워크 케이블을 통해 다른 네트워크 기기와 연결하게 되어 있고 OS를 위한 별도의 드라이버를 필요로 하지 않는다.

라우터는 물리적으로 연결하려는 네트워크 장치의 네트워크 인터페이스 카드에 네트워크 케이블을 통해 연결하게 돼있다. WAN이나 인터넷 연결에 할당 된 IP 주소는 공인 주소다. 로컬 네트워크 연결에 할당된 아이피 주소는 사설 IP 주소다. 라우터에 할당된 사설 IP 주소는 대개 네트워크의 여러 기기들의 기본 게이트웨이가 될 수 있다. 

bridge

https://en.wikipedia.org/wiki/Bridging_(networking)<br>
https://geek-university.com/ccna/what-is-a-network-bridge/<br>
https://www.youtube.com/watch?v=OBlJ3QuEt9k<br>

gateway

https://en.wikipedia.org/wiki/Gateway_(telecommunications)<br>
https://techterms.com/definition/gateway<br>
https://www.lifewire.com/definition-of-gateway-817891<br>
https://www.quora.com/What-is-gateway-in-networking<br>

gateway는 general한 의미로는 모든 트래픽이 통과하는 가장 마지막 장치다.


네트워크 게이트웨이는 서로 다른 네트워크에 속한 장치들이 통신할 수 있도록 한다. 게이트웨이가 없으면, 인터넷에 접속할 수 없고, 데이터를 주고 받을 수도 없다. 게이트웨이는 소프트웨어, 하드웨어 혹은 둘의 혼합으로 구현돼있다. 네트워크 말단에서 나타나기 때문에, 관련된 capabilities인 방화벽, 프록시 서버등과 함께 쓰일 수 있다.

... 게이트웨이는 라우터의 가장 중요한 기능이기도 하다. 동시에 라우터는 게이트웨이의 가장 흔한 유형이다.

게이트웨이는 네트워크 프로토콜 컨버터다. 종종 기반 프로토콜이 다른 두 네트워크를 연결하기 때문이다. 지원한느 프로토콜에 따라, 네트워크 게이트웨이는 OSI 모델의 어느 계층에도 속할 수 있다.


