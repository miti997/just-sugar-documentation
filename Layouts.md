Layouts in just sugar are used to hold common parts between multiple views and to load the views themselves. You can load components in them if you want those components to appear on multiple views for example a header or a footer.

You can also add events to layouts using the `on()` function though it is recommended you keep events in components and views.

After a route is matched the first thing that gets loaded is the layout. The layout is loaded based on the scope that was matched from the `/src/config/routes.js` file. For example here we load the default layout if the `/` scope is matched.
```javascript
import Router from '/core/router.js'

const router = new Router();

router.addScope('/', 'default')
router.addRoute('/', 'home')
router.addRoute('/users', '/users/index')

export default router;
```
You can add other scopes as well by using the `addScope()` function. Each scope lasts until another one is added. For example here the `/` scope lasts until the `/users` scope is added using the `addScope()` method.

```javascript
import Router from '/core/router.js'

const router = new Router();

router.addScope('/', 'default')
router.addRoute('/', 'home')

router.addScope('/users')
router.addRoute('/', 'users/index')

export default router;
```

After the layout is loaded it's `render()` method is called in which the view is loaded using the `loadView()` method from the parent class `Layout` found in `/core/layout.js`.
## Example

For this example we will make use of the view we created for the [views](Views) part of this documentation.

We will create a new scope for `/users` and we will load a layout specific to this section of our app.
### Linking to a page

The first thing we will do is take a look at the layout that is loaded on the home page of our app. Locate the `default.js` file in `/src/layouts` and open it up.

```javascript
import Layout from "/core/layout.js";
import Header from "/src/components/default/header.js";

export default class DefaultLayout extends Layout {
    async render(viewName) {
        return /*html*/ `
            ${this.loadStyle('just_sugar')}
            ${this.loadComponent(Header)}
            ${await this.loadView(viewName)}
        `;
    }
}
```

As you can see the layout is pretty bear bones. We load the `just_sugar.css` file using the `loadStyle()` method. This method loads style sheets from `/resources/css`. We load the style sheet in this layout because we want it to be applied to all views that are loaded into this layout.

The `loadView()` method is the most important part of layouts. It is used to find the module for the view, initialize the class and render it.

All layouts **MUST** extend the `Layout` class.

Finally we load the `Header` component. This component is where we will add out link to the users index page. Open the `header.js` file and modify it's `template()` method to look like this:
```javascript
    template() {
        return /*html*/ `
            <nav>
                <div class="logo">
                    ${this.loadComponent(Img, 'just_sugar_logo.svg', {alt: 'Just sugar logo', width:40, height:40})}
                </div>
                <ul>
                    ${this.loadComponent(Link, 'https://github.com/miti997/just-sugar-documentation', 'Read the docs', {wrapperElement: 'li', target: '_blank'})}
                    ${this.loadComponent(Link, 'https://github.com/miti997/just-sugar', 'Check the project', {wrapperElement: 'li', target: '_blank'})}
                    ${this.loadComponent(SpaLink, '/users', 'Users', {wrapperElement: 'li'})}
                </ul>
            </nav>
        `;
    }
```
What we did is we added the `SpaLink` component at the end of the list. This should result in out new link appearing in the nav bar and it should take as to '/users' but without refreshing the browser.

Now let's try to make the logo image on the left a link to the home page of our app. We will also remove the existing links. In the end the `template()` method should look like this:

```javascript
    template() {
        return /*html*/ `
            <nav>
                <div class="logo">
                    ${this.loadComponent(
                        SpaLink,
                        '/',
                        this.loadComponent(Img, 'just_sugar_logo.svg', {alt: 'Just sugar logo', width:40, height:40}))}
                </div>
                <ul>
                    ${this.loadComponent(SpaLink, '/users', 'Users', {wrapperElement: 'li'})}
                </ul>
            </nav>
        `;
    }
```

Note that you will need to refresh your page each time you make changes in this component as it's loaded in the layout. In SPA mode the layout only changes if you refresh, you update it (via an event for example) or another one is matched.

### Adding a new scope

In order to apply a different layout for the users section of our app we first need to add a scope in `/src/config/routes.js`. Let's do that:
```javascript
import Router from '/core/router.js'

const router = new Router();

router.addScope('/', 'default')
router.addRoute('/', 'home')

router.addScope('/users', 'users')
router.addRoute('/', 'users/index')

export default router;
```
That is it! From now on when we access a URL that starts with /users we will load the `users` layout.

If you try to go to `/users` right now you will get an error telling you the layout could not be loaded. This is because we have not yet created our layout. In order to do this create a new file called `users.js` in your `/src/layouts` folder. The content of the file should look like this:
```javascript
import Layout from "/core/layout.js";
import Header from "/src/components/default/header.js";

export default class UsersLayout extends Layout {
    async render(viewName) {
        return /*html*/ `
            ${this.loadStyle('just_sugar')}
            ${this.loadComponent(Header)}
            ${await this.loadView(viewName)}
        `;
    }
}
```
You will notice it is identical to the previous layout - `default.js`. We need to do it like this if we want to have the same top navbar and style but in practice your layouts will most likely have very few things in common.

The `loadView()` method is always called in a layout, assuming you want to load a view in it.

### Changing the layout

Now that we have this second layout let's make it different from default. We will add a sidebar containing different actions for the users section of our app.

First let's load the `SpaLink` component. Add the import statement above the `UsersLayout` class
```javascript
import SpaLink from '/src/components/spa_link.js';
```
Next let's add a list for these actions. We will add a wrapper div so we can place the sidebar on the left. Change the `render()` method to:
```javascript
 async render(viewName) {
        return /*html*/ `
            ${this.loadStyle('just_sugar')}
            ${this.loadComponent(Header)}
            <div class="container">
                <ul class="sidebar">
                    <li>${this.loadComponent(SpaLink, '/users', 'All Users', )}</li>
                    <li>${this.loadComponent(SpaLink, '/users/add', 'Add a New User', )}</li>
                    <li>${this.loadComponent(SpaLink, '/users/roles', 'Manage Roles', )}</li>
                </ul>
                ${await this.loadView(viewName)}
            </div>
        `;
    }
```

These links will result in errors for now but it's not really the focus of this tutorial. We only want to have a different layout for now.

Next we will style the list a little bit using the style function:
```javascript
 style() {
        return /*css*/`
            .container {
                display: flex;
                height: 100vh;
            }
            .sidebar {
                bottom: 0;
                padding: 0;
                margin: 0;
                padding-right: 20px;
                background-color: var(--blue);
                color: var(--beige);
            }
            .sidebar li {
                list-style: none;
                margin: 10px;
            }
        `;
    }
```

And there we have it we create a new, albeit very basic layout.

Something to note here is that since we load the `just_sugar.css` file via `loadStyle()` AND we use the `style()` function there might be some conflicts between the two styles. If this were a component it wouldn't be an issue since the style is scoped to that component but because both styles are loaded in the same scope - the scope of this layout they might interfere with each other.

Feel free to style this further and to create the other views as well.

Next we will discuss [Components](Components.md)