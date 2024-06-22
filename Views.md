# Views

Views are the main part of Just Sugar. They make up the GUI of your interface. They hold content, components and styling.

In order to create a view in Just Sugar you first have to create  a `.js` file in `/src/views`. The name of the file can be whatever you want and it can be placed in any subfolder you want inside `/src/views` though it is recommended you try to match the route that will lead to this view.

If you use the built server in MPA mode you need to create a `.html` file and include Just Sugar in it.
## Example

In this example we will create a simple view, containing a list of users.

This example will not reflect reality as the data displayed in the view is just a mockup meant to simulate a real data set.

We will be using Just Sugar's routing system for this example but if you do not want to use it simply ignore the first part of this guide.

Let's get started!
### Routing

The first thing we need to do is create a route for out new view. When adding routes you can call them whatever you want but for this example we will keep things simple and call it `/users`.

Before we add the route let's try accessing this URL and see what happens. Go to `http://localhost:3000/users` and you will be met with an error page.

If you click on "Routes" you will see that we currently have only one route, the one for the first page.

If you click on "Error" you will be able to see the details of the error.

To fix this error you need to go to `/src/config/routes.js` and add the new route. To do so you will need to call the `addRoute()` function like so:

```javascript
router.addRoute('/users', '/users/index')
```

The first parameter is the URL we are trying to match while the second is the view we are trying to render. In the end your routes file should look like this:

```javascript
import Router from '/core/router.js'

const router = new Router();

router.addScope('/', 'default')
router.addRoute('/', 'home')
router.addRoute('/users', '/users/index')

export default router;
```
You will notice we have the `addScope()` function as well in our routes file. The `addScope()` function is used to determine what layout needs to be loaded based on the matched URL. For example you might load a certain layout for `/` and a different layout for `/admin`. We will discuss layouts in mode detail at a later time. For now let's see how our users index looks like.

### Creating the view class
Let's refresh the page and see what happens

You will notice the error changed. This mean the route we added was matched but now Just Sugar can not find the appropriate view. In order to fix this error we need to create a view in ```/src/views/users/```. Create the users folder in views and then create an ```index.js``` file in it. In the newly created file add the following code:

```javascript
import View from '/core/view.js';

export default class UsersIndex extends View {
    title() {
        return 'Users'
    }

    template() {
        return /*html*/`
           We have a view
        `;
    }
}
```

Refresh the page and you should see things are now working!

A few things to note here:
1. All views extend the ```View``` class from ```/core/view.js```
2. Views can have a title function in order to change the title in the browser
3. You may add comments before the template's string in order to get syntax highlighting. In this case [Inline HTML](https://marketplace.visualstudio.com/items?itemName=pushqrdx.inline-html) was used.

### Adding content
In order to add some content we will create an object to simulate some a data retrieval from a database and/or API.

In our UsersIndex class we will create a new property called users:

```javascript
    users = [
        {
            id: 324,
            userName: 'user_name_1',
            email: 'user@name1.com',
            roleId: 1,
            registrationDate: '2024-01-01 00:00:00'
        },
        {
            id: 445,
            userName: 'user_name_2',
            email: 'user@name2.com',
            roleId: 2,
            registrationDate: '2024-02-01 00:00:00'
        },
        {
            id: 567,
            userName: 'user_name_3',
            email: 'user@name3.com',
            roleId: 2,
            registrationDate: '2024-01-23 00:00:00'
        },
    ];
```

Now in our ```template()``` function we will create a table and itterate over the array of users adding a row for each one. This will be done using the built in ```for()``` function. This function allows us to go over a set of data and add HTML for each record. Here is how your template function should look at the end:

```javascript
    template() {
        return /*html*/`
            <table>
                <tr>
                    <th>Id</th>
                    <th>User Name</th>
                    <th>Role</th>
                    <th>Email</th>
                    <th>Registered at</th>
                    <th>Actions</th>
                </tr>
                ${this.for(this.users, (user) => {
                    return /*html*/`
                        <tr>
                            <td>${user.id}</td>
                            <td>${user.userName}</td>
                            <td>${user.roleId}</td>
                            <td>${user.email}</td>
                            <td>${user.registrationDate}</td>
                            <td></td>
                        </tr>
                    `;
                })}
            </table>
        `;
    }
```
The ```for()``` action takes as arguments an itterable value and a callback function. The results of the callback function are appended to eachother in a loop and then returned.

Let's make some changes to display either "Admin" or "User" on the role column. Id 1 will be "Admin" while id 2 will be "User".

In order to do this we can use the built in ```if()``` function like so:

```javascript
    template() {
        return /*html*/`
            <table>
                <tr>
                    <th>Id</th>
                    <th>User Name</th>
                    <th>Role</th>
                    <th>Email</th>
                    <th>Registered at</th>
                    <th>Actions</th>
                </tr>
                ${this.for(this.users, (user) => {
                    return /*html*/`
                        <tr>
                            <td>${user.id}</td>
                            <td>${user.userName}</td>
                            <td>
                                ${this.if(user.roleId == 1, 'Admin', 'User')}
                            </td>
                            <td>${user.email}</td>
                            <td>${user.registrationDate}</td>
                            <td></td>
                        </tr>
                    `;
                })}
            </table>
        `;
    }
```

The ```if()``` function accepts 3 parameters. The first one is the condition to be checked, the second one the string to return in case the condition is true and, optionally a third parameter that is returned in case the condition is not true.

The ```for()``` and ```if()``` functions are basic yet powerful tools that you can use to quickly and easily create templates

### Styling the view
Now that we have something to look at let's style the table a little.

In Just Sugar you have two main ways of adding styling to your templates. You can either load a CSS file using the ```loadStyle()``` function or you can make view/component/layout specific styles by creating a ```style()``` function in your class. It is recommended that you use ```loadStyle()``` in layouts since they wrap multiple views. This way they will apply the style in a more general manner. The ```style()``` function is best used to create component or view specific styling.

In this example we will use the ```style()``` function. You can add this function anywhere in your class like this:

```javascript
style() {
    return /*css*/`
        table {
            margin-top: 30px;
            border-collapse: collapse;
        }
        table, th, td {
            padding: 10px;
            border: 1px solid var(--black);
        }
    `
}
```

This will only be applied to the current view. This is done using CSS Nesting. The identifier of the view (an id) is used as the parent selector and all other selectors are added under it.

You will notice the ```--black``` variable being used. This comes from ```/resources/css/just_sugar.css```. This style sheet is loaded in ```/src/views/layouts/default.js``` and as such it is applied to all children of the layout including this view.

In case you do not want to rely on CSS Nesting you can turn it off by setting `nestedCSS` to false in your class like so:
```javascript
    nestedCSS = false;
```
You will then have to change the style above to include the ID of the class:

```javascript
style() {
    return /*css*/`
        #${this.id} table {
            margin-top: 30px;
            border-collapse: collapse;
        }
        #${this.id} table, th, td {
            padding: 10px;
            border: 1px solid var(--black);
        }
    `
}
```

### Adding functionality

Now that we have the looks figured out let's add some functionality to this view. We will add a delete button that removes a user from the users array of the view. In reality this would probably trigger an ajax call as well but we will not go so in depth.

In order to trigger the delete action we will use the ```on()``` helper function like so:

```javascript
`<td><button ${this.on('click', 'deleteUser', [user.id])}>Delete</button></td>`
```

The ```on()``` function takes 3 parameters. The first one is the event to listen for. In this case the event is on click. The second parameter is the function to be called once the event is triggered. The function MUST be in the class you added the event listener to. Finally the parameters array determines what will be sent to the callback function. This parameter is optional.

The last thing we need to do is create the ```deleteUser()``` function in the ```UsersIndex``` class:

```javascript
deleteUser(userId) {
    for (let index = 0; index < this.users.length; index++) {
        if (this.users[index].id == userId) {
            this.users.splice(index, 1);
            this.users = this.users;
            return;
        }
    }
}
```

And there we have it!

A functional view created entirely with Just Sugar!

It's important to note the fact we did not directly modify the DOM in our ```deleteUser``` function. What we did is change the users property of the class. In Just Sugar each time a property of a class is updated the class is re-rendered. This way you do not have to worry about selecting elements and changing them. All you have to do is change the property that is used for said element

Next we will discuss [Layouts](Layouts)