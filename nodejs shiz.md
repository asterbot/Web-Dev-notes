# Nodejs Stuff

## Intro

| Front-end(browser)                                   | Back-end (server)                    |
| ---------------------------------------------------- | ------------------------------------ |
| **JS**: Add interactivity to pages(click events etc) | **NODE JS**: Run JS on a server side |

## Computers & Code

Computers only understand machine code<br>
So assembly language is used to make it easier to code in
And ON TOP of that we have languages like C++

So C++ is compiled to machine code so the computer can understand it
JS is abstracted even further away from machine code than C++
![image-20231226162646720](C:\Users\Arjun\AppData\Roaming\Typora\typora-user-images\image-20231226162646720.png)

### How does JS work in browsers?

Running in browsers is an engine called V8 engine(written in C++)
It compiles JS to machine code in the browser so JS cannot run outside the browser without the engine

### Enter NodeJS

Another program in C++ which *wraps* V8 engine
Since NodeJS is written in C++ and compile to machine code

So in essence we can run JS directly on a computer using NodeJS

### Node is more than a wrapper to V8

It can:

- Read & Write files on a computer
- Connect to a database
- Act as  a server for content

Since JS is running outside browser, so we cant use DOM(Document Object Model)
BUT we dont need that since we're now running it on a server, ie the backend

NodeJS comes above this hierarchy 



## Role of Node.js

Run JS on the server side and handle requests coming from a browser
![image-20231226162711099](C:\Users\Arjun\AppData\Roaming\Typora\typora-user-images\image-20231226162711099.png)



### Why use Node.js?

- No extra language for server if yk JS
- Can share code bw front and back end
- Node.js has a massive community behind it
- Huge amount of third-party packages & tools to help



### Running files using node.js

If you have a `test.js` file:
```js
const name = "Mario";

console.log(name);
```

You can run it on terminal by:
```bash
$ node test
mario
```



<div class="page-break-after:always;"></div>

## Node.js Basics

### Basics

Arrow functions + you can put ` character to have f-strings:
```js
const greet = (n) =>{
    console.log(`Hello, ${n}`);
}

greet("Mario");
greet("Luigi");
```

This gives

```bash
Mario
Luigi
```



### The Global Object

Just like on the browser, if you see the `window` class, it has certain methods:
![image-20231226175436716](C:\Users\Arjun\AppData\Roaming\Typora\typora-user-images\image-20231226175436716.png)

Similarly nodejs has a `global` object:
```js
// The global object 
console.log(global);
```

which gives
```bash
$ node global
<ref *1> Object [global] {
  global: [Circular *1],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Getter]
  },
  queueMicrotask: [Function: queueMicrotask],
  structuredClone: [Getter/Setter],
  atob: [Getter/Setter],
  btoa: [Getter/Setter],
  performance: [Getter/Setter],
  fetch: [Function: fetch],
  crypto: [Getter]
}
```

Can use `global.setTimeOut()`:
```js
global.setTimeout(()=>{
    console.log('in the timeout')   
},3000)
```

this prints after doing a time-out of 3 seconds

Can omit `global` and it is implied:
```js
setTimeout(()=>{
    console.log('in the timeout')   
},3000)
```


Writing using interval:
```js
const interval = setInterval(()=>{
    console.log('in the interval');
},1000)

setTimeout(()=>{
    console.log('in the timeout');
    clearInterval(interval);
},3000)
```

This will print `in the interval` after 1 second each and print `in the timeout` at the third second, where the program terminates
```bash
$ node global
in the interval
in the interval
in the timeout
```


Doing:
```js
console.log(__dirname);
console.log(__filename);
```

gives:
```bash
$ node global
/home/asterbot/WebDevStuff/nodejs
/home/asterbot/WebDevStuff/nodejs/global.js
```

(the paths you expect)


Also 
```js
console.log(document.querySelector);
```

does not work, since `document` object is *not* in the global scope of node-js, but it is in the global scope of the window when we work on the front-end. Since nodejs works on the server-side, it is not required



### Modules & Require
