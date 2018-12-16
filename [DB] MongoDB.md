# MongoDB + Mongoose



MongoDB is a database that stores data records (documents) for use by an application. Mongo is a non-relational, "NoSQL" database. This means Mongo stores all data associated within one record, instead of storing it across many preset tables as in a SQL database. Some benefits of this storage model are:

- Scalability: by default, non-relational databases are split (or "sharded") across many systems instead of only one. This makes it easier to improve performance at a lower cost.
- Flexibility: new datasets and properties can be added to a document without the need to make a new table for that data.
- Replication: copies of the database run in parallel so if one goes down, one of the copies becomes the new primary data source.

While there are many non-relational databases, Mongo's use of JSON as its document storage structure makes it a logical choice when learning backend JavaScript. Accessing documents and their properties is like accessing objects in JavaScript.

Mongoose.js is an npm module for Node.js that allows you to write objects for Mongo as you would in JavaScript. This can make is easier to construct documents for storage in Mongo.



## CRUD Operations using MongoDB



#### Install MongoDB

expressjs.com/en/guide/database-integration.html#mongodb

```javascript
$ brew install mongodb

// create a directory for MongoDB to store its data
$ mkdir -p /data/db
// when you get error 'Permission denied'
$ sudo mkdir -p /data/db
Password:

// configure with the right permission
$ sudo chown -R `id -un` /data/db

// run Mongo Damon (a service that runs in the background and listens for connections on a given port)
$ mongod
NETWORK  [initandlisten] waiting for connections on port 27017
```



#### Connect to MongoDB

```javascript
// 'index.js'
const mongoose = require('mongoose');

mongoose.connect('mongodb://USERNAME:PASSWORD@localhost:27017/DB_NAME') // returns promise
  .then(() => console.log('Connected to MongoDB...'))
  .catch(err => console.error('Could not connect to MongoDB...', err));

// terminal
$ nodemon index.js
Connected to MongoDB... // only when mongo damon is running
```

Connection string should come from a configuration file in the real application.



#### Schema

In relational databases, you have tables and rows.

In MongoDB, we have collections and documents. Each documents is a container of key-value pairs.

We use a schema in Mongoose to define the shape of documents in a MongoDB collection. So we use that to define what are the properties we have in this document.

```javascript
const courseSchema = new mongoose.Schema({
  name: String,
  author: String,
  tags: [ String ],
  date: { type: Date, default: Date.now },
  isPublished: Boolean
});
```

SCHEMA TYPES : String, Number, Date, Buffer, Boolean, ObjectID, Array



#### Compile into Models

To create a class, need to compile the schema into a model.

`mongoose.model()` The first argument is the singular name of the collection that this model is for. The second argument is the schema that defines the shape of documents in this collection. 

```javascript
const Course = mongoose.model('Course', courseSchema);
```

Once we have a schema, we need to compile that into a model, which gives us a class, next we can create an object based on that classand this object maps to a document in Mongo DB database. 



#### Save a document

When we save the object in MongoDB, MongoDB is going to assign a unique identifier to this object, to this document. `save()` returns the actual course object that's saved in the database.

```javascript
// Classes, objects(instance)
// Course, nodeCourse

async function createCourse() {
  const course = new Course({
    name: 'node Course',
    author: 'Mosh',
    tags: ['node', 'backend'],
    isPublished: true
  });
  
  const result = await course.save(); 
  console.log(result);
};

createCourse();
```



#### Query document

`find()` returns DocumentQuery object which is similar to Promise.

```javascript
async function getCourse() {
  const courses = await Course 
  	.find({ author: 'Mosh', isPublished: true }) // can pass filtering strings
    .limit(10) 
    .sort({ name: 1 }) // 1: ascending, -1: descending order
    .select({ name: 1, tags: 1 }); // choose the only property you want to see
  console.log(courses);
}

getCourse();
```



***Comparison Query Operators**

eq (equal), ne (not equal)

gt (greater than), lt (less than)

gte (greater than or equal to), lte (less than or equal to)

in, nin (not in)

```javascript
// { $operator: value }

const courses = await Course 
	// .find({ price: { $gte: 10, $lt: 20 } })
	.find({ price: { $in: [10, 15, 20] } });
	// .or([ {price: 10}, {price: 15}, {price: 20} ]) 와 동일
```



***Logical Query Operators**

or(), and()

```javascript
// operatorFunction(filter object)

const courses = await Course
    .find()
    .or([ { author: 'Mosh '}, { isPublished: true } ]);
```



***Regular Expression**

```javascript
const courses = await Course
	// starts with Mosh
    .find({ author: /^Mosh/ })

	// ends with Hamedani
	.find({ author: /Hamedani$/i }); // i: case-insensitive

	// contains Mosh
	.find({ author: /.*Mosh.*/ }); // 0 or more characters
```



***counting**

`count()` : counting the documents in database



***pagination**

```javascript
async function getCourse() {
  const pageNumber = 2;
  const pageSize = 10;
  
  const courses = await Course
    .find({ author: 'Mosh', isPublished: true })
    .skip((pageNumber - 1) * pageSize) // /api/courses?pageNumber=2&pageSize=10
    .limit(pageSize)
    .sort({ name: -1 }) // .sort('-name') 과 동일
    .select({ name: 1, tags: 1 }); // .select('name tags') 와 동일
  console.log(courses);
}
```



#### Update the documents

update operators : docs.mongodb.com/manual/reference/operator/update/

```javascript
// Approach 1: Query first
// findById()
// Modifiy its properties
// save()

async function updateCourse(id) {   
	const course = await Course.findById(id);
    if (!course) return;
    
    course.isPublished = true;
    course.author = 'Another Author';   
    
    const result = await course.save();
    console.log(result);
}

// Approach 2: Update first
// Update directly
// Optionally: get the updated document

async function updateCourse(id) {
  const result = await Course.update({ _id: id }, {
    $set: {
      author: 'Mosh',
      isPublished: false
    }
  });
  
  console.log(result);
}

updateCourse('5c0d2681de4ab0b479331e16');

```

```javascript
async function updateCourse(id) {
  const course = await Course.findByIdAndUpdate(id, { // 1st args: user로부터 입력받은 id값
    $set: { // 2nd args: update할 내용 (using update operators)
      author: 'Jason',
      isPublished: true
    }
  }, { new: true }); // 3rd args: option을 부여해야 업데이트된 model 객체 리턴
  
  console.log(result);
}
```



#### Remove the documents

```javascript
async function removeCourse(id) {
  const result = await Course.deleteOne({ _id: id });
  console.log(result);
  
}

removeCourse('5c0d2681de4ab0b479331e16');

```





## Mongoose - Data Validation



can add validation options to schema as its properties.



#### Built-in Validators

* `required` validation
* validation for String : `minlength`, `maxlength`, `match`, `enum`
* validation for Number : `min`, `max`

```javascript
const courseSchema = new mongoose.Schema({
  name: { 
      type: String, 
      required: true,
      minlength: 5,
      maxlength: 255,
      // match: /pattern/
  }, 
  category: {
    type: String,
    required: true,
    enum: ['web', 'mobile', 'network']
  },
  author: String,
  tags: {
    type: Array,
    validate: {
      validator: function(value) {
        return value && value.length > 0;
      },
      message: 'A course should have at least one tag.'
    }
  },
  date: { type: Date, default: Date.now },
  isPublished: Boolean,
  price: {
    type: Number,
    required: function() { return this.isPublished; }, // conditional required option
    min: 10,
    max: 200
  }
});

const Course = mongoose.model('Course', courseSchema);

async function createCourse() {
  const course = new Course({
    // name: 'node Course',
    author: 'Mosh',
    tags: [], // Course validation failed: tags: A course should have at least one tag.
    isPublished: true,
    price: {
        type: Number,
        required: function() { return this.isPublished; } 
        // arrow function 사용하면 this가 자기 객체를 가리키지 않으므로 사용할 수 없다!
  	}
  });
  
  // validation
  try {
    // await course.validate();
    const result = await course.save(); 
    console.log(result);
  }
  catch (ex) {
    console.log(ex.message);
  }
};
```

#### Custom Validator

`{ validate: { validator: custom }, { message: error msg } }`

```javascript
const courseSchema = new mongoose.Schema({
  tags: {
    type: Array,
    validate: {
      validator: function(value) {
        // return value.length > 0; // 1)
        return value && value.length > 0; // 2)
      },
      message: 'A course should have at least one tag.'
    }
  }
});

const Course = mongoose.model('Course', courseSchema);

async function createCourse() {
  const course = new Course({
    // tags: null 
      // 1) Course validation failed: tags: Cannot read property 'length' of null
      // 2) Course validation failed: tags: A course should have at least one tag.
    tags: [], // Course validation failed: tags: A course should have at least one tag.
  });
    
  try {
    await course.validate();
  }
  catch (ex) {
    console.log(ex.message);
  }
};
```



#### Async Validators

```javascript
const courseSchema = new mongoose.Schema({
  tags: {
    type: Array,
    validate: {
      isAsync: true,
      validator: function(value, callback) {
        setTimeout(() => {
          // Do some async work
          const result = value && value.length > 0;
          callback(result);
        }, 3000);
      },
      message: 'A course should have at least one tag.'
    }
  }
});
```



#### Validation Errors

```javascript
async function createCourse() {
  const course = new Course({
    name: 'node Course',
    category: '-', // error
    author: 'Mosh',
    tags: null, // error
    isPublished: true,
    price: 15
  });
  
  try {
    await course.validate();
  }
  catch (ex) {
    // 현재의 ex object
      // const ex = {
      //    errors: {
      //        category: { message },
      //        tags: { message }
      //    }
      // }
    for (field in ex.errors) {
      console.log(ex.errors[field].message);
    }
  }
};
```



#### SchemaType Options

String options: `lowercase` , `uppercase` , `trim`

Custom options: `get` (called when the data is retrieved), `set` (called when the data is stored)

```javascript
const courseSchema = new mongoose.Schema({
    category: {
        type: String,
        required: true,
        enum: ['web', 'mobile', 'network'],
        lowercase: true
  	},
    price: {
        type: Number,
        required: function() { return this.isPublished; },
        min: 10,
        max: 200,
        get: (v) => Math.round(v),
        set: (v) => Math.round(v)
  	}
});

const Course = mongoose.model('Course', courseSchema);

async function createCourse() {
  const course = new Course({
    name: 'Express Course',
    category: 'Web', // will be lowercased 
    author: 'Mosh',
    tags: ['express', 'backend'],
    isPublished: true,
    price: 15.8 // will be rounded up to 16 and stored
  });
  // try ~ catch 생략
};

async function getCourse() {
  const courses = await Course
    .find({ _id: '5c0dc95d816a8f033a5a34e7' }) // price: 15.8
    .sort({ name: 1 })
    .select( {name: 1, tags: 1, price: 1 });
  console.log(courses[0].price); // 16
}  
```



[참고] lImport data into a Mongo database 

```javascript
// import data into a Mongo database
mongoimport --db DATABASE_NAME --coollection COLLECTION_NAME --file FILE_PATH --jsonArray

$ mongoimport --db mongo-exercises --collection courses --drop --file exercise-data.json --jsonArray
```





## Mongoose - Modeling Relationships



#### Modelling Relationships

Approach 1) Using References (Normalization) -> CONSISTENCY

```javascript
let author = {
    name: 'Mosh'
}

let course = {
    author: 'ref_id' 
}
```



Approach 2) Using Embedded Documents (Denormalization) -> PERFORMANCE

```javascript
let course = {
    author: {
        name: 'Mosh'
    }
}
```



=> Trade off between query performance vs. consistency



Approach 3) Hybrid

```javascript
let author = {
    name: 'Mosh' 
    // 50 other properties
}

let course = {
    author: {
        id: 'ref',
        name: 'Mosh'
    }
}
```



#### Referencing Documents

```javascript
// 'population.js'

const Author = mongoose.model('Author', new mongoose.Schema({
  name: String,
  bio: String,
  website: String
}));

const Course = mongoose.model('Course', new mongoose.Schema({
  name: String,
  author: {
    type: mongoose.Schema.Types.ObjectId, // referencing author
    ref: 'Author' // target collection name
  }
}));

async function createAuthor(name, bio, website) { 
  const author = new Author({
    name, 
    bio, 
    website 
  });

  const result = await author.save();
  console.log(result);
}

async function createCourse(name, author) {
  const course = new Course({
    name, 
    author
  }); 
  
  const result = await course.save();
  console.log(result);
}

createAuthor('Mosh', 'My bio', 'My Website');

createCourse('Node Course', '5c0e487afac4f17867652de6');
```



#### Population

```javascript
// 'population.js'

async function listCourses() { 
  const courses = await Course
    .find()
    // .populate('author') // get a complete representation of an author document
    .populate('author', 'name -_id') 
  	// 1st args: reference document, 2nd args: the properties to include or exclude(-)
    .populate('category', 'name')
    .select('name author');
  console.log(courses);
}

listCourses();
```



#### Embedding Documents

```javascript
// 'embedding.js'

const authorSchema = new mongoose.Schema({
  name: String,
  bio: String,
  website: String
});

const Author = mongoose.model('Author', authorSchema);

const Course = mongoose.model('Course', new mongoose.Schema({
  name: String,
  author: {
    type: authorSchema, // embedding the document
    required: true
  }
}));

async function createCourse(name, author) {
  const course = new Course({
    name, 
    author
  }); 
  
  const result = await course.save();
  console.log(result);
}

async function updateAuthor(courseId) {
  const course = await Course.update({ _id: courseId }, {
    $set: { // <-> $unset: remove subdocument
      'author.name': 'John Smith'
    }
  });
  // const course = await Course.findById(courseId);
  // course.author.name = 'Mosh Hamedani';
  // course.save();
  console.log(course);
}

createCourse('Node Course', new Author({ name: 'Mosh' }));

updateAuthor('5c0e5d1004e77b3c4c6503de');

```



#### Using an Array of Sub-documents

```javascript
// 'embedding.js'

const authorSchema = new mongoose.Schema({
  name: String,
  bio: String,
  website: String
});

const Author = mongoose.model('Author', authorSchema);

const Course = mongoose.model('Course', new mongoose.Schema({
  name: String,
  authors: [ authorSchema ] // type을 array로 지정, 내부 element type은 schema instance로 지정
}));

async function createCourse(name, authors) { // plural로 변경
  const course = new Course({
    name, 
    authors // plural로 변경
  }); 
  
  const result = await course.save();
  console.log(result);
}

createCourse('MongoDB Course', [
  new Author({ name: 'Mosh' }),
  new Author({ name: 'John' })
]);

// subdocument인 array에 data 추가 (storyId로 find한 다음 post 추가)*****
async function addAuthor(courseId, author) {
  const course = await Course.findById(courseId);
  course.authors.push(author);
  course.save();
} 

addAuthor('5c0e6215e299ea6a75813e7d', new Author({ name: 'Amy' }));

// subdocument인 array에 data 삭제
async function removeAuthor(courseId, authorId) {
  const course = await Course.findById(courseId);
  const author = course.authors.id(authorId); // id(): lok up the child object by its id
  author.remove(); // delete data
  course.save();
}

removeAuthor('5c0e6215e299ea6a75813e7d', '5c0e623961a9a46b92a36641');
```



#### Transactions

Two Phase Commits

```javascript
$ npm i fawn (@2.1.5)

const Fawn = require('fawn'); // two phase commit

Fawn.init(mongoose);
```



#### ObjectId

ObjectId is a unique identifier which is generated by MongoDB driver.

 _id: 5c0e6215e299ea6a75813e7d
 12 bytes
​	 4 bytes: timestamp
​	 3 bytes: machine identifier
​	 2 bytes: process identifier
​	 3 bytes: counter

```javascript
const mongoose = require('mongoose');

const id = new mongoose.Types.ObjectId();
console.log(id);
console.log(id.getTimestamp());

const isValid = mongoose.Types.ObjectId.isValid('1234'); 
console.log(isValid); // false
```



* validate objectId

```javascript
$ npm i joi-objectid (@2.0.0)

// import these in 'index.js' so that other modules can use it too
const Joi = require('joi');
Joi.objectId = require('joi-objectid')(Joi); // returns function

// 'rentals.js'
function validateRental(rental) {
  const schema = {
    customerId: Joi.objectId().required(),
    movieId: Joi.objectId().required()
  };

  return Joi.validate(rental, schema);
}
```



#### [참고] Test data를 json 파일로 만들어 mongoDB에 import하는 방법

```
$ mongoimport --db DB_NAME -u USER_ID -p PASSWORD --collection COLLECTION_NAME --file FILE_PATH --jsonArray
```



#### [참고] MongoDB 설치 및 쉘 접속 방법

```
$ brew install mongodb(@3.6.2)
$ sudo mkdir -p /data/db
// enter mac user password prompt --> root directory에 폴더 생성 (MongoDB 데이터 저장)
$ sudo chown -R `id -un` /data/db --> data directory configured

$ brew services start mongodb || mongod
$ mongo --port 27017 -u USERNAME -p PASSWORD

$ brew services stop mongodb
```



#### [참고] MongoDB Authentication

```
// 1) start MongoDB and connect to the mongo shell
$ mongod -- port 27017 --dbpath /data/db
$ mongo --port 27017

// 2) create the user admin
$ use admin
$ db.createUser(
  {
    user: "USERNAME",
    pwd: "PASSWORD",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
)

// 3) dissconnect the mongo shell and restart
$ mongod --auth --port 27017 --dbpath /data/db --> authenticate한 사용자만 접속 가능

// 4) authenticate as the admin
$ mongo --port 27017 -u "USERNAME" -p "PASSWORD" --authenticationDatabase "admin"

// 5) create additional users
$ use DATABASE_NAME // create a new database if it doesn't exist
$ db.createUser(
  {
    user: "USERNAME",
    pwd: "PASSWORD",
    roles: [ { role: "readWrite", db: "DATABASE_NAME" },
             { role: "read", db: "DATABASE_NAME" } ]
  }
)

// 6) authenticate as a user
$ mongo --port 27017 -u "USERNAME" -p "PASSWORD" --authenticationDatabase "DATABASE_NAME"
```

