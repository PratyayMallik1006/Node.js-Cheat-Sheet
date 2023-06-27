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
