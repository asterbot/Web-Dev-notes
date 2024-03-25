# useEffect hook

This hook runs a function on every render of a component and also when the *state* (from state hook) changes, and re-renders the DOM accordingly.

This is a way to run code *on every render*.

It is simple to set up:

```js
import { useState , useEffect } from 'react';

useEffect(()=>{
        console.log("useEffect ran");
    });
```
We can now see when this function runs! \
It runs in the start and every time the state changes. 

This is useful for having side effects (like for authentication services, API Calls, etc)

# Dependencies

If we want `useEffect` to run only after certain conditions (and not after every render) we use dependencies 

Dependencies is just an array you can pass into the hook

Starting with an empty dependency array:
```js
useEffect(()=>{
        console.log("useEffect ran");
    }, []);
```
This only runs at the very start (not after state updates)

This array can contain values which, upon changing state, should re-render the DOM

Let's consider this example:

```js
const Home = ()=>{

    useEffect(()=>{
        console.log("useEffect ran");
        console.log(name);
    }, [name]);

    return ( 
        <div className="home">
            <button onClick={()=>setName("Luigi")}>Set name!</button>
            <p>{ name }</p>
        </div>
     );

}
```

Now the useEffect hook *depends* on `name` so it will update when `name` updates. The console will show this:

```js
useEffect ran
Mario
useEffect ran
Luigi
```
It will run after the button press to set the name (ie. change its state)


