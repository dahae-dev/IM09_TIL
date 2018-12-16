# Asynchronous call



#### callback

다른 코드의 arguments로서 넘겨주는 function을 의미

콜백을 parameter로 넘겨받는 코드는 이 콜백을 필요에 따라 즉시 실행할 수도 있고(synchronously), 아니면 나중에 실행할 수도 있다(asynchronously)

* iterator 로 사용될 때 - synchronous callback

```javascript
[1,2,3].map(function(element){
    return element * element;
}) 
// map의 arguments로 넘어간 익명함수가 callback함수로서, 넘겨받는 즉시 실행된다
```

* Event handler 로 사용될 때 - asynchronous callback

```javascript
$('button').on('click', function(e){
    console.log('button clicked');
}) 
// 버튼 클릭 이벤트의 핸들러로 넘어간 익명함수가 callback 함수로서, click 이벤트 발생시 실행된다
```



*** 자바스크립트는 single thread 기반의 언어 = 하나의 call stack을 사용한다**

그럼에도 불구하고, 동시성이 지원되는 이유는 event loop 에 의해 가능하다 



#### Event loop

* 콜백 함수를 보관하는 공간 (task queue)를 마련해놓고
* 동기 작업이 모두 끝나 call stack이 비워지면 (execution context가 전부 사라지면)
* (event loop에 의해) 콜백 함수가 실행



#### Timer API 

* setTimeout(callback, millisecond) : 일정 시간 후에 콜백함수 실행, 임의의 timerId return
* setInterval(callback, millisecond) : 일정 시간의 간격을 가지고 콜백함수를 반복적으로 실행, 임의의 timerId return
* clearInterval(timerId) : 반복 실행중인 타이머 종료

```javascript
// 1) A - D - C - B
console.log("A");
setTimeout(function() {
  console.log("B");
}, 1000); // 1s 뒤에 task queue로 넘어감

setTimeout(function() {
  console.log("C");
}, 500) // 0.5s 뒤에 task queue로 넘어감

console.log("D"); // 동기 작업 먼저 완료 후, task queue에 저장된 순서대로 콜백함수 실행

// 2) A - C - B
console.log("A");
setTimeout(function() {
  console.log("B");
}, 0); // 비동기 영역으로 먼저 넘어간 다음 바로(0초 뒤) task queue로 넘어감

console.log("C");

// 3) A - B - C
console.log("A");
setTimeout(function() {
  console.log("B");
}, 1000); // 비동기 영역에서 1s 뒤 task queue로 넘어감

superLongComputation(); // synchronous, takes 5s to run

console.log("C");

// 4)
console.log("A");
setTimeout(function() {
  console.log("B");
}, 500); // 비동기 영역에서 0.5s 뒤 task queue로 넘어감

setTimeout(console.log("C"), 1000); // 바로 실행됨
```



#### Asynchronous Call

* Event handler

* timer에서의 callback

  * animation

* 서버에 자원 요청 (Ajax call)

  Ajax Library

  $.ajax

  fetch()

  axios



*checkpoint 12 참고

*udemy 수업