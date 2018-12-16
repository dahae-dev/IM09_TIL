# [React] JSX Syntax



React 컴포넌트를 만들어 주는 Javascript + XML 형태의 특정 문법

Markup 언어로 표기된 엘리먼트를 자바스크립트로 변환(compile)되어야 한다.



#### JSX element의 특징

JSX element can be saved in a variable, passed to a function, stored in an object or array...

JSX elements can have *attributes*, just like HTML elements can.

```jsx
my-attribute-name="my-attribute-value"

// ex)
const panda = <img src="images/panda.jpg" alt="panda" width="500px" height="500px" />;
```



- **nested JSX**

can *nest* JSX elements inside of other JSX elements, just like in HTML.

must wrap the multi-line JSX expression in parentheses. 

JSX expression must return a single element so that it has exactly **"one" outermost element.** Several JSX elements written as siblings with no parent wrapper element will not transpile.

```react
const theExample = (
   <div> 
     <h1>Hello</h1>
     <h1>Bye</h1>
   </div>
);
// ** <Fragment> **
// <div> 대신 <Fragment> 태그를 사용하면 부가적인 div태그 생성 없이 nested element를 하나로 묶을 수 있다. 
```



- **Self-Closing Tags should be always closed**

`<img>, <input>, <br>` 등은 반드시 slash를 포함해 닫아줘야 한다. 



- **JavaScript injection into JSX using curly braces**

can access variables while inside of a JSX expression, even if those variables were declared on the outside.

for tasks like accessing props, passing props, accessing state, inserting comments into your code, and most recently, styling your components. 

*** Concatenating variables and strings**

`{'string' + variable}`

*** you can not inject an `if` statement into a JSX expression**

1) JSX 문법 바깥에서 `else/if`문 사용

2) Ternary Operator 사용 : `condition ? expressionIfTrue : expressionIfFalse`

3) && expression 사용 : `{condition && <p>markup</p>}`



* **rendering JSX element**

`ReactDOM.render()`'s first argument should be a JSX expression, and it will be rendered to the screen.

The first argument is *appended* to whatever element is selected by the *second* argument.

*only updates DOM elements that have changed*.

```react
ReactDOM.render(JSX expression, html element)
```

*** using Array.map() to dynamically render elements**

user input을 받아서 render하는 경우, 얼마를 받을지 모르기 때문에 constructor에서 array를 받는 state를 선언해 놓고, 그 array에 map을 돌려서 렌더링한다.



* **Event Listener in JSX**

An event listener attribute's *name* should be something like `onClick` or `onMouseOver` : the word `on`, plus the type of event that you're listening for.

An event listener attribute's *value* should be a function. 

Unlike html, event listener names are written in camelCase in JSX.



- **`key` attribute**

`id`  attribute와 유사하게 unique한 값을 value값으로 부여한다.

1. The list-items have *memory* from one render to the next. For instance, when a to-do list renders, each item must "remember" whether it was checked off. The items shouldn't get amnesia when they render.

2. A list's order might be shuffled. For instance, a list of search results might be shuffled from one render to the next.


* **style 적용**

  1) inline style

  written as an attribute using double curly braces

  ```react
  <h1 style={{ color: 'red' }}>Hello world</h1>
  ```

  top-level scope에 `style` 이라는 이름의 변수를 정의하여, 여러 개의 스타일을 한꺼번에 정의한 객체를 저장한 다음, JSX에  `style` attribute를 통해 inject 해준다.

  React에서는 module.exports를 통해 외부로 내보내지 않는 한, 파일끼리 서로 볼 수 없으므로 `style` 이라는 이름의 전역변수가 서로 충돌나지 않는다.

  ```react
  const styles = { // style 속성은 반드시 camelCase로 작성
    color: 'darkcyan',
    background: 'mintcream',
    fontSize: '16px', // 숫자 형식의 value도 무조건 string으로 작성해야 한다
    marginTop: 100 // 기본 단위가 px이기 때문에, 단위를 px로 사용하고 싶을 땐 숫자만 작성해도 된다
  };
  
  export class StyledClass extends React.Component {
    render() {
      return (
        <h1 style={styles}>
          Hello world
        </h1>
      );
    }
  }
  ```

  한 번 만들어 놓은 style을 export 해줌으로써 reuse도 가능하다.

  ```react
  export const styles = {
    fontFamily: fontFamily,
    background: background,
    fontSize:   fontSize,
    padding:    padding,
    color:      color
  }
  ```

   2) css 파일을 import하여 불러온 다음, 해당 element에 `className` attribute를 통해 적용해준다.



  - **`className` attribute**

    element에 `class` 이름을 부여할 때 attribute name을 `class` 대신, `className` 으로 사용해야 한다. 이는 JSX가 자바스크립트로 변환되고, 이 때 `class`가 자바스크립트의 예약어(reserved word) 이기 때문이다.

    JSX가 렌더링 될 때는 `className` attributes가 자동적으로 `class` attributes로 변환되어 렌더링 된다.

    ```react
    class App extends Component {
          render() {
              return <div className="App">Hello!</div>
          }
    }
    ```


* **Comment**

  ```jsx
  class App extends Component {
      render() {
          return (
              <div // 태그 내부에도 주석 가능>
                  Hello!
                  {/* JSX 주석 처리 */}
              </div>
          )
      }
  }
  ```



- **without JSX expression**

  ```react
  // JSX
  const h1 = <h1>Hello world</h1>;
  
  // without JSX
  const h1 = React.createElement(
    "h1",
    null,
    "Hello, world"
  );
  
  // 형식
  React.createElement(
    type,
    [props],
    [...children]
  )
  ```


