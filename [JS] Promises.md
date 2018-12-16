# Promises



#### callbacks

If we had used the handler’s return value as the response value, that would mean that a request handler can’t itself perform asynchronous actions. A function doing asynchronous work typically returns before the work is done, having arranged for a callback to be called when it completes. So we need some asynchronous mechanism—in this case, another callback function—to signal when a response is available.

In a way, asynchronicity is *contagious*. Any function that calls a function that works asynchronously must itself be asynchronous, using a callback or similar mechanism to deliver its result. Calling a callback is somewhat more involved and error-prone than simply returning a value, so needing to structure large parts of your program that way is not great.



#### promises

In the case of asynchronous actions, you could, instead of arranging for a function to be called at some point in the future, return an object that represents this future event.

 A *promise* is an asynchronous action that may complete at some point and produce a value. It is able to notify anyone who is interested when its value is available.

* create a promise

  * `Promise` constructor

  To create a promise, you can use `Promise` as a constructor. It has a somewhat odd interface—the constructor expects a function as argument, which it immediately calls, passing it a function that it can use to resolve the promise. It works this way, instead of for example with a `resolve` method, so that only the code that created the promise can resolve it.

  ```javascript
  function storage(nest, name) {
    return new Promise(resolve => {
      nest.readStorage(name, result => resolve(result)); // resolved 됐을 때의 value: result
    });
  }
  
  storage(bigOak, "enemies")
    .then(value => console.log("Got", value)); // promise 인스턴스의 resolve 메서드를 통해 리턴된 value값을 then 메서드에서 받아서 사용 가능
  ```

  ```javascript
  async function retrieveArticle(url) {
    return new Promise((resolve, reject) => { // Promise constructor의 인수로는 callback function을 받고, 그 callback function의 파라미터로는 resolve, reject를 넘긴다.
      const req = https.get(url, (res) => {
        let data = '';
        res.on('data', (chunk) => {
          data += chunk.toString();
        })
        res.on('end', () => {
          resolve(data);
        })
      })
      req.on('error', (err) => {
        reject(err);
      })
    })
  }
  ```



  * `Promise.resolve` & `Promise.reject`

  The easiest way to create a promise is by calling `Promise.resolve`. This function ensures that the value you give it is wrapped in a promise. If it’s already a promise, it is simply returned—otherwise, you get a new promise that immediately finishes with your value as its result.

  ```javascript
  // create a promise
  let fifteen = Promise.resolve(15); // resolved된 상태의 promise 인스턴스를 생성
  
  // get the result of a promise
  fifteen.then(value => console.log(`Got ${value}`)); // Got 15
  ```

* `then` method

  To get the result of a promise, you can use its `then` method. This registers a callback function to be called when the promise resolves and produces a value. You can add multiple callbacks to a single promise, and they will be called, even if you add them after the promise has already *resolved* (finished).

  `then`의 인자로 받은 콜백함수는 비동기 영역으로 넘어가서 task queue에서 대기하고 있다가, call stack이 모두 비워진 다음에 call stack으로 들어와서 실행됨.

  PromiseStatus와 PromiseValue가 바뀐 promise 인스턴스를 다시 리턴하고, PromiseValue는 다음 then 메서드로 전달 가능.



It is useful to think of promises as a device to move values into an asynchronous reality. A normal value is simply there. A promised value is a value that *might* already be there or might appear at some point in the future. Computations defined in terms of promises act on such wrapped values and are executed asynchronously as the values become available.

This asynchronous function returns a meaningful value. This is the main advantage of promises—they simplify the use of asynchronous functions. Instead of having to pass around callbacks, promise-based functions look similar to regular ones: they take input as arguments and return their output. The only difference is that the output may not be available yet.



```javascript
console.log("line 1");

const ex1 = (resolve) => {
 console.log("in excuter");
 resolve("line 5");
}

console.log("line 8");

const p1 = new Promise(ex1);

console.log("line 12");

const resolve = (value) => {
 console.log("line 15");
 console.log("resolve : ", value);
}

p1.then(resolve)

console.log("line 21");

// 
```



```javascript
const executor = (resolve, reject) => {
    const bool = false;
  
    if(bool)
      resolve(bool);
    else 
      reject(bool);
  }
  
  let number = 0;
  const promise = new Promise(executor); // status: rejected, value: false
  
  const resolve = value => {
    number++
    console.log(promise);
  }
  
  const reject = reason => {
    console.log(promise);
    if(number++ < 1) {
      return Promise.reject("test"); // throw new Error("test"); 
    }
    else {
      number++;
      return "go resolve!!";
    }
  }
  
  promise
    .then(resolve).catch(reject) // status: rejected, value: false
    .then(resolve).catch(reject) // status: rejected, value: 'test'
    .then(resolve).catch(reject) // status: resolved, value: 'go resolve!!'
```



[+] async, await 추가