# Express Setup

Create a folder called backend and create `server.js` in it. Also do `npm init -y` to intialize npm and then do `npm install express`

Also modify `package.json`:
```json
{
  "name": "backend",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "dev": "nodemon server"  // add this line
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
Now we can run `npm run dev` to run our application



Set up express in the standard way:
```js
const express = require('express');

const app = express();

// listen for requests
app.listen(4000, ()=>{
    console.log('Listening on port 4000');
})

// Routes
app.get('/', (req,res)=>{
    res.json({msg: 'Welcome to the app'});
});
```


Now create a `.env` file in the backend folder - this stores all environment variables

```env
PORT=4000
```
The idea is if you're pushing this to github, you make put this in the gitignore so that everything here stays private 

To actually use this, do
```bash
npm install dotenv
```
This loads the variables from the `.env` file into the `process.env` object - available *globally* in a nodejs environment

Now you can update server.js as follows:
```js
require('dotenv').config()

const express = require('express');

const app = express();


// Listen at port 4000
app.listen(process.env.PORT, ()=>{
    console.log('Listening on port 4000');
})


// Routes
app.get('/', (req,res)=>{
    res.json({msg: 'Welcome to the app'});
});
```

Can also add some middleware:

```js
//...
app.use((req,res,next)=>{
    console.log('Path:',req.path,'  Method:' ,req.method);
    next();
});
```