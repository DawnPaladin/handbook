**AngularJS**

# Hello World

```html
<html ng-app="appName">
<head>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.7/angular.js"></script>
  <script src="app.js"></script>
</head>
<body ng-controller="MyCtrl">
  {{ foo }}
</body>
```

# Controller

```js
// app.js
var appName = angular.module('appName', []);

appName.controller('MyCtrl',
  ['$scope',
    function($scope) {
      $scope.foo = 'Hello World';
      $scope.myFunction = function() {};
    }
  ]
);
```

# Directive

```html
<!--  js/directives/firstDirective.html -->
<p>My first directive</p>
```

```js
// directives/firstDirective.js
appName.directive('firstDirective', function(){
  return {
    templateUrl: "js/directives/firstDirective.html",
    restrict: "E",
    scope: {},
    link: function(scope, element, attrs) {
      // optional callback fired immediately after element creation
    }
  };
});
```

If you set `restrict: "E"`, the directive will be an Element, like this: `<first-directive></first-directive>`

If you set `restrict: "A"`, the directive will be an Attribute, like this: `<a first-directive>`.

## Scope variables

The directive is created with an **isolate scope**--it does not inherit anything in the nearby controller's scope. If necessary you can override this by having `scope` return `true` instead of an object.

```js
scope: {
  twoWayBoundVar: '=',
  passedInOnceVar: '@',
  oneWayBoundVar: '&', // commonly used for functions
}
```
```html
<first-directive two-way-bound-var="foo"
                 passed-in-once-var="{{ bar }}"
                 one-way-bound-var="someFunction()">
</first-directive>                 
```

- Two-way-bound variables (=) are passed a reference and are watched in the digest loop.
- One-way-bound variables (&, @) are passed a value and are not updated thereafter.

## Passing values into functions in a directive

```js
// js/directives/myDirective.js
appName.directive('myDirective', function() {
  return {
    ...
    scope: {
      directiveFunction: '&'
    }
  }
})
```
```html
<!-- js/directives/myDirective.html -->
<p ng-click="directiveFunction({param: variable})"></p>
```
```html
<div my-directive directive-function="outsideFunction(param)"></div>
```

## Transclusion

### Setup

```js
// directives/customDirective.js
appName.directive('customTag', function() {
  return {
    ...
    transclude: true,
  }
})
```
```html
<!-- directives/customDirective.html -->
<!-- $scope.headline = "Post Headline" -->
<div class="post">
  <h1>{{ headline }}</h1>
  <p><ng-transclude></ng-transclude></p>
</div>
```

### Usage

Code:

```html
<custom-tag>
  Text in custom-tag
</custom-tag>
```

Output:

```html
<div class="post">
  <h1>Post Headline</h1>
  <p>Text in custom-tag</p>
</div>
```

# Service

Modular storage of data & functions, accessible across multiple controllers

```js
appName.factory('serviceName', function() {
  var exports = {};
  var privateVar = "foo";
  exports.publicVar = "bar";
  return exports;
})
```

If the service has a dependency, you can inject it like so:
```js
appName.factory('serviceName', ['dependency1', function(dependency1) {
  ...
}]);
```

# Misc

After clicking an element in the inspector, you can inspect its scope in the console with `angular.element($0).scope()`.

Angular includes **jqLite**, a stripped-down version of jQuery.

- If you want the full version of jQuery, be sure to include it before you include Angular so Angular knows it's there.
- Do queries with `angular.element('selector')`.
- When Angular gives you an element, you can typically run jqLite methods on it.
- It's not conventional to prefix jqLite variables with $, since $ is commonly used for Angular variables.
