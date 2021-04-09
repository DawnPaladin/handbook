**JavaScript**

# Flow Control

## Switches

```js
// A standard `switch`  (out of favor)
switch ( foo ) {
  case "bar":
    alert( "the value was bar -- yay!" );
    break;
  case "baz":
    alert( "boo baz :(" );
    break;
  default:
    alert( "everything else is just ok" );
}

// Using an object instead
var stuffToDo = {
  "bar": function() {
    alert( "the value was bar -- yay!" );
  },
  "baz": function() {
    alert( "boo baz :(" );
  },
  "default": function() {
    alert( "everything else is just ok" );
  }
};

// Check if the property exists in the object.
if ( stuffToDo[ "foo" ] ) {
  // This code will NOT run because there is no `foo` property for the `stuffToDo` object
  stuffToDo[ "foo" ]();
} else {
  // This code will run because there is a `default` property set up in the `stuffToDo` object and it contains a function to run.
  stuffToDo[ "default" ]();
}
```

## Looping

```js
for ( var i = 0 ; i < 5 ; i++ ) {
  console.log("in the " + i + " iteration");
}
```
```js
var i = 0;
while ( i < 100 ){
  // do stuff
  i++;
}
```
```js
do {
  // will execute at least once
} while ( false );
```
Break a loop with `break` or jump to the next iteration with `continue`.

# Strings

All of these functions are nondestructive.

## split

Splits a string into arrays. Opposite of `join()`.

```js
"abc def".split(' '); // ["abc", "def"]
```

## substr

Returns characters from a string, starting at an index through a specified number of characters.

```js
str.substr(start[, length])

"Bird is the word".substr(0,4); // "Bird"
```

## slice

Extracts a section of a string and returns a new string.
```js
str.slice(beginSlice[, endSlice])

"123456".slice(2);    // "3456"
"123456".slice(2,4);  // "34"
"123456".slice(2,-1); // "345"
```

## substring

Same as `slice`, except the order of the arguments doesn't matter, and you can't use negatives.

```js
str.slice(beginSlice[, endSlice])

"123456".substring(2);    // "3456"
"123456".substring(2,4);  // "34"
"123456".substring(4,2);  // "34"
```

## toLowerCase, toUpperCase

```js
"abc".toUpperCase(); // "ABC"
"ABC".toLowerCase(); // "abc"
```

## trim

Removes whitespace from the beginning and end of a string.

```js
str.trim()
```


## indexOf

Find a string within another string. Returns the index at which the string was found, or -1 if it wasn't found.

```js
str.indexOf(searchValue[, fromIndex])

str.lastIndexOf(searchValue[, fromIndex]) // same, but searching from the end
```

## match

Retrieves regex matches. Returns an array containing the entire match result and any parentheses-captured matched results; `null` if there were no matches.

```js
str.match(regexp)
```

## search

Runs a regex search and returns the index at which a match was found.

```js
str.search(regexp)
```

## replace

Searches a string, using either a regex or a simple substring, and replaces, using a function or a simple substring.

```js
str.replace(regexp|substr, newSubStr|someFunction)
```

# Arrays

## Nondestructive manipulation

These return a new array rather than modifying the existing one.

```js
[1,2].concat([3,4]); // [1,2,3,4]
[1,2].join(' '); // "1 2"
[1,2,3,4,5,6].slice(2); // [3,4,5,6]
[1,2,3,4,5,6].slice(2,4); // [3,4]
[1,2,3,4,5,6].slice(2,-1); // [3,4,5]
```

## Destructive manipulation

These modify the original array rather than returning a new one.

### sort()

```js
[4,3,2,1].sort() // [1,2,3,4]
```
You can [pass this a function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) to define your own sorting behavior.

### splice()

Syntax: `splice(startIndex, numRemoved, insert1, insert2...)`

```js
// just removing elements
myArr=[1,2,3,4,5,6];               // [1, 2, 3, 4, 5, 6]
myArr.splice(3,2);                 // [4, 5]
myArr;                             // [1, 2, 3, 6]

// removing and inserting elements
myArr=[1,2,3,4,5,6];               // [1, 2, 3, 4, 5, 6]
myArr.splice(3,2,"three", "four"); // [4, 5]
myArr;                             // [1, 2, 3, "three", "four", 6]
```

## Iteration

```js
var myArray = [ "hello", "world", "!" ];

// simple iteration with a `for` loop
for ( var i = 0; i < myArray.length; i = i + 1 ) {
  console.log( myArray[ i ] );
}

// run a function on each with a `forEach` loop
myArray.forEach(
  function(currentElement, elementIndex, array) {
    console.log(currentElement);
  }
)
```

# Objects

Literal syntax:

```js
myObj = {
  foo: "bar",
  fooFunc: function(){ console.log("hi") }
}
```

Constructor syntax:

```js
function MyConstructor(){
  this.someProp = "value!";
  this.someMethod = function(){ return "I'm a method!" } ;
}

var myObj = new MyConstructor();

myObj.someProp; // "value!"
myObj.someMethod(); // "I'm a method!"
```

Adding onto all similar objects via the prototype:

```js
myObj.prototype.someProp = x;
```

Namespacing:

```js
var MY_APP = MY_APP || {};
```

# Organization

## Object pattern

Useful for namespacing. Everything is public. Chainable.

Top of a typical MVC file:

```js
"use strict";

var APP_NAME = APP_NAME || {};
var model = APP_NAME.model = {};

model.someProp = "foo";
```

## Module pattern

Encloses code in an Immediately-Evaluated Function Expression. Only export the values and interfaces you want to.

```js
var fooModule = (function() {
  var _privateVar = 123;
  var publicVar = "foo";
  return {
    getPublicVar: function() { return publicVar; },
    setPublicVar: function(val) { return publicVar = val; },
    publicMethod: function() { console.log("Hi!"); },
  }
})();
```

### Revealing module pattern

```js
var fooModule = (function() {
  var _privateVar = 123;
  var publicVar = "foo";
  var getPublicVar = function() { return publicVar; };
  var setPublicVar = function(val) { return publicVar = val; };
  var publicMethod = function() { console.log("Hi!"); };
  return {
    getPublicVar: getPublicVar,
    setPublicVar: setPublicVar,
    publicMethod: publicMethod,
  }
})();
```

### exports object

```js
var fooModule = (function() {
  var exports = {}; // or "stub"
  var _privateVar = 123;
  var publicVar = "foo";
  exports.getPublicVar = function() { return publicVar; };
  exports.setPublicVar = function(val) { return publicVar = val; };
  exports.publicMethod = function() { console.log("Hi!"); };
  return exports;
})();
```

### Dependency injection

To use another module inside yours, pass it in via a parameter.

```js
var myModule = (function(otherModule) {
  var localVar = otherModule.moduleMethod();
  return { localVar: localVar }
})(OtherModule);
```

### Extending a module

```js
var extendedModule = (function(originalModule) {
  originalModule.extension = function() { doStuff; }
  return originalModule;
})(originalModule || {});
```

If `originalModule` exists, `extension` will be added onto it. If it does not exist, you'll still get `extension` on a blank object rather than a crash.

### MVC with dependency injection

```js
// model.js
var APP_NAME = APP_NAME || {};
APP_NAME.model = (function() {
  "use strict";
  var exports = {};

  var privateMethod = function privateMethod() { ... };
  exports.publicMethod = function publicMethod() { ... };
  exports.init = function init() { ... };

  return exports;
})();
```
```js
// controller.js
var APP_NAME = APP_NAME || {};
APP_NAME.controller = (function(model) {
  'use strict';

  model.init();

})(APP_NAME.model);

```

# Module standards

## ES6 Modules

[MDN guide.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) Also known as "ES modules" or "ECMAScript modules", this is the new official standard format for JavaScript modules. It's supported by Node.js and modern browsers.

```js
import myModule from './localModule';

function myFunction() { ... }

export myFunction
```

Here are two other equivalent ways of exporting myFunction:

```js
export function myFunction() { ... }
export { myFunction }
```

If you're only exporting one thing, make it the default export.

```js
export default function() { ... }
```
```js
import myFunction from './myModule';
```

This is just an export named "default"; it's not special, except that `import` knows to look for an export with that name.

### Module namespace objects

```js
import * as cows from "cows";

// if the "cows" module exports a "moo" function, we can do:
cows.moo();
```

### Limitations

For this to work you must mark your JavaScript files as modules; this can be done using `<script type="module">`, the .mjs file extension, or by adding `"type": "module"` to package.json.

All `import` and `export` statements must be top-level - no conditionals. `import`s have no error recovery and don't support try/catch, [unless you use them as a function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#dynamic_module_loading).

## CommonJS

Node's original module format, and still the most popular one. Intended for server-side; not recommended for client-side, and `require()` is implemented by Node but not the browser.

Declare your exports using `exports` or `module.exports`. Import modules using `require('module-name')`.

```js
var moduleInNode_modules = require('npmModule');
var localModule = require('/path/to/module');

function myFunction() { ... }

exports.myFunction = myFunction;
```

`require()` also supports .json files.

### Gotchas

**TL;DR** You can replace `module.exports`, but not `exports`. You can assign properties to either one.

Node [wraps your code](https://stackoverflow.com/a/16383925/1805453) in something like:

```js
var module = { exports: {} };
var exports = module.exports;

// your code

return module.exports;
```

So you can assign properties to `exports`, like `exports.foo = bar`, but if you reassign `exports`, you should do something like this: `exports = module.exports = { a, b, c }`.

## Less-used standards

[Useful guide](https://www.freecodecamp.org/news/javascript-modules-a-beginner-s-guide-783f7d7a5fcc/)

### AMD

Asynchronous Module Definition looks like this:

```js
define(['myModule', 'myOtherModule'], function(myModule, myOtherModule) {
  console.log(myModule.hello());
});
```

### UMD

Universal Module Definition supports both AMD and CommonJS, so it works on the client and the server. It looks like this:

```js
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
      // AMD
    define(['myModule', 'myOtherModule'], factory);
  } else if (typeof exports === 'object') {
      // CommonJS
    module.exports = factory(require('myModule'), require('myOtherModule'));
  } else {
    // Browser globals (Note: root is window)
    root.returnExports = factory(root.myModule, root.myOtherModule);
  }
}(this, function (myModule, myOtherModule) {
  // Methods
  function notHelloOrGoodbye(){}; // A private method
  function hello(){}; // A public method because it's returned (see below)
  function goodbye(){}; // A public method because it's returned (see below)

  // Exposed public methods
  return {
      hello: hello,
      goodbye: goodbye
  }
}));
```

# Documentation

## DocBlocks

DocBlocks let you describe your code, including what type of parameters your functions expect; the results will appear in your editor's IntelliSense.

```js
/**
 * Multiplies a string. Example: multiplyString('!', 3) == '!!!'
 * @param {string} string String to be multiplied.
 * @param {number} times Number of times to copy it.
 */
function multiplyString(string, times) {
  var output = "";
  for (var i = 0; i < times; i++) {
    output += string;
  }
  return output;
}
```

Putting a parameter name in brackets means it's optional.

```js
/**
 * @param {Object} [options] - Options object (optional).
 */
```

[More details about the @param tag](https://devdocs.io/jsdoc/tags-param)

### Describing objects

```js
/**
 * @param {Object} options - Options object
 * @param {string} options.name
 * @param {function} options.initializer
 */
```

### Commonly-used parameter types

- `Object`
- `function`
- `string`
- `number`
- `boolean`
- `Array.<classname>` - An array of something. `Array.number` means an array of numbers, which you can also write `number[]`.

[More details](https://devdocs.io/jsdoc/tags-type)

# AJAX

Basic XMLHttpRequest:

```js
var xhr = new XMLHttpRequest();
xhr.open( "GET", "https://reqres.in/api/products", true );
xhr.onload = function() {
  console.log(xhr);
  console.log(JSON.parse(xhr.responseText));
};
xhr.send();
```

Using jQuery's [$.ajax](http://api.jquery.com/jquery.ajax/):

```js
$.ajax({
  url: 'https://reqres.in/api/products',
  complete: function(xhr) {
    console.log(xhr);
  }
});
```

Options:

- `method:` GET | POST | PUT
- `contentType:` what we're sending to them
- `dataType:` what the server should send back
- `success, error:` callbacks for when a request succeeds or fails
- `complete:` callback that always runs
- `data:` data to send along with the request (useful for POST requests)

# ES6

[Source](https://www.udemy.com/javascript-es6-tutorial/learn/v4/content)

## const & let

In ES5, you declare new variables with `var`. They're scoped to the function.

In ES6, you declare new variables with `const` if they won't change and `let` if they will. This makes it clear when variables are expected to change. `let` is scoped to the block (`if {}`, `for {}`, etc.)

## Template strings

```js
// ES5
output = "first: " + first + ", second: " + second

// ES6
output = `first: ${first}, second: ${second}`
```

Backtick syntax also enables easy multiline strings.

## Arrow functions

### ES5

```js
var add = function(a,b) {
	return a + b;
}
```

### ES6

Instead of a `function` keyword before the arguments list, an arrow function has an arrow (`=>`) after the arguments list.

```js
const add = (a, b) => {
	return a + b;
}
```

### Implicit return

If your function has just one expression, you can omit the curly braces and the `return` keyword.

```js
const add = (a, b) => a + b;
```

### Single argument

If the function has only one argument, you can omit the parens.

```js
const double = num => num * 2;
```

### Non-binding of this

Arrow functions don't create a new context for the `this` keyword. In an arrow function, the value of `this` is the same as outside the function. [Further details](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this)

## Enhanced object literals

### Deduplicate property names

If the key and the value have the same name, you don't need both.

#### ES5

```js
function createBook(name, author) {
	return {
		name: name,
		author: author
	}
}
```

#### ES6

```js
function createBook(name, author) {
	return {
		name,
		author
	}
}
```

### Omit function keyword

#### ES5

```js
{
	increment: function() { value += 1; }
}
```

#### ES6

```js
{
	increment() { value += 1; }
}
```

## Default arguments for functions

### ES5

```js
function makeAjaxRequest(url, method) {
	if (!method) {
		method = 'GET';
	}
	// logic to make the request
}
```

### ES6

```js
function makeAjaxRequest(url, method = 'GET') {
	// logic to make the request
}
```

## Rest paramenter

```js
function sum(a, b, ...otherArgs) {
	let total = 0;
	total += a;
	total += b;
	otherArgs.each(function(el) { total += el; }); // otherArgs is an array of all the other arguments passed in
	return total;
}
```

## Spread operator

```js
const KingKillerChronicles = ["The Name of the Wind", "The Wise Man's Fear", "The Doors of Stone"];
const StormlightArchive = ["The Way of Kings", "Words of Radiance"];

const fantasyBooks = ["Elantris", ...KingKillerChronicles, ...StormlightArchive];
// ["Elantris", "The Name of the Wind", "The Wise Man's Fear", "The Doors of Stone", "The Way of Kings", "Words of Radiance"]
```

## Destructuring objects

```js
const obj = {
	a: 'foo',
	b: 'bar'
};

let { a, b } = obj;
// a === 'foo'
// b === 'bar'
```

### In the signature of a function

```js
const file = {
	name: 'Macguffin',
	extension: 'exe',
	size: '1024'
};

function fileSummary({ name, extension, size}) {
	return `The file ${name}.${extension} is of size ${size}.`;
}
fileSummary(file);
```

This is useful when creating functions that take lots of arguments. Instead of worrying about the order of arguments, you can pass in an options object with a bunch of properties on it. In the function signature, you destructure out the specific properties you're interested in, and now you don't have to reference `options.property` a bunch of times in the body of the function.

### With an array

```js
const colors = ['red', 'blue', 'green'];
let [ firstColor, secondColor ] = colors; // firstColor === 'red', secondColor === 'blue'
```

## Classes

```js
class Car {
	constructor({ color }) { // pass in an options object and destructure it
		this.color = color;
	} // no comma or semicolon
	honk() { return 'beep'; }
}
const car = new Car({ color: 'red' })
```

### Inheritance

```js
class Toyota extends Car {
	constructor(options) {
		super(options); // `super` calls Car.constructor
		this.make = "Toyota";
		this.mileage = options.mileage;
	}
}

let Camry = new Toyota({ mileage: 10000 });
Camry.honk(); // 'beep'
```

## Promises

Use promises to start some long-running process and define what will happen when the process finishes and also when it fails. When the process finishes, run `resolve()` and callbacks passed into `.then()` will trigger in sequence; if it fails, run `reject()` and callbacks attached to `.catch()` will trigger.

```js
let promise = new Promise((resolve, reject) => {
	setTimeout(function() { // async function
		resolve("Waited 250ms"); // when then async function completes, return "Success" to the `then` function
	}, 250);
});

promise
	.then((successMessage) => {
		return "Success: " + successMessage;
	})
	.then((message) => {
		console.log(message);
	})
	.catch((failureMessage) => {
		console.warn("Failure: " + failureMessage);
	})
;
```

### Fetching JSON without a library

```js
let url = 'https://jsonplaceholder.typicode.com/posts';
fetch(url)
	.then(response => response.json())
	.then(data => console.log(data))
;
```

Note that [`.catch()` will only catch network errors on your device](https://www.tjvantoll.com/2015/09/13/fetch-and-errors/), not 404s or 401s or anything else. In production apps, you may want to use a library like [Request](https://github.com/request/request).

## Modules

See Module Standards section.

# Debugging in the browser

If you're monitoring the value of an object property, try making it private and using getters and setters to change its values. Then you can `console.log()` in the setter to be notified every time it changes.

## Interactive interpreter variables

`$_` gives you the value of whatever expression was evaluated last.  `$_` gives you the value of whatever expression was evaluated last.

```js
"foo"    // "foo"
$_       // "foo"
```

`$0` refers to the DOM element currently selected in the Inspector. So if `<div id="foo">` is highlighted:  `$0` refers to the DOM element currently selected in the Inspector. So if `<div id="foo">` is highlighted:

```js
$0                      // <div id="foo">
$0.getAttribute('id')   // "foo"
```

`$1` refers to the element previously selected, `$2` to the one selected before that, and so forth for `$3` and `$4`.  `$1` refers to the element previously selected, `$2` to the one selected before that, and so forth for `$3` and `$4`.

To get a collection of elements matching a CSS selector, use `$$(selector)`. This is essentially a shortcut for `document.querySelectorAll`.

```js
var images = $$('img'); // Returns an array or a nodelist of all matching elements
```

## Using getters and setters to find out what changed a property

Let's say you have an object like this:

```js
var myObject = {
	name: 'Peter'
}
```

Later in your code, you try to access `myObject.name` and you get **George** instead of **Peter**. You start wondering who changed it and where exactly it was changed. There is a way to place a `debugger` (or something else) on every set (every time someone does `myObject.name = 'something'`):

```js
var myObject = {
	_name: 'Peter',
	set name(name){debugger;this._name=name},
	get name(){return this._name}
};
```

Note that we renamed `name` to `_name` and we are going to define a setter and a getter for `name`.

`set name` is the setter. That is a sweet spot where you can place `debugger`, `console.trace()`, or anything else you need for debugging. The setter will set the value for name in `_name`. The getter (the `get name` part) will read the value from there. Now we have a fully functional object with debugging functionality.

Most of the time, though, the object that gets changed is not under our control. Fortunately, we can define setters and getters on **existing** objects to debug them.

```js
// First, save the name to _name, because we are going to use name for setter/getter
otherObject._name = otherObject.name;

// Create setter and getter
Object.defineProperty(otherObject, "name", {
    set: function(name) {debugger;this._name = name},
    get: function() {return this._name}
});
```

Check out [setters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set) and [getters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) at MDN for more information.

## Using the console

In many environments, you have access to a global `console` object that contains some basic methods for communicating with standard output devices. Most commonly, this will be the browser's JavaScript console (see [Chrome](https://developers.google.com/web/tools/chrome-devtools/debug/console/?utm_source=dcc), [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Browser_Console), [Safari](https://developer.apple.com/safari/tools/), and [Edge](https://developer.microsoft.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/console/) for more information).

```js
// At its simplest, you can 'log' a string
console.log("Hello, World!");

// You can also log any number of comma-separated values
console.log("Hello", "World!");

// You can also use string substitution
console.log("%s %s", "Hello", "World!");

// You can also log any variable that exist in the same scope
var arr = [1, 2, 3];
console.log(arr.length, this);
```

## Pausing execution

You can place a `debugger;` statement anywhere in your JavaScript code. Once the JS interpreter reaches that line, it will stop the script execution, allowing you to inspect variables and step through your code.

For named (non-anonymous) functions, you can use `debug()` to break when the function is executed:

```js
debug(functionName);
```

More options are available in your browser's developer tools.
