# NPM(Node Package Manager)

## Third party packages
To get packages which are not available in core node, go to [https://www.npmjs.com/](https://www.npmjs.com/)

These include things like a js utility library, a package to help in validation logic or some framework for building node websites(like express)

All of these are installed using NPM

The website contains documentation and installation instructions

## Installing packages globally(nodemon)

Currently we have to restart the server file every time we make any change to our code. <br>
Nodemon automatically restarts the server without having to restart it

Follow the installation instruction on the website

Now you can run
```bash
nodemon server
```

and it will do the same thing as `node server` except it will update the website automatically if you make changes to your `server.js` file


## package.json file

The package file keeps track of any packages we install locally + project details and project specific scripts

To create a `package.json` file, run this on the terminal:
```bash
npm init
```

and it makes a `package.json` file

**package.json:**
```json
{
  "name": "server",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js"
  },
  "author": "",
  "license": "ISC"
}
```

## Installing packages locally

To install `lodash`, we need to run this(according to the website):
```bash
$ npm i --save lodash
```
The `--save` saves it to the local dependencies for the project<br>
Once installed, you will see `package.json` updated with this:
```json
//...
"dependencies": {
    "lodash": "^4.17.21"
  }
```
This stores the dependencies - keeps track of all packages used in the project

When seeing documentation you may see code fragments like:
```js
_.chunk(array, [size=1])
```
The `_` stands for whatever we name the variable when we require it

When we installed `lodash`, it created a folder named `node_modules` which stores all the node libraries we are using for the project(you will see lodash there)

So when
```js
const _ = require('lodash');
```
is run, it checks in the `node_modules` folder

Getting a random number using lodash:
```js
const num = _.random(0,20);
```
If we want something to run exactly once, we can do:
```js
const greet = _.once(()=>{
    console.log('hello');
});

greet(); // this is only going to run once
greet(); // this will not run again
```

## Dependencies

Suppose someone downloads a node project and (obviously) won't download `node_modules`, they can get the correct `node_modules` since all the dependencies are noted in `package.json`

To re-install `node_modules` depending on what the dependencies are, you can just do:
```bash
npm install
```
This will look inside `package.json` and install all the dependencies