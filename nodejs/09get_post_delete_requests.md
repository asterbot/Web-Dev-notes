# Get, Post & Delete Requests

## Request Types
So far we've only had handlers for GET requests (using `app.get`)- used for getting data from the server

Types of requests:
- GET requests: get a resource
- POST requests: create new data(eg. a new blog)
- DELETE requests: (eg. delete a blog)
- PUT requests: (eg. update data on a blog)

Examples of requests:

| URL | Request type | Description|
| --- | ------------ | -----------|
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>GET</td><td>Displays the blogs on the browser</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/create</td><td>GET</td><td>Displays the web form for creating new blogs</td></tr> | 
| localhost:3000/blogs | POST | When a new blog is created, this request is sent to create it in the database|
| localhost:3000/blogs/:id | GET | Gets blog with the corresponding ID|
| localhost:3000/blogs/:id | DELETE | Deletes blog with corresponding ID
| localhost:3000/blogs/:id | PUT | Update blog with corresponding ID|

We have already doene the GET requests(we aren't doing PUT requests yet)

## POST Request

Sequence of events to occur:
- User enters data on form website
- Data is stored in the js file which is in turn stored in the database
- The view is updated

### Sending the POST request
Observing the HTML(ejs) file for the form:
```html
<html lang="en">
<%- include("./partials/head.ejs") %>

<body>
  <%- include("./partials/nav.ejs") %>

  <div class="create-blog content">
    <form>
      <label for="title">Blog title:</label>
      <input type="text" id="title" name="title" required>
      <label for="snippet">Blog snippet:</label>
      <input type="text" id="snippet" name="snippet" required>
      <label for="body">Blog body:</label>
      <textarea id="body" name="body" required></textarea>
      <button>Submit</button>
    </form>
  </div>

  <%- include("./partials/footer.ejs") %>
</body>
</html>
```

Modify the `form` tag:
```html
<form action="/blogs" method="POST">
```
This sends a POST request to the URL `/blogs` which in turn will perform the relevant action upon recieiving this request<br>
> Keep in mind to make the `name` unique for each input field so that we can access it in the back-end

To receive this in the js file as something usable, we need to use some middleware. In **app.js** add this line next to other middlewares:

```js
app.use(express.urlencoded({ extended: true }));
```
This will make it easy to access the received data

Now for testing we can do:
```js
app.post('/blogs', (req,res)=>{
    console.log(req.body);
});
```
And if we submit a blog with everything having "hi", the console will have:
```bash
{ title: 'hi', snippet: 'hi', body: 'hi' }
```

Which is a JS object!

If we didn't use the relevant expressJS middleware, we would get `undefined` logged to the console

So we can save in the database like we did previously on this post request:
```js
app.post('/blogs', (req,res)=>{
    const blog = new Blog(req.body);
    blog.save()
        .then((result)=>{
            res.redirect('blogs');
        })
        .catch((err)=>{
            console.log(err);
        })
});
```
and redirect to the home(all blogs) page

| URL | Request type | Description|
| --- | ------------ | -----------|
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>GET</td><td>Displays the blogs on the browser</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/create</td><td>GET</td><td>Displays the web form for creating new blogs</td></tr> | 
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>POST</td><td>When a new blog is created, this request is sent to create it in the database</td></tr> |
| localhost:3000/blogs/:id | GET | Gets blog with the corresponding ID|
| localhost:3000/blogs/:id | DELETE | Deletes blog with corresponding ID
| localhost:3000/blogs/:id | PUT | Update blog with corresponding ID|

## Route parameters

The variable parts of the route that may change value

> For example in this URL:<br>
> localhost:3000/blogs/<font color="cyan">:id</font> <br>
> Examples:
> - localhost:3000/blogs/<font color="cyan">12345</font>
> - localhost:3000/blogs/<font color="cyan">50</font>
> - localhost:3000/blogs/<font color="cyan">hello</font>

In express, we will extract the route parameter and use it as a variable

Let's make each blog in `/blogs` a URL link which shows the body of the blog!

Do that by going to **index.ejs** and changing:

```html
<div class="blog-content">
        <h2>All blogs</h2>
        

        <% if(blogs.length > 0){ %>
            <% blogs.forEach((blog)=>{ %>
              <a class="single" href="/blogs/<%= blog._id %>">
                <h3 class="title"> <%=blog.title %> </h3>
                <p class="snippet"> <%=blog.snippet %> </p> 
              </a>
                <br>  
            <% }) %>
        <%} else{ %>
            <h2>there are no blogs :/</h2>
        <% } %>

       </div>
```
All it does is redirect to `/blogs/(--id of blog--)` <br>
For example, `http://localhost:3000/blogs/6590abad51979f36a107277b`

Currently it doesn't have any page for it, so it gives the 404 page

To create a get request for this, we can do this in **app.js**:

```js
app.get('/blogs/:id', (req,res)=>{
    const id = req.params.id;
    console.log(id);
});
```

> Make sure to put `:id` and not `id` otherwise it will match with the literal string id

After clicking one of the blogs, the console prints its ID exactly

We can render a page called `details` for this case:

```js
app.get('/blogs/:id', (req,res)=>{
    const id = req.params.id;
    Blog.findById(id)
        .then((result)=>{
            res.render('details', {blog: result, title: 'Blog details'})
        })
        .catch((err)=>{
            console.log(err);
        })
});
```

Now we can make a new `details.ejs` page in views:
```html
<html>
    <%- include('./partials/head.ejs') %>

    <body>
        <%- include('./partials/nav.ejs') %>

        <div class="details content">
            <h2><%=blog.title %></h2>
            <div class="content">
                <p><%= blog.body %></p>
            </div>
        </div>

        <%- include('partials/footer.ejs') %>

    </body>

</html>
```
All this does it renders the title and body based on the blog it receives (which it will upon the get request after the `<a>` tag in index.ejs)

Progress:
| URL | Request type | Description|
| --- | ------------ | -----------|
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>GET</td><td>Displays the blogs on the browser</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/create</td><td>GET</td><td>Displays the web form for creating new blogs</td></tr> | 
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>POST</td><td>When a new blog is created, this request is sent to create it in the database</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/:id</td><td>GET</td><td>Gets blog with the corresponding ID</td></tr> |
| localhost:3000/blogs/:id | DELETE | Deletes blog with corresponding ID
| localhost:3000/blogs/:id | PUT | Update blog with corresponding ID|


## Delete requests
In **details.ejs** we can add an anchor tag for deleting the current blog:

```html
 <div class="details content">
    <h2><%=blog.title %></h2>
    <div class="content">
        <p><%= blog.body %></p>
    </div>
    <a class="delete" data-doc="<%= blog._id %>">delete</a>
</div>
```

`data-doc` is a *custom attribute* which just sets it to the ID of whatever document we want to delete<br>
To access it we just do `dataset.doc` (or whatever you have after `data-`)

Let's put a script tag in this html page:

```html
<script>
    const delete_event = document.querySelector('a.delete');

    delete_event.addEventListener('click', (event)=>{
        // Sets the endpoint of what we want to send the delete request to
        const endpoint = `/blogs/${delete_event.dataset.doc}`;
        
        // Fetch method sends a request, and the object specifies the method
        fetch(endpoint,{
            method: 'DELETE',
        })
        .then(()=>{})
        .catch((err)=>console.log(err));
    })
</script>
```

> **What `fetch()` does**<br>
> It is used to make HTTP requests<br>
> Syntax:
> ```js
> fetch(url, options)
>  .then((response) => {
>    // handle the response
>  })
>  .catch((error) => {
>    // handle errors
>  });
> ```
> The `options` object can include setting such as HTTP method(GET,POST,DELETE)etc.<br>
> Also this returns a promise that resolves to the `Response` object representing the response to the request (given from the backend in this case - since it is listening for requests :D)
 
Remember this script is local to the front-end, so we need to handle the actual request on the server-side

**app.js**
```js
app.delete('/blogs/:id', (req,res)=>{
    const id = req.params.id;
    Blogs.findByIdAndDelete(id)
            .then((result)=>{
                //(what to do?)
            })
            .catch((err)=>{
                console.log(err);
            })
});
```

Now, we must send some JSON to the front-end using the delete request

> **Why can't we simply redirect?**<br>
> `fetch` is a AJAX request since it is from javascript(and not some webform)<br> 
> Node doesn't allow redirects on AJAX requests! We must send JSON data back to the browser- *the JSON we return will have a redirect property* which will redirect on the frontend (since we can't do that on the server, since it is an AJAX request)


So this code becomes in **app.js**:
```js
app.delete('/blogs/:id', (req,res)=>{
    const id = req.params.id;
    Blogs.findByIdAndDelete(id)
            .then((result)=>{
                res.json({ redirect: '/blogs' })
            })
            .catch((err)=>{
                console.log(err);
            })
});
```

Now we must use this returned JSON on the front end(ie. in the `<script>` tag)

```html
<script>
    const delete_event = document.querySelector('a.delete');

    delete_event.addEventListener('click', (event)=>{
        const endpoint = `/blogs/${delete_event.dataset.doc}`;
        
        fetch(endpoint,{
            method: 'DELETE',
        })
        .then((response)=>{return response.json()})
        .then((data)=>console.log(data))
        .catch((err)=>console.log(err));
    })
</script>
```

The `console.log(data)` gives:
```bash
DELETE /blogs/6590b74f9b4fce3e61c79617
```

Now we can just in the case change it to actually redirect

```html
<script>
    const delete_event = document.querySelector('a.delete');

    delete_event.addEventListener('click', (event)=>{
        const endpoint = `/blogs/${delete_event.dataset.doc}`;
        
        fetch(endpoint,{
            method: 'DELETE',
        })
        .then((response)=>{return response.json()})
        .then((data)=> {window.location.href = data.redirect})
        .catch((err)=>console.log(err));
    })
</script>
```

> A bit of JS: the `data` parameter in the second promise catch is the same as the `response.json()` returned by the first promise catch. It is finding the data of its own promise, and whatever is returned once the function is complete

| URL | Request type | Description|
| --- | ------------ | -----------|
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>GET</td><td>Displays the blogs on the browser</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/create</td><td>GET</td><td>Displays the web form for creating new blogs</td></tr> | 
| <tr style="background-color: green;"><td>localhost:3000/blogs</td><td>POST</td><td>When a new blog is created, this request is sent to create it in the database</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/:id</td><td>GET</td><td>Gets blog with the corresponding ID</td></tr> |
| <tr style="background-color: green;"><td>localhost:3000/blogs/:id</td><td>DELETE</td><td>Deletes blog with corresponding ID</td></tr> |
| localhost:3000/blogs/:id | PUT | Update blog with corresponding ID|

No PUT in this lesson :O