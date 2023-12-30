# MongoDB

| SQL | NoSQL |
| -- | -- |
|Tables | Collections|
| Rows | Documents|
| Columns | MongoDB| 

MongoDB is a **NoSQL** database

Mongo has collections and each collection stores a particular type of data

![Alt text](images/image-16.png)

Each collection stores documents(in this case Blog documents)

![Alt text](images/image-17.png)

A document is *like* a single record in a SQL database - each record document stores the title, snipped, body, etc.<br>
The `_id` field is auto-generated

## MongoDB Setup & Atlas
We can either locally install mongodb or use a cloud database

We will use a cloud database since its easier to manage<br>
The cloud server is called **Cloud Server Atlas**

### Create a new cluster
To create a new cluster, go to https://www.mongodb.com/cloud/atlas/ and create a new database

### Add data and collections
Once set up with a password(for this tutorial i've set the pwd `test12345`) then add data and set collection name to blogs (cuz)

### Add users
Then go to database access- add a new user and give a password(i've again set `test12345`) and give read/write permission to database

### Connect
Press connect -> Drivers -> Follow the instructions and copy the connection string


Then modify **app.js**:
```js
const express = require('express');
const morgan = require('morgan');

// express app
const app = express();

// connect to mongodb
const db_URI = 'mongodb+srv://asterbot:test12345@nodetrial.cbdbupi.mongodb.net/?retryWrites=true&w=majority';

// (rest of the code)...
```
## Mongoose=

Mongoose is an ODM library - *Object-Document mapping library*

![Alt text](images/image-18.png)

This basically wraps the standard mongodb API and provides us with a much more easy way to communicate with the database and use the database

It allows us to make *data models* which have query methods like get/delete/update database documents etc.

### Schemas

Schemas defines the structure of a type of data/document 
- properties & property types

Examples:
| User schema | Blog Schema|
| -- | -- |
| - name(string) required | title(string) required 
| - age(number) required | snipped(string) required
| - bio(string) required | body(string) required


### Models 
Models allow us to communicate with database collections

![Alt text](images/image-19.png)


### Using mongoose to connect to the database

First install it and stuff

Then to use it, first require it and use the connect method. Although we need to make one small modification to our `db_URI` string- add the name of the cluster(in this case `node-tuts`) after `...mongodb.net/`

So it should look something like this:
```js
const mongoose = require('mongoose');

// connect to mongodb
const db_URI = 'mongodb+srv://asterbot:test12345@nodetrial.cbdbupi.mongodb.net/node-tuts?retryWrites=true&w=majority';
mongoose.connect(db_URI);
```

If you get deprecation warnings, you can do:
```js
mongoose.connect(db_URI, { useNewUrlParser: true, useUnifiedTopology: true });
```

What happens is the `connect` functions takes long to complete, so it returns a *promise* so we can attach a `.then()` method to it

The `then()` method takes a callback function which runs when the connection is complete

This is the updated code:
```js
const db_URI = '...';
mongoose.connect(db_URI)
    .then((result)=> console.log('Connected to database'))
    .catch((err) => console.log(err))
```
Now the console will print that when the connection to the database has been done

Why do we want this? We want to ensure nothing is done relating to the server until the connection has been established 

So we can take the point where we listen to port 3000 (the line which says `app.listen(3000)` and put it in the `then` part of this code)

The entire relevant part of the code now is:
```js
const express = require('express');
const morgan = require('morgan');
const mongoose = require('mongoose');

// express app
const app = express();

// connect to mongodb
const db_URI = 'mongodb+srv://asterbot:test12345@nodetrial.cbdbupi.mongodb.net/node-tuts?retryWrites=true&w=majority';
mongoose.connect(db_URI)
    .then((result)=> app.listen(3000)) //listen for requests if connection is established
    .catch((err) => console.log(err))

// register view engine
app.set('view engine', 'ejs');

// middleware & static files
app.use(express.static('public'));

//etc..
```

Now we have to create a schema and model for our blog data<br>
Make a new folder named models to store this and put a file named `blog.js` in it:

```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
```

The `Schema` is a *constructor function* to create a new schema

We can create an instance of `Schema` using the new keyword:
```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const blogSchema = new Schema({
    title: {
        type: String,
        required: true
    },
    snippet: {
        type: String,
        required: true
    },
    body: {
        type: String,
        required: true
    }
}, { timestamps: true })
```
And this describes the schema. Each field has an object storing a `type` and `required` field, which describes what type the data is and whether it is required or not.\
`{ timestamps: true }` automatically generates timestamp properties for us for blog documents (which creates and updates it too)

Now that we have a schema, we need a *model* which let's us communicate with the database

So the entire js file would look like this:
```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;


// the schema
const blogSchema = new Schema({
    title: {
        type: String,
        required: true
    },
    snippet: {
        type: String,
        required: true
    },
    body: {
        type: String,
        required: true
    }
}, { timestamps: true })

// the model
const Blog = mongoose.model('Blog', blogSchema)

module.exports = Blog;
```
The model function takes in 2 arguments:
- A string - what happens is when we want to look for the database, it takes that name, pluralizes it and looks for it in the database collection. So we can use the `Blog` object to communicate with the database in the future
- the schema

## Getting & Saving Data
Now we will use the `Blog` object in **app.js**

```js
//(other require statements)
const Blog = require('./models/blog')

//(initialize express app, make mongoose connect etc.)

// mongoose and mongo sandbox routes
app.get('/add-blog', (req,res)=>{
    const blog = new Blog({
        title: 'new blog',
        snippet: 'about my new blog',
        body: 'more about my new blog'
    })
    blog.save();
});

// (rest of the routes)
```
All we did is when `/add-blog` route is found, we make a new blog object (which we exported in `blog.js`)

Now we also use the method `save()` to save it to the database\

So the model essentially looks for `Blogs` database, creates a document based on the parameters and the schema and saves it into the database

Although, the save method is a promise since it takes some time to execute, so we need to use `then` on this:
```js
app.get('/add-blog', (req,res)=>{
    const blog = new Blog({
        title: 'new blog',
        snippet: 'about my new blog',
        body: 'more about my new blog'
    })
    blog.save()
    .then((result)=>{
        res.send(result);
    })
    .catch((err)=>{
        console.log(err);
    })
});
```

So if we go to `localhost:3000/add-blog` we see a response which is the blog object we created!<br>
It looks like a JS object:
```js
{"title":"new blog","snippet":"about my new blog",
"body":"more about my new blog",
"_id":"658fb6b715e39abac6b95dfe",
"createdAt":"2023-12-30T06:20:39.973Z",
"updatedAt":"2023-12-30T06:20:39.973Z","__v":0}
```
> (i put the newlines, the browser did not)

The `_id` and `createdAt`/`updatedAt` timestamps are auto-generated!

Now this object is visible on the database too:
![Alt text](images/image-20.png)

What if we want to retreive all the blogs from the collection?

```js
app.get('/all-blogs', (req,res)=>{
    Blog.find()
        .then((result)=>{
            res.send(result);
        })
        .catch((err)=>{
            console.log(err);
        })
});
```
This `find` method returns the array of all blogs, and since its asynchronous we need to do `then`

On the browser at `localhost:3000/all-blogs` we see:
```
[{"_id":"658fb6b715e39abac6b95dfe","title":"new blog","snippet":"about my new blog","body":"more about my new blog","createdAt":"2023-12-30T06:20:39.973Z","updatedAt":"2023-12-30T06:20:39.973Z","__v":0},{"_id":"658fc1422f76431c4678256f","title":"new blog 2","snippet":"about my new blog","body":"more about my new blog","createdAt":"2023-12-30T07:05:38.253Z","updatedAt":"2023-12-30T07:05:38.253Z","__v":0}]
```
which is a 2-element array

What about loading a single blog? We require the thing that uniquely identifies it- it's ID!

Since an ID in the MongoDB database is its own object, we need to implicitly convert it to string and back. Mongoose takes care of this for us

```js
{"_id":{"$oid":"658fb6b715e39abac6b95dfe"},
"title":"new blog",
"snippet":"about my new blog",
"body":"more about my new blog",
"createdAt":{"$date":{"$numberLong":"1703917239973"}},
"updatedAt":{"$date":{"$numberLong":"1703917239973"}},
"__v":{"$numberInt":"0"}}
```

Now we can write one for the route `/single-blog`:
```js
app.get('/single-blog', (req,res)=>{
    Blog.findById('658fb6b715e39abac6b95dfe')
        .then((result)=>{
            res.send(result);
        })
        .catch((err)=>{
            console.log(err);
        })
});
```
Now it works! We know how to find all the blogs and search for a blog by it's ID

## Outputting documents in view

Now we will integrate with the remaining routes to make the website dynamically work:
```js
const express = require('express');
const morgan = require('morgan');
const mongoose = require('mongoose');
const Blog = require('./models/blog')

// express app
const app = express();

// connect to mongodb
const db_URI = 'mongodb+srv://asterbot:test12345@nodetrial.cbdbupi.mongodb.net/node-tuts?retryWrites=true&w=majority';
mongoose.connect(db_URI)
    .then((result)=> app.listen(3000)) //listen for requests if connection is established
    .catch((err) => console.log(err))

// register view engine
app.set('view engine', 'ejs');

// middleware & static files
app.use(express.static('public'));

// morgan middleware
// app.use(morgan('dev'));


// Routes
app.get('/',(req,res)=>{
    const blogs = [
        {title: 'Yoshi finds eggs', snippet: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'Mario finds stars', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'How to defeat bowser', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        
    ];
    res.render('index',{ title: 'Home', blogs: blogs });
});


app.get('/about',(req,res)=>{
    res.render('about',{ title: 'About' })
});


// Redirects
app.get('/about-us',(req,res)=>{
    res.redirect('/about');
});

// 404
app.use((req,res)=>{
    res.render('404', { title: '404' })
});
```

For now , let's remove the rendering of dummy data in `/` path and redirect it to `/blogs` - which will render data from the database

So the updated code looks like:

```js
app.get('/',(req,res)=>{
    res.redirect('blogs');
});

//...

app.get('/blogs', (req,res)=>{
    Blog.find()
        .then((result)=>{
            res.render('index', {title: 'All blogs', blogs: result})
        })
        .catch((err)=>{
            console.log(err);
        })
})
```

If we do `Blog.find().sort({createdAt:-1})` it will do it in reverse order of `createdAt`, ie. the newest will come first