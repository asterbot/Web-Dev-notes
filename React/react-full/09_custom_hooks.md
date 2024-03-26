# Making a Custom Hook

Currently in **Home.js** we have a setup with state hooks for variables and the useEffect hook to fetch data.

If we want to do something similar in another component (fetch data and update values) - we can abstract it away into it's own JS file and create the logic there

This is called **creating a custom hook** and can be used over various components.

This is the code in **Home.js** currently:
```js
import { useState , useEffect } from 'react';
import BlogList from './BlogList';

const Home = () => {

    const [blogs, setBlogs] = useState(null);
    const [isPending, setIsPending] = useState(true);
    const [error, setError] = useState(null);

    useEffect(()=>{ 
        fetch('http://localhost:8000/blogs')
            .then(res => {
                if (!res.ok){
                    throw Error('Could not fetch data for that resource');
                }
                return res.json();
            })
            .then(data=>{
                setBlogs(data);
                setIsPending(false);
                setError(null);
            })
            .catch(err =>{
                setError(err.message);
                setIsPending(false);
            })
    }, []);

    return ( 
        <div className="home">
            {error && <div>{error}</div>}
            {isPending && <div>Loading...</div>}
            {blogs && <BlogList blogs={blogs} title="All Blogs"/>}
        </div>
     );
}
 
export default Home;
```

## Custom Hook to fetch data!

Create `src/useFetch.js` 

> Side note: Remember to write the word "use" before the name of your hook - otherwise it won't work

We will generalize it to fetch *any* kind of data on any URL API

```js
import { useState, useEffect  } from "react";

const useFetch = (url)=>{
    const [data, setData] = useState(null);  // general data
    const [isPending, setIsPending] = useState(true);
    const [error, setError] = useState(null);

    useEffect(()=>{ 
        fetch(url) //general URL
            .then(res => {
                if (!res.ok){
                    throw Error('Could not fetch data for that resource');
                }
                return res.json();
            })
            .then(data=>{
                setData(data);
                setIsPending(false);
                setError(null);
            })
            .catch(err =>{
                setError(err.message);
                setIsPending(false);
            })
    }, [url]); //put URL as dependency for useEffect

    return {data, isPending, error};
}

export default useFetch;
```

Now this can be used to fetch data at *any* URL

We can modify **Home.js** like this:

```js
import BlogList from './BlogList';
import useFetch from './useFetch';


const Home = (url) => {

    // grab data and call it "blogs" in this context
    const {data: blogs, isPending, error} = useFetch('http://localhost:8000/blogs');

    return ( 
        <div className="home">
            {error && <div>{error}</div>}
            {isPending && <div>Loading...</div>}
            {blogs && <BlogList blogs={blogs} title="All Blogs"/>}
        </div>
     );
}
 
export default Home;
```

and it's done!

Now this can fetch any data from a URL and return the data itself, the pending statr and the error state