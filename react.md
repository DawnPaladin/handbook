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
const MyComponent = function() { // Custom components get their names capitalized
	return {
		<div className='nameOfTheClass' />
	}
}
```

## Full component

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
