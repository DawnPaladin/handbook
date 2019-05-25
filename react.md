**React**

# Components

Custom components must have their names start with a capital letter.

`class` is a reserved word in JavaScript, so JSX uses `className`.

## Stateless functional component

```jsx
const MyComponent = function() {
	return {
		<div className='nameOfTheClass' />
	}
}
```

Or, more simply:
```jsx
const MyComponent = props => (<div>Name: {props.name}</div>);

<MyComponent name="Joe" />
```


## Stateless component

```jsx
class Kitten extends React.Component {
	constructor(props) {
		super(props);
	}

	render() {
		return (
			<h1>Hi</h1>
		);
	}
}

<Kitten />
```

## Splitting between files

```js
// MyComponent.jsx
import React from 'react';

export default class MyComponent extends React.Component {
	// ...
}
```
```js
// App.jsx
import MyComponent from './MyComponent';
```

## Props

Passing a prop (property) into a component:
```jsx
<Cat type="kitten" />

const Cat = (props) => <div>This cat is a {props.type}</div>
```

### Default props

```jsx
Cat.defaultProps = { name: "DefaultCat" };
```

### Setting prop types

```jsx
import PropTypes from 'prop-types';

class Cat extends React.Component {
	static propTypes = {
		name: PropTypes.string;
	}
}
```

PropTypes are the same as JavaScript types, except for `func` and `bool`.

To make the prop **required:**
```jsx
Cat.propTypes = { name: PropTypes.string.isRequired };
```

To require an object with specific properties:
```jsx
Cat.propTypes = { options: PropTypes.shape({
	lolCat: PropTypes.bool,
	caption: PropTypes.string,
})}
```

## State

### Declaring state

```jsx
class Cat extends React.Component {
	constructor(props) {
		super(props);
		this.state = { name: "Fluffle" };
	}
	render() {
		return (
	      <p>I am a cat named {this.state.name}</p>
		)
	}
}
```

### Updating state

```jsx
this.setState({ name: "Grumpy Cat" });
```

## Methods

Methods often need access to `this`, which can be provided by `bind`ing it in the constructor.

```jsx
class Cat extends React.component {
	constructor(props) {
		super(props);
		this.state = { foodLevel: 0 };
		this.eat = this.eat.bind(this);
	}
	eat() {
		this.setState({
			foodLevel: this.state.foodLevel + 1;
		});
	}
	render() {
		return (
			<div>
				<button onClick={this.eat}>Eat</button>
				<p>Food level: {this.state.foodLevel}</p>
			</div>
		);
	}
}
```

The experimental public class fields syntax provides access to `this` without a `bind` statement:

```jsx
	eat = () => {
		this.setState({
			foodLevel: this.state.foodLevel + 1;
		});
	}
```

See the [documentation](https://reactjs.org/docs/handling-events.html) for more information.

## Controlled Inputs

You can set up components that have two-way data binding between the DOM state and the React component's state.

```jsx
class Cat extends React.component {
	constructor(props) {
		super(props);
		this.state = { thoughts: '' };
		this.handleChange = this.handleChange.bind(this);
	}
	handleChange(event) {
		this.setState({ input: event.target.value });
	}
	render() {
		return (
			<div>
				<label>Enter thoughts <input value={this.state.thoughts} onChange={this.handleChange} /> </label>
				<p>Thoughts: {this.state.thoughts}</p>
			</div>
		);
	}
}
```

This can be done with whole `<form>` elements.

```jsx
<form onSubmit={this.handleSubmit}>
	<button type='submit'>Submit</button>
</form>
```
Don't forget to include an `event.preventDefault()` statement in the submit handler; actually submitting the form would cause a page refresh.

# Styling

```jsx
<div style={{
	color: 'red',
	height: 100, // defaults to px
	width: "50%" // but you can specify other units
}}>
```

# Conditional rendering

## && operator

You can perform traditional JavaScript logic to determine what JSX a `render()` function should return. This can be made more concise using the `&&` logical operator:

```jsx
return (
	<p>Always display</p>
	{ this.state.displaySecondPara && <p>Sometimes display</p> }
)
```

If the condition is false, the logic will short-circuit out the JSX.

## Ternary operator

```jsx
render() {
	const JSXifTrue = <div/>
	const JSXifFalse = <p/>
	return (
		{ condition ? JSXifTrue : JSXifFalse }
	);
}
```

# React with Rails

Use this [tutorial](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/) to set up a Rails and a React server that will work together.

Then, if your `config/routes.rb` says `resources :cats`, and you want the list of cats:

```js
// client/src/Client.js
function index(callback) {
	return fetch('cats', { // this string expands to "localhost:3001/cats"
		accept: "application/json"
	})
	.then(checkStatus)
	.then(parseJSON)
	.then(callback);
}

function checkStatus(response) {
	if (response.status >= 200 && response.status < 300) {
		return response;
	}
	const error = new Error(`HTTP Error ${response.statusText}`);
	error.status = response.statusText;
	error.response = response;
	console.log(error); // eslint-disable-line no-console
	throw error;
}

function parseJSON(response) {
	return response.json();
}

const Client = { index };
export default Client;
```

```jsx
// client/src/App.js
import Client from "./Client";

class App extends Component {
	constructor(props) {
		super(props);
		this.state = {
			cats: []
		};
		Client.index(cats => this.setState({ cats: cats }))
	}
	...
}

export default App;
```

# Miscellaneous

## List keys

When rendering a list, React needs to give each item in the list a unique key so that when the list changes, it can keep track of which items have been added & removed. This key just needs to be unique within the list, not within the whole app.

If you have a unique key for the item, use that. If you must, use the array index. If you don't assign a key, React will use the list index, and this may lead to problems. [More info about list keys](https://reactjs.org/docs/lists-and-keys.html)

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
	<li key={number.toString()}>
		{number}
	</li>
);

const todoItems = todos.map((todo) =>
	<li key={todo.id}>
		{todo.text}
	</li>
);

const todoItems = todos.map((todo, index) =>
	// Only do this if items have no stable IDs
	<li key={index}>
		{todo.text}
	</li>
);
```
