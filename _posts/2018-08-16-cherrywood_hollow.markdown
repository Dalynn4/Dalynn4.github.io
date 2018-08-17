---
layout: post
title:      "Cherrywood Hollow"
date:       2018-08-17 00:24:00 +0000
permalink:  cherrywood_hollow
---

### My React-Redux Portfolio Project



I decided to do my react-redux portfolio project in the style of a single-page-application based on similar web pages for video games. The game is based on the popular Werewolf party game, and one I hope to make one day!

Using React.js to code was actually a lot of fun. The code is very intuitive and flexible. I started with a create-react-app in my console, and react acutally has a webpage already running and ready for you if you are so inclined to start it.

Part one of the process was hooking up a Rails API to the project. I followed the tutorial posted by Learn on the page for the project(https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/). Once I followed those steps, I set about making compnents.

I started with my container compenents. I knew that I was going to get them hooked up to redux, but I wanted to make sure I could get them to connect to the API without redux, because if not, there would be no way I could manage to do it with redux. Once that was complete, I decided to hook up react-router to allow my app to have the appearance of multiple different pages.

One of the most interesting aspects of react-router is the NavLink function. This allows you to link to the routes you make with react-router within your app. I used this to make a menu bar that users can use to navigate through the entirety of my app. Here is the code:

```
const Menu = () => {
  return (
    <div>
      <div className="Menu">
        <NavLink
        to="/"
        exact
        style={link}
        activestyle={{
          background: 'black'
        }}
        >Home</NavLink>
        <NavLink
        to="/about"
        exact
        style={link}
        activestyle={{
          background: 'black'
        }}
        >About</NavLink>
        <NavLink
        to="/trailer"
        exact
        style={link}
        activestyle={{
          background: 'black'
        }}
        >Trailer</NavLink>
        <NavLink
        to="/reviews"
        exact
        style={link}
        activestyle={{
          background: 'black'
        }}
        >Reviews</NavLink>
      </div>
```

All it requires on this page is `import { NavLink } from 'react-router-dom'` at the top (assuming you have set up react-router on your main page). Like so:

```
ReactDOM.render((
<Provider store={store}>
  <Router>
    <App />
  </Router>
</Provider>),
document.getElementById('root'));
registerServiceWorker();

```

that is the main dom rendering for my app. The Router enables react-router. The Provider enables redux.

Setting up redux was actually more difficult than having it set up without, but will allow greater ease in any future changes or additional compnents that require access to state.

Once I had react-router and redux set up and working, I only needed to style the app. 

Presto! Complete!
