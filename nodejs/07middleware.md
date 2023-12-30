# Middleware

- Code which runs (on the server) between getting a request and sending a response

![Alt text](images/image-13.png)
Any code that runs between receiving a request and sending a response is a middleware

Usually `app.use(func)` is used to run middleware code (like we did to handle 404 cases)

Usually you have multiple middlewares which would run on the server before a response is sent

![Alt text](images/image-14.png)
This runs each of them top-to-bottom in the server

For example, if we receive the route `/` it would go something like this:

![Alt text](images/image-15.png)

## Middleware examples
- Logger middleware to log details of every request
- Authentication check middleware for projected routes
- Middleware to parse JSON data from request
- Return 404 pages


## Creating middleware in code

Currently we already have *some* middleware:
```js
const express = require('express');

// express app
const app = express();

// register view engine
app.set('view engine', 'ejs');

// listen for requests
app.listen(3000);

app.get('/',(req,res)=>{
    const blogs = [
        {title: 'Yoshi finds eggs', snippet: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'Mario finds stars', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'How to defeat bowser', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        
    ];
    res.render('index',{ title: 'lolollo', blogs: blogs });
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
First we try to match different URL routes, and if none of them work the middleware for 404 pages fires

Let's try to see what `app.use` receives as a request by putting this on the top:

```js
app.use((req,res)=>{
    console.log('New request made');
    console.log('Host:', req.hostname);
    console.log('Path:', req.path);
    console.log('Method:',req.method);
});
```
And we get the output:
```
New request made
Host: localhost
Path: /
Method: GET
```
Although the browser is hanging, since after express runs this code, it doesn't know how to move on to other middleware (which are the remaining `.get()` statements)

## Using next()

This is a parameter which you can take when writing the callback function for the middleware. All you need to do is invoke it:
```js
app.use((req,res,next)=>{
    console.log('New request made');
    console.log('Host:', req.hostname);
    console.log('Path:', req.path);
    console.log('Method:',req.method);
    next(); //invoke it
});
```

Let's test this by adding multiple middlewares

```js
const express = require('express');

// express app
const app = express();

// register view engine
app.set('view engine', 'ejs');

// listen for requests
app.listen(3000);

app.use((req,res,next)=>{
    console.log('New request made');
    console.log('Host:', req.hostname);
    console.log('Path:', req.path);
    console.log('Method:',req.method);
    next();
});

app.use((req,res,next)=>{
    console.log('In the next request');
    next();
});


app.get('/',(req,res)=>{
    const blogs = [
        {title: 'Yoshi finds eggs', snippet: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'Mario finds stars', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'How to defeat bowser', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        
    ];
    res.render('index',{ title: 'lolollo', blogs: blogs });
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

And we get the output:
```
New request made
Host: localhost
Path: /
Method: GET
In the next request
```

So clearly, it got to the next middleware


Suppose we put the second middleware *after* the home page render:
```js
//...
app.use((req,res,next)=>{
    console.log('New request made');
    console.log('Host:', req.hostname);
    console.log('Path:', req.path);
    console.log('Method:',req.method);
    next();
});

app.get('/',(req,res)=>{
    const blogs = [
        {title: 'Yoshi finds eggs', snippet: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'Mario finds stars', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        {title: 'How to defeat bowser', snippet:'Lorem ipsum dolor sit amet, consectetur adipiscing elit'},
        
    ];
    res.render('index',{ title: 'lolollo', blogs: blogs });  //(1)
});

app.use((req,res,next)=>{
    console.log('In the next request');
    next();
});

//...
```

In this case the second middleware will NOT fire, since we send a response at (1), so it never gets ahead


## 3rd party middleware
We will use a middleware called "morgan" to log stuff to the console, since it is better than what we have:
```js
app.use((req,res,next)=>{
    console.log('New request made');
    console.log('Host:', req.hostname);
    console.log('Path:', req.path);
    console.log('Method:',req.method);
    next();
});
```

According to documentation, after requiring it we need to call the function with a parameter like this:
```js
morgan('tiny')
```
which tells *how the log is to be formatted*

Can replace the `app.use` code we currently have with this:
```js
const morgan = require('morgan');

//...

app.use(morgan('dev'));
//...
```

Since `app.use` takes in a function, instead of writing our own arrow function we will use the function we required

```
GET / 304 7.818 ms - -
GET / 304 1.512 ms - -
GET /about 304 2.515 ms - -
GET /about 304 1.274 ms - -
```

You can use different options such as `morgan('tiny')` and find out what they do through documentation

## Static Files
With our current implementation, we would not be able to access static files

For example, create `style.css` in the current directory:
```css
body{
    background:black;
}
``` 

This 
- can't be accessed on the browser doing `localhost:3000/styles.css`
- can't be accessed by any of the HTML(EJS) files

We need to provide which files are to be allowed publicly accessible and which ones are *not*

This is done using the **static middleware**

To do this, we do:
```js
const express = require('express');
const morgan = require('morgan');

// express app
const app = express();

// register view engine
app.set('view engine', 'ejs');

// listen for requests
app.listen(3000);

// middleware & static files
app.use(express.static('public'));

// (rest of the code)
```

All this does is looks for a directory named `public` and allows public access for those for any files on the front-end

So in `header.ejs` we can add this line in the header:
```html
<link rel="stylesheet" href="styles.css">
```

And now the background color is black :D (this looks so ugly)

Notice how we didn't say `public/styles.css` since it automatically looks in the public folder because of our js code above

Also now we can do `localhost:3000/styles.css` and we can see it on the browser since its public

Now we can move all the css in the css file instead of a `<style>` tag

Now `header.ejs` is much more simple:
```html
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WEBSITE WOOHOOO | <%= title %></title>
    <link rel="stylesheet" href="/styles.css">
</head>
```
