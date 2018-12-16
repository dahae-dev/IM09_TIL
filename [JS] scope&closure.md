# Scope & Closure



#### Scope

변수에 접근 가능한 유효 범위

함수가 선언되는(Lexical) 동시에 자신만의 scope가 생성되고,

함수를 호출할 때 선언시 생성된 lexical scope를 참조한다

*** 일반적인 for, switch 등의 블럭 {} 에 의해서는 생성되지 않는다**

lexical scoping : 함수를 처음 선언하는 순간, 함수 내부의 변수는 자기 scope로부터 가장 가까운 곳에 있는 변수를 계속 참조하게 된다



#### Hoisting

변수를 선언하고 초기화했을 때 선언 부분이 최상단으로 끌어올려지는 현상

이는 함수 선언식에만 해당, 함수 표현식은 적용되지 않는다

```javascript
console.log(zero); // undefined
sayWow(); // wow
function sayWow() {
    console.log('wow');
}
var zero = 'zero';
```

위 코드는 아래 코드와 동일하다

```javascript
function sayWow() {
    console.log('wow');
}
var zero;
console.log(zero); // undefined
sayWow(); // wow
zero = 'zero';
```



#### Closure

외부함수의 context에 접근 가능한 내부함수

외부함수의 lifecycle이 끝나도 여전히 내부함수는 외부함수의 scope에 접근 가능

내부의 closure 함수를 리턴하는 함수를 호출할 경우, 매번 새로운 함수가 생성되어 리턴되는 것이므로, 이 때마다 scope chain은 새롭게 할당되어 저장된다

```javascript
var makeClosure = function() {
  var name = 'zero';
  return function () {
    console.log(name);
  }
};
var closure = makeClosure(); // function () { console.log(name); }
closure(); // 'zero';
```

```javascript
"global context": {
  변수객체: {
    arguments: null,
    variable: [{ makeClosure: Function }, 'closure']
  },
  scopeChain: ['전역 변수객체'],
  this: window
}
  
"makeClosure context": {
  변수객체: {
    arguments: null,
    variable: [{ name: 'zero' }]
  },
  scopeChain: ['makeClosure 변수객체', '전역 변수객체'],
  this: window
}

"closure context":  {
  변수객체: {
    arguments: null,
    variable: null
  },
  scopeChain: ['closure 변수객체', 'makeClosure 변수객체', '전역 변수객체'],
  this: window
}
```



IIFE를 통해 선언하면서 즉시 호출하게 되면, lexical scope 생성하게 되어, binding 공유할 수 있으며, 그 내부의 closure 함수 내부는 local binding이 형성된다

```javascript
for (var i = 0; i < 5; i++) { // 5번 반복 후, i = 5
  $('#target' + i).on('click', function() {
    console.log(i); // i는 외부의 i를 참조
  });
}
// 5
// 5
// 5
// 5
// 5

// Solution 1 : IIFE 사용
for (var i = 0; i < 5; i++) {
  (function(j) {
    $('#target' + j).on('click', function() {
      console.log(j);
    });
  })(i);
}

// Solution 2 : let 사용
for (let i = 0; i < 5; i++) {
  $('#target' + i).on('click', function() {
    console.log(i);
  });
}
```



*** closure는 함수가 정의된 scope 이외의 곳에서 사용될 때 private 저장소처럼 활용 가능하다**

​	- 내부의 function을 리턴하여 다른 곳에서 사용 (바로 호출하던지, 인자로 넘겨주는 등)

​	- setTimeout, setInterval 등과 같이 비동기적으로 호출되는 경우

​	- 이벤트 콜백함수로 활용되는 경우(addEventListener 또는 xmlhttprequest.onreadystatechange 등)

=> 반복적으로 같은 작업을 할 때 같은 초기화 작업이 필요할 시, 콜백 함수에 동적인 데이터를 넘겨주고 싶을 때 closure를 사용하자!