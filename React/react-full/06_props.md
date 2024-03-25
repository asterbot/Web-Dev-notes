# Props

Currently when rendering lists, we have the following code in `Home.js`

```js
import { useState } from 'react';

const Home = () => {

    const [blogs, setBlogs] = useState([
        { title: 'My website is so cool!', body: 'see it :)', author: 'me', id:1},
        { title: 'I like Mario', body: '(luigi is nice too)', author: 'me', id:2},
        { title: 'Ok but yoshi', body: 'and his eggs', author: 'me', id:3}
    ])

    return ( 
        <div className="home">
            {blogs.map((blog)=>(
                <div className="blog-preview" key={blog.id}>
                    <h2>{blog.title}</h2>
                    <p>Written by: {blog.author}</p>
                </div> 
            ))}
        </div>
     );
}
 
export default Home;
```

We can abstract away the entire HTML template of rendering the blog list into it's own individual re-usable component. It can be used for other parts of the website too!

The neat thing we can do is we can pass it different data each time we use the component. This could be useful if we only want to render the blogs which match search keywords on a separate page.

Let's make a new **BlogList.js** component:

```js
const BlogList = () => {
    return ( 
        <div className="blog-list">
            {blogs.map((blog)=>(
                <div className="blog-preview" key={blog.id}>
                    <h2>{blog.title}</h2>
                    <p>Written by: {blog.author}</p>
                </div> 
            ))}
        </div>
     );
}
 
export default BlogList;
```

The problem is now that `blogs` isn't defined! So just trying to import `BlogLists` into the home component and calling it won't work

To pass this data to the component, we use props. \
Advantages of props:
1. BlogList is re-usable
2. Can use data in Home component in other places instead of copy-pasting


The way to use `props` is that it is an object that the function `BlogList` can use:

```js
const BlogList = (props) => {
    const blogs = props.blogs;
    const title = props.title;
    
    return ( 
        <div className="blog-list">
            <h2>{title}</h2>
            {blogs.map((blog)=>(
                <div className="blog-preview" key={blog.id}>
                    <h2>{blog.title}</h2>
                    <p>Written by: {blog.author}</p>
                </div> 
            ))}
        </div>
     );
}
 
export default BlogList;
```

And we can call it like this in **Home.js**

```js
<BlogList blogs={blogs} title="All Blogs"/>
```
where `blogs` is defined in this component.


## Destructuring the props
A more elegant way to do this is to destructure the props object.


`BlogList.js`
```js
const BlogList = ({blogs,title}) => {
    
    return ( 
        <div className="blog-list">
            <h2>{title}</h2>
            {blogs.map((blog)=>(
                <div className="blog-preview" key={blog.id}>
                    <h2>{blog.title}</h2>
                    <p>Written by: {blog.author}</p>
                </div> 
            ))}
        </div>
     );
}
 
export default BlogList;
```


## Re-using components

We can re-use the same `BlogList` prop in Home.js multiple times:

```js
const Home = () => {

    const [blogs, setBlogs] = useState([
        { title: 'My website is so cool!', body: 'see it :)', author: 'yoshi', id:1},
        { title: 'I like Mario', body: '(luigi is nice too)', author: 'me', id:2},
        { title: 'Ok but yoshi', body: 'and his eggs', author: 'yoshi', id:3}
    ])

    return ( 
        <div className="home">
            <BlogList blogs={blogs} title="All Blogs"/>
            <BlogList blogs={blogs.filter((blog)=> blog.author==='yoshi')} title="Yoshi's Blogs" /> 
        </div>
     );
}
 
export default Home;
```

We can use the filter function toi filter out blogs :D


# Functions as props

Allowing users to delete blogs by a button click

Let's first add a delete button in `BlogList.js`:

```js
const BlogList = ({blogs,title}) => {

    const handleDelete = (id)=>{
        // delete logic
    }

    return ( 
        <div className="blog-list">
            <h2>{title}</h2>
            {blogs.map((blog)=>(
                <div className="blog-preview" key={blog.id}>
                    <h2>{blog.title}</h2>
                    <p>Written by: {blog.author}</p>
                    <button onClick={()=> handleDelete(blog.id)}>Delete blog</button>
                </div> 
            ))}
        </div>
     );
}
 
export default BlogList;
```

How will the delete logic work? The `blogs` is stored in **Home.js**

BUT `blogs` was defined with a state hook, so we can just use that to modify it! But we need to modify the data *in* Home.js, so we should move the `handleDelete` function there and pass it as a prop to `Bloglist.js`

So `Home.js`:
```js
import { useState } from 'react';
import BlogList from './BlogList';

const Home = () => {

    const [blogs, setBlogs] = useState([
        { title: 'My website is so cool!', body: 'see it :)', author: 'yoshi', id:1},
        { title: 'I like Mario', body: '(luigi is nice too)', author: 'me', id:2},
        { title: 'Ok but yoshi', body: 'and his eggs', author: 'yoshi', id:3}
    ])

    const handleDelete = (id)=>{
        // delete logic
    }

    return ( 
        <div className="home">
            <BlogList blogs={blogs} title="All Blogs" handleDelete={handleDelete}/>
        </div>
     );
}
 
export default Home;
```

`BlogList.js`:
```js
const BlogList = ({blogs,title,handleDelete}) => {

    return ( 
        <div className="blog-list">
            <h2>{title}</h2>
            {blogs.map((blog)=>(
                <div className="blog-preview" key={blog.id}>
                    <h2>{blog.title}</h2>
                    <p>Written by: {blog.author}</p>
                    <button onClick={()=> handleDelete(blog.id)}>Delete blog</button>
                </div> 
            ))}
        </div>
     );
}
 
export default BlogList;
```

We can define `handleDelete` as follows:

```js
const handleDelete = (id)=>{
    const newBlogs = blogs.filter((blog)=>  blog.id !== id );
    setBlogs(newBlogs);
}
```

