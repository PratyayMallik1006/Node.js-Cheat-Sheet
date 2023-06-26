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