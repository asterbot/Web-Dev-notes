# Controlled Inputs(Forms)

Adding webforms to react

We can take the data users enters and store it in a state, and use that to send a POST request to the browser.

Let's make the template of the form:  \
**Create.js**
```js
const Create = () => {
    return ( 
        <div className="create">
            <h2>Add a New Blog</h2>
            <form>
                <label>Blog title:</label>
                <input type="text" required />
                
                <label>Blog body:</label>
                <textarea required />

                <label>Blog Author:</label>
                <select>
                    <option value="mario">Mario</option>
                    <option value="luigi">Luigi</option>
                </select>
                <button>Add Blog</button>
            </form>
        </div>
     );
}
 
export default Create;
```


Now we want to keep track of what the user enters in the fields!

Create a state for the `title` for example:
```js
const [title, setTitle]=useState("");
```

and update the input:
```js
<form>
    <label>Blog title:</label>
    <input type="text" required 
        value={title}
        onChange={(e)=> setTitle(e.target.value)}/>
```

It will update the `title` upon typing, and the `onChange` function gets triggered.

# Submit Events

To the form tag, add `onSubmit` propety:

```js
<form onSubmit={handleSubmit}>
```

and in `handleSubmit` let's do this for now:

```js
const [title, setTitle]=useState("");
const [body, setBody]=useState("");
const [author, setAuthor]=useState("mario");

const handleSubmit = (e)=>{
    e.preventDefault(); // this prevents a default refresh
    const blog = { title, body, author };
    console.log(blog);
}
```

and we can see the object that got created in the log

# Making a POST request


It's very similar to a GET request, just specify the type of request and the body correctly

```js
const handleSubmit = (e)=>{
        e.preventDefault(); // this prevents a default refresh
        const blog = { title, body, author };
        
        fetch('http://localhost:8000/blogs', {
            method: 'POST',
            headers: { "Content-Type": "applications/json"},
            body: JSON.stringify(blog) // convert blog to json string
        }).then(()=>{
            console.log("New blog added!")
        })
    }
```

Let's add a state for `isPending` if the POST request hasn't gone through yet

```js
import { useState } from "react";

const Create = () => {
    const [title, setTitle]=useState("");
    const [body, setBody]=useState("");
    const [author, setAuthor]=useState("mario");
    const [isPending, setIsPending] = useState(false);

    const handleSubmit = (e)=>{
        e.preventDefault(); // this prevents a default refresh
        const blog = { title, body, author };

        setIsPending(false);
        
        fetch('http://localhost:8000/blogs', {
            method: 'POST',
            headers: { "Content-Type": "applications/json"},
            body: JSON.stringify(blog) // convert blog to json string
        }).then(()=>{
            console.log("New blog added!");
            setIsPending(false);
            setAuthor("");
            setBody("");
            setTitle("");
        })
    }

    return ( 
        <div className="create">
            <h2>Add a New Blog</h2>
            <form onSubmit={handleSubmit}>
                {/* Rest of the stuff */}
                { !isPending && <button>Add Blog</button>}
                { isPending && <button disabled>Loading...</button> }
            </form>

        </div>
     );
}
 
export default Create;
```

# Programatic Redirects

After submitting, say we want to redirect to the home page to view all the blogs

New react hook: `useNavigate`
```js
import { useNavigate } from "react-router-dom";

    const navigate = useNavigate();

    const handleSubmit = (e)=>{
        e.preventDefault(); // this prevents a default refresh
        const blog = { title, body, author };

        setIsPending(false);
        
        fetch('http://localhost:8000/blogs', {
            method: 'POST',
            headers: { "Content-Type": "applications/json"},
            body: JSON.stringify(blog) // convert blog to json string
        }).then(()=>{
            console.log("New blog added!");
            setIsPending(false);
            setAuthor("");
            setBody("");
            setTitle("");
            navigate('/'); // this will send you back to the home page
        })
    }
```


