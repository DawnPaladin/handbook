**React**

Custom components must have their names start with a capital letter.

`class` is a reserved word in JavaScript, so JSX uses `className`.

# Functional components

```jsx
// Cat.jsx
import React, { useState, useEffect } from 'react';

export default function Cat(props) {
	const [name, setName] = useState(props.name);
	
	const formatName = inputName => {
		return inputName + "-kins";
	}
	
	useEffect(() => {
		// side effect that happens on mount
	});
	
	return <div className="cat">
		I'm a cat named {name}.
	</div>
}

Cat.propTypes = {
	name: PropTypes.string.isRequired;
}
```

```jsx
// App.jsx
import Cat from './Cat';

<Cat name="Fluffle">
```

# Class components

```jsx
// Cat.jsx
import React from 'react';

class Cat extends React.Component {
	static propTypes = {
		name: PropTypes.string.isRequired;
	}
	
	constructor(props) {
		super(props);
		this.state = { name: this.props.name };
	}

	render() {
		return <div className="cat">
			I'm a cat named {name}.
		</div>
	}
}
```

```jsx
// App.jsx
import Cat from './Cat';

<Cat name="Fluffle" />
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

# Props and propTypes

## Default props

```jsx
Cat.defaultProps = { name: "DefaultCat" };
```

### Setting prop types

PropTypes are the same as JavaScript types, except for `func` and `bool`.

To require an object with specific properties:
```jsx
Cat.propTypes = { options: PropTypes.shape({
	lolCat: PropTypes.bool,
	caption: PropTypes.string,
})}
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

# Redux

https://www.codecademy.com/learn/paths/learn-redux/tracks/learn-redux/modules/core-redux-api/cheatsheet

## Core Redux API
### Installing Redux

The `redux` package is added to a project by first installing it with `npm`.

Some of the resources imported from `redux` are:

-   `createStore`
-   `combineReducers`

```
npm install redux
```

### Create the Redux Store

The `createStore()` helper function creates and returns a Redux `store` object that holds and manages the complete state tree of your app. The only required argument is a reducer function, which is called every time an action is dispatched.

The `store` object returned has three key methods that ensure that all interactions with the application state are executed through the `store`:

-   `store.getState()`
-   `store.dispatch(action)`
-   `store.subscribe(listener)`

```js
const initialState = 0;
const countUpReducer = (
	state = initialState,
	action
) => {  
	switch (action.type) {
		case 'increment':
			return state += 1;
		default:
			return state;
}};

const store = createStore(countUpReducer);
```

### The `getState()` Method

The `getState()` method of a Redux `store` returns the current state tree of your application. It is equal to the last value returned by the `store`‘s reducer.

-   In the one-way data flow model (store → view → action → store), `getState` is the only way for the view to access the store’s state.
-   The state value returned by `getState()` should not be modified directly.

```js
const initialState = 0;
const countUpReducer = (
  state = initialState, 
  action
) => {
  switch (action.type) {
    case 'increment':
      return state += 1;
    default:
      return state;
}};
  
const store = createStore(countUpReducer);
   
console.log(store.getState());
// Output: 0
```

### The `dispatch()` Method

The `dispatch(action)` method of a Redux `store` is the only way to trigger a state change. It accepts a single argument, `action`, which must be an object with a `type` property describing the change to be made. The `action` object may also contain additional data to pass to the reducer, conventionally stored in a property called `payload`.

Upon receiving the `action` object via `dispatch()`, the store’s reducer function will be called with the current value of `getState()` and the `action` object.

```js
const initialState = 0;
const countUpReducer = (
  state = initialState, 
  action
) => {
  switch (action.type) {
    case 'increment':
      return state += 1;
    case 'incrementBy':
      return state += action.payload;
    default:
      return state;
}};
  
const store = createStore(countUpReducer);
 
store.dispatch({ type: 'increment' });
// state is now 1.
 
store.dispatch({ type: 'incrementBy'
                 payload: 3 });
// state is now 4.
```

### The `subscribe()` Method

The `subscribe(listener)` method of a Redux `store` adds a callback function to a list of callbacks maintained by the `store`. When the `store`‘s state changes, all of the _listener_ callbacks are executed. A function that unsubscribes the provided callback is returned from `subscribe(listener)`.

Often, `store.getState()` is called inside the subscribed callback to read the current state tree.

```js
const printCurrentState = () => {
  const state = store.getState()
  console.log(`state: ${state}`);
}
 
store.subscribe(printCurrentState);
```

### Action Creators

An _action creator_ is a function that returns an action, an object with a `type` property and an optional `payload` property. They help ensure consistency and readability when supplying an action object to `store.dispatch()`, particularly when a `payload` is included.

```js
// Creates an action with no payload.
const clearTodos = () => {
  return { type: 'clearTodos' };
}
store.dispatch(clearTodos());
 
// Creates an action with a payload.
const addTodo = todo => {
  return { 
    type: 'addTodo',
    payload: {
      text: todo
      completed: false
    }
  }
};
store.dispatch(addTodo('Sleep'));
```

### Slices

A _slice_ is the portion of Redux code that relates to a specific set of data and actions within the `store`‘s state.

A _slice reducer_ is the reducer responsible for handling actions and updating the data for a given slice. This allows for smaller reducer functions that focus on a slice of state.

Often, the actions and reducers that correspond to the same slice of the state are grouped together into a single file.

```js
/* 
This state has two slices:
1) state.todos
2) state.filter
*/
const state = {
  todos: [
    { 
      text: 'Learn React',
      completed: true
    },
    { 
      text: 'Learn Redux',
      completed: false
    },
  ],
  filter: 'SHOW_COMPLETED'
}
 
/*
This slice reducer handles only
the state.todos slice of state.
*/
const initialTodosState = [];
const todosReducers = (
  state=initialTodosState,
  action
) => {
  switch (action.type) {
    case 'todos/clearTodos':
      return [];
    case 'todos/addTodo':
      return [...state, action.payload];
    default:
      return state;
}};
```

### The `combineReducers()` Function

The `combineReducers()` helper function accepts an object of slice reducers and returns a single “root” reducer. The keys of the input object become the names of the slices of the `state` and the values are the associated slice reducers.

The returned root reducer can be used to create the `store` and, when executed, delegates actions and the appropriate slices of state to the slice reducers and then recombines their results into the next `state` object.

```js
const rootReducer = combineReducers({
  todos: todosReducer, 
  filter: filterReducer
})
```

### Introduction To Redux

A React application can share multiple points of data across components. In many cases managing the data shared can become a complex task.

Redux is a library for managing and updating application state. It provides a centralized “store” for state that is shared across your entire application, with rules ensuring that the state can only be updated in a predictable fashion using events called “actions”.

Redux works well with applications that have a large amount of global `state` that is accessed by many of the application’s components. The goal of Redux is to provide scaleable and predictable state management.

### Store

In Redux, a _store_ is a container that holds and manages your application’s global state.

The `store` is the center of every Redux application. It has the ability to update the global state and subscribes elements of an application’s UI to changes in the state. Accessing the state should never be done directly and is achieved through functions provided by the `store`.

### Actions

In Redux, an _action_ is a plain JavaScript object that represents an intention to change the store’s state. Action objects must have a `type` property with a user-defined string value that describes the action being taken.

Optional properties can be added to the action object. One common property added is conventionally called `payload`, which is used to supply data necessary to perform the desired action.

```js
/*
Basic action object for a shopping list
that removes all items from the list
*/
const clearItems = {
  type: 'shopping/clear'
}
 
/*
Action object for a shopping list
that adds an item to the list
*/
const addItem = {
  type: 'shopping/addItem',
  payload: 'Chocolate Cake'
}
```

### Reducers

A _reducer_ (also called a _reducing function_) is a plain JavaScript function that accepts the store’s current `state` and an `action` and returns the new `state`.

Reducers calculate the new `state` based on the action it receives. Reducers are the only way the store’s current `state` can be changed within a Redux application. They are an important part of Redux’s one-way data flow model.

```js
/*
 A reducer function that handles 2 actions
 or returns the current state as a default
*/
 
const shoppingReducer = (
  state = [],
  action
) => {
  switch (action.type) {
    case "shopping/clear":
      return [];
    case "shopping/addItem":
      return [
        ...state, 
        action.payload];
    default:
      /* 
      If the reducer doesn't care 
      about this action type, return 
      the existing state unchanged
      */
      return state;
  }
}
```

## Connect to React with React Redux
### react-redux Package

The `react-redux` package, the official Redux-UI binding package for React, lets your React components interact with a Redux store without writing the interaction logic yourself. This allows an application to rely on Redux to manage the global state and React to render the UI based on the state.

Interactions may include reading data from a Redux store and dispatching actions to the store.

### Install react-redux

The `react-redux` package is added to a React/Redux project by first installing it with `npm`.

A few of the resources imported from `react-redux` are:

-   `Provider`
-   `useSelector`
-   `useDispatch`

```
npm install react-redux
```

### The Provider Component

The `<Provider />` component makes the Redux store available to a nested child component. The store should be passed in as the `store` prop.

All child components of the `<Provider />` component can now use the resources provided by `react-redux` to access the Redux store including retrieving data and dispatching actions.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
 
import { App } from './App';
import { createStore } from 'redux';
 
const store = createStore();
 
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

### The useSelector() Hook

The `useSelector()` hook extracts state data from the Redux store using a selector function each time the state is updated. Any component nested within `<Provider />` may access state using `useSelector()`. Selectors used with `useSelector()` can be pre-defined functions or inline selectors.

When called within a React component `useSelector(selector)` accomplishes two things:

-   Returns the data retrieved by the selector
-   Subscribes the React component to changes in the store and forces a re-render if the selector’s result changes

```js
import React from 'react';
import { useSelector } from 'react-redux';
 
// Pre-defined selector function
const selectPosts = state => state.posts;
 
const App = (props) => {
  const posts = useSelector(selectPosts);
  
  // Alternatively, selectors can be used inline
  // const posts = useSelector(state => state.posts);
 
  // code to render posts is omitted...
};
```

### The useDispatch() Hook

The `useDispatch()` hook returns a reference to the `store.dispatch()` method. It must be used within a React component that is nested within the `<Provider />` component.

By convention, a React component defines `dispatch` and assigns the reference returned by `useDispatch()`. `dispatch()` can then be used within the component to dispatch action objects.

```js
import React from 'react';
import { useDispatch } from 'react-redux';
 
const MyComponent = () => {
  const dispatch = useDispatch();
 
  return (
    <button 
        onClick={() => dispatch(
            { type: 'buttonClicked' }
    )} >
      Click Me
    </button>
  );
};
```

### Selectors

In Redux, selectors are user-defined functions that extract specific pieces of information from a store state value. Selectors are pure functions that take the `state` as an argument and React components can use selectors to get specific data from the store.

By convention selector function names start with `select` and describe the data they retrieve from the store.

```js
/*
state = {
    todos: [
    {id: 1, content: 'Work'},
    {id: 2, content: 'Shop'},
    {id: 3, content: 'Sleep'}
  ]
}
*/
 
// This selector retrieves the todos array.
const selectTodos = state => state.todos;
 
 
// This selector retrieves an array of todo ids.
const selectTodoIDs = state => state.todos
  .map(todo => todo.id);
```

## Refactoring with Redux Toolkit
### Redux Toolkit

Redux Toolkit, also known as the `@reduxjs/redux-toolkit` package, contains packages and functions that are essential for building a Redux app. Redux Toolkit simplifies most Redux tasks like setting up the store, creating reducers and performing immutable updates.

### Installing Redux Toolkit

The `@reduxjs/redux-toolkit` package is added to a project by first installing it with `npm`.

Some of the resources imported from `@reduxjs/redux-toolkit` are:

-   `createSlice`
-   `configureStore`

```
npm install @reduxjs/redux-toolkit 
```

### `createSlice()` Options Object

The `createSlice()` function is used to simplify and reduce the code needed when creating application slices. It takes an object of options as an argument. The options are:

-   `name`: the slice name used as the prefix of the generated `action.type` strings
-   `initialState`: the initial value for the state to be used by the reducer
-   `reducers`: an object of action names and their corresponding case reducers

```js
/*
The action.type strings created will be
'todos/clearTodos' and 'todos/addTodo'
*/
const options = {
  name: 'todos',
  initialState: [],
  reducers: {
    clearTodos: state => [],
    addTodo: (state, action) 
      => [...state, action.payload]
  }
}
const todosSlice = createSlice(options);
```

### “Mutable” Code with `createSlice()`

`createSlice()` lets you write immutable updates using “mutation-like” logic within the case reducers. This is because `createSlice()` uses the [Immer library](https://immerjs.github.io/immer/) internally to turn mutating code into immutable updates. This helps to avoid accidentally mutating the state, which is the most commonly made mistake when using Redux.

```js
/* 
addTodo uses the mutating push() method
*/
const todosSlice = createSlice({
  name: 'todos',
  initialState: [],
  reducers: {
    clearTodos: state => [],
    addTodo: (state, action) 
      => { state.push(action.payload) }
  }
});
```

### Slices with `createSlice()`

`createSlice()` returns an object containing a slice reducer (`todosSlice.reducer`) and corresponding auto-generated action creators (`todosSlice.actions`).

-   The slice reducer is generated from the case reducers provided by `options.reducers`.
-   The action creators are automatically generated and named for each case reducer. The `action.type` values they return are a combination of the slice name (`'todos'`) and the action name (`'addTodo'`) separated by a forward slash (`todos/addTodo`).

When creating slices in separate files it is recommended to export the action creators as named exports and the reducer as a default export.

```js
const todosSlice = createSlice({
  name: 'todos',
  initialState: [],
  reducers: {
    addTodo: (state, action) 
      => { state.push(action.payload) }
  }
});
 
/*
todosSlice = {
  name: "todos",
  reducer: (state, action) => newState,
  actions: {
    addTodo: (payload) => ({type: "todos/addTodo", payload})
  },
  caseReducers: {
    addTodo: (state, action) => newState
  }
}
*/
 
export const { addTodo } = todosSlice.actions;
export default todosSlice.reducer;
```

### Create `store` with `configureStore()`

`configureStore()` accepts a single configuration object parameter. The input object should have a `reducer` property that is assigned a function to be used as the root reducer, or an object of slice reducers which will be combined to create a root reducer. When `reducer` is an object `configureStore()` will create a root reducer using Redux’s `combineReducers()`.

```js
import todosReducer from '.todos/todosSlice';
import filterReducer from '.filter/filterSlice';
 
const store = configureStore({
  reducer: {
    todos: todosReducer, 
    filter: filterReducer
  }
});
```

## Async Actions with Middleware and Thunks
### Thunks

A _thunk_ is a function used to delay a computation until it is needed by an application. The term thunk comes from a play on the word “think” but in the past tense.

In JavaScript, functions are thunks since they hold a computation and they can be executed at any time or passed to another function to be executed at any time.

A common practice is for thunks to be returned by a higher-order function. The returned thunk contains the process that is to be delayed until needed.

```js
const alarmOne = () => {
  console.log("Wake Up!!!");
};
alarmOne(); // "Wake Up!!!"
 
const getAlarmThunk = () => {
    return () => {
    console.log("Wake Up!!!");
  } 
};
const alarmTwo = getAlarmThunk();
alarmTwo(); // "Wake Up!!!"
```

### Thunks in Redux

In Redux thunks can be used to hold asynchronous logic that interacts with the Redux store. When thunks are dispatched to the store the enclosed asynchronous computations are evaluated before making it to the Redux store. The arguments passed to thunks are the Redux store methods `dispatch` and `getState`. This allows actions to be dispatched or for the state to be referenced within the containing logic.

Other benefits of thunks are:

-   Creating abstract logic that can interact with any Redux store
-   Move complex logic out of components

```js
import { fetchTodos } from '../actions';
 
const fetchTodosThunk = (
  dispatch, 
  getState
) => {
  setTimeout(
    dispatch(fetchTodos()), 
    5000);
};
```

### Middleware In Redux

Redux _middleware_ extends the store’s abilities and lets you write asynchronous logic that can interact with the store. Middleware is added to the store either through `createStore()` or `configureStore()`.

The `redux-thunk` package is a popular tool when using middleware in a Redux application.

### Logger
```js
import logger from 'redux-logger';

export default configureStore({
	reducer: {
		tasks
	},
middleware: (getDefaultMiddleware) => getDefaultMiddleware().concat(logger),
})
```

### Redux Thunk Middleware

The `redux-thunk` middleware package allows you to write action creators that return a function instead of an action. The thunk can be used to delay the dispatch of an action or to dispatch only if a certain condition is met.

```js
import { fetchTodos } from '../actions';
 
const fetchTodosLater = () => {
  return (dispatch, getState) => {
    setTimeout(
    dispatch(fetchTodos()), 
    5000);
    }
};
/*
redux-thunk allows the returned 
thunk to be dispatched
*/
store.dispatch(fetchTodosLater());
```

### The `redux-thunk` Package

The `redux-thunk` package is included in the Redux Toolkit (`@reduxjs/redux-toolkit`) package. To install `@reduxjs/redux-toolkit` or the standalone `redux-thunk` package use `npm`.

The `redux-thunk` middleware allows for asynchronous logic when interacting with the Redux store.

```
npm install @reduxjs/redux-toolkit

npm install redux-thunk
```

### `createAsyncThunk()

`createAsyncThunk()` accepts a Redux action type string and a callback function that should return a promise. It generates promise lifecycle action types based on the action type prefix that you pass in, and returns a thunk action creator that will run the promise callback and dispatch the lifecycle actions based on the returned promise.

The callback function takes a user-defined data argument and a thunkAPI object argument. The data argument is originally sent as an argument to the thunk action creator where an object can be used if multiple points of data are necessary. The thunkAPI object contains the usual thunk arguments such as dispatch and getState.

```js
import { createAsyncThunk } from '@reduxjs/toolkit'
import { userAPI } from './userAPI'
 
const fetchUser = createAsyncThunk(
  'users/fetchByIdStatus',
  async (user, thunkAPI) => {
    const response = await userAPI.fetchById(user.id)
    return response.data
  }
)
 
const user = {username: "coder123", id: 3};
store.dispatch(fetchUser(user))
```

### `extraReducers` Property

The object passed to `createSlice()` may contain a fourth property, `extraReducers`, which allows `createSlice()` to respond to other action types besides the types it has generated. This is useful when handling asynchronous logic using thunks.

The logic within `extraReducers` that acts on the slice of `state` can safely use mutatable updates because it uses Immer internally.

```js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { client } from '../api';
 
const initialState = {
  todos: [],
  status: 'idle'
};
 
export const fetchTodos = createAsyncThunk('todos/fetchTodos', async () => {
  const response = await client.get('/todosApi/todos');
  return response.todos;
});
 
const todosSlice = createSlice({
  name: 'todos',
  initialState,
  reducers: {
    addTodo: (state, action) => {
      state.todos.push(action.payload);
    }
  },
  extraReducers: {
    [fetchTodos.pending]: (state, action) => {
      state.status = 'loading';
    },
    [fetchTodos.fulfilled]: (state, action) => {
      state.status = 'succeeded';
      state.todos = state.todos.concat(action.payload);
    }
  }
});  
```
