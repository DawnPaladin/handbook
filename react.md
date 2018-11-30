**React**

# Basics

```jsx
const jsx = <h1>Hello, world!</h1>
ReactDOM.render(jsx, document.getElementById('root'));
{/* this is what comments look like */}
```

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
Cat.propTypes = { name: PropTypes.string };
```

PropTypes are the same as JavaScript types, except for `func` and `bool`.

To make the prop **required:**
```jsx
Cat.propTypes = { name: PropTypes.string.isRequired };
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

Methods typically need access to `this`, which is provided by `bind`ing it in the constructor.

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
