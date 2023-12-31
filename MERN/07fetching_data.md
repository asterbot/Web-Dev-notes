# Fetching Data

Now that we have the base react app running, let's fetch data from the backend API

> **`useEffect` hook**<br>
> Used to perform side effects in functional components(like data fetching, subscriptions, manually changing DOM etc)<br>
> Helps in managing the lifecycle of a component and handling asynchronous operations
>
> **Syntax:**
> ```js
> useEffect(()=>{
>   // Side effect code here
>   return ()=>{
>       // Clean up code here (optional)
>   }
> }, [dependencies]);
>```
>
> Parameters:<br>
> **Effect function:** First argument to `useEffect` - code that will be executed as the side effect. Can also return a cleanup function
> **Dependencies Array(optional)**: Contains variables that, when changed, will trigger re-execution of the effect function. If dependencies array is ommitted, effect runs after every render
>
> **How it works**:
> 1. **Initial Render:** Effect fn is executed after the component has been initially rendered
> 2. **Subsequent Renders:** If a dependency array is specified, effect function is re-executed whenever any of the dependencies change
> 3. **Cleanup:** If a cleanup function is returned from the effect function, it is executed before the next render or when the component unmounts. This is useful for cleaning up resources (like subscriptions/timers) to prevent memleaks
>
> Example: Simple side effect
> ```js
> import React, { useEffect, useState } from 'react';
>function MyComponent() {
>  useEffect(() => {
>    // Side effect code (runs after every render)
>    console.log('Component has been rendered');
>
>    // Cleanup (optional)
>    return () => {
>      console.log('Component will unmount or re-render');
>    };
>  });
>
>  return <div>My Component</div>;
>}
> ```
>
> Example: Side effect with dependencies
>  ```js
> import React, { useEffect, useState } from 'react';
> function MyComponent({ userId }) {
>   useEffect(() => {
>     // Side effect code (runs when userId changes)
>     console.log(`Fetching data for user with ID: ${userId}`);
> 
>     // Cleanup (optional)
>     return () => {
>       console.log('Component will unmount or re-render');
>     };
>   }, [userId]);
> 
>   return <div>My Component</div>;
> }
> ```
> In this example, the component will re-run whenever `userId` prop changes


Use `useEffect` and `useState` hooks to write **Home.js**:
```js
import { useEffect, useState } from 'react'

const Home = ()=>{

    const [workouts,setWorkouts] = useState(null);

    useEffect(()=>{
        const fetchWorkouts = async ()=>{
            const response = await fetch('http:://localhost:4000/api/workouts');
            const json = await response.json();

            if (response.ok){
                setWorkouts(json);
            }
        }

        fetchWorkouts();
    }, []);

    return(
        <div className = "home">
            <h2>Home</h2>
        </div>
    )
}

export default Home;
```
> Here we want `fetchWorkouts` to be async but the parameter of `useEffect` cannot be async! So this is a valid workaround

First try to fetch response using the API route 

Remember our `GET` method for getting all workouts:
```js
// GET all workouts
const getAllWorkouts = async (req, res) => {
    // leave empty object since we want all docs
    const workouts = await Workout.find({}).sort({createdAt: -1}); 

    res.status(200).json(workouts);
};
```
It returns a JSON object of all the workouts, so in the front-end we just set our workouts variable to whatever the response from the API is<br>
And each object of the returned value is a workout

Now we can cycle through those workouts:

```js
const Home = ()=>{
    //...(effect & state hooks)
    return(
        <div className = "home">
            <div className="Workouts">
                {workouts && workouts.map((workout)=>(
                    <p key={workout._id}>{workout.title}</p>
                ))}    
            </div>
        </div>
    )
}
```
This doesn't work!<br>
If we try running the backend and front-end simulataneously, it won't work since `localhost:3000` won't have access to `localhost:4000`

To fix this, open **package.json**  in the frontend folder and add a `proxy` property at the top:
```json
{
  "proxy": "http://localhost:4000",
  "name": "frontend",
  //...
}
```
This just tells the react app to proxy any API requests it doesn't recognize to our backend server (which is `localhost:4000`)<br>
Also modify the `fetch` method in `Home` component:
```js
const response = await fetch('/api/workouts');
```
(no need to explicitly say `localhost:4000`)

Now the react app renders the data correctly!

## Rendering workout details