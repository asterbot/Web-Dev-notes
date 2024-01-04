# Deleting Data

Let's place a delete button in the top-right corner of each button. The button will send a delete request to the API which will in turn communicate with MongoDB and delete it

After deleting, we also need to update our global state(the context) to delete the item from it as well. We will use the `dispatch` with a different kind of action

Let's use the **WorkoutsDelete.js** component:

```js
const WorkoutDetails = ({ workout })=>{
    return(
        <div className="workout-details">
            <h4>{workout.title}</h4>
            <p><strong>Load(kg):</strong> {workout.load}</p>
            <p><strong>Reps:</strong> {workout.reps}</p>
            <p>{workout.createdAt}</p>
            <span onClick = {handleClick}>Delete</span>
        </div>
    )
}

export default WorkoutDetails;
```

Then we can create the `handleClick` function:

```js
const WorkoutDetails = ({ workout }) =>{
    const handleClick = async ()=>{
        const response = await fetch('/api/workouts/'+ workout._id, {
            method: 'DELETE',
        });
        const json = await response.json();

        if (response.ok){
            dispatch({type: 'DELETE_WORKOUT', payload: json})
        }
    }

}
```

We also need to add the case for the `DELETE_WORKOUT` in the reducer function for the context:

```js
export const workoutsReducer = (state,action)=>{
    switch(action.type){
        //other cases
        
        case 'DELETE_WORKOUT':
            return{
                workouts: state.workouts.filter((w)=>w._id !== action.payload._id)
            }
        
        default:
            return state;
    }
}
```

The filter function (yay functional programming :D)