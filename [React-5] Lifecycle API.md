# [React] Lifecycle API

#### Phase 1. Mounting

- Mounting : 컴포넌트가 브라우저 상에 나타날 때

  - `constructor` : 컴포넌트가 생성되어 브라우저 상에 처음 나타날 때 가장 먼저 실행되는 함수, 컴포넌트가 가지고 있는 state 초기 설정 등

    ```react
    class App extends React.Component {
        constructor(props) { // 반드시 props를 인자로 받는다
            super(props); // 상속받은 React.Component의 생성자를 먼저 호출해준다
        }
    }
    ```

  - `componentWillMount` (deprecated) : 컴포넌트가 render 되기 전에 호출

  - `static getDerivedStateFromProps` : props로 받은 값을 state에 그대로 동기화시키고 싶을 때, 특정 props가 바뀔 때 설정하고 싶은 state값을 리턴하는 형태

    ```react
    static getDerivedStateFromProps(nextProps, prevState) {
        if (nextProps.value !== prevState.value) {
        	return { value: nextProps.value };
    	}
    	return null; // 따로 업데이트 할 것은 없다라는 의미
    }
    ```

  - `render` : mounting & updating에 걸쳐서 실행

  - `componentDidMount` : HTML이 로딩 되자마자 호출

    When you call an API in this method, and set your state with the data that the API returns, it will automatically trigger an update once you receive the data.

    - DOM에 관련된 작업 및 이벤트 리스닝

      It's good practice to use this lifecycle method to do any clean up on React components before they are unmounted and destroyed. Removing event listeners is an example of one such clean up action.

    - 외부 라이브러리 연동 및 데이터 요청 시 ex. Ajax 요청 등

      컴포넌트가 마운트 되기 전에 외부 API로부터 response가 오는 경우 제대로 렌더링되지 않을 수 있기 때문에,

      컴포넌트가 마운트된 다음에 외부 데이터를 받아와서 렌더링 할 수 있도록 해야한다.

    - set timers ex. setTimeout, setInterval 등



rendering and mounting are considered different things in the component lifecycle. When a page first loads, all components are mounted and this is where methods like `componentWillMount()`and `componentDidMount()`are called. After this, as state changes, components re-render themselves.



#### Phase 2. Updating

- Updating : 컴포넌트의 props / state 의 값이 바뀔 때

  - `componentWillReceiveProps` : nextProps값이 렌더링 되기 전에 비교하여 렌더링 할 지 말 지 결정

    - nextProps라는 객체를 argument로 자동 전달받는다. `setState` 사용 가능.

  - `shouldComponentUpdate` : 특정 조건에 따라 Virtual DOM에 렌더링 할 지 말지 결정 (Boolean값  리턴)

    - nextProps, nextState 객체를 argument로 자동 전달받는다.
    - true값을 리턴할 때에만 render() 호출

    - 성능 최적화를 위해 사용

      You can use `shouldComponentUpdate()`to prevent this by comparing the `props`. The method must return a `boolean`value that tells React whether or not to update the component. You can compare the current props (`this.props`) to the next props (`nextProps`) to determine if you need to update or not, and return `true`or `false`accordingly.

  - `componentWillUpdate` (deprecated) : to interact with things *outside* of the React architecture. If you need to do non-React setup before a component renders, such as checking the `window`size or interacting with an API, then `componentWillUpdate` is a good place to do that.

    - nextProps, nextState 객체를 argument로 자동 전달받는다.

  - `render`

  - `getSnapshotBeforeUpdate` : 렌더링하고 업데이트 하기 바로 직전에 호출하는 함수로, 값 확인 가능

  - `componentDidUpdate` : render() 호출하여 HTML이 모두 로딩된 직후 호출, state가 변경되었을 때 실행

    usually used for interacting with things outside of the React environment, like the browser or APIs.

    - prevProps, prevState 객체를 argument로 자동 전달받는다.



#### Phase 3. Unmounting

- Unmounting : 컴포넌트가 브라우저 상에서 사라질 때

  - `componentWillUnmount` : DOM에서 컴포넌트가 제거되기 직전 호출
    - mounting 시 설정했던 이벤트 리스닝을 제거

- 에러 발생 시

  - componentDidCatch : 에러가 발생할 수 있는 컴포넌트의 부모 컴포넌트에서 구현해줘야 한다.

    ```react
    componentDidCatch(error, info) {
        // error : 발생한 에러 정보
        // info : 에러가 발생한 컴포넌트의 정보
    }
    ```

