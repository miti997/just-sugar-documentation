## The built in server.

Just Sugar comes with a built in node server. This server should only be used for development.

You can run the server either in SPA mode or MPA mode.

To run the server in SPA mode you need to execute the following command:

```bash
node server.js SPA
```

To run the server in MPA mode you need to execute the following command:

```bash
node server.js MPA
```

Note that MPA mode requires you to create a HTML file in the root directory of Just Sugar.

After running one of the above commands you can access the server by typing `localhost:3000` in your browser's address bar.
## Loading Just Sugar into HTML

To use Just Sugar you need to load the app from the core folder and then create a new instance of the `App` class.

```html
<script type="module">
	import APP from "/core/app.js";
	new APP(null, {wrapperSelector: '#APP', devMode: true, layout: "default", view: "home"})
</script>
```

As you can see we import the app `App` class from `/core/app.js`.

In this example we do not take advantage of the routing system. To do so we first need to load it, then pass it to the new instance of out app.

```html
<script type="module">
	import APP from "/core/app.js";
	import routes from "/src/config/routes.js"
	new APP(routes, {wrapperSelector: '#APP', devMode: true, layout: "default", view: "home"})
</script>
```
## File structure

Just sugar comes with a pretty simple directory structure

```
root
	core
		app.js
		component.js
		error.js
		layout.js
		router.js
		sugar_cube.js
		view.js
	resources
		css
			just_sugar.css
		img
			just_sugar_logo.svg
			just_sugar.svg
	src
		components
			default
				error_details.js
				header.js
			img.js
			link.js
			spa_link.js
		config
			routes.js
		layouts
			errors
				error.js
			default.js
		views
			errors
				component_not_loaded.js
				generic_not_found.js
				layout_not_loaded.js
				no_matched_route.js
				view_not_loaded.js
			home.js
	index.html
```

This might seem like a lot of files however many of them you will not work with unless you want to change some fundamental things about Just Sugar.

Let's have a look at each folder and what it holds. The files will be later explained so we will skip them for now.
### core

This directory holds all the things that make Just Sugar work. Here you will find the app, parent classes for all your components, views, layouts and errors as well as the router and a special file called `sugar_cube.js`.

In Just Sugar the `SugarCube` class represents the root of all other components, views and layouts. Every single parent class in the core folder extends the `SugarCube` class. This class provides helper functions for everything else and it is used to load and update everything throughout the app.

### resources

This folder is used to hold styles and images that you can load all throughout you Just Sugar app.

### config

The config folder is where you can set up all your routes for your application. You can also create general configuration files that can be loaded in other places throughout your app. An example of configurations would be two settings for your app. One for development and one for production.
### src

The src directory is where you will write most of your code. Here is where you will create your layouts, views, components and errors.
