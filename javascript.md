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
