# Express.js - router



#### Create an Express application.

```javascript
// on the top of the file
const express = require('express');
const app = express(); // returns an instance of an Express application.
// This application can then be used to start a server and specify server behavior.
```

The purpose of a server is to listen for requests, perform whatever action is required to satisfy the request, and then return a response.

To actually start your server listening, run the command `node app.js` to run your server in Node.



#### Start a working express server

Tell the server to listen on a given port, putting it in running state.

tell the server where to *listen* for new requests by providing a port number argument to a method called `app.listen()`

```javascript
// at the bottom of the file
app.listen(PORT, OPTIONAL_CALLBACK);

// ex)
const PORT = 4001;
app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

The second argument is a callback function that will be called once the server is running and ready to receive responses.



#### Implement route handler

To tell our server how to deal with any given request, we register a series of *routes*. Routes define the control flow for requests based on the request's *path* and HTTP verb.

* METHOD is an http method in lowercase. 

* PATH is a relative path on the server, which is the part of a request URL after the [hostname](https://en.wikipedia.org/wiki/Hostname) and port number (it can be a string, or even a regular expression). 

* HANDLER is a callback function that Express calls to handle the request and send a response when the route is matched.

```javascript
app.METHOD(PATH, HANDLER);

// ex) 'GET' method
app.get('/', function(req, res, next) { // HANDLER is supposed to pass in req, res object
    res.send('hello'); // send a response
})

// 동일한 route path에 chaining 가능
app.route(path).get(handler).post(handler)
```

Express tries to match requests by route, meaning that if we send a request to :/api-endpoint, the Express server will search through any registered routes in order and try to match '/api-endpoint'



#### Implement REST API 

HTTP follows a one request-one response cycle. Each client expects exactly one response per request, and each server should only send a single response back to the client per request. 

While an HTML server serves (you guessed it!) HTML, an API serves data. A REST (REpresentational State Transfer) API allows data exchange in a simple way, without the need for clients to know any detail about the server. 

The client only needs to know where the resource is (the URL), and the action it wants to perform on it (the verb). The GET verb is used when you are fetching some information, without modifying anything. 

* Send a response

Express servers send responses using the `.send()`method on the response object. `res.send()` will take any input and include it in the response body.

* Set status codes

The `res` object has a `.status()` method to allow us to set the status code, and other methods like `.send()` can be chained from it.

```javascript
const monsterStoreInventory = { fenrirs: 4, banshees: 1, jerseyDevils: 4, krakens: 3 };
app.get('/monsters-inventory/:name', (req, res, next) => {
  const monsterInventory = monsterStoreInventory[req.params.name];
  if (monsterInventory) {
    res.send(monsterInventory);
  } else {
    res.status(404).send('Monster not found');
  }
});
```

* Serve JSON on a specific route

These days, the preferred data format for moving information around the web is JSON. Simply put, JSON is a convenient way to represent a JavaScript object as a string, so it can be easily transmitted.

```javascript
// ex) create a simple API by creating a route that responds with JSON at the path /json
app.get('/json', function(req, res) {
  res.json({"message": "Hello json"});
});
```

 `res.json()` : pass in an object as an argument, close the request-response loop, returning the data. Behind the scenes it converts a valid JavaScript object into a string, then sets the appropriate headers to tell your browser that you are serving JSON, and sends the data back. A valid object has the usual structure `{key: data}`. Data can ba a number, a string, a nested object or an array. Data can also be a variable or the result of a function call; in which case it will be evaluated before being converted into a string.



#### Serve an HTML file

Respond with a file using the method `res.rendFile(ABSOLUTE_PATH)`

Behind the scenes this method will set the appropriate headers to instruct your browser on how to handle the file you want to send, according to its type. Then it will read and send the file. This method needs an absolute file path. We recommend you to use the Node global variable `__dirname`to calculate the path.

```javascript
res.rendFile(ABSOLUTE_PATH);

// ex)
app.get('/', function(req, res) {
    res.sendFile(__dirname + '/views/index.html');
})
```



#### Get input from the client

* using route parameter 

Parameters are route path segments that begin with `:` in their Express route definitions. They act as wildcards, matching any text at that path segment. For example `/monsters/:id` will match both`/monsters/1` and `/monsters/45`.

Express parses any parameters, extracts their actual values, and attaches them as an object to the request object: `req.params`. This object's keys are any parameter names in the route, and each key's value is the actual value of that field per request.

```javascript
route_path: '/user/:userId/book/:bookId'
actual_request_URL: '/user/546/book/6754' 
req.params: {userId: '546', bookId: '6754'}

app.get("/:word/echo", function(req, res) {
  var word = req.params.word;
  res.json({"echo": word});
});
```

* Using query strings

The query strings appear at the end of the path in URLs, and they are indicated with a `?` character, and includes field=value couples. Each couple is separated by an ampersand (&). Express can parse the data from the query string, and populate the object `req.query`.

Query strings do not count as part of the route path. Instead, the Express server parses them into an object and attaches it to the request body as `req.query`. The `key: value` relationship is indicated by the `=` character in a query string, and key-value pairs are separated by `&`.

```javascript
route_path: '/library'
actual_request_URL: '/library?userId=546&bookId=6754' 
req.query: {userId: '546', bookId: '6754'}

app.route("/name").get(function(req, res) {
  var firstname = req.query.first;
  var lastname = req.query.last;
  res.json({ "name": `${firstname} ${lastname}`});
})
```

When updating, many servers will send back the updated resource after the updates are applied so that the client has the exact same version of the resource as the server and database.



#### Use body-parser to parse POST requests

POST is the default method used to send client data with HTML forms. In the REST convention POST is used to send data to create new items in the database (a new user, or a new blog post).

In these kind of requests the data doesn’t appear in the URL, it is hidden in the request body. This is a part of the HTML request, also called payload. Since HTML is text based, even if you don’t see the data, it doesn’t mean that they are secret. The raw content of an HTTP POST request is shown below:

```javascript
POST /path/subpath HTTP/1.0
From: john@example.com
User-Agent: someBrowser/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 20
name=John+Doe&age=25 // the query string is the default format used by HTML forms

// [참고] With Ajax we can also use JSON to be able to handle data having a more complex structure. There is also another type of encoding: multipart/form-data. This one is used to upload binary files.
```

To parse the data coming from POST requests, you have to install a package: the body-parser. This package allows you to use a series of middleware, which can decode data in different formats. 

```javascript
var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: false }));
```



#### Get data from POST requests

A form in the html frontpage will submit the same data of exercise 10 (Query string). If the body-parser is configured correctly, you should find the parameters in the object `req.body`. 

```javascript
route: POST '/library'
urlencoded_body: userId=546&bookId=6754 
req.body: {userId: '546', bookId: '6754'}
```



#### HTTP methods (CRUD)

* POST (sometimes PUT) - Create a new resource using the information sent with the request (201)
* GET - Read an existing resource without modifying it (200)
* PUT or PATCH (sometimes POST) - Update existingresources using the data sent

* DELETE - Delete a resource (204 No Content)

Except from GET, all the other methods listed above can **have a payload (i.e. the data into the request body)**. The body-parser middleware works with these methods as well.



```javascript
const express = require('express');
const app = express();

const { getElementById, getIndexById, updateElement,
  seedElements, createElement } = require('./utils');

const PORT = process.env.PORT || 4001;
// Use static server to serve the Express Yourself Website
app.use(express.static('public'));

let animals = [];
seedElements(animals, 'animals');

// Get all animals
app.get('/animals', (req, res, next) => {
  res.send(animals);
});

// Get a single animal
app.get('/animals/:id', (req, res, next) => {
  const animal = getElementById(req.params.id, animals);
  if (animal) {
    res.send(animal);
  } else {
    res.status(404).send();
  }
});

// Create an animal
app.post('/animals', (req, res, next) => {
  const receivedAnimal = createElement('animals', req.query);
  if (receivedAnimal) {
    animals.push(receivedAnimal);
    res.send(receivedAnimal);
  } else {
    res.status(400).send();
  }
});

// Update an animal
app.put('/animals/:id', (req, res, next) => {
  const animalIndex = getIndexById(req.params.id, animals);
  if (animalIndex !== -1) {
    updateElement(req.params.id, req.query, animals);
    res.send(animals[animalIndex]);
  } else {
    res.status(404).send();
  }
});

// Delete a single animal
app.delete('/animals/:id', (req, res, next) => {
  const animalIndex = getIndexById(req.params.id, animals);
  if (animalIndex !== -1) {
    animals.splice(animalIndex, 1);
    res.status(204).send();
  } else {
    res.status(404).send();
  }
});

app.listen(PORT, () => {
  console.log(`Server is listening on ${PORT}`);
});
```



#### Express.Router

An Express router provides a subset of Express methods. To create an instance of one, we invoke the `.Router()` method on the top-level Express import.

To use a router, we *mount* it at a certain path using `app.use()` and pass in the router as the second argument. This router will now be used for all paths that begin with that path segment.

```javascript
// app.js
const express = require('express');
const app = express();

const { getElementById, getIndexById, updateElement,
  seedElements, createElement } = require('./utils');

const PORT = process.env.PORT || 4001;
// Use static server to serve the Express Yourself Website
app.use(express.static('public'));

// Import and mount the expressionsRouter
const expressionsRouter = require('./expressions.js');
app.use('/expressions', expressionsRouter);

const animalsRouter = require('./animals.js'); // import router
app.use('/animals', animalsRouter);

app.listen(PORT, () => {
  console.log(`Server is listening on ${PORT}`);
});
```

```javascript
// animals.js
const express = require('express');

const { getElementById, getIndexById, updateElement,
  seedElements, createElement } = require('./utils');

let animals = [];
seedElements(animals, 'animals');

animalsRouter = express.Router(); // create router

// Get all animals
animalsRouter.get('/', (req, res, next) => {
  res.send(animals);
});

// Get a single animal
animalsRouter.get('/:id', (req, res, next) => {
  const animal = getElementById(req.params.id, animals);
  if (animal) {
    res.send(animal);
  } else {
    res.status(404).send();
  }
});

// Create an animal
animalsRouter.post('/', (req, res, next) => {
  const receivedAnimal = createElement('animals', req.query);
  if (receivedAnimal) {
    animals.push(receivedAnimal);
    res.status(201).send(receivedAnimal);
  } else {
    res.status(400).send();
  }
});

// Update an animal
animalsRouter.put('/:id', (req, res, next) => {
  const animalIndex = getIndexById(req.params.id, animals);
  if (animalIndex !== -1) {
    updateElement(req.params.id, req.query, animals);
    res.send(animals[animalIndex]);
  } else {
    res.status(404).send();
  }
});

// Delete a single animal
animalsRouter.delete('/:id', (req, res, next) => {
  const animalIndex = getIndexById(req.params.id, animals);
  if (animalIndex !== -1) {
    animals.splice(animalIndex, 1);
    res.status(204).send();
  } else {
    res.status(404).send();
  }
});

module.exports = animalsRouter; // export router
```



* `router.param()`

```javascript
app.param('spellId', (req, res, next, id) => {
  SpellBook.find(id, (err, spell) => {
    if (err) {
      next(err);
    } else if (spell) {
      req.spell = spell;
      next();
    } else {
      next(new Error('Your magic spell was not found in any of our tomes'));
    }
  });
});
```



* merge parameters

```javascript
const sorcererRouter = express.Router();
const familiarRouter = express.Router({mergeParams: true});

sorcererRouter.use('/:sorcererId/familiars', familiarRouter);

sorcererRouter.get('/', (req, res, next) => {
  res.status(200).send(Sorcerers);
  next();
});

sorcererRouter.param('sorcererId', (req, res, next, id) => {
  const sorcerer = getSorcererById(id);   
  req.sorcerer = sorcerer;
  next();
});

familiarRouter.get('/', (req, res, next) => {
  res.status(200).send(`Sorcerer ${req.sorcerer} has familiars ${getFamiliars(sorcerer)}`);
});

app.use('/sorcerer', sorcererRouter);
```



```javascript
const express = require('express');
const app = express();
const bodyParser = require('body-parser');

app.use(express.static('public'));

const PORT = process.env.PORT || 4001;

const vendingMachine = [
  {
    id: 1,
    name: 'Gum',
    price: 1.25,
  },
  {
    id: 7,
    name: 'Bag of chips',
    price: 3.50,
  },
  {
    id: 23,
    name: 'cumin',
    price: .75,
  }
];

let nextSnackId = 24;

app.use(bodyParser.json());

// router.param() 예시
app.param('snackId', (req, res, next, id) => {
  const snackId = Number(id);
  const snackIndex = vendingMachine.findIndex(snack => snack.id === snackId);
  if (snackIndex === -1) {
    res.status(404).send('Snack not found!');
  } else {
    req.snackIndex = snackIndex;
    next();
  }
});

app.get('/snacks/', (req, res, next) => {
  res.send(vendingMachine);
});

app.post('/snacks/', (req, res, next) => {
  const newSnack = req.body;
  if (!newSnack.name || !newSnack.price) {
    res.status(400).send('Snack not found!');
  } else {
    newSnack.id = nextSnackId++;
    vendingMachine.push(newSnack);
    res.send(newSnack);
  }
});

app.get('/snacks/:snackId', (req, res, next) => {
  res.send(vendingMachine[req.snackIndex]);
});

app.put('/snacks/:snackId', (req, res, next) => {
  vendingMachine[req.snackIndex] = req.body;
  res.send(vendingMachine[req.snackIndex]);
});

app.delete('/snacks/:snackId', (req, res, next) => {
  vendingMachine.splice(req.snackIndex, 1);
  res.status(204).send();
});

app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```



