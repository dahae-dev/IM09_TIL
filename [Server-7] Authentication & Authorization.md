# Authentication & Authorization



Authentication is the process of identifying if the user is who they claim they are. That's when they login. So we send our username and password to the server, and the server authenticates us.

Authorization is determining if the user has the right permission to perform the given operation.



## Authentication



#### Create the User Model

```javascript
// 'models/user.js'

const Joi = require('joi');
const mongoose = require('mongoose');

const User = mongoose.model('User', new mongoose.Schema({
  name: {
    type: String,
    required: true,
    minlength: 5,
    maxlength: 50
  },
  email: {
    type: String,
    required: true,
    minlength: 5,
    maxlength: 255,
    unique: true
  },
  password: {
    type: String,
    required: true,
    minlength: 5,
    maxlength: 1024 // hased password will be stored
  },
}));

function validateUser(user) {
  const schema = {
    name: Joi.string().min(5).max(50).required(),
    email: Joi.string().min(5).max(255).required().email(),
    password: Joi.string().min(5).max(255).required() // user's actual input password
      // can use joi-password-complexity library 
  };

  return Joi.validate(genre, schema);
}

exports.User = User; 
exports.validate = validateUser;
```



#### Register Users (Sign up)

```javascript
// 'routes/users.js'

const { User, validate } = require('../models/user');
const mongoose = require('mongoose');
const express = require('express');
const router = express.Router();

// register a new user
router.post('/', async (req, res) => {
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (user) return res.status(400).send('User already registered.');

  user = new User({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password 
  })

  await user.save();

  res.send(user);
});

module.exports = router;
```



#### Use Lodash - Modify the response to the client

```javascript
// Modify the response to the client

// option 1: return a custom object
  res.send({
    name: user.name,
    email: user.email
  });

// option 2: using library
$ npm i lodash

// 'routes/users.js'
const _ = require('lodash'); // add

router.post('/', async (req, res) => {
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (user) return res.status(400).send('User already registered.');

  user = new User(_.pick(req.body, ['name', 'email', 'password']));
  await user.save();

  res.send(_.pick(user, ['_id', 'name', 'email']));
});
```



#### Hash passwords

```javascript
// install library 
$ npm i bcrypt

// 'routes/users.js'
const bcrypt = require('bcrypt'); // add

// register a new user
router.post('/', async (req, res) => {
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (user) return res.status(400).send('User already registered.');

  user = new User(_.pick(req.body, ['name', 'email', 'password']));
  // hashing password
  const salt = await bcrypt.genSalt(10);
  user.password = await bcrypt.hash(user.password, salt);
  await user.save();

  res.send(_.pick(user, ['_id', 'name', 'email']));
});
```



#### Authentication (Log in)

```javascript
// 'routes/auth.js'

const Joi = require('joi');
const bcrypt = require('bcrypt');
const _ = require('lodash');
const { User } = require('../models/user');
const mongoose = require('mongoose');
const express = require('express');
const router = express.Router();

// authentication - login
router.post('/', async (req, res) => {
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (!user) return res.status(400).send('Invalid email or password.');

  const validPassword = await bcrypt.compare(req.body.password, user.password);
  if (!validPassword) return res.status(400).send('Invalid email or password.');

  res.send(true);
});

function validate(req) {
  const schema = {
    email: Joi.string().min(5).max(255).required().email(), // email(): check the format of email address
    password: Joi.string().min(5).max(255).required() // user's actual input password
  };

  return Joi.validate(req, schema);
}

module.exports = router;
```



#### JWT (JSON Web Tokens)

A JSON web token is basically a long string that identifies a user.

When the user logs in on the server, we generate this JSON Web token, which is basically like a license or password. We give it to the client, and then tell them 'hey, next time you want to come back here and call one of our API endpoints', we need to show your password, we need to show your drivers license. This is your id, so on the client we need to store this json web token which is a string, so we can send it back to the server for future API calls. ?????

```javascript
$ npm i jsonwebtoken

// 'routes/auth.js'

const config = require('config'); // add
const jwt = require('jsonwebtoken'); // add

router.post('/', async (req, res) => {
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (!user) return res.status(400).send('Invalid email.');

  const validPassword = await bcrypt.compare(req.body.password, user.password);
  if (!validPassword) return res.status(400).send('Invalid password.');

  // gerate authentication tokens
  const token = jwt.sign({ _id: user._id }, config.get('jwtPrivateKey')); 
    // 1st args: payload, 2nd args: secretOrPrivateKey
  res.send(token);
});
```

* store secrets in environment variables ***

```javascript
// install config library
$ npm i config

// 'index.js'

const config = require('config'); // add

if(!config.get('jwtPrivateKey')) {
  console.error('FATAL ERROR: jwtPrivateKey is not defined.');
  process.exit(1); // 0 = success
}

// create 'config' folder and 'custom-environment-variables' file in it
// terminal

$ export vidly_jwtPrivateKey=mySecureKey
```

* Set response headers

```javascript
// 'routes/users.js'

const config = require('config');
const jwt = require('jsonwebtoken');

router.post('/', async (req, res) => { // register a new user
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (user) return res.status(400).send('User already registered.');

  user = new User(_.pick(req.body, ['name', 'email', 'password']));
  // hashing password
  const salt = await bcrypt.genSalt(10);
  user.password = await bcrypt.hash(user.password, salt);
  await user.save();

  // set response headers
  const token = jwt.sign({ _id: user._id }, config.get('jwtPrivateKey'));
  res.header('x-auth-token', token).send(_.pick(user, ['_id', 'name', 'email']));
});
```



#### Encapsulate Logic in Mongoose Models

encapsulate generating token logic in a single place.

```javascript
// 'models/user.js'

const config = require('config'); // add
const jwt = require('jsonwebtoken'); // add

const userSchema = new mongoose.Schema({
  // 생략
});

userSchema.methods.generateAuthToken = function() {
  const token = jwt.sign({ _id: this._id }, config.get('jwtPrivateKey'));
  return token;
}

const User = mongoose.model('User', userSchema);


// 'routes/auth.js'

router.post('/', async (req, res) => { // authentication - login
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (!user) return res.status(400).send('Invalid email.');

  const validPassword = await bcrypt.compare(req.body.password, user.password);
  if (!validPassword) return res.status(400).send('Invalid password.');

  const token = user.generateAuthToken(); // get token
  res.send(token);
});

// 'routes/users.js'
router.post('/', async (req, res) => { // register a new user
  const { error } = validate(req.body); 
  if (error) return res.status(400).send(error.details[0].message);

  let user = await User.findOne({ email: req.body.email });
  if (user) return res.status(400).send('User already registered.');

  user = new User(_.pick(req.body, ['name', 'email', 'password']));
  // hashing password
  const salt = await bcrypt.genSalt(10);
  user.password = await bcrypt.hash(user.password, salt);
  await user.save();

  const token = user.generateAuthToken(); // get token
  res.header('x-auth-token', token).send(_.pick(user, ['_id', 'name', 'email']));
});
```



## Authorization



#### Authorization Middleware

```javascript
authentication validation logic --> middleware
  // const token = req.header('x-auth-token');
  // res.status(401).send('Invalid token.');


// 'middleware/auth.js'

const config = require('config');
const jwt = require('jsonwebtoken');

module.exports = function (req, res, next) {
  const token = req.header('x-auth-token');
  if (!token) return res.status(401).send('Access denied. No token provided.');

  try {
    const decodedPayload = jwt.verify(token, config.get('jwtPrivateKey'));
    req.user = decodedPayload;
    next();
  }
  catch (ex) {
    res.status(400).send('Invalid token.');
  }
}
```



#### Protect Routes

```javascript
// 'routes/genres.js'

const auth = require('../middleware/auth'); // add

router.post('/', auth, async (req, res) => {
    // 생략
}
```



#### Get the Current User

```javascript
// 'routes/users.js'

const auth = require('../middleware/auth');

// get the current user
router.get('/me', auth, async (req, res) => {
  const user = await User.findById(req.user._id).select('-password');
  res.send(user);
})
```



#### Log Out Users

should not store tokens in the database, or at least encrypt or hash them.

```javascript

```



#### Role Based Authorization

```javascript
// 'models/user.js'
const userSchema = new mongoose.Schema({
  // 생략
  isAdmin: Boolean // isAdmin 추가
});

userSchema.methods.generateAuthToken = function() {
  const token = jwt.sign({ _id: this._id, isAdmin: this.isAdmin }, // isAdmin 추가 config.get('jwtPrivateKey'));
  return token;
}

// 'middleware/admin.js'

module.exports = function (req, res, next) {
  // auth middleware function sets 'req.user'
  if (req.user.isAdmin) return res.status(403).send('Access denied.'); 
    // error: 401 Unauthorized, 403 Forbidden
  next();
}


// 'routes/genres.js'

const admin = require('../middleware/admin'); // add

router.delete('/:id', [auth, admin], async (req, res) => { // add middleware as array
  const genre = await Genre.findByIdAndRemove(req.params.id);
  if (!genre) return res.status(404).send('The genre with the given ID was not found.');

  res.send(genre);
});
```







**[참고] Explain why each system fails, and how to guard against that failure:**

\- Plaintext passwords

악의적인 의도를 가진 해커가 패스워드 데이터베이스에 불법적인 접근을 하는 경우, 즉시 모든 사용자의 패스워드가 있는 그대로 노출이 되기 때문에 보안에 매우 취약하다. 따라서 데이터베이스에 저장하기 전에 반드시 암호화를 시켜야 한다.

\- Encrypted passwords

대칭키를 이용한 암호화(symmetric encryption)는 reversible operation으로, 키 관리가 어려워 보안이 취약해질 가능성이 높다. 이는 encrypter와 decrypter가 동일한 키를 이용하기 때문에, 해커가 패스워드 데이터베이스에 불법적으로 접근한 경우, symmetric key만 알아내면 모든 패스워드를 복호화하여 접근할 수 있기 때문이다. 반면, 비대칭키를 이용한 암호화(asymmetric encryption)를 하는 경우에는, 보안성은 높아지나 복잡한 알고리즘 때문에 무거워지는 단점이 있다.

\- Hashed passwords

hashing은 one-way function이기 때문에, hash를 반대로 돌리는 것은 불가능하다. 하지만, 가능한 패스워드의 모든 경우의 수와 그 해시값을 구한 rainbow table를 가지고, 불법적으로 접근한 패스워드 데이터베이스에 저장된 해시값과 비교하여 일치하는 원본 패스워드를 알아낼 수 있다.

따라서 이를 예방하기 위해 hashed password를 salting 하면, 패스워드의 길이도 길어지고 hashing도 추가적으로 되기 때문에, rainbow table이 의미가 없어지므로 해킹을 하기에도 훨씬 복잡하고 시간이 더 오래 걸려 보안성이 높아진다. 