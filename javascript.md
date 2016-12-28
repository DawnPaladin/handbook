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
