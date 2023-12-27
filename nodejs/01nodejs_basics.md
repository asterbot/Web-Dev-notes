# Node.js Basics

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



## The Global Object

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



## Modules & Require
