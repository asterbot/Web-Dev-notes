# Deleting Blogs

You can add a delete button in **BlogDetails.js**:

```js
import { useParams } from "react-router-dom";
import useFetch  from './useFetch';

const BlogDetails = () => {

    const { id } = useParams(); // get id from the URL
    const { data: blog, error, isPending } = useFetch('http://localhost:8000/blogs/' + id); 

    const handleClick = ()=>{
        // delete logic
    }

    return ( 
        <div className="blog-details">
            { isPending && <div>Loading...</div> }
            { error && <div>{error}</div> }
            { blog && (
                <article>
                    <h2>{ blog.title }</h2>
                    <p>Written by: { blog.author }</p>
                    <div>{blog.body}</div>
                    <button onClick={handleClick}>Delete</button>
                </article>
            )}
        </div>
     );
}
 
export default BlogDetails;
```

Now let's write the `handleClick` function

```js
const navigate = useNavigate();

const handleClick = ()=>{
    // delete logic
    fetch('http://localhost:8000/blogs/' + blog.id, {
        method: 'DELETE'
    }).then(()=>{
        navigate('/');
    })
}
```

and the delete works!

# 404 pages 

Page where you go to an invalid URL

Let's make **NotFound.js**:
```js
import { Link } from "react-router-dom";

const NotFound = () => {
    return ( 
        <div className="not-found">
            <h2>Sorry</h2>
            <p>This page doesn't exist uwu</p>
            <Link to="/">Back to the homepage...</Link>
        </div>
     );
}
 
export default NotFound;
```

So you can modify **App.js** to show this when you enter an invalid URL. 
```js
import Navbar from './Navbar.js';
import Home from './Home';
import Create from './Create';

import { BrowserRouter as Router, Route, Routes} from 'react-router-dom';
import BlogDetails from './BlogDetails.js';
import NotFound from './NotFound.js';

function App() {
  return (
    <Router>
      <div className="App">
        <Navbar />
        <div className="content">
          <Routes>
            <Route exact path="/" element={<Home />} /> 
            <Route exact path="/create" element={<Create />} />
            <Route exact path="/blogs/:id" element={<BlogDetails />} />
            <Route path="*" element={<NotFound />} />          
          </Routes>
        </div>
      </div>
    </Router>
  );
}

export default App;
```
The `*` is a a globbing pattern! Be sure to put it *at the end*.

And voila! Done
