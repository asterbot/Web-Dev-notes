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
![Alt text](images/image-2.png)

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

does not work, since `document` object is *not* in the global scope of node-js, but it is in the global scope of the window when we work on the front-end. <br>
Since nodejs works on the server-side, it is not required



## Modules & Require

Consider:

**people.js**:
```js
const people = ['A','B','C','D'];

console.log(people);
```

**modules.js**:
```js
const xyz = require('./people')

console.log(xyz);   
console.log(people);
```

Running `modules`:
```bash
$ node modules
[ 'A', 'B', 'C', 'D' ]
{}
/home/asterbot/WebDevStuff/nodejs/modules.js:4
console.log(people);
            ^

ReferenceError: people is not defined
    at Object.<anonymous> (/home/asterbot/WebDevStuff/nodejs/modules.js:4:13)
    at Module._compile (node:internal/modules/cjs/loader:1376:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1435:10)
    at Module.load (node:internal/modules/cjs/loader:1207:32)
    at Module._load (node:internal/modules/cjs/loader:1023:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:135:12)
    at node:internal/main/run_main_module:28:49

Node.js v20.10.0
```

Notice `xyz` is an **empty object**<br>
Also `people` is not accessible in modules

This happens because `people.js` is not *exporting* anything

To do that:<br>
**people.js**
```js
const people = ['A','B','C','D'];

console.log(people);

module.exports = 'hello'; //this is what is returned to whoever requires this file
```

Now the output of `console.log(xyz)` is `hello`<br>
So we can do:
```js
//...
module.exports = people;
```

So running `modules.js`:
```js
const xyz = require('./people')

console.log(xyz);   
```
And we get:
```bash
$ node modules
[ 'A', 'B', 'C', 'D' ]
[ 'A', 'B', 'C', 'D' ]
```

### Returning multiple objects

**people.js**
```js
const people = ['A','B','C','D'];
const ages = [10,20,30,40];


module.exports = {
    people:people,
    ages:ages
};
```
An acceptable shorthand is:
```js
//...
module.exports={
  people,ages
};
```
And `modules.js` being the same

You get the output:
```bash
$ node modules
{ people: [ 'A', 'B', 'C', 'D' ], ages: [ 10, 20, 30, 40 ] }
```

Since we returned an object, we can just access the fields:<br>
**modules.js**
```js
const xyz = require('./people')

console.log("People:",xyz.people, "Ages:",xyz.ages);   
```
```bash
$ node modules
People: [ 'A', 'B', 'C', 'D' ] Ages: [ 10, 20, 30, 40 ]
```

Can also use *destructuring*:<br>

**modules.js**
```js
const { people, ages } = require('./people')

console.log("People:",people," Ages:" ,ages);   
```

```bash
$ node modules
People: [ 'A', 'B', 'C', 'D' ]  Ages: [ 10, 20, 30, 40 ]
```

Can also access `os` object by requiring it:

```js
const os = require('os');

console.log(os.platform(),'\n', os.homedir());
```
And you get:
```bash
linux 
 /home/asterbot
```

## File System in Node
Reading/Writing/Creating/Deleting files cannot be done on a computer system without Node!

First we need to import the module for files

```js
const fs = require('fs');
```

### Reading files
To read a file, call `fs.readFile` which takes in 2 arguments: the name of the file and a *callback function*

The callback function is called after the file is attempted to be read and requires 2 arguments: `err` and `data`:
- `err`: The error thrown (if any)
- `data`: The data found

So we can do:

Suppose `hello.txt` is:
```
Hello there, how are you?
```

```js
const fs = require('fs');

fs.readFile('hello.txt', (err,data)=>{
    if (err){
        console.log(err);
    }
    console.log(data);
});
```

Running gives:
```bash
<Buffer 48 65 6c 6c 6f 20 74 68 65 72 65 2c 20 68 6f 77 20 61 72 65 20 79 6f 75 3f>
```
A *buffer* is a package of data sent to us when we read the file

To see the string, just do
```js
//...
console.log(data.toString())
```

> Keep in mind that `readFile` takes some time to execute. Even though this happens, it doesn't halt the program. It will run instructions ahead and when it is finished reading it will call the callback function

```js
const fs = require('fs');

fs.readFile('hello.txt', (err,data)=>{
    if (err){
        console.log(err);
    }
    console.log(data.toString());
});

console.log('hi');
```

Will give the output:
```bash
hi
Hello there, how are you?
```

### Writing Files
Use `fs.writeFile` function which takes 3 arguments:
- the relative path to the file
- the new content
- a callback function which is executed when the write is finished


```js
fs.writeFile('hello.txt','hello world',()=>{
    console.log("file written");
});
```

Once the file is overwritten with `hello world`, the console outputs `file written`- as expected

If the file does not exist, **node creates the file and writes in that!**

### Directories

To make a directory, use `fs.mkdir()` which takes the name of the directory and the callback function

```js
fs.mkdir('./newdir',(err)=>{
    if (err){
        console.log(err);
    }
    console.log('directory created');
});
```
This creates the directory<br>
However, attempting to create it after the directory already exists gives the error:
```bash
[Error: EEXIST: file already exists, mkdir './newdir'] {
  errno: -17,
  code: 'EEXIST',
  syscall: 'mkdir',
  path: './newdir'
}
```

To avoid this erorr, use `existsSync()` which returns whether the directory exists or not
```js
if (!fs.existsSync('./newdir')){ 
    fs.mkdir('./newdir',(err)=>{
        if (err){
            console.log(err);
        }
        else{
          console.log('directory created');
        }
    });
}
```

To delete the directory if it already exists:
```js
if (!fs.existsSync('./newdir')){ 
    fs.mkdir('./newdir',(err)=>{
        if (err){
            console.log(err);
        }
        else{
            console.log('directory created');
        }
    });
}
else{
    fs.rmdir('./newdir',(err)=>{
        if (err){
            console.log(err);
        }
        else{
            console.log('directory deleted');
        }
    });
}
```

### Deleting files
To delete a file, first ensure it exists and then use `unlink`

```js
if (fs.existsSync('./newdir/deleteme.txt')){
    fs.unlink('./newdir/deleteme.txt',(err)=>{
        if (err){
            console.log(err);
        }
        else{
            console.log('file deleted');
        }
    })
}
```


## Streams & Buffers
Better than files - scale up better

**Streams** - Start using data, before it has finished loading

We break data stream into *buffers* which are small chunks of data to be accessed.

There are different kinds of streams: **Read Streams** & **Write Streams**

### Read Stream
To initialize a read stream, do
```js
const readStream = fs.createReadStream('lorem_ipsum.txt');
```
This initializes the stream, and node starts to read the data

To get chunks of the stream, do
```js
readStream.on('data', (chunk)=>{
    console.log('----NEW CHUNK------');
    console.log(chunk);
});
```

`readStream.on` is an *event-listener*- the event being every time we receive a buffer from the stream. <br>
It is a *data-event*, hence we write `data` as the first parameter<br>
Every time a new chunk of data is received, the callback function is called

Running this for *long* files (like 5000 lines or something) gives something like this:
```
-----NEW CHUNK------
<Buffer 4c 6f 72 65 6d 20 69 70 73 75 6d 20 64 6f 6c 6f 72 20 73 69 74 20 61 6d 65 74 2c 20 63 6f 6e 73 65 63 74 65 74 75 72 20 61 64 69 70 69 73 63 69 6e 67 ... 65486 more bytes>
-----NEW CHUNK------
<Buffer 65 63 74 75 73 20 63 6f 6d 6d 6f 64 6f 20 6e 6f 6e 2e 20 44 75 69 73 20 70 75 72 75 73 20 6a 75 73 74 6f 2c 20 66 65 75 67 69 61 74 20 76 69 74 61 65 ... 65486 more bytes>

(etc...)
```

If we do `console.log(chunk.toString());` we see it in readable format and not as buffer objects

Another alternative is to initialize the readstream as follows:
```js
const readStream = fs.createReadStream('lorem_ipsum.txt', { encoding: 'utf8' });
```


### Write Streams

For writing data using chunks
```js
const fs = require('fs');

const readStream = fs.createReadStream('lorem_ipsum.txt', { encoding: 'utf8'});

const writeStream = fs.createWriteStream('write_test.txt'); //initialize the write stream

readStream.on('data', (chunk)=>{
    console.log('-----NEW CHUNK------');
    console.log(chunk);    

    // write the chunks we receive
    writeStream.write('\n---NEWCHUNK------\n')
    writeStream.write(chunk);
});
```
And now `write_test.txt` will have the same content as `lorem_ipsum.txt`


### Piping
Another way to transfer chunks from `readStream` to `writeStream` is:

```js
const fs = require('fs');

const readStream = fs.createReadStream('lorem_ipsum.txt', { encoding: 'utf8'});

const writeStream = fs.createWriteStream('write_test.txt'); //initialize the write stream

readStream.pipe(writeStream);
```