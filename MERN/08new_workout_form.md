# New Workout Form

We can add a form to create a new workout, ie. a new react component

We need 3 states for each field of a workout document:<br>
**WorkoutForm.js**
```js
import { useState } from 'react';

const WorkoutForm = () =>{
    const [title, setTitle]= useState('');
    const [load, setLoad]= useState('');
    const [reps, setReps]= useState('');

    return(
        <form className="create" onSubmit={handleSubmit}>
            <h3>Add a new Workout</h3>
            <label>Exercise Title</label>
            <input 
                type="text"
                onChange={(event) => setTitle(event.target.value)} 
                value = {title}
            />

            <label>Load(in kg)</label>
            <input 
                type="number"
                onChange={(event) => setLoad(event.target.value)} 
                value = {load} 
            />

            <label>Reps:</label>
            <input 
                type="number"
                onChange={(event) => setReps(event.target.value)} 
                value = {reps} 
            />
        </form>
    )
}

export default WorkoutForm;
```

Now we need to create the `handleSubmit` function in the `onSubmit` part of the form 

```js
// inside the WorkoutForm function
const [error,setError] = useState(null);

const handleSubmit = async (event)=>{
    event.preventDefault(); // to prevent the default action of form submission: refresh

    const workout = {title,load,reps};

    const response = await fetch('/api/workouts', {
        method: 'POST',
        body: JSON.stringify(workout), //changes workout to JSON string
        headers : {
            'Content-Type': 'application/json',
        }
    })
    const json = await response.json();

    if (!response.ok){
        setError(json.error);
    }
    else{
        setError(null);
        // If it was ok-reset everything to empty string
        setTitle('');
        setLoad('');
        setReps('');
        console.log('New workout added!');
    }
}
```

Currently it won't show the list of workouts until and unless we refresh after submitting a new one

To fix that, we need to use **React Context**...