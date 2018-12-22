# Typescript Syntax



#### type

``` typescript
let a: number;
let b: boolean;
let c: string;
let d: any;
let e: number[] = [1, 2, 3];
let f: any[] = [1, true, 'a', false];
```



#### enum

```typescript
const ColorRed = 0;
const ColorGreen = 1;
const ColorBlue = 2;

enum Color { Red = 0, Green = 1; Blue = 2, Purple = 3 }
let backgroundColor = Color.Red;
```



#### type assersion

```typescript
let message;
message = 'abc';
let endWithC = (<string>message).endsWith('c');
let alternativeWay = (message as string).endsWith('c');
```



#### custom types : inline annotation

```typescript
let drawPoint = (point: { x: number, y: number }) => {
    // ...
}

drawPoint({
    x: 1,
    y: 2
})
```



#### custom types : interface

```typescript
interface Point {
    x: number,
    y: number,
    draw: () => void
}

let drawPoint = (point: Point) => {
    // ...
}

let getDistance = (point1: Point, point2: Point) => {
    // ...
}

drawPoint({
    x: 1,
    y: 2
})
```



#### custom types : class

Groups variables(properties) and functions(methods) that are highly related. => Cohesion principle

```typescript
class Point {
    // properties
    x: number;
    y: number;
    
    // constructor : initialize properties
    constructor(x?: number, y?: number) { // question mark means 'optional'
        this.x = x;
        this.y = y;
    }
    
    // methods
    draw() {
    	console.log('x: ' + this.x + ', y: ' + this.y);
    }
    
    getDistance(another: Point) {
        // ...
    }
}

let point: Point = new Point(1, 2); // allocate memory to use the object
point.draw();
```



#### access modifiers

* public (default)
* private
* protected

```typescript
class Point {
    constructor(private x?: number, private y?: number) { 
    }
    
    public draw() {
    	console.log('x: ' + this.x + ', y: ' + this.y);
    }
}

let point: Point = new Point(1, 2);
point.x = 3; // ERROR!!!
point.draw();
```



#### property : getter & setter

```typescript
class Point {
    constructor(private _x?: number, private _y?: number) { 
    }
    
    draw() {
    	console.log('x: ' + this._x + ', y: ' + this._y);
    }
    
    get x() {
        return this._x;
    }
    
    set x(value) {
        if (value < 0) {
            throw new Error('value cannot be less than 0.');
        }
        this._x = value;
    }
}

let point: Point = new Point(1, 2); 
let x = point.x; // point.getX();
point.x = 10; // point.setX(10);
point.draw();
```



#### modularity

Each file is a module.

```typescript
// point.ts
export class Point {
    constructor(private _x?: number, private _y?: number) { 
    }
    
    draw() {
    	console.log('x: ' + this._x + ', y: ' + this._y);
    }
}

// main.ts
import { Point } from './point';

let point = new Point(1, 2);
point.draw();
```



#### Typescript in React

* Install typrings for React allowing TypeScript to type check React code

```
$ yarn add --dev @types/react | @types/react-native
```



#### TSLint

https://medium.com/@sgroff04/configure-typescript-tslint-and-prettier-in-vs-code-for-react-native-development-7f31f0068d2

https://spin.atomicobject.com/2017/06/05/tslint-linting-setup/

https://itnext.io/next-js-awesome-typescript-integration-d05cfe9af057