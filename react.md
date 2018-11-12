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
