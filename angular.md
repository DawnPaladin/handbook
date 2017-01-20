**AngularJS**

# Hello World

```html
<html ng-app="appName">
<head>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.js"></script>
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
