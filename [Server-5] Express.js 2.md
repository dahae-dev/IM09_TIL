# Express.js - middleware



Middleware is code that executes between a server receiving a request and sending a response. It operates on the boundary, so to speak, between those two HTTP actions.

In Express, middleware is a function. Middleware can perform logic on the request and response objects, such as: inspecting a request, performing some logic based on the request, attaching information to the response, attaching a status to the response, sending the response back to the user, or simply passing the request and response to another middleware. Middleware can do any combination of those things or anything else a Javascript function can do.

```javascript
app.use([PATH,] CALLBACK [, CALLBACK...])

app.use((req, res, next) => {
  console.log('Request received');
});
```

**`app.use()` takes a callback function that it will call for every received request so that it can be used only when shared by all of our routes.** In this example, every time the server receives a request, it will find the first registered middleware function and call it. 

We can also write middleware that will run for every request at a specific path.

```javascript
app.use('/login', (req, res, next) => {
  console.log('User has hit endpoint /login');
  next();
});
```

The function signature of an Express middleware, i.e., `(req, res, next)` is the very same that we've used for Express routes in the past. That's because express routes are middleware. **Every route created in Express is also a middleware function handling the request and response objects at that part of the stack.** Express routes also have the option of sending a response body and status code and closing the connection. These two features are a byproduct of Express routes being middleware, because all Express middleware functions have access to the request, the response, and the next middleware in the stack.



To quote the [Express documentation](http://expressjs.com/en/guide/using-middleware.html):

*An Express application is essentially a series of middleware function calls.*

It is precisely this service that we leverage Express for. In addition to performing the routing that allows us to communicate appropriate data for each separate endpoint, we can perform application logic we need by implementing the necessary middleware.



#### Serve static assets

Place the static assets needed by the application(stylesheets, scripts, images) using the middleware `express.static(ABSOLUTE_PATH)` 

- Mount the middleware

Why use middleware? code reusability ---> 유지보수 용이

Basically middlewares are functions that intercept route handlers, adding some kind of information. A middleware needs to be mounted using the method `app.use(OPTIONAL_PATH, middlewareFunction)`

If you don’t pass first path argument, the middleware will be executed for all the requests.

```javascript
// ex) mount the express.static() middleware for all the requests
app.use(express.static(__dirname + '/public')); 
```



#### Use the .env file

The `.env`file is a hidden file that is used to pass environment variables to your application. This file is secret, no one but you can access it, and it can be used to store data that you want to keep private or hidden. For example, you can store API keys from external services or your database URI. You can also use it to store configuration options. By setting configuration options, you can change the behavior of your application, without the need to rewrite some code.

The environment variables are accessible from the app as `process.env.VAR_NAME`. The `process.env`object is a global Node object, and variables are passed as strings. By convention, the variable names are all uppercase, with words separated by an underscore. The `.env`is a shell file, so you don’t need to wrap names or values in quotes. 

```javascript
process.env.VAR_NAME=value // cannot be space around the equals sign when assigning values

// ex) 
process.env.MESSAGE_STYLE="uppercase";
app.get('/json', function(req, res) {
  if(process.env.MESSAGE_STYLE==="uppercase") {
    res.json({"message": "HELLO JSON"});
  }
  res.json({"message": "Hello json"});
});
```



#### Implement a root-level middleware

Middleware functions are functions that take 3 arguments: the request object, the response object, and the next function in the application’s request-response cycle. 

These functions execute some code that can have side effects on the app, and usually add informations to the request or response objects. They can also end the cycle sending the response, when some condition is met. If they don’t send the response, when they are done they start the execution of the next function in the stack. This is triggered calling the 3rd argument `next()`. 

```javascript
// A root-level middleware should be mounted before all the routes
app.use(function(req, res, next) { // middleware function is supposed to pass in req, res object and next callback function
    console.log("I'm a middleware...");
    console.log(`${req.method} ${req.path} - ${req.ip}`);
    next();
});

// logged : GET /json - ::ffff:127.0.0.1
```

`app.use(<mware-function>)` will be executed for all the requests. `app.post(<mware-function>)` can be executed only for POST requests, and same for all the http verbs (GET, DELETE, PUT, …)



#### Chaining middleware : next()

Most of Express's functionality is chaining middleware. This chain of middleware is referred to as the *middleware stack*.

The middleware stack is processed in the order they appear in the application file, such that middleware defined later happens after middleware defined before. It's important to note that this is regardless of method — an `app.use()` that occurs after an `app.get()` will get called after the `app.get()`. 

An Express middleware is a function with three parameters: `(req, res, next)`. The sequence is expressed by a set of callback functions invoked progressively after each middleware performs its purpose. The third argument to a middleware function, `next`, should get explicitly called as the last part of the middleware's body. This will hand off the processing of the request and the construction of the response to the next middleware in the stack.

```javascript
app.METHOD(PATH, OPTIONAL_MIDDLEWARE, HANDLER);

// ex)
app.get('/now', function(req, res, next) {
  req.time = new Date().toString();
  next();
}, function(req, res) {
  res.json({"time": req.time});
});
```

```javascript
app.use((req, res, next) => { // middleware
  console.log("A sorcerer approaches!");
  next();
});

app.get('/magic/:spellname', (req, res, next) => {
  console.log("The sorcerer is casting a spell!");
  next(); // chaining
});

app.get('/magic/:spellname', (req, res, next) => {
  console.log(`The sorcerer has cast ${req.params.spellname}`);
  res.status(200).send();
});

app.get('/magic/:spellname', (req, res, next) => {
  console.log("The sorcerer is leaving!");
});

// Accessing http://localhost:4001/magic/fireball 
// Console Output:
// "A sorcerer approaches!"
// "The sorcerer is casting a spell!"
// "The sorcerer has cast fireball"
```

In the above code, the routes are called in the order that they appear in the file, provided the previous route called `next()` and thus passed control to the next middleware. We can see that the final matching call was not printed. This is because the previous middleware did not invoke the `next()` function to run the following middleware.

**The reason why it is useful to have `next()` as a separate function call is that we don't always want to pass control to the next middleware in the stack.**



#### Route-Level app.use() - Multiple Path

Express documentation for `app.use()`:

"*argument*: path

*description*: The path for which the middleware function is invoked; can be any of:

- A string representing a path.
- A path pattern.
- A regular expression pattern to match paths.
- An array of combinations of any of the above. "



#### Middleware Stacks

```javascript
const authenticate = (req, res, next) => {
  ...
};

const validateData = (req, res, next) => {
  ...
};

const getSpell = (req, res, next) => {
  res.status(200).send(getSpellById(req.params.id));
};

const createSpell = (req, res, next) => {
  createSpellFromRequest(req);
  res.status(201).send();
};

const updateSpell = (req, res, next) => {
  updateSpellFromRequest(req);
  res.status(204).send();
}

app.get('/spells/:id', authenticate, getSpell);

app.post('/spells', authenticate, validateData, createSpell);

app.put('/spells/:id', authenticate, validateData, updateSpell);
```



#### Open-Source Packages : morgan

Morgan is an open-source library for logging information about the HTTP request-response cycle in a server application. `morgan()` is a function that will *return a middleware function*, to reiterate: the return value of `morgan()` will be a function, that function will have the function signature `(req, res, next)` that can be inserted into an `app.use()`, and that function will be called before all following middleware functions. Morgan takes an argument to describe the formatting of the logging output. For example, `morgan('tiny')` will return a middleware function that does a "tiny" amount of logging. With morgan in place, we'll be able to remove the existing logging code. Once we see how fast it is to add logging with morgan, we won't have to spend time in the future trying to figure out how to replicate that functionality.

```javascript
const morgan = require('morgan');
app.use(morgan('dev')); // logging middleware
```



#### Open-Source Middleware: Body Parsing

```javascript
// implemented body-parser middleware
const bodyParser = (req, res, next) => {
  let queryData = '';
  req.on('data', (data) => {
    data = data.toString();
    queryData += data;
  });
  req.on('end', () => {
    if (queryData) {
      req.body = JSON.parse(queryData);
    }
    next();
  });
};

// using library
const bodyParser = require('body-parser');

```



#### Error-Handling Middleware

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

Express has its own error-handler, which catches errors that we haven't handled. But if we anticipate an operation might fail, we can invoke our error-handling middleware. We do this by passing an error object as an argument to `next()`. Usually, `next()` is called without arguments and will proceed through the middleware stack as expected. When called with an error as the first argument, however, it will call any applicable error-handling middleware.

```javascript
app.use((req, res, next) => {
  const newValue = possiblyProblematicOperation();
  if (newValue === undefined) {
    let undefinedError = new Error('newValue was not defined!');
    undefinedError.status = 404;
    return next(undefinedError);
  }
  next();
});

app.use((err, req, res, next) => {
  const status = err.status || 500;
  res.status(status).send(err.message);
});
```



#### Express Middleware

https://expressjs.com/en/resources/middleware.html

```javascript
const express = require('express');
const app = express();
const morgan = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler')
app.use(errorHandler());

app.use(express.static('public'));

const PORT = process.env.PORT || 4001;

const jellybeanBag = {
  mystery: {
    number: 4
  },
  lemon: {
    number: 5
  },
  rootBeer: {
    number: 25
  },
  cherry: {
    number: 3
  },
  licorice: {
    number: 1
  }
};

// Body-parsing Middleware
app.use(bodyParser.json());

// Logging Middleware
app.use(morgan('dev'));

app.use('/beans/:beanName', (req, res, next) => {
  const beanName = req.params.beanName;
  if (!jellybeanBag[beanName]) {
    return res.status(404).send('Bean with that name does not exist');
  }
  req.bean = jellybeanBag[beanName];
  req.beanName = beanName;
  next();
});

app.get('/beans/', (req, res, next) => {
  res.send(jellybeanBag);
});

app.post('/beans/', (req, res, next) => {
  const body = req.body;
  const beanName = body.name;
  if (jellybeanBag[beanName] || jellybeanBag[beanName] === 0) {
    return res.status(400).send('Bean with that name already exists!');
  }
  const numberOfBeans = Number(body.number) || 0;
  jellybeanBag[beanName] = {
    number: numberOfBeans
  };
  res.send(jellybeanBag[beanName]);
});

app.get('/beans/:beanName', (req, res, next) => {
  res.send(req.bean);
});

app.post('/beans/:beanName/add', (req, res, next) => {
  const numberOfBeans = Number(req.body.number) || 0;
  req.bean.number += numberOfBeans;
  res.send(req.bean);
});

app.post('/beans/:beanName/remove', (req, res, next) => {
  const numberOfBeans = Number(req.body.number) || 0;
  if (req.bean.number < numberOfBeans) {
    return res.status(400).send('Not enough beans in the jar to remove!');
  }
  req.bean.number -= numberOfBeans;
  res.send(req.bean);
});

app.delete('/beans/:beanName', (req, res, next) => {
  const beanName = req.beanName;
  jellybeanBag[beanName] = null;
  res.status(204).send();
});

app.put('/beans/:beanName/name', (req, res, next) => {
  const beanName = req.beanName;
  const newName = req.body.name;
  jellybeanBag[newName] = req.bean;
  jellybeanBag[beanName] = null;
  res.send(jellybeanBag[newName]);
});

app.use((err, req, res, next) => {
  res.status(500).send(err);
});

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

