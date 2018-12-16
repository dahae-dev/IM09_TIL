# this



#### 'this' keyword

- 모든 함수 scope 내에서 자동으로 설정되는 특수한 식별자
- execution context의 구성요소 중 하나로, 함수가 실행되는 동안 이용할 수 있다



#### Execution context

- 브라우저 시작 시 Global memory 및 Global execution context 생성

- 함수 호출 시마다 Local memory(lexical scope에 의존) 및 Local execution context 생성

  : call stack에 push, 함수 실행 후 벗어나면 call stack에서 pop

- execution context 생성시 context 내부에 생성되는 것

  - 변수객체(arguments, variable)

  - Scope chain

  - this

- context 생성 후 함수가 실행되면서, 사용되는 변수들은 해당 context 내부의 변수객체 안에서 값을 찾고,

  없다면 scope chain을 따라 올라가며 찾는다

- 함수 실행이 마무리 되면 local execution context는 사라진다 (closure 제외)

  페이지 종료시 global execution context가 사라진다



#### 5 Patterns of Binding 'this'

1. Global Reference: window
2. Function Invocation : window

```javascript
var name = 'Global Variable';
console.log(this.name); // "Global Variable"

function foo(){
    console.log(this.name); 
}
foo(); // "Global Variable"

function outer() {
    function inner() {
        console.log(this.name);
    }
    inner();
}
outer(); // "Global Variable"

function outer2() {
    var closure = function() {
        console.log(this.name);
    }
    return closure;
}
outer2()(); // "Global Variable"
```



3. Method Invocation : 부모 object

```javascript
var counter = {
    val: 0,
    increment: function() {
        this.val += 1;
    }
};

counter.increment();
console.log(counter.val); // 1
counter['increment']();
console.log(counter.val); // 2

var obj = {
    fn: function(a, b) {
        return this;
    }
};
var obj2 = {
    method: obj.fn
};

console.log(obj2.method() === obj2); // true
console.log(obj.fn() === obj); // true
```

[참고] function invocation vs. method invocation

모든 function은 내부적으로 window.function()으로 invoked 되기 때문에, function invocation도 method invocation과 마찬가지로 부모객체를 가리키는 것과 같다.

```javascript
var obj = {
  foo: function(){
    console.log(this);
  }
}

var fn = obj.foo;
fn(); // window
// 내부적으로 window.fn()으로 실행
```



4. Construction mode (new 연산자로 생성된 function 영역) : 새로 생성된 인스턴스

```javascript
function F(v) {
    this.val = v;
}

var f = new F('WooHoo!');

console.log(f.val); // WooHoo!
console.log(val); // ReferenceError
```



5. call or .apply Invocation : call, apply의 첫번째 인자로 명시된 객체

```javascript
var add = function (x, y) {
    this.val = x + y;
}

var obj = {
    val: 0
}

add.apply(obj, [2, 8]);
console.log(obj.val); // 10
add.call(obj, 2, 8);
console.log(obj.val); // 10
```



#### Binding Problem

Function Invocation은 this가 바인딩되지 않기 때문에, 항상 this는 window를 가리킨다.

따라서 callback function 적용 시, binding problem이 발생할 수 있다.

cf) setTimeout() : 항상 window를 바인딩한다

cf) bind(this) vs. call(this)

​    - bind(this) : this를 바인딩 한 function을 리턴

​    - call(this) : 리턴값이 없고, this를 바인딩 후 바로 실행 

```javascript
function getSalaryFromServer(callback) {
    setTimeout(function() {
        callback(10000);
    }, 1000);
}

function member() {
    return {
        first: 'Rachel',
        last: 'Green',
        age: 30,
        printDetail: function() {
            getSalaryFromServer(function(salary){
                console.log(`Name: ${this.first} ${this.last}`);
                console.log(`Salary: ${salary}`);
            });
        }
    }
}

var rachel = new member();
rachel.printDetail();
// Name: undefined undefined
// Salary: 10000
```



###### Solution 1. Use bind()

```javascript
function member() {
    return {
        first: 'Rachel',
        last: 'Green',
        age: 30,
        printDetail: function() {
            getSalaryFromServer(function(salary){
                console.log(`Name: ${this.first} ${this.last}`);
                console.log(`Salary: ${salary}`);
            }.bind(this)); // this와 바인딩을 한 함수를 리턴
        }
    }
}

var rachel = new member();
rachel.printDetail();
// Name: Rachel Green
// Salary: 10000
```



###### Solution 2. Use Arrow Function

* lexical `this`
  * ES5의 `this` 는 어디에서보다 어떻게 호출되는지가 더 중요
  * arrow function을 사용한 함수는 어디에서 호출되는지만 고려
* `arguments` 를 바인딩하지 않음
* `call` , `apply` 의 `this` 바인딩은 무시됨

```javascript
function member() {
    return {
        first: 'Rachel',
        last: 'Green',
        age: 30,
        printDetail: function() {
            getSalaryFromServer(salary => {
                console.log(`Name: ${this.first} ${this.last}`);
                console.log(`Salary: ${salary}`);
            });
        }
    }
}

var rachel = new member();
rachel.printDetail();
// Name: Rachel Green
// Salary: 10000
```





#### 실행 컨텍스트 추가 예제)

```javascript
var name = 'zero';
function wow(word){
    console.log(word + ' ' + name);
}
function say(){
    var name = 'nero';
    console.log(name);
    wow('hello');
}
say();
// nero
// hello zero
```

##### Global execution context

```javascript
'global context' = {
    변수객체: {
        arguments: null,
        variables: ['name', 'wow', 'say']
    },
    scopeChain: ['global scope'],
    this: window
}
```

##### Local execution context

```javascript
'say context' = {
    변수객체: {
        arguments: null,
        variables: ['name']
    },
    scopeChain: ['say local scope', 'global scope'],
    this: window
}
```

```javascript
'wow context' = {
    변수객체: {
        arguments: [{word: 'hello'}],
        variable: null
    },
    scopeChain: ['wow local scope', 'global scope'],
    this: window
}
```

