# View Engines

Currently all the content on the website is static (ie. predefined and doesn't change)

To inject dynamic data(like from databases) or user-data

This is where view engines/template engines come in

View engines let us write HTML like:
```html
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>WEBSITE WOOHOOO</title>
        <link rel="stylesheet" href="#">
    </head>
    <body>
        <nav>
            <a href="/">Home</a>
            <a href="/about">About</a>
        </nav>

       <h1>Hello World</h1>
       <p>This is a paragraph</p>

    </body>
</html>
```
But allows to put logic like variables, loops etc.

## EJS(Embedded JavaScript templates)
Find on npm website

To set it up in the js, do:
```js
const express = require('express');

// express app
const app = express();

// register view engine
app.set('view engine', 'ejs');
```
Now it knows the view engine we want is ejs

Make `index.ejs` file:
```html
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>WEBSITE WOOHOOO</title>
        <link rel="stylesheet" href="#">
    </head>
    <body>
        <nav>
            <a href="/">Home</a>
            <a href="/about">About</a>
        </nav>

       <h1>Hello World</h1>
       <p>This is a paragraph</p>

    </body>
</html>
```

Currently it is the same as vanilla HTML

To render the view, we do:
```js
app.get('/',(req,res)=>{
    res.render('index');
});
```