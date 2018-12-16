# Node.js



#### node.js란?

a cross-platform runtime environment and a library for running applications written in JavaScript outside the browser (on the server).

uses **non-blocking I/O** and **asynchronous events** to create a highly performant environment built on Google's V8 engine.

* Blocking vs. Non-Blocking
  * Blocking: 커널(OS) 대기가 발생하면 기다림
  * Non-Blocking: 대기가 필요할 경우 바로 종료
* Sync vs. Async
  * Sync : 완료 시점을 응용 프로그램이 확인 (능동적)
  * Async : 완료 시점에 이벤트가 발생, 일반적으로 callback을 통해 처리 (수동적)

일반적으로 Sync + Blocking, Non-blocking, Asynchronous 3가지 상태가 존재



#### node.js 용도

* 웹 어플리케이션 서버

  * MEA/RN stack : MongoDB, Express, Angular/React, Node.js

  * Nginx + node.js  조합으로 많이 사용됨

    Nginx : web server business logic 이외의 task 수행 (ex. http로 요청된 문서를 https로 변환 등) 

    Node.js : app server, business logic을 수행

  * REST API 서버

  * GraphicQL

  * web socket 이용한 프로젝트 : chatting server, push alarm



#### The CommonJS Pattern

Each module is created within its own file with an isolated scope.

The author of the module can specify exactly what functionality needs to be exposed.



#### Global Object

browser의 window 객체가 node.js에서는 global 객체로 사용



#### Using Node modules : require() & module.exports

```javascript
// export : make this module available outside of this module
// generally use function expression in node.js
module.exports = FUNCTION_NAME | VARIABLE_NAME;
exports.FUNCTION_NAME = function() {}

// export 할 게 여러 개인 경우
module.exports.FUNCTION_NAME = FUNCTION_NAME; 
module.exports.FUNCTION_NAME = function() {}

module.exports = {
    PROPERTY1: FUNCTION_NAME,
    PROPERTY2: VARIABLE_NAME
}

// import
var MODULE_NAME = require('FILE_PATH'); // '.js'가 default 확장자로 지정
// MODULE_NAME.PROPERTY_NAME 형태로 해당 모듈의 변수 및 함수에 접근 가능
var { FUNCTION_NAME, VARIABLE_NAME } = require('FILE_PATH'); // ES6 문법
```

The `exports` variable is available within a module's file-level scope, and is assigned the value of `module.exports` before the module is evaluated.

It allows a shortcut, so that `module.exports.f = ...` can be written more succinctly as `exports.f = ...`. However, be aware that like any variable, if a new value is assigned to `exports`, it is no longer bound to `module.exports`:

```js
module.exports.hello = true; // Exported from require of module
exports = { hello: false };  // Not exported, only available in the module
```

When the `module.exports` property is being completely replaced by a new object, it is common to also reassign `exports`:

```js
module.exports = exports = function Constructor() {
  // ... etc.
};
```



#### [Core Modules]

#### Create the Server : http

- http : to make Node.js act as an HTTP server

  * Create a web server object by using `http.createServer`

    ```javascript
    const http = require('http');
    
    const server = http.createServer(requestHandler);
    // createServer()로 리턴된 Servre 객체는 EventEmitter
    ```

  * Request

    * request headers

    When handling a request, the first thing you'll probably want to do is look at the *method* and *URL*, so that appropriate actions can be taken. 

    이는 request 객체의 헤더에 접근하여 확인 가능. `request.method` , `request.url`

    * request body

    POST or PUT request 인 경우, request body가 중요하다. 이는 request headers에 접근하여 얻을 수 있다.

    ```javascript
    let body = [];
    request.on('data', (chunk) => {
        body.push(chunk);
    }).on('end', () => {
        body = Buffer.concat(body).toString();
        // response
    })
    ```

    The `request` object that's passed in to a handler implements the [`ReadableStream`](https://nodejs.org/api/stream.html#stream_class_stream_readable)interface. This stream can be listened to or piped elsewhere just like any other stream. We can grab the data right out of the stream by listening to the stream's `'data'` and `'end'` events. The chunk emitted in each `'data'` event is a [`Buffer`](https://nodejs.org/api/buffer.html). If you know it's going to be string data, the best thing to do is collect the data in an array, then at the `'end'`, concatenate and stringify it.

  * Response

    The `response` object  is an instance of [`ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse), which is a [`WritableStream`](https://nodejs.org/api/stream.html#stream_class_stream_writable). It contains many useful methods for sending data back to the client. 

    * response headers

      ```javascript
      // HTTP Status Code
      response.statusCode = 200 // default
      201 // created : POST request
      404 // error : not found
      
      // set response headers
      response.setHeader('Content-Type', 'application/json');
      
      // send header data (위 두 줄과 동일)
      response.writeHead(200, {
          'Content-Type': 'application/json'
      })
      ```

    * response body

      ```javascript
      response.write('data');
      response.end();
      
      // 위 두 줄과 동일
      response.end('data');
      ```

    * listen

      ```javascript
      const http = require('http');
      
      const server = http.createServer((req, res) => {
          if (req.url === '/') {
              // do something...
          }
          if (req.url === '/api/courses') {
              // do something...
          }
      });
      
      server.listen(3000); // 해당 port number로 서버 가동
      ```

- url : to parse URL strings




#### Read & Write Files : fs

* fs : to handle file system

  ```javascript
  var fs = require('fs');
  
  // read a file
  var VARIABLE_NAME = fs.readFileSync('FILE_NAME', 'ENCODING');
  
  // write a file
  fs.writeFileSync('NEW_FILE_NAME', 'CONTENT')
  
  // read & write asynchronously
  fs.readFile('FILE_NAME', 'ENCODING', function(err, data){
      fs.writeFile('NEW_FILE_NAME', data);
  });
  ```

* path : to handle file paths



#### Event Emitter : events

* events 

  ```javascript
  const EventEmitter = require('events');
  
  // Class: EventEmitter
  class MyEmitter extends EventEmitter {}
  const myEmitter = new MyEmitter();
  
  // Event: 'newListener'
  ```



#### External Modules and npm

* package.json

  * 프로젝트의 의존성(dependencies)에 대해 명시한 파일

  * 필요한 패키지들의 버전 등에 대해 기술되어 있음

  * Task Runner로의 기능도 담당

* npm install 패키지명 --save : 프로젝트의 "실행"에 필요한 패키지가 설치되면서 package.json에 추가됨

* npm install 패키지명 --save-dev : 프로젝트의 "개발"에 필요한 패키지가 설치되면서 package.json에 추가됨



#### Debugging

1. `console.log()`
2. `--inspect` flag

```javascript
node --inspect-brk FILE_PATH
```



*** common and difference between nodejs environment and javascript environment in chrome browser**

common feature : Both uses the same javascript engine which is the backbone of chrome.

difference : 

Javascript has "window", "location", "document" as predefined global object, while nodejs does not. Node has predefined global object named "global" which contains functions that are not available in browsers and only working on server side.

In Node.js, everything is module and "require" object is predefined which is used to include modules in the app, on the other hands, moduling is not mandatory in javascript.

Node processes request object, while browser processes response object.