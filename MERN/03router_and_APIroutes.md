# Express Router & API Routes

## API Endpoints

For this project, we require the following endpoints:

| URL | Request Type | Description|
|-- | -- | -- |
| /workouts | GET | Gets all workout documents|
| /workouts | POST | Creates a new workout document|
| /workouts/:id | GET | Gets a single workout document |
| /workouts/:id | DELETE | Deletes a single workout
| /workouts/:id | PATCH | Updates a single workout

Create a new folder for routes and put **workouts.js** in it, and make the skeleton code:

```js
const express = require('express');

// Create a Router
const router = express.Router();

// GET all workouts!
router.get('/', (req,res)=>{
    res.json({msg: 'GET all workouts!'});
});

// GET single workout
router.get('/:id', (req,res)=>{
    res.json({msg : 'GET a single workout'});
})

// POST a new workout
router.post('/', (req,res)=>{
    res.json({msg: 'POST a new workout'});
});

// DELETE a workout
router.post('/:id', (req,res)=>{
    res.json({msg: 'DELETE a workout'});
});

// UPDATE a workout
router.patch('/:id', (req,res)=>{
    res.json({msg: 'UPDATE a workout'});
});



module.exports = router;
```

Then in **server.js** you can just do:
```js
require('dotenv').config()

const express = require('express');
const workoutRoutes = require('./routes/workouts');

const app = express();


app.use((req,res,next)=>{
    console.log('Path:',req.path,'  Method:' ,req.method);
    next();
});

app.use('/api/workouts',workoutRoutes);


// Listen at port 4000
app.listen(process.env.PORT, ()=>{
    console.log('Listening on port 4000');
})
```

## Handling a post/patch request
Here we send data to the server, we can access it via the `req` object<br>
But we can only access it via some middleware, so we need to add it:


```js
app.use(express.json());
```
All it does is it looks at the requests to identify whether it has a body, and if it does it attaches it to the request object so we can access it in the request handler

So in the post request we can get access to it via `req.body`