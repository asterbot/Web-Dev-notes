# Establishing database connection

Most of how to set up is found on the nodejs tutorial

The updated **server.js** looks like this:

```js
require('dotenv').config()

const express = require('express');
const mongoose = require('mongoose');
const workoutRoutes = require('./routes/workouts');

//express app
const app = express();

// middleware
app.use(express.json());

app.use((req,res,next)=>{
    console.log('Path:',req.path,'  Method:' ,req.method);
    next();
});

// Routes
app.use('/api/workouts',workoutRoutes);

// connect to DB
mongoose.connect(process.env.MONGOURI)
    .then(
        // Listen at port 4000
        app.listen(process.env.PORT, ()=>{
            console.log('Listening on port 4000');
        })
    )
    .catch((err)=>console.log('Could not connect \n',err))
```

Yes, put the `MONGO_URI` in the `.env` file

## Making schema and model for our database

Create **workoutModel.js** inside models:
```js
const mongoose = require('mongoose');

const Schema = mongoose.Schema;

const workoutSchema = new Schema({
    title: {
        type: String,
        required: true
    },
    reps: {
        type: Number,
        required: true
    },
    load: {
        type: Number,
        required:true
    }
}, { timestamps: true });

const Workout = mongoose.model('Theworkout', workoutSchema);

module.exports = Workout;
```

And in `workouts.js` in routes, we can update the POST method:
```js
router.post('/', async (req,res)=>{
    const {title,load,reps} = req.body;

    try{
        const workout = await Workout.create({title, load, reps});
        res.status(200).json(workout);
    }
    catch(err){
        res.status(400).json({error: err.message});
    }

    res.json({msg: 'POST a new workout'});
});
```