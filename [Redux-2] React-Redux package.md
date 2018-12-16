# [Redux] React-Redux package

In a React Redux app, you create a single Redux store that manages the state of your entire app. Your React components subscribe to only the pieces of data in the store that are relevant to their role. Then, you dispatch actions directly from React components, which then trigger store updates.



#### API

* `<Provider>` : Redux `store` 와 해당 app의 child components를 props로 받는다.

  The `Provider`is a wrapper component from React Redux that wraps your React app. This wrapper then allows you to access the Redux `store`and `dispatch`functions throughout your component tree. `Provider`takes two props, the Redux store and the child components of your app. Defining the `Provider`for an App component might look like this:

  ```react
  <Provider store={store}>
    <App/>
  </Provider>
  ```



  The `Provider`component allows you to provide `state`and `dispatch`to your React components, but you must specify exactly what state and actions you want. This way, you make sure that each component only has access to the state it needs. You accomplish this by creating two functions: `mapStateToProps()`and `mapDispatchToProps()`.



  * mapStateToProps()

  In these functions, you declare what pieces of state you want to have access to and which action creators you need to be able to dispatch. Once these functions are in place, you'll see how to use the React Redux `connect`method to connect them to your components in another challenge.

  **Note:** Behind the scenes, React Redux uses the `store.subscribe()`method to implement `mapStateToProps()`.

   `mapStateToProps()` function should take `state`as an argument, then return an object which maps that state to specific property names. These properties will become accessible to your component via `props`.



  * mapDispatchToProps()

  The `mapDispatchToProps()`function is used to provide specific action creators to your React components so they can dispatch actions against the Redux store. It returns an object that maps dispatch actions to property names, which become component `props`. Each property returns a function that calls `dispatch`with an action creator and any relevant action data. You have access to this `dispatch`because it's passed in to `mapDispatchToProps()`as a parameter when you define the function, just like you passed `state`to `mapStateToProps()`. 

  **Note:** Behind the scenes, React Redux is using Redux's `store.dispatch()`to conduct these dispatches with `mapDispatchToProps()`. 



* connect` function : `mapStateToProps`, `mapDispatchToProps` 를 arguments로 받는다.

  컴포넌트를 Redux에 연결하는 또다른 함수를 반환한다.

  `connect()(COMPONENT_CLASS_NAME)` store에 연결된 새로운 컴포넌트 클래스가 반환된다.

  store가 변화할 때마다 실행되는 함수, map `state`and `dispatch`to the `props`of one of your React components

  ```react
  connect(mapStateToProps, mapDispatchToProps)(MyComponent)
  // Note: If you want to omit one of the arguments to the connect method, you pass null in its place.
  ```


[참고]

`Presentational`, and this wasn't arbitrary. This term *generally* refers to React components that are not directly connected to Redux. They are simply responsible for the presentation of UI and do this as a function of the props they receive. By contrast, container components are connected to Redux. These are typically responsible for dispatching actions to the store and often pass store state to child components as props.