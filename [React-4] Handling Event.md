# [React] Handling Event



#### Event Listener

1) stateful한 parent가 `state` 를 stateless한 child에게 props로 전달 가능

a *stateful*, parent component passes down a `prop` to a *stateless*, child component.

한 Child component는 parent로부터 전달받은 state를 display한다. 그 Sibling component는 parent의 state를 변경할 방법을 display한다. parent component는 이들을 rendering한다.



2) child가 parent의 state를 update 가능 (Event Listener 연결을 통해)

A stateful, parent component passes down an *event handler* to a stateless, child component. The child component then uses that *event handler* to update its parent's `state`.

A child component updates its parent's state, and the parent passes that state to a *sibling* component.

Components  should only have one job. 따라서 최대한 기능별로 component를 쪼갠 다음, 그에 맞는 props만 전달한다.

state 변경하는 메서드를 parent로부터 전달받아 이벤트리스너를 연결할 때, 제대로 된 argument를 넘겨주어야 한다. 만약 argument를 넘겨주지 않는다면, 모든 이벤트리스너와 마찬가지로 `event` object를 argument로 전달하게 된다.

따라서 새로운 이벤트핸들러 function을 하나 더 정의하여 그 안에서 state를 변경해줄 내용을 argument로 넘기면서 state를 바꾸는 메서드를 호출해준다.

```react
import React from 'react';

export class Child extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    // 항상 명시적으로 메서드를 현재 인스턴스의 this와 바인딩해준다
    // ** 메서드를 콜백으로 넘길 때에는 this를 해당 인스턴스에 명시적으로 바인딩해주지 않으면 this가 undefined가 된다 **
  }
  
  // parent로부터 넘겨받은 function을 호출할 새로운 funtion을 정의한다.
  handleChange(e) { // event object를 argument로 받는다
    const name = e.target.value;
    this.props.onChange(name);
  }
  
  render() {
    return (
      <div>
        <h1>
          Hey my name is {this.props.name}!
          {/*이벤트가 감지되면 parent의 state.name이 변경되어 child의 props.name도 변경된다 */}
        </h1>
        <select id="great-names" onChange={this.handleChange}> {/*새로운 funtion을 이벤트리스너로 연결, 익명함수 형태로도 작성 가능*/}
          <option value="Frarthur">
            Frarthur
          </option>

          <option value="Gromulus">
            Gromulus
          </option>

          <option value="Thinkpiece">
            Thinkpiece
          </option>
        </select>
      </div>
    );
  }
}
```



[참고] Naming convention

Here's how the naming convention works: first, think about what *type of event* you are listening for. In our example, the event type was "click."

If you are listening for a "click" event, then you name your *event handler* `handleClick`. If you are listening for a "keyPress" event, then you name your event handler `handleKeyPress`



#### Forms

input onChange

Controlled vs Uncontrolled

React can control the internal state for certain elements like `input`and `textarea`, which makes them controlled components. This applies to other form elements as well, including the regular HTML `form`element.



#### Separating component by logics

This pattern illustrates some important paradigms in React. The first is *unidirectional data flow*. State flows in one direction down the tree of your application's components, from the stateful parent component to child components. The child components only receive the state data they need. The second is that complex stateful apps can be broken down into just a few, or maybe a single, stateful component. The rest of your components simply receive state from the parent as props, and render a UI from that state. It begins to create a separation where state management is handled in one part of code and UI rendering in another. This principle of separating state logic from UI logic is one of React's key principles. When it's used correctly, it makes the design of complex, stateful applications much easier to manage.



#### Pass a callback as props

You can pass `state`as props to child components, but you're not limited to passing data. You can also pass handler functions or any method that's defined on a React component to a child component. This is how you allow child components to interact with their parent components. You pass methods to a child just like a regular prop. It's assigned a name and you have access to that method name under `this.props`in the child component.