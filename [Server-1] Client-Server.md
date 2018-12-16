# Client - Server Model



#### Server

* 서비스를 제공(serve)하는 컴퓨터로, 다수의 클라이언트 컴퓨터의 요청을 처리하기 위해 존재
* Web Server, File Server 등
* 웹페이지 지원이나 공유 데이터의 처리 및 저장 등의 비즈니스 로직을 주로 수행
* 리소스(static HTML files or JSON-formatted data)에 대한 접근을 제공하는 컴퓨터로, 자원에 대한 클라이언트의 요청(requests)을 listen하고 응답(responses)을 보내준다



#### Client

* 터미널의 역할을 수행하는 컴퓨터 ex) 웹브라우저
* 사용자의 입력을 주로 수행
* 서버에 대한 응답을 화면에 표시
* 현대의 복잡한 시스템은 클라이언트이면서 서버의 역할을 동시에 수행하는 경우도 있음 ex) P2P



#### Communication between Client and Server

* 클라이언트와 서버는 프로토콜(protocol) 이라고 불리는 정해진 규약에 따라 메시지를 교환
* 클라이언트는 서버가 어떤식으로 요청을 처리하는지에 대해선 구체적으로 알 필요없이, 추상화된 인터페이스(API; Application Programming Interface)를 바탕으로 원격 서버에 요청(RPC; Romote Procedure Call)을 하고, 응답에 대해 적절한 형태로 화면에 표시



#### Messaging Patterns

* Request-Response

  대표적으로 HTTP가 사용하는 메시징 패턴

  보통 동기적으로 작동하며, 연결이 열리면 응답이 전달될 때까지 기다리거나 timeout 전달

* Publish-Subscribe

  수신자가 지정되지 않은 대신, 메시지가 발행되면 구독을 신청한 수신자에게 일괄적으로 전달

  비동기 메시징이며, 메시지 브로커 또는 Event Bus 라고 불리는 것이 중간에 구현되어야 함

* Push Technology (Server push)

  Publish-Subscribe 모델의 일종

  전송 요청이 클라이언트로부터 시작되지 않음

  HTTP/2에서 Server push 라는 이름으로 구현

  WebSocket 프로토콜을 이용해 구현 가능



#### APIs

서버가 정의한 인터페이스

HTTP requests를 사용하여 리소스에 접근하기 위해 서버와 상호작용(interact) 하는 방법을 서술해 둔 일련의 규칙들