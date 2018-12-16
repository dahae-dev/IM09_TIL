# [Redux] Core Concepts



a state management framework

Redux is a predictable state container for JavaScript apps. It helps you write applications that behave consistently, run in different environments (client, server, and native), and are easy to test. 



#### Intro

In Redux, there is **a single state object** that's responsible for the entire state of your application.

The entire state of your app would be defined by a single state object housed in the Redux `store`.  The Redux store is the single source of truth when it comes to application state. This also means that any time any piece of your app wants to update state, it **must** do so through the Redux store. 



#### 특징

**Predictable state updates** 

* Single source of truth : 단 하나의 store만을 사용한다.
* State is read-only : 어플리케이션에서 store의 state를 직접 변경할 수는 없다. state를 변경하기 위해서는 무조건 action이 dispatch되어야 한다.
* Changes are made with pure functions : action 객체를 처리하는 함수인 reducer는 정보를 받아서 state를 어떻게 업데이트할 지 정의한다. reducer는 비동기적인 처리를 하면 안 된다 (같은 인수로 실행된 함수는 언제나 같은 결과를 리턴).

**Centralized state handling**



#### 장점

* time-travel debugging : state 변화 추적 가능

* 두 개 이상의 컴포넌트가 같은 데이터를 반영해야 하는 경우, 부모 컴포넌트에서 일일이 props로 데이터를 내려줄 필요없이, 하나의 global store 안에 있는 데이터 저장소에서 각 컴포넌트에 뿌려줄 수 있다
* hot module reloading



#### Core Concepts

* **Action** : 이벤트 정의, 어떤 변화가 일어나야 할 지 알려주는 객체

  In Redux, all state updates are triggered by dispatching actions. An action is simply a JavaScript object that contains information about an action event that has occurred. The Redux store receives these action objects, then updates its state accordingly. Sometimes a Redux action also carries some data. For example, the action carries a username after a user logs in. While the data is optional, actions must carry a `type`property that specifies the 'type' of action that occurred.

  Think of Redux actions as messengers that deliver information about events happening in your app to the Redux store. The store then conducts the business of updating state based on the action that occurred.

  ```react
  // define an action type
  const LOGIN = 'LOGIN';
  
  // define a redux action
  const action = { // 액션 정의
      type: LOGIN // required property
      // optional data can be added
  }
  ```

  After creating an action, the next step is sending the action to the Redux store so it can update its state. In Redux, you define action creators to accomplish this. An action creator is simply a JavaScript function that returns an action. In other words, action creators create objects that represent action events.

  ```react
  // define an action creator
  const actionCreator = () => {
  	return action; // 액션 리턴
  }
  
  // define a action & an action creator at the same time
  const loginAction = (params) => { // can take params to send specific data along with your actions
    return {
      type: 'LOGIN',
      data: params
    }
  };
  ```

​	

* **Reducer** : 어떤 액션 타입이냐에 따라 store를 어떻게 변화시킬 것인가를 결정하는 함수, store 생성 시 `Redux.createStore()` 의 인자로 전달

  - Reducers need to update data immutably.

    Reducers in Redux are responsible for the state modifications that take place in response to actions.

    A `reducer`takes `state`and `action`as arguments, and it always returns a new `state`.

    ```react
    (prevState, action) => newState
    ```

    body는 주로  `switch/case` statement를 사용하여 작성한다. 반드시 `default :` case도 함께 작성하여 초기화해야 한다.

    `state`is read-only in Redux. The `reducer`function must **always** return a new **copy** of `state`and never modify state directly.

    `reducer` 은 pure function으로 작성, 네트워크 및 데이터베이스에 접근하거나 인수를 변경해서는 안 된다.

    - 외부 네트워크 혹은 데이터베이스에 접근하지 않아야한다.
    - return 값은 오직 parameter 값에만 의존되어야한다.
    - 인수는 변경되지 않아야한다.
    - 같은 인수로 실행된 함수는 언제나 같은 결과를 반환해야한다.
    - 순수하지 않은 API 호출을 하지 말아야 한다. (Date 및 Math 의 함수 등)

    It is important to see that this is the **only** role of the reducer. It has no side effects — it never calls an API endpoint and it never has any hidden surprises.

  - Multiple Reducers

    You define multiple reducers to handle different pieces of your application's state, then compose these reducers together into one root reducer. The root reducer is then passed into the Redux `createStore()`method.

    In order to let us combine multiple reducers together, Redux provides the `combineReducers()`method. This method accepts an object as an argument in which you define properties which associate keys to specific reducer functions. The name you give to the keys will be used by Redux as the name for the associated piece of state.

    ```react
    // combine multiple reducers
    const rootReducer = Redux.combineReducers({
      auth: authenticationReducer,
      notes: notesReducer
    });
    ```

* **Store** : 어플리케이션의 현재 상태를 가지고 있는 객체

  - The Redux `store` is an object which holds and manages application `state`. 
  - methods : `dispatch`, `getState`, `subscribe`

  ```react
  // create Redux store
  const store = Redux.createStore(
  	(state = {login: false}) => state  // accept a reducer function as required argument
  ); 
  
  // get state from Redux store
  let currentState = store.getState()
  
  // dispatch an action event to Redux store
  // when an action is received and the store is updated
  store.dispatch(actionCreator());
  // the following lines are equivalent
  store.dispatch(loginAction());
  store.dispatch({ type: 'LOGIN' });
  
  // register a store listener
  // subscribe listener functions to the store, which are called whenever an action is dispatched against the store.
  store.subscribe(callback);
  const callback = () => execution;
  ```


*** simple example*** 

```react
// use const for action type
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
const LOGIN = 'LOGIN';
const LOGOUT = 'LOGOUT';

// write reducer function
const counterReducer = (state = 0, action) => { // takes state and action as arguments
  switch(action.type) {
    // always returns a new state
    case INCREMENT:
      return state + 1; 
    case DECREMENT:
      return state - 1;
    default:
      return state;
  }
};
const authReducer = (state = {authenticated: false}, action) => {
  switch(action.type) {
    case LOGIN:
      return {
        authenticated: true
      }
    case LOGOUT:
      return {
        authenticated: false
      }
    default:
      return state;
  }
};

// combine multiple reducers
const rootReducer = Redux.combineReducers({
  count: counterReducer,
  auth: authReducer
})

// create Redux store
const store = Redux.createStore(rootReducer);
```

```react
// use const for action type
const ADD = 'ADD';

// write reducer function
const reducer = (state = 0, action) => { 
  switch(action.type) {
    case ADD:
      return state + 1; 
    default:
      return state;
  }
};

// create Redux store
const store = Redux.createStore(reducer);

// global count variable:
let count = 0;

// listener functions (callback)
const addOne = () => count += 1;

// subscribe a listener function to the store
store.subscribe(addOne);

// dispatch an action event to the store
store.dispatch({type: ADD});
console.log(count); // 1
store.dispatch({type: ADD});
console.log(count); // 2
store.dispatch({type: ADD});
console.log(count); // 3
```



#### methods of enforcing the key principle of state immutability in Redux

there are ways to help enforce state immutability when state is an `object`, too. A useful tool for handling objects is the `Object.assign()`utility. `Object.assign()`takes a target object and source objects and maps properties from the source objects to the target object. Any matching properties are overwritten by properties in the source objects. This behavior is commonly used to make shallow copies of objects by passing an empty object as the first argument followed by the object(s) you want to copy. Here's an example:

```react
const newObject = Object.assign({}, obj1, obj2);
```

This creates `newObject`as a new `object`, which contains the properties that currently exist in `obj1`and `obj2`.