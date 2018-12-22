# HTTP & Restful API



#### What happens when you type an URL in the browser and press enter?

1. Web browser request http://www.google.com (the URL you typed) to DNS Server
2. DNS Server provides server IP
3. Web browser make HTTP request to provided server IP 
4. HTTP response from server IP



#### HTTP (Hyper-Text Transfer Protocol)

* 서버와 클라이언트가 주로 HTML 등의 문서를 주고 받는데 사용하는 프로토콜

* 주로 TCP/UDP 80번 포트를 사용

* HTTP/1.1의 경우 요청과 응답은 Start/Status line, Header 그리고 Body로 이루어져 있다.

  [참고] https://developer.mozilla.org/en-US/docs/Web/HTTP/Messages

* 특징
  * HTTP는 특정 상태를 담고 있지 않는다 (Stateless) : 이전 요청이나 다음 요청을 기억하지 않음
  * 연결 상태를 유지시키지 않음 (Connectionless)
  * Request-Response Cycle



#### URI or URL

* 자원의 위치(the location of resource)
* includes the address of the server, called the **domain name**, and the path to the resource, often called **endpoints**
* HTTP 요청은 URI를 통해 가능
* 주소창을 통해 하는 요청은 전부 GET Request



#### HTTP Request Methods

* GET : Reads/retrieves a resource, 특정 리소스를 가져오도록 요청 
* POST : Creates/adds a new resource, 데이터를 서버로 제출하는 용도로 사용하며, 서버 상태의 변화를 일으킴
* PUT / PATCH : Updates a resource, POST와 비슷하나, 연속적인 요청시에도 같은 효과를 가져옴. 기존 데이터를 교체하는 용도로 쓰일 수 있음, 
* DELETE : Deletes a resource, 리소스 삭제를 요청할 때 사용
* OPTIONS : Request information about what methods are supported by the resource



#### HTTP Response Status Codes

* 2xx : Success
  * 200 : OK, 요청 성공 
* 3xx : Redirect
  * 301 : Moved Permanently, 요청한 리소스에 대한 URI 변경
  * 302 : Found
  * 304 : 요청에 대한 응답이 수정되지 않음 (Cache)
* 4xx : Client errors
  * 400 : Bad Request, 부적절한 요청
  * 401 : Unauthorized, 클라이언트가 인증되지 않은 상태에서 보호된 리소스 요청
  * 403 : Forbidden, 컨텐츠에 접근할 권한 없음 (클라이언트 인증 상태와 관계없이 응답 거부)
  * 404 : Not Fount, 요청받은 리소스를 사용할 수 없음
  * 405 : 클라이언트가 요청한 리소스에서 사용 불가능한 Method를 이용
* 5xx : Server errors
  * 500 : Internal Server Error, 서버가 처리할 수 없는 요청 (Syntax Error 등)
  * 502 : Bad Gateway



#### RESTful API (REpresentational State Transfer)

REST is a pattern by which we construct interfaces to our server applications that use specific combinations of HTTP verbs and URL nouns. The term RESTful API indeicates an interface that conforms to the REST pattern.

A RESTful API is an API that uses HTTP requests to GET, PUT, POST and DELETE data.

A RESTful API -- also referred to as a RESTful web service -- is based on representational state transfer technology, an architectural style and approach to communications often used in web services development.

* API : HTTP 요청으로 원격 서버에 요청(RPC) 할 수 있도록 외부로 노출된 추상화된 인터페이스
* REST : API 디자인에 있어서 HTTP 프로토콜을 의도에 맞게 사용하도록 정의된 아키텍쳐 스타일
  * URI는 정보의 자원(resource)을 표현해야 한다 --> 명사형 사용
  * 자원에 대한 행위(verbs)는 HTTP Methods로 표현해야 한다
* REST and HTTP verbs
  * Verbs : Endpoint -> Action
  * GET : /api/users -> 모든 사용자를 조회 (collection)
  * POST : /api/users -> 새 사용자를 리스트에 추가
  * PUT, PATCH : /api/users/{username} -> 특정 사용자의 정보를 갱신
  * DELETE : /api/users/{username} -> 특정 사용자의 정보를 삭제
  * GET : /api/users/{username} -> 특정 사용자의 정보를 조회 (a single resource)



#### Browser Security Model

* The Same-Origin Policy*

  * only permits scripts originating from the same site
  * is implemented voluntarily by the browser
  * protects your users from malicious websites
  * DOES NOT protect you from malicious users

* There are actually several same-origin policies for different browser behaviours. We will be focusing on the policy that concerns AJAX requests, but they all work the same way.

* CSRF ???? XSS ????

* Circumventing Same-Origin Policy

  Cooperating servers can get around it in several ways:

  * CORS headers
    * Supported by current browsers
    * Servers can't "opt in" to cross-origin requests
  * iframes / Flash / etc
    * Supported in older browsers
    * Extremely annoying to implement
  * JSONP
    * Technically older than AJAX
    * GET requests only



#### API testing

https://www.npmjs.com/package/supertest

https://hackernoon.com/api-testing-using-supertest-1f830ce838f1