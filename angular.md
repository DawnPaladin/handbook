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

# Creating directives

```html
<!--  js/directives/firstDirective.html -->
<p>My first directive</p>
```

```js
// directives/firstDirective.js
app.directive('firstDirective', function(){
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

## Transclusion

### Setup

```js
// directives/customDirective.js
app.directive('customTag', function() {
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

# Useful functions

After clicking an element in the inspector, you can inspect its scope in the console with `angular.element($0).scope()`.
