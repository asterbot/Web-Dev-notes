# Using JSON Server

We can use the `useEffect` hook to fetch data.

We can just set up a REST API to fetch the data, but for now we will use a JSON server. \
In the react project, create `data/db.json` and add JSON styled data for the blogs:

```json
{
    "blogs": [
      {
        "title": "My First Blog",
        "body": "yap yap yap",
        "author": "mario",
        "id": 1
      },
      {
        "title": "Opening Party!",
        "body": "yap yap yap",
        "author": "yoshi",
        "id": 2
      }
    ]
  }
```

Each blog is an object with the respective fields. \
We need to use a JSON server package.

To monitor this JSON file with the server, run:

```bash
npx json-server --watch data/db.json --port 8000
```
and you will get this on the output:

```bash
JSON Server started on PORT :8000
Press CTRL-C to stop
Watching data/db.json...

( ˶ˆ ᗜ ˆ˵ )

Index:
http://localhost:8000/

Static files:
Serving ./public directory if it exists

Endpoints:
http://localhost:8000/blogs
```

Now this is *watching* the file and has wrapped it in API endpoints! Now we need to send GET requests to get the data lol

The endpoints we will be using:

| URL | Request type | Description |
|-|-|-|
| `/blogs` | GET | Fetch all blogs |
| `/blogs/{id}`  | GET | Fetch a single blog |
| `/blogs` | POST | Add a new blog
| `/blogs/{id}` | DELETE | Delete a blog


# Fetching Data using useEffect

Set the blogs in **Home.js** to `null`:
```js
const [blogs, setBlogs] = useState(null);
```

and now we will use the useEffect to fetch data from the API

```js
useEffect(()=>{ 
        fetch('http://localhost:8000/blogs')
            .then(res => {
                return res.json();
            })
            .then(data=>{
                setBlogs(data);
            })
    }, []);

```

But this gives a runtime error:

```
Cannot read properties of null (reading 'map')
```

Since the function is asynchronous, blogs isn't updated on the first render so it is trying to map over `null` which is a runtime error.

So you can change the `BlogList` tag a bit:

```js            
{blogs && <BlogList blogs={blogs} title="All Blogs" handleDelete={handleDelete}/>}
```

And there you go. Now it will only render when the blogs are ready.

We will add delete calls to the API now.

# Conditional Loading messages

To print a message while the data is being fetched. 

Add a new state

```js
const [isPending, setIsPending] = useState(true);
```
and use this to conditionally render.
```js
{isPending && <div>Loading...</div>}
```

and in the useEffect hook you can do:
```js
useEffect(()=>{ 
        fetch('http://localhost:8000/blogs')
            .then(res => {
                return res.json();
            })
            .then(data=>{
                setBlogs(data);
                setIsPending(false);
            })
    }, []);
```

And now it will show up when it is pending!

# Handling fetch errors

If we have any errors (like network errors we need to catch it with the useEffect hook):

```js
useEffect(()=>{ 
        fetch('http://localhost:8000/blogs')
            .then(res => {
                return res.json();
            })
            .then(data=>{
                setBlogs(data);
                setIsPending(false);
            })
            .catch(err =>{
                console.log(err.message);
            })
    }, []);
```

A funny way to simulate this error is to just shut the JSON server.

Then in the console you will get the following:

```
GET http://localhost:8000/blogs net::ERR_CONNECTION_REFUSED
(anonymous) @ Home.js:11
Show 11 more frames
Home.js:20 Failed to fetch
```

If there are  other errors (like if the server gives an error because of invalid endpoints or the request is denied) then the `catch` block may not catch it since it is still sending a response object `res` to us, and the response object may not contain data but instead contain the error.

We can use the `ok` property of the response object.

If we modify the useEffect hook as:
```js
useEffect(()=>{ 
        fetch('http://localhost:8000/blogs')
            .then(res => {
                console.log(res);
                if (res.ok){
                    // some logic                    
                }
                return res.json();
            })
    //etc..
},[])
```

We will see the `res` object logged on the console, with the `ok` property being true.

You can throw an error which if `res` is not ok, *which will be caught by the catch block*

```js
useEffect(()=>{ 
        fetch('http://localhost:8000/blogs')
            .then(res => {
                console.log(res);
                if (!res.ok){
                    throw Error('Could not fetch data for that resource');
                }
                return res.json();
            })
            .then(data=>{
                setBlogs(data);
                setIsPending(false);
            })
            .catch(err =>{
                console.log(err.message);
            })
    }, []);
```

You will see your custom error message in the console if you try to fetch an invalid endpoint (like `http://localhost:8000/lol`)

Let's set this error in a state

```js
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
                setError(null); // reset error to null just in case
            })
            .catch(err =>{
                setError(err.message);
                setIsPending(false); // to make sure the loading doesn't come if there's an error
            })
    }, []);
```

And you can conditionally render an error too:

```js 
return ( 
        <div className="home">
            {error && <div>{error}</div>}
            {isPending && <div>Loading...</div>}
            {blogs && <BlogList blogs={blogs} title="All Blogs"/>}
        </div>
     );
```

