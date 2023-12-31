# Controllers

We should implement controllers for the code where the database logic can go

We will implement 5 functions:
- GET all workouts
- GET a single workout by ID
- CREATE a new workout
- DELETE a workout by ID
- UPDATE a workout by ID

Create a directory controllers and put `workoutController.js` in it



## CREATE and GET workouts

**workoutController.js:**
```js
const Workout = require('../models/workoutModel');

//...

// CREATE a new workout
const createWorkout = async (req,res)=>{
    const {title,load,reps} = req.body;

    // add workout to db
    try{
        const workout = await Workout.create({title, load, reps});
        res.status(200).json(workout);
    }
    catch(err){
        res.status(400).json({error: err.message});
    }

    res.json({msg: 'POST a new workout'});
}
```

**workouts.js:** (route file)
```js
const express = require('express');
const workoutController = require('../controllers/workoutController');

// POST a new workout
router.post('/', workoutController.createWorkout);
```

Similarly can add other functions:

**workoutController.js**:
```js
const Workout = require('../models/workoutModel');

// GET all workouts
const getAllWorkouts = async (req, res) => {
    // leave empty object since we want all docs
    const workouts = await Workout.find({}).sort({createdAt: -1}); 

    res.status(200).json(workouts);
};

// GET a single workout by ID
const getWorkout = async (req, res) => {
    const id = req.params.id; 

    const workout = await Workout.findById(id);

    if (!workout){
        return res.status(404).json({error: 'No such workout'});
    }

    // if it exists, send it
    res.status(200).json(workout);

};

// CREATE a new workout
const createWorkout = async (req,res)=>{
    const {title,load,reps} = req.body;

    // add workout to db
    try{
        const workout = await Workout.create({title, load, reps});
        res.status(200).json(workout);
    }
    catch(err){
        res.status(400).json({error: err.message});
    }

    res.json({msg: 'POST a new workout'});
}
```

**workouts.js**:

```js
const express = require('express');
const workoutController = require('../controllers/workoutController');

// Create a Router
const router = express.Router();

// GET all workouts!
router.get('/', workoutController.getAllWorkouts);

// GET single workout
router.get('/:id', workoutController.getWorkout)

// POST a new workout
router.post('/', workoutController.createWorkout);

module.exports = router;
```


One small problem, putting a random URL for getting a single workout crashes the website. Fix?<br>
The error we get is by Mongoose, when we do `Workout.findById(id)` we need to first perform a check that the `id` is in-fact, a valid *kind* of ID

```js
const getWorkout = async (req, res) => {
    const id = req.params.id; 

    if (!mongoose.Types.ObjectId.isValid(id)){
        return res.status(404).json({error:'No such workout'});
    }

    const workout = await Workout.findById(id);

    if (!workout){
        return res.status(404).json({error: 'No such workout'});
    }

    // if it exists, send it
    res.status(200).json(workout);

};
```
This pre-check is important to do

## Delete and Update workouts

The delete function is straightforward:
```js
// DELETE a workout by ID
const deleteWorkoutById = async (req, res) => {
    const id = req.params.id;
    
    if (!mongoose.Types.ObjectId.isValid(id)){
        return res.status(404).json({error:'No such workout'});
    }

    const workout = await Workout.findOneAndDelete({_id: id});

    if (!workout){
        return res.status(404).json({error:'No such workout'});
    }

    res.status(200).json(workout);

};
```

The update function is a bit more tricky

```js
// UPDATE a workout by ID
const updateWorkoutById = async (req, res) => {
    const id = req.params.id;
    if (!mongoose.Types.ObjectId.isValid(id)){
        return res.status(404).json({error:'No such workout'});
    }

    const workout = await Workout.findOneAndUpdate({_id: id}, {
        title: 'abc'; //for example
    })
};

```
Here, this code would update the title field to `abc` and leave the other fields alone.<br>
When we send a patch(similar to post request) request, we need the data (which we will update *to*) and it will be updated in this code.

So we can do:
```js
const updateWorkoutById = async(req,res)=>{
    //...
    const workout = await Workout.findOneAndUpdate({_id: id}, {
        ...req.body
    })
};
```
This is the *spread operator* of JS
> **More about the spread operator**<br>
> For example, if `req.body` returns `{title: "Bench Press", reps: 40, load: 50}` then `...req.body` would expand to `title: "Bench Press", reps:40, load:50`<br>
> This would be used to update the document at the corresponding `_id` field


Now the back-end is complete!