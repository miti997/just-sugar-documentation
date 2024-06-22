# Components

Components in Just Sugar represent pieces of code that exist within views, layout or other components and can be reused throughout an application. They can also be used to keep the view lighter by abstracting some logic or even some templating.

In order to create a component you need to make a `.js` file in `/src/components`. The name of this file can be anything you want though it is recommended it matches the name of the component and it's purpose. For example Just Sugar comes with the `ImgComponent`, `LinkComponent` and `SpaLinkComponent` classes that are located in `img.js`, `link.js` and `spa_link.js`.

After the file is created you need to export a class, with any name you choose, that extends the `Component` class.

Components can be loaded in everywhere using the `loadComponent` method. This method takes a class as it's first parameter then an array of additional parameters that will be used when creating a new instance of the component. The `template()` method of the class is then called.

Keep in mind that if you don't want to use this default behavior you can simply instantiate the class and then call whatever method you want.
```javascript
let myComponent = new MyComponentComponent();
myComponent.renderFirstPart();
//some code here
myComponent.renderLastPart();
```
## Example
In this example we will use the previously created `UsersIndex` class. To be more exact we are going to change the table we have so it uses components thus allowing us to make the view a bit lighter.

Let's start by creating a new file in `/src/components` called `table_row.js` in which we will create a class called `TableRowComponent` that extends to `Component` class.

```javascript
import Component from "../../core/component.js";

export default class TableRowComponent extends Component {
    
}
```
Next let's add the a constructor. This constructor will take as a parameter an array. This array will represent each cell of the row. We are also going to set this parameter as a property of our class. Additionally we will add another parameter that will be used to determine if we want to add a table head or a table cell.
```javascript
import Component from "../../core/component.js";

export default class TableRowComponent extends Component {
    data;
    head;
    constructor(data, head = false) {
        super();
        this.data = data;
        this.head = head;
    }
}
```
Now that our data get's passed let's add the `template()` method.
```javascript
     template() {
        let cell = this.if(this.head, 'th', 'td');
        return /*html*/`
            <tr>
                ${this.for(this.data, (data) => {
					return /*html*/`<${cell}>${data}</${cell}>`;
				})}
            </tr>
        `
    }
```
The template function is quite simple. We make a new row and we add cells based on the data sent. We check whether or not we add a head and set the cell type appropriately.

Now the last thing we need to do is make use of this component in our `UsersIndex` class.

First import the component: 
```javascript
import TableRow from '/src/components/table_row.js'
```
Then we need to actually use the component in our `template()` method. We will first add the head of the table:
```javascript
template() {
		return /*html*/`
			<table>
				${this.loadComponent(TableRow, ['Id', 'User Name', 'Role', 'Email', 'Registered at','Actions'], true)}
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
							<td><button ${this.on('click', 'deleteUser', [user.id])}>Delete</button></td>
						</tr>
					`;
				})}
			</table>
		`;
	}
```
And finally let's change the actual content of the table:
```javascript
template() {
		return /*html*/`
			<table>
				${this.loadComponent(TableRow, ['Id', 'User Name', 'Role', 'Email', 'Registered at','Actions'], true)}
				${this.for(this.users, (user) => {
					return this.loadComponent(TableRow, [
						user.id,
						user.userName,
						this.if(user.roleId == 1, 'Admin', 'User'),
						user.email, user.registrationDate,
						`<button ${this.on('click', 'deleteUser', [user.id])}>Delete</button>`
					])
				})}
			</table>
		`;
	}
```

And that's it! We created a component and we made our view a bit lighter. The main advantage of this however is that now we will be able to use this component all throughout our app.