# [Express.js - 1] Building RESTful API



### Intro

* Hard-coding without Express.js

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    if (req.url === '/') {
        // do something...
    }
    if (req.url === '/api/courses') {
        // do something...
    }
    // keep adding if statements for new routes
});

server.listen(3000);
```

* REpresentational State Transfer

CRUD Operations : Create / Read / Update / Delete 

HTTP Methods : POST / GET  / PUT / DELETE



### Build Web Server

```javascript
// 'index.js'

const express = require('express'); // returns express function
const app = express(); // returns an object of type Express

app.get('/', (req, res) => { // two parameters: path, route handler
  res.send('Hello World');
});

app.get('/api/courses', (req, res) => {
  res.send([1, 2, 3]);
});

app.listen(3000, () => console.log(`Listening on port 3000...`));

// terminal

// install nodemon
$ sudo npm i -g nodemon

// run the application
$ nodemon index.js
Listening on port 3000...
```



### Environment Variables

hosting environment가 각자 다른 것을 고려하여, environment variable로 PORT 설정

```javascript
const PORT = process.env.PORT || 3000; 
app.listen(PORT, () => console.log(`Listening on port ${PORT}...`));

// run the application
$ nodemon index.js
Listening on port 3000...

// set an env var by executing the export command
$ export PORT=5000
$ nodemon index.js
Listening on port 5000..
```



### Route Parameter

* Route parameter

```javascript
app.get('/api/courses/:id', (req, res) => {
  res.send(req.params.id);
});

// GET, localhost:3000/api/courses/1
req.params = {
    id: "1"
}

app.get('/api/posts/:year/:month', (req, res) => {
  res.send(req.params);
});

// GET, localhost:3000/api/posts/2018/1
req.params = {
    year: "2018",
    month: "1"
}
```

* Query string parameter : optional

```javascript
app.get('/api/posts/:year/:month', (req, res) => {
  res.send(req.query);
});

// GET, localhost:3000/api/posts/2018/1?sortBy=name
req.query = {
    sortBy: "name"
}
```



### Handle HTTP GET / POST Requests

```javascript
const express = require('express'); // returns express function
const app = express(); // returns an object of type Express

app.use(express.json());

const courses = [
  { id: 1, name: 'course1' },
  { id: 2, name: 'course2' },
  { id: 3, name: 'course3' }
];

app.get('/', (req, res) => { // two parameters: path, route handler
  res.send('Hello World');
});

app.get('/api/courses', (req, res) => {
  res.send(courses);
});

app.post('/api/courses', (req, res) => {
  const course = {
    id: courses.length + 1,
    name: req.body.name
  };
  courses.push(course);
  res.send(course);
});

app.get('/api/courses/:id', (req, res) => {
  const course = courses.find(course => course.id === parseInt(req.params.id));
  if (!course) res.status(404).send('The course with the given Id was not found.');
  res.send(course);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Listening on port ${PORT}...`));


// GET, localhost:3000/api/courses/1
{
    id: 1,
    name: "course1"
}

// using POSTMAN
// POST, http://localhost:3000/api/courses
{
    "id": 4,
    "name": "new course"
}
```



### Input Validation

* manual validation

```javascript
app.post('/api/courses', (req, res) => {
  if (!req.body.name || req.body.name.length < 3) {
    // 400 Bad Request
    res.status(400).send('Name is required and should be minimum 3 characters.');
    return;
  }
  
  const course = {
    id: courses.length + 1,
    name: req.body.name
  };
  courses.push(course);
  res.send(course);
});
```

* validation using library 'joi'

```javascript
// install package to validate user's input
$ npm i joi (@13.1.0)  

// 'index.js'
const Joi = require('joi'); // returns class

app.post('/api/courses', (req, res) => {
  const schema = {
    name: Joi.string().min(3).required()
  };

  const result = Joi.validate(req.body, schema);
  if (result.error) {
    res.status(400).send(result.error.details[0].message);
    return;
  }

  const course = {
    id: courses.length + 1,
    name: req.body.name
  };
  courses.push(course);
  res.send(course);
});
```



### Handle HTTP PUT Requests

```javascript
app.put('/api/courses/:id', (req, res) => {
  // Look up the course
  // If not existing, return 404
  const course = courses.find(course => course.id === parseInt(req.params.id));
  if (!course) res.status(404).send('The course with the given Id was not found.');

  // Validate
  // If invalid, return 400 - Bad request
  const schema = {
    name: Joi.string().min(3).required()
  };

  const result = Joi.validate(req.body, schema);
  if (result.error) {
    res.status(400).send(result.error.details[0].message);
    return;
  }

  // Update course
  course.name = req.body.name;
  // Return the updated course
  res.send(course);
});
```



### Handle HTTP DELETE Requests

```javascript
app.delete('/api/courses/:id', (req, res) => {
  // Look up the course
  // Not existing, return 404
  const course = courses.find(course => course.id === parseInt(req.params.id));
  if (!course) return res.status(404).send('The course with the given Id was not found.');

  // Delete
  const index = courses.indexOf(course);
  courses.splice(index, 1);

  // Return the same course
  res.send(course);
});
```

