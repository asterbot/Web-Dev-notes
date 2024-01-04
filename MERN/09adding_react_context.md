# Adding React Context

When we add a new document it doesn't do anything until we refresh the page. This is because the local react workouts isn't in sync with the database

- We could force a refetch-too much work for a single document
- We can update the state locally for each new document - using *react context*

> **React context**<br>
> Way to provide global state to many different components so that you don't have to manually pass props at each level<br>
> Helps managing state & making it accessible to multiple components in a more efficient and organized manner
>
> Concepts:
> 1. **Provider** component is used to wrap the part of your component tree where you want to make a certain piece of data available.<br>
> Takes a `value` prop, which represents data to share
> ```js
> const myContext = React.createContext(defaultValue);
> //...
> <MyContext.Provider value={...some_data...}>
> // (components that can access the context)
> </MyContext.Provider>
> ```
> When creating the context, it is given value `defaultValue` is it does not find a matching **Provider** in the ancestor tree 
> <br><br>
> 2. **Consumer** component is used to consume data provided by **Provider**- takes a function as a child, where the context value is passed as an argument:
> ```js
> return(
>   <MyContext.Consumer>
>   {(value)=>{(render something based on context)}}
>   </MyContext.Consumer>
> )
> ```
> 3. **useContext hook** - allows functional components to subscribe to context changes. It takes the context object as an argument and returns the current context value
> ```js
> import { useContext } from 'react';
> 
> function myComponent() {
>    const contextValue = useContext(MyContext);
>    // use contextValue as needed
> }
> ```
> Simple example:
> ```js
> // Create context
> import React, { createContext, useState, useContext } from 'react';
> 
> // Creating custom context
> const myContext = createContext();
>
> // Custom Provider
> function myProvider( {children} ){
>    const data = ___;
>
>    return(
>        <myContext.Provider value={value}>
>            {children}
>        </myContext.provider>
>    )
>}
>
> // Child component
> function App(){
>    const contextValue = useContext(myContext);
>
>    return(
>        <div>
>            <h1>{contextValue}</h1>
>            <ChildComponent />
>        </div>
>    )
>}
>
> // Grandchild component
> function childComponent(){
>    const contextValue = useContext(myContext);
>
>    return(
>        <div>
>            <p>{contextValue}</p>
>        </div>
>    )
> }
> 
>   
> // The final render:
> const root = ...;
> root.render(
>     <myProvider>
>         <App />
>     </myProvider>
> )
> ```

Rather than using props to communicate between components, we will use Contexts

Create a folder contexts create:<br>
**workoutContext.js**   
```js
import { createContext  } from "react";

export const WorkoutsContext = createContext();

export const WorkoutsContextProvider = ({ children })=>{

    return(
        <WorkoutsContext.Provider>
            {children}
        </WorkoutsContext.Provider>
    );
}
```

Also we are passing `children` as props, so we can simply go to **index.js**:
```js
//other import statements
import WorkoutsContextProvider from './context/WorkoutContext';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <WorkoutsContextProvider>
      <App />
    </WorkoutsContextProvider>
  </React.StrictMode>
);
```
And the whole component tree (from App) is included in the Context, ie. it's like the global variable for it

Now what value do we give the Context? We can pass an object with a `workouts` property which is simply an array of workout objects. This object will be available throughout the application in any component<br>
Since this keeps changing, this should be a *dynamic state value* - maybe the `useState` hook? We can update it to add/remove workouts.

Instead, we will use a different hook called `useReducer`
> **useReducer hook**<br>
> This is better than `useState` for complex state logic in the components
>
> It takes 2 main arguments: a reducer function and an initial state. The reducer function receives the current state and action, and based on the action type, returns a new state
>
> Example:
> ```js
> import { useReducer } from 'react';
> 
> // Reducer function
> const reducer = (state, action)=>{
>     switch(action.type){
>         case 'INCREMENT':
>             return { count: state.count + 1 };
>         case 'DECREMENT':
>             return { count: state.count - 1 };
>         default:
>             return state;
>     }
> }
> 
> // Component using useReducer:
> const Counter () =>{
>    const [state,dispatch] = useReducer(reducer, {count: 0});
>
>    return(
>        <div>
>            <p> Count: {state.count}</p>
>            <button onClick={()=>dispatch({type: 'INCREMENT'})}> Increment </button>
>            <button onClick={()=>dispatch({type: 'DECREMENT'})}>Decrement </button>
>            
>    )
>}
> ```
> Here the `reducer` function handles the logic depending on the action type `INCREMENT`/ `DECREMENT`. The `useReducer` intializes the state as an object `{count:0}` and returns the `state` & `dispatch` function<br>
> The `dispatch` function is used to send actions to the reducer, triggering state updates
>
> The `dispatch` function can also accept a property called `payload`- this is any data you may want while updating the state
>
> For example:
> ```js
> // Reducer function
> const reducer = (state,action)=>{
>  switch(action.type){
>    case 'SET_USERNAME':
>      return {...state, username: action.payload};
>    case 'INCREMENT':
>      return {...state, count: state.count + action.payload};
>    default:
>      return state;
>  }
>}
>
> const Login = ()=>{
>  const [state,dispatch] = useReducer(reducer, {username:'', count:0});
>
>  return(
>    <div>
>      <p> Username: {state.username}</p>
>      <button onclick={dispatch({type: 'SET_USERNAME', payload: 'John Doe'})}> Set Username </button>
>      <button onclick={dispatch({type: 'INCREMENT', payload: 5})}> Increment Count </button>
>      
>    </div>
>  )
>}
> 
> ```

In our case, we have:

**WorkoutContext.js:**
```js
import { createContext, useReducer  } from "react";

export const WorkoutsContext = createContext();

export const WorkoutsContextProvider = ({ children })=>{
    const [state, dispatch] = useReducer(workoutsReducer,{
        workouts: null
    });

    return(
        <WorkoutsContext.Provider value={..(something)..}>
            {children}
        </WorkoutsContext.Provider>
    );
}
```
Our `dispatch` function will have it's `type` field as what action we want to do(eg: `ADD_WORKOUT`, `SET_WORKOUT` etc) and the payload is the array of workouts after the change.<br>

For now, we can write the following for `workoutsReducer`:
```js
export const workoutsReducer = (state,action)=>{
    switch(action.type){
        case 'SET_WORKOUTS':
            return {
                workouts: action.payload
            }
        
        case 'CREATE_WORKOUT':
            return{
                workouts: [action.payload, ...state.workouts] //state.workouts is the previous state(so spread it) 
            }
        
        default:
            return state;
    }
}
```

And in the final we can return
```js
export const WorkoutsContextProvider = ({ children })=>{
    const [state, dispatch] = useReducer(workoutsReducer,{
        workouts: null
    });

    return(
        <WorkoutsContext.Provider value={{....state, dispatch}}>
            {children}
        </WorkoutsContext.Provider>
    );
}
```
We will return the state/dispatch for the reducer hook. This is so that any component may edit/access the data as needed

## Writing our own hook

Let's write our own hook for the same thing. Create a folder hooks and create the file:

**useWorkoutsContext.js**:
```js
export const useWorkoutContext = ()=>{
    const context = useContext(WorkoutsContext);

    if (!context){
      throw Error('useWorkoutsContext must be used inside a WorkoutsContextProvider')
    }

    return context;
}
```
> This may seem like a lot of setup for a global variable, but in the long run it makes things easier when a lot of different components start to share & update the same state

Now we can use this hook to consume the context

Going to **Home.js**, we currently are using the state hook for the workouts array:
```js
const [workouts,setWorkouts] = useState(null);
```

We will instead use the hook we just created

Now we can get the context value using the hook and de-structure it to get the value and update it accordingly

