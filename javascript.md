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

### "Stub" or "export" object

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
