---
layout: post
title:  "Unit Testing AngularJS Applications"
categories: code
author: Ben Centra
date:   2015-11-16 20:00:00
tags: unit testing angular javascript jasmine karma
---

Over the last few years I've written several apps using [Angular][angular], but only after a project for work have I had to worry about [unit testing][whyunittest] them. Much like learning the framework itself, there are a number of techniques you need to pick up to be able to properly unit test an Angular app. Here are some common ones for testing controllers, services, and directives.

This post assumes the following:

* You're running AngularJS 1.3 or greater
* You're using [Jasmine][jasmine] as your test framework
* You're using [Karma][karma] as your test runner
* You're familiar with the above three things

### Demo App

<img class="post-image-half" src="{{ site.url }}/assets/images/todont.png" alt="To-don't, an app for tracking things you shouldn't do">

In order to write about testing, I need something to test! So I whipped up a to-do list app called "To-Don't" that uses some common Angular features, including:

* A controller `ListController` with the main application logic
* A service `TodoService` for making HTTP calls to the backend 
* A custom `ngEnter` directive to enhance DOM capabilities

You can view the code [on GitHub][todont] or try out the [live demo][todont-live]. 

## Angular Mocks

The secret sauce to testing Angular apps is `angular-mocks`, a separate module not included in the main `angular.js` file. This is loaded in for unit tests only, as specified in the `files` section of `karma.conf.js`:

{% highlight javascript %}
// list of files / patterns to load in the browser
files: [
  // External libs
  'http://code.jquery.com/jquery-2.1.4.min.js',
  'http://ajax.googleapis.com/ajax/libs/angularjs/1.4.3/angular.min.js',
  'http://ajax.googleapis.com/ajax/libs/angularjs/1.4.3/angular-mocks.js', // secret sauce!
  // Source
  'src/js/app.js',
  'src/js/ListController.js',
  'src/js/TodoService.js',
  'src/js/ngEnter.js',
  // Tests
  'test/spec/**/*-spec.js'
],
{% endhighlight %}

`angular-mocks` will expose a number of objects and methods required for writing unit tests.

### Module Creation

The `ToDont` module, representing the To-Don't Angular app, is initialized in `app.js` like this:

{% highlight javascript %}
var ToDont = angular.module('ToDont', []);
{% endhighlight %}

In the unit tests, the `ToDont` module is loaded in a Jasmine `beforeEach` block using the [`module` function][angular.mock.module]: 

{% highlight javascript %}
beforeEach(module('ToDont'));
{% endhighlight %}

## Controllers

[Controllers][controllers] are where most of the application logic lives. To-Don't only has one controller, `ListController`, but your app might have one controller per route or page in your application.

### Initialization and Dependency Injection

Angular uses [dependency injection][di] to provide dependencies to a component. For example, `ListController` is initialized with its dependencies using the `controller` method.

In [ListController.js](https://github.com/bencentra/todont/blob/master/src/js/ListController.js):
{% highlight javascript %}
ToDont.controller('ListController', 
  ['$scope', '$timeout', 'TodoService', function($scope, $timeout, TodoService) {

    $scope.items = [];        // Array of to-do items
    $scope.newItem = '';      // New item to add to the list
    $scope.errorMsg = false;  // Error message

    // Get a list of saved items
    $scope.getItems = function() {
      TodoService.get().then(
        function(response) {
          $scope.items = response.data.items;
        },
        function(error) {
          $scope.errorMsg = error.error;
        }
      );
    };

    // Other controller methods go here

  }]
);
{% endhighlight %}

In the tests, the controller's dependencies must be injected manually. This is done with `angular-mock`'s `inject()` function. Below `ListController`'s dependencies are manually injected into the test and a test controller is created, complete with a test `$scope` object.

In [ListController-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/ListController-spec.js):
{% highlight javascript %}
describe('ListController', function() {

  beforeEach(module('ToDont'));
  
  var TodoService, timeout, q, scope, controller;

  beforeEach(inject(function($rootScope, $controller, $timeout, $q, _TodoService_) {
    // Services
    TodoService = _TodoService_; // _'s are automatically unwrapped
    timeout = $timeout;
    q = $q;
    // Controller Setup
    scope = $rootScope.$new();
    controller = $controller('ListController', {
        $scope: scope,
        TodoService: TodoService
    });
  }));

  // Tests that use 'controller'

});
{% endhighlight %}

### Dealing with `$scope`

Behavior is added to a controller by attaching properties and methods to the `$scope` object. When the app is running, actions such as method calls and changes to properties are handled by Angular's own event processing capabilities. In the unit tests, however, Angular must be explicitly told that these actions are occurring. This is done with the `$scope.$apply()` method.

In [ListController-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/ListController-spec.js):
{% highlight javascript %}
describe('initialization', function() {

  it('initializes with proper $scope variables and methods', function() {
    scope.$apply(); // Let Angular know some changes have happened (in this case, the scope is created)
    expect(scope.items).toEqual([]);
    expect(scope.newItem).toEqual('');
    expect(scope.errorMsg).toEqual(false);
  });

});
{% endhighlight %}

`$scope.$apply()` can also be called with a function as a parameter. Code in the body of the function will be updated as if it were running in an Angular app.

{% highlight javascript %}
it('is a test', function() {
  // Test stuff
  scope.$apply(function() {
    scope.getItems();
  });
  // More test stuff
});
{% endhighlight %}

### Mocking Out Services

Because any services will be tested separately, they can be replaced in the controller unit tests with mocks. `ListController` utilizes the `TodoService` to interact with the backend API, so that will be mocked out. Methods of `TodoService` can be spied on individually and given mock behavior specific to each test.

In [ListController-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/ListController-spec.js):
{% highlight javascript %}
describe('getItems()', function() {

  it('successfully gets the list of items from the service', function() {
    // Mock implementation of TodoService.get()
    spyOn(TodoService, 'get').and.callFake(function() {
      var deferred = q.defer();
      deferred.resolve(testItems);
      return deferred.promise;
    });
    // Perform an action
    scope.$apply(function() {
      scope.getItems();
    });
    // Run expectations
    expect(TodoService.get).toHaveBeenCalled();
    expect(scope.items.length).toBe(testItems.length);
  });

  // Don't forget the negative test case for failed actions!

});
{% endhighlight %}

## Services

[Services][services] are used to organize code into common functionality. In To-Don't, the `TodoService` contains all the logic for updating the to-do list and is consumed by `ListController`. In this case, `TodoService` is also responsible for making requests to the backend API using the built-in `$http` service. 

### Initialization

Initializing a service is similar to creating a controller, using the either the `service` or `factory` method ([there's a difference][factory-service-provider]) and injecting dependencies.

In [TodoService.js](https://github.com/bencentra/todont/blob/master/src/js/TodoService.js):
{% highlight javascript %}
ToDont.service('TodoService', ['$http', '$q', 'Config', function($http, $q, Config) {
  
  // BaseURL for the API
  this.baseUrl = Config.API_BASE_URL;

  // Get the list of items
  this.get = function() {
    var deferred = $q.defer();
    $http({
      url: this.baseUrl,
      method: "GET"
    }).success(function(data) {
      if (data.success === true) {
        deferred.resolve(data);
      }
      else {
        deferred.reject(data);
      }
    }).error(function(data) {
      deferred.reject(data);
    });
    return deferred.promise;
  };

  // Other service methods go here

}]);
{% endhighlight %}

In the tests the service instance is created using `inject`, similar to the controller tests.

In [TodoService-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/TodoService-spec.js):
{% highlight javascript %}
describe('TodoService', function() {

  // Load the ToDont module
  beforeEach(module('ToDont'));

  var TodoService, httpBackend, q, testUrl, testItem, testItems;

  beforeEach(inject(function(_TodoService_, $httpBackend, $q) {
    testUrl = 'API_BASE_URL';
    // Test data
    testItem = {id:1,desc:'test item',complete:false};
    testItems = [testItem];
    // Service instance and dependencies
    TodoService = _TodoService_;
    httpBackend = $httpBackend;
    q = $q;
  }));

  // Tests go here

});
{% endhighlight %}

The `TodoService` instance is now ready to be tested.

### Mocking HTTP Requests

In the controller unit tests the focus was the controller logic, so the `TodoService` calls were mocked out. In the service unit tests the focus is the `TodoService` logic, so the HTTP calls it makes will need to be mocked out; the backend itself doesn't need to be tested (at least not here). 

`angular-mocks` provides a mock implementation of [`$httpBackend`][httpbackend], acting like a Jasmine spy for HTTP requests. In the tests, `$httpBackend` is told to expect certain requests and respond to them as appropriate. Below is an example of testing a successful GET request.

In [TodoService-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/TodoService-spec.js):
{% highlight javascript %}
describe('get()', function() {

  it('gets the list of todo items', function() {
    var promise, response, result;
    // Make the request and implement a fake success callback
    promise = TodoService.get();
    promise.then(function(data) {
      result = data;
    });
    response = {
      success: true,
      data: {
        items: testItems
      }
    };
    httpBackend.expectGET(testUrl).respond(200, response); // Expect a GET request and send back a canned response
    httpBackend.flush(); // Flush pending requests
    expect(result).toEqual(response);
    expect(result.data.items).toEqual(testItems);
  });

  // Don't forget to add negative test cases for failed requests!

});
{% endhighlight %}

In between tests, make sure to clear `$httpBackend` to catch any unexpected results:

{% highlight javascript %}
afterEach(function() {
  httpBackend.verifyNoOutstandingExpectation();
  httpBackend.verifyNoOutstandingRequest();
});
{% endhighlight %}

## Directives

[Directives][directives] are Angular's way of teaching the DOM new tricks. To-Don't expands on the DOM's native capabilities with an [`ngEnter` directive][ngenter] that runs a callback function when the enter key is pressed within an input field. The directive is created using the `directive` function and returns a link function.

In [ngEnter.js](https://github.com/bencentra/todont/blob/master/src/js/ngEnter.js):
{% highlight javascript %}
ToDont.directive('ngEnter', function() {
  function (scope, element, attrs) {
    element.bind('keydown keypress', function(e) {
      if (e.which === 13) {
        scope.$apply(function() {
          scope.$eval(attrs.ngEnter);
        });
        e.preventDefault();
      }
    });
  };
});

{% endhighlight %}

Once a directive is defined, it is included in the HTML for the application:

{% highlight html %}
<p class="add">Add new: 
  <input type="text" ng-model="newItem" ng-enter="addItem()" placeholder="New item">
</p>
{% endhighlight %}

This binds the `addItem()` method of `ListController` as the callback when the enter key is pressed on the input. All the magic of linking and binding the callback is handled by Angular.

### Creating the Test Fixture

The directive can be tested by creating a fixture to interact with. The HTML will need to be compiled and registered with Angular manually using the `element()` method and `$compile` service.

In [ngEnter-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/ngEnter-spec.js):
{% highlight javascript %}
describe('ngEnter directive', function() {

  beforeEach(module('ToDont'));

  var testFixture = '<input type="text" ng-enter="enter()"/>',
      scope, compile, element, compiled, event;

  beforeEach(inject(function($rootScope, $compile) {
    scope = $rootScope.$new();
    compile = $compile;
    // Create the test fixture
    scope.enter = function() {};
    spyOn(scope, 'enter');
    element = angular.element(testFixture);
    compiled = compile(element)(scope);
    scope.$apply();
  }));

  // Tests go here

});
{% endhighlight %}

The final `compiled` element can now be tested by simulating user interaction. The test below simulates the user pressing the enter key.

In [ngEnter-spec.js](https://github.com/bencentra/todont/blob/master/test/spec/ngEnter-spec.js):
{% highlight javascript %}
it('should call scope.enter() on enter key press', function() {
  event = $.Event('keypress', {which:13});
  $(compiled).trigger(event);
  scope.$apply();
  expect(scope.enter).toHaveBeenCalled();
});
{% endhighlight %}

## Resources

There's much more to unit testing Angular apps than this post covers. Here are some materials for further reading:

* [Angular Developer Guide: Unit Testing][angular-unit-testing]
* [An Introduction To Unit Testing In AngularJS Applications][smashing-intro]
* [Unit Testing in AngularJS: Services, Controllers & Providers][sitepoint-testing]
* [Unit Testing Services in AngularJS for Fun and for Profit][unit-testing-services]
* [Ben Lesh: Angular JS - Unit Testing - Services][benlesh-testing-services]

_Don't forget to check out the source for To-Don't [on GitHub][todont] and try out the [live demo][todont-live]!_

[whyunittest]: http://stackoverflow.com/questions/67299/is-unit-testing-worth-the-effort
[angular]: https://angularjs.org/
[jasmine]: http://jasmine.github.io/
[karma]: http://karma-runner.github.io/0.13/index.html
[todont]: https://github.com/bencentra/todont
[todont-live]: http://bencentra.com/todont/
[di]: https://docs.angularjs.org/guide/di
[angular.mock.module]: https://docs.angularjs.org/api/ngMock/function/angular.mock.module
[angular.scope]: https://docs.angularjs.org/guide/scope
[factory-service-provider]: http://tylermcginnis.com/angularjs-factory-vs-service-vs-provider/
[directives]: https://docs.angularjs.org/guide/directive
[controllers]: https://docs.angularjs.org/guide/controller
[services]: https://docs.angularjs.org/guide/services
[httpbackend]: https://docs.angularjs.org/api/ngMock/service/$httpBackend
[ngenter]: http://stackoverflow.com/questions/17470790/how-to-use-a-keypress-event-in-angularjs
[angular-unit-testing]: https://docs.angularjs.org/guide/unit-testing
[smashing-intro]: http://www.smashingmagazine.com/2014/10/introduction-to-unit-testing-in-angularjs/
[sitepoint-testing]: http://www.sitepoint.com/unit-testing-angularjs-services-controllers-providers/
[unit-testing-services]: http://nathanleclaire.com/blog/2014/04/12/unit-testing-services-in-angularjs-for-fun-and-for-profit/
[benlesh-testing-services]: http://www.benlesh.com/2013/06/angular-js-unit-testing-services.html
