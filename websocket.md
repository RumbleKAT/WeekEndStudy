# WebSocket

## 데이터 통신 방식(Realtime, push, polling) <a href="0198" id="0198"></a>

## Realtime <a href="fae1" id="fae1"></a>

실시간은 사용자가 즉시라고 느낄 정도로 충분히 빠르거나, 또는 컴퓨터가 외부에서 진행되는 처리에 뒤떨어지지 않을 정도로 빠르게 동작하는 컴퓨터의 반응 수준이다

## Push <a href="94a8" id="94a8"></a>

push server는 클라이언트의 요청이 오면 응답해주는 방식이 아닌 서버가 클라이언트에게 공지사항과 같은 무엇인가 통지해주기 위한 방법이다. 다시 말해 클라이언트의 요청이 없이도 서버는 클라이언트에게 응답하는 방식이다.

## Polling <a href="9409" id="9409"></a>

**폴링**(polling)이란 하나의 장치(또는 프로그램)가 충돌 회피 또는 동기화 처리 등을 목적으로 다른 장치(또는 프로그램)의 상태를 주기적으로 검사하여 일정한 조건을 만족할 때 송수신 등의 자료처리를 하는 방식을 말한다

## Long Polling(COMET) <a href="6480" id="6480"></a>

클라이언트가 웹서버에게 새로운 내용이 있는지 물어보았을 때 웹서버에 새로운 내용이 없다면 대답해주지 않다가 새로운 내용이 생기면 이때 대답해주는 방식이다.

Interrupt 와 Polling

Interrupt와 Polling은 CPU가 다른 프로세스를 실행하는 동안 디바이스로 부터 발생하는 이벤트들을 처리하는 두 가지 방법이다. Polling과 Interrupt는 CPU가 현재 하던 일을 멈추고 중요한 일에 반응하도록 멈추게 한다. 

![](<.gitbook/assets/스크린샷 2021-08-28 오후 10.03.28.png>)

| 기본적인 비교    | Interrupt                                         | Polling                                                          |
| ---------- | ------------------------------------------------- | ---------------------------------------------------------------- |
| 기본         | 디바이스가 CPU에게 attention이 필요하다고 알려준다                 | CPU가 계속 CPU의 attention이 필요한 지 안한지 디바이스의 상태를 체크한다                 |
| 메커니즘       | Interrupt는 하드웨어의 메커니즘이다                           | Polling은 프로토콜이다                                                  |
| Servicing  | Interrupt 헨들러가 디바이스를 처리한다                         | CPU가 디바이스를 처리한다                                                  |
| Indication | Interrupt-request 라인이 디바이스가 servicing이 필요하다고 알려준다 | Command-ready 비트는 디바이스가 servicing이 필요하다고 알려준다                    |
| CPU        | CPU는 디바이스가 servicing이 필요할 때만 방해받으면 CPU의 사이클을 절약한다 | CPU는 주기적으로 디바이스가 servicing이 필요한 지 기다리고 체크해야 하기 때문에 CPU 사이클을 낭비한다 |
| Occurence  | Interrupt는 언제나 발생할 수 있다                           | CPU는 일정한 간격으로 device를 poll한다                                     |
|            | Interrupt는 디바이스가 반복적으로 CPU를 interrupt할 때 비효율이 된다  | Polling은 CPU가 service를 위한 device ready가 거의 없을 때 비효율이 된다          |
| 예시         | 벨이 울리면 문을 열고 누가 왔는지 확인한다                          | 계속 누가 왔는 지 문을 열어본다                                               |

Web Socket이란?

웹소켓은 HTML5 표준 기술로, 사용자의 브라우저와 서버 사이의 동적인 양방향 연결 채널을 구성합니다. Websocket API를 통해 서버로 메세지를 보내고, 요청 없이 응답을 받아오는 것이 가능합니다. 현재 API는 W3C에서 관장하고 있으며 프로토콜은 IETF에서 관리하고 있습니다. 웹소켓은 별도의 포트를 사용하지 않고 HTTP와 같은 80번 포트를 사용하고 있는데, 이 때문에 클라이언트인 웹 브라우저뿐만 아니라 웹 서버도 기능을 지원하고 있어야만 합니다.

#### Socket.io

그러나 웹소켓은 HTML5의 기술이기 때문에 오래된 버전의 웹 브라우저는 웹소켓을 지원하지 않습니다. 특히 자동 업데이트가 되지 않는 익스플로러 구 버전 사용자들은 웹소켓으로 작성된 웹페이지를 볼 수 없지요. 따라서 이를 해결하기 위해 나온 여러 기술 중 하나가 Socket.io입니다. 웹페이지가 열리는 브라우저가 웹소켓을 지원하면 웹소켓 방식으로 동작하고, 지원하지 않는 브라우저라면 일반 http를 이용해서 실시간 통신을 흉내내는 것입니다.

Socket.io는 node.js 기반으로 만들어진 기술로, 거의 모든 웹 브라우저와 모바일 장치를 지원하는 실시간 웹 애플리케이션 지원 라이브러리입니다. 이것은 100% 자바스크립트로 구현되어 있으며, 현존하는 대부분의 실시간 웹 기술들을 추상화해 놓았습니다. 다시 말해, Socket.io는 자바스크립트를 이용하여 브라우저 종류에 상관없이 실시간 웹을 구현할 수 있도록 한 기술입니다.\


Socket.io는 웹 브라우저와 웹 서버의 종류와 버전을 파악하여 가장 적합한 기술을 선택하여 사용합니다. 만약 브라우저에 FlashSocket이라는 기술을 지원하는 플러그인이 설치되어 있으면 그것을 사용하고 플러그인이 없으면 AJAX Long Polling 방식을 사용하도록 합니다.



#### 왜 웹소켓을 사용하는가?

초창기 웹은 단순히 인터넷에 접속한 사용자에게 콘텐츠를 전달하는 역할에 지나지 않았습니다. 사용자와의 상호작용은 크게 중요하지 않았으며, 정보의 검색 및 열람 수준에 그쳤습니다. 하지만 웹을 통해 사용자들이 정보를 교환하고 스스로 커뮤니티를 만들어 교류하고자 하는 수요가 늘어나면서 게시판, 블로그 등과 같은 서버와 클라이언트 간의 상호작용을 하는 부분들이 생기기 시작했습니다. 인터넷과 웹이 태동했던 CERN과 같은 초창기의 연구기관에서 사용하던 것과 달리 오늘날의 웹은 일상생활 깊숙이 파고들었고, 네이티브 애플리케이션을 대체하는 수준에 이르렀습니다. 특히 전형적인 브라우저 렌더링 방식은 HTTP 요청에 대한 HTTP 응답을 받아서 브라우저의 화면을 모두 지우고 받은 내용을 새로 표시하는 방식이었습니다. 그때 Ajax와 같은 기술이 나타나면서 사용자와 긴밀히 상호작용하는 웹 서비스가 등장하였고 인기를 끌기 시작하였습니다. 이러한 RIA(Rich Internet Application) 기술의 발달이 웹소켓의 등장 배경이라고 할 수 있습니다.

다시 말해, 기존에는 서버와 클라이언트가 실시간으로 상호작용하는 웹 서비스를 개발하기 위해서 숨겨진 프레임을 이용하는 방법이나 Long Polling, Stream 등과 같은 다양한 방법을 사용했었습니다. 하지만 이 방식은 브라우저가 HTTP 요청을 보내고 웹 서버가 이 요청에 대한 HTTP 응답을 보내는 단방향의 메시지 교환 방식을 유지하는 선에서 구현된 방식입니다. 즉, 기존의 방법에 일종의 트릭을 사용한 방법입니다. 이 때문에 기존의 웹 기술을 이용하여 실시간 웹 서비스를 만드는 일은 복잡하고 어려웠습니다.

바로 이러한 불편함과 사용자와 긴밀히 상호작용하는 웹 페이지를 더 쉽게 만들고자 하는 개발자의 요구가 브라우저와 웹 서버 사이의 자유로운 양방향 메시지 송수신 방법으로써 HTML5 표준안의 일부인 웹소켓 API가 등장했습니다. 

사용 방법은 Ajax와 비슷하지만, 개념 면에서 Ajax와 차이를 두고 있습니다. Ajax의 경우는 웹 브라우저에서 데이터를 호출하면 웹 서버에서 호출된 값을 검색, 작성해서 웹 브라우저로 메세지를 보내는 형식의 구조라면 웹소켓의 경우는 웹 브라우저에서 호출해서 데이터를 가져가는 기능을 포함하여 반대로 서버에서 클라이언트를 호출할 수 있는 기능까지 있습니다.

예로 채팅프로그램을 만든다고 가정할 때, 우리가 채팅을 서버로 보내는 건 가능합니다. 그러나 Ajax로 만든 웹 페이지라면 서버 측에서 클라이언트가 보낼 수가 없습니다. 대응책으로 10초마다 데이터를 갱신해서 확인할 수 있지만, 웹소켓은 서버에서도 클라이언트를 인지하는 상태이기에 양방향 통신이 가능합니다.



