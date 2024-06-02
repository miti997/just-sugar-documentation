# Getting started with Just Sugar

## Just Sugar comes with a simple homepage already created, let's take a look at it.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
</head>
<script type="module" src="/just_sugar.js"></script>
<body id="APP"></body>
</html>
```
As you can see we load the ```just_sugar.js``` file from our root directoty and that is about it! Everything else is handled automatically.

You may notice that we have an id of ```APP``` on the body element. This is because the body will be used as the wrapper of the application. The wrapper is used as a fixed reference point for all the logic of the application. It is where our interface will be rendered.

## Let's take a look at the ```just_sugar.js``` file now.

```javascript
import APP from '/core/app.js'
import SugarCube from '/core/sugar_cube.js'
import View from '/core/view.js'
import Component from '/core/component.js'
import Layout from '/core/layout.js'
import Router from '/core/router.js'
import routes from '/src/config/routes.js'
window.__JUST_SUGAR__ = new APP(routes, {wrapperSelector: '#APP', devMode: true})
await __JUST_SUGAR__.init()
```

This file is where we load what we need for our application.

From this entire list we MUST import:
- APP
- Router
- routes

Everything else will be loaded at some point during the application's life cycle but keep in mind that it might be useful to preload components that are used often.

In order to start the application we first create a new instance of the APP class. We save this instance to the window so we can always access parts of it if necessary and then we call the init function.

The init function finds our wrapper and populates it with the appropiate content based on the matched route.

The second parameter sent to the APP class are settings. We tell the app what wrapper to look for and whether or not it should start in developement mode. As of yet the only difference between developement mode is how errors are handled but we will look at those later.

## Now we are ready to start the show!

We had a very quick look at how the application get's loaded so let's see it in action now.

Just sugar comes with a simple server that can be used for developement. This server routes everything to the ```index.html``` file except for request for files (css, js, jpg, etc).

In order to start the server you can run ```node server.js``` from the framework's root directory.

You can access the app by going to ```http://localhost:3000/```

And here we have it!

![Alt text](first_page.png)

## What's next?

In the [next](ViewCreation.md) step of the tutorial we will create a view.

