# [Express.js - 2] Middleware



### Middleware

미들웨어도 또 하나의 function.  Requrest object를 받고, 클라이언트에게 Response를 리턴해주거나 다른 미들웨어 함수로 제어권을 넘겨준다.

route handler function 또한 미들웨어. req option을 받고, 클라이언트한테 응답해줌으로써 request-response cycle이 종료되기 때문.

`express.json()` 또한 미들웨어. request를 읽고, body에 JSON document가 있다면 request.body를 JSON object로 파싱하여 `request.body` 프러퍼티로 설정해준다.



***request processing pipeline***

request —————> middleware ——> middleware —————> response

각 미들웨어는 응답객체를 리턴하거나 다른 미들웨어로 컨트롤을 넘김으로써 request-response cycle을 종료시킨다.



### Custom Middleware

custom middleware function은 이 파이프라인의 가장 앞에 놓고, 우리가 서버에서 받는 모든 request가 이 미들웨어를 통과할 수 있도록 한다. ex. Logging, authentication, authorization

주로 middleware function은 별도의 파일이나 모듈에서 정의한 다음, import 해와서 사용한다.

```javascript
// 'index.js'

const logger = require('./logger'); // import middleware function module
const express = require('express');
const app = express();

// mount한 순서대로 middleware function 실행
app.use(express.json());
app.use(logger);
app.use((req, res, next)=> {
    console.log('Authenticating...');
    next();
})

// 'logger.js' : middleware module

function log((req, res, next) => {
    console.log('logging...');
    next();
})

module.exports = log;
```



### Built-in Middleware

```javascript
// parses incoming requests into JSON object and set request.body
app.use(express.json()); 

// parses incoming requests with url encoded payloads, body looks like key=value&key=value
app.use(express.urlencoded({ extended: true})); 

// serve the static class
app.use(express.static('public')); // pass the name of folder where you put all the static assets
```

[참고] key=value&key=value 

Basically if you have an html form with input fields and post that form to the server, the body of your request will look like this.



### Third-party Middleware

You have to install third-party middlewares and import them before using them.

```javascript
// secure the application by setting various http headers
const helmet = require('helmet');
app.use(helmet());

// log http requests
const morgan = require('morgan');
app.use(morgan('tiny'));
```



### Environments

To enable certain features based on the current environment, we can set environment variable to development, to testing, to staging, production.

```javascript
// when environment variable is not set

process.env.NOD_ENV // undefined
app.get('env') // development 

if(app.get('env') === 'development') {
    app.use(morgan('tiny'));
    console.log('Morgan enabled...');
}

// run the server in terminal
Morgan enabled...
$ export NODE_ENV=production
// nothing logged

```



### Configuration

To store configuration settings for the application and override them in each evnironment,

you have to install node packages for managing configuration.

(npmjs.com/package/rc, npmjs.com/package/config)

```javascript
$ npm i config

// create 'config' folder and json files for each environment in the folder

// 'default.json'
{
    "name": "My Express App",
}

// 'development.json'
{
    "name": "My Express App - Development",
    "mail": {
        "host": "dev-mail-server"
    }
}

// 'production.json'
{
    "name": "My Express App - Production",
     "mail": {
        "host": "prod-mail-server"
     }
}

// 'index.js'
const config = require('config');
console.log('Application Name: ' + config.get('name'));
console.log('Mail Server: ' + config.get('mail.host'));

// terminal
$ export NODE_ENV=development
$ nodemon index.js

Application Name: My Express App - Development
Mail.Server: dev-mail-server
```



Secrets such as passwords should be stored in environment variables instead of configuration files and can be read using config module.

```javascript
$ export app_password=1234

// 'custom-environment-variables.js' in config folder
{
    "mail": {
        "password": "app_password" // what we need here is only mapping
    }
}

// 'index.js'
console.log('Mail Password: ' + config.get('mail.password'));

// terminal
$ nodemon index.js

Mail Password: 1234
```



### Debugging

`require('debug')` takes an argument that is an arbitrary namespace we define for debugging and returns debugger function.

```javascript
$ npm i debug

// 'index.js'
const startupDebugger = require('debug')('app:startup');
const dbDebugger = require('debug')('app:db');

if(app.get('env') === 'development') {
    app.use(morgan('tiny'));
    startupDebugger('Morgan enabled...'); // use debug module instead of console.log
}

// some db work...
dbDebugger('Connexted to the database...');

// terminal
$ export DEBUG=app:startup,app:db // can use wildcard(*) here
$ nodemon index.js

app:startup Morgan enabled...
app:db Connected to the database...

// shortcut to set an env variable debug and run the app at the same time
$ DEBUG=app:db nodemon index.js
```



### Templating Engines

When return html markup to the client, we use a templating engines.

Pug(Jade), Mustache, EJS 

```javascript
$ npm i pug

// 'index.js'
app.set('view engine', 'pug'); // express internally load this module

// optional setting: override the path to the templates
app.set('views', './views'); // all the templates should be inside 'views' folder

// create 'views' folder and 'index.pug' inside the folder
// 'index.pug' : no need opening and closing elements
html
  head
    title= title // the name of a variable will set at runtime
  body
    h1= message

// 'index.js'
app.get('/', (req, res) => {
    res.render('index', { title: 'My Express App', message: 'Hello' });
})
// will be converted to html markup and rendered on the browser
```



### Structuring Express Applications

For every logical part of application for every api endpoint, separate files or modules. 

(usually put all of them in the same folder named 'routes')

```javascript
// inside 'routes' folder
// router files named 'home.js' 
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
    res.render('index', { title: 'My Express App', message: 'Hello' });
});

module.exports = router;

// router files names 'courses.js'
const express = require('express');
const router = express.Router();

const courses = [
    { id: 1, name: 'course1' },
    { id: 2, name: 'course2' },
    { id: 3, name: 'course3' }
];

router.get('/', (req, res) => {
    res.send(courses);
});

router.get('/:id', (req, res) => {
    const course = courses.find(c => c.id === parseInt(req.params.id));
    if (!course) return res.status(404).send();
    res.send(course);
});

module.exports = router;

// 'index.js'
const home = require('./routes/home');
const courses = require('./routes/courses');

app.use('/', home);
app.use('/api/courses', courses);
```



