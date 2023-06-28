# Node.js Cheat Sheet
- Runtime environment to execute JS Code, Not a framework
- JS Engine: JS Code to Machine Code
- Every browser has their own JS Engine - Chrome uses V8, Firefox uses SpiderMonkey
- Node uses V8

# Setting Up Environment
1. Install Node LTS(Latest Stable Version): https://nodejs.org/en
2. Check Version
```
node --version
```

## How Node Works
**Non-Blocking Asynchronous:**
- Single Thread to handle multiple requests
- Uses "Event Queue"
- Scalable, ideal for data intensive apps
- Ideal for I/O intensive apps
- Not ideal for CPU intensive apps - vide encoding, image manipulation
**Blocking Synchronous:**
- ASP .NET, Rails
- Thread waits until database executes the query
- For every concurrent client new thread is required

# Node Module System
## Global Object
Global Scope
```js
console
console.log()
setTimeout()
setInterval()
```
## Modules
- Global Objects gets overwritten when used in different files with different implementation
- Modules - small blocks, don't overwrite each other
- Every file in Nodejs is called a module

## Creating a Module
logger.js
```js
var url ="http://abc.com/log"

function log(message){
	console.log(message);
}

module.exports = log;
//module.exports.endPoint = url;
```
## Loading a Module
app.js
```js
const log = require('./logger')
log.log('message')
```
## Module Wrapper Function
```js
(function(exports, require, module, __filename, __dirname) {
...
})
```
## Nodejs Built-in Modules
Know more: https://nodejs.org/dist/latest-v18.x/docs/api/
### Path Module
```js
const path = require('path')
var pathObj = path.parse(__filename)
```
### OS Module
```js
const os = require('os')

//memory
var totalMemory = os.totalmem()
var freeMemory = os.freemem()
```
### File System Module
```js
const fs = require('fs')

//Synchronous
const files = fs.readdirSync('./');

//Asynchronous
fs.readdir('./',function(err,files){
	if(err) console.log(err)
	else console.log(files)
});
```
>Always prefer Asynchronous methods

### Events Module
It is a class
```js
const EventEmitter = require('events');
// EventEmitter -> class

const emitter = new EventEmitter
// emitter -> object

//Register a listener
emitter.on('messageLogged',function(){
	console.log('Listener')
});
//Listener must be defined before emitter

//Emitter - Raising an event
emitter.emit('messageLogged')
```
***Event Argument:***
Pass data about event
```js
emitter.on('messageLogged',function(arg){
	console.log('Listener', arg)
});

emitter.emit('messageLogged',{id:1, url:'http://'});
```
***Extending EventEmitter:***
logger.js
```js
const EventEmitter = require('events');

//contructor
class Logger extends EventEmitter {
	log(message){
		console.log(message);
		this.emitter.emit('messageLogged')
	}
}

module.exports = Logger;
```
app.js
```js
const EventEmitter = require('events');
const Logger = require('./logger');
const logger = new Logger();

logger.on('messageLogged',function(){
	console.log('Listener')
});

logger.log('message');
```
### HTTP Module
```js
const http = require('http');
//const server = http.createServer();
const server = http.createServer((req, res)=>{
	if(req.url === '/'){
		res.write("Hello World");
		res.end();
	}
	if(req.url === '/api/courses'){
		res.write(JSON.stringify([1,2,3]));
		res.end();
	}
});


server.on('connection',(socket)=>{
	console.log("New Connection")
});

server.listen(3000);
console.log("Listening on port 3000");
```
# Node Package Manager
Package Library + Command Line Interface
## Package.json
- Metadata about the application
- Create package json file
```
npm init
```
## Installing Node Package
```
npm i <packageName>
```
## Package Dependencies
All installed packages and their dependencies(packages they are depended on) are stores in node_modules folders
```
ls node_modules/
```
## NPM Packages and Source Control
- node_modules folder should be excluded from the source control
- .gitignore
```
node_modules/
```
- Installing all Packages from package json file
```
npm i
```
## Semantic Versioning
- Node packages follow semantic versioning: **major.minor.patch**
- "express": "^4.18.2"
- "^" defines to install latest version in same major versions (4.x.x)
- "express": "~4.18.2"
- "`" defines to install latest version in same major & minor versions (4.18.x)
## Listing the Installed Packages
```
npm list
npm list --dept=0
```
## Viewing Registry for a Package
```
npm view express
```
## Installing a Specific Version of a Package
```
npm i express@4.17.1
```
## Updating Local Packages
- Viewing outdated packages
```
npm outdates
```
- Updating outdated packages
```
 npm update
``` 
## Installing a Package as a Development Dependency
```
npm i <packagerName> --save-dev
```
## Uninstalling Node Package
```
npm un <packageName>
```
## Installing Node Package Globally
```
npm i -g <packageName>
```

# RESTful API
- Structure for HTTP Request
- API are middleware between client and server
- Alternatives: SOAP, GraphQl, Falcor
- REST is architecture style (conventions) for API
- Defines
  - HTTP Request Verbs
  - Specific pattern of Routes/Endpoint URLs
- **HTTP Request Verbs:** GET, POST, PUT, PATCH, DELETE
![restful methods](https://codeopinion.com/wp-content/uploads/2021/03/c.png)

- **Routes/Endpoint URLS:**

| **HTTP verbs** | **/articles**               | **/articles/Hamilton**             |
|----------------|-----------------------------|------------------------------------|
| GET            | Fetch **all** the articles  | Fetch **the** article on Hamilton  |
| POST           | Create **one** new article  |                  -                 |
| PUT            |              -              | Update **the** article on Hamilton |
| PATCH          |              -              | Update **the** article on Hamilton |
| DELETE         | Delete **all** the articles | Delete **the** article on Hamilton |


## RESTful Services
- REST -> **Re** presentational **S** tate **T** ransfer
- REST defines a set of conventions for creating HTTP services:
	I. POST: To create a resource
	II. PUT: To update it
	III. GET: To read it
	IV. DELETE: To delete it
	
## Express JS Framework
- Nodejs Framework
- fast and lightweight Framework for building web application
```
npm init
npm install express
```

## Building a Web Server Using Express
```js
const express = require('express');
const app =  express();

app.get()
app.post()
app.put()
app.delete()

app.listen(3000,()=> console.log("PORT 3000"))
```
## Nodemon
- Nodemon -> Node Monitor
- Restarts server whenever code changes
```
npm i -g nodemon
nodemon index.js
```
## Environment Variables
- To save configurations of the environment
- To set Port or API Key etc.
index.js
```js
const port = process.env.PORT || 3000
app.listen(port,()=> console.log(`PORT ${port}`))
```
- Setting an environment variable
```
set PORT=8000
```
## Route Parameters
app.js
```js
app.get('/api/courses/:id', (req, res) => {
        res.send(req.params.id);
});
```
- To get route params --> req.params
```js
app.get('/api/location/:lat/:lon', (req, res) => {
        res.send(req.params.lat);
        res.send(req.params.lon);
});
```
- To get query params --> req.query
```js
app.get('/api/location/?tab=cities', (req, res) => {
        res.send(req.query);
});
```
## Handling HTTP Get Requests
index.js
```js
const courses =[
	{id:1,name:"abc"},
	{id:2,name:"def"}
];

//To all the courses
app.get('/api/courses', (req, res) => {
        res.send(courses);
});

//To get a specific course by its id
app.get('/api/courses/:id', (req, res) => {
        const course = courses.find(c => c.id === parseInt(req.params.id));
        if (!course) res.status(404).send(`course ${req.params.id} not found`);
        res.send(course);
});
```



## Handling HTTP Post Requests
- Creating an object
index.js
```js
app.use(express.json()); //Middleware to handle json body 

app.post('/api/courses', (req, res) => {
        const course = {
            id: courses.length + 1,
            name: req.body.name //uses express.json()
        };
        courses.push(course);
        res.send(course);
});
```

## Input Validation
- npm package JOI
```
npm i JOI
```
index.js
```js
const Joi = require('joi');

app.use(express.json()); //Middleware to handle json body 

app.post('/api/courses', (req, res) => {
		const schema = {
        name: Joi.string().min(3).required()
		};
		const result = Joi.validate(req.body, schema);

        // 400 bad request
        if (result.error) {
	        return res.status(400).send(result.error.details[0].message);
		}      

        const course = {
            id: courses.length + 1,
            name: req.body.name //uses express.json()
        };
        courses.push(course);
        res.send(course);
});
```

## Handling HTTP Put Requests
- Updating an object
index.js
```js
 app.put('/api/courses/:id', (req, res) => {
	const course = courses.find(c => c.id === parseInt(req.params.id));
	
	//If not existing, return 404
	if (!course) {
		return res.status(404).send(`course ${req.params.id} not found`);
	}

	// validate and if invalid return 400 bad req
	//const result = validateCourse(req.body)
	const { error } = validateCourse(req.body) //destructuring
	
// 400 bad request
	if (error) {
		return res.status(400).send(result.error.details[0].message);
	}
// else update and return updated course
	course.name = req.body.name;
	res.send(course);	  
});

function validateCourse(course) {
const schema = {
	name: Joi.string().min(3).required()
};
return Joi.validate(course, schema);
}
``` 

## Handling HTTP Delete Requests
index.js
```js
app.delete('/api/courses/:id', (req, res) => {
        // look up the course 
        // if not exist, return 404
	const course = courses.find(c => c.id === parseInt(req.params.id));
	if (!course) return {
		res.status(404).send(`course${req.params.id} not found`);
	}

        // delete and return course which was deleted
	const index = courses.indexOf(course);
	courses.splice(index, 1);
	res.send(course);
});
```

# Express Advanced Topics
## MiddleWare
- A MiddleWare function is a function that takes a request object and either terminates the request/response cycle or passes control to another middleware function
- In express, every router handle function is middleware function
**Built-in express middleware functions:**
1.  ### express.json() 
- Returns a middleware function, reads request, if there is json object in body, parse the body into a json object
- sets **req.body** property

**Request Processing Pipeline**
 - Request > Middleware> ... > Middleware > response
 - Request > json() > route() > response

## Creating a Custom MiddleWare
```js
app.use((req, res, next) => {
	console.log('Logging...');
	next(); 
//pass to next middleware function if we dont do this req will end up hanging
});
```
Custom middleware applied to a route
```js
app.use('/api/admin',(req, res, next) => {
	console.log('Logging...');
	next(); 
//pass to next middleware function if we dont do this req will end up hanging
});
```
## Built-in Express Middleware Functions
1.  ### express.json() 

- Returns a middleware function, reads request, if there is json object in body, parse the body into a json object
- sets **req.body** property
2. ***express.urlencoded({ extended: true })***
- payload for form url encoded 
- key=value&key=value
3. ***app.use(express.static('public'))***
- we can serve static content using this file

## Third party middleware
- Know more: https://expressjs.com/en/resources/middleware.html

### Helmet 
- Use for secure http by setting header
```
npm i helmet
```
- index.js
```js
const helmet = require('helmet');
app.use(helmet())
```

### Morgan
- logging HTTP request
```
npm i morgan
```
- Index.js
```js
const morgan = require('morgan');
app.use(morgan('tiny'));
```
- We can configure it to log to a file

## Environments
- To get environment name (if not set undefined)
```js
console.log(process.env.NODE_env)

app.get('env') // if env not set then return development by default
if (app.get('env') === 'development') {
    app.use(morgan('tiny'));
    console.log('Morgan enabled...');
}
```
- CMD
```
set NODE_ENV=production
```

## Configuration
- RC package
```
npm i rc
```
- Another package: npm config 
- CMD
```
npm i config
```
- Create config folder with json files
- Create json contains different config files
- default.json
```json
    {
	    "name":"Node Project",
	    "mail":{
		    "host":"toto@mercedes.com",
	    }
    }
```
- index.js
```js
const config = require('config')

    console.log(`Application Name: ${config.get('name')}`);
    console.log(`Mail Server : ${config.get('mail.host')}`);
    console.log(`Mail Password : ${config.get('mail.password')}`);
```

## Debugging

- Node debug package
- Use env variable to enable or disable debug
- Avoid using console.log
- CMD
```
npm i debug
```
- index.js
```js
const startupDebugger = require('debug')('app:startup');
const dbDebugger = require('debug')('app:db');

dbDebugger("Database connected");
```
- CMD
```
set DEBUG=app:startup // single namespace
set DEBUG=app:startup,app:db // multiple namespace
set DEBUG=app:* // all debugging namespace

DEBUG=app:startup nodemon index.js // at runtime
```
## Templating Engine
- Sending HTML markup to client
- Various available: Pug, Mustache, **EJS**

### Pug
- CMD
```
npm i pug
```
- index.js
```js
app.set('view engine', 'pug'); //no need to require
app.set('views', './views'); 
// all templates insider folder view(optional setting)

//create index.pug
app.get('/', (req, res) => {
    res.render('index.pug', { title: 'My Express App', message: 'Hello World'});
});
```
- index.pug
```json
html
	body
		h1 = title
		p = message
```
## Structuring Express Applications
1. routes/courses.js
```js
const express = require('express');
const router = epress.Router();

const courses =[... ]

router.get('/',(req,res) => {
	...
});

router.get('/id',(req,res) => {
	...
});

router.post('/',(req,res) => {
	...
});

router.put('/:id',(req,res) => {
	...
});

router.delete('/:id',(req,res) => {
	...
});

module.export = router;
```
2. index.js
```js
const express = require('express')
const joi = require('Joi')
const courses = require('./routes/courses')
const instructors = require('./routes/instructors')

const app = express();
app.use(express.json());
app.use('/api/courses', courses);
app.use('/api/instructors', instructors);

app.listen(3000, () => console.log("3000"))
```
# Asynchronous JavaScript
## Synchronous vs Asynchronous Code
- **Synchronous:** Code executes one line (block) at a time
-  **Asynchronous:** If a block takes time, blocks ahead of it gets executed without waiting for it
## Patterns for Dealing With Asynchronous Code
    1) Callbacks
    2) Promises
    3) Async/Await

## Callbacks
```js
console.log('Before');
getUser(1, (user) => {
        console.log('User:', user);
        //waits until it gets user
});
console.log('After');


function getUser(id, callback) {
	setTimeout(() => {
		console.log('Reading user from db...');
		callback({ id: id, gitHubUsername: 'Lewis' });
	}, 2000); // 2 seconds wait
}
```
## Callback Hell
- Callbacks inside callbacks (also  called Christmas tree problem)
- Named functions
- Replace anonymous function with named function
```js
getUser(1, displayUser);

function displayUser(user) {
    console.log('User:', user);
    getRepositories(user, displayRepositories);
}

function displayRepositories(repos) {
    console.log(`Repo's: ${repos}`);
    getCommits(repos[1], displayCommits);
}

function displayCommits(commits) {
    console.log('Commits', commits);
}
```
## Promises

- It is an object which holds eventual result of async operation
- In 3 states: 
	- pending state, fulfilled or resolved, rejected state
```js
const p = new Promise((resolve, reject) => {
    setTimeout(() => {
		resolve(1); // pending => resolved, sets result = 1
        // reject(new Error('something went wrong')); // pending => rejected
    }, 2000);
});

//consuming the promise
p.then((result) => {
    console.log(`Result: ${result}`); // Result: 1
}).catch((error) => {
    console.log(`Error: ${error.message}`);
});
```
- Chained Promises
```js
getUser(1)
	.then((user) => getRepositories(user.gitHubUsername))
	.then((repos) => getCommits(repos[0]))
	.then((commits) => console.log('Commits', commits));
```


## Creating Settled Promises
- Already resolved or rejected promises
```js
const p = Promise.resolve({ id: 1});
    p.then(user => { console.log(user)});
```

## Running Parallel Promises

- Returns promises of both
```js
Promise.all([p1, p2])   
       .then((result) => {
            console.log(result);
       });
```
- Returns only one promise which happens early
```js
Promise.race([p1, p2])
    .then((result) => {
        console.log(result);
    });
```
## Async and Await
- Syntactical sugar over promises
- Write async code using sync code

# CRUD Using Mongoose and MongoDB
- MongoDB is an open-source document database. It stores data in flexible, JSONlike documents. 
- In relational databases we have **tables** and **rows**, in MongoDB we have **collections** and **documents.** A document can contain sub-documents. 
- We don’t have relationships between documents. 
## To connect to MongoDB: 
```js
const mongoose = require(‘mongoose’);

//Database name => playground
mongoose.connect(‘mongodb://localhost/playground')
	.then(() => console.log(‘Connected…’)) 
	.catch(err => console.error(‘Connection failed…’)); 
```
## To store objects in MongoDB
- We need to define a Mongoose **schema** first. 
- The schema defines the shape of documents in MongoDB.
```js
courseSchema = new mongoose.Schema({ 
	name: String, 
	price: Number
	});
```
- We can use a SchemaType object to provide additional details: 
```js
const courseSchema = new mongoose.Schema({ 
	isPublished: { type: Boolean, default: false } 
}); 
```
- Supported types are: **String, Number, Date, Buffer** (for storing binary data), **Boolean** and **ObjectID.** 

## Creating a Model 
- Once we have a schema, we need to compile it into a model. A model is like a class. It’s a blueprint for creating objects
- In collection(table) **Course**
```js
const Course = mongoose.model(‘Course’, courseSchema);
const course = new Course({
	name:"Node Js",
	prine:10
	
})
```
## CRUD Operations
### Saving a document 
```js
let course = new Course({ name: ‘…’ }); 
course = await course.save();
```
### Querying documents 
```js
const courses = await Course 
	.find({ author: ‘Lewis’, isPublished: true }) 
	.skip(10) 
	.limit(10) 
	.sort({ name: 1, price: -1 }) 
	.select({ name: 1, price: 1 });
```
### Updating a document (query first) 
```js
const course = await Course.findById(id); 
if (!course) return; 
course.set({ name: ‘…’ }); 
course.save(); 
```
### Updating a Document (query first) 
```js
const result = await Course.update({ _id: id }, { 
	$set: { name: ‘…’ } 
});
```
### Updating a Document (update first) and return it 
```js
const result = await Course.findByIdAndUpdate({ _id: id }, { 
	$set: { name: ‘…’ } 
}, { new: true }); 
```
### Removing a document 
```js
const result = await Course.deleteOne({ _id: id }); 
const result = await Course.deleteMany({ _id: id }); 
const course = await Course.findByIdAndRemove(id);
```
# Mongo - Data Validation
 - When defining a schema, you can set the type of a property to a SchemaType object. You use this object to define the validation requirements for the given property.
 -  Adding validation 
```js
const courseSchema = new mongoose.Schema({ 
	name: { type: String, required: true } 
})
```
 - Validation logic is executed by Mongoose prior to saving a document to the database. You can also trigger it manually by calling the **validate()** method. 
## Built-in validators: 
- **Strings:** minlength, maxlength, match, enum 
- **Numbers:** min, max 
- **Dates:** min, max 
- **All types:** required 
## Custom validation
```js
tags: { 
	type: Array, 
	validate: { 
		validator: function(v) { return v && v.length > 0; },
		message: ‘A course should have at least 1 tag.’ 
		} 
	}
```
## Async Validators
- If you need to talk to a database or a remote service to perform the validation, you need to create an async validator: 
```js
validate: { 
	isAsync: true 
	validator: function(v, callback) { 
		// Do the validation, when the result is ready, call the callback 
		callback(isValid); 
	} 
} 
```
## SchemaType Options
- Other useful SchemaType properties: 
	- **Strings:** lowercase, uppercase, trim 
	- **All types:** get, set (to define a custom getter/setter)
```js
price: { 
	type: Number, 
	get: v => Math.round(v), 
	set: v => Math.round(v)
}
```
# Mongoose - Modeling Relations between Connected Data
- To model relationships between connected data, we can either **reference** a document or **embed** it in another document. 
- When referencing a document, there is really no relationship between these two documents. So, it is possible to reference a non-existing document. 
- **Referencing documents (normalization)** is a good approach when you want to enforce data consistency. Because there will be a single instance of an object in the database. But this approach has a negative impact on the performance of your queries because in MongoDB we cannot JOIN documents as we do in relational databases. So, to get a complete representation of a document with its related documents, we need to send multiple queries to the database. 
- **Embedding documents (denormalization)** solves this issue. We can read a complete representation of a document with a single query. All the necessary data is embedded in one document and its children. But this also means we’ll have multiple copies of data in different places. While storage is not an issue these days, having multiple copies means changes made to the original document may not propagate to all copies. If the database server dies during an update, some documents will be inconsistent. For every business, for every problem, you need to ask this question: “can we tolerate data being inconsistent for a short period of time?” If not, you’ll have to use references. But again, this means that your queries will be slower. 
## Referencing a Document
```js
const courseSchema = new mongoose.Schema({ 
	author: { 
		type: mongoose.Schema.Types.ObjectId, 
		ref: ‘Author’ 
		} 
	})
```
## Embedding a Document
```js
const courseSchema = new mongoose.Schema({ 
	author: { 
		type: new mongoose.Schema({ 
			name: String, 
			bio: String 
			}) 
		}
	})
```
 - Embedded documents don’t have a save method. They can only be saved in the context of their parent. 
 
## Updating an Embedded Document 
 
```js
 const course = await Course.findById(courseId); 
 course.author.name = ‘New Name’; 
 course.save(); 
```
  
## Implementing Using Fawn 
- We don’t have **transactions** in MongoDB. To implement transactions, we use a pattern called **“Two Phase Commit”.** If you don’t want to manually implement this pattern, use the **Fawn** NPM package:
```js
try { 
await new Fawn.Task() 
	.save(‘rentals’, newRental) 
	.update(‘movies’, { _id: movie._id }, { $inc: numberInStock: -1 }}) 
	.run(); 
} 
catch (ex) { 
	// At this point, all operations are automatically rolled back 
}
```
## ObjectID
 - ObjectIDs  are generated by **MongoDB driver** and are used to uniquely identify a document. They consist of 12 bytes: 
	 - 4 bytes:  **timestamp**
	 - 3 bytes: **machine identifier** 
	 - 2 bytes: **process identifier**
	 - 3 byes: **counter** 
- ObjectIDs are almost unique. In theory, there is a chance for two ObjectIDs to be equal but the odds are very low (1/16,000,000) for most real-world applications. 
## Validating ObjectIDs 
```js
mongoose.Types.ObjectID.isValid(id);
```
 - To validate ObjectIDs using **joi**, use **joi-objectid** NPM package

# Authentication and Authorization
- **Authentication** is the process of determining if the user is who he/she claims to be. It involves validating their email/password. 
- **Authorization** is the process of determining if the user has permission to perform a given operation. 
- To hash passwords, use **bcrypt**
- **joipasswordcomplexity** npm package for password complexity 
- **bcrypt** for hashing password
- A **salt** is random string added in password so different password will be outputed
## Hashing passwords 
```js
async function run() {
    const salt = await bcrypt.genSalt(10);
    const hashed = await bcrypt.hash('1234', salt);
    console.log(salt);
}
```
## Validating passwords 
```js
const isValid = await bcrypt.compare(‘1234’, hashed);
```
## JSON Web Token (JWT)
 - A **JSON Web Token (JWT)** is a JSON object encoded as a long string. We use them to identify users. It’s similar to a passport or driver’s license. It includes a few public properties about a user in its payload. These properties cannot be tampered because doing so requires re-generating the digital signature. 
 - When the user logs in, we generate a JWT on the server and return it to the client. We store this token on the client and send it to the server every time we need to call an API endpoint that is only accessible to authenticated users. 
 - To generate JSON Web Tokens in an Express app use jsonwebtoken package. 
 
## Generating a JWT 
```js
const jwt = require(‘jsonwebtoken’); 
const token = jwt.sign({ _id: user._id}, ‘privateKey’);
```
 - Never store **private keys** and other secrets in your codebase. Store them in environment variables. Use the **config** package to read application settings stored in environment variables. 
 
## Setting Response Header 
```js
res.header('x-auth-token', token).send(_.pick(user, ['_id', 'name', 'email']));


// 401 UNAUTHORIZED
// 403 FORBIDDEN
```
## Encapsulate logic in Mongoose Models
 - Adding a Method to a Mongoose Model :
```js
userSchema.methods.generateAuthToken = function() { 
	const token = jwt.sign({ _id: this._id}, ‘privateKey’);
} 
const token = user.generateAuthToken();
```
 - Implement authorization using a middleware function. Return a 401 error (unauthorized) if the client doesn’t send a valid token. Return 403 (forbidden) if the user provided a valid token but is not allowed to perform the given operation. 
 - You don’t need to implement logging out on the server. Implement it on the client by simply removing the JWT from the client. 
 - Do not store a JWT in plain text in a database. This is similar to storing users’ passports or drivers license in a room. Anyone who has access to that room can steal these passports. Store JWTs on the client. If you have a strong reason for storing them on the server, make sure to encrypt them before storing them in a database

