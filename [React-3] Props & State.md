## [React] Props & State



#### Props

컴포넌트에 대한 정보를 가지고 있는 객체.

props를 통해 자식컴포넌트는 부모 컴포넌트로부터 값을 전달받는다. 

부모 컴포넌트 내부에서 자식 컴포넌트를 불러올 때 자식 컴포넌트의 태그 내부에 attribute로 props를 넘겨준다. 

`<ChildComponent propsName=propsValue />`

`propsValue` 가 string 일 때는 "string" 형태로 전달하고, string이 아닌 경우에는 curly braces로 감싸준 다음 전달한다.

props는 여러 개의 정보를 한꺼번에 전달할 수도 있다.

```react
<Example message="This is some top secret info." />
<Greeting myInfo={["top", "secret", "lol"]} />
<Greeting name="Frarthur" town="Flundon" age={2} haunted={false} />
```

props로 function을 전달할 수도 있다. (주로, event handler function)

리액트에서 이벤트 핸들러는 컴포넌트 클래스의 메서드로 정의된다.

자식 컴포넌트 입장에서 props는 읽기 전용이다.



- defaultProps

  기본값 설정

```react
// 1) 클래스 내부에 작성
static defaultProps = {
    propsName: propsValue;
}
// 2) 클래스 외부에 작성 or Functional component인 경우
ComponentName.defaultProps = {
    propsName: propsValue;
}
```



* propTypes

  to define the props you expect

  type-checking features to verify that components receive props of the correct type

  will throw a useful warning when the data is of any other type or the prop is not provided (isRequired)

```react
import React, { PropTypes } from 'react';

COMPONENT_NAME.propTypes = {
	PROP_NAME: React.PropTypes.EXPECTED_DATA_TYPE
}

Runner.propTypes = {
  message:   React.PropTypes.string.isRequired,
  style:     React.PropTypes.object.isRequired,
  isMetric:  React.PropTypes.bool.isRequired,
  miles:     React.PropTypes.number.isRequired,
  milesToKM: React.PropTypes.func.isRequired,
  races:     React.PropTypes.array.isRequired
};
```

[참고]

`props` is the name of the object that stores passed-in information. `this.props` refers to that storage object. At the same time, each piece of passed-in information is called a prop. This means that `props`could refer to two pieces of passed-in information, or it could refer to the object that stores those pieces of information. 



- pass & attach EventListener

Your prop name should be the word `on`, plus your event type. If you are listening for a "click" event, then you name your prop `onClick`. If you are listening for a "keyPress" event, then you name your prop `onKeyPress`:

`onClick` attribute creates an *event listener*, listening for clicks on the HTML-like elements such as `<button></button>` 

```react
<Button onClick={this.handleClick} />
// component instance에서는 단순한 prop 이름으로 전달됨
<button onClick={this.props.onClick} />
// HTML-like JSX element에서는 이벤트리스너를 생성하여 연결
```



- this.props.children

모든 컴포넌트의 `props` 객체에는 `children` 이라는 이름의 property가 존재한다. `this.props.children` 은 JSX 태그의 시작과 끝태그 사이에 있는 모든 것을 리턴해준다. 리턴값이 하나보다 많은 경우에는 array 형태로 리턴.



#### State

state는 props와 달리 외부로부터 전달받는 게 아니라, 컴포넌트 내부에서 정의되어지며, 변경 가능하다.

similar to props, but it is private and fully controlled by the component.

a feature available only to classes.

consists of any data your application needs to know about, that can change over time. 

create state in a React component by declaring a `state`property on the component class in its `constructor`. This initializes the component with `state`when it is created. The `state`property must be set to a JavaScript `object`.

```react
class AnyComponent extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            example: 'react' // 해당 type에 맞게 초기화해주어야 한다
		}
    }
    render() {
        return (
        	<div>{this.state.example}</div>
        )
    }
}
```

You have access to the `state`object throughout the life of your component. You can update it, render it in your UI, and pass it as props to child components.



* update `state`

메서드 내부에서 state 값을 변경할 때에는 반드시 `this.setState()` 메서드를 사용해야 한다. 

setState()의 인자로는 변경할 state 내용을 key-value pair의 object로 전달한다.

The keys are your state properties and the values are the updated state data.

State updates through the `setState`method can be asynchronous.

(batch multiple state updates in order to improve performance)

setState는 function을 argument로 받을 수도 있다.

Any time that you call this.setState(), this.setState() AUTOMATICALLY calls .render() as soon as the state has changed.



* handling `state`

컴포넌트 내부의 state를 핸들링하기 위해서는 별도의 커스텀 메서드(Event Listener)를 정의해줘야 한다.

whenever you define an event handler that uses `this`, you need to add `this.methodName = this.methodName.bind(this)` to your constructor function.

이렇게 명시적으로 `this` 를 바인딩해주지 않으면, `this` 가 해당 컴포넌트의 인스턴스를 가리키지 못하고 `undefined` 가 된다.



**Virtual DOM**

`State`is one of the most powerful features of components in React. It allows you to track important data in your app and render a UI in response to changes in this data. If your data changes, your UI will change. React uses what is called a virtual DOM, to keep track of changes behind the scenes. When state data updates, it triggers a re-render of the components using that data - including child components that received the data as a prop. React updates the actual DOM, but only where necessary. This means you don't have to worry about changing the DOM. You simply declare what the UI should look like.

Note that if you make a component stateful, no other components are aware of its `state`. Its `state`is completely encapsulated, or local to that component, unless you pass state data to a child component as `props`. This notion of encapsulated `state`is very important because it allows you to write certain logic, then have that logic contained and isolated in one place in your code.



#### Props vs. State

A React component should use `props` to store information that can only be changed by a different component and use `state` to store information that the component itself can change.

Props is a reference to an object that represents the data that a Parent component has given to a Child component.

State is a reference to an (immutable) Object that represents the data that a component uses to render itself.



