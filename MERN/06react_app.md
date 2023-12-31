# Setting up a React App

Now the front-end

Run 
```bash
npx create-react-app frontend
```
And the skeleton of the react application is ready in the frontend folder

In `src` folder get rid of everything except `App.js`, `index.css` and `index.js`

Modify the following files:

**App.js**:
```js
function App() {
  return (
    <div className="App">
      
    </div>
  );
}

export default App;
```

**index.js:**
```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

(just get rid of all the testing stuffs)

In the frontend folder, run:
```bash
npm install react-router-dom
```

So that we can use the router 


Now, modify **App.js**:
```js
import { BrowserRouter, Routes, Route} from 'react-router-dom'


function App() {
  return (
    <div className="App">
      <BrowserRouter>
        <div className="pages">
          <Routes>
            <Route
              path="/"
              element={}
            />
          </Routes>
        </div>
      </BrowserRouter>
      
    </div>
  );
}

export default App;
```

> - `<BrowserRouter>`: Route everything that needs the routing system
> - `<Routes>` List the routes as `Route` components
> - `<Route />`
>   - path: the path URL(`/` is home page) and renders the `Home` component
>   - element: what we want to render for that path


Let's create a component to render. Create a folder pages and make:

**Home.js**
```js
const Home = ()=>{
    return(
        <div className = "home">
            <h2>Home</h2>
        </div>
    )
}

export default Home;
```

And then modify **App.js**:
```js
import { BrowserRouter, Routes, Route} from 'react-router-dom'

// Pages & Components
import Home from './pages/Home'


function App() {
  return (
    <div className="App">
      <BrowserRouter>
        <div className="pages">
          <Routes>
            <Route
              path="/"
              element={<Home />}
            />
          </Routes>
        </div>
      </BrowserRouter>
      
    </div>
  );
}

export default App;
```

And you can run `npm start` and it will run

## Navbar component
Create a new folder for components and put 

**navbar.js**:
```js
import { Link } from 'react-router-dom';

const Navbar = ()=>{
    return(
        <header>
            <div className="container">
                <Link to="/">
                    <h1>Workout Buddy</h1>
                </Link>
            </div>
        </header>
    )
}

export default Navbar;
```

> **What `<Link>` is**<br>
> Creates Links in your application to manage routing. The `to` prop is used to describe the location where the Link should navigate to<br>
> Example:
> ```js
> <Link to='/about'>About</Link>
> ```
> This would render an anchor tag that navigates to "/about" when clicked<br>
> This is not going to rely on the backend for the link, it will just handle it on the browser - no need for a request!


So we can now modify **App.js**:
```js
import { BrowserRouter, Routes, Route} from 'react-router-dom'

// Pages & Components
import Home from './pages/Home'
import Navbar from './components/navbar'

function App() {
  return (
    <div className="App">
      <BrowserRouter>
        <Navbar />

        <div className="pages">
          <Routes>
            <Route
              path="/"
              element={<Home />}
            />
          </Routes>
        </div>
      </BrowserRouter>
      
    </div>
  );
}

export default App;
```

> You **MUST** put `<Navbar />` inside `<BrowserRouter>` because it contains `Link` components - which must be placed inside `<BrowserRouter>`<br>
> This is because the `Link` component uses the Router's context to navigate different routes when clicked<br>
> If you placed it outside, it wouldn't know how to perform the navigation and will throw an error

