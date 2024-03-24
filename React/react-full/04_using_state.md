# Using State (useState Hook)

**State** = data being used by a component at any given time

A naive approach to mutate data might be something like this:

```js
const Home = () => {
    let name='mario';
    
    const handleClick = (e)=>{
        name='luigi';
        console.log(name);
    }

    return ( 
        <div className="home">
            <h2>Homepage</h2>
            <p> {name} </p>
            <button onClick={handleClick}>Click me</button>
        </div>
     );
}
 
export default Home;
```

However, the name doesn't change onclick in the paragraph tag, even though the variable changes value.

The reason is that this variable is **local to this component!**. That means that the DOM did not get updated about this change happening, and this did not reflect it on the frontend. To also update the DOM, we need to update the DOM's state, which can be done via a **useState hook**

It isn't too bad:
```js
import { useState } from 'react';

const Home = () => {

    const [name,setName] = useState('mario');
    const [age, setAge] = useState(25);
    
    const handleClick = (e)=>{
        setName('luigi');
    }

    return ( 
        <div className="home">
            <h2>Homepage</h2>
            <p> {name} </p>
            <button onClick={handleClick}>Click me</button>
        </div>
     );
}
 
export default Home;
```
Now it updates correctly
