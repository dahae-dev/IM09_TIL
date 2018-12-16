# Instantiation Patterns



#### 1. Functional Instantiation

```javascript
var Car = function(position) {
    var someInstance = {};
    someInstance.position = position;
    someInstance.move = function() {
        this.position += 1;
    }
    return someInstance;
};

var car1 = Car(5);
var car2 = Car(10);
car1.move();
```



#### 2. Functional Shared Instantiation

```javascript
var extend = function(to, from) {
    for(var key in from){
        to[key] = from[key]
    }
};

var someMethods = {};
someMethods.move = function() {
    this.position += 1;
}

var Car = function(position) {
    var someInstance = {
        position: position
    };
    
    extend(someInstance, someMethods);
    return someInstance;
}

var car1 = Car(5);
var car2 = Car(10);
```



#### 3. Prototypal Instantiation

```javascript
var someMethods = {};
someMethods.move = function() {
    this.position += 1;
};

var Car = function(position) {
    var someInstance = Object.create(someMethods);
    someInstance.position = position;
    return someInstance;
};

var car1 = Car(5);
var car2 = Car(10);
```





#### 4. Pseudoclassical Instantiation - 'new' keyword

```javascript
var Car = function(position) {
    this.position = position;
};

Car.prototype.move = function() {
    this.position += 1;
};

var car1 = new Car(5);
var car2 = new Car(10);
```



#### [ES6] class keyword - 'new' keyword

```javascript
class Car {
    constructor(position){
        this.position = position;
    };
    
    move() {
        this.position += 1;
    };
};

var car1 = new Car(5);
var car2 = new Car(10);
```

