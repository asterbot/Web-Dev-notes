# Express Apps

## What is express?

Remember in the code, we put cases to render different HTML pages based on the URL:
```js
//...
let path = './views/';
switch(req.url){
    case '/':
        path+='index.html'
        res.statusCode=200;
        break;
    case '/about':
        path+='about.html'
        res.statusCode=200;
        break;
    case '/about-us':
        res.statusCode=301;
        res.setHeader('Location','/about');
        res.end();
        break;
    default:
        path+='404.html'
        res.statusCode=404;
        break;
}
//...
```

This will get tougher to maintain the longer your project becomes

Express is a framework that easily manages routing, server side requests etc. for us - it does it in a much more elegant way

Seeing the documentation for express:
```js
const express = require('express')
const app = express() //sets up an express app

app.get('/', function (req, res) { 
    //GET request (like we have been dealing with)
  res.send('Hello World')
})

app.listen(3000)
```
This is similar to the first case of our switch statements

This extends to larger projects better


## Creating an express app
Create a new file for the express app (typically called `app.js`)

Setting it up is easy:
```js
const express = require('express');

// express app
const app = express();
```

To actually listen to requests(similar to `server.listen`)
```js
app.listen(3000);  //localhost is implied
```

Now we can listen to a GET request using `app.get()`
```js
//...
app.get('/',(req,res)=>{
    res.send('<h1>Hello world</h1>');
});

app.get('/about',(req,res)=>{
    res.send('<h1>About page</h1>');
});
```
Here we use `res.send()` instead of `res.write()`/`res.end()`

It is better since it infers the type of content we want to send the browser and **implicitly sends the content-type header and the status code!** - we don't need to manually set it


## Routing & HTML

To route a HTML *file* instead of vanilla HTML we can use `res.sendFile` which is another thing express has

```js
res.sendFile('./views/index.html');
```

There is one small problem- this function accepts **absolute paths** and **not relative paths**

To give it a relative path, need to specify the root directory:

```js
app.get('/',(req,res)=>{
    res.sendFile('./views/index.html', { root: __dirname })
});

app.get('/about',(req,res)=>{
    res.sendFile('./views/about.html', { root: __dirname })
});
```

Now we can add hyperlinks in the HTML pages to navigate between them. Something like this:
```html
<nav>
    <a href="/">Home</a>
    <a href="/about">About</a>
</nav>
```

## Redirects & 404's

When we did redirects in vanilla node, we just create a new case:
```js
case '/about-us':
    res.statusCode=301;
    res.setHeader('Location','/about');
    res.end();
    break;
```
With expess, we can do:
```js
// Redirects
app.get('/about-us',(req,res)=>{
    res.redirect('/about');
});
```
which is much easier!

How about adding a 404 page?<br>
Use the `app.use()` function to create a middleware and fire the middleware function

```js
//...
app.use((req,res)=>{
    res.sendFile('./views/404.html', { root: __dirname })
});
```
The idea is that this is called at the end ONLY if the code reaches that point (ie. another request has not been handled)

This does not require a `url`, it will fire the function *for every single URL*(only if the code reaches there ofc)

So it should be at the **end of your code**

