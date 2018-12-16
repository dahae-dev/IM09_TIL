# [React] Component



React, popularized by Facebook, is an open-source JavaScript library for building user interfaces on the premise of **declarative and reusable components**. It is used to create components, handle state and props, utilize event listeners and certain life cycle methods to update data as it changes. It's often described as the 'V' in MVC frameworks and can be paired with other frameworks or Flux libraries.



**advantage of react**

- Handle many changes to DOM Elements.

- Modularize code into re-useable components.

- Declarative and reusable components.

- One way data flow. It makes it easy to manage data that changes over time.

- Virtual DOM makes React performant and minimizes re-renders.

- Easier to build and maintain complex user interfaces.



#### Virual DOM

Model이 변경되면 먼저 Virtual DOM에 렌더링하여 Real DOM과 비교하여 변경되는 부분만 업데이트가 된다. (state와 연관)

A virtual DOM object is a *representation* of a DOM object, like a lightweight copy.

Once the virtual DOM has updated, then React compares the virtual DOM with a virtual DOM *snapshot* that was taken right before the update.

By comparing the new virtual DOM with a pre-update version, React figures out *exactly which virtual DOM objects have changed.* This process is called "diffing."

Once React knows which virtual DOM objects have changed, then React updates those objects, *and only those objects,* on the real DOM. React can update only the necessary parts of the DOM.

**What is the virtual DOM used for? **

A virtual representation of the real DOM that React uses to intelligently re-render each component.



#### React Library

-  `import React from 'react'` creates a JavaScript object. This object contains properties that are needed to make React work, such as `React.createElement()` and `React.Component`.
-  `import ReactDOM from 'react-dom'`creates another JavaScript object. This object contains methods that help React interact with the DOM, such as `ReactDOM.render()`.
- By subclassing `React.Component`, you create a new *component class*. This is not a component! A component class is more like a factory that produces components. When you start making components, each one will come from a component class.
- Whenever you create a component class, you need to give that component class a name. That name should be written in UpperCamelCase.

```react
import React from 'react';
// React library 모듈을 import해야 React의 메서드들 사용 가능
// React와 직접적으로 관련된 메서드: creating components or writing JSX elements
// ex) React.createElement() -> when a JSX element가 컴파일될 때 내부적으로 호출되는 메서드

import ReactDOM from 'react-dom';
// DOM과 상호작용 하기 위한 메서드 포함
// ex) ReactDOM.render()-> DOM을 통해 렌더링

class UpperCamelCasedName extends React.Component {
   // a set of instructions to build components
    render() {
        return `JSX element`;
    }
}

ReactDOM.render(
	<UpperCamelCasedName />, // component instance
	document.getElementById('app')
);
// HTML-like element와 구분짓기 위해 component class name은 반드시 capital letter로 시작해야 한다.
```



#### Import & Export

* import 

  ```react
  import DEFAULT_COMPONENT_NAME from 'FILEPATH';
  import { COMPONENT_NAME } from 'FILEPATH';
  // FILEPATH는 상대경로 사용, 기본 확장자는 .js, 폴더 내 index.js가 default 파일로 설정
  
  // 여러 개의 action types을 불러오는 경우
  import * as types from 'FILEPATH'; // types.TYPE_NAME 형태로 접근 가능
  ```

* export

  1) named export

   `var`, `let`, `const`, `function`, or `class` 앞에 선언

  ```react
  export const hello = 'hello';
  
  export class MyComponent extends React.Component {}
  ```

  2) default export

  ```react
  export default MyComponent;
  // 파일 하단에서 별도로 export
  // default로 export 하는 경우, import 할 때 {} 사용할 필요 없이 객체 이름 그대로 불러올 수 있다
  ```




#### Components

개념상, 리액트 컴포넌트는 자바스크립트의 function과 같다. 

임의의 값("props")을 인자로 받고, 스크린에 보여질 elements를 리턴한다.

A component is a small, *reusable* chunk of code that is *responsible for one job*. That job is often to render some HTML. `render` method is a required method inside of each React component. Component instance accepts a single "props" with data and returns a React element.



* **Stateless Functional Components**

  초기 마운트 속도가 조금 더 빠르고, 메모리 자원도 덜 사용한다.

  단순히 값(props)을 받아와서 렌더링 하는 용도인 경우 사용한다. (the *presentational* component)

  1. javascript function
  2. returns either JSX or `null`
  3. function name should begin with a capital letter

  ```react
  // 아래 두 function은 동일
  const Welcome = (props) => (
      <h1>Hello, {props.name}</h1>
  );
  
  const Welcome = (props) => {
      return <h1>Hello, {props.name}</h1>;
  };
  // 인자로 전달받은 props는 객체 형태로 전달받게 된다
  // props = {
  //    name: "Sara"
  	  // key = attribute name
  	  // value = attribute value
  // }
  ```

  ```react
  const Clock = (props) => {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
  ```

* **Class Components**

  1. should `extends React.Component`

     has access to many useful React features, such as local state and lifecycle hooks

  2. needs `render()` method with the body of returned element

  3. has a `constructor`defined within it that calls `super()`

     call a component's `constructor`with `super`, and pass `props`to both to makes sure the component is initialized properly.

  4. props should be changed to `this.props` in render() body



  * Stateless Component

  ```react
  class Welcome extends React.Component {
      constructor(props) {
      	super(props);
    	}
      
      render() {
          return <h1>Hello, {this.props.name}</h1>;
      }
  }
  ```

  * Stateful Component = React Component

  ```react
  class Welcome extends React.Component {
      constructor(props) {
      	super(props);
          this.state = {
              greeting: 'Hello'
          }
    	}
      
      render() {
          return <h1>{this.state.greeting}</h1>;
      }
  }
  ```

  A common pattern is to try to *minimize statefulness* and to create stateless functional components wherever possible. This helps contain your state management to a specific area of your application. In turn, this improves development and maintenance of your app by making it easier to follow how changes to state affect its behavior.



#### Rendering a Component

DOM element 외에 사용자 정의 컴포넌트로 element를 사용할 수 있다.

사용자 정의 컴포넌트를 사용할 때에는, 이 컴포넌트에 JSX attributes로 데이터를 객체 형태로 전달한다.

```react
const element = <Welcome name="Sara" />;
// name="Sara"를 Welcome 컴포넌트의 props 인자로 전달

ReactDOM.render(
	element, // JS 변수에 저장된 컴포넌트를 렌더링
    document.getElementById('root')
)
```

```react
function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
      // attribute value로 JS 문법을 사용하는 경우 {} 사이에 작성
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```



#### Composing Components

a simple functional component called `ChildComponent`

a React component called `ParentComponent`

compose the two together by rendering the `ChildComponent`within the `ParentComponent`

```react
function App() { 
    return (
    	<div>
        	<Welcome name="Sara" /> 
            <Welcome name="Cahal" />
            <Welcome name="Edite" />
        </div>
    );
}

ReactDOM.render(
	<App />, 
    document.getElementById('root')
)
```



[참고] Render React on the server with `ReactDOMServer.renderToString()`

There are some use cases where it makes sense to render a React component on the server. Since React is a JavaScript view library and you can run JavaScript on the server with Node, this is possible. In fact, React provides a `renderToString()`method you can use for this purpose.

The `renderToString()`method is provided on `ReactDOMServer`. The method takes one argument which is a React element. 




#### Under the hood

1. Compiler/Transpiler : JSX syntax를 브라우저가 이해할 수 있는 언어로 변환

   ex. Babel ( + 최신 자바스크립트 문법을 구 버전의 브라우저에서도 인식할 수 있도록 변환)

   Babel is a Javascript compiler that takes code written in one syntax and transpiles it to another using presets. For example, code can be written in ES6 and then converted to ES5 by Babel.

2. Bundler : 파일 관리 도구

   각각의 모듈별로 생성된 파일을 하나로 묶어서 관리해준다

   ex. webpack

3. Package Manager



ex. create-react-app : 리액트를 사용하기 위한 기본 환경이 미리 다 셋팅되어 있는 라이브러리